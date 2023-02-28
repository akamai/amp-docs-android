---
layout: techdocs-devices
title: Adobe Experience Platform
categories: [analytics]
---

The following document describes how to implement the Adobe Experience Platform module from AMP Android SDK.

## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Prerequisite

The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/).

It also assumes you have started conversations with an Adobe representative, for them to provide you some custom metadata specific to your Android app, that you will need later in this guide _(LAUNCH_APP_ID and LAUNCH_ENVIRONMENT_FILE_ID)_.

## Getting started

For reference, check the AmpAnalyticsAdobeExperiencePlatformSample Android Studio sample project in the release package. To integrate the plugin into your app, you need to:

1. Add the following dependencies to your **build.gradle** file:

```gradle
    implementation 'com.adobe.marketing.mobile:sdk-core:1.5.8'
    implementation 'com.adobe.marketing.mobile:analytics:1.2.6'
    implementation 'com.adobe.marketing.mobile:media:2.1.0'
```

2. Add the following file to your to your project's **/libs** folder:

   * _amp-analytics-adobe-experienceplatform.jar_ (provided by Akamai Technologies Inc in **modules/AmpAnalyticsAdobeExperiencePlatform/libs**)

3. Call the following line on your `Application`'s `onCreate()` method:
```java
    AdobeExperience.onApplicationCreate(this, LAUNCH_ENVIRONMENT_FILE_ID, LAUNCH_APP_ID);
```
The String constants will come from your Adobe representative.
For that, you'll need the following `import`:
```java
    import com.akamai.amp.analytics.adobeexperienceplatform.AdobeExperience;
```

4. In the `Activity` where playback is handled, `import` the following Java packages:

```java
import com.akamai.amp.analytics.adobeexperienceplatform.AdobeExperience;
import com.akamai.amp.analytics.adobeexperienceplatform.AmpAdobeExperienceAnalyticsTracker;
import com.akamai.amp.config.data.experienceplatform.AdobeVideoMetadata;
import com.akamai.amp.config.data.experienceplatform.ExperiencePlatformData;
```

5. Add an object of the `AmpAdobeExperienceAnalyticsTracker` type in your Activity's members:

```java
    private AmpAdobeExperienceAnalyticsTracker analytics;
```

6. Initialize that object, on the `onResourceReady()` method from the `VideoPlayerContainerCallback`:

```java
      analytics = AdobeExperience.create(videoPlayerView, buildAdobeExperienceData());
```

7. The `buildAdobeExperienceData()` method, returns a `com.akamai.amp.config.data.experienceplatform.ExperiencePlatformData` object:

```java
    private ExperiencePlatformData buildAdobeExperienceData() {
        AdobeVideoMetadata videoMetadata = new AdobeVideoMetadata(VIDEO_NAME, VIDEO_ID);
        ExperiencePlatformData data = new ExperiencePlatformData(CHANNEL, videoMetadata);
        return data;
    }
```

8. If your app integrates ads, it should use the corresponding AMP Plugin for integration with Heartbeat. This example assumes Google IMA Ads. Once the com.akamai.ads.ima.AmpIMAManager is initialized (it has to send the different events to AMP's Experience Platform object):

```java
    adsManager.addEventsListener(analytics.getiAdsComponentListener());
```

9. Reach out to an Adobe representative to get certified.

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
