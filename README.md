# Gradle scripts

A set of re-usable Gradle scripts for Android apps and libraries.

## Importing

Update your root `build.gradle` as per below.

1. Add sdk releases as a buildscript repository
```groovy
   maven {
           name = "github-aevi-appflow"
           url = uri("https://maven.pkg.github.com/AEVI-AppFlow/gradle-scripts")
           credentials {
               username = System.getenv("GITHUB_ACTOR") ?: gh_username
               password = System.getenv("GITHUB_TOKEN") ?: gh_token
           }
   }
```

2. Add gradle-scripts as a buildscript dependency
```groovy
classpath 'com.aevi.sdk.build:gradle-scripts:<version>'
```

3. Add a lambda at root of file
```groovy
ext.gradleScript = { path ->
    return rootProject.buildscript.classLoader.getResource(path).toURI()
}
```
If you are using kts then you will need to add `gradleScriptLoader` at top of build.gradle.kts file
(don't forget to import `import java.net.URI` otherwise you will get error)
```kotlin
   val gradleScriptLoader by extra(
       fun(string: String): URI? {
           var data: URI? = null
           project.buildscript {
               data = classLoader.getResource(string).toURI()
           }
           return data
       }
   )
```
and then you can call
```kotlin
   apply(from = gradleScriptLoader("root/common-tasks.gradle"))
```

4. Apply root scripts
```groovy
   apply from: gradleScript('root/common-tasks.gradle')
```

5. In your app/lib modules build.gradle
   Define required variables
```groovy
   ext.applicationId = "com.aevi.XXX"
   ext.applicationName = "YourAppName"
   ext.applicationTargetSdkVersion = <target>
   ext.applicationMinSdkVersion = <min>
```

6. Apply relevant scripts
```groovy
   apply from: gradleScript('android/artifacts.gradle')
   apply from: gradleScript('android/versioning.gradle')
   apply from: gradleScript('android/basic-android.gradle')
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
