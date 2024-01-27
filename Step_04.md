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
- [ ] 
- [ ] q

<br>
<p align="center"> <a href="Step_03.md">:arrow_left:&nbsp;&nbsp;Step 03</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_05.md">Step 05&nbsp; :arrow_right:</a></p>
