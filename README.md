# Gradle scripts

A set of utility scripts to help with the publishing and signing of artifacts.

## Usage
Until this project gets eventually turned into a proper Gradle plugin the recommended way to use the scripts of this repository is via git submodules.

From your project directory, start by checking out the latest version of this repository as a submodule:
```shell
git submodule add git@github.com:Aevi-UK/gradle-scripts.git
```

The scripts should now be part of your project and located in the `gradle-script` directory. In the main gradle file of your project, we recommend adding the following:
```groovy
subprojects {
    ext.applyScript = { String script ->
        try {
            apply from: new File(rootDir, "gradle-scripts/${script}.gradle").path
        } catch (Exception e) {
            ext."${script}" = null
            project.logger.log(LogLevel.WARN, e.message)
        }
    }
}
```
This is a utility function which will allow you to conditionally load scripts from this repository only if they exist, therefore preventing the need to necessarily have to check them out on developer machines or to have to publicly share them when used in public repositories. The idea being a project should always be compileable with our without them.

Since git won't checkout submodules by default you'll need to make sure to checkout your project sources with the `--recurse-submodules` option from your CI script:
```shell
git clone --recurse-submodules
```

## Publishing
This script will automatically generate gradle tasks for the publication of the libraries and APKs of your project

```groovy
applyScript("publishingUtils")
```
This script relies on the following environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` in order to generate publications for the following repositories:
* _release_: s3://sdk-releases.aevi.com/maven2
* _qa_: s3://sdk-qa.aevi.com/maven2
* _snapshot_: s3://sdk-snapshots.aevi.com/maven2

It also has Github package publishing built-in support, assuming the build is being run from Github actions: 
```shell
./gradlew app:assembleRelease
./gradlew app:publishReleasePublicationToGithub
```

Publications details can be configured from your build script:
```groovy
publishingUtils?.publication { variant ->
    groupId "my.publication.group"
    artifactId "my-app-$variant"
    version "1.0.0"
}
```

## Signing

This script provides some predefined signing configurations that can be used into your build files.

```groovy
applyScript("signingUtils")
```
Signing information is still expected to be passed through environment variables. For a given signing configuration, the following 2 environement variables are expected to be set:
* `[name]_KEYSTORE`: the base64 encoded keystore to use
* `[name]_KEYSTORE_PASSWORD`: the keystore password

Note that the key alias being used is expected to be `key` and the key password is expected to be the same than the keystore password.

Alternatively, if a macthing environment variable can't be found, the script will look up for a lower case project property instead (ie: `[name]_keystore` and `[name]_keystore_Password`). This gives the ability to define them in a gradle property file instead.


This script has the following predefined signing configurations you can use:
* __`signingUtils.albert()`__ relying on `ALBERT_APP_xxx`
* __`signingUtils.albertPlatform()`__ relying on `ALBERT_PLATFORM_xxx`
* __`signingUtils.aosp()`__ relying on `AOSP_PLATFORM_xxx`
* __`signingUtils.sdkDev()`__ relying on `SDK_DEV_xxx`

For other signing configurations, until they eventually get added to the list of prebuilt ones, you can use `signingUtils.config([name])`, assuming the environment variables: `[name]_KEYSTORE` and `[name]_KEYSTORE_PASSWORD` have been defined.

Signing configurations can be used in build files as shown below
```groovy
release {
    signingConfig signingUtils?.sdkDev()
}
```

## License

Copyright (c) 2020 AEVI International GmbH. All rights reserved
Unauthorized copying or distribution of this project, via any medium is strictly prohibited
Proprietary and confidential