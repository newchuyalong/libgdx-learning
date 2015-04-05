Clojure is a dialect of Lisp, written for the JVM and with functional programming in mind. Clojure comes with native Java interoperability, making it able to leverage the powerful existing libraries in the Java ecosystem. [ClojureTV on YouTube](http://www.youtube.com/user/ClojureTV) has a lot of good videos, specifically [Clojure for Java Programmers](http://www.youtube.com/watch?v=P76Vbsk_3J0) [(Part 2)](http://www.youtube.com/watch?v=hb3rurFxrZ8).

## play-clj ##

The [play-clj](https://github.com/oakes/play-clj) library provides a Clojure wrapper for libGDX. To get started, install [Leiningen](http://leiningen.org/) and run the following command:

    lein new play-clj hello-world

A directory called `hello-world` should appear, and inside you'll find directories for `android`, `desktop`, and `ios`. Inside the `desktop` directory, you'll find a `src-common` directory, which contains the game code that all three projects will read from. Navigate inside of it to find `core.clj`, which looks like this:

```clojure
(ns hello-world.core
  (:require [play-clj.core :refer :all]
            [play-clj.ui :refer :all]))

(defscreen main-screen
  :on-show
  (fn [screen entities]
    (update! screen :renderer (stage))
    (label "Hello world!" (color :white)))

  :on-render
  (fn [screen entities]
    (clear!)
    (render! screen entities)))

(defgame hello-world
  :on-create
  (fn [this]
    (set-screen! this main-screen)))
```

This will display a label on the bottom left corner, which you can see by running `lein run` inside the `desktop` directory. To generate a JAR file that you can distribute to other people, run `lein uberjar` and grab the file in the `target` directory that contains the word "standalone".

## Links ##

* The [play-clj tutorial](https://github.com/oakes/play-clj/blob/master/TUTORIAL.md) provides a more in-depth walk-through on how to use the library.
* The [Nightcode](https://sekao.net/nightcode/) IDE allows you to create and run a play-clj project without installing Leiningen. You simply click the "New Project" button, give it a name, and choose the Game template.
* The [Nightmod](https://sekao.net/nightmod/) game tool provides an easier way to use play-clj by integrating the game and the text editor together so you can see instant results when you save your code.