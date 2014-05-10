Gradle is very powerful, and once you get used to it is a great tool to be versed in and have in your arsenal, but when mixed with your IDE it can cause workflow problems.  This varies from project to project, and from person to person; here is an example of such an issue:

Having a multi-project, multi-flavour project, where you want to be running your desktop build very often to check your latest changes.  Due to how Gradle works (it allows the flexibility of accessing and changing any part of the build from any part of the project), it must configure all projects in a multi-project setup before any task is executed.  When you have a project with desktop only, this is usually very speedy, but when you add in an android project, html project the time starts to rack up.  This is especially noticeable in Intellij IDEA.

Tips to speed up Gradle if you still want IDE integration

You can try a few things to get running more efficiently:

Gradle daemon
The Gradle daemon aims to lower execution time and startup time of tasks especially where tasks are executed frequently. 

How to run with the Gradle daemon
You can run tasks adding the flag --daemon
You can also add the option to run on all tasks by editing a "gradle.properties" file that resides in the root directory of your project.
Add the line; org.gradle.daemon=true

Further reading:
Daemon http://www.gradle.org/docs/current/userguide/gradle_daemon.html



How to remove Gradle IDE integration from your project

Note: This only applies for Eclipse and Intellij IDEA users

With the project that is generated from gdx-setup.jar, the Gradle IDEA, and Gradle Eclipse plugins are applied to all projects.
These plugins allow you to generate IDE specific files that you can then work from.

Doing this allows you to keep Gradle out of your IDE, so that when you want to launch any task, it wont configure your whole project and will be as quick as if you had a regular IDE generated project.  This does mean that you need to sort out support for the sub modules in the IDE yourself, android support, robovm ect.  (It is still recommended that you do all GWT/html stuff from the command line, as well as using the great packaging tasks that are supplied)

It also allows you to keep the power of Gradle at your disposal, to handle all your dependencies, packaging tasks, signing tasks, deployment and anything else you may want to implement by running from the command line.

Creating your IDEA project
Creating your Eclipse Project
