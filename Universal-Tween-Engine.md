# Universal Tween Engine

Since switching over to the gradle based build system some users have been having a hard time adding the Universal Tween Engine to their newly created projects. This article aims to make this process as painless as possible.

> **Note:** this article assumes that you've already created a project using gdx-setup. If you haven't created your project yet follow the instructions here before continuing:

> [Project Setup Gradle](https://github.com/libgdx/libgdx/wiki/Project-Setup-Gradle)

## FileTree Dependency Method (Easiest)

This method is the easiest to get up and running but if you end up with multiple projects that make use of the Universal Tween Engine you might want to consider using a Local Maven Repo. See below for instructions on how to do that.

---

1. Download the Universal Tween Engine from here: [Universal Tween Engine](https://code.google.com/p/java-universal-tween-engine/downloads/list)
2. In the root of your project directory create a new directory called 'libs'
3. Extract the Universal Tween Engine zip file to the directory that you just created
4. Open up the build.gradle file located in the root of your project directory in a text editor or your IDE
  * Locate the section marked **project(":core")**
  * In the **dependencies** section add the following line:
  ```
  compile fileTree(dir: '../libs', include: '*.jar')
  ```
  * the **project(":core")** section of your build.gradle file should now look like this:

  ```groovy
  project(":core") {
     ...

      dependencies {
          ...
          compile fileTree(dir: 'libs', include: '*.jar')
      }
  }
  ```
5. **Required For Android**: Locate the section marked **project(":android")**
  * In the **dependencies** section add the following line:
  ```
  compile fileTree(dir: '../libs', include: '*.jar')
  ```
  * the **project(":android")** section of your build.gradle file should now look like this:
  ```groovy
  project(":android") {
     ...

      dependencies {
          ...
          compile fileTree(dir: 'libs', include: '*.jar')
      }
  }
  ```
6. **Required for HTML Projects**
  * Add the following line to your **GdxDefinition.gwt.xml** and **GdxDefinitionSuperdev.gwt.xml** files
    * they are located in &lt;my-project-dir&gt;/html/src/your/package/name
  ```xml
<inherits name='aurelienribon.tweenengine'/>
  ```

7. And most importantly you now need to refresh your project dependencies through your IDE or by running the following command from your project directory:

  ```
  gradlew --refresh-dependencies
  ```