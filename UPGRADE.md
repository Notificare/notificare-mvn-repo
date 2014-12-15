Upgrade
=======

Upgrade to 1.2

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


