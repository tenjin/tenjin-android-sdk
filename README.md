# Summary

The native Android SDK for Tenjin. To learn more about Tenjin and our product offering, please visit https://www.tenjin.com.

- Please see our <a href="https://github.com/tenjin/tenjin-android-sdk/blob/master/RELEASE_NOTES.md" target="_new">Release Notes</a> to see detailed version history of changes.
- We recommend using the latest version of <a href="https://developer.android.com/studio/index.html" target="_new">Android Studio</a>.
- For Unity integration, please visit https://github.com/tenjin/tenjin-unity-sdk.
- For any issues or support, please contact: support@tenjin.com.

# Table of contents
- [Initial setup][1]
  - [Google Play or Amazon store][2]
	- [Permissions][3]
	- [Android Advertising ID (AAID)][4]
	- [App Store][5]
  - [Android other store][6]
	- [Permissions][7]
	- [Android Advertising ID (AAID)][8]
	- [OAID][9]
	  - [MSA OAID][10]
	  - [Huawei OAID][11]
	  - [Huawei Install Referrer][12]
	- [App Store][13]
- [Proguard settings][14]
- [Integration][15]
  - [App Initilization][16]
  - [GDPR][17]
  - [Purchase Events][18]
  - [Custom Events][19]
  - [Deferred Deeplink][20]
  - [Server-to-server integration][21]
  - [App Subversion][22]
- [Impression Level Ad Revenue Integration][23]
  - [AppLovin Impression Level Ad Revenue Integration][24]
  - [IronSource Impression Level Ad Revenue Integration][25]
  - [HyperBid Impression Level Ad Revenue Integration][26]
  - [AdMob Impression Level Ad Revenue Integration][27]
  - [TopOn Impression Level Ad Revenue Integration][28]
- [Testing][29]

# <a id="setup"></a> Initial setup
## Android Studio

1. Download the latest Android SDK from <a href="https://github.com/tenjin/tenjin-android-sdk/releases" target="_new">here.</a>
2. Add the Tenjin SDK into your Android Studio project. Go to the Project Navigator in Android Studio. Select the option `Project` in the Project Navigator. You will find the `libs` folder under the `app` module of your Android Studio project.
3. You need to add the file `tenjin.jar` or `tenjin.aar` to the `libs` folder.
   <br /><br />
   ![AndroidStudio][image-1]
   <br /><br />
4. In your Android Studio project under `app` module, select the `build.gradle` file,  and add the following under the dependencies block:
```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar', '*.aar'])
    implementation files('libs/tenjin.aar')
}
```

> We have a demo project - [tenjin-android-sdk-demo][30] that demonstrates the integration of tenjin-android-sdk. You can this project as example to understand how to integrate the tenjin-android-sdk.

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
By default, <b>unspecified</b> is the default App Store. Update the app store value to either <b>googleplay</b> or <b>amazon</b>, depending on your app.

1. `AndroidManifest.xml`:

```xml
<meta-data
    android:name="TENJIN_APP_STORE"
    android:value="googleplay" />
```

2. `setAppStore()`:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.googleplay);
```

## <a id="android-other"></a>Other Android store
If you distribute your apps outside of Google Play Store or Amazon store(Other Android store), implement the following initial setups.

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
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.setAppStore(TenjinSDK.AppStoreType.other);
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

# <a id="integration"></a> Integration

## <a id="initialization"></a> App Initialization

1. Get your `<API_KEY>` from your <a href="https://www.tenjin.io/dashboard/docs" target="_new">Tenjin dashboard</a>.
2. In your Activity, import Tenjin: `import com.tenjin.android.TenjinSDK;`
3. In the `onResume` method of your main `Activity` class, add the following line of code:

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");

instance.connect();
```

**NOTE:** Please ensure you implement this code on every `onResume`, not only on the first app open of the app. If we notice that you don't follow our recommendation, we can't give you proper support or your account might be suspended.

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
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optInParams = {"ip_address", "advertising_id", "limit_ad_tracking", "referrer"};
instance.optInParams(optInParams);

instance.connect();
```

If you want to send ALL parameters except specific device-related parameters, use `optOutParams()`. In the example below, we will send ALL device-related parameters except:

```java
String apiKey = "<API_KEY>";
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

String[] optOutParams = {"locale", "timezone", "build_id"};
instance.optOutParams(optOutParams);

