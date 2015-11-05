So you are planning to join #libGDXJAM and you are considering (or already decided upon) choosing Overlap2D editor as your primary weapon of choice. If so, read on!

In this article I (as the creator of Overlpa2D) will try to provide as much information as possible on how to be friends with O2D and use it wisely!

# Getting Started

### Prerequisites

  1. Make sure you have JDK 1.8 (It's required for Overlap2D to run, and you always can have both 1.7 and 1.8)
  2. Download the Editor itself from our [website](http://overlap2d.com/)
  3. Make yourself a nice cozy folder to work in
  4. Get some test assets (png sprites) later you can substitute that with your own.

### Working with Overlap2d

Working with Overlap2D consists of several parts:
  1. Creating your main libGDX project using [setup tool](http://bitly.com/1i3C7i3)
  2. Creating empty project with overlap2d
  3. Making a test scene in the editor (import some images, put some stuff on the scene)
  4. Export that data to your assets folder in your libGDX project
  5. Use overlap2d-libgdx-runtime to render your scene in your game.

Now I can write a lot and bore you to death. Or, you can just watch me do all this steps in detail: [https://www.youtube.com/watch?v=bhvHm2sM0qo](https://www.youtube.com/watch?v=bhvHm2sM0qo)

Important things to know:
* When using setup app, click on Third Party Extensions and select overlap2d from the list, also make sure to have freetype and box2d checkboxes selected.
* For the JAM better use latest runtime, which is 0.1.2-SNAPSHOT (make sure to set that in build.gralde file, because it's not default value)
* When creating your o2d project, don't think about multiple resolutions and huge textures. This is just a JAM. I would go with a small world size (say 800x480 or similar), and pixel per world unit somewhere between 1-4.


# What game are you making?


# Tips and Tricks