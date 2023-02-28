---
layout: techdocs-devices
title: Mobile Generic UI Integration
categories: [ui]
---

The following document describes how to implement the Generic Mobile UI Integration module for Android AMP SDK.


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

## Getting started

For reference, check the `AMPGenericUISample` Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1) From the de unzipped AMP distribution zip, get `modules/AmpUIMobileGeneric/libs`, add the `amp-ui-mobile-generic.aar` to your project's `/libs` folder.

2) Add the following line to the runnable module's `build.gradle` file:

```java
	implementation(name:'amp-ui-mobile-generic', ext:'aar')
```

3) In the `Activity` where the playback is handled, import the following Java packages:

```java
    import com.akamai.amp.uimobile.generic.listeners.IMediaPlayerControllerListener;
    import com.akamai.amp.uimobile.generic.media.PlayerControlsOverlay;
```

4) Add an attribute of type `PlayerControlsOverlay`:

```java
	private PlayerControlsOverlay mPlayerControlsOverlay;
```

The PlayerControlsOverlay is a customized FrameLayout, that contains the required methods to control the basic interaction between the user and a video player: play, pause, replay, scrub, DVR, etc.

5) Add a UI Widget of type `PlayerControlsOverlay` to the `Activity`'s xml layout:
```xml
    <com.akamai.uimobile.generic.media.PlayerControlsOverlay
        android:id="@+id/playerControls"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"/>
```

6) In the `Activity`s `onCreate()` method, create the instance of the `PlayerControlsOverlay`:

```java
    mPlayerControlsOverlay = (PlayerControlsOverlay) findViewById(R.id.playerControls);
```

7) Also on the `onCreate()` method, call the `setInlineUI()` method, copy the respective code:
```java
    private void setFullScreenUI() {
        mPlayerControlsOverlay.overrideControlsLayout(R.layout.androidsdk_mediaplayercontroller_fullscreen);
        setupFullScreenPlayerControls();
    }

    private void setInlineUI() {
        mPlayerControlsOverlay.overrideControlsLayout(R.layout.androidsdk_mediaplayercontroller);
        setupInlinePlayerControls();
    }

    private void setupFullScreenPlayerControls() {

        mPlayerControlsOverlay.managePlayPause(R.id.androidsdk_playPauseCtrl,
                R.mipmap.amp_play,
                R.mipmap.amp_pause);
        mPlayerControlsOverlay.manageCurrentPosition(R.id.androidsdk_seekbarTextCtrl);
        mPlayerControlsOverlay.manageTimeRemaining(R.id.video_duration);
        mPlayerControlsOverlay.manageScrubbing(R.id.androidsdk_seekbarCtrl,
                R.id.androidsdk_seekToLiveAction);
        mPlayerControlsOverlay.manageFullScreen(R.id.androidsdk_fullscreenCtrl,
                R.mipmap.amp_non_fullscreen_btn,
                R.mipmap.amp_fullscreen_btn);
    }

    private void setupInlinePlayerControls() {
        mPlayerControlsOverlay.managePlayPause(R.id.androidsdk_playPauseCtrl,
                R.mipmap.play,
                R.mipmap.pause);
        mPlayerControlsOverlay.manageCurrentPosition(R.id.androidsdk_seekbarTextCtrl);
        mPlayerControlsOverlay.manageTimeRemaining(R.id.video_duration);
        mPlayerControlsOverlay.manageScrubbing(R.id.androidsdk_seekbarCtrl,
                R.id.androidsdk_seekToLiveAction);

        mPlayerControlsOverlay.manageFullScreen(R.id.androidsdk_fullscreenCtrl,
                R.mipmap.amp_non_fullscreen_btn,
                R.mipmap.amp_fullscreen_btn);
    }
```

The whole point of having a generic UI is that the module is able to handle the basic interaction, regardless of how the UI is displayed in the screen, so you can create your own UI as you do normally with any activity or fragment in Android.
In order to do that, the module needs: the id of the xml that contains the definition of your UI, this is a normal xml layout file. Here, you can create and place any button or UI widget as you prefer.
Besides receiving the xml layout of your choosing, you need to let the Controls Overlay know what functionality is going to be handled internally, that's why there is a set of methods available for that.
This is important because not only you have the freedom to create your own xml for the controls but you are not restricted to names or specific conditions, you can create the component as you wish, and then just pass the id reference of the specific component, and then the module handles its functionality internally, so you don't have to worry about handling the resources or icons for each button, call the correct player methods or make sure the data displayed is correct, the module does that for you.

8) For that to work, copy both `androidsdk_mediaplayercontroller.xml` and `androidsdk_mediaplayercontroller_fullscreen.xml` to the `/res/layout` folder.

9) On the `onResourceReady()` callback method, set the `VideoPlayerContainer` object to the `PlayerControlsOverlay` as follows:

```java
mPlayerControlsOverlay.setVideoPlayerContainer(videoPlayerContainer);
```
The controls overlay needs the references of the VideoPlayerContainer to make the connection between the UI components and the player behavior. But, even though the VideoPlayerContainer reference is available since the onCreate, it needs to be assigned here because underneath the controls overlay also needs the videoPlayerView reference, which is obtained through the VideoPlayerView object, and  at this point you know for sure that object is available and ready to use.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>