Tenjin Android SDK
==================

Eclipse integration instructions:
---------------------------------

1. Download and unzip the .zip file from https://github.com/Ordinance/tenjin-android-sdk/archive/master.zip
3. Create a folder called `libs` in your project's root folder
4. Copy the `tenjin.jar` file to the `libs` folder
5. Right click on `tenjin.jar` and then select Build Path > Add to Build Path - this should create a folder called `Referenced Libraries` in your project
6. Get your `API_KEY` from a cfarm@tenjin.io
7. In your main Activity include the Tenjin SDK with `import com.tenjin.android.TenjinSDK;`
8. In the `onCreate` method add the following line of code: `TenjinSDK.getInstance(this, "[API_KEY]").connect();`

Here's an example of what the first/main activity should look like.

```
import com.tenjin.android.TenjinSDK;

public class TenjinDemo extends ActionBarActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tenjin_demo);
        TenjinSDK.getInstance(this, "X6SJQRU3UE3PXWJDEQB2S17GE7YZ3S80").connect();

    }
```