instance.connect();
```

## Device-Related Parameters

| Param                | Description                  | Reference     |
| -------------------- | ---------------------------- | ------------- |
| ip\_address          | IP Address                   |               |
| advertising\_id      | Device Advertising ID        | [Android][31] |
| limit\_ad\_tracking  | limit ad tracking enabled    | [Android][32] |
| oaid                 | Open Advertising ID          | [Android][33] |
| imei                 | Device IMEI                  | [Android][34] |
| platform             | Platform                     | Android       |
| referrer             | Google Play Install Referrer | [Android][35] |
| os\_version          | operating system version     | [Android][36] |
| device               | device name                  | [Android][37] |
| device\_manufacturer | device manufacturer          | [Android][38] |
| device\_model        | device model                 | [Android][39] |
| device\_brand        | device brand                 | [Android][40] |
| device\_product      | device product               | [Android][41] |
| carrier              | phone carrier                | [Android][42] |
| connection\_type     | cellular or wifi             | [Android][43] |
| screen\_width        | device screen width          | [Android][44] |
| screen\_height       | device screen height         | [Android][45] |
| os\_version\_release | operating system version     | [Android][46] |
| build\_id            | build ID                     | [Android][47] |
| locale               | device locale                | [Android][48] |
| country              | locale country               | [Android][49] |
| timezone             | timezone                     | [Android][50] |

<br/>

## <a id="purchase-events"></a>Purchase Events

To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. Tenjin will validate `transaction` receipts for you. Kindly note that we currently only support IAP transactions from Google Play.

**IMPORTANT:** You will need to add your app's public key in the <a href="https://www.tenjin.io/dashboard/apps" target="_new">Tenjin dashboard</a> \> Your Android App \> Edit. You can retrieve your Base64-encoded RSA public key from the <a href="https://play.google.com/apps/publish/"> Google Play Developer Console</a> \> Select your app \> Development Tools \> Services & APIs.

<br/>
<img src="https://s3.amazonaws.com/tenjin-instructions/android_pk.png" />
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
<img src="https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events_2.png" />
<br/>

**Disclaimer:** If you are implementing purchase events on Tenjin for the first time, make sure to verify the data with other tools you’re using before you start scaling up your user acquisition campaigns using purchase data.

## <a id="custom-events"></a>Custom Events

**NOTE:** The initialization event `connect()` must come before sending any custom events.

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

Tenjin supports the ability to direct users to a specific part of your app after a new attributed installation via Tenjin's campaign tracking URLs. You can utilize the `getDeeplink` method and callback to access the deferred deeplink through the data object. To test, you can follow the instructions found <a href="http://help.tenjin.io/t/how-do-i-use-and-test-deferred-deeplinks-with-my-campaigns/547">here</a>.

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

Below are the parameters, if available, that are returned to the deferred deeplink callback:

| Parameter               | Description                                                      |
| ----------------------- | ---------------------------------------------------------------- |
| advertising\_id         | Advertising ID of the device                                     |
| ad\_network             | Ad network of the campaign                                       |
| campaign\_id            | Tenjin campaign ID                                               |
| campaign\_name          | Tenjin campaign name                                             |
| site\_id                | Site ID of source app                                            |
| referrer                | The referrer params from the app store                           |
| deferred\_deeplink\_url | The deferred deep-link of the campaign                           |
| clickedTenjinLink       | Boolean representing if the device was tracked by Tenjin         |
| isFirstSession          | Boolean representing if this is the first session for the device |

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

## <a id="server-to-server"></a>Server-to-server integration

Tenjin offers server-to-server integration, which is a paid feature. If you want to access to the documentation, please send email to support@tenjin.com and discuss the pricing.

## <a id="subversion"></a>App Subversion parameter for A/B Testing (requires DataVault)

If you are running A/B tests and want to report the differences, we can append a numeric value to your app version using the `appendAppSubversion()` method. For example, if your app version `1.0.1`, and set `appendAppSubversion(8888)`, it will report app version as `1.0.1.8888`.

This data will appear within DataVault, where you will be able to run reports using the app subversion values.

```java
TenjinSDK instance = TenjinSDK.getInstance(this, "<API_KEY>");
instance.appendAppSubversion(8888);
instance.connect();
```

# <a id="ilrd"></a>Impression Level Ad Revenue Integration

Tenjin supports the ability to integrate with the Impression Level Ad Revenue (ILRD) feature from,
- AppLovin
- IronSource
- HyperBid
- AdMob
- TopOn

This feature allows you to receive events which correspond to your ad revenue is affected by each advertisement show to a user. To enable this feature, follow the below instructions.

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #a94442; background-color: #f2dede; border-color: #ebccd1;">
NOTE: ILRD is a paid feature, so please contact your Tenjin account manager to discuss the price at first before sending ILRD events.
</div>
 
## <a id="applovin"></a>AppLovin Impression Level Ad Revenue Integration

> *NOTE*, Please ensure you have the latest AppLovin Android SDK installed (\> 10.3.5)

```
public class DemoActivity extends Activity implements MaxAdRevenueListener {
    private TenjinSDK tenjinInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Initialize Tenjin
        this.tenjinInstance = TenjinSDK.getInstance(this, "<Tenjin API Key>");

