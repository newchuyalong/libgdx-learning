Most Android devices do not posses a hardware keyboard. Instead, a soft- or on-screen keyboard can be presented to the user. To bring up the on-screen keyboard we can call this method:

```java
Gdx.input.setOnscreenKeyboardVisible(true);
```

Once visible, any key presses will be reported as [[events | Event Handling]] to the application. Additionally, [[polling|Polling]] can be used to check for a specific key's state.

Note that there is currently a bug in the on-screen keyboard implementation when landscape mode is used. The default Android on-screen keyboard can be switched for a custom keyboard, and many handset manufacturers like HTC make use of this. Sadly, their keyboard implementations tend to be buggy which leads to problems described in this [issue](http://code.google.com/p/libgdx/issues/detail?id=431).

On-screen keyboard functionality is only available on Android.

### iOS on screen keyboard
See this forum post for a workaround: http://www.badlogicgames.com/forum/viewtopic.php?f=17&t=11788&hilit=ios+keyboard#p53019