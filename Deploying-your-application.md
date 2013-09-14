### The mechanism to deploy your game differs between platforms. ###

# fixme: ToC

## Deploy to Windows/Linux/Mac ##
The easiest way to deploy to Windows/Linux/Mac is to create a runnable JAR in Eclipse, which includes all of your code, the code of libgdx and any extensions, and your assets. To create a runnable JAR:

  1. Right click your desktop project, select "Export"
  2. In the next dialog, select "Java -> Runnable Jar", click "Next"

<img src="http://libgdx.badlogicgames.com/uploads/Screen%20Shot%202013-08-23%20at%2011.12.45-xTDyPTSMu2.png" width="400"></img>
  3. In the next dialog, specify the launch configuration you use to start the game on the desktop from within Eclipse, and select the output JAR file name

<img src="http://libgdx.badlogicgames.com/uploads/Screen%20Shot%202013-08-23%20at%2011.16.10-0STXrLHoAH.png" width="400"></img>

This will package all code and your assets into a single JAR file. To run the JAR file, a user can just double click it, provided she has Java installed.

## Deploy to Android ##
TBD

## Deploy to iOS ##
TBD

## Deploy to HTML/JS ##
Deploying to HTML/JS is straightforward for most cases.
  1. Right click your html project and select "Google -> GWT Compile"
  2. Keep the default settings and click compile.
  3. A window will appear asking you to select the WAR directory. It is in you html project's folder.
  4. GWT will proceed to cross-compile your code into javascript. It will take awhile, but you can view the progress by pulling up the console in Eclipse.

Once the compile is complete everything you need to run your game on the web will be inside the war directory. Unless you are using RPC calls or other server side operations in your game, there are only a few things left to do.
  1. Copy the "index.html" file, your assets folder, and the folder ending in "mygdxgame.GWTDefinition" to a directory on your web server.
  2. Navigate to the URL and you should see a loading screen for your game. 

Notes:
  * If you are using server-side operations in your code, you will need to install Tomcat or similar software on your web server and place the full contents of your project's war directory in the "webapp" directory. More details [here.](https://tomcat.apache.org/tomcat-6.0-doc/appdev/deployment.html)  