        // Initialize AppLovin
        AppLovinSdk.getInstance(this).setMediationProvider(AppLovinMediationProvider.MAX);
        AppLovinSdk.initializeSdk(this);

        // AppLovin Banner
        initAppLovinBanner();
    }

    private void initAppLovinBanner() {
        MaxAdView adView = new MaxAdView("<Applovin Ad unit ID>", this);
        adView.setPlacement("Placement name Banner");
        adView.setRevenueListener(this);

        // Set the height of the banner ad based on the device type.
        final boolean isTablet = AppLovinSdkUtils.isTablet(this);
        final int heightPx = AppLovinSdkUtils.dpToPx(this, isTablet ? 90 : 50);
        // Banner width must match the screen to be fully functional.
        adView.setLayoutParams(
            new FrameLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, heightPx));

        // Need to set the background or background color for banners to be fully functional.
        adView.setBackgroundColor(Color.BLACK);

        final ViewGroup rootView = findViewById(android.R.id.content);
        rootView.addView(adView);

        // Load the first ad.
        adView.loadAd();
    }

    @Override
    public void onAdRevenuePaid(MaxAd maxAd) {
        tenjinInstance.eventAdImpressionAppLovin(maxAd);
    }
}

```

## <a id="ironsource"></a>IronSource Impression Level Ad Revenue Integration

> *NOTE*, Please ensure you have the latest IronSource Android SDK installed (\> 1.7.13.1)

```
public class DemoActivity extends Activity implements ImpressionDataListener {
    private TenjinSDK tenjinInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Initialize Tenjin
        this.tenjinInstance = TenjinSDK.getInstance(this, "<Tenjin API Key>");

        // Initialize IronSource
        IronSource.addImpressionDataListener(this);
        IronSource.init(this, "<IronSource App Key>", AD_UNIT.BANNER);

        // IronSource Banner
        initIronSourceBanner();
    }

    private void initIronSourceBanner() {
        ISBannerSize size = ISBannerSize.BANNER;
        IronSourceBannerLayout ironSourceBannerLayout = IronSource.createBanner(this, size);

        FrameLayout bannerFrameLayout = findViewById(R.id.bannerFrameLayout);
        FrameLayout.LayoutParams layoutParams = new FrameLayout.LayoutParams(FrameLayout.LayoutParams.MATCH_PARENT,
                FrameLayout.LayoutParams.MATCH_PARENT);
        bannerFrameLayout.addView(ironSourceBannerLayout, 0, layoutParams);

        IronSource.loadBanner(ironSourceBannerLayout);
    }

    @Override
    public void onImpressionSuccess(ImpressionData impressionData) {
        instance.eventAdImpressionIronSource(impressionData);
    }
}

```

## <a id="hyperbid"></a>HyperBid Impression Level Ad Revenue Integration

> *NOTE*, Please ensure you have the latest HyperBid Android SDK installed (\>= 5.7.78)

```
public class DemoActivity extends Activity implements HBBannerExListener {
    private TenjinSDK tenjinInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Initialize Tenjin
        this.tenjinInstance = TenjinSDK.getInstance(this, "<Tenjin API Key>");

        // Initialize HyperBid
        HBSDK.checkInit(this);
        HBSDK.setAppChannel(<HyperBid App Channel>);
        HBSDK.setAppSubChannel(<HyperBid App Sub Channel>);
        HBSDK.start(getApplicationContext(), <HyperBid App Id>, <HyperBid App Key>);

        // HyperBid Banner
        initHyperBidBanner();
    }

    private void initHyperBidBanner() {
        final FrameLayout frameLayout = findViewById(R.id.bannerFrameLayout);
        bannerView = new HBBannerView(this);
        bannerView.setPlacementId(<HyperBid Placement Banner Ad Id>);
        frameLayout.addView(bannerView);
        bannerView.setBannerAdListener(this);
        bannerView.loadAd();
    }

    @Override
    public void onBannerShow(HBAdInfo hbAdInfo) {
        instance.eventAdImpressionHyperBid(hbAdInfo);
    }
}

