# Using libgdx with Android Studio #
At Google I/O 2013, the Android tools team released [Android Studio](http://developer.android.com/sdk/installing/studio.html), and IDE based on IntelliJ IDEA, focusing on pure Android development.

Android Studio is sadly unfit for libgdx projects at this time, as it only allows the creation of Android projects. Standard Java projects are not supported, and it is unclear if this will ever become available.

Libgdx's cross-platform nature does not lend itself well to development with Android Studio. This should not  be a big hindrance, as libgdx projects don't benefit much from Android Studios capabilities. E.g. the layout preview is irrelevant for libgdx applications, as they don't use native Android UI widgets. The inline documentation support for Android APIs is also not needed for libgdx applications.

However, we'll try to make developing libgdx applications with the new [Android Gradle build system](http://tools.android.com/tech-docs/new-build-system/user-guide) possible in the near future.

If you must use Android Studio, check out this [thread](http://www.badlogicgames.com/forum/viewtopic.php?f=11&t=9097) on the forums.

Alternatively you can use [[IntelliJ Idea | Using libgdx with IntelliJ IDEA]] with libgdx, potentially with [[Maven|Maven Integration]].