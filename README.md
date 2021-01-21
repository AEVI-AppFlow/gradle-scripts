# Gradle scripts

A set of re-usable Gradle scripts for Android apps and libraries.

## Importing

Update your root `build.gradle` as per below.

1. Add sdk releases as a buildscript repository
```
        maven {
            url "s3://sdk-releases.aevi.com/maven2"
            credentials(AwsCredentials) {
                accessKey aws_accessid
                secretKey aws_accesskey
            }
        }
```

2. Add gradle-scripts as a buildscript dependency
```
classpath 'com.aevi.sdk.build:gradle-scripts:<version>'
```

3. Add a lambda at root of file
```
ext.gradleScript = { path ->
    return rootProject.buildscript.classLoader.getResource(path).toURI()
}
```

4. Apply root scripts
```
apply from: gradleScript('root/common-tasks.gradle')
apply from: gradleScript('root/repositories.gradle')
```

5. In your app/lib modules build.gradle
Define required variables
```
ext.applicationId = "com.aevi.XXX"
ext.applicationName = "YourAppName"
ext.applicationTargetSdkVersion = <target>
ext.applicationMinSdkVersion = <min>
```

6. Apply relevant scripts
```
apply from: gradleScript('android/artifacts.gradle')
apply from: gradleScript('android/versioning.gradle')
apply from: gradleScript('android/basic-android.gradle')
apply from: gradleScript('android/signing-utils.gradle')
```

7. Define/override Android settings as required
- Build types
- Flavors
- Etc

## Signing

This script provides some predefined signing configurations that can be used into your build files.

```groovy
applyScript("signingUtils")
```
Signing information is still expected to be passed through environment variables. For a given signing configuration, the following 2 environement variables are expected to be set:
* `[name]_KEYSTORE`: the base64 encoded keystore to use
* `[name]_KEYSTORE_PASSWORD`: the keystore password

Note that the key alias being used is expected to be `key` and the key password is expected to be the same than the keystore password.

This script has the following predefined signing configurations you can use:
* __`signingUtils.albert`__ relying on `ALBERT_APP_xxx`
* __`signingUtils.albertPlatform`__ relying on `ALBERT_PLATFORM_xxx`
* __`signingUtils.aosp`__ relying on `AOSP_PLATFORM_xxx`
* __`signingUtils.sdkDev`__ relying on `SDK_DEV_xxx`

For other signing configurations, until they eventually get added to the list of prebuilt ones, you can use `signingUtils.config([name])`, assuming the environment variables: `[name]_KEYSTORE` and `[name]_KEYSTORE_PASSWORD` have been defined.

Signing configurations can be used in build files as shown below
```groovy
release {
    signingConfig signingUtils?.sdkDev
}
```

## License

Copyright (c) 2020 AEVI International GmbH. All rights reserved
Unauthorized copying or distribution of this project, via any medium is strictly prohibited
Proprietary and confidential
