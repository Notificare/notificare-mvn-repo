Changelog
=========

1.6.1

Fixed IllegalArgumentException in adding empty list of geofences
Added setNotificationAccentColor
Upgraded V7AppCompat to 23.1

1.6.0

Suppress errors for unbound BeaconManager
Fixed lastRegistration date to refer to latest actual registration API call
Upgraded Gradle build tools 1.4.0, Google Play Services 8.1.0, V7AppCompat 23.0.1
Added Android M system permissions helper methods and checks
Replaced UrlImageViewer with Ion
Added PushService listener intent filters
Added InstanceID intent service
Added Registration intent service

1.5.6

Added check for external storage, show localizable Toast when failing
Add handleNotificationOpenedIntent

1.5.5

Add smarter refresh of geofences

1.5.4

Log SDK version at launch
Log comprehensive error messages on authentication failure
Log comprehensive error messages on max retries

1.5.3

Fix location update with unknown country

1.5.2

- Add option to enable JavaScript in WebViews
- Add reverse-geocoded country code to device location update
- Updated Gradle build tools 1.2.3, Google Play Services 7.5.0, V7AppCompat 22.2.1

1.5.1

- Reregister if last registration was more than a month ago

1.5.0

- Send oldDeviceID if changed
- Added system push / refresh application info
- Removed deprecated ActionBarActivity for AppCompatActivity
- Removed deprecated Apache HTTP client references
- Updated Gradle build tools 1.2.2, Google Play Services 7.3.0, V7AppCompat 22.1.1

1.4.3 2015-04-21

- Set previously registered deviceId at start
- Replace some RuntimeExceptions with warnings
- Check for applicationInfo before passing onReady intent
- Log spurious ready intents in BaseIntentReceiver

1.4.2 2015-04-14

- Fixed concurrency issue in Connectivity listener that could lead to crashes

1.4.1 2015-03-25

- Added lights settings to be read from incoming notifications
- Added default settings specific for Android (currently only lights)
- Added preference setting for enabled/disabled notifications, apps should check with isNotificationsEnabled() in onReady callback
- Fixed compatibility issue with latest V7AppCompat
- Updated to Gradle build tools 1.1.3, Android build tools 22.0.1, Android SDK 22, GooglePlayServices 7.0.0, V7AppCompat 22.0.0, AltBeacon 2.1.4

1.4.0 2015-02-05

- Let OS handle all URLs in WebView
- Update AndroidBeaconLibrary to 2.1 
- Added multiple beacon sessions, for overlapping regions
- Changed update intervals on geofences 
- Added customizable small icon, use setSmallIcon() to change it from default application icon
- Retry API requests
- Clear entered beacon regions
- Save registered device to shared preferences, so API calls are only done when needed, even after restart
- Save already entered beacons to shared preferences, so even after restart enters are not triggered
- Save already entered geofences to shared preferences, so even after restart enters are not triggered

1.3.2	2015-01-22

- Only make one fetchApplicationInfo request at once

1.3.1	2015-01-21

- Retry fetchApplicationInfo soon connectivity becomes available
- Move logOpenNotification to NotificationActivity
- Log open and influenced after handling action from notification center
- Removed UserSegment events

1.3.0	(2015-01-08)
- prevent compatibility issue with Android Wear
- add region and beacon session
- minor bug fixes

1.2.3	(2014-12-16)
- check for non-null BeaconClient before monitoring beacons
- make listener interfaces public in Billing

1.2.2	(2014-12-15)
- check for enabled GCM and Location service at launch, now needs to wait for onReady for all services
- added onReady listener interface in addition to onReady intent
- modular dependencies for Google Play Services

1.2.1	(2014-12-10)
- removed debug logging messages
- upgraded to new Google Play Services API

1.2.0	(2014-12-08)
- added beacon support
- added in-app billing
- added user preferences
- added inbox
 
