GoogleAnalyticsBridge
=============
**Google Analytics Bridge** is built to provide an easy interface to the native Google Analytics libraries on both **iOS** and **Android**.

## Installation iOS

1. `npm install --save react-native-google-analytics-bridge`
2. In XCode, right click the Libraries folder under your project ➜ `Add Files to <your project>`.
3. Go to `node_modules` ➜ `react-native-google-analytics-bridge` and add the `RCTGoogleAnalyticsBridge.xcodeproj` file.
4. Add libRCTGoogleAnalyticsBridge.a to your project properties ➜ "Build Phases" ➜ "Link Binary With Libraries"
5. Next you will have to link a few more SDK framework/libraries (if you do not already have them linked.) Under the same "Link Binary With Libraries", click the + and add the following:
  1. AdSupport.framework
  2. CoreData.framework
  3. SystemConfiguration.framework
  4. libz.tdb
  5. libsqlite3.0.tdb
6. Under your project properties ➜ "Info", add a new line with the following:
  1. Key: GAITrackingId
  2. Type: String
  3. Value: UA-12345-1 (in other words, your own tracking id).
7. See own guide for tracking from Javascript.

## Installation Android
1. `npm install --save react-native-google-analytics-bridge`
2. Add the following in `android/setting.gradle`

  ```gradle
  ...
  include ':GoogleAnalyticsBridge', ':app'
  project(':GoogleAnalyticsBridge').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-google-analytics-bridge/src/android')
  ```

3. And the following in `android/app/build.gradle`

  ```gradle
  ...
  dependencies {
      ...
      compile project(':GoogleAnalyticsBridge')
  }
  ```

4. Register package in `MainActivity.java`

  ```java
  // Step 1; import package:
  import com.idehub.GoogleAnalyticsBridge.GoogleAnalyticsBridgePackage;

  public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {
      ...

      @Override
      protected void onCreate(Bundle savedInstanceState) {
          ...

          mReactInstanceManager = ReactInstanceManager.builder()
                  .setApplication(getApplication())
                  .setBundleAssetName("index.android.bundle")
                  .setJSMainModuleName("index.android")
                  .addPackage(new MainReactPackage())
                  // Step 2; register package, with your GA tracking id:
                  .addPackage(new GoogleAnalyticsBridgePackage("UA-12345-1"))
                  .setUseDeveloperSupport(BuildConfig.DEBUG)
                  .setInitialLifecycleState(LifecycleState.RESUMED)
                  .build();

          ...
      }
      ...
  ```
5. Make sure your `AndroidManifest.xml` has `INTERNET` and `ACCESS_NETWORK_STATE` permissions
  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.example">

      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
      ...
  ```

## JavaScript example
```javascript
const GoogleAnalytics = require('react-native-google-analytics-bridge');

GoogleAnalytics.trackScreenView('Home');
GoogleAnalytics.trackEvent('testcategory', 'testaction');
```

## Javascript API
At the moment the implementation only exposes two methods:
### trackScreenView(screenName)
This method only takes one parameter, the name of the current screen view. E. g. `GoogleAnalytics.trackScreenView('Home')`.
**Important**: Calling this will also set the "current view" in this session. So events tracked will be tagged as having occured on the current view, `Home` in this example. So it is important to keep this up-to-date.
### trackEvent(category, action, optionalValues = {})
This method takes takes two required parameters, the event category and action. The optionalValues can be label, and value. e. g. `{ label: "v1.0.3", value: 22 }`.
**Note**: Label is a string, while value must be a number.