```

## <a id="admob"></a>AdMob Impression Level Ad Revenue Integration

> *NOTE*, Please ensure you have the latest AdMob Android SDK installed (\>= 20.5.0)

```
public class DemoActivity extends Activity {
    private TenjinSDK tenjinInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Initialize Tenjin
        this.tenjinInstance = TenjinSDK.getInstance(this, "<Tenjin API Key>");

        // Initialize AdMob
        MobileAds.initialize(this, initializationStatus -> initBanner());

        // AdMob Banner
        initAdMobBanner();
    }

    private void initAdMobBanner() {
        AdView adView = findViewById(R.id.adView);
        AdRequest adRequest = new AdRequest.Builder().build();
        adView.loadAd(adRequest);
        adView.setOnPaidEventListener(adValue -> instance.eventAdImpressionAdMob(adValue, adView));
    }
}

```

## <a id="topon"></a>TopOn Impression Level Ad Revenue Integration

> *NOTE*, Please ensure you have the latest TopOn Android SDK installed (\>= 5.7.99)

```
public class DemoActivity extends Activity implements ATBannerExListener {
    private TenjinSDK tenjinInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Initialize Tenjin
        this.tenjinInstance = TenjinSDK.getInstance(this, "<Tenjin API Key>");

        // Initialize TopOn
        ATSDK.integrationChecking(this);
        ATSDK.setChannel(<TopOn App Channel>);
        ATSDK.setSubChannel(<TopOn App Sub Channel>);
        ATSDK.init(getApplicationContext(), <TopOn App Id>, <TopOn App Key>);

        // TopOn Banner
        initTopOnBanner();
    }

    private void initTopOnBanner() 
        final FrameLayout frameLayout = findViewById(R.id.bannerFrameLayout);
        bannerView = new ATBannerView(this);
        bannerView.setPlacementId(<TopOn Placement Banner Ad Id>);
        frameLayout.addView(bannerView);
        bannerView.setBannerAdListener(this);
        bannerView.loadAd();
    }

    @Override
    public void onBannerShow(ATAdInfo atAdInfo) {
        instance.eventAdImpressionTopOn(atAdInfo);
    }
}

```

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
[16]:	#initialization
[17]:	#gdpr
[18]:	#purchase-events
[19]:	#custom-events
[20]:	#deferred-deeplink
[21]:	#server-to-server
[22]:	#subversion
[23]:	#ilrd
[24]:	#applovin
[25]:	#ironsource
[26]:	#hyperbid
[27]:	#admob
[28]:	#topon
[29]:	#testing
[30]:	https://github.com/tenjin/tenjin-android-sdk-demo
[31]:	https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.html#getAdvertisingIdInfo(android.content.Context)
[32]:	https://developers.google.com/android/reference/com/google/android/gms/ads/identifier/AdvertisingIdClient.Info.html#isLimitAdTrackingEnabled()
[33]:	http://www.msa-alliance.cn/col.jsp?id=120
[34]:	https://developer.android.com/reference/android/telephony/TelephonyManager#getImei()
[35]:	https://developer.android.com/google/play/installreferrer/index.html
[36]:	https://developer.android.com/reference/android/os/Build.VERSION.html#SDK_INT
[37]:	https://developer.android.com/reference/android/os/Build.html#DEVICE
[38]:	https://developer.android.com/reference/android/os/Build.html#MANUFACTURER
[39]:	https://developer.android.com/reference/android/os/Build.html#MODEL
[40]:	https://developer.android.com/reference/android/os/Build.html#BRAND
[41]:	https://developer.android.com/reference/android/os/Build.html#PRODUCT
[42]:	https://developer.android.com/reference/android/telephony/TelephonyManager.html#getSimOperatorName()
[43]:	https://developer.android.com/reference/android/net/ConnectivityManager.html#getActiveNetworkInfo()
[44]:	https://developer.android.com/reference/android/util/DisplayMetrics.html#widthPixels
[45]:	https://developer.android.com/reference/android/util/DisplayMetrics.html#heightPixels
[46]:	https://developer.android.com/reference/android/os/Build.VERSION.html#RELEASE
[47]:	https://developer.android.com/reference/android/os/Build.html
[48]:	https://developer.android.com/reference/java/util/Locale.html#getDefault()
[49]:	https://developer.android.com/reference/java/util/Locale.html#getDefault()
[50]:	https://developer.android.com/reference/java/util/TimeZone.html

[image-1]:	https://tenjin-instructions.s3.amazonaws.com/android_jar.png "studio"
[image-2]:	https://s3.amazonaws.com/tenjin-instructions/sdk_live_purchase_events_2.png
