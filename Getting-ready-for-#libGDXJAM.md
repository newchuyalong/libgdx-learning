The Libgdx community is organizing [#libGDXJAM](https://twitter.com/hashtag/libGDXJam?src=hash), a game programming competition that will take place sometime in December, the final date is yet to be announced.

This article will take you through the rules and help you get ready to jam!

## Rules

**Note**: The following rules are not final, stay up to date on the [rules discussions](http://www.badlogicgames.com/forum/viewtopic.php?f=11&t=20974).

### Duration
The game jam will last 4 weeks. This is to allow developers with day jobs and other responsibilities to participate and be able to submit a typical 48h jam quality game.

### Theme
Submissions must meet the theme that the community will chose from. We are currently crowdsourcing a [list of ideas](https://docs.google.com/spreadsheets/d/1-wDLMLVjh4mpnoBIv6i2MxV4NCW6VOIhQ0-bghS869Q/edit). The best 5 themes from the list will be selected and the community will vote on the final theme one week before the jam starts.

### Assets
Use whatever you can find, be it something you created from scratch, or something from the interwebs. Make sure you are respecting the license though.

### Code
Use of libraries like gdx-ai, gdx-pay, Ashley or other 3rd party libs is absolutely allowed. Use of tools like Tiled, Overlap2D, etc. is absolutely allowed. Code you've written before is allowed.

What is NOT allowed is re-skinning an existing game!

### Prizes & Voting

[RoboVM](https://robovm.com/) and [Robotality](http://robotality.com/blog/) are sponsoring prices. The former will sponsor devices (Mac Mini, iPad and iPod Touch) whilst the latter will sponsor 20 Steam keys for [Halfway](http://halfwaygame.com/). Our partner [itch.io](http://itch.io/) is providing us with a super fancy game jam system that allows voting, which is a lot of work on their end already.

After the jam period of 4 weeks, we'll have 1 week of voting on itch.io. The winner of that community vote will get the Mac Mini. The iPad and iPod Touch will be awarded to the participants that 1) submitted an entry and 2) documented their development the most in a dev log and on Twitter, using tags that promote our sponsors (RoboVM, Robotality and itch.io). The dev logs will all be gathered in a single thread here on the forum. The tweets should be of the following form:

* *New screeny of my #libGDXJam game. Progress! @robotality @robovm*.
* *New dev log entry for my #libGDXJam game @robovm @robotality*.

The Halfway keys will be awarded to 20 random submissions.

## Survival guide

All hail ye who are brave enough to take up on the #libGDXJam challenge, have you done all the preparations prior the competition?

> What preparation?

Make sure you have decided on:

* Libraries: obviously Libgdx, but... Are you using any extensions or third party libraries?
* Programming language and IDE: Java, Kotlin, Clojure or Scala? Eclipse, Intellij, Netbeans, Sublime, Vim, Emacs?
* Graphic editors
* Level editors
* Sound generators and editors

**Golden advice**: use what you already know. If you experiment too much, you will most likely not finish a game on time!

### Tools & Resources

#### Audio

[BFXR](http://www.bfxr.net/): web based sound effect generator.

[[images/bfxr.png]]

[Audiotool](http://www.audiotool.com/): web based music synthesizer.

[[images/audiotool.png]]

[Audacity](http://audacity.sourceforge.net/): open source audio editor.

[[images/audacity.png]]

Free sound effects and music:

* [FreeSound](https://www.freesound.org/)
* [SoundCloud](https://soundcloud.com/)
* [Open Music Archive](http://openmusicarchive.org/)
* [CC Mixter](http://dig.ccmixter.org/)
* [Indie Game Music](http://www.indiegamemusic.com/)

#### Graphics 

[Gimp](http://www.gimp.org/): open source image editor.

[[images/gimp.png]]

[Paint.NET](http://www.getpaint.net/index.html): open source image editor.

[[images/paint.net.jpg]]

[Inkscape](https://inkscape.org/en//): open source vector editor.

[[images/inkscape.png]]

[Spine](http://esotericsoftware.com/): skeletal 2D animation tool.

[[images/spine.png]]

[Blender](http://www.blender3d.org/): open source 3D editor.

[[images/blender.jpg]]

Free game art:

* [Open Game Art](http://opengameart.org/)
* [Kenney](http://kenney.nl/)
* [Lost Garden](http://www.lostgarden.com/)
* [Game Art 2D](http://www.gameart2d.com/)

#### Map editors

[Tiled Map Editor](http://www.mapeditor.org/): open source tile based editor.

[[images/tiled.png]]

[Overlap2D](http://overlap2d.com/): open source game editor.

[[images/overlap2d.png]]

[BDX](https://github.com/GoranM/bdx): Open source 3D game engine integrated with Blender.

![](https://lh5.googleusercontent.com/-66Li4J9maL4/VP8ICZcGAxI/AAAAAAAABIo/Mth2R1Qo81w/w1038-h586-no/BlenderWithBDX2.png)

### Team structure

Gather your quest party and put your specialist hats on!

#### Programmers

* Do the programmy bits
* Split taks among them: graphics, controls, physics, UI
* The less code overlap, the easier!
* Need to tell the artists what formats they need
* Need to define how level designers create content

#### Graphics and audio artists

* Do the artsy bits
* Need to spit up tasks among them: UI, background, characters, effects...
* Agree on a consistent style
* May need to create placeholder art early on

#### Game and Level designer

* Does the content bits
* Needs to define the game mechanics
* Needs to define the game progression
* Needs to create levels
* Needs to playtest and give feedback to programmers and artists

#### Coordinator

* Makes sure everyone knows what to do
* Keeps track of things to be done
* Keeps track of dependencies between members
* Keeps track of time
* Keeps track of human needs (food, sleep)

**Note**: depending on your actual team, some roles could be blurred or shared.
* No artists? Use pre-existing art.
* No designers? Everybody becomes a games designer.
* No coordinator? Pick one person.
* Alone? You do everything!

### The 5 phases of Jamming

Now let's get onto it!

#### Brainstorming

Goals:
* Consider the set theme.
* Get a high-level understanding of your game: genre, mechanics, setting, story, art style...
* Take time limits into account. FPS, MMO and RTS games might not be your best choices.
* Think outside the box.

To-do:
  1. Gather ideas from everyone.
  2. Pick the most promising ones via vote.
  3. Define genre and mechanics using pen and paper.
  4. Define setting and story.
  5. Define art style, artist could draw quick mockups.

#### Setup

Goals:
* Get a detailed understanding of your game:
  * What will programmers have to do?
  * What will artists have to do?
  * What will game designers have to do?
* Define interfaces between all team members
  * How do programmers work with each other?
  * How do artists get their art into the game?
  * How do game designers create game content?
* Define tasks and their order for every team member!
  * The coordinator is responsible for keeping track of tasks

To-do
  1. Programmers agree on platform and tools
  2. Artists agree on style
  3. Programmers and artists agree on how to get art into the game
  4. Programmers and game designers agree on how to create content
  5. Each subteam defines their initial task
  6. Coodinator keeps track of things

A super lightweight [Kanban](https://en.wikipedia.org/wiki/Kanban)-like board can help, such as [Trello](https://trello.com/).

#### Implementation

Goals:
* Get the damned game done!
* Ensure to have a playable prototype early
  * Prioritize tasks accordingly
  * Game mechanics first to see if they are fun!
* Realize you'll likely not get everything done!
  * Which is why you should have something playable at all times
  * Cut corners, kill features, focus on the core of your game

To-do
  1. Every sub-team works on their task
  2. Coordinator keeps track of progress
  3. Sub-teams talk whenever they need to re-define or prioritize new tasks
  4. Goto 1

**Have something playable early on**

Tips for programmers:
  * Use source control (git, SVN...), do not use shared drives, zip files nor e-mail
  * Don't code for re-use
  * Don't optimize
  * Try to create a modular-design so people don't depend on each other too much
  * Make sure the game designer can create content as early as possible
  * Make sure artists export to easy to use formats
  * Make sure artists and game designers understand limitations

Tips for artists:
  * Make it easy to export your art into the proper format
  * Make sure everyone uses the same coordinate system/resolution
  * Use descriptive names for files
  * Have one shared folder (Dropbox, Google Drive) containing assets ready to integrate into the game
    * Don't put multiple versions of the same thing there
    * Have whatever local folder structure for work in progress assets

Tips for game designers
  * Talk to the developers about what's possible and what not
  * Focus on simple mechanics but try to put in a twist
  * Favor simple level-design over brainy or complex levels, they take too long to design!
  * If you have down-time, try to help or be the coordinator
  


#### Finishing up!
