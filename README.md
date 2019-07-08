# cordova-plugin-firebasex
This plugin is a fork of [cordova-plugin-firebase](https://github.com/arnesson/cordova-plugin-firebase) which has been updated to fix several issues.

It brings push notifications, analytics, event tracking, crash reporting and more from Google Firebase to your Cordova project!  

Supported platforms: Android and iOS

<!-- DONATE -->
[![donate](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG_global.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=ZRD3W47HQ3EMJ)

I dedicate a considerable amount of my free time to developing and maintaining this Cordova plugin, along with my other Open Source software.
To help ensure this plugin is kept updated, new features are added and bugfixes are implemented quickly, please donate a couple of dollars (or a little more if you can stretch) as this will help me to afford to dedicate time to its maintenance. Please consider donating if you're using this plugin in an app that makes you money, if you're being paid to make the app, if you're asking for new features or priority bug fixes.
<!-- END DONATE -->


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Installation](#installation)
  - [Supported Cordova Versions](#supported-cordova-versions)
  - [Migrating from cordova-plugin-firebase](#migrating-from-cordova-plugin-firebase)
- [Build environment notes](#build-environment-notes)
  - [Specifying dependent library versions](#specifying-dependent-library-versions)
  - [PhoneGap Build](#phonegap-build)
  - [Android-specific](#android-specific)
    - [AndroidX](#androidx)
  - [Google Play Services and Firebase libraries](#google-play-services-and-firebase-libraries)
  - [iOS-specific](#ios-specific)
    - [Strip debug symbols](#strip-debug-symbols)
- [Firebase config setup](#firebase-config-setup)
- [Cloud messaging](#cloud-messaging)
  - [Android Notifications](#android-notifications)
    - [Background notifications](#background-notifications)
    - [Foreground notifications](#foreground-notifications)
    - [Android Notification Channels](#android-notification-channels)
    - [Android Notification Icons](#android-notification-icons)
      - [Android Default Notification Icon](#android-default-notification-icon)
      - [Android Large Notification Icon](#android-large-notification-icon)
      - [Android Custom Notification Icons](#android-custom-notification-icons)
    - [Android Notification Color](#android-notification-color)
    - [Android Notification Sound](#android-notification-sound)
  - [Android Data Messages](#android-data-messages)
    - [Data Message Notification Keys](#data-message-notification-keys)
- [Google Tag Manager](#google-tag-manager)
  - [Android](#android)
  - [iOS](#ios)
- [API](#api)
  - [getToken](#gettoken)
  - [onTokenRefresh](#ontokenrefresh)
  - [onNotificationOpen](#onnotificationopen)
  - [grantPermission (iOS only)](#grantpermission-ios-only)
  - [hasPermission](#haspermission)
  - [setBadgeNumber](#setbadgenumber)
  - [getBadgeNumber](#getbadgenumber)
  - [clearAllNotifications](#clearallnotifications)
  - [subscribe](#subscribe)
  - [unsubscribe](#unsubscribe)
  - [unregister](#unregister)
  - [logEvent](#logevent)
  - [setCrashlyticsUserId](#setcrashlyticsuserid)
  - [setScreenName](#setscreenname)
  - [setUserId](#setuserid)
  - [setUserProperty](#setuserproperty)
  - [verifyPhoneNumber](#verifyphonenumber)
    - [Android](#android-1)
    - [iOS](#ios-1)
  - [fetch](#fetch)
  - [activateFetched](#activatefetched)
  - [getValue](#getvalue)
  - [getByteArray](#getbytearray)
  - [getInfo](#getinfo)
  - [setConfigSettings](#setconfigsettings)
  - [setDefaults](#setdefaults)
  - [createChannel](#createchannel)
  - [setDefaultChannel](#setdefaultchannel)
    - [Default Android Channel Properties](#default-android-channel-properties)
  - [startTrace](#starttrace)
  - [incrementCounter](#incrementcounter)
  - [stopTrace](#stoptrace)
  - [setAnalyticsCollectionEnabled](#setanalyticscollectionenabled)
  - [sendCrash](#sendcrash)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

  ## Installation
Install the plugin by adding it to your project's config.xml:
```
<plugin name="cordova-plugin-firebasex" spec="latest" />
```
or by running:
```
cordova plugin add cordova-plugin-firebasex
```

### Supported Cordova Versions
- cordova: `>= 9`
- cordova-android: `>= 8`
- cordova-ios: `>= 9`

### Migrating from cordova-plugin-firebase
If you already have [cordova-plugin-firebase](https://github.com/arnesson/cordova-plugin-firebase) installed in your Cordova project, you need to completely remove it before installing this plugin otherwise they will conflict and cause build errors in your project. The safest way of doing this is as follows:

    rm -Rf platforms/android
    cordova plugin rm cordova-plugin-firebase
    rm -Rf plugins/ node_modules/
    npm install
    cordova plugin add cordova-plugin-firebasex
    cordova platform add android

## Build environment notes

### Specifying dependent library versions
This plugin depends on various components such as the Firebase SDK which are pulled in at build-time by Gradle on Android and Cocoapods on iOS.
By default this plugin pins specific versions of these in [its `plugin.xml`](https://github.com/dpa99c/cordova-plugin-firebase/blob/master/plugin.xml) where you can find the currently pinned Android & iOS versions as `<preference>`'s, for example:

    <preference name="ANDROID_FIREBASE_CORE_VERSION" default="17.0.0" />

These defaults can be overridden at plugin installation time by specifying plugin variables as command-line arguments, for example:

    cordova plugin add cordova-plugin-firebasex --variable ANDROID_FIREBASE_CORE_VERSION=17.0.0
    
Or you can specify them as plugin variables in your `config.xml`, for example:

    <plugin name="cordova-plugin-firebasex" spec="latest">
        <variable name="ANDROID_FIREBASE_CORE_VERSION" value="17.0.0" />
    </plugin>
    
The following plugin variables are use to specify the follow Gradle dependency versions on Android:

- `ANDROID_PLAY_SERVICES_TAGMANAGER_VERSION` => `com.google.android.gms:play-services-tagmanager`
- `ANDROID_FIREBASE_CORE_VERSION` => `com.google.firebase:firebase-core`
- `ANDROID_FIREBASE_MESSAGING_VERSION` => `com.google.firebase:firebase-messaging`
- `ANDROID_FIREBASE_CONFIG_VERSION` => `com.google.firebase:firebase-config`
- `ANDROID_FIREBASE_PERF_VERSION` => `com.google.firebase:firebase-perf`
- `ANDROID_FIREBASE_AUTH_VERSION` => `com.google.firebase:firebase-auth`
- `ANDROID_CRASHLYTICS_VERSION` => `com.crashlytics.sdk.android:crashlytics`
- `ANDROID_CRASHLYTICS_NDK_VERSION` => `com.crashlytics.sdk.android:crashlytics-ndk`
- `ANDROID_SHORTCUTBADGER_VERSION` => `me.leolin:ShortcutBadger`

For example, to explicitly specify all the component versions at plugin install time:

    cordova plugin add cordova-plugin-firebasex \
        --variable ANDROID_PLAY_SERVICES_TAGMANAGER_VERSION=17.0.0 \
        --variable ANDROID_FIREBASE_CORE_VERSION=17.0.0 \
        --variable ANDROID_FIREBASE_MESSAGING_VERSION=19.0.0 \
        --variable ANDROID_FIREBASE_CONFIG_VERSION=18.0.0 \
        --variable ANDROID_FIREBASE_PERF_VERSION=18.0.0 \
        --variable ANDROID_FIREBASE_AUTH_VERSION=18.0.0 \
        --variable ANDROID_CRASHLYTICS_VERSION=2.10.1 \
        --variable ANDROID_CRASHLYTICS_NDK_VERSION=2.1.0 \
        --variable ANDROID_SHORTCUTBADGER_VERSION=1.1.22 \
        
### PhoneGap Build
This plugin will not work with Phonegap Build (and other remote cloud build envs) do not support Cordova hook scripts as they are used by this plugin to configure the native platform projects.
        
### Android-specific
#### AndroidX
This plugin has been migrated to use [AndroidX (Jetpack)](https://developer.android.com/jetpack/androidx/migrate) which is the successor to the [Android Support Library](https://developer.android.com/topic/libraries/support-library/index).
This is implemented by adding a dependency on [cordova-plugin-androidx](https://github.com/dpa99c/cordova-plugin-androidx) which enables AndroidX in the Android platform of a Cordova project.

This is because the [major release of the Firebase and Play Services libraries on 17 June 2019](https://developers.google.com/android/guides/releases#june_17_2019) were migrated to AndroidX.

Therefore if your project includes any plugins which are dependent on the legacy Android Support Library, you should add [cordova-plugin-androidx-adapter](https://github.com/dpa99c/cordova-plugin-androidx-adapter) to your project.
This plugin will dynamically migrate any plugin code from the Android Support Library to AndroidX equivalents.

### Google Play Services and Firebase libraries
Your Android build may fail if you are installing multiple plugins that use the Google Play Services library.  
This is caused by plugins installing different versions of the Google Play Services library.  
This can be resolved by installing [cordova-android-play-services-gradle-release](https://github.com/dpa99c/cordova-android-play-services-gradle-release) which enables you to override the versions specified by other plugins in order to align them.

Similarly, if your build is failing because multiple plugins are installing different versions of the Firebase library, 
you can try installing [cordova-android-firebase-gradle-release](https://github.com/dpa99c/cordova-android-firebase-gradle-release) to align these.  

### iOS-specific
#### Strip debug symbols
If your iOS app build contains too many debug symbols (i.e. because you include lots of libraries via a Cocoapods), you might get an error (e.g. [issue #28](https://github.com/dpa99c/cordova-plugin-firebase/issues/28)) when you upload your binary to App Store Connect, e.g.:

    ITMS-90381: Too many symbol files - These symbols have no corresponding slice in any binary [16EBC8AC-DAA9-39CF-89EA-6A58EB5A5A2F.symbols, 1B105D69-2039-36A4-A04D-96C1C5BAF235.symbols, 476EACDF-583B-3B29-95B9-253CB41097C8.symbols, 9789B03B-6774-3BC9-A8F0-B9D44B08DCCB.symbols, 983BAE60-D245-3291-9F9C-D25E610846AC.symbols].

To prevent this, you'll need to edit the `platforms/ios/Podfile` to add a config block to prevent symbolification of all libraries included via Cocoapods - (see here)[https://stackoverflow.com/a/48518656/777265]

Then run `pod install` from `platforms/ios/`

Note: if you do this, any crashes that occur within libraries included via Cocopods will not be recorded in Crashlytics or other crash reporting services.

## Firebase config setup
There's a handy [installation and setup guide on medium.com](https://medium.com/@felipepucinelli/how-to-add-push-notifications-in-your-cordova-application-using-firebase-69fac067e821).
However, if using this, remember this forked plugin is `cordova-plugin-firebasex` (not `cordova-plugin-firebase`).

Download your Firebase configuration files, `GoogleService-Info.plist` for iOS and `google-services.json` for android, and place them in the root folder of your cordova project.
Check out this [firebase article](https://support.google.com/firebase/answer/7015592) for details on how to download the files.

```
- My Project/
    platforms/
    plugins/
    www/
    config.xml
    google-services.json       <--
    GoogleService-Info.plist   <--
    ...
```

IMPORTANT: The Firebase SDK requires the configuration files to be present and valid, otherwise your app will crash on boot or Firebase features won't work.


## Cloud messaging
There are 2 distinct types of messages that can be sent by Firebase Cloud Messaging (FCM):

- [Notification messages](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications)
    - automatically displayed to the user by the operating system on behalf of the client app **while your app is not running or is in the background**
        - **if your app is in the foreground when the notification message arrives**, it is passed to the client app and it is the responsibility of the client app to display it.
    - have a predefined set of user-visible keys and an optional data payload of custom key-value pairs.
- [Data messages](https://firebase.google.com/docs/cloud-messaging/concept-options#data_messages)
    - Client app is responsible for processing data messages.
    - Data messages have only custom key-value pairs.
    
Note: only notification messages can be sent via the Firebase Console - data messages must be sent via the [FCM APIs](https://firebase.google.com/docs/cloud-messaging/server).
    

### Android Notifications
Notifications on Android can be customised to specify the sound, icon, LED colour, etc. that's displayed when the notification arrives.

#### Background notifications
If the notification message arrives while the app is in the background/not running, it will be displayed as a system notification.
No callback can be made by the plugin to the app when the message arrives since the display of the notification is entirely handled by the operating system.
However if the user taps the system notification, this launches/resumes the app and the notification title, body and optional data payload is passed to the [onNotificationOpen](#onnotificationopen) callback.
When the `onNotificationOpen` is called in response to a user tapping a system notification, it will be passed the property `tap: true`.

In addition to the title and body of the message, Android system notifications support specification of the following notification settings:
- [Icon](#android-notification-icons)
- [Sound](#android-notification-sound)
- [Color accent](#android-notification-color)
- [Channel ID](#android-notification-channels) (Android 8.0 (O) and above)

#### Foreground notifications
If the notification message arrives while the app is in running in the foreground, by default **it will NOT be displayed as a system notification**.
Instead the notification title, body and optional data payload will be passed to the [onNotificationOpen](#onnotificationopen) callback for the plugin to handle.
When the `onNotificationOpen` is called in response to a foreground notification, it will be passed the property `tap: false`.

If you include the `foregroundNotification` key (the value doesn't matter) in the `data` payload, the plugin will also display a system notification upon receiving the notification messages while the app is running in the foreground.
For example:

    {
      "name": "my_notification",
      "notification": {
        "body": "Notification body",
        "title": "Notification title"
      },
      "data": {
        "foregroundNotification": "true",
      }
    }

You can set additional properties of the foreground notification using the same key names as for [Android Data Message Notifications]((#data-message-notification-keys)).    
    
#### Android Notification Channels
- Android 8 (O) introduced [notification channels](https://developer.android.com/training/notify-user/channels).
- Notification channels are configured by the app and used to determine the sound/lights/vibration settings of system notifications.
- By default, this plugin creates a default channel with [default properties]((#default-android-channel-properties)
    - These can be overridden via the [setDefaultChannel](#setdefaultchannel) function. 
- The plugin  enables the creation of additional custom channels via the [createChannel](#createchannel) function.

On Android 7 and below, the sound setting of system notifications is specified in the notification message itself, for example:

    {
      "name": "my_notification",
        "notification": {
          "body": "Notification body",
          "title": "Notification title"
        },
      "android": {
        "notification": {
          "sound": "crystal",
          "tag": "default"
      }
    }


#### Android Notification Icons
By default the plugin will use the default app icon for notification messages.

##### Android Default Notification Icon
To define a custom default notification icon, you need to create the images and deploy them to the `<projectroot>/platforms/android/app/src/main/res/<drawable-DPI>` folders.
The easiest way to do this is using the [Image Asset Studio in Android Studio](https://developer.android.com/studio/write/image-asset-studio#create-notification) or using the [Android Asset Studio webapp](https://romannurik.github.io/AndroidAssetStudio/icons-notification.html#source.type=clipart&source.clipart=ac_unit&source.space.trim=1&source.space.pad=0&name=notification_icon).

The icons should be monochrome transparent PNGs with the following sizes:

- mdpi: 24x24
- hdpi: 36x36
- xhdpi: 48x48
- xxhdpi: 72x72
- xxxhdpi: 96x96

Once you've created the images, you need to deploy them from your Cordova project to the native Android project.
To do this, copy the `drawable-DPI` image directories into your Cordova project and add `<resource-file>` entries to the `<platform name="android">` section of your `config.xml`, for example:

    <platform name="android">
        <resource-file src="res/android/drawable-mdpi/notification_icon.png" target="app/src/main/res/drawable-mdpi/notification_icon.png" />
        <resource-file src="res/android/drawable-hdpi/notification_icon.png" target="app/src/main/res/drawable-hdpi/notification_icon.png" />
        <resource-file src="res/android/drawable-xhdpi/notification_icon.png" target="app/src/main/res/drawable-xhdpi/notification_icon.png" />
        <resource-file src="res/android/drawable-xxhdpi/notification_icon.png" target="app/src/main/res/drawable-xxhdpi/notification_icon.png" />
        <resource-file src="res/android/drawable-xxxhdpi/notification_icon.png" target="app/src/main/res/drawable-xxxhdpi/notification_icon.png" />
    </platform>
    
The default notification icon images **must** be named `notification_icon.png`. 

You then need to add a `<config-file>` block to the `config.xml` which will instruct Firebase to use your icon as the default for notifications:

    <platform name="android">
        <config-file target="AndroidManifest.xml" parent="application">
            <meta-data android:name="com.google.firebase.messaging.default_notification_icon" android:resource="@drawable/notification_icon" />
        </config-file>
    </platform>


##### Android Large Notification Icon
The default notification icons above are monochrome, however you can additionally define a larger multi-coloured icon.

**NOTE:** FCM currently does not support large icons in system notifications displayed for notification messages received in the while the app is in the background (or not running).
So the large icon will currently only be used if specified in [data messages](#android-data-messages) or [foreground notifications](#foreground-notifications).

The large icon image should be a PNG-24 that's 256x256 pixels and must be named `notification_icon_large.png` and should be placed in the `drawable-xxxhdpi` resource directory.
As with the small icons, you'll need to add a `<resource-file>` entry to the `<platform name="android">` section of your `config.xml`:

    <platform name="android">
        <resource-file src="res/android/drawable-xxxhdpi/notification_icon_large.png" target="app/src/main/res/drawable-xxxhdpi/notification_icon_large.png" />
    </platform>


##### Android Custom Notification Icons
You can define additional sets of notification icons in the same manner as above.
These can be specified in notification or data messages.

For example:

        <resource-file src="res/android/drawable-mdpi/my_icon.png" target="app/src/main/res/drawable-mdpi/my_icon.png" />
        <resource-file src="res/android/drawable-hdpi/my_icon.png" target="app/src/main/res/drawable-hdpi/my_icon.png" />
        <resource-file src="res/android/drawable-xhdpi/my_icon.png" target="app/src/main/res/drawable-xhdpi/my_icon.png" />
        <resource-file src="res/android/drawable-xxhdpi/my_icon.png" target="app/src/main/res/drawable-xxhdpi/my_icon.png" />
        <resource-file src="res/android/drawable-xxxhdpi/my_icon.png" target="app/src/main/res/drawable-xxxhdpi/my_icon.png" />
        <resource-file src="res/android/drawable-xxxhdpi/my_icon_large.png" target="app/src/main/res/drawable-xxxhdpi/my_icon_large.png" />
        
When sending an FCM notification message, you will then specify the icon name in the `android.notification` section, for example:

    {
      "name": "my_notification",
      "notification": {
        "body": "Notification body",
        "title": "Notification title"
      },
      "android": {
        "notification": {
          "icon": "my_icon",
        }
      },
      "data": {
        "foregroundNotification": "true",
      }
    }

You can also reference these icons in [data messages](#android-data-messages), for example:

    {
      "name": "my_data",
      "data" : {
        "body" : "Data message body",
        "title": "Data message title",
        "foregroundNotification": "true",
        "icon": "my_icon",
      }
    }         


#### Android Notification Color
On Android Lollipop (5.0/API 21) and above you can set the default accent color for the notification by adding a color setting.
This is defined as an [ARGB colour](https://en.wikipedia.org/wiki/RGBA_color_space#ARGB_(word-order)) which the plugin sets by default to `#FF00FFFF` (cyan).

You can override this default by specifying a value using the `ANDROID_ICON_ACCENT` plugin variable during plugin installation, for example:

    cordova plugin add cordova-plugin-firebasex --variable ANDROID_ICON_ACCENT=#FF123456
    
You can override the default color accent by specifying the `colour` key as an RGB value in a notification message, e.g.:

    {
      "name": "my_notification",
      "notification": {
        "body": "Notification body",
        "title": "Notification title"
      },
      "android": {
        "notification": {
          "color": "#00ff00"
        }
      }
    }
    
And in a data message:

    {
      "name": "my_data",
      "data" : {
        "body" : "Data message body",
        "title": "Data message title",
        "foregroundNotification": "true",
        "color": "#00ff00"
      }
    }         


#### Android Notification Sound
The plugin supports custom sounds for notifications.

Sound files must be in `.mp3` format and deployed to the `/res/raw` directory in the Android project.
To do this, you can add `<resource-file>` tags to your `config.xml` to deploy the files, for example:

    <platform name="android">
        <resource-file src="res/android/raw/my_sound.mp3" target="app/src/main/res/raw/my_sound.mp3" />
    </platform>

In a notification message, you specify the sound file name in the `android.notification` section, for example:

    {
      "name": "my_notification",
      "notification": {
        "body": "Notification body",
        "title": "Notification title"
      },
      "android": {
        "notification": {
          "sound": "crystal"
        }
      }
    }
    
And in a data message by specifying it in the `data` section:

    {
      "name": "my_data",
      "data" : {
        "body" : "Data message body",
        "title": "Data message title",
        "foregroundNotification": "true",
        "sound": "crystal"
      }
    } 

### Android Data Messages
FCM data messages are sent as an arbitrary k/v structure and by default are passed to the app for it to handle them.

**NOTE:** FCM data messages **cannot** be send from the Firebase Console - they can only be sent via the FCM APIs.

#### Data Message Notification Keys
This plugin supports specification of specific key names which enables a data message to be displayed as a system notification.
These key should be placed in the `data` section of the message.

Note: [foreground notifications](#foreground-notifications) can also make use of these keys.
 
The plugin supports the following keys:

- `foregroundNotification` - setting this key to any value causes the plugin to display a system notification when the data message is received.
    - the follow keys are only relevant if this key is set
- `icon` - name of a [custom notification icon](#android-custom-notification-icons) in the drawable resources
    - if not specified, the plugin will use the default `notification_icon` if it exists; otherwise the default app icon will be displayed
    - if a [large icon](#android-large-notification-icon) has been defined, it will also be displayed in the system notification.
- `color` - the [color accent](#android-notification-color) to use for the small notification icon
    - if not specified, the default color accent will be used
- `channel_id` - ID of the [notification channel](#android-notification-channels) to use to display the notification
    - Only applies to Android 8.0 and above   
    - If not specified, the [default notification channel](#default-android-channel-properties) will be used.
        - You can override the default configuration for the default notification channel using [setDefaultChannel](#setdefaultchannel). 
    - You can create additional channels using [createChannel](#createchannel).
- `priority` - Specifies the notification priority
    - Possible values:
        - `2` - Highest notification priority for your application's most important items that require the user's prompt attention or input.
        - `1` - Higher notification priority for more important notifications or alerts.
        - `0` - Default notification priority.
        - `-1` - Lower notification priority for items that are less important.
        - `-2` - Lowest notification priority. These items might not be shown to the user except under special circumstances, such as detailed notification logs.
    - Defaults to `2` if not specified.
- `visibility` - Specifies the notification visibility
    - Possible values:
        - `1` - Show this notification in its entirety on all lockscreens.
        - `0` - Show this notification on all lockscreens, but conceal sensitive or private information on secure lockscreens.
        - `-1` - Do not reveal any part of this notification on a secure lockscreen.
    - Defaults to `1` if not specified.

The following keys only apply to Android 7 and below. 
On Android 8 and above they will be ignored - the `channel_id` property should be used to specify a [notification channel](#android-notification-channels) with equivalent settings.

- `sound` - name of a sound resource to play as the [notification sound](#android-notification-sound)
    - if not specified, no sound is played
    - `default` plays the default device notification sound
    - otherwise should be the name of an `.mp3` file in the `/res/raw` directory, e.g. `my_sound.mp3` => `"sounds": "my_sound"`
- `lights` - color and pattern to use to blink the LED light
    - if not defined, LED will not blink
    - in the format `ARGB, time_on_ms, time_off_ms` where
        - `ARGB` is an ARGB color definition e.g. `#ffff0000`
        - `time_on_ms` is the time in milliseconds to turn the LED on for
        - `time_off_ms` is the time in milliseconds to turn the LED off for
    - e.g. `"lights": "#ffff0000, 250, 250"`
- `vibrate` - pattern of vibrations to use when the message arrives
    - if not specified, device will not vibrate
    - an array of numbers specifying the time in milliseconds to vibrate
    - e.g. `"vibrate": "500, 200, 500"`

Example data message with Android notification keys:

    {
      "name": "my_data_message",
      "data" : {
        "body" : "Message body",
        "title": "Message title",
        "foregroundNotification": "true",
        "channel_id": "my_channel",
        "priority": "2",
        "visibility": "1",
        "color": "#ff0000",
        "icon": "coffee",
        "sound": "crystal",
        "vibrate": "500, 200, 500",
        "lights": "#ffff0000, 250, 250"
      }
    }


## Google Tag Manager
Download your container-config json file from Tag Manager and add a resource-file node in your `config.xml`.

### Android
```
<platform name="android">
    <resource-file src="GTM-XXXXXXX.json" target="assets/containers/GTM-XXXXXXX.json" />
    ...
```

### iOS
```
<platform name="ios">
    <resource-file src="GTM-YYYYYYY.json" />
    ...
```

## API
The list of available methods for this plugin is described below.

### getToken

Get the device token (id):
```
window.FirebasePlugin.getToken(function(token) {
    // save this server-side and use it to push notifications to this device
    console.log(token);
}, function(error) {
    console.error(error);
});
```
Note that token will be null if it has not been established yet.

### onTokenRefresh

Register for token changes:
```
window.FirebasePlugin.onTokenRefresh(function(token) {
    // save this server-side and use it to push notifications to this device
    console.log(token);
}, function(error) {
    console.error(error);
});
```
This is the best way to get a valid token for the device as soon as the token is established

### onNotificationOpen

Register notification callback:
```
window.FirebasePlugin.onNotificationOpen(function(notification) {
    console.log(notification);
}, function(error) {
    console.error(error);
});
```
Notification flow:

1. App is in foreground:
    1. User receives the notification data in the JavaScript callback without any notification on the device itself (this is the normal behaviour of push notifications, it is up to you, the developer, to notify the user)
2. App is in background:
    1. User receives the notification message in its device notification bar
    2. User taps the notification and the app opens
    3. User receives the notification data in the JavaScript callback

Notification icon on Android:

[Changing notification icon](NOTIFICATIONS.md#changing-notification-icon)

### grantPermission (iOS only)

Grant permission to receive push notifications (will trigger prompt):
```
window.FirebasePlugin.grantPermission();
```
### hasPermission

Check permission to receive push notifications:
```
window.FirebasePlugin.hasPermission(function(data){
    console.log(data.isEnabled);
});
```

### setBadgeNumber

Set a number on the icon badge:
```
window.FirebasePlugin.setBadgeNumber(3);
```

Set 0 to clear the badge
```
window.FirebasePlugin.setBadgeNumber(0);
```

### getBadgeNumber

Get icon badge number:
```
window.FirebasePlugin.getBadgeNumber(function(n) {
    console.log(n);
});
```

### clearAllNotifications

Clear all pending notifications from the drawer:
```
window.FirebasePlugin.clearAllNotifications();
```

### subscribe

Subscribe to a topic:
```
window.FirebasePlugin.subscribe("example");
```

### unsubscribe

Unsubscribe from a topic:
```
window.FirebasePlugin.unsubscribe("example");
```

### unregister

Unregister from firebase, used to stop receiving push notifications. Call this when you logout user from your app. :
```
window.FirebasePlugin.unregister();
```

### logEvent

Log an event using Analytics:
```
window.FirebasePlugin.logEvent("select_content", {content_type: "page_view", item_id: "home"});
```

### setCrashlyticsUserId

Set Crashlytics user identifier.
- https://firebase.google.com/docs/crashlytics/customize-crash-reports?authuser=0#set_user_ids

### setScreenName

Set the name of the current screen in Analytics:
```
window.FirebasePlugin.setScreenName("Home");
```

### setUserId

Set a user id for use in Analytics:
```
window.FirebasePlugin.setUserId("user_id");
```

### setUserProperty

Set a user property for use in Analytics:
```
window.FirebasePlugin.setUserProperty("name", "value");
```

### verifyPhoneNumber

Request a verification ID and send a SMS with a verification code. Use them to construct a credential to sign in the user (in your app).
- https://firebase.google.com/docs/auth/android/phone-auth
- https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signInWithCredential
- https://firebase.google.com/docs/reference/js/firebase.User#linkWithCredential

**NOTE: This will only work on physical devices.**

iOS will return: credential (string)
Android will return:
credential.verificationId (object and with key verificationId)
credential.instantVerification (boolean)
credential.code (string) (note that this key only exists if instantVerification is true)

You need to use device plugin in order to access the right key.

IMPORTANT NOTE: Android supports auto-verify and instant device verification. Therefore in that case it doesn't make sense to ask for an sms code as you won't receive one. In this case you'll get a credential.verificationId and a credential.code where code is the auto received verification code that would normally be sent via sms. To log in using this procedure you must pass this code to PhoneAuthProvider.credential(verificationId, code). You'll find an implementation example further below.

When using node.js Firebase Admin-SDK, follow this tutorial:
- https://firebase.google.com/docs/auth/admin/create-custom-tokens

Pass back your custom generated token and call
```js
firebase.auth().signInWithCustomToken(customTokenFromYourServer);
```
instead of
```
firebase.auth().signInWithCredential(credential)
```
**YOU HAVE TO COVER THIS PROCESS, OR YOU WILL HAVE ABOUT 5% OF USERS STICKING ON YOUR SCREEN, NOT RECEIVING ANYTHING**
If this process is too complex for you, use this awesome plugin
- https://github.com/chemerisuk/cordova-plugin-firebase-authentication

It's not perfect but it fits for the most use cases and doesn't require calling your endpoint, as it has native phone auth support.

```
window.FirebasePlugin.verifyPhoneNumber(number, timeOutDuration, function(credential) {
    console.log(credential);

    // if instant verification is true use the code that we received from the firebase endpoint, otherwise ask user to input verificationCode:
    var code = credential.instantVerification ? credential.code : inputField.value.toString();

    var verificationId = credential.verificationId;

    var credential = firebase.auth.PhoneAuthProvider.credential(verificationId, code);

    // sign in with the credential
    firebase.auth().signInWithCredential(credential);

    // OR link to an account
    firebase.auth().currentUser.linkWithCredential(credential)
}, function(error) {
    console.error(error);
});
```


#### Android
To use this auth you need to configure your app SHA hash in the android app configuration in the firebase console.
See https://developers.google.com/android/guides/client-auth to know how to get SHA app hash.

#### iOS
Setup your push notifications first, and verify that they are arriving on your physical device before you test this method. Use the APNs auth key to generate the .p8 file and upload it to firebase.  When you call this method, FCM sends a silent push to the device to verify it.

### fetch

Fetch Remote Config parameter values for your app:
```
window.FirebasePlugin.fetch(function () {
    // success callback
}, function () {
    // error callback
});
// or, specify the cacheExpirationSeconds
window.FirebasePlugin.fetch(600, function () {
    // success callback
}, function () {
    // error callback
});
```

### activateFetched

Activate the Remote Config fetched config:
```
window.FirebasePlugin.activateFetched(function(activated) {
    // activated will be true if there was a fetched config activated,
    // or false if no fetched config was found, or the fetched config was already activated.
    console.log(activated);
}, function(error) {
    console.error(error);
});
```

### getValue

Retrieve a Remote Config value:
```
window.FirebasePlugin.getValue("key", function(value) {
    console.log(value);
}, function(error) {
    console.error(error);
});
```

### getByteArray
Android only.
Retrieve a Remote Config byte array:
```
window.FirebasePlugin.getByteArray("key", function(bytes) {
    // a Base64 encoded string that represents the value for "key"
    console.log(bytes.base64);
    // a numeric array containing the values of the byte array (i.e. [0xFF, 0x00])
    console.log(bytes.array);
}, function(error) {
    console.error(error);
});
```

### getInfo
Android only.
Get the current state of the FirebaseRemoteConfig singleton object:
```
window.FirebasePlugin.getInfo(function(info) {
    // the status of the developer mode setting (true/false)
    console.log(info.configSettings.developerModeEnabled);
    // the timestamp (milliseconds since epoch) of the last successful fetch
    console.log(info.fetchTimeMillis);
    // the status of the most recent fetch attempt (int)
    // 0 = Config has never been fetched.
    // 1 = Config fetch succeeded.
    // 2 = Config fetch failed.
    // 3 = Config fetch was throttled.
    console.log(info.lastFetchStatus);
}, function(error) {
    console.error(error);
});
```

### setConfigSettings
Android only.
Change the settings for the FirebaseRemoteConfig object's operations:
```
var settings = {
    developerModeEnabled: true
}
window.FirebasePlugin.setConfigSettings(settings);
```

### setDefaults
Android only.
Set defaults in the Remote Config:
```
// define defaults
var defaults = {
    // map property name to value in Remote Config defaults
    mLong: 1000,
    mString: 'hello world',
    mDouble: 3.14,
    mBoolean: true,
    // map "mBase64" to a Remote Config byte array represented by a Base64 string
    // Note: the Base64 string is in an array in order to differentiate from a string config value
    mBase64: ["SGVsbG8gV29ybGQ="],
    // map "mBytes" to a Remote Config byte array represented by a numeric array
    mBytes: [0xFF, 0x00]
}
// set defaults
window.FirebasePlugin.setDefaults(defaults);

```

### createChannel
Android 8+ only.
Creates a custom channel to be used by notification messages received in background which have the `android_channel_id` property set with the `id` of the channel. 
For each channel you may set the sound to be played, the color of the phone LED (if supported by the device), whether to vibrate and set vibration pattern (if supported by the device), importance and visibility. 
Channels should be created as soon as possible (on program start) so notifications can work as expected.
A default channel is created by the plugin at app startup; the properties of this can be overridden see [setDefaultChannel](#setdefaultchannel) 

Calling on Android 7 or below or another platform will have no effect.

```
// Define custom  channel - all keys are except 'id' are optional.
var channel  = {
    // channel ID - must be unique per app package
    id: "my_channel_id",
    
    // Channel name. Default: empty string
    name: "Channel name",
    
    //The sound to play once a push comes. Default value: 'default'
    //Values allowed:
    //'default' - plays the default notification sound
    //'ringtone' - plays the currently set ringtone
    //'false' - silent; don't play any sound
    //filename - the filename of the sound file located in '/res/raw' without file extension (mysound.mp3 -> mysound) 
    sound: "mysound",
    
    //Vibrate on new notification. Default value: true
    //Possible values:
    //Boolean - vibrate or not
    //Array - vibration pattern - e.g. [500, 200, 500] - milliseconds vibrate, milliseconds pause, vibrate, pause, etc.
    vibration: true,
    
    // Whether to blink the LED
    light: true,
    
    //LED color in ARGB format - this example BLUE color. If set to -1, light color will be default. Default value: -1.
    lightColor: "0xFF0000FF",
    
    //Importance - integer from 0 to 4. Default value: 3
    //0 - none - no sound, does not show in the shade
    //1 - min - no sound, only shows in the shade, below the fold
    //2 - low - no sound, shows in the shade, and potentially in the status bar
    //3 - default - shows everywhere, makes noise, but does not visually intrude
    //4 - high - shows everywhere, makes noise and peeks
    importance: 4,
    
    //Show badge over app icon when non handled pushes are present. Default value: true
    badge: true,
    
    //Show message on locked screen. Default value: 1
    //Possible values (default 1):
    //-1 - secret - Do not reveal any part of the notification on a secure lockscreen.
    //0 - private - Show the notification on all lockscreens, but conceal sensitive or private information on secure lockscreens.
    //1 - public - Show the notification in its entirety on all lockscreens.
    visibility: 1
};

// Create the channel
window.FirebasePlugin.createChannel(options,
function(){
    console.log('Channel created: ' + options.id);
},
function(error){
   console.log('Create channel error: ' + error);
});
```

Example FCM v1 API notification message payload for invoking the above example channel:

```

{
 "notification":
 {
      "title":"Notification title",
      "body":"Notification body",
 },
 "android": {
     "notification": {
       "channel_id": "my_channel_id"
     }
   }
}

```

### setDefaultChannel
Android 8+ only.
Overrides the properties for the default channel.
The default channel is used if no other channel exists or is specified in the notification.
Any options not specified will not be overridden.
Should be called as soon as possible (on app start) so default notifications will work as expected. 
Calling on Android 7 or below or another platform will have no effect.

```
var options = {
  id: "my_default_channel",
  name: "My Default Name",
  sound: "ringtone",
  vibration: [500, 200, 500],
  light: true,
  lightColor: "0xFF0000FF",
  importance: 4,
  badge: false,
  visibility: -1
};

window.FirebasePlugin.setDefaultChannel(options,
function(){
    console.log('Channel created: ' + options.id);
},
function(error){
   console.log('Create channel error: ' + error);
});
```

#### Default Android Channel Properties
The default channel is initialised at app startup with the following default settings:

```
{
    id: "fcm_default_channel",
    name: "Default",
    sound: "default",
    vibration: true,
    light: true,
    lightColor: -1,
    importance: 3,
    badge: true,
    visibility: 1
}
```

##deleteChannel
Android 8+ only.
Removes a previously defined channel.
Calling on Android 7 or below or another platform will have no effect.

```
window.FirebasePlugin.deleteChannel("my_channel_id",
function(){
    console.log('Channel deleted');
},
function(error){
   console.log('Delete channel error: ' + error);
});

```

##listChannels 
Android 8+ only.
Gets a list of all channels.
Calling on Android 7 or below or another platform will have no effect.

```
window.FirebasePlugin.listChannels(
function(channels){
     if(typeof channels == "undefined")
          return;

     for(var i=0;i<channels.length;i++)
     {
          console.log("ID: " + channels[i].id + ", Name: " + channels[i].name);
     }
},
function(error){
   alert('List channels error: ' + error);
});

```
### startTrace

Start a trace.

```
window.FirebasePlugin.startTrace("test trace", success, error);
```

### incrementCounter

To count the performance-related events that occur in your app (such as cache hits or retries), add a line of code similar to the following whenever the event occurs, using a string other than retry to name that event if you are counting a different type of event:

```
window.FirebasePlugin.incrementCounter("test trace", "retry", success, error);
```

### stopTrace

Stop the trace

```
window.FirebasePlugin.stopTrace("test trace");
```

### setAnalyticsCollectionEnabled

Enable/disable analytics collection (useful for GDPR/privacy settings).

```
window.FirebasePlugin.setAnalyticsCollectionEnabled(true); // Enables analytics collection

window.FirebasePlugin.setAnalyticsCollectionEnabled(false); // Disables analytics collection
```

### sendCrash

Simulates (causes) a fatal native crash.

```javascript
window.FirebasePlugin.logMessage("about to send a crash for testing!");
window.FirebasePlugin.sendCrash();
```
