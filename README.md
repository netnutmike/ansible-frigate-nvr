# ansible-frigate-nvr

This repository contains ansible scripts to install the Frigate NVR on a small board compture.  If you have a Raspberry PI 4 or CM4 you can add on a Google Coral to offload the hard work of the video processing.  

I am testing this out on other small board computers as well.  I am trying first the OrangePI CM4 on a the OrangePi CM4 board with an NVME drive on the back.

## Requirements
For the Frigate NVR to work well on these SBCs, here are the minimum requirements:

* A Raspberry Pi 4, CM4 or something equivalent from another manufacturer that supports a Debian based OS
* at Minimum 2 GB of Ram, I am testing with 4 and 8 GB models so any feedback on how 2GB works is appreciated
* Either an SSD or NVMe drive.  The larger the better, larger drives provide more storage for historical video
* Google Coral (highly recommended) to offload the video processing from the SBC

One more thing that is required is Ansbile installed on a machine to run this script

## Preparation

There are a few things that need to be setup before you are ready to run the installation script:

1. Prepare a SBC computer with the most recent version of the OS running on it (this script is tested regularly using ubuntu 22.04 on a Raspberry Pi and Orangepi CM4)
2. Be sure you can login via SSH, be sure to write down the username and password
> If possible try to setup setup an [SSH Key Pair](https://www.raspberrypi-spy.co.uk/2019/02/setting-up-ssh-keys-on-the-raspberry-pi/) to automatically login to the SBC.  If you are not sure how to do that, do not worry, I will provide you the option to login with a password, it is just not as secure.
3. Make sure your SSD or NVMe drive is mounted as `/video_files`
4. Download this Repository to your computer that has Ansible installed on it
5. Edit the hosts.ini file and change the `changeme` under `[DVR]` to your hostname or IP address
6. Change the `ansible_user=changeme` to the username that is used to login to your SBC
7. If you plan to use an MQTT server refer to the ==Setup Frigate to talk to MQTT== below
8. If you want to setup your cameras before installing Frigate, refer to the ==Setup Frigate Cameras Before Install==.  If you do not set them up before installing, there is a good guide here: [The Ultimate Guide to Frigate NVR Notifications](https://www.simplepush.io/blog/frigate-nvr-push-notification-guide#run-mosquitto-mqtt-in-docker).


## What the Ansible Script Does

Here is what the Ansible script will do when you run it:
1. Login to your device and update the appliction cache `apt-get update`
2. Upgrade the OS to the latest version `apt-get upgrade -y`
3. Install Docker
4. Reboot
5. Install Frigate

## Run The Script

```
ansible-playbook main.yml
```

### Run without an ssh key

Run the ansible-playbook command with a -k and you will be prompted for the password for the user you setup in hosts.ini

```
ansible-playbook -k main.yml
```

## Learn more about Frigate

[Frigate Official Site](https://docs.frigate.video/)



## Advanced Topics


### Setup Frigate to talk to MQTT

I already have an MQTT server running for my Home Assistant installation so I did not go into detail about installing MQTT.  If you need to install MQTT here is a great guide on how to install it with Frigate in Docker: [The Ultimate Guide to Frigate NVR Notifications](https://www.simplepush.io/blog/frigate-nvr-push-notification-guide#run-mosquitto-mqtt-in-docker).

If you already have an MQTT server running, here is how you setup Frigate to use it.



### Setup Frigate to work with Home Assistant

### Setup Frigate Cameras Before Install

If you want to setup the cameras prior to install you can edit the frigate.yml.j2 file in the frigate folder.  In that file there is a section that defines the cameras.  By defualt it looks like this:

```
cameras:
  office:
    ffmpeg:
      inputs:
        - path: rtsp://10.0.10.10:554/rtsp
          roles:
            - detect
            - rtmp
```

Refer to the Frigate documentation on how to configure your cameras.  [Frigate Official Site](https://docs.frigate.video/)

In this installation, Frigate is installed in Docker, for more details on how to modify the configuration when running in Docker, there is a good reference at [The Ultimate Guide to Frigate NVR Notifications](https://www.simplepush.io/blog/frigate-nvr-push-notification-guide#run-mosquitto-mqtt-in-docker).

## Author Details

This project was created in 2023 by [Mike Myers](https://mikemyers.me)

Lots of thanks and credit needs to go to [Jeff Geerling](https://www.jeffgeerling.com).  I have learned Ansible from his guides and this project has lots of pieces from parts of his Raspberry PI NVR project.