---
layout: techdocs-devices
title: Comscore
categories: [analytics]
---

The following document describes how to implement Comscore module  from AMP Android SDK.

## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

It also assumes you have started conversations with a Comscore representative, for them to provide you some custom metadata specific to your Android app, that you will need in Point #5 of this guide (`publisherId`, `publisherSecret`, `appName`, `appVersion` and others.).

## Getting started

For reference, check the **AMPAnalyticsComscoreStreamsenseSample** Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1) Add the following files to your project libraries: 
* _comscore.aar_ (provided by Comscore) 
* _amp-analytics-comscore-streamsense.jar_ (provided by Akamai)

2) The first Comscore call should be made on the Application's `onCreate()`, add the following line to it:

```java
ComscoreStreamsenseAnalyticsTracker.onApplicationCreate(getStreamsenseAppData(), this);
```

It needs as parameter a `StreamsenseAppData` object, that contains the `publisherId`, `publisherSecret`, `appName` and `appVersion` constants.

3) In the Activity where playback is handled, import the following Java packages:

```java
    import com.akamai.analytics.comscorestreamsense.ComscoreStreamsenseAnalyticsTracker;
    import com.akamai.config.data.streamsense.StreamsenseAppData;
    import com.akamai.config.data.streamsense.StreamsenseClipLabels;
    import com.akamai.config.data.streamsense.StreamsenseData;
    import com.akamai.config.data.streamsense.StreamsensePlaylistLabels;
```

4) Add an object of the `ComscoreStreamsenseAnalyticsTracker` type in your Activity's members:

```java
    private ComscoreStreamsenseAnalyticsTracker streamsenseAnalytics;
```

5) Initialize that object, on the `onResourceReady()` method from the VideoPlayerContainerCallback:

```java
    private void initAnalytics() {
       streamsenseAnalytics = new ComscoreStreamsenseAnalyticsTracker(buildStreamsenseData());
       streamsenseAnalytics.setVideoPlayerView(videoPlayerView);
       streamsenseAnalytics.init(MainActivity.this);
    }
```

6) The **buildStreamsenseData()** method, returns a **com.akamai.config.data.streamsense.StreamsenseData** object, which is composed by all four of the following objects:

*   `StreamsenseAppData`: Comscore provided data that identifies the specific app (`publisherId`, `publisherSecret`, `appName` and `appVersion`).
*   `StreamsensePlaylistLabels`: Metadata related to the playlist, for example title, length in milliseconds and total number of clips.
*   `StreamsenseClipLabels`: Metadata that describes either the content or the ads, set with the methods `StreamsenseData.setVideoLabels()` and `StreamsenseData.setAdLabels()` respectively.

Each one of the other three objects receive a **java.util.Map** of two Strings: the beacon's name and it's value. To query all the available beacon names on any of those objects, use the **getAllMetadata()** method. To query all the mandatory beacon names on any of those objects, use the **getMandatoryMetadata()** method. Both methods mentioned previously will return a Set , with the corresponding "official" beacon names.

Check the **MainActivity.java** file from the AMPStreamsenseSample, for an example of how to fill this data.

6.1) Alternatively, you can provide this data as an AMP Config file.

```java
videoPlayerContainer.setConfig(COMSCORE_CONFIG);
```

When creating the ComscoreStreamsenseAnalyticsTracker, use the empty constructor (the StreamsenseData will be constructed automatically from the .json configuration file).

7) If your app integrates ads, it should use the corresponding AMP Plugin for integration with Comscore. This example assumes Google IMA Ads. Once the **com.akamai.ima.AdsComponent** is initialized, it has to send the different events to AMP's Nielsen object:

```java
adsComponent.addEventsListener(streamsenseAnalytics);
```

8) Reach out to a Comscore representative to get certified.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
