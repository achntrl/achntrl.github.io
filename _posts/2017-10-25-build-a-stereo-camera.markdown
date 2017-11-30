---
layout:      post
title:       "Build a stereo camera (part 1)"
subtitle:    "See in 3 dimensions"
date:        2017-10-25 12:00:00
update-date: 2017-10-25 12:00:00
author:      "Alexandre"
header-img:  "img/post-bg-08.jpg"
comments:    True

---

## Stereo vision

Stereo vision is using two cameras to capture the same scene from different angles.
It allows us to extract 3D information from the relative position of the same objects in the scene.

You can then build a disparity map, also called depth map. It colors each pixel depending on its distance to the camera
![Disparity Map]({{ site.baseurl }}/img/stereovision-disparitymap.png)

## Set up

You're going to need:

- 2 Rapsberry Pi (I'll assume you have a RP3) running on Raspbian
- 2 Pi Camera
- A computer with OpenCV

I used the latest version of Raspbian at the time: `2017-07-05-raspbian-jessie-lite`.

You can follow [this guide](https://www.theodo.fr/blog/2017/03/getting-started-headless-on-raspberry-pi-in-10-minutes/) to set up the RPs and [this guide](http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/) to set up the Wifi (optional).

On each RP, enable the camera with:

~~~bash
sudo raspi-config
~~~~

You have to navigate in the menu to enable the camera and reboot.

Then we will use [motion](https://github.com/Motion-Project/motion) as our streaming server

~~~bash
sudo apt-get install motion
sudo modprobe bcm2835-v4l2
~~~

~~~bash
sudo vim /etc/modules
~~~

and at the end of the file, add this line : `bcm2835-v4l2`.

Then copy [this file](https://gist.github.com/achntrl/b97c42f636a3127e97613a9f65d0cde7) on the home of your RP (or at `/etc/motion/motion.conf`).

~~~bash
sudo motion start
~~~

🎉 You can go on `http://<ip_of_your_rpi>:8081` and read the stream in your browser

![Horray]({{ site.baseurl }}/img/stereovision-video.png)

Next time, we will learn to parse two video stream with openCV.
