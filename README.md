# Notificare Maven Repository

A repository with artifacts for developing apps with the Notificare SDK

## Notificare Push SDK for Android

### BREAKING CHANGE in 2.0.0 !!

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
        implementation 're.notifica:notificare-core:2.0.1'
        implementation 're.notifica:notificare-location:2.0.1'
    }
```

