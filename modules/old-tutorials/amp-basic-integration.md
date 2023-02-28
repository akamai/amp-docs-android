---
layout: techdocs-devices
title: AMP Basic Integration
categories: [core]
position: 1
---

The following document guides you through the basic steps of AMP Basic Integration.


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Description
The Android AMP SDK is a set of tools that allows app developers to implement video playback capabilities natively, for any Android based device (mobile phone, tablet or TV).

It provides a very easy integration process and offers several advanced functionalities, such as Video and Audio playback for PMD, VOD and Live videos on different formats like HLS, DASH, SmoothStreaming, MP4, MP3, and others.

The core functionality of the player is multimedia playback, however, there is also a set of modules that include Captioning, UI Controls and several different Ads and Analytics providers.


## Getting Started
This is a basic guide to integrate AMP on any Android application. The end result will be a video playing on your sample app. It won't have any UI controls, as that is a separate module.


## Prerequisite
The rest of this guide assumes that the app developer who is integrating AMP has a working Android Studio environment; that is, they can run a "Hello World" sample on their device or emulator. It is recommended that uses the latest Android APIs.

It is also assumed that you have already received an Android AMP SDK license from Akamai. If not, reach out to us at <amp-sdk-support@akamai.com>


## Installation
There are two main AMP distributions:
* [Standard](https://mdtp-a.akamaihd.net/amp-android-sdk): includes basic playback, captions and UI only
* [Premier](https://mdtp-a.akamaihd.net/amp-android-sdk/premier): includes the ads and analytics plugins

Download the .zip package and extract the contents. This package includes several Android Studio samples. You can use your target plugin sample as base.

1. On the `/modules/Core/libs` folder, you can find the `amp-core.jar`. Copy it into the `libs` folder of your project.

2. Add the `VideoPlayerContainer` object in your `Activity`'s layout as following (attributes like `width`, `height`, `padding`s, `margin`s, etc; are modifiable as any other UI component in Android):

```xml
<com.akamai.media.VideoPlayerContainer
    android:layout_width="match_parent"
    android:id="@+id/playerViewCtrl"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:layout_gravity="center" />
```

3. In the `onCreate()` method of your `Activity`, get a reference to the `VideoPlayerContainer` object. Register the callback for playback and prepare the URL to be played:
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
    mVideoPlayerContainer = (VideoPlayerContainer) findViewById(R.id.playerViewCtrl);
    mVideoPlayerContainer.addVideoPlayerContainerCallback(this);
    mVideoPlayerContainer.prepareResource(VIDEO_URL);
}
```
With this callback, the SDK converts the URL of a stream into a com.akamai.media.elements.MediaResource. Optionally, there is an enable autorecovery functionality which can reset the video playback in cases where the Internet network state is down and then up. In order to enable autorecovery, just call the following method before `prepareResouce(VIDEO_URL)`.
```java
    ...
    mVideoPlayerContainer.enableAutoRecovery();
    mVideoPlayerContainer.prepareResource(VIDEO_URL);
    ...
```

4. In order for the callback registering to succeed, the activity must implement the `com.akamai.media.VideoPlayerContainer.VideoPlayerContainerCallback`:
```java
public class MainActivity extends Activity implements VideoPlayerContainer.VideoPlayerContainerCallback {
```

5. For the activity to be a `VideoPlayerContainerCallback`, it has to implement three methods: `onVideoPlayerCreated()`, `onResourceReady()` and `onResourceError()`:
```java
@Override
public void onResourceReady(MediaResource mediaResource) {
}

@Override
public void onVideoPlayerCreated() {
}

@Override
public void onResourceError() {
}
```

On the `onResourceReady()` method, use the `play(MediaResource resource)` method of the `VideoPlayerView` object to start playing a stream; use `playAudio(MediaResource resource)` for audio-only streams.


6. MediaResource Object:
In order to play a video or an audio using AMP, you need to provide a `MediaResource` object as shown in the previous steps. This object is a representation of a Media Resource precisely, it contains everything the player needs to start the playback, stream URLs, titles and subtitles, captions, poster image (if provided, etc).
The player is going to provide this object to you:
```java
@Override
public void onResourceReady(MediaResource mediaResource) {
    resource = mediaResource;
    mVideoPlayerView = mVideoPlayerContainer.getVideoPlayer();
    mVideoPlayerView.setLicense(LICENSE);
    mVideoPlayerView.setLogEnabled(true);
    mVideoPlayerView.play(resource, mCurrentPosition);
}
```
However, if you want to manually provide any of these attributes, you can modify this object to do so.

## Android lifecycle
```java
@Override
public void onResume() {
    if (videoPlayerView != null) {
        videoPlayerView.onResume();
    }
    super.onResume();
}

@Override
public void onPause() {
    if (videoPlayerView != null) {
        videoPlayerView.onPause();
    }
    super.onPause();
}

@Override
protected void onDestroy() {
    if (videoPlayerView != null) {
        videoPlayerView.onDestroy();
    }
    super.onDestroy();
}
```

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>