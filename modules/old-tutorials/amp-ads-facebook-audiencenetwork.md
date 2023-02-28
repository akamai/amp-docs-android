---
layout: techdocs-devices
title: Facebook Audience Network (FBAN) for AMP
categories: [ads]
---

The following document describes how to integrate the Facebook Audience Network (FBAN) ads plugin for AMP.

[Facebook describes FBAN](https://developers.facebook.com/docs/audience-network/) as a way to "monetize your mobile property with Facebook ads". Using "App Bidding", as "a way for publishers to establish an impartial and open auction over their ad inventory by offering every ad opportunity to multiple demand sources in real time".


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/).

***

## Integration

For reference, the rest of this guide follows the `AmpAdsFacebookAudienceNetworkSample` Android Studio project, found in the release package.

To integrate FBAN into your app, you need to:

1. Follow Facebook's [Getting Started](https://developers.facebook.com/docs/audience-network/android/) guide.

2. Make sure you have added the reference to FBAN in the `build.gradle`:

```gradle
implementation 'com.facebook.android:audience-network-sdk:5.1.0'
```

3. Copy the `amp-ads-facebook-audiencenetwork` into your app's `/libs` folder. This is distributed by AMP.

4. Add the import:

```java
 import com.akamai.amp.ads.fban.AmpFacebookAudienceNetworkManager;
```

5. Facebook should provide you with a FBAN application ID:

```java
    private static final String PLACEMENT_ID = "YOUR_PLACEMENT_ID";
```

6. You should provide a list of the seconds when you want an ad to start loading:

```java
    private static final int[] ADS_AT_SECONDS = {10, 20, 30};
```

If you don't want any midrolls, you can provide an empty array.

7. Add an attribute of type `AmpFacebookAudienceNetworkManager`:

```java
	private AmpFacebookAudienceNetworkManager adManager;
```

8. On AMP's `VideoPlayerContainerCallback` `onVideoPlayerCreated()` method, create the FBAN object:

```java
        adManager = new AmpFacebookAudienceNetworkManager(PLACEMENT_ID, ADS_AT_SECONDS);
        adManager.setVideoPlayerContainer(videoPlayerContainer);
        adManager.setVideoPlayerView(videoPlayerView);
```

9. At this point, the plugin is ready to work. You should see the ads you have indicated in the `ADS_AT_SECONDS` array.

10. You should register AMP's UI as an ad event listener, for it to automatically show or hide:
`adManager.addEventsListener(playerControlsOverlay);` (where `playerControlsOverlay` is of type `com.akamai.amp.uimobile.generic.media.PlayerControlsOverlay`).

***

## Alternative ways of requesting ads

Besides providing a list of midrolls you want to see, you can also enable the visualization of a single prerolls or a single postroll. The respective methods to accomplish this are:

```java
adManager.enablePreroll(true);
adManager.enablePostroll(true);
```

Furthermore, you can request an ad at any point in time you need:

```java
adManager.requestAd();
```

Also, there might be some cases where "optional" midroll ads could be needed (they are seen under normal circumstances, except if the customers seeks over them, in which case they are skipped). These type of ads are set with:

```java
adManager.addOptionalMidrolls({20, 40, 60});
```

***

## IAdsComponentListener

You can create and register your own `com.akamai.amp.ads.IAdsComponentListener<FacebookAdsInfo>`, in case you need to be notified of the following events:

```java
	void onListenerRegistered();
	void onAdsInitialized();
	void onAdsLoaded(AdsCount adsCount);
	void onAdsStarted(FacebookAdsInfo adsInfo);
	void onAdsEnded();
	void onAllPostrollsEnded();
	void onAdsPaused();
	void onAdsResumed();
	void onAdBreakStarted();
	void onAdBreakEnded();
	void onAdsTrackProgress(int progress); //0 is first quartile, 1 is midpoint, 2 third quartile and 3 is completed
	void onAdsPlayheadUpdate(int seconds);
	void onAdsError(String reason);
	void onPauseContentRequested();
	void onResumeContentRequested();
	void onAdsTapped();
	void onAdEvent();
```

There might be certain situations where Facebook does not report some of the above; therefore, AMP won't be able to report them in turn.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
