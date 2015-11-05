So you are planning to join #libGDXJAM and you are considering (or already decided upon) choosing Overlap2D editor as your primary weapon of choice. If so, read on!

In this article I (as the creator of Overlpa2D) will try to provide as much information as possible on how to be friends with O2D and use it wisely!

# Getting Started

### Prerequisites

  1. Make sure you have JDK 1.8 (It's required for Overlap2D to run, and you always can have both 1.7 and 1.8)
  2. Download the Editor itself from our [website](http://overlap2d.com/)
  3. Make yourself a nice cozy folder to work in
  4. Get some test assets (png sprites) later you can substitute that with your own.
  5. Learn more about [Ashley](https://github.com/libgdx/ashley/wiki) (our runtime uses that architecture)

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
* When creating your o2d project, don't think about multiple resolutions and huge textures. This is just a JAM. I would go with a small camera-world size (say 800x480 or similar), and pixel per world unit somewhere between 1-4.


# What game are you making?

How to best use the editor depends on what kind of game are you making, so I'll try to cover some of them:

### Side scroller or runner

So you are making a side scroller or a runner, meaning you have this character that walks or runs on this background from left to right. That is perfect candidate for Overlap2D. You can make several layers, like background, foreground. Combine things like trees and mountains into groups, position things behind. Then have your main character (probably an animation) be in front. Give it a unique identifier, And so on.
There is even tutorial series on how to do it in our [documentation](http://overlap2d.com/documentation/)

### Tiled view from above

Could be tower defense or strategy or dungeon kind of thing. In this case you want your world to have tiles. 
Decide on images you want to use as tiles (make sure they are same size and square) and put them lined up somewhere on the scene. give each of them tag "tile". In upper panel find grid size, and set it to your tile size (this way things will snap to grid) Now you can just copy paste your initial tiles, and put them where they fit. You can then give them additional tags like "wall" or "turret" to mention their behaviour or if 
player can or cannot walk through them. Of course later you need to write a System in your code, to use this tags accordingly. There is a sample "Tower defense" project I made you can take a look at as an example  
[here](https://github.com/azakhary/thm)

### I need it for UI

Okay so your game itself is kind of different and does not need a predefined scene data. Maybe you generate all stuff with code, and that's fine. But you want to make your menus, dialogs and buttons with overlap2d?
That's cool too! You can make buttons in overlap2d easily. just chose button assets, and right click convert to button. This will create a composite with some predefined layers. Make sure to go in, and put things in correct places (pressed layer and so on) You can test your button directly in the editor. 
To make some dialogs uses 9patch images for background (actually same for buttons) If you do not have a 9patch images, you can make one directly from the editor. Just right click on regular image, and choose "Convert to 9patch" this will open a dialog to set it up. You can later convert all the buttons and texts to composite items, and put that dialog into library. Then later form your code create a CompositeActor from that library data.


# Tips and Tricks