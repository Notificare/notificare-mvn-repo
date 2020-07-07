Changelog
=========
# notificare-common 2.4.0-beta-1
- Split core, location and scannable for Google Play Services and Huawei Mobile Services
- Added location accuracy to device model 
- Added helper methods to deal with new background location permissions in Android 11
- Added markAll 
- Added configurable settings for toasts and progress bar in notification activity
- Updated Android billing to v3

# notificare-core 2.3.0

- Instantiate beacon client at launch, configure after application info is loaded
- Added inbox expiration and visibility
- Maximise screen intensity when viewing passbook 
- Updated deprecated oauth2 library dependencies
- Updated to AndroidX TaskWorker
- Depend on Glide instead of Picasso for image manipulation

# notificare-core 2.2.3

- Add fix for video playing issues in background

# notificare-support-lib 2.0.0

- Migrated to AndroidX

# notificare-core 2.2.2

- Updated dependencies

# notificare-core 2.2.1

- Hide action menu on URL and WebView notifications when action links present in HTML
- Allow relative links in WebView
- Fix incorrect URL handling in Android 7+
- No longer exclude deeplink intents from recents 

# notificare-core 2.2.0

- Refactored beacon client to interface with separate implementation in notificare-beacon
- Request FCM token at launch so we don't miss newToken intents

# notificare-core 2.1.2

- Check for empty notification tags

# notificare-core 2.1.1

- Ignore equality check on inbox items

# notificare-core  2.1.0

- Update to AndroidX
- Update to Android Q
- Added background location permission
- Added new listener interface for foreground notifications
- Added grouped notifications from remote message payload
- Pass through presentation property of remote message to foreground notification listener

# notificare-core 2.0.9

- Ignore equality check on inboxitems

# notificare-core 2.0.8

- Ignore request portrait orientation on Android O

# notificare-core 2.0.7

- Ignore invalid location coordinates
- Allow null value for RemoteMessage messageId

# notificare-core 2.0.6

- Updated dependencies
- Correctly restore passbook type from DataStore when loading wallet
- Added passbook legacy mode flag
- Added passbook webview

# notificare-core 2.0.5

- Fix marking local inbox item as read when passbook is opened from inbox

# notificare-core 2.0.4

- Fix marking local inbox item as read when deeplink is opened from inbox

# notificare-core 2.0.3

- Add socket stats tag to requests
- Prevent inboxmanager from refreshing too early
- Prevent device being registered before FCM token is retrieved
- Update Firebase Core and AltBeacon dependency

# notificare-core 2.0.2

- Fix loading of userId and userName on launch
- Updated dependency Firebase Messaging to 17.3.4

# notificare-core 2.0.1

- Fix incorrect parsing of values in title, subtitle, attachment and extra when constructing an inbox item from JSON
- Updated dependency GooglePlayServices Vision to 17.0.2

# notificare-core 2.0.0

- Split AAR into notificare-core, notificare-location, notificare-beacon and notificare-scannable for easy dependency management
- Added necessary AndroidManifest entries to the AAR, only Activities and IntentReceiver are needed
- Moved BaseIntentReceiver to re.notifica.app
- Moved DefaultIntentReceiver to re.notifica.app
- Moved TaskService to re.notifica.service
- Moved BaseApplication to re.notifica.app
- Moved BaseActivity to re.notifica.app, lifecycle handling is done automatically, no need to setForeground anymore in the onResume etc.
- InboxManager getItems replaced by LiveData getObservableItems
- InboxManager getUnreadCount replaced by LiveData getObservableUnreadCount
- Removed onRegistrationFinished -> onDeviceRegistered
- Removed onUnregistrationFinished
- Checking isNotificationsEnabled defaults to false, call enableNotifications at least once, will still honor SharedPreferences stored
- Checking isLocationsEnabled defaults to false, call enableLocationUpdates at least once, will still honor SharedPreferences stored
- Now onReady calls after device is registered, no need to wait for onDeviceRegistered
- Registering device does not automatically sync device tags, needs to be explicitly called, e.g. in onDeviceRegistered
- Added addDeviceTag and removeDeviceTags methods
- New method onDeviceRegistered called with every attempt, even if no change, now is passed the NotificareDevice
- NotificareInboxItem now has message, title, subtitle, attachment, extra
- SystemIntentReceiver added to default manifest to handle timezone and locale changes even if app is closed
- Only unknown system notifications are reported to the intent receiver
- In WebViews, JavaScript / DomStorage always allowed, cache cleared on loading web content
- In WebView, actions are handled also from URL query parameters
- Added bluetoothEnabled flag
- Added course, speed and altitude to device
- Added verticalAccuracy to region session locations
- Use actual sent time as time in inbox and notifications
- Add locale region to device registration
- Added NotificationFragmentFactory to get a fragment to insert in custom notification activity
- Added callback interface for custom notification activities that add the Notification fragment themselves
- Remove activity animations from NotificationActivity

