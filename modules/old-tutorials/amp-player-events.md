---
layout: techdocs-devices
title: AMP Player Events
categories: [core]
position: 2
---

The following document describes how to get the events that the AMP Android SDK generates.

## Prerequisite
The rest of this guide assumes you have successfully integrated AMP's Core (you are able to play back a video): [Basic Integration](https://developer.akamai.com/tools/AdaptiveMediaPlayer/docs/android/amp-basic-integration/)

## Getting started
To get all AMP generated events, you must attach your own `com.akamai.amp.media.IPlayerEventsListener` to your `com.akamai.amp.media.VideoPlayerView` instance:

```java
videoPlayerView.addEventsListener(new IPlayerEventsListener() {
    @Override
    public boolean onPlayerEvent(int eventID) {
        Log.i(TAG, "AMP Event: " + PlayerEvents.toString(eventID));
        if (PlayerEvents.matches(eventID, IPlayerEventsListener.PLAYER_EVENT_APP_SENT_TO_FOREGROUND))
        {
            // Do something
        }
        return false;
    }

    @Override
    public boolean onPlayerExtendedEvent(int i, int i1, int i2) {
        return false;
    }
});
```

In the example above, the app developer need to add custom code when the app is sent to the foreground (the `IPlayerEventsListener.PLAYER_EVENT_APP_SENT_TO_FOREGROUND` event is fired).

The `com.akamai.amp.media.PlayerEvents` class exist to give a meaningful name to the event IDs (the `toString()` method). It can also be used to check if the event ID is of certain type (the `matches()` method).


## Events list
These are the events defined on the `com.akamai.amp.media.IPlayerEventsListener`:

```java
	/** 
	 * Indicates a time position change. Used to track the progress of the video 
	 */  
	int PLAYER_EVENT_TYPE_POSITION_UPDATE = 0;

    /**
     * Indicates that player is ready to start the playback
     */
    int PLAYER_EVENT_TYPE_START_PLAYING = 1;

	/** 
	 * Indicates the end of the playback 
	 */
	int PLAYER_EVENT_TYPE_FINISHED = 2;

	/** 
	 * Indicates when the first frame has been showed, and therefore, the playback has started
	 */
	int PLAYER_EVENT_TYPE_STARTED = 3;
	
	/** 
	 * Indicates an error has occurred
	 */
	int PLAYER_EVENT_TYPE_ERROR = 4;
	
	/** 
	 * Indicates the control is starting buffering
	 */
	int PLAYER_EVENT_TYPE_START_REBUFFERING = 5;
	
	/** 
	 * Indicates the last rebuffering event has ended
	 */
	int PLAYER_EVENT_TYPE_END_REBUFFERING = 6;

	/** 
	 * Indicates a bitrate switch has been requested
	 */
	int PLAYER_EVENT_TYPE_SWITCH_REQUESTED = 7;

	/** 
	 * Indicates a bitrate switch has just occurred
	 */
	int PLAYER_EVENT_TYPE_SWITCH = 8;
	
	/** 
	 * Indicates the control is loading a media resource
	 */
	int PLAYER_EVENT_TYPE_LOADING = 9;
	
	/** 
	 * Indicates the control has executed a bandwidth measure
	 * arg1: Current client bandwidth
	 * arg2: Index of the recommended playback bitrate
	 */
	int PLAYER_EXTENDED_EVENT_BANDWIDTH_MEASURE = 10;

	/** 
	 * Indicates a successful seeking operation 
	 * arg1: Seeking position
	 */
	int PLAYER_EXTENDED_EVENT_SEEKING_SUCCEDEED = 11;

	/** 
	 * Indicates a fullscreen mode being selected
	 */
	int PLAYER_EVENT_TYPE_FULLSCREEN_MODE_CHANGE = 12;
	
	/** 
	 * Video size or position has changed. This event is fired whenever the position 
	 * or the size of the video control changes. For example, when there is a change in
	 * the screen orientation
	 */
	int PLAYER_EVENT_TYPE_SIZE_CHANGE = 13;
	
	/** 
	 * There is a request for loading a new video. A plugin can pause the load (returning false in onPlayerEvent)
	 * for doing things (use case: implementing ads plugins)
	 */
	int PLAYER_EVENT_TYPE_LOAD_REQUESTED = 14;

	/** 
	 * Resume method called
	 */
	int PLAYER_EVENT_TYPE_RESUME_REQUESTED = 15;
	
	/** 
	 * Pause method called
	 */
	int PLAYER_EVENT_TYPE_PAUSE_REQUESTED = 16;

    /**
     * The app has been sent to the background
     */
    int PLAYER_EVENT_APP_SENT_TO_BACKGROUND = 17;

    /**
     * The app has been sent to the foreground
     */
    int PLAYER_EVENT_APP_SENT_TO_FOREGROUND = 18;

	/**
	 * Indicates the beginning of a new seeking operation
	 */
	int PLAYER_EXTENDED_EVENT_SEEKING_STARTED = 19;

	/**
	 * Indicates that a chunk has just finished loading
	 */
	int PLAYER_EVENT_CHUNK_LOAD_COMPLETE = 20;

	/**
	 * Indicates that a STOP has just been requested
	 */
	int PLAYER_EVENT_TYPE_STOP_PLAYING = 21;

	/**
	 * Indicates that a PLAY has just been requested
	 */
	int PLAYER_EVENT_TYPE_PLAY_REQUESTED = 22;

	/**
	 * The activity's onDestroy method has been called
	 */
	int PLAYER_EVENT_APP_ON_DESTROY = 23;

	/**
	 * The Ui's Previous button has been tapped
	 */
	int PLAYER_EVENT_PREV = 24;

	/**
	 * The Ui's Next button has been tapped
	 */
	int PLAYER_EVENT_NEXT = 25;

	/**
	 * Internal use, signals that the next resume() that is going to get fired, comes from an ad provider
	 */
    int PLAYER_EVENT_TYPE_RESUME_LOGICAL_REQUESTED = 26;

	/**
	 * Internal use, signals that the next pause() that is going to get fired, comes from an ad provider
	 */
	int PLAYER_EVENT_TYPE_PAUSE_LOGICAL_REQUESTED = 27;

	/**
	 * Signals an PLAYER_EVENT_TYPE_FINISHED or the finish of the postroll ad break (if the session has ads)
	 */
	int PLAYER_EVENT_TYPE_ALL_PLAYBACK_COMPLETED = 28;

	/**
	 * Internal use, signals that the progress bar is now visible
	 */
	int PLAYER_EVENT_PROGRESS_BAR_VISIBLE = 29;

	/**
	 * Internal use, signals that the progress bar is now gone
	 */
	int PLAYER_EVENT_PROGRESS_BAR_GONE = 30;
	
	/**
	 * Signals that the Captions have been enabled
	 */
	int PLAYER_EVENT_CAPTIONS_ENABLED = 31;

	/**
	 * Signals that the Captions have been disabled
	 */
	int PLAYER_EVENT_CAPTIONS_DISABLED = 32;

	/**
	 * Signals that the available buffer has run out
	 */
	int PLAYER_EVENT_PLAYBACK_STALLED = 33;
```

***

If you have further questions or comments, reach out to us via <amp-sdk-support@akamai.com>
