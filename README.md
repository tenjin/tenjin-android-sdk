
Please see our <a href="https://github.com/tenjin/tenjin-android-sdk/wiki">Release Notes</a> to see detailed version history.

For Unity-specific instructions, please visit https://github.com/tenjin/tenjin-unity-sdk.

Tenjin Android SDK (v1.8.4) (132KB) - Google Play and Amazon Store support
==================
**Note: We recommend using the latest version of <a href="https://developer.android.com/studio/index.html">Android Studio</a> when integrating our SDK.**

Manifest requirements:
----

- Include `INTERNET` permissions within the manifest tags
- Include Google Play Services within the application tags
- Include Tenjin's INSTALL_REFERRER receiver

```xml
<manifest>
  ...
  <application ...>
    <meta-data android:name="com.google.android.gms.version"
           android:value="@integer/google_play_services_version" />
    ...
    <receiver android:name="com.tenjin.android.TenjinReferrerReceiver" android:exported="true">
      <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER"/>
      </intent-filter>
    </receiver>
    ...
  </application>
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
  compile 'com.google.android.gms:play-services:10.0.1'
  compile 'com.android.installreferrer:installreferrer:1.0'
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

3a. For each `onResume` method of every `Activity` add the following line of code:
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
        final TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

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

Tenjin GDPR:
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

To opt-in/opt-out specific device-related parameters, you can use the `OptInParams()` or `OptOutParams()`.  `OptInParams()` will only send device-related parameters that are specified.  `OptOutParams()` will send all device-related parameters except ones that are specified.  Please note that we require at least `ip_address`, `advertising_id`, `developer_device_id`, `limit_ad_tracking`, `referrer` (Android), and `iad` (iOS) to properly track devices in Tenjin's system.

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
| advertising_id  | Device Advertising ID | [Android](https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.html#getAdvertisingIdInfo(android.content.Context)) |
| limit_ad_tracking  | limit ad tracking enabled | [Android](https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.Info.html#isLimitAdTrackingEnabled()) |
| platform | platform| Android |
| referrer | Google Play Install Referrer | [Android](https://developer.android.com/google/play/installreferrer/index.html) |
| os_version | Operating system version | [Android](https://developer.android.com/reference/android/os/Build.VERSION.html#SDK_INT) |
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
Tenjin can validate `transaction` receipts for you. Visit your app on the dashboard (<a href="https://www.tenjin.io/dashboard/apps">Apps</a> -> Your Android App -> Edit) and enter your Public Key that can be found in your Google Play dashboard under "Services & APIs".

![Dashboard](https://s3.amazonaws.com/tenjin-instructions/android_pk.png "dashboard")

After entering your Public Key into the Tenjin dashboard for your app, you can use the Tenjin SDK method below:
```java
public void transaction(String productId, String currencyCode, int quantity, double unitPrice, String purchaseData, String dataSignature)
```

Here's an example of this can be implemented at the time of purchase (ex. code taken from here http://developer.android.com/google/play/billing/billing_integrate.html#Purchase):
```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
   if (requestCode == 1001) {
      int responseCode = data.getIntExtra("RESPONSE_CODE", 0);
      String purchaseData = data.getStringExtra("INAPP_PURCHASE_DATA");
      String dataSignature = data.getStringExtra("INAPP_DATA_SIGNATURE");

      if (resultCode == RESULT_OK) {
         try {
            JSONObject jo = new JSONObject(purchaseData);
            String sku = jo.getString("productId");

            //here you will need to assign the currencyCode, quantity, and the price
            TenjinSDK.getInstance(this, "<API_KEY>").transaction(sku, "USD", 1, 3.99, purchaseData, dataSignature)
          }
          catch (JSONException e) {
             alert("Failed to parse purchase data.");
             e.printStackTrace();
          }
      }
   }
}
```
Once code implemented, send a test transaction to our backend to verify purchase events are coming in.  Add your `advertising_id` or `IDFA/GAID` to the list of test devices. You can find this under Support -> <a href="https://www.tenjin.io/dashboard/debug_app_users">Test Devices</a>.  Go to the <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">SDK Live page</a> and send a test transaction from your app.  You should see a live event come in showing the verified purchase event:
![](https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events.png)


2. Pass the transaction manually (usually this is necessary if purchases are not handled by Google Play)
To send `transaction` events, you must provide the `productId`, `currencyCode`, `quantity`, and `unitPrice` of the user's transaction following method signature below:

`public void transaction(String productId, String currencyCode, int quantity, double unitPrice)`.

Here's an example of how this can be implemented at the time of purchase:
```java
//The developer's own method for completing a transaction that happened in app
public void completeTransaction(String productId, String currencyCode, int quantity, double unitPrice){
  ...
  //Call the Tenjin SDK with the context and the API_KEY
  TenjinSDK.getInstance(this, "<API_KEY>").transaction(productId, currencyCode, quantity, unitPrice);
  ...
}
```

- `productId` -> Name or ID of the product that you're selling
- `currencyCode` -> Currency code of the price
- `quantity` -> Number of transactions that you are doing on this event
- `unitPrice` -> Unit price of a single transaction

Tenjin will calculate the Total Revenue from a transaction based on `quantity`*`unitPrice`
Tenjin will record and track the revenue based on the currency code, quantity, and the unit price sent.

Tenjin custom event integration instructions:
-----
You can use the Tenjin SDK to pass a custom event: `eventWithName(String name)`.

The custom interactions with your app can be tied to level cost from each acquisition source that you use through Tenjin's service. Here is an example of usage:

```java
String apiKey = <API_KEY>;
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name - ie. swiping right on the screen
instance.eventWithName("swipe_right");

```

Passing custom events with integer values:
----
NOTE: **DO NOT SEND CUSTOM EVENTS BEFORE THE INITIALIZATION** `connect()` event (above). The initialization event must come before any custom events are sent. 

You can use the Tenjin SDK to pass a custom event with an integer value: `eventWithNameAndValue(String name, String value)` or `eventWithNameAndValue(String name, int value)`.

Passing an integer `value` with an event's `name` allows marketers to sum up and track averages of the values passed for that metric in the Tenjin dashboard. If you plan to use DataVault, these values can be used to derive additional metrics that can be useful.

```java
String apiKey = <API_KEY>;
TenjinSDK.instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name and value - ie. paying 100 virtual coins for an item
instance.eventWithNameAndValue("item", "100");
```

Using the example above, the Tenjin dashboard will sum and average the values for all events with the name `item`.

Keep in mind that this event will not work if the value passed not an integer.


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
        String apiKey = "<API_KEY>"; //You can potentially set this as a global variable too
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
        String apiKey = "<API_KEY>"; //You can potentially set this as a global variable too
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

Testing the Android Referrer:
----
To test the Android `INSTALL_REFERRER` is working:

1. Do the above initialization instrutions
2. Open up your `./adb shell`. If `adb` is not in your home directory locate it in your Android SDK folder
3. Run your app
4. Filter for the `REF` tag in your IDE
5. Run and Test:
```sh
am broadcast -a com.android.vending.INSTALL_REFERRER -n <com.your.apppackage>/com.tenjin.android.TenjinReferrerReceiver --es "referrer" "ai=test&gclid=click_test"
```
After testing this you should see the output of the values passed to your `referrer` in your IDE console. In the case above you would see:

`ai=test&gclid=click_test`
