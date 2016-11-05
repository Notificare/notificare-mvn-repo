# Notificare Maven Repository

A repository with artifacts for developing apps with the Notificare SDK

## Notificare Push SDK for Android

### BREAKING CHANGES in 1.9.0 !!

See UPGRADE.md for details

### Setup

To be used in Android Studio, add the following to your app's Gradle config:

```groovy
    repositories {
        jcenter()
        maven {
            url "https://github.com/Notificare/notificare-mvn-repo/raw/master/releases"
        }
    }

    dependencies {
        compile group: 're.notifica:notificare-push-lib-android:1.9.0'
    }
```