# notificare-push-lib=android  1.15.0

- Updated GooglePlayServices to 16.0.0
- Updated Firebase Messaging to 17.3.0
- Updated AltBeacon to 2.15

# notificare-support-lib-android 1.5.0

- Updated Support Libraries to 28.0.0

# notificare-push-lib-android 1.14.2
- Updated GooglePlayServices to 15.0.1 / 15.0.2
- Updated Firebase Core to 16.0.0
- Updated Firebase Messaging to 17.0.0
- Updated AltBeacon to 2.14

# notificare-push-lib-android 1.14.1

- Perform token unregistration off the main thread

# notificare-push-lib-android 1.14.0

-Updated GooglePlayServices to 15.0.0
- Removed Legacy GCM mode

# notificare-support-lib-android 1.4.3

- Updated Support Libraries to 27.1.1

# notificare-push-lib-android 1.13.5

- Updated GooglePlayServices to 12.0.1

# notificare-push-lib-android 1.13.4

- Fixed a bug where unknown passes would crash passbook viewer
- Updated GooglePlayServices to 12.0.0

# notificare-support-lib-android 1.4.2

- Updated Support Libraries to 27.1.0

# notificare-push-lib-android 1.13.3

- Check for null IntentReceiver in InstanceIDService
- Updated Support Libraries to 27.1.0

# notificare-push-lib-android 1.13.2

- Enable local storage in webviews if javascript is allowed

# notificare-push-lib-android 1.13.1

- Added logCustomEvent with callback

# notificare-push-lib-android 1.13.0

- Improve retry of API calls
- Add partial notification to inbox before full content is loaded
- Post partial notification to notification manager if content fetch fails
- Clear last known location when disabling location updates

# notificare-push-lib-android 1.12.2

- Updated GooglePlayServices to 11.8.0

# notificare-push-lib-android 1.12.1

- Updated GooglePlayServices to 11.6.2
- Updated Support Libraries to 27.0.2

# notificare-support-lib-android 1.4.1

Updated Support Libraries to 27.0.2

# notificare-push-lib-android 1.12.0

- Updated GooglePlayServices to 11.6.0
- Updated Support Libraries to 27.0.1

# notificare-support-lib-android 1.4.0

- Updated Support Libraries to 27.0.1

# notificare-push-lib-android 1.11.0

- Added extra to asset group

# notificare-push-lib-android 1.10.1

- Updated GooglePlayServices to 11.4.2
- Updated Support Libraries to 26.1.0

# notificare-support-lib-android 1.3.2

- Updated GooglePlayServices to 11.4.2
- Updated Support Libraries to 26.1.0

# notificare-support-lib-android 1.3.1

- Fix dimissal of InternetConnectionActivty

# notificare-push-lib-android 1.10.0

- Support for Android Oreo
- Added polygon support for geofences
- Added scannable activity

# notificare-push-lib-android 1.9.11

- Ask for camera permission in actions when needed
- Fix incorrect handling of options menu in notifications
- Updated GooglePlayServices to 11.0.4
- Changed add to wallet icon to text

# notificare-push-lib-android 1.9.10

- Updated GooglePlayServices to 11.0.1
- Added FileProvider for camera actions
- Post AssetLoader async results on main thread

# notificare-push-lib-android 1.9.9

- Updated GooglePlayServices to 11.0.0
- Updated Support Libraries to 25.4.0
- Switch to Picasso for image loading
- Add ProGuard rules

# notificare-push-lib-android 1.9.8

Log correct region id as fence in beacon sessions

# notificare-push-lib-android 1.9.7

Check if progress indicator, dialog and activity still exist when returning from async calls inside notification fragment
Catch missing network permissions when doing requests
Catch missing network permissions when checking network connectivity

# notificare-support-lib-android 1.2.2

Catch missing network permissions when checking network connectivity

# notificare-push-lib-android 1.9.6

Add app recommendation notification type
Add close window query parameter check on URL click
Allow to set or add optional headers on request
Allow to provide optional headers when doing a cloud request

# notificare-push-lib-android 1.9.5

Update to Support Libraries 25.3.1
Update to GooglePlayServices 10.2.1
Add registerUser / unregisterUser
Call these methods after login/logout

