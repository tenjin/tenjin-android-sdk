Tenjin Android SDK (v1.1.1)
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
  ...
</manifest>
```

Setup and initialization instructions (Eclipse):
---------------------------------

#####1. Download the latest Android SDK from <a href="https://github.com/Ordinance/tenjin-android-sdk/releases">here.</a>
#####2. Create a folder `libs` in your project's root folder.
#####3. Copy the `tenjin.jar` file to the `libs` folder.
#####4. Right click on `tenjin.jar` and then select `Build Path` -> `Add to Build Path` 
- This should create a folder called `Referenced Libraries` in your project

#####5. Install Google's `Android Support Repository`, `Android Support Library`, `Google Play Services` and `Google Repository` SDKs from the SDK Manager. Google outlines how to best <a href="http://developer.android.com/google/play-services/setup.html">configure this</a> if you haven't already.
#####6. Get your `API_KEY` from your <a href="https://tenjin.io/dashboard/organizations">Tenjin Organization tab.</a>
#####7. In your main Activity include the Tenjin SDK with `import com.tenjin.android.TenjinSDK;`
#####8. For each `onResume` method of every `Activity` add the following line of code: 
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

Tenjin purchase event instructions:
-----
To understand user revenue and purchase behavior, developers can send `transaction` events to Tenjin. To send `transaction` events, you must provide the `productId`, `currencyCode`, `quantity`, and `unitPrice` of the user's transaction following method signature below:

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
