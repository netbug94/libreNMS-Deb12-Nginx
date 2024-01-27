# Step 02 :shipit:
## Debian 12 container setup (works the same with virtual machine)
- [ ] Ensure your Proxmox server is turned on. Open your preferred web browser and enter the following URL for your server: ```https://Host-IP-Goes-Here:8006/```. Then, log in

<div align="center">
  
![Screenshot from 2024-01-26 15-47-24](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/5a1eb9cd-7035-49e5-ac15-7ee55f9682d2)
</div>

- [ ] Go into Datacenter > xxxnet > local(xxx) > Templates

<div align="center">
  
![Screenshot from 2024-01-26 15-59-42](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/a877283c-7293-4151-b4d2-b813a432ec04)
  </div>
  
- [ ] Download Debian 12 (Bookworm) from the list

<div align="center">

  ![Screenshot from 2024-01-26 16-03-18](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/5c58dcb7-6cfe-4e29-b5cf-3b27e477efa5)
  </div>

- [ ] Click on 'Create CT'

<div align="center">

![Screenshot from 2024-01-26 16-04-32](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/3f59efb8-4807-40dd-992e-5345269f0144)
  </div>
  
- [ ] Fill in the required details, referring to the following images as a guide

<div align="center">

![Screenshot from 2024-01-26 16-07-02](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/eb401d5f-edf0-4c72-86b5-00a243fac8a6)

![Screenshot from 2024-01-26 16-10-44](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/d482b3b5-2d84-4965-8a7c-eb5099ae7a46)
  
![Screenshot from 2024-01-26 16-11-39](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/b31a8564-89b2-4502-bd0e-8b5c0c0e7b38)

![Screenshot from 2024-01-26 16-12-05](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/a77834cc-a1e3-44c2-8d0e-b010b5f20314)

![Screenshot from 2024-01-26 16-12-30](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/5527b8ff-ed00-43b0-b554-00fb345716b4)

![Screenshot from 2024-01-26 16-12-50](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/2e84fb3d-0f96-4af8-abc1-9220baab392f)

![Screenshot from 2024-01-26 16-13-05](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/5d7aeda0-c8fb-4509-b08c-547718314205)

![Screenshot from 2024-01-26 16-13-32](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/0f7b4018-1158-40db-af8f-697114078104)

![Screenshot from 2024-01-26 16-14-13](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/ce764194-9c27-448c-a5ba-f2fb1def0cb2)

  </div>
  
- [ ] Navigate to your new Debian Container into 'Features' and make sure to activate 'keyctl'

<div align="center">

![Screenshot from 2024-01-26 16-17-00](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/7a9075b4-8254-4d06-8d71-5aa4c99c830d)
</div>

- [ ] Configure network options for your new container by navigating to Networks > Edit. You can use the following setup or your own

![Screenshot from 2024-01-27 09-20-05](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/0684ef38-5740-4f62-aac6-e3dab7034c8a)

- [ ] Navigate to 'Console' > click 'Start' > log in using the same credentials > and make sure to update/upgrade

```bash
apt update ; apt upgrade ; shutdown now
```

- [ ] Make sure your container is shutdown, and it's time to create your first checkpoint (backup)

<div align="center">
  
![Screenshot from 2024-01-26 16-30-44](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/bb01854e-b5da-4f10-aaf4-0b6814ad5836)
</div>

<div align="center">
  
![Screenshot from 2024-01-26 16-32-19](https://github.com/hispanicdevian/libreNMS-Deb12-Nginx/assets/135581442/3f5d7201-0163-41ee-9e3d-493cf091d8d9)
</div>

- [ ] Once the backup is done, start your container, and it's time to [install LibreNMS](Step_03.md)
<br>

<p align="center"> <a href="Step_01.md">:arrow_left:&nbsp;&nbsp;Step 01</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <a href="Step_03.md">Step 03&nbsp; :arrow_right:</a></p>
