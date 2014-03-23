## Importing to Intellij IDEA
Intellij IDEA supports Gradle projects out of the box. As such, you don't have to drop down onto the command line to generate Intellij IDEA projects. Open Intellij IDEA, and select Import Project, then select to the build.gradle file in the root folder of your project and press OK. In the next dialog leave all settings as they are and hit OK. That's it!

#### As a file system
Let gradle generate the Intellij Idea project:
```
./gradlew idea
```

Once gradle is done with generating the project file, you can open this with Idea:

1. File -> Open
2. Select the .ipr file from the root directory, hit 'OK'

You'll need to set the Android SDK for the android module:

1. File -> Project Structure
2. Select 'modules' in the panel on the left
3. Select the android module
4. Set the module SDK to the android SDK that you configured for IntelliJ

#### Using the gradle plugin
Intellij has gradle support bundled, so you can import a gradle project without installing any additional plugins.

To do this: File > Import Project, locate and choose the build.gradle in the root directory of the project and press ok. 

![Import Project](http://i.imgur.com/dx4ZJQh.png)

Keep the settings as default unless you know that you want to use a customized gradle wrapper or your local distribution. Hit OK, and it will start the import of the project and pulling in the default dependencies. 

#### A note on Intellij content root problems
Gradle in Intellij does not like any source paths that are outside of the content root. For example setting the resource directory for the desktop project as the android assets folder, this will cause a gradle refresh to fail. There are currently only a few solutions for this problem, and none of them are that ideal.  The developers have said that it will someday play nice, but as for now you can do the following:

* Don't use gradle tasks to run tasks that would require the content out of the root and keep the asset directories as 'wrong'
* Copy assets to each project
* Don't use the gradle IDE integration, run from command line and import as a file system

### Running & Debugging in Intellij Idea
##### Running the desktop project
At the top window hit Run > Edit configurations, a dialog will pop up. Click Application on the left and use the little green + to create a new run configuration.
![Edit run configuration](http://i.imgur.com/CZQgTpx.png)

If you click the ... on the right hand side, you will be able choose from launchers in your projects. Choose the DesktopLauncher.

Next you need to change the working directory to where your assets are. The usual way to do this is access everything from the android/assets folder as shown in the image.

Finally choose the desktop module for classpath. Give it a run!

##### Running the android project
At the top of the window hit Run > Edit configurations, a dialog will pop up. Click on Android Application on the left and if there is no project defined there, use the little green + to add a new configuration.
![Android run configuration](http://i.imgur.com/68zYHpx.png)

All the settings here are fairly straight forward, make sure you have the module selected as the android module, if you have multiple activities you can choose which one you want to launch.  

Under target device, you can select if you want to run on an actual device or god forbid an emulator.

Hit ok, give it a run!