# notificare-support-lib-android 1.2.1

Update to Support Libraries 25.3.1

# 1.9.4

Update to Support Libraries 25.3.0
Implicitly set userId to null when registering anonymously
Allow mailto: and other URLs in Webview notifications
Check for deviceId when fetching user data

# 1.9.3

Update to GooglePlayServices 10.2.0, Firebase 10.2.0, Support Libraries 25.1.1
Change minimum API level to 14
Check for NULL values in JSON parsing

# 1.9.2

Check for notificationsEnabled when handling GooglePlayServices error resolution
Update to GooglePlayServices 10.0.1, Firebase 10.0.1, Support Libraries 25.1.0

# 1.9.1

Check for relevant date before ending pass location relevance
Check if lib is set up before handling Firebase intents

# 1.9.0

Added Notificare Cloud API requests
Added FCM support
Added userData
Added title to notifications
Added attachments to notifications
Added reply to notification actions in NotificationManager
Added pass updates by push
Added pass updated notifications
Added pass relevance for date
Allow pass relevance to be onGoing notifications
Add specific alternative text to passbook
Add check for allowedUI

# 1.8.7

Use resource for default relevance text
Remove pass from notification manager when not relevant anymore
Add removePass method
Check pass voided or expired
Honor minimum value of maxDistance and default radius
Add inboxConfig to applicationInfo

## 1.8.6

Fix billing manager setup exception handling
Fix device tags clearing on DnD loading
Updated Gradle wrapper and tools
Updated support dependencies

## 1.8.5

Fix DB upgrade issue from pre-V5 to V7

## 1.8.4

Build against SDK 24
Fixed SQLite table config for passes
Fix service enabled check
Updated InAppBilling to use latest version
Updated Google Play Services to 9.4.0

## 1.8.3

Added support for relevance notifications, based on locations and beacons
Improved pass' parsing
Added default large and small radius for relevant passes
Updated Google Play Services to 9.2.1
Updated Support libs to 24.1.1

## 1.8.2

Fix handling of NotificationOpened intents for Passbook
Correctly log influenced open events
Correctly handle type None and unknown types
Add DND methods, check for empty message
Add TimeOfDay and TimeOfDayRange models
Fix external passbook handling
Updated Support Libraries to 24.0.0
Updated GooglePlayServices to 9.2.0

## 1.8.1

Added passbook storage
Added passbook UI, log warning when PassbookActivity not declared in manifest
Added openInboxItem
Added fetchInboxItem that will lazy load an inbox item's content
Fix timezone in parsing of JSON ISO8601 dates
Added reload (with optional clear of local inbox) for inbox manager
Added fetch inbox from remote
Added getUrl for file assets
Allow non-file assets
Ignore Notifications that could not be fetched
Updated Support Libraries to 23.4.0
Updated Google Play Services to 9.0.2

## 1.8.0

Added URLScheme notification type
Added None notification type
Added Video notification content types
Added inbox-only messages
Added assets
Upgraded Support Libraries to 23.3.0

## 1.7.4

Added missing unread count decrement when adding a duplicate notification that was unread
Added OnNotificationReceived listener interface
Updated Support Libraries to 23.2.1

## 1.7.3

Upgraded Support Libraries to 23.2.0, upgraded Ion to 2.1.7

## 1.7.2

Fix duplicate unread count updates when updating inbox items (T476)
Disable caches for POST and PUT API calls

## 1.7.1

Fix URL webview, added bounds check
Mark inboxItems as read when opening from NotificationManager

## 1.7.0

Added click url events in webviews
Refactored db class into its own package
Added inboxItemId to intents
Added inbox sync methods, use inboxItemId when provided
Added configurable settings for geofenceInitialTrigger and geofenceResponsiveness
Force beacon exit upon region exit
Changed default background beacon monitoring interval
Force location update and fence loading when app is started
Added configurable settings for locationUpdatesInterval, locationUpdatesFastestInterval and locationUpdatesSmallestDisplacement

## 1.6.6

Added null check when adding location to region session, in edge cases this would lead to a NPE

1.6.5

Keep monitoring entered beacons when refreshing geofences so they can still trigger an exit
Upgraded GooglePlayServices

1.6.4

Added setApplicationKey / setApplicationSecret

1.6.3

Upgraded GooglePlayServices and V7AppCompat

1.5.9

Upgraded GooglePlayServices and V7AppCompat

1.6.2

Added localized labels for action buttons
Added clear inbox
Fixed issue where app could crash at launch from a malformed GCM intent

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
 
