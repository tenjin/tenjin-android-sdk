Please see our [Release Notes](https://github.com/Ordinance/tenjin-android-sdk/wiki) to see detailed version history.

For Unity-specific instructions, please visit https://github.com/Ordinance/tenjin-unity-sdk.

Tenjin Android SDK (v1.7.6) - Google Play and Amazon Store support
==================
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
1. Download the latest Android SDK from <a href="https://github.com/Ordinance/tenjin-android-sdk/releases">here.</a>
2. Add the tenjin.jar into your Android Studio project by selecting New > Module.
3. In the New Module dialog, select the Import .JAR or .AAR Package option and click on Next.
![AndroidStudio](https://tenjin-instructions.s3.amazonaws.com/android_studio_import.png "studio")
4. Select the tenjin.jar file click on Finish.
5. If you haven’t already installed the <a href="https://developers.google.com/android/guides/setup">Google Play Services</a>, add it to our build.gradle file.

```java
dependencies {
  compile 'com.google.android.gms:play-services:10.0.1'
}
```

### Eclipse Integration
1. Download the latest Android SDK from <a href="https://github.com/Ordinance/tenjin-android-sdk/releases">here.</a>
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
TenjinSDK.getInstance(this, "[API_KEY]").connect();
```

Or similarly here's an example of what the `Activity` integration(s) should look like:

```java
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    //...other callbacks are here

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = <API_KEY>; //You can potentially set this as a global variable too
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);
        instance.connect();

        //Your other code...
        ...

    }
```
3b. Alternate initialization with Facebook (DO NOT USE 3a and 3b. You need to use only one.)
```java
import com.facebook.applinks.AppLinkData;

import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    //...other callbacks are here

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = <API_KEY>; //You can potentially set this as a global variable too
        final TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

        AppLinkData.fetchDeferredAppLinkData(this,
                new AppLinkData.CompletionHandler() {
                    @Override
                    public void onDeferredAppLinkDataFetched(AppLinkData appLinkData) {
                        if (appLinkData != null) {
                            String appLinkUri = appLinkData.getTargetUri().toString();
                            instance.connect(appLinkUri);
                        } else {
                            instance.connect();
                        }
                    }
                }
        );

        //Your other code...
        ...

    }
```

Tenjin purchase event instructions:
-----
To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. There are two ways to send `transaction` events to Tenjin.

1. Validate receipts
Tenjin can validate `transaction` receipts for you. Visit your app on the dashboard (Apps -> Your Android App -> Edit) and enter your Public Key that can be found in your Google Play dashboard under "Services & APIs".

![Dashboard](https://s3.amazonaws.com/tenjin-instructions/android_pk.png "dashboard")

After entering your Public Key into the Tenjin dashboard for your app, you can use the Tenjin SDK method below:

`public void transaction(String productId, String currencyCode, int quantity, double unitPrice, String purchaseData, String dataSignature)`

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
            TenjinSDK.getInstance(this, API_KEY).transaction(sku, "USD", 1, 3.99, purchaseData, dataSignature)
          }
          catch (JSONException e) {
             alert("Failed to parse purchase data.");
             e.printStackTrace();
          }
      }
   }
}
```

2. Pass the transaction manually (usually this is necessary if purchases are not handled by Google Play)
To send `transaction` events, you must provide the `productId`, `currencyCode`, `quantity`, and `unitPrice` of the user's transaction following method signature below:

`public void transaction(String productId, String currencyCode, int quantity, double unitPrice)`.

Here's an example of how this can be implemented at the time of purchase:
```java
//The developer's own method for completing a transaction that happened in app
public void completeTransaction(String productId, String currencyCode, int quantity, double unitPrice){
  ...
  //Call the Tenjin SDK with the context and the API_KEY
  TenjinSDK.getInstance(this, API_KEY).transaction(productId, currencyCode, quantity, unitPrice);
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
NOTE: **DO NOT SEND CUSTOM EVENTS BEFORE THE INITIALIZATION** event (above). The initialization event must come before any custom events are sent. 

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

public class TenjinDemo extends ActionBarActivity {

    //...other callbacks are here

    @Override
    public void onResume() {
        //standard code
        super.onResume()

        //Integrate TenjinSDK connect call
        String apiKey = <API_KEY>; //You can potentially set this as a global variable too
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
        String apiKey = <API_KEY>; //You can potentially set this as a global variable too
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
