# Environment Setup

## For iOS

1. Open Xcode `$ npm run open-xcode`

2. Config Schema:

- Select Edit Schema...<br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-1.png" /><br /><br />
- Create new Schema with name: <b>App Dev</b><br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-2.png" /><br /><br />
- Config .env, copy this script: `echo ".env.development" > /tmp/envfile`
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-3.png" /><br /><br />
- Do the same with staging and production environments<br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-4.png" /><br />

3. Create file Config:

- Select New File...<br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-5.png" /><br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-6.png" /><br /><br />
- Copy this script: `#include? "tmp.xcconfig"`
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-7.png" /><br /><br />
- Apply Config:<br/>
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-8.png" /><br />

4. Create New Run Script Phase:<br />

- Copy this script: `"${SRCROOT}/../node_modules/react-native-config/ios/ReactNativeConfig/BuildXCConfig.rb" "${SRCROOT}/.." "${SRCROOT}/tmp.xcconfig"`<br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-9.png" /><br />

5. Config Builde Setiings:<br />

- Tab All and search with key: `preprocess`
- Set <b>Preprocess Info.plist File</b> -> <b>Yes</b>
- Set <b>Info.plist Preprocessor Prefix File</b> -> `${BUILD_DIR}/GeneratedInfoPlistDotEnv.h`
- Set <b>Info.Plist Other Preprocessor Flags</b> -> `-traditional`
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-10.png" /><br />

6. Config Info.plist:

- Set <b>Bundle display name</b> -> `RNC_APP_NAME`
- Set <b>Bundle version string (short)</b> -> `RNC_IOS_APP_VERSION_CODE`
- Set <b>Bundle version</b> -> `RNC_IOS_APP_BUILD_CODE`<br />
  <img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-11.png" /><br />

## For Android

1. Genarate keystore with this script and move the keystore file to ./android/app:

```bash
# Replace DEMO with the name you want
$ keytool -genkeypair -v -keystore DEMO.keystore -alias DEMO-alias -keyalg RSA -keysize 2048 -validity 10000
```

2. Copy this to file ./android/gradle.properties:

```
# infomation develop keystore
DEBUG_STORE_FILE=debug.keystore
DEBUG_KEY_ALIAS=androiddebugkey
DEBUG_STORE_PASSWORD=android
DEBUG_KEY_PASSWORD=android
# infomation staging keystore
STAGING_STORE_FILE=staging.keystore
STAGING_KEY_ALIAS=staging-alias
STAGING_STORE_PASSWORD=app@123
STAGING_KEY_PASSWORD=app@123
# infomation product keystore
PRODUCT_STORE_FILE=product.keystore
PRODUCT_KEY_ALIAS=staging-alias
PRODUCT_STORE_PASSWORD=app@123
PRODUCT_KEY_PASSWORD=app@123
```

> Replace value match with your keystore

3. Open ./android/app/build.gradle:

- Copy this to the top of the file:

```
project.ext.envConfigFiles = [
    dev: ".env.development",
    staging: ".env.staging",
    product: ".env.production",
    anothercustombuild: ".env",
]
```

<img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-12.png" /><br />

- Insert this after line `apply from: "../../node_modules/react-native/react.gradle"`

```
apply from: project(':react-native-config').projectDir.getPath() + "/dotenv.gradle"
```

<img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-13.png" /><br />

- Update `defaultConfig` like this:

```
defaultConfig {
    applicationId env.get("ANDROID_APP_ID")
    minSdkVersion rootProject.ext.minSdkVersion
    targetSdkVersion rootProject.ext.targetSdkVersion
    versionCode Integer.valueOf(env.get("ANDROID_APP_VERSION_CODE"))
    versionName env.get("ANDROID_APP_VERSION_NAME")
    resValue "string", "build_config_package", "{packageName}"
}
```

> Replace {packageName} with your app package name<br />
> Find {packageName} in ./android/app/src/main/AndroidManifest.xml

<img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-14.png" /><br />

- Update `signingConfigs` like this:

```
debug {
    storeFile file(DEBUG_STORE_FILE)
    storePassword DEBUG_STORE_PASSWORD
    keyAlias DEBUG_KEY_ALIAS
    keyPassword DEBUG_KEY_PASSWORD
}
develop {
    storeFile file(DEBUG_STORE_FILE)
    storePassword DEBUG_STORE_PASSWORD
    keyAlias DEBUG_KEY_ALIAS
    keyPassword DEBUG_KEY_PASSWORD
}
staging {
    storeFile file(STAGING_STORE_FILE)
    storePassword STAGING_STORE_PASSWORD
    keyAlias STAGING_KEY_ALIAS
    keyPassword STAGING_KEY_PASSWORD
}
product {
    storeFile file(PRODUCT_STORE_FILE)
    storePassword PRODUCT_STORE_PASSWORD
    keyAlias PRODUCT_KEY_ALIAS
    keyPassword PRODUCT_KEY_PASSWORD
}
```

- Insert this after :

```
flavorDimensions "enviroment"
productFlavors {
    develop {
        dimension "enviroment"
        resValue "string", "app_name", project.env.get("APP_NAME")
        signingConfig signingConfigs.develop
    }
    staging {
        dimension "enviroment"
        resValue "string", "app_name", project.env.get("APP_NAME")
        signingConfig signingConfigs.staging
    }
    product {
        dimension "enviroment"
        resValue "string", "app_name", project.env.get("APP_NAME")
        signingConfig signingConfigs.product
    }
}
```

<img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-15.png" /><br />

4. Go to ./android/app/src/main/res/values/strings.xml and remove all `<string />`

<img src="https://hieubeo0.github.io/static-file/react-native-guide/guide-16.png" /><br />

