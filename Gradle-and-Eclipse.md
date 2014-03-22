### Importing to Eclipse
Using the Gradle project with Eclipse can be done in two ways:

* Generate Eclipse projects on the command line and import them into Eclipse
* Install the Eclipse Gradle Integration and load the Gradle projects directly

#### Generating and Importing Eclipse projects
Open the command line, navigate to your project's root folder, then execute

    $ ./gradlew eclipse

This will generate .classpath and .project files for every subproject (core, desktop, android, ios, gwt).

Next, open Eclipse, go to File -> Import -> General -> Existing Projects into Workspace. Click on the Browse button and select your project's root directory. You should see a list of projects (android, core, desktop, gwt, ios), all checked. Click the Finish button.

### Importing with Gradle Integration
If you have installed the [Eclipse Gradle Integration plugin](https://github.com/spring-projects/eclipse-integration-gradle/blob/master/README.md) (check the section 'Installing Gradle tooling from update site") you can work without dropping down onto the command line.

Simple go to File -> Import -> Gradle 