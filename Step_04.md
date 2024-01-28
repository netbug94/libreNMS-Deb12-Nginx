# Step 05 :shipit:
## Smokeping Backend

- [ ] Install Smokeping
```bash
nala install smokeping -y
```
- [ ] Install cron script
```bash
cp /opt/librenms/misc/smokeping-debian.example /etc/cron.hourly/librenms-smokeping
```
- Make this file executable
```bash
chmod +x /etc/cron.hourly/librenms-smokeping
```
- [ ] Remove everything from your Probes file
```bash
*** Probes ***

@include /etc/smokeping/config.d/librenms-probes.conf
```
- [ ] Do the same with your Targets file
```bash
*** Targets ***

probe = FPing

menu = Top
title = Network Latency Grapher
remark = Welcome to the SmokePing website of <b>Insert Company Name Here</b>. \
         Here you will learn all about the latency of our network.

@include /etc/smokeping/config.d/librenms-targets.conf
```
- [ ] Lets combine smkeping dir with librenms
- Stop smokeping
```bash
systemctl stop smokeping
```
- Move smokeping directory
```bash
mv /var/lib/smokeping /opt/librenms/rrd/
```
- Add permissions
```bash
usermod -a -G librenms smokeping
```
- Edit and update the data directory
```bash
vi /etc/smokeping/config.d/pathnames
```
- Modify the values for ```datadir``` and ```dyndir```
```bash
datadir = /opt/librenms/rrd/smokeping
dyndir = /opt/librenms/rrd/smokeping/__cgi
```

<div align="center">
         
![Screenshot from 2024-01-27 15-20-50](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/66336547-49ba-4163-95c9-b3cfeea6b70a)
</div>

- For this changes to work properly, make sure Smokeping subfolders are empty ```Local```, ```__cgi```, ```__sortercache```

```bash
cd /opt/librenms/rrd/smokeping ; tree
```
- In my case, as of this step, SmokePing only generated two files: ```--LocalMachine.rrd``` and ```--data.FPing.storable```

```bash
rm /opt/librenms/rrd/smokeping/Local/LocalMachine.rrd ; rm /opt/librenms/rrd/smokeping/__sortercache/data.FPing.storable ; tree
```
<div align="center">
         
![Screenshot from 2024-01-27 15-45-08](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/a7a232b0-866f-46fb-ae61-836f39c03fe0)
</div>

- [ ] Configure LibreNMS
- Switch to librenms user
```bash
su - librenms
```
- Update libreNMS settings
```bash
lnms config:set smokeping.dir '/opt/librenms/rrd/smokeping'
lnms config:set smokeping.pings 20
lnms config:set smokeping.probes 2
lnms config:set smokeping.integration true
lnms config:set smokeping.url 'smokeping/'
```
- Make sure to exit back to the root user
```bash
exit
```
- [ ] Configure Smokeping's Web UI
- Install fcgiwrap for CGI wrapper interact with Nginx

```bash
nala install fcgiwrap -y
```

- configure Nginx with the default configuration

```bash
cp /usr/share/doc/fcgiwrap/examples/nginx.conf /etc/nginx/fcgiwrap.conf
```
- Configure Nginx to respond to http://0.0.0.0/smokeping

```bash
vi /etc/nginx/sites-enabled/librenms.vhost
```
- Paste the following code after the last "location"

```bash
location = /smokeping/ {
        fastcgi_intercept_errors on;

        fastcgi_param   SCRIPT_FILENAME         /usr/lib/cgi-bin/smokeping.cgi;
        fastcgi_param   QUERY_STRING            $query_string;
        fastcgi_param   REQUEST_METHOD          $request_method;
        fastcgi_param   CONTENT_TYPE            $content_type;
        fastcgi_param   CONTENT_LENGTH          $content_length;
        fastcgi_param   REQUEST_URI             $request_uri;
        fastcgi_param   DOCUMENT_URI            $document_uri;
        fastcgi_param   DOCUMENT_ROOT           $document_root;
        fastcgi_param   SERVER_PROTOCOL         $server_protocol;
        fastcgi_param   GATEWAY_INTERFACE       CGI/1.1;
        fastcgi_param   SERVER_SOFTWARE         nginx/$nginx_version;
        fastcgi_param   REMOTE_ADDR             $remote_addr;
        fastcgi_param   REMOTE_PORT             $remote_port;
        fastcgi_param   SERVER_ADDR             $server_addr;
        fastcgi_param   SERVER_PORT             $server_port;
        fastcgi_param   SERVER_NAME             $server_name;
        fastcgi_param   HTTPS                   $https if_not_empty;

        fastcgi_pass unix:/var/run/fcgiwrap.socket;
}

location ^~ /smokeping/ {
        alias /usr/share/smokeping/www/;
        index smokeping.cgi;
        gzip off;
}
```
- [ ] 
- [ ] q

<br>
<p align="center"> <a href="Step_03.md">:arrow_left:&nbsp;&nbsp;Step 03</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_05.md">Step 05&nbsp; :arrow_right:</a></p>
