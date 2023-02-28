---
layout: techdocs-devices
title: Amazon Publisher Services (APS) for AMP
categories: [ads]
---

The following document describes how to integrate the Amazon Publisher Services (APS) header bidding plugin for AMP.

Notice this only works on the Amazon ecosystem (FireTV only, not on AndroidTV).

[Amazon describes APS](https://aps.amazon.com/aps/index.html/) as "a suite of cloud services that brings you solutions".

[On their OTT integration documentation](https://ams.amazon.com/webpublisher/uam/docs/ott-integration-documentation.html/), Amazon expands specifically on their header bidding solution:
> APS integration is based on a pre-fetched bid model where the Fire TV app calls APS for all the available ad opportunities for one or more ad breaks before it calls the ad server to get the required ads. APS responds with the bid price sent back as key-value pairs, which the app then sends to the ad server while making the ad call for one or more ad breaks. These key-value pairs are pre-determined during the ad server set-up (as explained in the next paragraph). The final ad decision happens on the ad server based on the business rules set by the publisher.

Note that in order to access their [developer documentation](https://ams.amazon.com/webpublisher/uam/docs/ott-integration-documentation.html/), you have to be registered as an official developer. Reach out to Amazon to get access to it.

## Prerequisite

The rest of this guide assumes you have successfully integrated [AMP's Freewheel plugin](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-ads-freewheel/) into your app (you are able to get a preroll and then AMP continues with content playback).

## Integration

For reference, the rest of this guide follows the `AMPAmazonPublisherServicesSample` Android Studio project, found in the release package.

To integrate APS into your app, you need to:

1. Copy the following mandatory files into your app's `/libs` folder:

   * `AmazonFireTVAdsSDK-release.aar`
   * `amp-bidder-amazon-aps.jar`

2. Add the import:

```java
    import com.akamai.amp.bidder.aps.AmpApsBidder;
```

3. Amazon should provide you with an APS application ID. Also, for every content type, they should provide a hash:

```java
    private static final String APS_APP_ID = "abcdefghijklmnopqrstuv1234567890";
    private static final long APS_TIME_OUT_MILLIS = 1000L;
    private static final boolean APS_TEST_FLAG = true;
    private static final String APS_AD_BREAK_ID_22_MINS = "abcde123-ab12-ab12-ab12-abcdef12345A";
    private static final String APS_AD_BREAK_ID_42_MINS = "abcde123-ab12-ab12-ab12-abcdef12345B";
    private static final String APS_AD_BREAK_ID_MOVIE   = "abcde123-ab12-ab12-ab12-abcdef12345C";
    private static final String APS_AD_BREAK_ID_OTHERS  = "abcde123-ab12-ab12-ab12-abcdef12345D";
```

Depending on your conditions, you should tweak and try different values for `APS_TIME_OUT_MILLIS` (it generally is 1, 5 or 10 seconds).

Be sure to set the `APS_TEST_FLAG` to `false` when your app is in production.

4. On the `onCreate()` method, add the APS related code:

```java
    private void checkAps(MediaResource mediaResource) {
        if (!Utils.isFireTV(this)) return; //APS should only be created on FireTV

        setApsData(mediaResource);
        AmpApsBidder apsBidder = new AmpApsBidder(ampFreewheelManager.getBidderCallback(), this, APS_APP_ID, APS_TIME_OUT_MILLIS, APS_TEST_FLAG);
        apsBidder.requestBids(mediaResource, APS_AD_BREAK_ID_22_MINS);
    }

    private void setApsData(MediaResource mediaResource) {
        mediaResource.setGuid("abc123");
        mediaResource.setRating("TV-Y");
        mediaResource.setGenres(Arrays.asList("Action", "Adventure"));
        mediaResource.setCategories(Arrays.asList("IAB1", "IAB4"));
        mediaResource.setDuration(3600);
    }
```

Note that by this point in time, the `AmpFreewheelManager` has already been created.

5. To check the integration is working, you should find output similar to the following in your logcat logs:

>`I/KSOMessageProcessor: APS: Bids received from amazon : [{key:ott35p5,value:IqX7vwkQL9U9BYRaAamMqR8AAAFpowTBGQEAAA_ABozyB8Y}, {key:amznslots,value:ott35p5}, {key:ott15p5,value:InFrQjz5ifPir9wivd0jVKMAAAFpowTBGQEAAA_ABmChAyU}, {key:amznslots,value:ott15p5}, {key:ott25p5,value:IhkX48fsl15VVr8ijiOFUTUAAAFpowTBGQEAAA_ABv7WfIk}, {key:amznslots,value:ott25p5}, {key:ott35p4,value:IvqmWasZdPc1DN6atd9eIegAAAFpowTBGQEAAA_ABtjAG0c}, {key:amznslots,value:ott35p4}, {key:amzninvp1,value:IgYBnlm37vXr60-sVPZ6i4kAAAFpowTBGgEAAA_ABhq3Abc}, {key:amznslots,value:amzninvp1}, {key:ott21p5,value:IlV6DVzDsvJz9FbycNuy0bgAAAFpowTBGgEAAA_ABsbj8OI}, {key:amznslots,value:ott21p5}]`

Those same values will be reported in the logs by `D/AmpApsBidder: onSuccess` and `D/FreewheelAdsHandler: onBidsFound`.

The Freewheel request XML will include the bids are set in the `<keyValues>` element:

```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>
<adRequest networkId="375600" profile="375600:amzn_live_android" version="1">
    <capabilities>
        <expectMultipleCreativeRenditions />
        <requiresRendererManifest />
        <requiresVideoCallbackUrl />
        <supportNullCreative />
        <supportsAdUnitInMultipleSlots />
        <supportsFallbackAds />
        <supportsSlotCallback />
        <supportsSlotTemplate />
    </capabilities>
    <visitor caller="android-6.2.0-r10834-1506230518" />
    <keyValues>
        <keyValue key="_fw_dpr" value="2" />
        <keyValue key="amznslots" value="ott1_preroll" />
        <keyValue key="amznslots" value="ott5_preroll" />
        <keyValue key="amznslots" value="ott1_midroll" />
        <keyValue key="amznrequestid" value="WtiADNR00GK6qjshafbhjshgyu7" />
        <keyValue key="amznregion" value="pdx" />
    </keyValues>
    <siteSection customId="amazonmmb_ad_fw_example" pageViewRandom="968955661">
        <videoPlayer>
            <videoAsset autoPlay="true" customId="B332EIBU6U" duration="62.0" durationType="EXACT" videoPlayRandom="735287846">
                <adSlots compatibleDimensions="1920,1080" defaultSlotProfile="375600:amzn_live_android">
                    <temporalAdSlot adUnit="PREROLL" customId="24698513" timePosition="0.0" />
                    <temporalAdSlot adUnit="MIDROLL" customId="24698516" timePosition="15.0" />
                </adSlots>
            </videoAsset>
            <adSlots />
        </videoPlayer>
        <adSlots />
    </siteSection>
</adRequest>
```

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
