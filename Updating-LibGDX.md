#### [Check the latest version of libGDX and all dependencies](http://libgdx.badlogicgames.com/versions.html)

libGDX's Gradle based projects make it very easy to switch between libGDX versions. In general you'll be interested in two types of libGDX builds:

* Release builds: these are considered stable. You can see the available release versions on [Maven Central](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.badlogicgames.gdx%22%20AND%20a%3A%22gdx%22).
* Nightly builds: also known as SNAPSHOT builds in Maven lingo. These are cutting edge versions of libGDX that are built on every change to the source repository. Snapshot builds also have a version number of the form x.y.z-SNAPSHOT, e.g. 1.0.1-SNAPSHOT. You can find the latest SNAPSHOT version string [here](https://github.com/libgdx/libgdx/blob/master/pom.xml#L13).

Your Gradle based project makes it very easy to switch between releases and nightly builds. Open up the `build.gradle` file in the root of your project, and locate the following line:

```Groovy
 gdxVersion = "1.5.2"
```

The version you see may be higher than 1.5.2 already. Once you've located that string, you can simply change it to the latest release (or an older release) or to the current SNAPSHOT version. You may also have to update other modules in that same section of the build.gradle file, based on the [versions listing](http://libgdx.badlogicgames.com/versions.html). Once edited, save the build.gradle file.

The next step is dependent on your IDE:

* **Eclipse**: Select all your projects in the package explorer, right click, then click `Gradle -> Refresh All`. This will download the libGDX version you specified in build.gradle and wire up the JAR files with your projects correctly.
* **Intellij IDEA**: will usually detect that your build.gradle has been updated and show a refresh button. Just click it and IDEA will update libGDX to the version you specified in build.gradle. Go into the gradle tasks panel/tool view and click the refresh button. Running a task like 'builddependents' also tends to do this.
* **Netbeans**: in the "Projects" view, right-click the top-most project node and select "Reload Project".  All sub-projects will also be reloaded with the new files.
* **Command Line**: invoking any of the tasks will usually check for changes in dependency versions and redownload anything that changed.

And that's it! No need to manually juggle JAR files, .so files or anything else. Just change a string in a file and update via your IDE or the command line.