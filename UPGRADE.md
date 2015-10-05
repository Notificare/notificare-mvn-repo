Upgrade
=======

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



