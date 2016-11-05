# Upgrade

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


