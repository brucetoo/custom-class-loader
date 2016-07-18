Port of the work in the [following blog](http://android-developers.blogspot.jp/2011/07/custom-class-loading-in-dalvik.html) 
to the new Gradle based Android Studio build system, as per [this thread on StackOverflow](http://stackoverflow.com/questions/18174022/custom-class-loading-in-dalvik-with-gradle-android-new-build-system/27241083#27241083)

As the Android Studio Gradle plugin now provides [native multidex support](https://developer.android.com/tools/building/multidex.html),
which effectively solves the Android 65k method limit, the main motivation for using custom class loading at runtime is now 
extensibility. In my particular case, I'm trying to make a [plugin framework for AnkiDroid](http://stackoverflow.com/questions/10239596/plugins-architecture-for-an-android-app).

Therefore the main focus of this version of the project is on building the secondary jar file in a clean and modular manner,
which makes it easy to update the main project without having to update the plugins. The main apk is in the app module, and the library which shows the toast is in the libraries/lib1 module with its own namespace `com.example.toastlib`



##STEP

  1.Define interface in `app` main module,and implement them in `libraries:lib1` module wait to generate dex file
  
  2.Add `':libraries:lib1'` in `settings.gradle` let libraries become a module
  
  3.Change `app.gradle`  from `apply plugin: 'com.android.application'` to `apply plugin: 'com.android.library'`
  to be dependency by `libraries:lib1` (line `35`)
  
  4.Config `local.properties` in `libraries:lib1.gradle` in line `63` to get android SDK
 
  5.Run in command line `gradlew assembleExternalJar` to get dex file,file name can be configured in `gradle.properties`
  by `PLUGIN_NAMESPACE` ,then dex file inside jar archive can be found in `/libraries/lib1/build/outputs/PLUGIN_NAMESPACE.jar`
  
  6.Copy `PLUGIN_NAMESPACE.jar` into `app' assets file`
  
  7.Change `app.gradle`  from `apply plugin: 'com.android.library'` to `apply plugin: 'com.android.application'` 
  and remove `':libraries:lib1'` from settings.gradle

