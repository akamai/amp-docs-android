---
layout: techdocs-devices
title: Adobe Heartbeat
categories: [analytics]
---

The following document describes how to implement Adobe HeartBeat module from AMP Android SDK.

## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/).

It also assumes you have started conversations with a Comscore representative, for them to provide you some custom metadata specific to your Android app, that you will need in Point #5 of this guide _(AdobeUserId, TrackingServer, Publisher, Channel, Ovp)_.

## Getting started

For reference, check the AMPHeartbeatSample Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1. Add the following files to your to your project's **/libs** folder:

   * _adobeMobileLibrary.jar_ (provided by Adobe)
   * _VideoHeartbeat.jar_ OR _MediaSDK.jar_ (provided by Adobe)
   * _amp-analytics-adobe-heartbeat.jar_ (provided by Akamai Technologies Inc in **modules/AnalyticsAdobeHeartbeat/libs**)

2. In the Activity where playback is handled, import the following Java packages:

```java
    import com.akamai.amp.analytics.adobeheartbeat.AdobeHeartbeat;
    import com.akamai.amp.analytics.adobeheartbeat.AmpAdobeHeartbeatAnalyticsTracker;
    import com.akamai.amp.config.data.heartbeat.HeartbeatConfigurationData;
    import com.akamai.amp.config.data.heartbeat.HeartbeatData;

```

3. Add an object of the `AmpAdobeHeartbeatAnalyticsTracker` type in your Activity's members:

```java
    private AmpAdobeHeartbeatAnalyticsTracker heartbeatAnalytics;
```

4. Initialize that object, on the `onResourceReady()` method from the `VideoPlayerContainerCallback`:

```java
    private void initAnalytics() {
      ...
      heartbeatAnalytics = AdobeHeartbeat.create(videoPlayerView, buildHeartbeatData());
      ...
    }
```

5. The `buildHeartbeatData()` method, returns a `com.akamai.amp.config.data.heartbeat.HeartbeatData` object. Check all its `set()` methods, to send the appropriate values. For example:

```java
    HeartbeatConfigurationData data = new HeartbeatConfigurationData();
    data.setTrackingServer(HEARTBEAT_TRACKING_SERVER);
    data.setPublisher(HEARTBEAT_PUBLISHER);
    data.setChannel(HEARTBEAT_CHANNEL);
    data.setOvp(HEARTBEAT_OVP);
    data.setAudienceManagerDpid(AUDIENCE_MANAGER_DPID);
    data.setAudienceManagerDpuuid(AUDIENCE_MANAGER_DPUUID);
    data.setAdobeUserId(ADOBE_USER_ID);
```

6. If your app integrates ads, it should use the corresponding AMP Plugin for integration with Heartbeat. This example assumes Google IMA Ads. Once the com.akamai.ads.ima.AmpIMAManager is initialized (it has to send the different events to AMP's Heartbeat object):

```java
    adsManager.addEventsListener(heartbeatAnalytics.getiAdsComponentListener());
```

7. Reach out to an Adobe representative to get certified. Remember to load place the corresponding ADBMobileConfig.json in main/assets/ folder: [ADB Mobile Config](https://docs.adobe.com/content/help/en/mobile-services/android/configuration-android/json-config.html)

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
