## Step 03 :shipit:
### LibreNMS install
> [!IMPORTANT]
> - OS -> Debian 12
> - Web Server -> Nginx; For additional information on operating systems and web server options, please visit the official website: [LibreNMS](https://www.librenms.org/)

> - **The guide assumes that you are a "root" user. If you are not, make sure to switch to root or be a sudoer.**
- [ ] Install packages:
```bash
apt install apt-transport-https lsb-release ca-certificates wget acl curl fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php8.2-cli php8.2-curl php8.2-fpm php8.2-gd php8.2-gmp php8.2-mbstring php8.2-mysql php8.2-snmp php8.2-xml php8.2-zip python3-dotenv python3-pymysql python3-redis python3-setuptools python3-systemd python3-pip rrdtool snmp snmpd unzip whois
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
- [ ] d
- [ ] e
- [ ] f
- [ ] g
- [ ] h
- [ ] i
- [ ] j
- [ ] k
- [ ] l
- [ ] m
- [ ] n
- [ ] o
- [ ] p
- [ ] q

<p align="center"> <a href="Step_02.md">:arrow_left:&nbsp;&nbsp;Step 02</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_04.md">Step 04&nbsp; :arrow_right:</a></p>
