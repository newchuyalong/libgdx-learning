## Contents

* [**Useful links**] (#useful-links)
* [**Guide to build.gradle**] (#guide-to-build.gradle)
* [**Libgdx Dependencies**] (#libgdx-dependencies)
 * [Available libgdx extensions] (#libgdx-extensions)
* [**Maven Dependencies**] (#maven-dependencies)
 * [Examples] (#maven-dependency-examples)
* [**File Dependencies**] (#file-dependencies)

### Useful links
Dependency management with Gradle is easy to understand, and has many different approaches.  If you are familiar with Maven or Ivy, Gradle is fully compatible with both approaches, as well as being able to support custom approaches.  If you aren't familiar with Gradle, there are great resources on their site to learn, it is recommended you give them a read to get comfortable with Gradle.
* [Gradle's User Guide](http://www.gradle.org/docs/current/userguide/userguide.html)
* [Gradle's Depedency Management Guide](http://www.gradle.org/docs/current/userguide/dependency_management.html)

### Guide to build.gradle
Gradle projects are managed by build.gradle files in their root directory. If you have used the gdx-setup.jar to build your libgdx project you will notice the structure: [Structure Example](https://github.com/libgdx/libgdx/wiki/Project-Setup-Gradle#project-layout)

The root directory, and each sub directory contains a build.gradle file, for clarity we will define the dependencies in the root directory's build.gradle file.

Here is a small section of the _default_ buildscript that is generated from the setup:

_Full script you will see will differ slightly depending on what other modules you have
see here [here](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-setup/src/com/badlogic/gdx/setup/resources/build.gradle)_
```groovy
buildscript {
    //Defines the repositories that are required by this script, e.g. android plugin
    repositories {
        //maven central repository, needed for the android plugin
        mavenCentral()
        //repository for libgdx artifacts
        maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
    }
    //
    dependencies {
        //Adds the android gradle plugin as a dependency of this buildscript
        classpath 'com.android.tools.build:gradle:0.9+'
    }
}

allprojects {
    apply plugin: "eclipse"
    apply plugin: "idea"
    
    version = "1.0"
    ext {
        appName = "%APP_NAME%"
        gdxVersion = "1.0-SNAPSHOT"
        roboVMVersion = "0.0.10"
    }
    
    repositories {
        //Defines all repositories needed for all projects
        mavenLocal();
        mavenCentral()
        maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
    }
}

project(":core") {
    apply plugin: "java"
    
    dependencies {
        //Defines dependencies for the :core project, in this example the gdx depdendency
        compile "com.badlogicgames.gdx:gdx:$gdxVersion"
    }
}

project(":desktop") {
    apply plugin: "java"
    
    dependencies {
    //Defines dependencies for the :desktop project, adds dependency on the :core project as well as 
    //The gdx lwjgl backend and native dependencies
        compile project(":core")
        compile "com.badlogicgames.gdx:gdx-backend-lwjgl:$gdxVersion"
        compile "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-desktop"
    }
}

project(":android") {
    apply plugin: "android"

    configurations { natives }

    dependencies {
        //defines dependencies for the :android project, depends on the :core project,
        //and also the android backends and all platform natives. Note the 'natives' classifier
        //in this project
        compile project(":core")
        compile "com.badlogicgames.gdx:gdx-backend-android:$gdxVersion"        
        natives "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-x86"
        natives "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-armeabi"
        natives "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-armeabi-v7a"
    }
}

```

### Libgdx Dependencies
#### Libgdx Extensions

### Maven Dependencies
#### Maven Dependency Examples

### File Dependencies

