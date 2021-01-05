Please see our <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/RELEASE_NOTES.md">Release Notes</a> to see detailed version history.

For Unity-specific instructions, please visit https://github.com/tenjin/tenjin-unity-sdk.

For any issues or support, please contact: support@tenjin.com

Tenjin Android SDK (v1.9.3) (132KB) - Google Play and Amazon Store support
=========

The native Android SDK for Tenjin. Integrate this into your Android app or game to get access to the functionality offered at https://www.tenjin.com/.

**Note: We recommend using the latest version of <a href="https://developer.android.com/studio/index.html">Android Studio</a> when integrating our SDK.**

Manifest requirements:
----

- Include `INTERNET` permissions within the manifest tags
- Include Google Play Services within the application tags

```xml
<manifest>
  ...
  <uses-permission android:name="android.permission.INTERNET"></uses-permission>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission> <!-- Required to get network connectivity (i.e. wifi vs. mobile) -->
  ...
</manifest>
```

SDK Integration:
---------------------------------

### Android Studio Integration
1. Download the latest Android SDK from <a href="https://github.com/tenjin/tenjin-android-sdk/releases">here.</a>
2. Add the tenjin.jar into your Android Studio project by selecting New > Module.
3. In the New Module dialog, select the Import .JAR or .AAR Package option and click on Next.
![AndroidStudio](https://tenjin-instructions.s3.amazonaws.com/android_studio_import.png "studio")
4. Select the `tenjin.jar` or `tenjin.aar`
 file click on Finish.
5. If you haven’t already installed the <a href="https://developers.google.com/android/guides/setup">Google Play Services</a>, add it to our build.gradle file.  Starting with Tenjin Android SDK v1.8.3, you will need to add <a href="https://developer.android.com/google/play/installreferrer/library.html">Google's Install Referrer Library</a>.

```java
dependencies {
  implementation 'com.google.android.gms:play-services-analytics:17.0.0'
  implementation 'com.android.installreferrer:installreferrer:1.1.2'
}
```
6. In your app  module's build.gradle file, make sure to add this into the dependencies block:
```java
dependencies {
  compile project(":tenjin")
}
```
### Eclipse Integration
1. Download the latest Android SDK from <a href="https://github.com/tenjin/tenjin-android-sdk/releases">here.</a>
2. Create a folder `libs` in your project's root folder.
3. Copy the `tenjin.jar` file to the `libs` folder.
4. Right click on `tenjin.jar` and then select `Build Path` -> `Add to Build Path`
- This should create a folder called `Referenced Libraries` in your project
5. Install Google's `Android Support Repository`, `Android Support Library`, `Google Play Services` and `Google Repository` SDKs from the SDK Manager. Google outlines how to best <a href="http://developer.android.com/google/play-services/setup.html">configure this</a> if you haven't already.

Code Integration:
---------------------------------
1. Get your `API_KEY` from your <a href="https://tenjin.io/dashboard/organizations">Tenjin Organization tab.</a>
2. In your main Activity include the Tenjin SDK with `import com.tenjin.android.TenjinSDK;`

3a. In the `onResume` method of your main `Activity` class add the following line of code:
```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");
instance.connect();
```

Or similarly here's an example of what the `Activity` integration(s) should look like:

```java
import com.tenjin.android.TenjinSDK;
import com.tenjin.android.Callback;

public class TenjinDemo extends ActionBarActivity {

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = "<API_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);
        instance.connect();

        //Your other code...
        //...

    }
}
```
3b. Alternate initialization to handle deep links from other services. (DO NOT USE 3a and 3b. You need to use only one.)
If you use other services to produce deferred deep links, you can pass Tenjin those deep links to handle the attribution logic with your Tenjin enabled deep links.
```java
import com.facebook.applinks.AppLinkData;

import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = "<API_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

        String appLinkUri = "your_deeplink";
        if (appLinkUri){
          instance.connect(appLinkUri);
        } else {
          instance.connect();
        }

        //Your other code...
        //...

    }
}
```

You can verify if the integration is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>. Add your `advertising_id` or `IDFA/GAID` to the list of test devices. You can find this under Support -> <a href="https://www.tenjin.io/dashboard/debug_app_users">Test Devices</a>.  Go to the <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">SDK Live page</a> and send a test events from your app.  You should see a live event come in:
![](https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events.png)

Tenjin and GDPR:
-------
As part of GDPR compliance, with Tenjin's SDK you can opt-in, opt-out devices/users, or select which specific device-related params to opt-in or opt-out.  `OptOut()` will not send any API requests to Tenjin and we will not process any events.

To opt-in/opt-out:
```java
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = "<API_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

        boolean userOptIn = checkOptInValue();

        if (userOptIn) {
            instance.optIn();
        }
        else {
            instance.optOut();
        }

        instance.connect();

        //Your other code...
        //...

    }

    protected boolean checkOptInValue(){
        // check opt-in value
        // return true; // if user opted-in
        return false;
    }
}
```

To opt-in/opt-out specific device-related parameters, you can use the `OptInParams()` or `OptOutParams()`.  `OptInParams()` will only send device-related parameters that are specified.  `OptOutParams()` will send all device-related parameters except ones that are specified.  **Please note that we require at least `ip_address`, `advertising_id`, `developer_device_id`, `limit_ad_tracking`, `referrer` (Android), and `iad` (iOS) to properly track devices in Tenjin's system. If you plan on using Google, you will also need to add: `platform`, `os_version`, `locale`, `device_model`, and `build_id`.**

If you want to only get specific device-related parameters, use `OptInParams()`. In example below, we will only these device-related parameters: `ip_address`, `advertising_id`, `developer_device_id`, `limit_ad_tracking`, `referrer`, and `iad`:

```java
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optInParams = {"ip_address", "advertising_id", "developer_device_id", "limit_ad_tracking", "referrer", "iad"};
instance.optInParams(optInParams);

instance.connect();
```

If you want to send ALL parameters except specfic device-related parameters, use `OptOutParams()`.  In example below, we will send ALL device-related parameters except:


```java
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optOutParams = {"locale", "timezone", "build_id"};
instance.optOutParams(optOutParams);

instance.connect();
```

#### Device-Related Parameters

| Param  | Description | Reference |
| ------------- | ------------- | ------------- |
| ip_address  | IP Address | |
| advertising_id  | Device Advertising ID | [Android](https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.html#getAdvertisingIdInfo(android.content.Context)) |
| limit_ad_tracking  | limit ad tracking enabled | [Android](https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.Info.html#isLimitAdTrackingEnabled()) |
| platform | platform| Android |
| referrer | Google Play Install Referrer | [Android](https://developer.android.com/google/play/installreferrer/index.html) |
| os_version | operating system version | [Android](https://developer.android.com/reference/android/os/Build.VERSION.html#SDK_INT) |
| device | device name | [Android](https://developer.android.com/reference/android/os/Build.html#DEVICE) |
| device_manufacturer | device manufactuer | [Android](https://developer.android.com/reference/android/os/Build.html#MANUFACTURER) |
| device_model | device model | [Android](https://developer.android.com/reference/android/os/Build.html#MODEL) |
| device_brand | device brand | [Android](https://developer.android.com/reference/android/os/Build.html#BRAND) |
| device_product | device product | [Android](https://developer.android.com/reference/android/os/Build.html#PRODUCT) |
| carrier | phone carrier | [Android](https://developer.android.com/reference/android/telephony/TelephonyManager.html#getSimOperatorName()) |
| connection_type | cellular or wifi | [Android](https://developer.android.com/reference/android/net/ConnectivityManager.html#getActiveNetworkInfo()) |
| screen_width | device screen width | [Android](https://developer.android.com/reference/android/util/DisplayMetrics.html#widthPixels) |
| screen_height | device screen height | [Android](https://developer.android.com/reference/android/util/DisplayMetrics.html#heightPixels) |
| os_version_release | operating system version  | [Android](https://developer.android.com/reference/android/os/Build.VERSION.html#RELEASE) |
| build_id | build ID | [Android](https://developer.android.com/reference/android/os/Build.html) |
| locale | device locale | [Android](https://developer.android.com/reference/java/util/Locale.html#getDefault()) |
| country | locale country |[Android](https://developer.android.com/reference/java/util/Locale.html#getDefault()) |
| timezone | timezone | [Android](https://developer.android.com/reference/java/util/TimeZone.html) |


Tenjin purchase event instructions:
-----
To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. There are two ways to send `transaction` events to Tenjin.

1. Validate receipts
Tenjin can validate `transaction` receipts for you.

**IMPORTANT:** You will need to add your app's public key in the <a href="https://www.tenjin.io/dashboard/apps" target="_new">Tenjin dashboard</a> > Your Android App > Edit. You can retreive your Base64-encoded RSA public key from the <a href="https://play.google.com/apps/publish/"> Google Play Developer Console</a> > Select your app > Development Tools > Services & APIs.

![Dashboard](https://s3.amazonaws.com/tenjin-instructions/android_pk.png "dashboard")

After entering your Public Key into the Tenjin dashboard for your app, you can use the Tenjin SDK method below:
```java
public void transaction(String productId, String currencyCode, int quantity, double unitPrice, String purchaseData, String dataSignature)
```

Example:
```java

public void sendPurchaseEvent(Purchase purchase, Double price, String currencyCode) {
        String sku = purchase.getSku();
        String purchaseData = purchase.getOriginalJson();
        String dataSignature = purchase.getSignature();

        TenjinSDK instance = getTenjinInstance();
        instance.transaction(sku, currencyCode, 1, price, purchaseData, dataSignature);
}

```
You can verify if the IAP validation is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>.  You should see a live event come in:
![](https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events.png)

Tenjin custom event integration instructions:
-----

NOTE: **DO NOT SEND CUSTOM EVENTS BEFORE THE INITIALIZATION** `connect()` event (above). The initialization event must come before any custom events are sent.

**IMPORTANT: Limit custom event names to less than 80 characters. Do not exceed 500 unique custom event names.**

You can use the Tenjin SDK to pass a custom event: `eventWithName(String name)`.

The custom interactions with your app can be tied to level cost from each acquisition source that you use through Tenjin's service. Here is an example of usage:

```java
String apiKey = <API_KEY>;
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name - ie. swiping right on the screen
instance.eventWithName("swipe_right");

```

Passing custom events with integer values:

To pass a custom event with an integer value: `eventWithNameAndValue(String name, String value)` or `eventWithNameAndValue(String name, int value)`.

Passing an integer `value` with an event's `name` allows marketers to sum up and track averages of the values passed for that metric in the Tenjin dashboard. If you plan to use DataVault, these values can be used to derive additional metrics that can be useful.

```java
String apiKey = <API_KEY>;
TenjinSDK.instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name and value - ie. paying 100 virtual coins for an item
instance.eventWithNameAndValue("item", "100");
```

Using the example above, the Tenjin dashboard will sum and average the values for all events with the name `item`.

Keep in mind that this event will not work if the value passed is not an integer.

Tenjin deferred deeplink integration instructions:
-------
Tenjin supports the ability to direct users to a specific part of your app after a new attributed install via Tenjin's campaign tracking URLs. You can utilize the `getDeeplink` method and callback to access the deferred deeplink through the data object. To test you can follow the instructions found <a href="http://help.tenjin.io/t/how-do-i-use-and-test-deferred-deeplinks-with-my-campaigns/547">here</a>.

```java
import com.tenjin.android.TenjinSDK;
import com.tenjin.android.Callback;

public class TenjinDemo extends ActionBarActivity {

    //...other callbacks are here

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = "<API_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);
        instance.connect();

        instance.getDeeplink(new Callback() {
            @Override
            public void onSuccess(boolean clickedTenjinLink, boolean isFirstSession, Map<String, String> data) {
                if (clickedTenjinLink) {
                    if (isFirstSession) {
                        if (data.containsKey(TenjinSDK.DEEPLINK_URL)) {
                           // use the deferred_deeplink_url to direct the user to a specific part of your app
                        }
                    }
                }
            }
        });

        //Your other code...
        ...

    }
```

Below are the parameters, if available, that are returned in the deferred deeplink callback:

| Parameter             | Description                                                      |
|-----------------------|------------------------------------------------------------------|
| advertising_id        | Advertising ID of the device                                     |
| ad_network            | Ad network of the campaign                                       |
| campaign_id           | Tenjin campaign ID                                               |
| campaign_name         | Tenjin campaign name                                             |
| site_id               | Site ID of source app                                            |
| referrer              | The referrer params from the app store                           |
| deferred_deeplink_url | The deferred deep-link of the campaign                           |
| clickedTenjinLink     | Boolean representing if the device was tracked by Tenjin         |
| isFirstSession        | Boolean representing if this is the first session for the device |

You can also use the v1.7.1+ SDK for handling post-install logic by checking the `isFirstSession` param. For example, if you have a paid app, you can register your paid app install in the following way:

```java
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    //...other callbacks are here

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = "<API_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);
        instance.connect();

        instance.getDeeplink(new Callback() {
            @Override
            public void onSuccess(boolean clickedTenjinLink, boolean isFirstSession, Map<String, String> data) {
                if (isFirstSession) {
                  // send paid app price and revenue to Tenjin
                }
            }
        });

        //Your other code...
        ...

    }
```

ProGuard Settings:
----
```java
-keep class com.tenjin.** { *; }
-keep public class com.google.android.gms.ads.identifier.** { *; }
-keep public class com.google.android.gms.common.** { *; }
-keep public class com.android.installreferrer.** { *; }
-keep class * extends java.util.ListResourceBundle {
    protected Object[][] getContents();
}
-keepattributes *Annotation*
```

App Subversion parameter for A/B Testing (requires DataVault)
-------

If you are running A/B tests and want to report the differences, we can append a numeric value to your app version using the `appendAppSubversion` method.  For example, if your app version `1.0.1`, and set `appendAppSubversion: @8888`, it will report as `1.0.1.8888`.

This data will appear within DataVault where you will be able to run reports using the app subversion values.

```
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");
instance.appendAppSubversion(8888);
instance.connect();
```
