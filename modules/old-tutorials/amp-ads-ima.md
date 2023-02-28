---
layout: techdocs-devices
title: Google IMA / DAI
categories: [ads]
---

The following document describes how to implement Google IMA module from AMP Android SDK.


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

## Getting started

For reference, check the IMA Sample Android Studio sample project in the release package. To integrate the plugin into your app, you need to: 

1) Copy the **amp-ads-google-ima.jar** to your project's **/libs** folder. 

2) Add the following line in the runnable module **build.gradle** file:

```java
    api 'com.google.ads.interactivemedia.v3:interactivemedia:3.19.4'
```

3) In the `Activity` where playback is handled, import the following Java packages:

```java
import com.akamai.amp.ads.AdsCount;
import com.akamai.amp.ads.AdsHelper;
import com.akamai.amp.ads.GoogleAdsInfo;
import com.akamai.amp.ads.IAdsComponentListener;
import com.akamai.amp.ads.ima.AmpDAIManager;
import com.akamai.amp.ads.ima.AmpIMAManager;
import com.akamai.amp.ads.ima.DAI;
```

4) There are two possible `IMAAdsPlugin`s, for client and server side ads respectively:

```java
    private AmpIMAManager adsComponent;
    private AmpDAIManager adsComponent;
```

Depending on you business case, you could use one or the other, or both at the same time.

5) The initialization of those objects, depends on the specific type:

```java
        adsComponent = IMA.create(this).buildClientSideManager();
        adsComponent = IMA.create(this).buildServerSideManager();
```
You should also configure them with their respective `setVideoPlayerContainer()` method

Optionally, you could also call `addEventsListener()` if needed. More details on the section below.

6) Configure the `AdsComponent` object when the `onResourceReady(MediaResource resource)` callback is triggered as follows:

```java
    @Override
    public void onResourceReady(MediaResource resource) {
        .
        .
        adsComponent.setVideoPlayerView(mVideoView);
        adsComponent.attachToVideoPlayerContainer(mVideoContainer);
        adsComponent.addEventsListener(this);
        adsComponent.setAdsUrl(adsURL);
        .
        .
    }
```

7) Add the following line to the `onDestroy()` method of the Activity's life cycle:

```java
    @Override
    public void onDestroy(){
        super.onDestroy();
        adsComponent.onDestroy();
    }
    
```

***

## Optional

You can add the following implementation to the `onResume()` and `onPause()` methods of the Activity's life cycle, to implement automatic pause and recovery of the Ads in case the App implementing the Ads loses its foreground status.

```java
    @Override public void onPause() { 
        if(adsComponent!=null) 
            adsComponent.onPause(); 
        super.onPause(); 
    }

    @Override public void onResume() { 
        if(adsComponent!=null) 
            adsComponent.onResume(); 
        super.onResume(); 
    }
```

***

## IAdsComponentListener

You can create and register your own `com.akamai.amp.ads.IAdsComponentListener<GoogleAdsInfo>`, in case you need to be notified of the following events:

```java
	void onListenerRegistered();
	void onAdsInitialized();
	void onAdRequest();
	void onAdsLoaded(AdsCount adsCount);
	void onAdsStarted(T ad);
	void onAdsEnded();
	void onAllPostrollsEnded();
	void onAdsPaused();
	void onAdsResumed();
	void onAdBreakStarted();
	void onAdBreakEnded();
	void onAdsTrackProgress(int progress); //First quartile (0), midpoint (1), third quartile (2) and completed (3)
	void onAdsPlayheadUpdate(int seconds);
	void onAdsError(String reason);
	void onPauseContentRequested();
	void onResumeContentRequested();
	void onAdsTapped();
	void onAdSkipped();
	void onAdEvent();
	void onAdBufferingStarted();
	void onAdBufferingEnded();
```

There might be certain situations where IMA does not report some of the above; therefore, AMP won't be able to report them in turn.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>