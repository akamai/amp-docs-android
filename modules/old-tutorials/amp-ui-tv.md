---
layout: techdocs-devices
title: TV UI Integration
categories: [ui]
---

 The following document describes how to implement TV UI module from AMP Android SDK.


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

## Getting started

For reference, check the TVUISample Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1) From **modules/UITV/libs**, add the **amp-ui-tv.aar** to your project's **/libs** folder.

2) Copy the following lines in your runnable module build.gradle file

```java
    compile(name:'amp-ui-tv', ext:'aar')
    compile 'com.android.support:recyclerview-v7:23.3.0'
    compile 'com.android.support:leanback-v17:23.3.0'
```

3) In the Activity where playback is handled, import the following Java packages:

```java
    import com.akamai.amp.ui.tv.MediaControlBarManager;
    import com.akamai.amp.ui.tv.MediaPlayerController;
```

4) Implement the MediaControlBarManager.OnRemoteControlActionsListener callback in the Activity that handles the playback, this will allow you to receive actions related to the TV's remote control:

```java
    @Override
    public boolean onRemoteActionReceived(MediaControlBarManager.RemoteControlAction action){
        switch (action) {
            case PLAY:
                return true;
            case PAUSE:
                return true;
            case FAST_FORWARD:
                return true;
            case REWIND:
                return true;
            case CAPTION_ON:
                return true;
            case CAPTION_OFF:
                return true;
            default:
                return false;
        }
    }
```

5) Implement two instances of the following objects:

```java
    private MediaPlayerController mediaPlayerController;
    private MediaControlBarManager mediaControlBarManager;
```

6) Create a View type fragment on the activity's xml layout as follows:

```xml
    <fragment
        android:id="@+id/playback_controls_fragment"
        android:name="com.akamai.uitv.MediaPlayerController"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

7) Create an instance of the Fragment controller in the code as follows:

```java
    mediaPlayerController = (MediaPlayerController) getFragmentManager().findFragmentById(R.id.playback_controls_fragment);
```

8) On the onResourceReady() callback, create an instance of the Controller Manager and set the VideoPlayerView object to it:

```java
    mediaControlBarManager = new MediaControlBarManager(this, this, mediaPlayerController);
    mediaControlBarManager.setVideoPlayerView(mVideoPlayerView);
```

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
