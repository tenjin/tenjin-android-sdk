# Summary

The Tenjin Android SDK allows users to track events and installs in their Android apps. To learn more about Tenjin and our product offering, please visit https://www.tenjin.com.

- Please see our <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/RELEASE_NOTES.md" target="_new">Release Notes</a> to see detailed version history of changes.
- We recommend using the latest version of <a href="https://developer.android.com/studio/index.html" target="_new">Android Studio</a>.
- For Unity integration, please visit https://github.com/tenjin/tenjin-unity-sdk.
- For any issues or support, please contact: support@tenjin.com.

# Table of contents
- [Basic Integration][1]
  - [Google Play or Amazon store][2]
    - [Permissions][3]
    - [Android Advertising ID (AAID)][4]
    - [App Store][5]
    - [App Initialization][25]
  - [Android other store][6]
    - [Permissions][7]
    - [Android Advertising ID (AAID)][8]
    - [OAID][9]
      - [MSA OAID][10]
      - [Huawei OAID][11]
      - [Huawei Install Referrer][12]
    - [App Store][13]
    - [App Initialization][26]
  - [Proguard settings][14]
- [Additional Integration][15]
  - [GDPR][17]
  - [Purchase Events][18]
  - [Custom Events][19]
  - [Deferred Deeplink][20]
  - [Server-to-server integration][21]
  - [App Subversion][22]
  - [Attribution Info][23]
  - [Customer User ID][24]
  - [Retry/cache events and IAP][52]
  - [Impression Level Ad Revenue Integration][27]
- [Testing][30]

# <a id="setup"></a> Basic Integration
### Manual Installation
Please use the steps listed below under the section 'Android Studio.'

### Maven
If you use Maven, add implementation `com.tenjin:android-sdk:VERSION` to your `Gradle` dependencies and add `mavenCentral()` to the source repositories if it’s not there already.

## Android Studio
1. Download the latest Android SDK from <a href="https://github.com/tenjin/tenjin-android-sdk/releases" target="_new">here.</a>
2. Add the Tenjin SDK into your Android Studio project. Go to the Project Navigator in Android Studio. Select the option `Project` in the Project Navigator. You will find the `libs` folder under the `app` module of your Android Studio project.
3. You need to add the file `tenjin.jar` or `tenjin.aar` to the `libs` folder.
    ![][image-1]
4. In your Android Studio project under `app` module, select the `build.gradle` file,  and add the following under the dependencies block:

    ```java
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar', '*.aar'])
        implementation files('libs/tenjin.aar')
    }
    ```

    > We have a demo project - [tenjin-android-sdk-demo][31] that demonstrates the integration of tenjin-android-sdk. You can this project as example to understand how to integrate the tenjin-android-sdk.

## <a id="google-play"></a>Google Play or Amazon store
If you distribute your apps on Google Play Store or Amazon store, implement the following initial setups.

### <a id="google-play-permissions"></a>Permission
The Tenjin SDK requires the following permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> <!-- Required to get network connectivity (i.e. wifi vs. mobile) -->
```

Later this year, Google Play Services will require all API level 32 (Android 13) apps using the advertising_id(Android Advertising ID (AAID)) to declare the Google Play Services AD_ID permission (shown below) in their manifest file. **Please add this permission as soon as possible.** You are also required to update the tenjin-android-sdk to version 1.12.8 in order to use the below permission.

```xml
<uses-permission android:name="com.google.android.gms.permission.AD_ID"/>
```

### <a id="play-services-ads-identifier-google-play"></a>Android Advertising ID (AAID) and Install Referrer
Add <a href="https://developers.google.com/android/guides/setup#list-dependencies" target="_new">Android Advertising ID (AAID)</a> and <a href="https://developer.android.com/google/play/installreferrer/library" target="_new">Install Referrer</a> libraries, add it to your build.gradle file.

```java
dependencies {
  implementation 'com.google.android.gms:play-services-ads-identifier:{version}'
  implementation 'com.android.installreferrer:installreferrer:{version}'
}
```
### <a id="google-play-app-store"></a>App Store
By default, <b>unspecified</b> is the default App Store. Update the app store value to either <b>googleplay</b>, <b>amazon</b>, or <b>other</b> depending on your app.

1. `AndroidManifest.xml`:

```xml
<meta-data
    android:name="TENJIN_APP_STORE"
    android:value="googleplay" />
