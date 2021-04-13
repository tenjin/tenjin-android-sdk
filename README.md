# Summary

The native Android SDK for Tenjin. To learn more about Tenjin and our product offering, please visit https://www.tenjin.com.

- Please see our <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/RELEASE_NOTES.md" target="_new">Release Notes</a> to see detailed version history of changes.
- We recommend using the latest version of <a href="https://developer.android.com/studio/index.html" target="_new">Android Studio</a>.
- For Unity integration, please visit https://github.com/tenjin/tenjin-unity-sdk.
- For any issues or support, please contact: support@tenjin.com

# Table of contents

- [Permissions](#permissions)
- [SDK Integration](#sdk-integration)
  - [Google Play Services](#google-play-services)
  - [OAID](#oaid)
    - [MSA OAID](#msa-oaid)
    - [Huawei OAID](#huawei-oaid)
    - [Huawei Install Referrer](#huawei-install-referrer)
    - [Setting for other Android App Store](#other-app-store)
  - [Proguard](#proguard)
- [Integration](#integration)

  - [App Initilization](#initialization)
  - [App Store](#app-store)
  - [GDPR](#gdpr)
  - [Purchase Events](#purchase-events)
  - [Custom Events](#custom-events)
  - [Deferred Deeplink](#deferred-deeplink)
  - [App Subversion](#subversion)

- [Testing](#testing)

# <a id="permissions"></a> Permissions

The Tenjin SDK requires the following permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> <!-- Required to get network connectivity (i.e. wifi vs. mobile) -->
```

If you are using an Ad Network that targets the `IMEI`, you will need to add the following permissions enabled:

```xml
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

# <a id="sdk-integration"></a> SDK Integration

### Android Studio

1. Download the latest Android SDK from <a href="https://github.com/tenjin/tenjin-android-sdk/releases" target="_new">here.</a>
2. Add the Tenjin SDK into your Android Studio project by selecting: `New > Module`.
3. In the New Module dialog, select the `Import .JAR.AAR Package` option and click on Next.
   <br /><br />
   ![AndroidStudio](https://tenjin-instructions.s3.amazonaws.com/android_studio_import.png "studio")
   <br /><br />
4. Select the `tenjin.jar` or `tenjin.aar` file and click on Finish.
5. In your app module's build.gradle file, make sure to add this into the dependencies block:

```java
dependencies {
  compile project(":tenjin")
}
```

## <a id="google-play-services"></a>Google Play Services and Install Referrer

If you are doing distribution with Google Play, you will need to add <a href="https://developers.google.com/android/guides/setup" target="_new">Google Play Services</a> and <a href="https://developer.android.com/google/play/installreferrer/library" target="_new">Install Referrer</a> libraries, add it to your build.gradle file.

```java
dependencies {
  implementation 'com.google.android.gms:play-services-analytics:{version}'
  implementation 'com.android.installreferrer:installreferrer:{version}'
}
```

## <a id="oaid"></a>OAID and other Android App Stores

Tenjin supports promoting on other Android App Stores using the Android OAID. We have the following options for integrating OAID libraries.

### <a id="msa-oaid"></a>MSA OAID (In China)

For integration with the <a href="http://www.msa-alliance.cn/col.jsp?id=120" target="_new">MSA libary</a>, download the following <a href="msa-oaid/oaid_sdk_1.0.25.aar">oaid_sdk_1.0.25.aar</a> and <a href="msa-oaid/supplierconfig.json">supplierconfig.json</a>.

Add the following to your project gradle file:

```
implementation files('libs/oaid_sdk_1.0.23.aar')

```

Be sure to copy the <a href="msa-oaid/supplierconfig.json">supplierconfig.json</a> file to the `assets/supplierconfig.json` folder of your project.

#### <a id="other-app-store"></a>Android Other App Store

In order to properly track installs from Third Party App Stores that use OAID/IMEI for tracking, it is required to properly set the App Store source.

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.other);
```

### <a id="huawei-oaid"></a>Huawei OAID (Outside China)

For integration with the <href="https://developer.huawei.com/consumer/en/codelab/HMSAdsOAID/index.html#3" target="\_new">Huawei OAID libary</a>, add the following to your project:

In your `build.gradle` file, add the Maven address for the Huawei SDKs:

```

allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://developer.huawei.com/repo/' }
    }
}

```

```java
dependencies {
    implementation 'com.huawei.hms:ads-identifier:{version}'
}
```

#### <a id="other-app-store"></a>Android Other App Store

In order to properly track installs from Third Party App Stores that use OAID/IMEI for tracking, it is required to properly set the App Store source.

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.other);
```

### <a id="huawei-install-referrer"></a>Huawei Install Referrer

If you are marketing your app with <a href="https://appgallery.huawei.com/" target="_new">Huawei App Gallery</a>, add both the `Huawei OAID` SDK from above and the <a href="https://developer.huawei.com/consumer/en/codelab/HMSAdsTransformOAID/index.html#3" target="_new">Install Referrer</a> library.

```java
dependencies {

    implementation 'com.huawei.hms:ads-identifier:{version}'
    implementation 'com.huawei.hms:ads-installreferrer:{version}'

}
```

## <a id="proguard"></a>Proguard Settings

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

If you are using Huawei libraries, you can to use these setttings:

```java
-keep class com.huawei.hms.ads.** { *; }
-keep interface com.huawei.hms.ads.** { *; }
```

# <a id="integration"></a> Integration

## <a id="initialization"></a> App Initialization

1. Get your `<API_KEY>` from your <a href="https://www.tenjin.io/dashboard/docs" target="_new">Tenjin dashboard</a>.
2. In your Activity, import Tenjin: `import com.tenjin.android.TenjinSDK;`
3. In the `onResume` method of your main `Activity` class add the following line of code:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.connect();
```

## <a id="app-store"></a> App Store

By default, <a href="https://play.google.com/" target="_new">Google Play</a> is the default App Store. If you are publishing in a different App Store, update to the appropriate `TenjinSDK.AppStoreType.*` value:

1. `AndroidManifest.xml`:

```xml
<meta-data
    android:name="TENJIN_APP_STORE"
    android:value="{{APP_STORE_TYPE_VALUE}}" />
```

2. `setAppStore()`:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.{{APP_STORE_TYPE_VALUE}});
```

Current `AppStoreType` options:

- `googleplay`
- `amazon`
- `other`

## <a id="gdpr"></a> GDPR

As part of GDPR compliance, with Tenjin's SDK you can opt-in, opt-out devices/users, or select which specific device-related params to opt-in or opt-out. `optOut()` will not send any API requests to Tenjin and we will not process any events.

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

To opt-in/opt-out specific device-related parameters, you can use `optInParams()` or `optOutParams()`.

- `optInParams()` will only send device-related parameters that are specified. `optOutParams()` will send all device-related parameters except ones that are specified.

- Please note that we require the following parameters to properly track devices in Tenjin's system:

  - `ip_address`
  - `advertising_id`
  - `limit_ad_tracking`
  - `referrer`

- If you are targeting IMEI and/or OAID Ad Networks, these params are required:

  - `imei`
  - `oaid`

- If you plan on using Google AdWords, these params are required:
  - `platform`
  - `os_version`
  - `locale`
  - `device_model`
  - `build_id`

If you want to only get specific device-related parameters, use `optInParams()`. In example below, we will only these device-related parameters: `ip_address`, `advertising_id`, `limit_ad_tracking`, and `referrer`.

```java
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optInParams = {"ip_address", "advertising_id", "limit_ad_tracking", "referrer"};
instance.optInParams(optInParams);

instance.connect();
```

If you want to send ALL parameters except specfic device-related parameters, use `optOutParams()`. In example below, we will send ALL device-related parameters except:

```java
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optOutParams = {"locale", "timezone", "build_id"};
instance.optOutParams(optOutParams);

instance.connect();
```

## Device-Related Parameters

| Param               | Description                  | Reference                                                                                                                                                                 |
| ------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ip_address          | IP Address                   |                                                                                                                                                                           |
| advertising_id      | Device Advertising ID        | [Android](<https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.html#getAdvertisingIdInfo(android.content.Context)>) |
| limit_ad_tracking   | limit ad tracking enabled    | [Android](<https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.Info.html#isLimitAdTrackingEnabled()>)               |
| platform            | Platform                     | Android                                                                                                                                                                   |
| referrer            | Google Play Install Referrer | [Android](https://developer.android.com/google/play/installreferrer/index.html)                                                                                           |
| os_version          | operating system version     | [Android](https://developer.android.com/reference/android/os/Build.VERSION.html#SDK_INT)                                                                                  |
| device              | device name                  | [Android](https://developer.android.com/reference/android/os/Build.html#DEVICE)                                                                                           |
| device_manufacturer | device manufactuer           | [Android](https://developer.android.com/reference/android/os/Build.html#MANUFACTURER)                                                                                     |
| device_model        | device model                 | [Android](https://developer.android.com/reference/android/os/Build.html#MODEL)                                                                                            |
| device_brand        | device brand                 | [Android](https://developer.android.com/reference/android/os/Build.html#BRAND)                                                                                            |
| device_product      | device product               | [Android](https://developer.android.com/reference/android/os/Build.html#PRODUCT)                                                                                          |
| carrier             | phone carrier                | [Android](<https://developer.android.com/reference/android/telephony/TelephonyManager.html#getSimOperatorName()>)                                                         |
| connection_type     | cellular or wifi             | [Android](<https://developer.android.com/reference/android/net/ConnectivityManager.html#getActiveNetworkInfo()>)                                                          |
| screen_width        | device screen width          | [Android](https://developer.android.com/reference/android/util/DisplayMetrics.html#widthPixels)                                                                           |
| screen_height       | device screen height         | [Android](https://developer.android.com/reference/android/util/DisplayMetrics.html#heightPixels)                                                                          |
| os_version_release  | operating system version     | [Android](https://developer.android.com/reference/android/os/Build.VERSION.html#RELEASE)                                                                                  |
| build_id            | build ID                     | [Android](https://developer.android.com/reference/android/os/Build.html)                                                                                                  |
| locale              | device locale                | [Android](<https://developer.android.com/reference/java/util/Locale.html#getDefault()>)                                                                                   |
| country             | locale country               | [Android](<https://developer.android.com/reference/java/util/Locale.html#getDefault()>)                                                                                   |
| timezone            | timezone                     | [Android](https://developer.android.com/reference/java/util/TimeZone.html)                                                                                                |

<br/>

## <a id="purchase-events"></a>Purchase Events

To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. Tenjin will validate `transaction` receipts for you.

**IMPORTANT:** You will need to add your app's public key in the <a href="https://www.tenjin.io/dashboard/apps" target="_new">Tenjin dashboard</a> > Your Android App > Edit. You can retreive your Base64-encoded RSA public key from the <a href="https://play.google.com/apps/publish/"> Google Play Developer Console</a> > Select your app > Development Tools > Services & APIs.

<br/>
![](https://s3.amazonaws.com/tenjin-instructions/android_pk.png)
<br/><br/>

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

You can verify if the IAP validation is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>. You should see a live event come in:

<br/>
![](https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events.png)
<br/><br/>

## <a id="custom-events"></a>Custom Events

**NOTE:** The initialization event `connect()` must come before any custom events are sent.

You can use the Tenjin SDK to pass a custom event: `eventWithName(String name)`.

The custom interactions with your app can be tied to level cost from each acquisition source that you use through Tenjin's service. Here is an example of usage:

```java
String apiKey = <API_KEY>;
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name - ie. swiping right on the screen
instance.eventWithName("swipe_right");

```

## Custom Events with values:

You can use the Tenjin SDK to pass a custom event with an integer value: `eventWithNameAndValue(String name, String value)` or `eventWithNameAndValue(String name, int value)`.

Passing an integer `value` with an event's `name` allows marketers to sum up and track averages of the values passed for that metric in the Tenjin dashboard. If you plan to use DataVault, these values can be used to derive additional metrics that can be useful.

```java
String apiKey = <API_KEY>;
TenjinSDK.instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name and value - ie. paying 100 virtual coins for an item
instance.eventWithNameAndValue("item", 100);
```

Using the example above, the Tenjin dashboard will sum and average the values for all events with the name `item`.

Keep in mind that this event will not work if the value passed not an integer.

## <a id="deferred-deeplink"></a>Deferred Deeplink

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
| --------------------- | ---------------------------------------------------------------- |
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

## <a id="subversion"></a>App Subversion parameter for A/B Testing (requires DataVault)

If you are running A/B tests and want to report the differences, we can append a numeric value to your app version using the `appendAppSubversion()` method. For example, if your app version `1.0.1`, and set `appendAppSubversion(8888)`, it will report app version as `1.0.1.8888`.

This data will appear within DataVault where you will be able to run reports using the app subversion values.

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");
instance.appendAppSubversion(8888);
instance.connect();
```

# <a id="testing"></a>Testing

You can verify if the integration is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>. Add your `advertising_id` or `IDFA/GAID` to the list of test devices. You can find this under Support -> <a href="https://www.tenjin.io/dashboard/debug_app_users">Test Devices</a>. Go to the <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">SDK Live page</a> and send a test events from your app. You should see a live events come in:

<br />

![](https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events.png)

<br /><br />
