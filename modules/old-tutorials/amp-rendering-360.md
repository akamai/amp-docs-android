---
layout: techdocs-devices
title: 360 Module 
categories: [others]
---

The following document describes how to implement 360 module from AMP Android SDK.

##  Settings
Reach out to us, to provide you with an Android Studio sample where you can test all the following settings.


### setDisplayMode(DISPLAY_MODE)

* `AMP360Component.DISPLAY MODENORMAL` Renders the surface on a normal rectangular screen (phones, tablets, TVs).
* `AMP360Component.DISPLAY MODEGLASS` Renders the surface on the Google Glass screen.


### setInteractiveMode(INTERACTIVE_MODE)

* `AMP360Component.INTERACTIVE MODEMOTION` Allows the user to control the focus of the video by tilting and moving the device around (using the gyroscope of the device if available).
* `AMP360Component.INTERACTIVE MODETOUCH` Allows the user to control the focus of the video by touching the screen and moving the focus with the fingers (it uses gestures and touch events to adjust the position of the video on the screen).
* `AMP360Component.INTERACTIVE MODEMOTION WITHTOUCH` It's a combination of the two modes above, the video can be positioned both with the fingers or by using the gyroscope.
* `AMP360Component.INTERACTIVE MODECARDBORAD_MOTION` Allows the user to control the focus of the screen by moving around the device when using a Cardboard set (it splits the screen and creates the correct layout to focus the video for the Cardboard lenses).
* AMP360Component.INTERACTIVE MODECARDBORAD MOTIONWITH_TOUCH Allows the user to control the focus of the screen by moving the device around and by touching the screen, when using a cardboard set (it splits the screen and creates the correct layout to focus the video for the Cardboard lenses).

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