```

2. `setAppStore()`:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.googleplay);
```

#### App Store Options
```java
  TenjinSDK.AppStoreType.googleplay // Google Play App Store
  TenjinSDK.AppStoreType.amazon     // Amazon AppStore
  TenjinSDK.AppStoreType.other      // Other
```

### <a id="google-play-initialization"></a> App Initialization
1. Get your `SDK_KEY` from your app page. Note: `SDK_KEY` is unique for each of your app. You can create up to 3 keys for the same app.
![image-3]
2. In your Activity, import Tenjin: `import com.tenjin.android.TenjinSDK;`
3. In the `onResume` method of your main `Activity` class, add the following line of code:

    ```java
    TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
    instance.setAppStore(TenjinSDK.AppStoreType.googleplay);
    instance.connect();
    ```

> [!NOTE]
> If your app has the logic to ask user's consent between `onCreate` and `onResume`, use `onCreate` instead of `onResume` for Tenjin SDK initialization because users who don't consent won't be tracked on `onResume`.

> [!NOTE]
> Please ensure you implement this code on every `onResume`, not only on the first app open of the app. If we notice that you don't follow our recommendation, we can't give you proper support or your account might be suspended.

### <a id="android-other-permissons"></a>Permission
The Tenjin SDK requires the following permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> <!-- Required to get network connectivity (i.e. wifi vs. mobile) -->
```

Later this year, Google Play Services will require all API level 32 (Android 13) apps using the advertising_id(Android Advertising ID (AAID)) to declare the Google Play Services AD_ID permission (shown below) in their manifest file. **Please add this permission as soon as possible.** You are also required to update the tenjin-android-sdk to version 1.12.8 in order to use the below permission.

```xml
<uses-permission android:name="com.google.android.gms.permission.AD_ID"/>
```

### <a id="play-services-ads-identifier-android-other"></a>Android Advertising ID (AAID) and Install Referrer
Add <a href="https://developers.google.com/android/guides/setup#list-dependencies" target="_new">Android Advertising ID (AAID)</a> and <a href="https://developer.android.com/google/play/installreferrer/library" target="_new">Install Referrer</a> libraries, add it to your build.gradle file.

```java
dependencies {
  implementation 'com.google.android.gms:play-services-ads-identifier:{version}'
  implementation 'com.android.installreferrer:installreferrer:{version}'
}
```

If you are using an Ad Network that targets the IMEI, you will need to add the following permissions enabled.
```xml
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

### <a id="oaid"></a>OAID
Tenjin supports promoting your app on other Android App Stores using the Android OAID. We have the following requirements for integrating OAID libraries.

#### <a id="msa-oaid"></a>MSA OAID
MSA OAID is an advertising ID for devices manufactured in China that the MSA (Mobile Security Alliance) provides. For integration with the <a href="http://www.msa-alliance.cn/col.jsp?id=120" target="_new">MSA library</a>, download the following <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/msa-oaid/oaid_sdk_1.0.25.aar" target="_new">oaid\_sdk\_1.0.25.aar</a> and <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/msa-oaid/supplierconfig.json" target="_new">supplierconfig.json</a>.

Add the following to your project gradle file:

```
implementation files('libs/oaid_sdk_1.0.25.aar')
```

Be sure to copy the <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/msa-oaid/supplierconfig.json" target="_new">supplierconfig.json</a> file to the `assets` folder of your project.

#### <a id="huawei-oaid"></a>Huawei OAID
For outside of China, you can collect OAID using the library provided by Huawei. For integration with the <a href="https://developer.huawei.com/consumer/en/codelab/HMSAdsOAID/index.html#3" target="_new">Huawei OAID library</a>, add the following to your project:

