Libgdx comes with a ton of small [test examples](https://github.com/libgdx/libgdx/tree/master/tests/gdx-tests/src/com/badlogic/gdx/tests) To run and easily analyze these, you need to work directly from the libgdx source.

Running the tests is currently limited to Eclipse, so [[make sure you have Eclipse setup properly|Setting up your Development Environment (Eclipse, Intellij IDEA, NetBeans)]]. You will not need the C/C++ cross compilers to run the tests and demos or mess with libgdx's Java source code.

## Getting the Source ##
To get the sources and all the native libraries for all platforms, perform the following on the command line. You can do the cloning with a Git GUI as well of course:

```
git clone git://github.com/libgdx/libgdx.git
cd libgdx
ant -f fetch.xml
```

This fetches all the source code from the git repository, then downloads the native files from the build server via the fetch.xml Ant script. The Git repository does not contain these native libraries to trim down it's size, hence the two steps.

To stay up to date you can do the following from within the libgdx directory every now and then:

```
git pull
ant -f fetch.xml
```

This will pull in the latest changes from the Git repository, and download the latest native libraries from the build server.

## Importing into Eclipse ##
Once you have the sources and native libraries, you can open Eclipse and import all the projects:

  * Go to `File -> Import`
  * Choose `General -> Existing Projects into Workspace`
  * `Browse` to the libgdx directory and make sure *all* projects are checked
  * Click `Finish`

Eclipse now imports all the projects from the libgdx directory. On first import, you usually see errors everywhere. Fear not, that is just Eclipse being silly. To resolve the issues do the following:

  * Go to `Project -> Clean`
  * Make sure all projects are selected, then click `OK`
  * Select all projects in the package explorer, then hit `F5` to refresh them (vodoo...)
  * There will be a few issues remaining concerning GWT. In the Problems view, right click on each entry saying "The web.xml file does not exist", select `Quick Fix` and press `OK`

If you did not run 'ant -f fetch.xml' before this point, you will encounter many errors here due to missing backends.

To compile and run anything GWT related, you'll also need to install the Google Web Toolkit SDK, and the Google plugin for Eclipse. These can be found here: http://www.gwtproject.org/.

You are now ready to start the tests or run the demo games.

## Running the Demos & Tests ##
The test examples are contained in the gdx-tests project. This project only contains the source code. To actually start the tests on the desktop you have to run the LwjglTestStarter class, contained in the gdx-tests-lwjgl project. To run the tests on Android simply fire up the gdx-tests-android project in the emulator or on a connected device! To run the tests in your browser fire up the gdx-tests-gwt project.

Running the demo games works similarly. Each demo game has 4-5 projects, the core project, the desktop project (sometimes that's the same as the core project), the android project, the html5 project and the iOS/RoboVM project.

For both the tests and the demos the following steps are used to start the apps

  * Right click one of the projects mentioned above
  * Select Run As -> Java Application for desktop projects. In the upcoming dialog select LwjglTestStarter
  * Select Run As -> Android Application for the android project. This will either start the emulator or deploy the test project to a connected Android device
  * Select Run As -> Web Application for the html5 project. You might be asked for a WAR directory.  Use the gdx-tests-gwt/war. A new view opens up, click the URL that's presented to you. You might get prompted to install the GWT plugin for your browser of choice (hint: use Chrome). Note that the app runs in development mode, which means it will be terribly slow. For full speed you have to compile the HTML project, then deploy it to a web server.

For a more in-depth description of the project setup as well as how to run and debug a libgdx project see ProjectSetupNew

##Creating New Tests

To add a test:

* Add a class to gdx-tests/src/com/badlogic/gdx/tests/, it should extend GdxTest
* Add any assets you need to gdx-tests-android/assets/data. Ideally you want to reuse assets already in there
* Add your class to GdxTests#tests, it's a long list of Class instances. Try to keep it in alphabetical order! This will automatically add your test to the desktop and Android test runners (E.g. LwjglTestStarter)
* INCLUDE FOR GWT: Add your test to GwtTestWrapper, through creating an Instancer. Your test needs to specify that it needs GL20 for it to work with the GWT test harness.
* EXCLUDE FROM GWT: Add a line to GdxTests.gwt.xml excluding your test and giving a short explanation for why it has been excluded in an xml comment.
