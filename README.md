# Notificare Maven Repository

A repository with artifacts for developing apps with the Notificare SDK

## Notificare Push SDK for Android

To be used in Android Studio, add the following to your app's Gradle config:

```groovy
allprojects {
    repositories {
        mavenCentral()
        maven {
            url "https://github.com/Notificare/notificare-mvn-repo/raw/master/releases"
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 're.notifica:notificare-push-lib-android:1.4.2'
}
```

