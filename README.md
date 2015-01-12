## Introduction


SENSIYA for Android is a revolutionary contextual awareness SDK that allows you to personalize your application’s content and UI to match each unique user’s needs, patterns and real life behavior. 
Using SENSIYA SDK you can create next-generation experiences, more timely and contextual than before.


## Integrating the SDK

Follow these two simple steps to integrate the SDK in your Android application:

#### Add the SENSIYA SDK to your project

1. Copy the SensiyaSDK.jar file you have downloaded from the website to your project’s libs directory (if libs directory does not exist, create one).
2. Add a reference to the above JAR file in your project.

- Update your Android Manifest file

#### Add Permissions: 
Add the following permissions to the manifest section. In addition to the mandatory permissions, you can choose to add on the permissions required for the module you wish to implement, as specified below. Adding more permission will mean higher accuracy and higher confidence level per parameter.

```sh
<!-- Mandatory basic permissions -->
<!-- used to get demographics data and updated formula for activity recognition -->
<uses-permission android:name="android.permission.INTERNET" />  
<!-- used to restart the SDK activity upon device reboot -->
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" /> 
<!-- used for internal logging, optional -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
<!-- used in rare cases to optimize the data you receive, optional -->
<uses-permission android:name="android.permission.WAKE_LOCK" /> 

<!-- Demographics permissions permissions -->
<!-- Non mandatory permissions, but each permission increases the accuracy of the demographics data -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PROFILE" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.READ_CALL_LOG" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="com.android.browser.permission.READ_HISTORY_BOOKMARKS" />
```
```sh
<!-- Geo fencing and context awareness permissions →
<!-- Used to get non accurate location -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> 
<!-- Used to get accurate location to improve recognition -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"  />
<!-- Used to get network state -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!-- Used to get non accurate location -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```
```sh
<!-- Activity Recognition permissions -->
<!-- Used to get non accurate location -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> 
<!-- Used to get accurate location to improve recognition -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"  />
``` 

#### Declare the SDK service
```sh
<service android:name="com.sensiya.brainssdk.BrainsService"/>
```

#### Declare the SDK receiver
```sh
<receiver android:name="com.sensiya.brainssdk.BrainsBroadcastReceiver">
<intent-filter>
<action android:name="android.intent.action.BOOT_COMPLETED"/>
<action android:name="android.net.wifi.STATE_CHANGE"/>
<action android:name="android.location.PROVIDERS_CHANGED"/>
<action android:name="com.brainssdk.action.BRAINS"/>
</intent-filter>
</receiver>
``` 

#### Declare your own receiver with the events you would like to receive from the SDK:

```sh 
<receiver android:name=".YourBroadcastReceiverClassNameHere">
<intent-filter>
<action android:name="brainssdk.intent.action.LEAVING_HOME"/>
<action android:name="brainssdk.intent.action.ENTERING_HOME"/>
<action android:name="brainssdk.intent.action.LEAVING_WORK"/>    	
<action android:name="brainssdk.intent.action.ENTERING_WORK"/>    	
<action android:name="brainssdk.intent.action.HEADING_TO_WORK"/>      
<action android:name="brainssdk.intent.action.HEADING_HOME"/>    	
<action android:name="brainssdk.intent.action.WENT_TO_SLEEP"/>    	
<action android:name="brainssdk.intent.action.WOKE_UP"/>           
<action android:name="brainssdk.intent.action.GEO_FENCE"/>    	
<action android:name="brainssdk.intent.action.USER_ACTIVITY_CHANGED"/>    	
<action android:name="brainssdk.intent.action.USER_BROWSING"/>  
 </intent-filter>
</receiver>
``` 

#### Add the unique application key

Find and add the unique application key that was generated for your app in the web console:
```sh 
<meta-data android:name ="SENSIYA_APP_KEY" android:value="PasteYourApplicationKeyHere"/>
``` 
## Running the SDK

