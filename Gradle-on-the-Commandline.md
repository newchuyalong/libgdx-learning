### Running from the command line
The ANDROID_HOME environment variable needs to be pointing to a valid android SDK before you do any command line wizardry. To do this on Windows, you can issue this from the command line:

`set ANDROID_HOME=C:/Path/To/Your/Android/Sdk`

On Linux and Mac OS X you can do this from the shell:

`export ANDROID_HOME=/Path/To/Your/Android/Sdk`

Once you have the ANDROID_HOME environment variable pointing at the Android SDK, you can use the following to run gradle tasks from the command line:

On Windows invoke gradle like this:
`gradlew clean`

On Mac OS or Linux invoke gradle like this: 
`./gradlew clean`

Note the leading dot slash on Unix like systems. In both cases, the clean task will remove all build files from all modules in the project, e.g. class files previously generated.

#### Running the desktop project
`./gradlew desktop:run`
#### Running the android project
`./gradlew android:installDebug`

This task will compile the APK for android and install it on a connected device. You will have to start the app manually on the device yourself.
#### Running the gwt project
`./gradlew gwt:gwtDev`

#### Running the ios project
`./gradlew ios:launchIPhoneSimulator`
`./gradlew ios:launchIPadSimulator`
`./gradlew ios:launchIOSDevice`

The last command will launch your ios project on a connected iDevice, provided it is provisioned. Please refer to Apple's docs on how to provision a device.

### Packaging from the command line
TBD