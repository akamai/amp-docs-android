---
layout: techdocs-devices
title: AMP Player
categories: [core]
position: 0
---

The following document guides you through the basic steps of AMP Player Wrapper Integration.


## Introduction

Before you continue be aware that AMP Android SDK can be integrated with different approaches:

* AMP Basic Integration: AMP Android SDK has a standard API to implement all features. [AMP Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/). It requires more code development and gives more freedom. For example: it allows UI control customization, rebuild the UI Controls completely, integration of Server Side Ad Insertion like DAI and Client Side Ad Insertion like IMA at the same time.
* AMP Player Wrapper Integration: AMP Android SDK has a Wrapper module called [AmpPlayer module](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-player/). This module simplifies the implementation even further. It is intended for customers who want a quick integration, with almost no customizations and a default UI. The AMP Player module unifies and coordinates all the different plugins we provide: playback, UI (scrubbar, buttons and events), Close Captions, ads and analytics. With this new module we are saving  the developer most integration effort, by providing a single drag and drop plugin that will provide production quality results in a matter of a few minutes.

Reach out to us at <amp-sdk-support@akamai.com> with any questions or concerns.


## Integration

For reference, the rest of this guide follows the `AmpPlayerSample` Android Studio project, found in the release package.

To integrate AMP into your app, you need to:

1) Add the INTERNET permission to your `AndroidManifest.xml`:

```xml
    <uses-permission android:name="android.permission.INTERNET" />
```

2) We still distribute our SDKs as `.jar`s and `.aar`s, the mandatory ones you'll need can be found in the `AmpPlayerSample/libs` folder; copy the following mandatory files into your app's `/libs` folder:
   * `amp-player.aar`
   * `amp-core.jar`
   * `amp-ui-mobile-generic.aar`
   * `amp-closed-captions.jar`

3) Add the `com.akamai.amp.AmpPlayer` `View` to the desired `Activity`'s XML layout:

```xml
    <com.akamai.amp.AmpPlayer
        android:id="@+id/amp"
        android:layout_width="match_parent"
        android:layout_weight="0.4"
        android:layout_height="0dp"
        android:background="@android:color/black" />
```

4) Add the `com.akamai.amp.AmpPlayer` to the `Activity`'s Java code:
   * Add the import: ```import com.akamai.amp.AmpPlayer;```
   * Declare the class's attribute: ```private AmpPlayer amp;```
   * Instantiate AMP's main class, as you would do with any other UI component in your Activity or Fragment.

5) At this point, AMP is ready to be used. A quick test you could run is to play back a stream:

```java
    amp.play(STREAM_URL, this, AMP_LICENSE); //'this' is the current Activity
```

The second parameter is the `Activity` being used.

6) The previous step shows how to start a video right away with no UI, just to test the video decoding is working as expected, however to take advantage of the rest of functionalities, you need to configure the `AmpPlayer` instance. 

Two actions are required: 

- Call the configure action to provide data such as the license, the activity reference, callbacks, etc.

```java
    /* 
    AMP_API_KEY: API Key given to the Customer, 
    this: it is the Activity reference, 
    customButtonCallback(): It references the IMediaPlayerControllerListener instance  */
    amp.configure( AMP_API_KEY, this, customButtonCallback() );
```

- Call the configure specifically for the UI, which determines the initial state of the UI, if you want to enable miniplayer state, etc.

```java
    /*
    First Parameter defines the initial player overlay state, these are the two options
    public @interface State {
            int INLINE_PLAYER = 1; 
            int FULLSCREEN_PLAYER = 2; 
        }
    Second is the initial Controls mode, these are the three options:
    public @interface Mode {
            int INLINE = 1; 
            int EMBEDDED = 2;
            int CONTENT_LIST = 3;
        }
    Third parameter determines if the player must rotate when the device orientation changes
    Fourth parameter determines if the player must start on miniplayer mode
    */
    amp.configureVisually(State.INLINE_PLAYER, Mode.INLINE, true, true);

```

7) **Prepare resource Builder:**  There are several different ways that a media source can be prepared, the basic action is provide a video to play, however, there are other factors involved, such as video with CSAI, video with SSAI, 360 rendering, start playback at a specific position, muted, paused, etc. For each one of those cases, this is how you should start the playback:

```java
    amp.prepare().withStream(STREAM_URL).init();
```

The prepare builder requires you to invoke the prepare action by calling the method `prepare()` then, select the video you wish to play and how, and once you have configured how you are going to start the playback, call the `init()` action to put the prepare action in motion.

These are the options available for the `prepare()`:

