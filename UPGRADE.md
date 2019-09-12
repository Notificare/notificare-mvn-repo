# Upgrade

## Upgrade to 2.1.0

### AndroidX

To use this version of the SDK, your project must use the AndroidX libraries. If this is already the case, building against this update will work without any changes.
If your project still uses the old Android Support Library, this would be a perfect time to upgrade. Detailed instructions can be found on the [Android Developer website](https://developer.android.com/jetpack/androidx/migrate)

### Background location updates

The notificare-location dependency will now add the `ACCESS_BACKGROUND_LOCATION` permission to your AndroidManifest. Upgraded phones will automatically get the permission for background locations if users allowed location updates.
For new installs, calling `Notificare.shared().requestLocationPermission` will ask for background location updates. Likewise, `Notificare.shared().hasLocationPermissionGranted` will only return true if the user allowed background location updates ('always')
If the user only allows foreground location updates, geofences will not work and locations will only be updated in background. See [Notificare Docs](https://docs.notifica.re/sdk/v2/android/implementation/location-services/) for more info and code examples.

### New Foreground Listener

The `OnNotificationReceivedListener` interface is superseded by the new `OnNotificareNotificationListener` interface. This interface has a `onNotificareNotification` method which receives both the Notification as well as the corresponding Inbox Item (if using inbox).
It also will pass along the `shouldPresent` boolean which will be set to true if the `presentation` option is selected when sending a notification from the Dashboard.

## Upgrade to 2.0.0

### Prerequisites

Your app **must** be ready for Notificare SDK 1.14 or 1.15. If not, please follow the upgrade steps below until you can build and run against (at least) 1.14

### Android SDK compatibility

Your app must be compiled with Android SDK 28, minimal Android SDK version for your app is 16 (i.e., Android JellyBean / 4.1), but for security it's a good idea to set your minimal SDK version to 19.

### New Gradle dependencies

The SDK is now split in 4 parts, for easy (transitive) dependency management. Replace existing entries in your `app/build.gradle` with the new entries:

```java

    // implementation 're.notifica:notificare-push-lib-android:1.15.0'      // <-- will be replaced by notificare-core
    // implementation 'org.altbeacon:android-beacon-library:2.14'           // <-- will be taken care of by the new notificare-beacon
    // implementation 'com.google.android.gms:play-services-vision:16.2.0'  // <-- will be taken care of by the new notificare-scannable

    implementation 're.notifica:notificare-core:2.0.0'
    implementation 're.notifica:notificare-location:2.0.0'    // <-- if you want to use location, geofences
    implementation 're.notifica:notificare-beacon:2.0.0'      // <-- if you want to use beacons
    implementation 're.notifica:notificare-scannable:2.0.0'   // <-- if you want to use scannable items and tags
```

### Remove unnecessary Manifest entries

The following entries will be automatically added by the SDK and can be *removed* :

```xml
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <uses-permission android:name="android.permission.NFC" />
    <uses-permission android:name="android.permission.CAMERA" />

    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <service
        android:name="re.notifica.push.fcm.PushService"
        android:exported="false"
        android:label="Notificare Push Service">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    <service
        android:name="re.notifica.push.fcm.InstanceIDService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT" />
        </intent-filter>
    </service>

    <meta-data android:name="com.google.android.gms.vision.DEPENDENCIES" android:value="barcode" />

    <uses-library android:name="org.apache.http.legacy" android:required="false" />
```

### Class and Namespace changes

`DefaultIntentReceiver`, `BaseApplication` and `BaseActivity` moved to the `re.notifica.app` package

The Activity Lifecycle events are now handled automatically by Notificare. If your activities call `setForeground()`, `setBackground()`, `logStartSession()` or `logEndSession()` in their `onResume` / `onPause`, these can be removed.

JavaScript is now always allowed in WebViews, so the setAllowJavascript() call is gone.

The Notificare UserPreferencesActivity is removed, your app needs to implement a settings fragment or activity itself.

### Intent Receiver changes

The Notificare SDK now automatically registers a device at launch, so you will always have a device token when `onReady` is called. On top of that, the new `onDeviceRegistered` method will be called each time a device is registered.

Of course, as always, you will still need to call `enableNotifications` if you want to receive Push Notifications. If you don't, the device will be registered for inbox messages only. Calling `disableNotifications` will set the device to non-push.

This also means you can call `enableLocationUpdates` and `enableBeacons` in the `onReady` method.

The `isNotificationsEnabled()` and `isLocationUpdatesEnabled()` checks will now return `false` if you didn't call them yet. Of course, in an existing install, the status of notifications / location updates will be honored.

The `onRegistrationFinished` and `onUnregistrationFinished` methods are removed from the intent receiver.

In short, your intent receiver should look something like this:

```java

    @Override
    public void onReady() {
        // Check if notifications are enabled, by default they are not.
        // Make sure to call enableNotifications() after on-boarding (or from your app settings view)
        if (Notificare.shared().isNotificationsEnabled()) {
            Notificare.shared().enableNotifications();
        }
        // Check if location updates are enabled, by default they are not.
        // Make sure to call enableLocationUpdates() after on-boarding (or from your app settings view)
        if (Notificare.shared().isLocationUpdatesEnabled()) {
            Notificare.shared().enableLocationUpdates();
        }


        // If you want to be sure tags are synced from the Notificare API
        Notificare.shared().fetchDeviceTags(new NotificareCallback<List<String>>() {

            @Override
            public void onError(NotificareError arg0) {

            }

            @Override
            public void onSuccess(List<String> arg0) {

            }

        });
    }

    @Override
    public void onDeviceRegistered(NotificareDevice device) {
        // At this point, a device is registered with Notificare API
    }
```

For registrations as a user nothing really changed, but as a reminder, the best way to do it would be to call

```java

    Notificare.shared().setUserId("123456789");
    Notificare.shared().setUserName("My Name");
    Notificare.shared().registerDevice(new NotificareCallback<String> ({
        // etc.
    }))
```

right after the user finished login in your Activity. Notificare SDK will remember the userID you set, so there is no need to do this anywhere in the intent receiver.

### Inbox is now LiveData

Instead of calling and requesting data every time from your inbox views, just hook up an observer to the inbox as LiveData, for example in your fragment `onCreateView` do something like this

```java
    LiveData<SortedSet<NotificareInboxItem>> temp = Notificare.shared().getInboxManager().getObservableItems();
    temp.observe(this, notificareInboxItems -> {
        Log.i(TAG, "inbox changed");
        inboxListAdapter.clear();
        if (notificareInboxItems != null) {
            inboxListAdapter.addAll(notificareInboxItems);
        }
    });
```

As with all LiveData observers, automatic handling of the lifecycle events of your UI will make sure any changes will end up in your observer.

Of course, your observer should be a bit smarter and use a RecyclerView with check for differences instead of just replacing the full list.


### NotificationActivity changes

The NotificationActivity will now open and close without animations and honor transparency styles from your AndroidManifest. A good way to let the (alert type) notifications appear transparently on top of your existing task stack would be:

```xml
    <activity
        android:name="re.notifica.ui.NotificationActivity"
        android:configChanges="keyboardHidden|orientation|screenSize"
        android:hardwareAccelerated="true"
        android:theme="@style/AppTheme.Translucent"
        android:parentActivityName="re.notifica.demo.MainActivity" />
    </activity>
```

and then define styles as

```xml
    <style name="AppTheme.Translucent" parent="AppTheme">
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowBackground">@android:color/transparent</item>
    </style>
```

The NotificationActivity now uses callbacks from the notification fragments, which means it is now easier for you to display notification fragments in your own Activities. For more details, take a look at the [customization docs](https://docs.notifica.re/sdk/v2/android/customizations).


## Upgrade from 1.14.x to 1.15.0

### Manifest update

Dependency on SDK level 28 and Google Play Services Maps makes the following entry necessary:

```xml
    <uses-library android:name="org.apache.http.legacy" android:required="false" />
```

## Upgrade from 1.13.x to 1.14.0

### Removed support for Legacy GCM mode

Google Play Services 15.0.0 removed the necessary GCM dependencies, so the Notificare SDK can no longer support Legacy GCM mode.
If you app uses it, please follow the upgrade instructions for 1.8.x to 1.9.0, where we introduced FCM. Classes and methods have been removed, so you should expect build errors if you didn't update your code yet.

In short, your AndroidManifest should now only need the following Service entries for Notificare:

```xml
        <service
            android:name="re.notifica.push.fcm.PushService"
            android:exported="false"
            android:label="Notificare Push Service">
            <intent-filter>

                <!-- Receives the actual messages. -->
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
        <service
            android:name="re.notifica.push.fcm.InstanceIDService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT" />
            </intent-filter>
        </service>
```

The entries for NotificationActivity, PassbookActivity and your IntentReceiver remain as they are.


## Upgrade from 1.10.x to 1.11.0

#### Manifest update

Remove unnecessary entries:

```xml

    <!-- this service is no longer needed -->
    <service
        android:name="re.notifica.push.fcm.RegistrationService"
        android:exported="false" />
        
```

```xml

    <!-- this service is automatically added by Gradle dependency -->
    <service
        android:name="re.notifica.push.gcm.TaskService"
        android:exported="true"
        android:permission="com.google.android.gms.permission.BIND_NETWORK_TASK_SERVICE">
        <intent-filter>
            <action android:name="com.google.android.gms.gcm.ACTION_TASK_READY" />
        </intent-filter>
    </service>
    
```

## Upgrade from 1.9.x to 1.10.0

### Add notification channel

Starting with Android O, you need at least one channel for notifications to be shown to users. Without a channel, notifications will _never_ appear in the notification manager.
Luckily, the SDK let's you easily add one. In your Application class <code>onCreate</code> add the following:

```java
    Notificare.shared().launch(this); <-- this should already be there
    Notificare.shared().createDefaultChannel();
```

That's it! Now, every notification will appear in the default channel "Push Notifications". 
You can change the name and the description of that channel in <code>strings.xml</code> :

```xml
<string name="notificare_default_channel_name">Push Notifications</string>
<string name="notificare_default_channel_description">This channel shows push notifications</string>
```

If you want a channel that your app created to be the default channel, call

```java
Notificare.shared().setDefaultChannel(myChannel.getId());
```

You can also dedicate a specific channel to passbook relevance notifications and updates

```java
Notificare.shared().setPassbookChannel(myPassbookChannel.getId());
```


### CAVEAT
Please be aware that channels, once created, can never be removed from the app's settings anymore, unless the user reinstalls the app or clears the app's data. In other words: plan your channels before you create them.


## Upgrade from 1.8.x to 1.9.0

### Upgrade to the new Firebase Cloud Messaging

By default, the SDK will assume your app is a Firebase app. This is the recommended setup and has the added benefit of being able to make use of the latest features in Google's API features. However, if you want to stick to the legacy GCM implementation, you can do so by following instructions in the next chapter. Be aware this comes with some caveats.

#### Import your project in Firebase

Log in to the [Firebase Console](https://console.firebase.google.com/) and click *Import Google Project*

After your project is imported, please go to *Project Settings > Cloud Messaging* and verify that the Server key is still the same as the one that is in the Notificare Dashboard in *Settings > Services > GCM*.
If not, please replace it with the new value from the Firebase Console.

#### Connect your app to Firebase

In Android Studio, go to *Tools > Firebase* and select *Set up Firebase Cloud Messaging*.
Connect your app to Firebase and select the project you just imported in the step above.
Add the necessary FCM changes to your app by clicking *Add FCM to your app*. The rest of the steps are handled by the Notificare SDK, so FCM Messaging is set up correctly now.

#### Upgrade Notificare SDK

Change the version of Notificare SDK in your Gradle build dependencies to 1.9.0.
Your Gradle build file should now contain something like this

```gradle
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 're.notifica:notificare-push-lib-android:1.9.0'
    compile 'com.android.support:appcompat-v7:25.0.0'
    compile 'com.google.firebase:firebase-messaging:9.8.0'
    testCompile 'junit:junit:4.12'
}


apply plugin: 'com.google.gms.google-services'

```

You can now remove the `gcmSenderId` property from `assets/notificare.properties`, since this is now provided by google-services.json.

#### Manifest update

The new FCM implementation in the 1.9.0 SDK needs the following changes to AndroidManifest.xml:

Remove unnecessary permissions

```xml

    <uses-permission android:name="android.permission.WAKE_LOCK" />

    <permission android:name="yourapp.namespace.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="yourapp.namespace.permission.C2D_MESSAGE" />
    
```

The following permissions are still needed

```xml

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" android:maxSdkVersion="22"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" android:maxSdkVersion="22"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

    <!-- This app has permission to register and receive message -->
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <!-- Optional permissions when using in-app billing -->
    <!--<uses-permission android:name="com.android.vending.BILLING" />-->
    
```

Remove the existing GCM receivers and services

```xml

    <receiver
        android:name="re.notifica.push.gcm.PushReceiver"
        android:permission="com.google.android.c2dm.permission.SEND" >
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <!-- Change this to be your package name -->
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    <service
        android:name="re.notifica.push.gcm.PushService"
        android:exported="false"
        android:label="Notificare Push Service">
        <intent-filter>

            <!-- Receives the actual messages. -->
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        </intent-filter>
    </service>
    <service
        android:name="re.notifica.push.gcm.InstanceIDService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID" />
        </intent-filter>
    </service>
    <service
        android:name="re.notifica.push.gcm.RegistrationService"
        android:exported="false" />

```

Replace them with the new FCM entries:

```xml
    <service
        android:name="re.notifica.push.fcm.PushService"
        android:exported="false"
        android:label="Notificare Push Service">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>

    <service
        android:name="re.notifica.push.fcm.InstanceIDService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT" />
        </intent-filter>
    </service>
    <service
        android:name="re.notifica.push.fcm.RegistrationService"
        android:exported="false" />

```


Add a new service for time-sensitive updates such as passbook relevance

```xml
    <service android:name="re.notifica.push.gcm.TaskService"
        android:permission="com.google.android.gms.permission.BIND_NETWORK_TASK_SERVICE"
        android:exported="true">
        <intent-filter>
            <action android:name="com.google.android.gms.gcm.ACTION_TASK_READY"/>
        </intent-filter>
    </service>

```

Add a new activity to handle Passbook compatible passes sent to this application.
The configuration below uses an intent filter to be able open the activity from any link to a pass sent from Notificare to this app.
You might of course want to add your main activity as a parent activity, so passes are opened in a task stack in your app.

```xml
    <activity
        android:name="re.notifica.ui.PassbookActivity"
        android:configChanges="keyboardHidden|orientation|screenSize"
        android:hardwareAccelerated="true">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:host="push.notifica.re"
                android:pathPrefix="/pass/forapplication/{notificareAppId}"
                android:scheme="https" />
        </intent-filter>
    </activity>

```

Replace `{notificareAppId}` by your application's ID in Notificare. It can be copied from the URL you see in the Notificare Dashboard, `https://dashboard.notifica.re/#/app/{notificareAppId}`

### Upgrade, but keep legacy Google Cloud Messaging

By default, the SDK will assume your app is a Firebase app. This is the recommended setup and has the added benefit of being able to make use of the latest features in Google's API features. However, if you want to stick to the legacy GCM implementation, you can do so by following instructions below.
Be careful when using google-services.json, as this may inadvertently add dependencies on Firebase as well, in which case you should follow the upgrade steps in the chapter above.

#### Manifest update

Add a new service for time-sensitive updates such as passbook relevance

```xml
    <service android:name="re.notifica.push.gcm.TaskService"
        android:permission="com.google.android.gms.permission.BIND_NETWORK_TASK_SERVICE"
        android:exported="true">
        <intent-filter>
            <action android:name="com.google.android.gms.gcm.ACTION_TASK_READY"/>
        </intent-filter>
    </service>

```

Add a new activity to handle Passbook compatible passes sent to this application.
If you don't add the `PassbookActivity` in your app, it will revert to opening passes in an external application.
This means you can not use the Pass Wallet functionality in your app and not be able to push updates to your passes.

The configuration below uses an intent filter to be able open the activity from any link to a pass sent from Notificare to this app.
You might of course want to add your main activity as a parent activity, so passes are opened in a task stack in your app.

```xml
    <activity
        android:name="re.notifica.ui.PassbookActivity"
        android:configChanges="keyboardHidden|orientation|screenSize"
        android:hardwareAccelerated="true">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:host="push.notifica.re"
                android:pathPrefix="/pass/forapplication/{notificareAppId}"
                android:scheme="https" />
        </intent-filter>
    </activity>

```

Replace `{notificareAppId}` by your application's ID in Notificare. It can be copied from the URL you see in the Notificare Dashboard, `https://dashboard.notifica.re/#/app/{notificareAppId}`


#### Update Application to use legacy GCM

Add the following line to your `Application` class `onCreate` method

```java
	@Override
	public void onCreate() {
		super.onCreate();

		Notificare.shared().setUseLegacyGCM(); // <-- Add this line

		Notificare.shared().launch(this);
		Notificare.shared().setIntentReceiver(AppReceiver.class);
		// ... rest of method
	}

```


## Upgrade from 1.5.x to 1.6

### Manifest updates

The new GCM implementation in the 1.6.0 SDK needs the following changes to AndroidManifest.xml:

Remove unnecessary permissions

```xml

	<uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES"/>

```

The following permissions are still needed, storage permissions are not needed anymore if the device runs SDK 23, so they can have a maxSdkVersion attribute

```xml

    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" android:maxSdkVersion="22"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" android:maxSdkVersion="22"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

    <!-- This app has permission to register and receive message -->
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <permission android:name="yourapp.namespace.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="yourapp.namespace.permission.C2D_MESSAGE" />

    <!--<uses-permission android:name="com.android.vending.BILLING" />-->

```

Add an intent filter to the PushService

```xml

    <service android:name="re.notifica.push.gcm.PushService"
        android:label="Notificare Push Service">
        <intent-filter>
            <!-- Receives the actual messages. -->
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        </intent-filter>
    </service>

```

Add 2 new services

```xml

	<service android:name="re.notifica.push.gcm.InstanceIDService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID" />
        </intent-filter>
    </service>

    <service android:name="re.notifica.push.gcm.RegistrationService"
        android:exported="false" />

```

### Android M Permissions

Android applications need to check for permissions to use location updates on Android M (SDK 23).
These permissions need to be requested from an activity in your app, after which you can safely enable location updates. To make sure the location updates aren't started before the Notificare SDK is ready, you should wait until it is safe to do so, by listening to the OnNotificareReady event.

Let your activity implement a OnNotificareReadyListener interface:

```java

    public class MainActivity extends ActionBarBaseActivity implements Notificare.OnNotificareReadyListener {


	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	
	        //...        
	        
	        Notificare.shared().addNotificareReadyListener(this);
	    }
	
	    @Override
	    protected void onDestroy() {
	        super.onDestroy();
	        
	        //...
	        
	        Notificare.shared().removeNotificareReadyListener(this);
	    }
	
	    @Override
	    public void onNotificareReady(NotificareApplicationInfo info) {
	    
	        // Request permission for location updates
	    
	    }

	}
```

In the onNotificareReady method, check for permissions and request them if necessary. Following Android Design guidelines, you should give users a chance to see why you need this permission. The Notificare SDK provides 4 utility methods to help you with these, the following example uses all of them:

```java

	@Override
    public void onNotificareReady(NotificareApplicationInfo info) {
        if (!Notificare.shared().hasLocationPermissionGranted()) {
            Log.i(TAG, "permission not granted");
            if (Notificare.shared().didRequestLocationPermission()) {
                if (Notificare.shared().shouldShowRequestPermissionRationale(this)) {
                    // Here we should show a dialog explaining location updates
                    new AlertDialog.Builder().setMessage(R.string.alert_location_permission_rationale)
                        .setTitle(R.string.app_name)
                        .setCancelable(true)
                        .setPositiveButton(R.string.button_location_permission_rationale_ok, new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int id) {
                                Notificare.shared().requestLocationPermission(MainActivity.this, LOCATION_PERMISSION_REQUEST_CODE);
                            }
                        })
                        .create()
                    	.show();
                }
            } else {
                Notificare.shared().requestLocationPermission(this, LOCATION_PERMISSION_REQUEST_CODE);
            }
        }
    }
    
    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        switch (requestCode) {
            case LOCATION_PERMISSION_REQUEST_CODE: {
                if (Notificare.shared().checkRequestLocationPermissionResult(permissions, grantResults)) {
                    Log.i(TAG, "permission granted");
                    Notificare.shared().enableLocationUpdates();
                    Notificare.shared().enableBeacons();
                }
                return;
            }
        }
    }

```


## Upgrade from 1.1 to 1.2

Apps now need to implement the onReady method in the DefaultIntentReceiver subclass before any calls can be made to any of the services. 
Previously, this was only needed for extra services. This means that where previously one could use:

```java

	public void onCreate() {
		super.onCreate();
		// Launch Notificare system
	    Notificare.shared().launch(this);
	    Notificare.shared().setIntentReceiver(DefaultIntentReceiver.class);
	    // Enable this device for push notifications
	    Notificare.shared().enableNotifications();
	    // Enable location updates
	    if (Notificare.shared().isLocationUpdatesEnabled()) {
	    	Notificare.shared().enableLocationUpdates();
	    }
	}
```

in the Application subclass, now this has to move to the intent receiver, like so:

```java

	public void onReady() {
	    // Enable this device for push notifications
	    Notificare.shared().enableNotifications();
	    // Enable location updates
	    if (Notificare.shared().isLocationUpdatesEnabled()) {
	    	Notificare.shared().enableLocationUpdates();
	    }
	}

```

By default, the DefaultIntentReceiver enables notifications and locationupdates in onReady, the BaseApplication doesn't do this anymore. 
If you've subclassed any of these, please check and change your code accordingly.


