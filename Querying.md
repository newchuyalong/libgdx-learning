The `Application` interface provides various methods to query properties of the run-time environment.

### Getting the Application Type ###
Sometimes it is necessary to special case specific parts of an application depending on the platform it is running on. The `Application.getApplicationType()` method returns the platform the application is currently running on:

```java
switch(Gdx.app.getApplicationType()) {
   case ApplicationType.Android:
       // android specific code
   case ApplicationType.Desktop:
       // desktop specific code
   case ApplicationType.WebGl:
       /// HTML5 specific code
}
```

On Android, one can also query the Android version the application is currently running on:

```java
int androidVersion = Gdx.app.getVersion();
```

This will return the SDK level supported on the current device, e.g. 3 for Android 1.5.

### Memory Consumption ###
For debugging and profiling purposes it is often necessary to know the memory consumption, for both the Java heap and the native heap:

```java
long javaHeap = Gdx.app.getJavaHeap();
long nativeHeap = Gdx.app.getNativeHeap();
```

Both methods return the number of bytes currently in use on the respective heap.