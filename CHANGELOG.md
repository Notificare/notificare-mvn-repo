Changelog
=========

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
 
