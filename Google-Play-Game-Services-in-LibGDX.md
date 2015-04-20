[Google Play Game Services](https://developers.google.com/games/services/) offers cross platform social leaderboards, achievements, and much more (realtime multiplayer, cloud saves, anti-piracy...)

A [Super Jumper](https://github.com/libgdx/libgdx-demo-superjumper) based example that makes use of Leaderboards and Achievements is available to download from [Google Play](https://play.google.com/store/apps/details?id=com.theinvader360.tutorial.libgdx.gameservices).

The project is freely available on [GitHub](https://github.com/TheInvader360/libgdx-gameservices-tutorial), and a companion tutorial is available [here](http://theinvader360.blogspot.co.uk/2013/10/google-play-game-services-tutorial-example.html).

Another in-depth LibGDX-based tutorial for adding Google Play Game Servics can be found [here](http://fortheloss.org/tutorial-set-up-google-services-with-libgdx/).

For getting started with code in iOS via RoboVM, check [here](https://github.com/BlueRiverInteractive/robovm-ios-bindings/blob/master/google-play-game-services/src/org/robovm/bindings/gpgs/sample/Sample.java).

# Intellij Setup

1. Install Google Play Service and Google Play Repository using and Android SDK

2. Download BaseGameUtils sample project [here](https://github.com/playgameservices/android-basic-samples) and place it in your project root folder.

3. Edit settings.gradle 
```
include 'desktop', 'android', 'ios', 'html', 'core', "BaseGameUtils"
```
4. Edit root build.gradle and add the below as android dependency: 
```
compile project(":BaseGameUtils")
```