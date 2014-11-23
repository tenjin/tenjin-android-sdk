Tenjin Android SDK
==================
Manifest requirements:
----

- Include `INTERNET` permissions within the manifest tags
- Include Google Play Services within the application tags

```
<manifest>
  ...
  <application ...>
    <meta-data android:name="com.google.android.gms.version"
           android:value="@integer/google_play_services_version" />
  </application>
  ...
  <uses-permission android:name="android.permission.INTERNET"></uses-permission>
  ...
</manifest>
```

Setup and initialization instructions (Eclipse):
---------------------------------

1. Download and unzip the latest Android SDK from releases (https://github.com/Ordinance/tenjin-android-sdk/releases)
3. Create a folder called `libs` in your project's root folder (if there isn't one already)
4. Copy the `tenjin.jar` file to the `libs` folder
5. Right click on `tenjin.jar` and then select Build Path > Add to Build Path - this should create a folder called `Referenced Libraries` in your project
7. Install Google's "Android Support Repository", "Android Support Library", "Google Play Services" and "Google Repository" SDKs from the SDK Manager (http://developer.android.com/google/play-services/setup.html)
8. Get your `API_KEY` from https://tenjin.io/dashboard/organizations
9. In your main Activity include the Tenjin SDK with `import com.tenjin.android.TenjinSDK;`
10. For each `onResume` method of every `Activity` add the following line of code: `TenjinSDK.getInstance(this, "[API_KEY]").connect();`

Or similarly here's an example of what the `Activity` integrations should look like:

```
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
```
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
There are two methods that you can use to pass custom events: `eventWithName(String name)` and `eventWithNameAndValue(String name, String value)`.

You can use these to pass Tenjin custom interactions with your app to tie this to user level cost from each acquisition source that you use through Tenjin's platform. Here are some examples of usage:

```
String apiKey = <API_KEY>;
TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);

//Integrate a custom event with a distinct name - ie. swiping right on the screen
instance.eventWithName("swipe_right");

//Integrate a custom event with a distinct name and value - ie. awarding user with 100 virtual coins
instance.eventWithNameAndValue("awarded_virtual_coins", "100");
```
