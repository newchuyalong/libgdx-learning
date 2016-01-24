Some Android devices have a gyroscope sensor that provides information about the rate of rotation in rad/s around a device's x, y, and z axis.

NOTE: The gyroscope is currently not available on iOS devices since there is no implementation in the RoboVM - backend yet.

Querying whether the gyroscope is available works as follows:

```java
boolean gyroscopeAvail = Gdx.input.isPeripheralAvailable(Peripheral.Gyroscope);
```

Once you determined that the gyroscope is indeed available, you can poll its state:

```java
float gyroX = Gdx.input.getGyroscopeX();
float gyroY = Gdx.input.getPitch();
float gyroZ = Gdx.input.getRoll();
```