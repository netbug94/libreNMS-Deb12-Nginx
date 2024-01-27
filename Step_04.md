# Step 05 :shipit:
## Smokeping Backend

- [ ] Install Smokeping
```bash
nala install smokeping
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
- Make sure Smokeping subfolder are still empty ```Local```, ```__cgi```, ```__sortercache```
```bash
cd /opt/librenms/rrd/smokeping ; tree
```
- In this scenario it only generated two files ```-- LocalMachine.rrd``` and ```-- data.FPing.storable```
```bash
rm /opt/librenms/rrd/smokeping/Local/LocalMachine.rrd ; rm /opt/librenms/rrd/smokeping/__sortercache/data.FPing.storable
```
- [ ] a
- [ ] a
- [ ] q

<br>
<p align="center"> <a href="Step_03.md">:arrow_left:&nbsp;&nbsp;Step 03</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_05.md">Step 05&nbsp; :arrow_right:</a></p>
