When a user presses the back button on an Android device, this usually kills the currently running activity. Games might chose to display a confirmation dialog before letting the user exit. For that to work one needs to catch the back key so it is not passed on to the operating system:

```java
Gdx.input.setCatchBackKey(true);
```

You will still receive key events if you have registered an [[InputProcessor|Event Handling]], but the operating system will not close your application.

Note that the general paradigm in Android is to have the back key close the current activity. Deviating from this is usually viewed as bad practice.

Another key that might need to be caught is the menu key. If uncaught, it will bring up the on-screen keyboard after a long press. Catching this key can be done as follows:

```java
Gdx.input.setCatchMenuKey(true);
```