```java
        //Enables custom renderings such as 360 or VR
        withCustomRenderer(RendererBuilder builder, int interactiveMode, int displayMode, int projectionMode) 

        // Basic action to provide a stream URL
        withStream(String streamURL)

        // Sets the stream playlist extension manually
        withExtension(String extension) 

        // Sets the stream mime type manually
        withMimeType(String mimeType) 

        // Sets a starting point for non Live streams
        atPosition(int startPosition)

        // Sets a duration in case it needs to ge provided manually
        withDuration(int durationInSeconds)

        // enables thumbnails slider for VOD, it receives the vtt source url as parameter
        withThumbnails(String vtt)

        // enables thumbnails slider for VOD, it receives the vtt source url as parameter and the base path in case the paths in the vtt file are relative.
        withThumbnails(String vtt, String basePath)

        // Defines if the video must autoplay or not upon start
        shouldPauseAtStart(boolean shouldPause)

        // Sets the video type (Live or non Live) in case it needs to be provided manually
        isLive(boolean isLive) 

        // Sets the assetKey for Server Side Ads from Google, this represents the stream as well, passing an asset key eliminates the need to provide a stream URL 
        withDAILive(String assetKey) 

         // Sets the content source ID and Video Id for Server Side Ads from Google, for VOD, this represents the stream as well, passing these values eliminates the need to provide a stream URL
        withDAIVOD(String vodContentSourcedId, String videoId)

        // Sets the Ad Tag URL for Client Side Ads from Google, in case the IMA plugin has been properly configured before
        withClientSideIMA(String adsUrl)

        // Sets the manifest suffix in case it is required to attach anything to the URL requests for DAI 
        withManifestSuffix(String manifestSuffix)

        // It initializes the playback using an internal proxy when Yospace Ads are properly configured before, similar to DAI, calling this method eliminates the need to provide a Stream URL
        withYospaceProxy() 

       // It initializes the playback using the session approach when Yospace Ads are properly configured before, similar to DAI, calling this method eliminates the need to provide a Stream URL    
        withYospaceSession()

        // It sets the debug Flags for the Yospace session
        withDebugFlags(int debugFlags)
```

8) AMP needs to be aware of the Android lifecycle events. The minimum necessary are `onResume()`, `onPause()` and `onDestroy()`:

```java
@Override
public void onResume() {
    if (amp != null) amp.onResume();
    super.onResume();
}

@Override
public void onPause() {
    if (amp != null) amp.onPause();
    super.onPause();
}

@Override
protected void onDestroy() {
    if (amp != null) amp.onDestroy();
    super.onDestroy();
}
```

Finally, the `VideoPlayerContainer` and `VideoPlayerView` objects are still accessible in case any of them is needed, perhaps to access api methods that are not exposed by `AmpPlayer`, you just need to call the following methods:

```java
    amp.getVideoPlayerContainer();
    amp.getVideoPlayerView();
```

***

## Ad providers

There are two options to initialize Ads using `AmpPlayer`: 

Set the `AmpPlayer` with a single ad plugin only: 

```java
amp.setAdsPlugin(googleAds, IAdsComponentListener);
```

or set the `AmpPlayer` with a single ad plugin and multiple analytics plugins:

```java
amp.setPlugins(googleAds, akamaiMediaAnalytics, streamsenseAnalytics, nielsenAnalytics);
```

The creation of those plugins varies, they can be seen on the `AmpPlayerSample` Android Studio project and every individual plugin sample.

The first parameter in the `setPlugins()` method above, receives an `com.akamai.amp.ads.IAdsPlugin`. Classes that implement this interface (and the way to instantiate them) are:

* AmpIMAManager: `IMA.create(this);`
* AmpDAIManager: `DAI.create(this);`
* AmpFreewheelManager: `Freewheel.create(c, FW_NETWORK_ID, FW_ADS_URL, FW_SITE_SECTION_ID, FW_VIDEO_ASSET_ID, FW_PROFILE);`
* AmpYospaceManager: `Yospace.create(activity, streamURL);`
* AmpFacebookAudienceNetworkManager: `FacebookAds.create(PLACEMENT_ID, ADS_AT_SECONDS);`

## Analytics trackers

The other parameters for the `setPlugins()` method call extend from the `com.akamai.amp.analytics.BaseTracker` class and in turn, for their constructor, receive a parameter that in turn extends from `com.akamai.amp.analytics.AnalyticsTrackerData`. The `BaseTracker`s and their respective `AnalyticsTrackerData` are:

* AmpAkamaiMediaAnalyticsTracker (MediaAnalyticsData)
* AmpComscoreStreamsenseAnalyticsTracker (StreamsenseData)
* AmpAdobeHeartbeatAnalyticsTracker (HeartbeatData)
* AmpNielsenDcrAnalyticsTracker (NielsenData)
* AmpGoogleAnalyticsTracker (GoogleAnalyticsData)
* AmpFirebaseAnalyticsTracker (FirebaseTrackerData)

**Notes:**
* The instantiation of plugins is the same if you use this approach or the `VideoPlayerView` and `VideoPlayerContainer` classes directly.
* All of AMP plugins are also notified of the lifecycle events by the `AmpPlayer` class, so there's no need to explicitly notify them.
* If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>