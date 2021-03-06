##Video Player

<img src="https://raw.githubusercontent.com/halilozercan/bettervideoplayer/master/screens/fullscreen.png" width="600px" />

##### Features

* Completely written in __Kotlin__.
* __Based on the stock MediaPlayer API__ It will work on all devices and all CPUs, and it works with both local and remote sources.
* __Simple__ Much less code is required than alternative options to get up and running.
* __Very configurable__ There are lots of options available to make the player behave exactly how you want it to behave.
* __Swipe Gestures__ Supports the common on-screen scroll behavior which is used by MXPlayer, VLC and other Android video players.
* __Tap Gestures__ Double tap on right or left side of the screen to jump back and forward like Youtube and Netflix players.

---

## Gradle Dependency

[![Release](https://jitpack.io/v/halilozercan/BetterVideoPlayer.svg)](https://jitpack.io/#halilozercan/BetterVideoPlayer)
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg?style=flat-square)](https://www.apache.org/licenses/LICENSE-2.0.html)

The Gradle dependency is available via jitpack

### Dependency

Add this to your app/build.gradle repositories:

```gradle
maven { url 'https://jitpack.io' }
```


## Getting Started

##### Configuring a Player Activity

You will need an `Activity` in your app that will hold the `MVideoPlayer` view and playback content.
There's only a bit of configuration required. However, MVideoPlayer offers great 'customizability'.

*Host Activity should disable recreation on orientation changes. This allows playback to continue
when the device orientation changes. The player will adapt the aspect ratio accordingly. You just need to
set `android:configChanges` values to your `Activity` in `AndroidManifest.xml`:*

<activity
    android:name=".MyPlayerActivity"
    android:label="@string/my_player_activity"
    android:configChanges="orientation|keyboardHidden|screenLayout|screenSize" /> 

##### Layouts

The layout for your player Activity can be very simple. You only need a `MVideoPlayer` view,
all the controls and everything else are created by the player itsel
<com.moktar.mvpkotlin.MVideoPlayer
    android:id="@+id/player"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />

### Notable Features

MVideoPlayer is capable of almost all functionality that you expect from a VideoPlayer. However, it is important to repeat that
MVideoPlayer uses Android MediaPlayer API. Thus, it __does not provide every codec in the world__. In the future, there is a plan for moving underlying
player to ExoPlayer.

#### Captions

MVideoPlayer supports captions in 2 formats; [SRT](https://en.wikipedia.org/wiki/SubRip) and [WEBVTT](https://w3c.github.io/webvtt/). Support for more formats through pull requests will be appreciated.

Captions can be obtained both online and from resource directory.MVideoPlayer __currently does not support captions from local file storage__.

```kotlin
// Online SUBRIP subtitle
mvp.setCaptions("https://www.example.com/subrip.srt", CaptionsView.SubMime.SUBRIP)

// res/raw SUBRIP subtitle
mvp.setCaptions(R.raw.sub, CaptionsView.SubMime.SUBRIP)
```

MVideoPlayer also lets you define the text size(in sp) and color of captions inside XML view.

```xml
<com.moktar.mvpkotlin.MVideoPlayer
        android:id="@+id/mvp"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        mvp:mvp_captionSize="20sp"
        mvp:mvp_captionColor="@android:color/holo_blue_light"/>
```

#### Toolbar

MVideoPlayer deploys a common toolbar at the top of the player. Toolbar is useful in a video player in two different ways.
- It offers a highly customizable Title area.
- You can inflate a menu on the toolbar and define as many actions as you need.

To access toolbar, just use `getToolbar()` method. You can also show and hide the toolbar by using `showToolbar()` and `hideToolbar()`. Besides these methods, it is not recommended to 
alter Toolbar's visibility.

#### Swipe Gestures

Swipe Gestures on a video player are proved to be very useful by MX, VLC and others. Swiping left and right to
seek to any point in video or swipe up and down to control volume and brightness. BetterVideoPlayer comes with
built-in support for these gestures. This feature enables developers to have a player that their users are familiar with.

You can enable or disable gestures by `enableSwipeGestures()` and `disableSwipeGestures()` methods.

__Important point:__ You need to use `enableSwipeGestures(Window)` method to also enable brightness control. Brightness setting needs a reference window.

![Gestures](https://github.com/halilozercan/BetterVideoPlayer/raw/master/screens/gestures.gif)


##### Code Setup

Initializing the player is very simple. You just set a callback listener and a source.

```kotlin
class MyPlayerActivity : AppCompatActivity , MVideoCallback {

    lateinit var player: MVideoPlayer

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_myplayer)

        // Grab a reference to the player view
        player = findViewById(R.id.player)

        // Set the source to the HTTP URL held in the TEST_URL variable.
        // To play files, you can use Uri.fromFile(new File("..."))
        player.setSource(Uri.parse(TEST_URL))

        // From here, the player view will show a progress indicator until the player is prepared.
        // Once it's prepared, the progress indicator goes away and the controls become enabled for the user to begin playback.
    }

    override fun onPause() {
        super.onPause()
        // Make sure the player stops playing if the user presses the home button.
        player.pause()
    }
    
    companion object {
        const val TEST_URL = "http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"
    }
}
```

You can see the almost identical code in action in the sample project.

---

## Programmatic Control

Here's a list of methods that can be used to control the `MVideoPlayer` programmatically.
Full list of available methods is in [IMVideoPlayer](https://github.com/halilozercan/BetterVideoPlayer/blob/master/bvpkotlin/src/main/java/com/halilibo/bvpkotlin/IBetterVideoPlayer.kt) interface.

```kotlin
val player: MVideoPlayer = findViewById<BetterVideoPlayer>(R.id.mvp)

// Sets a video source to be played.
player.setSource(Uri)

// Sets a callback to receive normal player events.
player.setCallback(VideoCallBack)

// Sets a callback that can be used to retrieve updates of the current playback position.
player.setProgressCallback(VideoProgressCallback)

// Starts or resumes playback.
player.start()

// Seeks to a position in the video.
player.seekTo(int)

// Pauses playback.
player.pause()

// Stops playback.
player.stop()

// Resets the player, allowing a new source to be set.
player.reset()

// Releases the underlying MediaPlayer and cleans up resources.
player.release()

// Shows the default controls. They can be hidden again if the user taps the player.
player.showControls()

// Hides the default controls. They can be shown again if the user taps the player.
player.hideControls()

// Shows the controls if they're hidden, hides them if they're shown.
player.toggleControls()

// Enables double tap to seek like in Youtube. Input: seek time in milliseconds
player.enableDoubleTapSeek(int)

// Returns true if the default controls are currently shown.
player.isControlsShown()

// Hide the default controls and prevents them from being shown.
player.disableControls()

// Undoes disableControls()
player.enableControls()

// Returns true if the player has prepared for playback entirely
player.isPrepared()

// Returns true if the player is NOT paused.
player.isPlaying()

// Returns the current position of playback.
player.getCurrentPosition()

// Returns the total duration of the video.
player.getDuration()
```

---

## Programmatic Configuration

There are options that can be used to change the default behavior of the `MVideoPlayer`:

```kotlin
val player: MVideoPlayer = findViewById<BetterVideoPlayer>(R.id.mvp)

// Defaults to true. The controls fade out when playback starts.
player.setHideControlsOnPlay(boolean)

// Defaults to false. Immediately starts playback when the player becomes prepared.
player.setAutoPlay(boolean)

// Sets a position that will be skipped to right when the player becomes prepared. Only happens once when set.
player.setInitialPosition(int)

// Sets a custom drawable for play, pause and restart button states.
player.setButtonDrawable(ButtonType, Drawable)

// Sets the left and right volume levels. The player must be prepared first.
player.setVolume(float, float)

// Sets whether or not the player will start playback over when reaching the end.
player.setLoop(false)

// Registers a caption source
player.setCaptions(Uri, mimeType)
```

---

## XML Configuration

The programmatic configuration options shown above can also be configured directly from your layout:

```xml
<com.moktar.mvpkotlin.MVideoPlayer
    android:id="@+id/player"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mvp_autoPlay="false"
    app:mvp_disableControls="false"
    app:mvp_hideControlsOnPlay="true"
    app:mvp_pauseDrawable="@drawable/bvp_action_pause"
    app:mvp_playDrawable="@drawable/bvp_action_play"
    app:mvp_restartDrawable="@drawable/bvp_action_restart"
    app:mvp_source="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"
    app:bvp_captionSize="22sp"
    app:bvp_captionColor="@color/caption_color"
    app:bvp_gestureType="SwipeGesture"
    app:bvp_loop="false" />
```
