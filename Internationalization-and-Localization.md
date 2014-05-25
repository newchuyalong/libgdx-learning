 * [Overview](#overview)
 * [Creating Properties Files](#creating-properties-files)
 * [Creating a Bundle](#creating-a-bundle)
 * [Fetch the Localized Strings](#fetch-the-localized-strings)
 * [Plural Forms](#plural-forms)
 * [GWT Limitations and Compatibility](#gwt-limitations-and-compatibility)
 * [Multiple Bundles](#multiple-bundles)

## Overview ##

Technically speaking, _internationalization_ is the process of designing a software so that it can potentially be adapted to various languages and regions without engineering changes.
 _Localization_ is the process of adapting internationalized software for a specific region or language by adding locale-specific components and translating text.
Due to the length of the terms, both internationalization and localization are frequently abbreviated to I18N and L10N respectively, where 18 and 10 stand for the number of letters between the initial and the final letters of the respective words.

Here we'll describe the cross-platform I18N system provided by LibGDX allowing you to make the L10N of your application.

Basically, the `I18NBundle` class is used to store and fetch strings that are locale sensitive. A bundle allows you to easily provide different translations for you application.


## Creating Properties Files ##

Conceptually each bundle is a set of properties files that share the same base name. In the example that follows `MyBundle` is the base name. The characters following the base name indicate the language code, country code, and variant of a Locale. `MyBundle_en_GB`, for example, matches the Locale specified by the language code for English (en) and the country code for Great Britain (GB).
For the sake of simplicity we omit the extension `.properties` from file names.

* MyBundle
* MyBundle_de
* MyBundle_en_GB
* MyBundle_fr_CA_VAR1

You should always create a **default properties file**. The name of this file begins with the base name of your bundle and ends with the `.properties` extension. In our example the contents of `MyBundle.properties` are as follows:
````
game=My Super Cool Game
newMission={0}, you have a new mission. Reach level {1}.
coveredPath=You covered {0,number}% of the path
highScoreTime=High score achieved on {0,date} at {0,time}
````

To support an additional Locale, your localizers will create an **additional properties file** that contains the translated values. No changes to your source code are required, because your program references the keys, not the values.

For example, to add support for the Italian language, your localizers would translate the values in `MyBundle.properties` and place them in a file named `MyBundle_it.properties`. Notice that the name of this file, like that of the default file, begins with the base name `MyBundle`, and ends with the `.properties` extension. However, since this file is intended for a specific Locale, the base name is followed by the language code (it). The contents of `MyBundle_it.properties` are as follows:

````
newMission={0}, hai una nuova missione. Raggiungi il livello {1}.
coveredPath=Hai coperto il {0,number}% del percorso
highScoreTime=High score ottenuto il {0,date} alle ore {0,time}
````
Notice that the key named `game` is missing from the Italian properties file since we want the game's name to remain unchanged regardless of the locale. As we'll see in the "parent chain" note below, you don't need to repeat such key/value pairs for each properties file.

As you may have already noticed, the strings in a properties file can contain parameters. These strings are commonly called patterns and follow the syntax specified by the `java.text.MessageFormat` API.
In short, a pattern can contain zero or more formats of the form `{index, type, style}` where the type and the style are optional.
Please, refer to the official javadoc of the MessageFormat class to learn all its features. 
Here I will bring to your attention only one interesting feature: formats are localizable. It means that typed data like number, date and time will be automatically expressed in the typical form of the specific locale. For example, the float number 3.14 becomes 3,14 for the Italian local (notice the comma in place of the decimal point).


## Creating a Bundle ##

An instance of the `I18NBundle` class manages the named strings for a locale after loading into memory the appropriate properties files.
Invoke the factory method `createBundle` to get a new instance of the desired bundle.
````java
FileHandle baseFileHandle = Gdx.files.internal("i18n/MyBundle");
Locale locale = new Locale("fr", "CA", "VAR1");
I18NBundle myBundle = I18NBundle.createBundle(baseFileHandle, locale);
````
**IMPORTANT NOTES:**
- **Default locale:** If you don't specify any locale when you invoke `createBundle` then the default locale is used.
- **Charset encoding:** Unlike `java.util.Properties` the default charset encoding is `UTF-8` (without BOM) so to avoid the use of the native2ascii tool to convert the file to an ISO-8859-1 properties file where all uncovered characters are escaped with the \uXXXX sequence. If, for whatever reason, you don't want to use UTF-8 encoding invoke one of the overloaded forms of the `createBundle` method that allows you to specify the desired encoding.
- **Loading strategy:** If a property file for the specified Locale does not exist, `createBundle` tries to find the closest match. For example, if MyBundle_fr_CA_VAR1 is the desired file and the default Locale is en_US, `createBundle` will look for files in the following order:
  * MyBundle_fr_CA_VAR1
  * MyBundle_fr_CA
  * MyBundle_fr
  * MyBundle_en_US
  * MyBundle_en
  * MyBundle
  
  Note that `createBundle` looks for files based on the default Locale before it selects the base file `MyBundle.properties`. If `createBundle` fails to find a match it throws a `MissingResourceException`. To avoid throwing this exception, you should always provide a base file with no suffixes.  


## Fetch the Localized Strings ##

To retrieve the translated value from the bundle, invoke the `get` method as follows:
````java
String value = myBundle.get(key);
````
The string returned by the `get` method corresponds to the specified key. The string is in the proper language, provided that a properties file exists for the specified locale.

Similarly, to retrieve the translated value from the bundle replacing the arguments at the same time, invoke the `format` method as follows:
````java
String value = myBundle.format(key, arg1, arg2, ...);
````

For example, to retrieve the strings from the properties files above your code might look like this:
````java
String game = myBundle.format("game");
String mission = myBundle.format("newMission", player.getName(), nextLevel.getName());
String coveredPath = myBundle.format("coveredPath", path.getPerc());
String highScoreTime = myBundle.format("highScoreTime", highScore.getDate());
````
**IMPORTANT NOTES:**
- **Parent chain:** As mentioned above, the `game` key is taken from the default property file since it's missing from locale-specific files. Especially, if no string for the given key can be found by the `get` method (or its parametric form `format`) a `MissingResourceException` is thrown.
- **Constant strings:** When a string has no arguments you might think that the methods `get` and `format` are equivalent. This is not entirely true. In fact, the `get` method returns the string exactly as it was specified in the properties file, while the `format` method might make some replacements even if no arguments are present. This brings us to the next point.
- **Escaping rule:** Since a left curly bracket is the first character of a placeholder of the form `{n}`, if you want to use it in your string you have to escape it somehow. The `MessageFormat` API provided by Java uses the single quote to start an escape sequence and you have to escape the single quote itself (by doubling it) if you want it to be part of your string. Unfortunately the rules for using single quotes within `MessageFormat`'s patterns have shown to be somewhat confusing. In particular, it isn't always obvious to localizers whether single quotes need to be doubled or not. For this very reason we decided to offer the simpler escaping rule below without limiting the expressive power of message format patterns:
    - A left curly bracket must be doubled if you want it to be part of your string.

    So, if you're used to MessageFormat's syntax, remember that now single quotes never need to be escaped.


## Plural forms ##

Plural forms are supported through the standard `choice` format provided by `MessageFormat`.
See the official documentation of the class `java.text.ChoiceFormat`.
I'm going to show you just an example. Let's consider the following property:
````
collectedCoins=You collected {0,choice,0#no coins|1#one coin|1<{0,number,integer} coins|100<hundreds of coins} along the path.
````
You can retrieve the localized string as usual:
````java
System.out.println(myBundle.format("collectedCoins", 0));
System.out.println(myBundle.format("collectedCoins", 1));
System.out.println(myBundle.format("collectedCoins", 32));
System.out.println(myBundle.format("collectedCoins", 117));
````
And the output result would be like the following: 
````
You collected no coins along the path.
You collected one coin along the path.
You collected 32 coins along the path.
You collected hundreds of coins along the path.
````

It's worth noting that the choice format can properly handle nested formats as we did with `{0,number,integer}` inside `{0,choice,...}`.



## GWT Limitations and Compatibility ##

As said before, the I18N system provided by LibGdx is cross-platform. However there are some limitations when it comes to the GWT back-end.
In particular:
- **Simple format:** The format syntax of `java.text.MessageFormat` is not fully supported. You'll have to stick to a simplified syntax where formats are made only by their index, i.e. `{index}`.
Format's type and style are not supported and cannot be used; otherwise an `IllegalArgumentException` is thrown.
- **Non localizable arguments:** Formats are never localized, meaning that the arguments passed to the `format` method are converted to a string with the `toString` method, so without taking into account the bundle's locale. 

If your application can run on both GWT and non-GWT back-ends, you should call [I18NBundle.setSimpleFormat(true)](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/utils/I18NBundle.html#setSimpleFormatter%28boolean%29) when the application starts. This way all subsequent invocations of the factory method `createBundle` will create bundles having the same behavior on all back-ends.
This is necessary because usually formats like `{0}` are localizable on non-GWT back-ends. For example, let's consider the property `msg={0}` and the call `myBundle.format("msg", MathUtils.PI);`.
If `simpleFormat` is set to `false` you'll get the localized string `3,14` for the Italian locale on a non-GWT back-end (notice the comma and the rounding) and the non-localized string `3.1415927` for the same locale on the GWT back-end.
On the contrary, if `simpleFormat` is set to `true` you'll get the non-localized string `3.1415927` for any locale on any back-end.  


## Multiple Bundles ##

Of course you can use multiple bundles in your application. For example, you might want to use a different bundle for each level of your game. Using multiple bundles offers some advantages:
   * Your code is easier to read and to maintain.
   * You'll avoid huge bundles, which may take somewhat long to load into memory.
   * You can reduce memory usage by loading each bundle only when needed.
