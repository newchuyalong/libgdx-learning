So you are planning to join #libGDXJAM and you are considering (or already decided upon) choosing Overlap2D editor as your primary weapon of choice. If so, read on!

In this article I (as the creator of Overlpa2D) will try to provide as much information as possible on how to be friends with O2D and use it wisely!

# Getting Started

### Prerequisites
I am assuming that you already have JDK installed, but Overlap2D editor itself, needs you to have Java 1.8, so make sure you have that one in order to be able to run the editor jar file.

### Preparing your coding project
This one you might already know, but let's go through it anyway. libGDX has this awesome project [setup tool](http://bitly.com/1i3C7i3) download it, and create your project somewhere where you feel comfy. Make sure that you check freetype and box2d checkboxes, as well as click on Third Party Extensions, and select overlap2d.
After you have a ready to go project, fire up your favorite coding IDE, import it, and just make sure everything runs smooth.

### Preparing your Overlap2D project
Overlap2D project is the one that will contain all your assets and scene data. Download Overlap2D editor from the main [website](http://overlap2d.com/). (red button on the left) This will download a ZIP archive, with a sample project and editor itself. Run the editor and make a new project. This is a JAM, so I am sure there is no point to worry about "multi-resolution" project. That means that you have to just feel the size of your desired screen size, set pixelPerWorld unit to something like 1, 2, 4 or similar (up to you, but usually if 
if you have planned big game screen size, maybe set unit to at least 4, to work with smaller numbers, and if you plan to have your game in something like 800x480 box, unit of 1 will do just one and avoid confusion)
Click create, and you have an empty project. If you already have some test assets ready, you can click File->Import and drag drop them there (note: drag dropping does not work on osx, so you have to click Browse instead)

### Exporting assets from o2d to main project
After you made a simple scene, you need to bring all of that data to your coding project. That is done by going to File->Export settings, and setting the assets path correctly. After that you can simply export by pressing Ctrl+E every time you made a change. 

### Runtime version
For the JAM you want to use the latest runtime for stability. so head to your build.gradle file in your project, find line with overlap2d, and make sure you are loading version 1.2-SNAPSHOT that contains all latest cool stuff.

### Getting started tutorial

Now this was a bird eye overview, but you might want to make sure you get it all right, and the easiest way to do it is to watch me doing all this steps on youtube: [https://www.youtube.com/watch?v=bhvHm2sM0qo&feature=youtu.be](https://www.youtube.com/watch?v=bhvHm2sM0qo&feature=youtu.be)


# What game are you making?


# Tips and Tricks