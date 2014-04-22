Clojure is a dialect of the Lisp programming language, written for the JVM and with functional programming in mind. Clojure comes with native Java interoperability, making it able to leverage the powerful existing libraries in the Java ecosystem. There are a few existing sources for use of Clojure game development using Libgdx.

This article assumes you have [Leiningen](http://leiningen.org/) installed, along with moderate knowledge of Clojure, or a Lisp. [ClojureTV on YouTube](http://www.youtube.com/user/ClojureTV) has a lot of good videos, specifically [Clojure for Java Programmers](http://www.youtube.com/watch?v=P76Vbsk_3J0) [(Part 2)](http://www.youtube.com/watch?v=hb3rurFxrZ8).

## project.clj ##

Because Libgdx is not hosted on any of Leiningen's default maven repos, we need to add our own, in the Sonatype repos.

```clojure
(defproject cljdx "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :license {:name "Apache License, Version 2.0"
          :url "http://www.apache.org/licenses/LICENSE-2.0.html"
          :distribution :repo
          :comments "Same as LibGDX"}
  :dependencies [[org.clojure/clojure "1.6.0"]
                 [com.badlogicgames.gdx/gdx "1.0.1-SNAPSHOT"]
                 [com.badlogicgames.gdx/gdx-backend-lwjgl "1.0.1-SNAPSHOT"]
                 [com.badlogicgames.gdx/gdx-platform "1.0.1-SNAPSHOT"
                  :classifier "natives-desktop"]]
  :repositories [["sonatype"
                  "https://oss.sonatype.org/content/repositories/snapshots/"]]
  :source-paths ["src/clojure"]
  :java-source-paths ["src/java"]
  :aot :all
  :main cljdx.desktop-launcher)
```

The code can be translated in a fairly straightforward manner:

here is the equivalent to Main.java, which we are naming `cljdx.desktop-launcher`:

```clojure
(ns cljdx.desktop-launcher
  (:require [cljdx.core])
  (:import [com.badlogic.gdx.backends.lwjgl LwjglApplication])
  (:gen-class))

(defn -main
  []
  (LwjglApplication. (cljdx.core.Game.) "cljdx" 800 600))
```

and here is the Screen implementation (as `cljdx.core`)

```clojure
(ns cljdx.core
  (:import [com.badlogic.gdx Game Gdx Graphics Screen]
           [com.badlogic.gdx.graphics Color GL20]
           [com.badlogic.gdx.graphics.g2d BitmapFont]
           [com.badlogic.gdx.scenes.scene2d Stage]
           [com.badlogic.gdx.scenes.scene2d.ui Label Label$LabelStyle]))

(gen-class
  :name cljdx.core.Game
  :extends com.badlogic.gdx.Game)

(def main-screen
  (let [stage (atom nil)]
    (proxy [Screen] []
      (show []
        (reset! stage (Stage.))
        (let [style (Label$LabelStyle. (BitmapFont.) (Color. 1 1 1 1))
              label (Label. "Hello world!" style)]
          (.addActor @stage label)))
      (render [delta]
        (.glClearColor (Gdx/gl) 0 0 0 0)
        (.glClear (Gdx/gl) GL20/GL_COLOR_BUFFER_BIT)
        (doto @stage
          (.act delta)
          (.draw)))
      (dispose[])
      (hide [])
      (pause [])
      (resize [w h])
      (resume []))))

(defn -create [^Game this]
  (.setScreen this main-screen))
```

and thats all the code necessary! a `lein uberjar` will generate the required runnable jar!

## Games written in Clojure using Libgdx ##

* [Bounce Away](http://www.badlogicgames.com/forum/viewtopic.php?f=16&t=6792&sid=5617bf292ecdeb67b09b2bdc2c4f6a69)

## Misc. info ##

* The [Nightcode](https://nightcode.info/) clojure and Java IDE provides a wonderful Libgdx clojure template.
* An example of the Simple Game tutorial in Clojure (without the sounds though) can be found from http://clojuregames.blogspot.com/2013/10/the-libgdx-simple-game-demo-in-clojure.html
* The [play-clj library](https://github.com/oakes/play-clj) provides a Clojure wrapper for LibGDX.
* More info would be greatly appreciated, there can certainly be more Polyglot Libgdx out there!