In your `build.gradle` file, add the Maven address for the Huawei SDKs:

```
allprojects {
    repositories {
        google()
        maven { url 'https://developer.huawei.com/repo/' }
    }
}
```

```java
dependencies {
    implementation 'com.huawei.hms:ads-identifier:{version}'
}
```

#### <a id="huawei-install-referrer"></a>Huawei Install Referrer
If you are marketing your app with <a href="https://appgallery.huawei.com/" target="_new">Huawei App Gallery</a>, add both the `Huawei OAID` SDK from above and the <a href="https://developer.huawei.com/consumer/en/codelab/HMSAdsTransformOAID/index.html#3" target="_new">Install Referrer</a> library.

```java
dependencies {

    implementation 'com.huawei.hms:ads-identifier:{version}'
    implementation 'com.huawei.hms:ads-installreferrer:{version}'

}
```
### <a id="android-other-app-store"></a>App Store
By default, <b>unspecified</b> is the default App Store. Update the app store value to <b>other</b>.

1. `AndroidManifest.xml`:

```xml
<meta-data
    android:name="TENJIN_APP_STORE"
    android:value="other" />
```

2. `setAppStore()`:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.other);
```

### <a id="android-other-initialization"></a> App Initialization
1. Get your `SDK_KEY` from your app page. Note: `SDK_KEY` is unique for each of your app. You can create up to 3 keys for the same app.
   ![image-3]
2. In your Activity, import Tenjin: `import com.tenjin.android.TenjinSDK;`
3. In the `onCreate` method of your main `Activity` class, add the following line of code:

    ```java
    TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
    
    instance.connect();
    ```

> [!NOTE]
> Please ensure you implement this code on every `onCreate`, not only on the first app open of the app. If we notice that you don't follow our recommendation, we can't give you proper support or your account might be suspended.

## <a id="proguard"></a>Proguard Settings

```java
-keep class com.tenjin.** { *; }
-keep public class com.google.android.gms.ads.identifier.** { *; }
-keep public class com.google.android.gms.common.** { *; }
-keep public class com.android.installreferrer.** { *; }
-keep class * extends java.util.ListResourceBundle {
    protected java.lang.Object[][] getContents();
}
-keepattributes *Annotation*
```

Do not obfuscate oaid classes:

```java
-keep class XI.CA.XI.**{*;}
-keep class XI.K0.XI.**{*;}
-keep class XI.XI.K0.**{*;}
-keep class XI.xo.XI.XI.**{*;}
-keep class com.asus.msa.SupplementaryDID.**{*;}
-keep class com.asus.msa.sdid.**{*;}
-keep class com.bun.lib.**{*;}
-keep class com.bun.miitmdid.**{*;}
-keep class com.huawei.hms.ads.identifier.**{*;}
-keep class com.samsung.android.deviceidservice.**{*;}
-keep class com.zui.opendeviceidlibrary.**{*;}
-keep class org.json.**{*;}
-keep public class com.netease.nis.sdkwrapper.Utils {public <methods>;}
```

If you are using Huawei libraries, you can to use these setttings:

```java
-keep class com.huawei.hms.ads.** { *; }
-keep interface com.huawei.hms.ads.** { *; }
```

# <a id="integration"></a> Additional Integrations

## <a id="gdpr"></a> GDPR

As part of GDPR compliance, with Tenjin's SDK you can opt-in, opt-out devices/users, or select which specific device-related params to opt-in or opt-out. `optOut()` will not send any API requests to Tenjin, and we will not process any events.

To opt-in/opt-out:

```java
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {
    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String sdkKey = "<SDK_KEY>";
        TenjinSDK instance = TenjinSDK.getInstance(this, sdkKey);

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

- Kindly note that we require the following parameter to properly track devices in Tenjin's system. If the mandatory parameter is missing, the event will not be processed or recorded.

  - `advertising_id`

- If you are targeting IMEI and/or OAID Ad Networks, these params are required:

  - `imei`
  - `oaid`

- If you intend to use Google AdWords, these params are required:
  - `platform`
  - `os_version`
  - `locale`
  - `device_model`
  - `build_id`

If you want to only get specific device-related parameters, use `optInParams()`. In example below, we will only these device-related parameters: `ip_address`, `advertising_id`, `limit_ad_tracking`, and `referrer`.

```java
String sdkKey = "<SDK_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, sdkKey);

String[] optInParams = {"ip_address", "advertising_id", "limit_ad_tracking", "referrer"};
instance.optInParams(optInParams);

instance.connect();
```

If you want to send ALL parameters except specific device-related parameters, use `optOutParams()`. In the example below, we will send ALL device-related parameters except:

```java
String sdkKey = "<SDK_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, sdkKey);

String[] optOutParams = {"locale", "timezone", "build_id"};
instance.optOutParams(optOutParams);

instance.connect();
```

## Device-Related Parameters

| Param                | Description                  | Reference     |
| -------------------- | ---------------------------- | ------------- |
| ip\_address          | IP Address                   |               |
| advertising\_id      | Device Advertising ID        | [Android][32] |
| limit\_ad\_tracking  | limit ad tracking enabled    | [Android][33] |
| oaid                 | Open Advertising ID          | [Android][34] |
| imei                 | Device IMEI                  | [Android][35] |
| platform             | Platform                     | Android       |
| referrer             | Google Play Install Referrer | [Android][36] |
| os\_version          | operating system version     | [Android][37] |
| device               | device name                  | [Android][38] |
| device\_manufacturer | device manufacturer          | [Android][39] |
| device\_model        | device model                 | [Android][40] |
| device\_brand        | device brand                 | [Android][41] |
| device\_product      | device product               | [Android][42] |
| carrier              | phone carrier                | [Android][43] |
| connection\_type     | cellular or wifi             | [Android][44] |
| screen\_width        | device screen width          | [Android][45] |
| screen\_height       | device screen height         | [Android][46] |
| os\_version\_release | operating system version     | [Android][47] |
| build\_id            | build ID                     | [Android][48] |
| locale               | device locale                | [Android][49] |
| country              | locale country               | [Android][50] |
| timezone             | timezone                     | [Android][51] |

<br/>

## <a id="purchase-events"></a>Purchase Events

To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. Tenjin will validate `transaction` receipts for you. Kindly note that we currently only support IAP transactions from Google Play and Amazon AppStore.


### <a id="purchase-events-google-play-iap"></a>Google Play IAP

**IMPORTANT:** You will need to add your Base64-encoded RSA public key in the <a href="https://www.tenjin.io/dashboard/apps" target="_new">Tenjin dashboard</a> \> Your Android App \> Edit. 

You can retrieve your Base64-encoded RSA public key from the <a href="https://play.google.com/apps/publish/"> Google Play Developer Console</a> \> Select your app \> Development Tools \> Services & APIs. After entering your Public Key into the Tenjin dashboard for your app, you can use the Tenjin SDK method below:

<img src="https://s3.amazonaws.com/tenjin-instructions/android_pk.png" />

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

### <a id="purchase-events-amazon-iap"></a>Amazon AppStore IAP

> [!IMPORTANT]
> You will need to add the Amazon Shared Key in the <a href="https://www.tenjin.io/dashboard/apps" target="_new">Tenjin dashboard</a> \> Your Android App \> Edit. 

You can retrieve your Amazon Shared Key from the <a href="https://developer.amazon.com/settings/console/sdk/shared-key/"> Amazon AppStore Developer Console</a>.

After entering your Amazon Shared Key into the Tenjin dashboard for your app, you can use the Tenjin SDK method below:

```java
public void transactionAmazon(String productId, String currencyCode, int quantity, double unitPrice, String receiptId, String userId, String receipt)
```

Example:

```java
public void handlePurchase(final Receipt receipt, final UserData userData) {
    try {
        if (receipt.isCanceled()) {
            revokeConsumablePurchase(receipt, userData);
        } else {
            Log.d(TAG, "=====================> RECEIPT: " + receipt.toString());
            Log.d(TAG, "=====================> USER: " + userData.toString());

            String productId = receipt.getSku();

            // The Amazon IAP Receipt does not have currency, quantity, or price
            // You will need to set those values accordingly.
            //
            String currencyCode = "USD";
            int quantity = 1;
            double price = 1.00;

            String userId = userData.getUserId();
            String receiptId = receipt.getReceiptId();
            String purchaseData = receipt.toString();

            TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
            instance.setAppStore(TenjinSDK.AppStoreType.amazon);
            instance.transactionAmazon(productId, currencyCode, quantity, price, receiptId, userId, purchaseData);

        }
    } catch (final Throwable e) {
        mainActivity.showMessage("Purchase cannot be completed, please retry");
    }
}
```

### IAP Purchase Validation

You can verify if the IAP validation is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>. You should see a live event come in:

<img src="https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events_2.png" />

**Disclaimer:** If you are implementing purchase events on Tenjin for the first time, make sure to verify the data with other tools you’re using before you start scaling up your user acquisition campaigns using purchase data.

## <a id="custom-events"></a>Custom Events

> [!NOTE]
> The initialization event `connect()` must come before sending any custom events.

You can use the Tenjin SDK to pass a custom event: `eventWithName(String name)`.

The custom interactions with your app can be tied to level cost from each acquisition source that you use through Tenjin's service. Here is an example of usage:

```java
String sdkKey = <SDK_KEY>;
TenjinSDK instance = TenjinSDK.getInstance(this, sdkKey);

//Integrate a custom event with a distinct name - ie. swiping right on the screen
instance.eventWithName("swipe_right");

```

## Custom Events with values:

You can use the Tenjin SDK to pass a custom event with an integer value: `eventWithNameAndValue(String name, String value)` or `eventWithNameAndValue(String name, int value)`.

Passing an integer `value` with an event's `name` allows marketers to sum up and track averages of the values passed for that metric in the Tenjin dashboard. If you plan to use DataVault, these values can be used to derive additional metrics that can be useful.

```java
String sdkKey = <SDK_KEY>;
TenjinSDK.instance = TenjinSDK.getInstance(this, sdkKey);

//Integrate a custom event with a distinct name and value - ie. paying 100 virtual coins for an item
instance.eventWithNameAndValue("item", 100);
```

Using the example above, the Tenjin dashboard will sum and average the values for all events with the name `item`.

Keep in mind that this event will not work if the value passed not an integer.

```
//Integrate a custom event with a distinct name and value - ie. paying 100 virtual coins for an item
instance.eventWithNameAndValue("item", "1");
```

## <a id="server-to-server"></a>Server-to-server integration

Tenjin offers server-to-server integration, which is a paid feature. If you want to access to the documentation, please send email to support@tenjin.com and discuss the pricing.

## <a id="subversion"></a>App Subversion parameter for A/B Testing (requires DataVault)

If you are running A/B tests and want to report the differences, we can append a numeric value to your app version using the `appendAppSubversion()` method. For example, if your app version `1.0.1`, and set `appendAppSubversion(8888)`, it will report app version as `1.0.1.8888`.

This data will appear within DataVault, where you will be able to run reports using the app subversion values.

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
instance.appendAppSubversion(8888);
instance.connect();
```

## <a id="attributioninfo"></a>Attribution Info
Tenjin supports retrieving of attributes, which are required for developers to get analytics installation id (previously known as tenjin reference id). This parameter can be used when there is no advertising id.

> [!WARNING]
> Attribution Info is a paid feature, so please contact your Tenjin account manager if you are interested in.

## <a id="customer-user-id"></a>Customer User ID
You can set and get customer user id to send as a parameter on events.

`.setCustomerUserId(userId: "user_id")`

`.getCustomerUserId()`

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
instance.setCustomerUserId(userId: "user_id");
userId = instance.getCustomerUserId(); 
```

## <a id="retry-cache"></a>Retry/cache of events/IAP
You can enable/disable retrying and caching events and IAP when requests fail or users don't have internet connection. These events will be sent after a new event has been added to the queue and user has recovered connection.

`.setCacheEventSetting(setting: true)`

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<SDK_KEY>");
instance.setCacheEventSetting(setting: true);
```

## <a id="ilrd"></a>Impression Level Ad Revenue Integration

Tenjin supports the ability to integrate with the Impression Level Ad Revenue (ILRD) feature from,
- AppLovin
- Unity LevelPlay
- HyperBid
- AdMob
- TopOn
- Clever Ads Solutions (CAS)
- TradPlus

This feature allows you to receive events which correspond to your ad revenue is affected by each advertisement show to a user. To enable this feature, follow the below instructions.

> [!WARNING]
> ILRD is a paid feature, so please contact your Tenjin account manager to discuss the price at first before sending ILRD events.

# <a id="testing"></a>Testing

You can verify if the integration is working through our <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>. Add your `advertising_id` or `IDFA/GAID` to the list of test devices. You can find this under Support -\> <a href="https://www.tenjin.io/dashboard/debug_app_users">Test Devices</a>. Go to the <a href="https://www.tenjin.io/dashboard/sdk_diagnostics">SDK Live page</a> and send the test events from your app. You should see live events come in:

<br />

![][image-2]

<br /><br />

[1]:	#setup
[2]:	#google-play
[3]:	#google-play-permissions
[4]:	#play-services-ads-identifier-google-play
[5]:	#google-play-app-store
[6]:	#android-other
[7]:	#android-other-permissons
[8]:	#play-services-ads-identifier-android-other
[9]:	#oaid
[10]:	#msa-oaid
[11]:	#huawei-oaid
[12]:	#huawei-install-referrer
[13]:	#android-other-app-store
[14]:	#proguard
[15]:	#integration
[17]:	#gdpr
[18]:	#purchase-events
[19]:	#custom-events
[20]:	#deferred-deeplink
[21]:	#server-to-server
[22]:	#subversion
[23]: #attributioninfo
[24]:	#customer-user-id
[25]:	#google-play-initialization
[26]:	#android-other-initialization
[27]:	#ilrd
[30]:	#testing
[31]:	https://github.com/tenjin/tenjin-android-sdk-demo
[32]:	https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.html#getAdvertisingIdInfo(android.content.Context)
[33]:	https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.Info.html#isLimitAdTrackingEnabled()
[34]:	http://www.msa-alliance.cn/col.jsp?id=120
[35]:	https://developer.android.com/reference/android/telephony/TelephonyManager#getImei()
[36]:	https://developer.android.com/google/play/installreferrer/index.html
[37]:	https://developer.android.com/reference/android/os/Build.VERSION.html#SDK_INT
[38]:	https://developer.android.com/reference/android/os/Build.html#DEVICE
[39]:	https://developer.android.com/reference/android/os/Build.html#MANUFACTURER
[40]:	https://developer.android.com/reference/android/os/Build.html#MODEL
[41]:	https://developer.android.com/reference/android/os/Build.html#BRAND
[42]:	https://developer.android.com/reference/android/os/Build.html#PRODUCT
[43]:	https://developer.android.com/reference/android/telephony/TelephonyManager.html#getSimOperatorName()
[44]:	https://developer.android.com/reference/android/net/ConnectivityManager.html#getActiveNetworkInfo()
[45]:	https://developer.android.com/reference/android/util/DisplayMetrics.html#widthPixels
[46]:	https://developer.android.com/reference/android/util/DisplayMetrics.html#heightPixels
[47]:	https://developer.android.com/reference/android/os/Build.VERSION.html#RELEASE
[48]:	https://developer.android.com/reference/android/os/Build.html
[49]:	https://developer.android.com/reference/java/util/Locale.html#getDefault()
[50]:	https://developer.android.com/reference/java/util/Locale.html#getDefault()
[51]:	https://developer.android.com/reference/java/util/TimeZone.html
[52]: #retry-cache

[image-1]:	https://tenjin-instructions.s3.amazonaws.com/android_jar.png
[image-2]:	https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events_2.png
[image-3]:	https://s3.amazonaws.com/tenjin-instructions/app_api_key.png
