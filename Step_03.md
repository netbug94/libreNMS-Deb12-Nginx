# Step 03 :shipit:
## LibreNMS install

> [!IMPORTANT]
> - OS -> Debian 12
> - Web Server -> Nginx
> - For additional information on o.s. and w.s. options, visit the official website: [LibreNMS](https://www.librenms.org/)
> - The guide assumes that you are a "root" user. If you are not, make sure to switch to root or be a sudoer

- [ ] Install packages
```bash
apt install apt-transport-https lsb-release ca-certificates wget acl curl fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php8.2-cli php8.2-curl php8.2-fpm php8.2-gd php8.2-gmp php8.2-mbstring php8.2-mysql php8.2-snmp php8.2-xml php8.2-zip python3-dotenv python3-pymysql python3-redis python3-setuptools python3-systemd python3-pip rrdtool snmp snmpd unzip whois vim nala -y
```
- [ ] Add librenms user
```bash
useradd librenms -d /opt/librenms -M -r -s "$(which bash)"
```
- [ ] Download LibreNMS
```bash
cd /opt
```
```bash
git clone https://github.com/librenms/librenms.git
```
- [ ] Set permissions
```bash
chown -R librenms:librenms /opt/librenms
```
```bash
chmod 771 /opt/librenms
```
```bash
setfacl -d -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
```
```bash
setfacl -R -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
```
- [ ] Install PHP dependencies
- Change into librenms user ``` su - librenms ```
- Run script ``` ./scripts/composer_wrapper.php install --no-dev ```
- Exit librenms user ``` exit ```
> [!CAUTION]
> Make sure to exit and go back to "root" user or sudoer

> [!IMPORTANT]
> If the above steps worked, disregard the next steps
 - Occasionally, when a proxy is utilized for internet access, the above script may encounter issues. In such cases, a workaround is to install the composer package manually. For a global installation
```bash
wget https://getcomposer.org/composer-stable.phar
```
```bash
mv composer-stable.phar /usr/bin/composer
```
```bash
chmod +x /usr/bin/composer
```
> [!IMPORTANT]
> Once you have resolved the details mentioned above, continue here
- [ ] Set timezone for your php files; make sure it matches the [official php options](https://www.php.net/manual/en/timezones.php)
- Inside the editor, search for the line ``` ;date.timezone = ``` remove the semicolon ``` ; ``` and add your desired timezone. For example: ``` date.timezone =Etc/UTC ``` [Example file -> fpm/php.ini](Resources/fpm/php.ini)
```bash
vi /etc/php/8.2/fpm/php.ini
```
- Repeat the same step [Example file -> cli/php.ini](Resources/cli/php.ini)
```bash
vi /etc/php/8.2/cli/php.ini
```
- [ ] Set the system timezone using the following command. Make sure to replace 'Etc/UTC' with your chosen option from above
```bash
timedatectl set-timezone Etc/UTC
```
- [ ] Configure MariaDB [Example file -> 50-server.cnf](Resources/50-server.cnf)
```bash
vi /etc/mysql/mariadb.conf.d/50-server.cnf
```
- Within the [mysqld] section add
```bash
innodb_file_per_table=1
lower_case_table_names=0
```
- Then restart MariaDB ``` systemctl enable mariadb ``` ``` systemctl restart mariadb ```
-  Start MariaDB client ``` mysql -u root ```
-  Your terminal should appear different now that you are logged into MariaDB. Add the following commands
> [!NOTE]
> Remember to press "Enter" after pasting each command
```bash
CREATE DATABASE librenms CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
- Ensure to replace 'password' with a secure and unique passphrase
```bash
CREATE USER 'librenms'@'localhost' IDENTIFIED BY 'password';
```
```bash
GRANT ALL PRIVILEGES ON librenms.* TO 'librenms'@'localhost';
```
```bash
exit
```
> [!CAUTION]
> Make sure to exit and go back to "root" user or sudoer
- [ ] Configure PHP-FPM [Example file -> librenms.conf](Resources/librenms.conf)
```bash
cp /etc/php/8.2/fpm/pool.d/www.conf /etc/php/8.2/fpm/pool.d/librenms.conf
```
```bash
vi /etc/php/8.2/fpm/pool.d/librenms.conf
```
- Change [www] to ``` [librenms] ```
- Change ``` user = www-data ``` and ``` group = www-data ``` to "librenms"
```bash
user = librenms
group = librenms
```
- Change ``` listen = /run/php/php8.2-fpm.sock ``` with
```bash
listen = /run/php-fpm-librenms.sock
```
- [ ] If there are no other PHP web applications on this server, remove file "www.conf"
```bash
rm /etc/php/8.2/fpm/pool.d/www.conf
```
- [ ] Configure Web Server
```bash
vi /etc/nginx/sites-enabled/librenms.vhost
```
- Yes, don't panic; the file is currently empty. We need to add content [Example file -> librenms.vhost](Resources/librenms.vhost)
```bash
server {
 listen      80;
 server_name librenms.example.com;
 root        /opt/librenms/html;
 index       index.php;

 charset utf-8;
 gzip on;
 gzip_types text/css application/javascript text/javascript application/x-javascript image/svg+xml text/plain text/xsd text/xsl text/xml image/x-icon;
 location / {
  try_files $uri $uri/ /index.php?$query_string;
 }
 location ~ [^/]\.php(/|$) {
  fastcgi_pass unix:/run/php-fpm-librenms.sock;
  fastcgi_split_path_info ^(.+\.php)(/.+)$;
  include fastcgi.conf;
 }
 location ~ /\.(?!well-known).* {
  deny all;
 }
}
```
- Remove "default" file
```bash
rm /etc/nginx/sites-enabled/default
```
- Reload nginx
```bash
systemctl reload nginx
```
- Restart php
```bash
systemctl restart php8.2-fpm
```
- [ ] Enable lnms command completion
```bash
ln -s /opt/librenms/lnms /usr/bin/lnms
```
```bash
cp /opt/librenms/misc/lnms-completion.bash /etc/bash_completion.d/
```
- [ ] Configure snmpd
```bash
cp /opt/librenms/snmpd.conf.example /etc/snmp/snmpd.conf
```
- Set your own community string by changing ``` RANDOMSTRINGGOESHERE ``` [Example file -> snmpd.conf](Resources/snmpd.conf)
```bash
vi /etc/snmp/snmpd.conf
```
```bash
curl -o /usr/bin/distro https://raw.githubusercontent.com/librenms/librenms-agent/master/snmp/distro
```
```bash
chmod +x /usr/bin/distro
```
- Enable and restart snmp
```bash
systemctl enable snmpd
```
```bash
systemctl restart snmpd
```
- [ ] Cron job (NOTE: Keep in mind that cron, by default, only uses a very limited set of environment variables. Review the following URL after you finished librenms install steps: [#proxy-support](https://docs.librenms.org//Support/Configuration/#proxy-support)
```bash
cp /opt/librenms/dist/librenms.cron /etc/cron.d/librenms
```
- [ ] Enable the scheduler
```bash
cp /opt/librenms/dist/librenms-scheduler.service /opt/librenms/dist/librenms-scheduler.timer /etc/systemd/system/
```
- Enable scheduler
```bash
systemctl enable librenms-scheduler.timer
```
- Start scheduler
```bash
systemctl start librenms-scheduler.timer
```
- [ ] Copy logrotate config
```bash
cp /opt/librenms/misc/librenms.logrotate /etc/logrotate.d/librenms
```
- [ ] Web installer
- Is time to open your browser and acces LibreNMS ``` http://192.168.0.0/install ```
- The web installer might prompt you to create a config.php file in your librenms install location manually, copying the content displayed on-screen to the file. If you have to do this, please remember to set the permissions on config.php after you copied the on-screen contents to the file. Run
```bash
chown librenms:librenms /opt/librenms/config.php
```
- [ ] Now is the time to save your progress by making a [checkpoint (backup)](Step_04.md)

> [!TIP]
> If things go wrong you can always go back your [checkpoint](Step_04.md)
<br>

<p align="center"> <a href="Step_02.md">:arrow_left:&nbsp;&nbsp;Step 02</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_04.md">Step 04&nbsp; :arrow_right:</a></p>
