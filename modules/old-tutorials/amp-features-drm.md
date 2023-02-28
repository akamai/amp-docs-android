---
layout: techdocs-devices
title: DRM Support
categories: [others]
---

Using the EXO mode, Digital Rights Managment (DRM) protected playback is supported on Android 4.4 (API level 19) and higher.

All Android devices are required to support Widevine modular DRM (with L3 security, although many devices also support L1).

Some devices may support additional schemes such as PlayReady.

All Android TV devices support PlayReady.

In AMP, to play a DRM protected stream, you proceed as usual, with a single modification. When preparing a resource, use as parameters:

1.  the video URL
2.  a **com.akamai.amp.media.exowrapper2.DrmScheme**
3.  the URL of the license, or null if it doesn't apply

In code:

```java
videoPlayerContainer.prepareResource(VIDEO_URL, DrmScheme.WIDEVINE, WIDEVINE_LICENSE_URL);
```

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
