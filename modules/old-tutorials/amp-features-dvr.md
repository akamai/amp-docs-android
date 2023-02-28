---
layout: techdocs-devices
title: DVR with AMP
categories: [others]
---

The following document shows you how to fully take advantage of DVR content available on your streams with AMP for Android. 

## Description

Digital Video Recorder (DVR) refers to the ability to fast forward, rewind and scrub content recently streamed on a live feed.
On a non DVR live stream, the viewer is watching the live point being broadcasted; however, there is no way to re-watch something already streamed. So, for example, in a basketball game, trying to see your favorite play again during the streaming is not possible. However, DVR allows to leave the stream's live point, seek through past content to re-watch it, and then return to the updated live point at any moment.

## What to expect

In terms of behavior, the DVR window is treated as a VOD. The only exception is that the current position should never reach the very end of the progress bar. The live position should be represented by the last bit of the scrub bar. The position fluctuates between a range of a few segments, which are considered the live point.

When the user wants to see a section of the video that has already been broadcasted, the position will stay put until changed. While the position in the scrub bar stays the same, the actual live position keeps moving forward, and the DVR window along with it. There might be cases where the current position is no longer valid, so the player will force the user to move back to the live edge.

## Prerequisite

* This guide assumes you have successfully integrated AMP's Core (you can [play back a video](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/))

* The streams must be properly configured to support DVR (MPEG-DASH or LHLS) for AMP to use the video properly.

## How To

To enable DVR with AMP, you must:

* **Enable DVR mode:** By default, DVR is disabled on AMP. To enable it you need to call the following method once the `VideoPlayerView` instance is created (you can detect that by using the `VideoPlayerContainer.VideoPlayerContainerCallback`):

```
mVideoPlayerView.enableDVRfeatures(true);
```

* **Implement the Player Controls:** The actual impact of the DVR feature is noticeable once the player UI options are implemented because it is through them that the options to seek are available.

There are two options to implement the Player Controls:

*1) Use the AMP mobile generic UI:* AMP provides a [UI feature](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-ui-mobile-generic/) that handles all DVR-related actions. No integration actions are required for DVR specifically.

*2) Implement custom Controls:* You always have the option to implement your own components, but this implies that more responsibility lies on your shoulders. AMP provides all the APIs needed to invoke the DVR-related actions. 

To build your own Player Controls and add DVR to your App, it is needed to treat the progress bar as you would do for a VOD video, meaning that the progress bar has a size, which usually is the duration of the video. But for live video, it should represent the size of the DVR window. In addition, seek, play and pause actions must be enabled. Also, add an extra option to allow the customer to move back to the live point in case their current viewing position in time fell behind.

Once controls are implemented, you need to connect the user actions with our API methods to react accordingly:

```
    //It returns the size of the DVR window
    mVideoPlayerView.getTimelineDuration(); 
        
    //It returns the current position within said window    
    mVideoPlayerView.getCurrentTimelinePosition(); 
    
    //It tells you if the video is live or VOD     
    mVideoPlayerView.isLive();    
    
    //It forces the playback position to move to the current live edge
    mVideoPlayerView.seekToLive(); 
    
    /*It allows you to seek to a position relative to the DVR window. Technically, Live video positions represented by 
    a timestamp or something similar can't be used here. However, a point in time can be represented as a particular 
    point within the DVR window and treat said window as a VOD. */
    mVideoPlayerView.seek(int newPosition); 
```

***

For more on how Live streams work, you can contact our team via <amp-sdk-support@akamai.com>, request the Live stream behavior walk-through, and leave further questions or comments.