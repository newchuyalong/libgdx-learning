* [Desktop (LWJGL)](#desktop-lwjgl)
* [Android](#android)
  - [Fragment based libgdx](#fragment-based-libgdx)
  - [Live Wallpapers](#live-wallpapers)
  - [Daydreams](#daydreams)
* [iOS Xamarin (Deprecated!)](#ios-xamarin-deprecated)
* [HTML5/GWT](#html5gwt)


For each target platform, a starter class has to be written. This class instantiates a back-end specific `Application` implementation and the `ApplicationListener` that implements the application logic. The starter classes are platform dependent, let's have a look at how to instantiate and configure these for each back-end.

This article assumes you have followed the instruction in [[Project Setup, Running & Debugging]] and have imported the generated core, desktop, Android and HTML5 projects into Eclipse.

# Desktop (LWJGL) #
Opening the `Main.java` class in `my-gdx-game` shows the following:

```java
package com.me.mygdxgame;

import com.badlogic.gdx.backends.lwjgl.LwjglApplication;
import com.badlogic.gdx.backends.lwjgl.LwjglApplicationConfiguration;

public class Main {
   public static void main(String[] args) {
      LwjglApplicationConfiguration cfg = new LwjglApplicationConfiguration();
      cfg.title = "my-gdx-game";
      cfg.useGL30 = false;
      cfg.width = 480;
      cfg.height = 320;
		
      new LwjglApplication(new MyGdxGame(), cfg);
   }
}
```

First an [LwjglApplicationConfiguration](https://github.com/libgdx/libgdx/tree/master/backends/gdx-backend-lwjgl/src/com/badlogic/gdx/backends/lwjgl/LwjglApplicationConfiguration.java) is instantiated. This class lets one specify various configuration settings, such as the initial screen resolution, whether to use OpenGL ES 1.x or 2.0 and so on. Refer to the Javadocs of this class for more information.

Once the configuration object is set, an `LwjglApplication` is instantiated. The `MyGdxGame()` class is the ApplicationListener implementing the game logic. 

From there on a window is created and the ApplicationListener is invoked as described in [[The Life-Cycle]]

# Android #
Android applications do not use a `main()` method as the entry-point, but instead require an Activity. Open the `MainActivity.java` class in the `my-gdx-game-android` project:

```java
package com.me.mygdxgame;

import android.os.Bundle;

import com.badlogic.gdx.backends.android.AndroidApplication;
import com.badlogic.gdx.backends.android.AndroidApplicationConfiguration;

public class MainActivity extends AndroidApplication {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        AndroidApplicationConfiguration cfg = new AndroidApplicationConfiguration();
        cfg.useGL20 = false;
        
        initialize(new MyGdxGame(), cfg);
    }
}
```

The main entry-point method is the Activity's `onCreate()` method. Note that `MainActivity` derives from `AndroidApplication`, which itself derives from `Activity`. As in the desktop starter class, a configuration instance is created ([AndroidApplicationConfiguration](https://github.com/libgdx/libgdx/tree/master/backends/gdx-backend-android/src/com/badlogic/gdx/backends/android/AndroidApplicationConfiguration.java)). Once configured, the `AndroidApplication.initialize()` method is called, passing in the `ApplicationListener`, `MyGdxGame`)\ as well as the configuration. Refer to the [AndroidApplicationConfiguration Javadocs](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/backends/android/AndroidApplicationConfiguration.html) for more information on what configuration settings are available.

Android applications can have multiple activities. Libgdx games should usually only consist of a single activity. Different screens of the game are implemented within libgdx, not as separate activities. The reason for this is that creating a new `Activity` also implies creating a new OpenGL context, which is time consuming and also means that all graphical resources have to be reloaded.

## Fragment based libgdx ##

The Android SDK has introduced an API to create controllers for specific parts of a screen, that can be easily re-used on multiple screens. This API is called the [Fragments API](http://developer.android.com/guide/components/fragments.html). Libgdx can now also be used as a part of a larger screen, inside a Fragment. To create a Libgdx fragment, subclass `AndroidFragmentApplication` and implement the `onCreateView()` with the following initialization:
```@Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        return initializeForView(new MyGdxGame());
    }
```

That code depends on some other changes to the -android project:
* 1. [Add Android V4 Support Library to the -android project and its build path](https://developer.android.com/tools/support-library/setup.html#libs-without-res) if you haven't already added it. This is needed in order to Extend FragmentActivity later
* 2. Change AndroidLauncher activity to extend FragmentActivity, not AndroidApplication
* 3. Implement AndroidFragmentApplication.Callbacks on the AndroidLauncher activity
* 4. Create a Class that extends AndroidFragmentApplication which is the Fragment implementation for Libgdx.
* 5. Add the initializeForView() code in the Fragment's onCreateView method.
* 6. Finally, replace the AndroidLauncher activity content with the Libgdx Fragment.

For example:
```
// 2. Change AndroidLauncher activity to extend FragmentActivity, not AndroidApplication
// 3. Implement AndroidFragmentApplication.Callbacks on the AndroidLauncher activity
public class AndroidLauncher extends FragmentActivity implements AndroidFragmentApplication.Callbacks
{
   @Override
   protected void onCreate (Bundle savedInstanceState)
   {
      super.onCreate(savedInstanceState);

      // 6. Finally, replace the AndroidLauncher activity content with the Libgdx Fragment.
      GameFragment fragment = new GameFragment();
      FragmentTransaction trans = getSupportFragmentManager().beginTransaction();
      trans.replace(android.R.id.content, fragment);
      trans.commit();
   }

   // 4. Create a Class that extends AndroidFragmentApplication which is the Fragment implementation for Libgdx.
   private class GameFragment extends AndroidFragmentApplication
   {
      // 5. Add the initializeForView() code in the Fragment's onCreateView method.
      @Override
      public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
      {  return initializeForView(new MyGdxGame());   }
   }


   @Override
   public void exit() {}
}
```

### The AndroidManifest.xml File ###
Besides the `AndroidApplicationConfiguration`, an Android application is also configured via the `AndroidManifest.xml` file, found in the root directory of the Android project. This might look something like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.me.mygdxgame"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk android:minSdkVersion="5" android:targetSdkVersion="15" />

    <application
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:screenOrientation="landscape"
            android:configChanges="keyboard|keyboardHidden|orientation">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

#### Target Sdk Version ####
It is crucial to specify the `targetSdkVersion` to a value >= 6 if the application is supposed to run on Android versions higher than 1.5. If this attribute is not set, higher Android versions will run the application in legacy mode. The resolution of the drawing area will be smaller than the actual screen resolution, an undesirable circumstance.

#### Screen Orientation & Configuration Changes ####
In addition to the targetSdkVersion, the `screenOrientation` and `configChanges` attributes of the activity element should always be set.

The `screenOrientation` attribute specifies a fixed orientation for the application. One may omit this if the application can work with both landscape and portrait mode.

The `configChanges` attribute is *crucial* and should always have the values shown above. Omitting this attribute means that the application will be restarted every time a physical keyboard is slid out/in or if the orientation of the device changes. If the `screenOrientation` attribute is omitted, a libgdx application will receive calls to `ApplicationListener.resize()` to indicate the orientation change. API clients can then re-layout the application accordingly.

#### Permissions ####
If an application needs to be able to write to the external storage of a device (e.g. SD-card), needs internet access, uses the vibrator or wants to record audio, the following permissions need to be added to the `AndroidManifest.xml` file:

```xml
	<uses-permission android:name="android.permission.RECORD_AUDIO"/>
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
	<uses-permission android:name="android.permission.VIBRATE"/>
```

Users are generally suspicious of applications with many permissions, so chose these wisely. 

For wake locking to work, `AndroidApplicationConfiguration.useWakeLock` needs to be set to true. 

If a game doesn't need accelerometer or compass access it is advised to disable these by setting the `useAccelerometer` and `useCompass` fields of `AndroidApplicationConfiguration` to false. 

Please refer to the [Android Developer's Guide](http://developer.android.com/guide/index.html) for more information on how to set other attributes like icons for your application.

## Live Wallpapers ##
Libgdx features a simple to use way to create [Live Wallpapers](http://android-developers.blogspot.co.at/2010/02/live-wallpapers.html) for Android. The starter class for a live wallpaper is called `AndroidLiveWallpaperService`, here's an example:

```java
package com.mypackage;

// imports snipped for brevity 

public class LiveWallpaper extends AndroidLiveWallpaperService {
	@Override
	public ApplicationListener createListener () {
		return new MyApplicationListener();
	}

	@Override
	public AndroidApplicationConfiguration createConfig () {
		return new AndroidApplicationConfiguration();
	}

	@Override
	public void offsetChange (ApplicationListener listener, float xOffset, float yOffset, float xOffsetStep, float yOffsetStep,
		int xPixelOffset, int yPixelOffset) {
		Gdx.app.log("LiveWallpaper", "offset changed: " + xOffset + ", " + yOffset);
	}
}
```

The methods `createListener()` and `createConfig()` will be called when your live wallpaper is shown in the picker or when it is created to be displayed on the home screen.

The `offsetChange()` method is scaled when the user swipes through screens on the home screen and tells you by how much the screen is offset from the center screen. This method will be called on the rendering thread, so you don't have to synchronize anything.

In addition to a starter class, you also have to create an XML file describing your wallpaper. Let's call that livewallpaper.xml. Create a folder called `xml/` in your Android project's `res/` folder and put the file in there (`res/xml/livewallpaper.xml`). Here's what to put into that file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper
       xmlns:android="http://schemas.android.com/apk/res/android"  
       android:thumbnail="@drawable/ic_launcher"
       android:description="@string/description"
       android:settingsActivity="com.mypackage.LivewallpaperSettings"/>
```

This defines the thumbnail to be displayed for your LWP in the picker, the description and an Activity that will be displayed when the user hits "Settings" in the LWP picker. This should be just a standard Activity that has a few widgets to change settings such as the background color and similar things. You can store those settings in SharedPreferences and load them later in your LWPs ApplicationListener via `Gdx.app.getPreferences()`.

Finally, you'll need to add things to your `AndroidManifest.xml` files. Here's an example for an LWP with a simple settings Activity:

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.mypackage"
      android:versionCode="1"
      android:versionName="1.0"
      android:installLocation="preferExternal">
	<uses-sdk android:minSdkVersion="7" android:targetSdkVersion="14"/>	
	<uses-feature android:name="android.software.live_wallpaper" />
		
	<application android:icon="@drawable/icon" android:label="@string/app_name">
		<activity android:name=".LivewallpaperSettings" 
				  android:label="Livewallpaper Settings"/>
		
		<service android:name=".LiveWallpaper"
            android:label="@string/app_name"
            android:icon="@drawable/icon"
            android:permission="android.permission.BIND_WALLPAPER">
            <intent-filter>
                <action android:name="android.service.wallpaper.WallpaperService" />
            </intent-filter>
            <meta-data android:name="android.service.wallpaper"
                android:resource="@xml/livewallpaper" />
        </service>				  	
	</application>
</manifest> 
```

The manifest defines:

  * it uses the live wallpaper feature, see `<uses-feature>`.
  * a permission to be allowed to bind the wallpaper, see `android:permission`
  * the settings activity
  * the livewallpaper service, pointing at the livewallpaper.xml file, see `meta-data`

Note that live wallpapers are only supported starting from Android 2.1 (SDK level 7).

LWPs have some limitations concerning touch input. In general only tap/drop will be reported. If you want full touch you can see the `AndroidApplicationConfiguration#getTouchEventsForLiveWallpaper` flag to true to receive full multi-touch events.

## Daydreams ##
Since Android 4.2, users can set [Daydreams](http://developer.android.com/about/versions/android-4.2.html#Daydream) that will get displayed if the device is idle or docked. These daydreams are similar to screensavers and can display things like photo albums etc. Libgdx let's you write such daydreams easily.

The starter class for a Daydream is called AndroidDaydream. Here's an example:

```java
package com.badlogic.gdx.tests.android;

import android.annotation.TargetApi;
import android.util.Log;

import com.badlogic.gdx.ApplicationListener;
import com.badlogic.gdx.backends.android.AndroidApplicationConfiguration;
import com.badlogic.gdx.backends.android.AndroidDaydream;
import com.badlogic.gdx.tests.MeshShaderTest;

@TargetApi(17)
public class Daydream extends AndroidDaydream {
   @Override
   public void onAttachedToWindow() {
      super.onAttachedToWindow();      
	  setInteractive(false);

      AndroidApplicationConfiguration cfg = new AndroidApplicationConfiguration();
      cfg.useGL20 = true;
      ApplicationListener app = new MeshShaderTest();
      initialize(app, cfg);
   }
}
```

Simply derive from AndroidDaydream, override onAttachedToWindow, setup your configuration and ApplicationListener and initialize your daydream.

In addition to the daydream itself you can provide a settings activity that lets the user configure your daydream. This can be a normal activity, or a libgdx `AndroidApplication`. An empty activity as an example: 

```java
package com.badlogic.gdx.tests.android;

import android.app.Activity;

public class DaydreamSettings extends Activity {

}
```

This settings activity must be specified as metadata to the Daydream service. Create an xml file in the res/xml folder of your Android project and specify the activity like this:

```xml
<dream xmlns:android="http://schemas.android.com/apk/res/android"
 android:settingsActivity="com.badlogic.gdx.tests.android/.DaydreamSettings" />
```

Finally, add a section for the settings activity in the AndroidManifest.xml as usual, and a service description for the daydream, like this:

```xml
<service android:name=".Daydream"
   android:label="@string/app_name"
   android:icon="@drawable/icon"
   android:exported="true">
   <intent-filter>
	   <action android:name="android.service.dreams.DreamService" />
	   <category android:name="android.intent.category.DEFAULT" />
   </intent-filter>
   <meta-data android:name="android.service.dream"
	   android:resource="@xml/daydream" />
</service>
```

# iOS Xamarin (Deprecated!) #

The iOS backend relies on using Xamarin's [MonoDevelop](http://xamarin.com/monotouch) IDE for development and a Monotouch license for deployment.  The entry-point for a Monotouch application is the AppDelegate found in the Main.cs file of the project.  An example of this is below:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

using MonoTouch.Foundation;
using MonoTouch.UIKit;
using com.badlogic.gdx.backends.ios;
using com.me.mygdxgame;

namespace com.me.mygdxgame
{		
	public class Application
	{
		[Register ("AppDelegate")]
		public partial class AppDelegate : IOSApplication {
			public AppDelegate(): base(new MyGdxGame(), getConfig()) {

			}

			internal static IOSApplicationConfiguration getConfig() {
				IOSApplicationConfiguration config = new IOSApplicationConfiguration();
				config.orientationLandscape = true;
				config.orientationPortrait = false;
				config.useAccelerometer = true;
				config.useMonotouchOpenTK = true;
				config.useObjectAL = true;
				return config;
			}
		}
		
		static void Main (string[] args)
		{
			UIApplication.Main (args, null, "AppDelegate");
		}
	}
}
```

### Info.plist ###

The Info.plist file contains the configuration information about your application: screen orientation, minimal OS version, optional parameters, screen shots, etc.  This is an XML file that is best edited via MonoDevelop.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>CFBundleDisplayName</key>
	<string>my-gdx-game</string>
	<key>MinimumOSVersion</key>
	<string>3.2</string>
	<key>UIDeviceFamily</key>
	<array>
		<integer>2</integer>
		<integer>1</integer>
	</array>
	<key>UIStatusBarHidden</key>
	<true/>
	<key>UISupportedInterfaceOrientations</key>
	<array>
		<string>UIInterfaceOrientationPortrait</string>
		<string>UIInterfaceOrientationPortraitUpsideDown</string>
	</array>
	<key>UISupportedInterfaceOrientations~ipad</key>
	<array>
		<string>UIInterfaceOrientationLandscapeLeft</string>
		<string>UIInterfaceOrientationLandscapeRight</string>
	</array>
</dict>
</plist>
```

### convert.properties ###

A conversion process is required in order to create the assemblies needed by Monotouch for the iOS platform.  This processing is done as part of the pre-build step when you issue the build operation in MonoDevelop.  If you are using third party libraries or have non-standard locations defined for some of your source, you will need to update the convert.properties file accordingly.  An example file is below:

```
SRC =       ../my-gdx-game/src/
CLASSPATH = ../my-gdx-game/libs/gdx.jar
EXCLUDE   =
IN        = -r:libs/ios/gdx.dll -recurse:target/*.class
OUT       = target/my-gdx-game.dll
```

This file specifies the input files that comprise the my-gdx-game.dll assembly.

# HTML5/GWT #
The main entry-point for an HTML5/GWT application is a `GwtApplication`. Open `GwtLauncher.java` in the my-gdx-game-html5 project:

```java
package com.me.mygdxgame.client;

import com.me.mygdxgame.MyGdxGame;
import com.badlogic.gdx.ApplicationListener;
import com.badlogic.gdx.backends.gwt.GwtApplication;
import com.badlogic.gdx.backends.gwt.GwtApplicationConfiguration;

public class GwtLauncher extends GwtApplication {
   @Override
   public GwtApplicationConfiguration getConfig () {
      GwtApplicationConfiguration cfg = new GwtApplicationConfiguration(480, 320);
      return cfg;
   }

   @Override
   public ApplicationListener getApplicationListener () {
      return new MyGdxGame();
   }
}
```

The main entry-point is composed of two methods, `GwtApplication.getConfig()` and `GwtApplication.getApplicationListener()`. The former has to return a [GwtApplicationConfiguration](https://github.com/libgdx/libgdx/tree/master/backends/gdx-backends-gwt/src/com/badlogic/gdx/backends/gwt/GwtApplicationConfiguration.java) instance, which specifies various configuration settings for the HTML5 application. The `GwtApplication.getApplicatonListener()` method returns the `ApplicationListener` to run.

### Module Files ###
GWT needs the actual Java code for each jar/project that is referenced. Additionally, each of these jars/projects needs to have one module definition file, having the suffix gwt.xml. 

In the example project setup, the module file of the html5 project looks like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE module PUBLIC "-//Google Inc.//DTD Google Web Toolkit trunk//EN" "http://google-web-toolkit.googlecode.com/svn/trunk/distro-source/core/src/gwt-module.dtd">
<module>
   <inherits name='com.badlogic.gdx.backends.gdx_backends_gwt' />
   <inherits name='MyGdxGame' />
   <entry-point class='com.me.mygdxgame.client.GwtLauncher' />
   <set-configuration-property name="gdx.assetpath" value="../my-gdx-game-android/assets" />
</module>
```

This specifies two other modules to inherit from (gdx-backends-gwt and the core project) as well as the entry-point class (`GwtLauncher` above) and a path relative to the html5 project's root directory, pointing to the assets directory.

Both the gdx-backend-gwt jar and the core project have a similar module file, specifying other dependencies. *You can not use jars/projects which do not contain a module file and source!*

For more information on modules and dependencies refer to the [GWT Developer Guide](https://developers.google.com/web-toolkit/doc/1.6/DevGuide).

### Reflection Support ###
GWT does not support Java reflection for various reasons. Libgdx has an internal emulation layer that will generate reflection information for a select few internal classes. This means that if you use the [Json serialization](https://code.google.com/p/libgdx/wiki/JsonParsing) capabilities of libgdx, you'll run into issues. You can fix this by specifying for which packages and classes reflection information should be generated for. To do so, you can put configuration properties in your GWT project's gwt.xml file like so:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<module>
    ... other elements ...
    <extend-configuration-property name="gdx.reflect.include" value="org.softmotion.explorers.model" />
    <extend-configuration-property name="gdx.reflect.exclude" value="org.softmotion.explorers.model.HexMap" />
</module>
```

You can add multiple packages and classes by adding more extend-configuration-property elements.

This feature is experimental, use at your own risk.

### Loading Screen ###

A libgdx HTML5 application preloads all assets found in the `gdx.assetpath`. During this loading process, a loading screen is displayed which is implemented via GWT widget. If you want to customize this loading screen, you can simply overwrite the `GwtApplication.getPreloaderCallback()` method (`GwtLauncher` in the above example). The following example draws a very simple, ugly loading screen using Canvas:

```java
long loadStart = TimeUtils.nanoTime();
public PreloaderCallback getPreloaderCallback () {
  final Canvas canvas = Canvas.createIfSupported();
  canvas.setWidth("" + (int)(config.width * 0.7f) + "px");
  canvas.setHeight("70px");
  getRootPanel().add(canvas);
  final Context2d context = canvas.getContext2d();
  context.setTextAlign(TextAlign.CENTER);
  context.setTextBaseline(TextBaseline.MIDDLE);
  context.setFont("18pt Calibri");

  return new PreloaderCallback() {
	 @Override
	 public void done () {
		context.fillRect(0, 0, 300, 40);
	 }

	 @Override
	 public void loaded (String file, int loaded, int total) {
		System.out.println("loaded " + file + "," + loaded + "/" + total);
		String color = Pixmap.make(30, 30, 30, 1);
		context.setFillStyle(color);
		context.setStrokeStyle(color);
		context.fillRect(0, 0, 300, 70);
		color = Pixmap.make(200, 200, 200, (((TimeUtils.nanoTime() - loadStart) % 1000000000) / 1000000000f));
		context.setFillStyle(color);
		context.setStrokeStyle(color);
		context.fillRect(0, 0, 300 * (loaded / (float)total) * 0.97f, 70);
		
		context.setFillStyle(Pixmap.make(50, 50, 50, 1));
		context.fillText("loading", 300 / 2, 70 / 2);
	 }

	 @Override
	 public void error (String file) {
		System.out.println("error: " + file);
	 }
  };
}
```

Note that you can only use pure GWT facilities to display the loading screen, libgdx APIs will only be available after the preloading is complete.