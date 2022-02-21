# Gradle scripts

A set of re-usable Gradle scripts for Android apps and libraries.

## Importing

Update your root `build.gradle` as per below.

1. Add sdk releases as a buildscript repository
```
   maven {
           name = "github-aevi-uk"
           url = uri("https://maven.pkg.github.com/aevi-uk/gradle-scripts")
           credentials {
               username = System.getenv("GITHUB_ACTOR") ?: gh_username
               password = System.getenv("GITHUB_TOKEN") ?: gh_token
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
...
```

7. Define/override Android settings as required
- Build types
- Flavors
- Etc

## Versioning Android
```groovy
apply from: gradleScript('android/versioning.gradle')
```

## Versioning Libraries
```groovy
apply from: gradleScript('lib/versioning.gradle')
```
## Publishing
This script will automatically generate gradle tasks for the publication of the libraries and APKs of your project

```groovy
apply from: gradleScript('publish/publishing-utils.gradle')

publishingUtils?.publication {
    groupId '...'
    artifactId project.name
    version libraryVersion
}
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

## Signing

In order to get automatic signing with the default SDK key, add the below to the relevant module gradle file
```
apply from: gradleScript('android/sdk-dev-signing-config.gradle')
apply from: gradleScript('android/common-build-types-v2.gradle')
```

The Github Actions workflow `env` block must have:
```
SDK_DEV_KEYSTORE: ${{ secrets.SDK_DEV_KEYSTORE }}
SDK_DEV_KEYSTORE_PASSWORD: ${{ secrets.SDK_DEV_KEYSTORE_PASSWORD }}
```

## License

Copyright (c) 2021 AEVI International GmbH. All rights reserved
Unauthorized copying or distribution of this project, via any medium is strictly prohibited
Proprietary and confidential
