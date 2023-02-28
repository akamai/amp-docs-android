---
layout: techdocs-devices
title: Nielsen DCR
categories: [analytics]
---
 
The following document describes how to implement Nielsen DCR module from AMP Android SDK.

## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

It also assumes you have started conversations with a Nielsen representative, for them to provide you some custom metadata specific to your Android app, that you will need in Point #5 of this guide (`appid`, `appversion`, `appname`, `sfcode` and others.).

## Getting started

For reference, check the `AmpAnalyticsNielsenDcrSample` Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1) Add the following .jars to your project's /libs folder: 
* **appsdk.jar** (provided by Nielsen) 
* **amp-analytics-nielsen-dcr.jar** (provided by Akamai)

2) In the Activity where playback is handled, import the following Java packages:

```java
    import com.akamai.amp.analytics.nielsenanalytics.AmpNielsenDcrAnalyticsTracker;
    import com.akamai.amp.config.data.NielsenAdData;
    import com.akamai.amp.config.data.NielsenAppData;
    import com.akamai.amp.config.data.NielsenContentData;
    import com.akamai.amp.config.data.NielsenData;
```

3) Add an object of the `AmpNielsenDcrAnalyticsTracker` type in your Activity's members:

```
    private AmpNielsenDcrAnalyticsTracker nielsenAnalytics;
```

4) Initialize that object, on the `onResourceReady()` method from the VideoPlayerContainerCallback:

```
    public void initAnalyticsTracker() {
       nielsenAnalytics = new AmpNielsenDcrAnalyticsTracker(buildNielsenData());
       nielsenAnalytics.setVideoPlayerView(videoPlayerView);
       nielsenAnalytics.enableReporting(true);
       nielsenAnalytics.init(PlayerActivity.this);
    
       Log.i("AMP+NielsenDCR", "AMP v" + AMPLibraryInfo.VERSION + " Nielsen DCR " + nielsenAnalytics.getAppSdkVersion());
    }
```

5) The `buildNielsenData()` method, returns a `com.akamai.config.data.NielsenData` object, which is composed by all three of the following objects:

```
    NielsenAppData;
    NielsenContentData;
    NielsenAdData;
```

Each of those three objects receive a **java.util.Map** of two Strings: the beacon's name and it's value. 
* To query all the available beacon names on any of those objects, use the `getAllMetadata()` method. 
* To query all the mandatory beacon names on any of those objects, use the `getMandatoryMetadata()` method. 
Both methods mentioned previously will return a Set , with the corresponding "official" beacon names.


Check the **PlayerActivity.java** file from the `AmpAnalyticsNielsenDcrSample`, for an example of how to fill this data.

Nielsen's description of every beacon

More information about the NielsenAppData's beacons

5.1) Alternatively, you can provide this data as an AMP Config file.

```
videoPlayerContainer.setConfig(NIELSEN_CONFIG);
```

When creating the AmpNielsenDcrAnalyticsTracker, use the empty constructor (the NielsenData will be constructed automatically from the .json configuration file).

6) If your app integrates ads, it should use the corresponding AMP Plugin for integration with Nielsen. This example assumes Google IMA Ads. Once the **com.akamai.ima.AdsComponent** is initialized, it has to send the different events to AMP's Nielsen object:

```
adsComponent.addEventsListener(nielsenAnalytics);
```

7) To get certified by Nielsen, it is mandatory to show to the customer an opt-out page. The AmpNielsenDcrAnalyticsTracker provides this custom URL, with the method getOptOutUrl(). Refer to PlayerActivity.java and OptOutActivity.java for an implementation example.

8) Reach out to a Nielsen representative to get the "Nielsen DCR Pre-Certification Checklist" PDF.

Once you have validated every item on the checklist is correct, send your app to Nielsen for certification.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>