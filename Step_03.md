# Step 03 :shipit:
## LibreNMS install

> [!IMPORTANT]
> - OS -> Debian 12
> - Web Server -> Nginx
> - For additional information on o.s. and w.s. options, visit the official website: [LibreNMS](https://www.librenms.org/)
> - The guide assumes that you are a "root" user. If you are not, make sure to switch to root or be a sudoer

- [ ] Install packages
```bash
apt update ; apt upgrade -y ; apt install apt-transport-https lsb-release ca-certificates wget acl curl fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php8.2-cli php8.2-curl php8.2-fpm php8.2-gd php8.2-gmp php8.2-mbstring php8.2-mysql php8.2-snmp php8.2-xml php8.2-zip python3-dotenv python3-pymysql python3-redis python3-setuptools python3-systemd python3-pip rrdtool snmp snmpd unzip whois -y ; apt install vim nala git sudo lsb-release -y ; apt update ; apt full-upgrade -y ; apt install acl -y ; pip3 install command_runner --break-system-packages
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
- Change into librenms user
```bash
su - librenms
```
- Run script
```bash
./scripts/composer_wrapper.php install --no-dev
```
- Exit librenms user
```bash
exit
```
> [!CAUTION]
> Make sure to exit, and go back to "root" user or sudoer

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
- Inside the editor, search for the line ``` ;date.timezone = ``` remove the semicolon ``` ; ```, and add your desired timezone. For example: ``` date.timezone =Etc/UTC ``` [Example file -> fpm/php.ini](Resources/fpm/php.ini)
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
- Then restart MariaDB
```bash
systemctl enable mariadb ; systemctl restart mariadb
```
-  Start MariaDB client
```bash
mysql -u root
```
-  Your terminal should appear different now that you are logged into MariaDB. Add the following commands
> [!NOTE]
> Remember to press "Enter" after pasting each command
```bash
CREATE DATABASE librenms CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
- Ensure to replace 'password' with a secure, and unique passphrase
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
> Make sure to exit, and go back to "root" user or sudoer
- [ ] Configure PHP-FPM [Example file -> librenms.conf](Resources/librenms.conf)
```bash
cp /etc/php/8.2/fpm/pool.d/www.conf /etc/php/8.2/fpm/pool.d/librenms.conf
```
```bash
vi /etc/php/8.2/fpm/pool.d/librenms.conf
```
- Change [www] to
```bash
[librenms]
```
- Change ``` user = www-data ```, and ``` group = www-data ``` to "librenms"
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
- Reload nginx, and restart php
```bash
systemctl reload nginx ; systemctl restart php8.2-fpm
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
systemctl enable snmpd ; systemctl restart snmpd
```
- [ ] Cron job (NOTE: Keep in mind that cron, by default, only uses a very limited set of environment variables. Review the following URL after you finished librenms install steps: [#proxy-support](https://docs.librenms.org//Support/Configuration/#proxy-support)
```bash
cp /opt/librenms/dist/librenms.cron /etc/cron.d/librenms
```
- [ ] Enable the scheduler
```bash
cp /opt/librenms/dist/librenms-scheduler.service /opt/librenms/dist/librenms-scheduler.timer /etc/systemd/system/
```
- Enable scheduler, and start scheduler
```bash
systemctl enable librenms-scheduler.timer ; systemctl start librenms-scheduler.timer
```
- [ ] Copy logrotate config
```bash
cp /opt/librenms/misc/librenms.logrotate /etc/logrotate.d/librenms
```
- [ ] Web installer
- It's time to open your browser and access LibreNMS at ``` http://192.168.0.0/install ```. Replace ```192.168.0.0``` with the actual IP or hostname
- The web installer might prompt you to create a config.php file in your librenms install location manually, copying the content displayed on-screen to the file. If you have to do this, please remember to set the permissions on config.php after you copied the on-screen contents to the file.

<div align="center">
 
![Screenshot from 2024-01-27 12-06-59](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/53634bd6-741f-4ee2-88a0-244c555793d2)

![Screenshot from 2024-01-27 12-07-18](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/466ddc82-17cf-4add-b563-58056fd52245)

![Screenshot from 2024-01-27 12-09-20](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/41b344e9-0b97-4f23-8730-f6f9b883f108)

![Screenshot from 2024-01-27 12-10-34](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/e22e7397-e40f-4591-b246-8c622d5e23b0)

![Screenshot from 2024-01-27 12-11-37](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/ae491099-34ea-4bde-a2a3-a7836e756b52)

![Screenshot from 2024-01-27 12-12-19](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/bb598611-2292-4d97-a511-b674c436489f)

</div>

- [ ] Run
```bash
chown librenms:librenms /opt/librenms/config.php
```
- [ ] LibreNMS will run .validate after the first login; it usually provides you with the answer to fix itself
- Click "Attempt to automatically fix"
<div align="center">
 
![Screenshot from 2024-01-27 12-42-57](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/0f177b27-fc88-417a-9544-fcd21028f8fe)
</div>

- Add ```localhost```, and remember to use the SNMP string you applied to your host in ```vi /etc/snmp/snmpd.conf```

<div align="center">

![Screenshot from 2024-01-27 12-52-05](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/557ed984-6b89-422b-aa69-01ab24976b77)

![Screenshot from 2024-01-27 12-53-31](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/2c0ecfea-a431-4ac6-9bf0-c6f5f45aa3ac)

</div>

- If you encounter the "Scheduler" error, run the recommended commands. If it persists after running them, you can ignore it, as I believe it's a common bug

- [ ] Shutdown your container and turn on the "Start at boot" option. Navigate to "Options," double-click "Start at boot," and checkmark the option

![Screenshot from 2024-01-27 13-12-30](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/ce2547da-7875-4ef2-8fb4-57441d75bd78)

- [ ] Is the time to save your progress by making a checkpoint

> [!TIP]
> If things go wrong you can always go back your checkpoint (backup)

- [ ] Shutdown your CT (container). You can run ```shutdown now``` or simply click the "shutdown" button
- [ ] Navigate to Backup and click "Backup Now" to create a new checkpoint

![Screenshot from 2024-01-27 12-23-23](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/95d43b1c-b871-4bfc-949c-31eae09b5dfc)

![Screenshot from 2024-01-27 12-28-27](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/cf1e929f-f52f-43e5-a7bf-9ac0521ca711)

- [ ] It's time to install [SmokePing plugin](Step_04.md)

<br>
<p align="center"> <a href="Step_02.md">:arrow_left:&nbsp;&nbsp;Step 02</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_04.md">Step 04&nbsp; :arrow_right:</a></p>
