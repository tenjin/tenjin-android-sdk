Tenjin Android SDK
==================

Eclipse integration instructions:
---------------------------------

1. Download and unzip the .zip file from https://github.com/Ordinance/tenjin-android-sdk/archive/master.zip
3. Create a folder called `libs` in your project's root folder
4. Copy the `tenjin.jar` file to the `libs` folder
5. Right click on `tenjin.jar` and then select Build Path > Add to Build Path - this should create a folder called `Referenced Libraries` in your project
7. Install Google's "Android Support Repository", "Android Support Library", "Google Play Services" and "Google Repository" SDKs from the SDK Manager (http://developer.android.com/google/play-services/setup.html)
8. Get your `API_KEY` from https://tenjin.io/dashboard/organizations
9. In your main Activity include the Tenjin SDK with `import com.tenjin.android.TenjinSDK;`
10. In the `onCreate` method add the following line of code: `TenjinSDK.getInstance(this, "[API_KEY]").connect();`

Here's an example of what the first/main activity should look like.

```
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tenjin_demo);
        //variable for Tenjin API_KEY and initialize the TenjinSDK
        String apiKey = <API_KEY>;
        TenjinSDK instance = TenjinSDK.getInstance(this, apiKey);
        instance.connect();

        //custom events with and without values - examples for "swipe_right" and "revenue"
        instance.eventWithName("swipe_right");
        instance.eventWithNameAndValue("revenue", "0.99");

    }
```

Manifest requirements:
- Include INTERNET permissions within the manifest tags
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