The SENSIYA SDK is started by calling its start API in the onCreate method of your custom Application, main Activity or Service, depends on the structure of your application. Once you get onConnected response to the callback your SENSIYA SDK is ready.
```sh 
BrainsAPI.start(context, new BrainsAPICallback(){
      @Override
      public void onConnected() {
        Log.d(TAG, "SDK is connected. Start using it from here.");
  	}
 
      @Override
      public void onError(Error error) {
        Log.e(TAG, String.format("Error starting the SDK. code: %d message: %s",
                      error.getCode(), error.getMessage());
  	}
 
      @Override
      public void onDisconnected() {
        Log.d(TAG, "SDK is disconnected");
  	}
 
      @Override
      public void onUserDataReady(UserData userdata) {
        Log.d(TAG, "User data ready");
  	}
 
});

``` 

#### Getting asynchronous updates

To get asynchronous notifications from the SDK you need to create a broadcast receiver and subscribe for actions you would like to be notified about.

For example, subscribe for the “brainssdk.intent.action.LEAVING_WORK” intent action to get notified every time the user is leaving his work place.

 
#### Calling synchronous API

You can also get a specific user data or SDK information using the direct API. For example:

- Get the SDK version:
```sh 
String version = BrainsAPI.getVersion();
``` 
- Get user object and its gender:
```sh 
UserData user = BrainsAPI.User.getUserData();
String gender = UserData.getGenderString(user.getGender());
float genderConfidence = user.getGenderConfidence();
``` 
- Get user’s last known activity:
```sh
int activity = BrainsAPI.ActivityRecognition.getLastKnownActivity(); 
//returns one of the BrainsIntent activity types
```

#### GeoFencing

Geofencing is a location-based service that sends messages when users.
Sensiya’s SDK makes it easy to define and set custom geo fences for your users. You can define geo fences using the following APIs:

- Define simple geo fence with custom radius:
```sh
BrainsAPI.LocationServices.GeoFencing.addGeofence(geoFenceId, latitude, longitude, 
radius);
``` 
- Define simple geo fence with custom radius and expiration:
```sh
BrainsAPI.LocationServices.GeoFencing.addGeofence(geoFenceId, latitude, longitude,
radius, expiration);
``` 
- Define simple geo fence with custom radius, expiration and loitering time to indicate if the user is dwelling :
```sh
BrainsAPI.LocationServices.GeoFencing.addGeofence(geoFenceId, latitude, longitude,
radius, loitering);
``` 

Upon receiving a geo event you will be able to identify the triggering geofence and transition type from the arrived intent extra data.


#### Analytics

Sensiya’s SDK Analytics gives you the ability to track the behavior of users in your product in a simple way:

- Track your session length:
You can indicate about session start/end in your onStart/onStop activity methods accordingly. (Make sure that the BrainsAPI is connected before you making the call):

    - Start session indication:
```sh
BrainsAPI.Analytics.startSession(context);
``` 
    - Log an event with extra data:
```sh
BrainsAPI.Analytics.endSession(context);
``` 


- Log your desired event:

    - Log an event without parameters:
```sh
BrainsAPI.Analytics.logEvent(eventName);
``` 
    - Log an event with extra data:
```sh
BrainsAPI.Analytics.logEvent(eventName, eventParams);
``` 


#### Proguard

If you are using proguard for your application obfuscation, please add these following lines to your proguard file for the Sensiya SDK to work properly:

-keep class com.sensiya.brainssdk.** { *; }

#### Privacy

As in every experience you offer your users or analytics service you’re using, we strongly recommend you look into your users privacy and interfaces with the various guidelines. 

We also ask that you review and respect the Sensiya Terms Of Service and Privacy Policy as they appear and may change from time to time on the Sensiya.com website.

#### Questions? Contact us.
We’re available 24/7. Email support@sensiya.com for a prompt reply.




















 
It’s about to get personal

