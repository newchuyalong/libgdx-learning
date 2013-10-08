Blender is an open source modeling application you can use to create 3D models, scenes and animations. You can get Blender at [blender.org](http://www.blender.org/). If you are new to creating 3D models using Blender, you can checkout the [blender tutorials](http://www.blender.org/education-help/tutorials/). This page provides practical tips on preparing and converting your Blender model for use in LibGDX.

### Exporting to FBX and converting to G3DB
The default (preferred) method is to export to FBX. Make sure you select all and only those options (e.g. nodes and animations) you want to actually include. Don't include your camera, lights, etc. Next download the latest version of [fbx-conv](https://github.com/libgdx/fbx-conv) and convert the FBX file to G3DB. You'll need to flip texture coordinates by using the `-f` commandline option.
`fbx-conv -f file.fbx`

Optionally, you may also convert your file to the G3DJ format, which is a JSON format which is readily viewable with a simple text editor. `fbx-conv -o G3DJ file.fbx` Please note that G3DJ will take longer to load when you run your application, as it is not a binary format.

Please note that there is a known limitation to using the FBX export in Blender. The current exporter only supports texface textures (i.e. textures assigned to an UV map). 

### Setting the coordinate system (up-axis)
The coordinate system Blender uses (z-up) is different compared to the most common system used for games (y-up). The Blender FBX exporter contains the option to change the coordinate system to y-up (which might be even the default in the FBX exporter), do not use this option, instead set it to Blender's default (z-up). 

Fbx-conv will compensate the coordinate system by rotating the model (to y-up). However it will only be able to do this, if the fbx file itself contains the correct information. The Blender FBX exporter option will not modify the model, instead it will simply act like its y-up (causing fbx-conv unable to compensate).

When fbx-conv needs to compensate the coordinate system, it will rotate all root nodes of the model 90 degrees along the X-axis. It will also modify any animations accordingly. The geometry (vertices) itself however, remains unchanged.

However, if you want to use z-up in your application, then you can set Blender's FBX exporter coordinate system option to y-up. This will cause fbx-conv not to rotate your model and animations, so it will be z-up.

### Troubleshooting missing textures

If your faces are not drawn, please check try disabling back face culling. Your faces may be missing because they are facing away from the camera. `DefaultShader.defaultCullFace = 0;`

### Troubleshooting black textures

Please ensure you limit the size of your texture files to POT (power of two dimensions) which are square shaped meaning of equal width and height (e.g. 32x32, 64x64 etc). A maximum recommended size would be 1024x1024 for widespread support, however larger sizes may still render, depending on device. Often non-POT textures render correctly on a desktop, but not on mobiles. This is a limitation specific to the GPU being used, and non-POT support will vary from device to device.

Additionally test that your lighting/color is configured in a way which will illuminate your model instance. A good test is to pass a null environment pointer to your Model Batch, which will disable lighting effects.

If you have used Blender's FBX export script, please ensure your textures were assigned to a UV map, as the export script only supports texface textures.


### RrSs warning
When using the Blender FBX exporter, you might receive a RrSs warning when converting the FBX file. This is due to the Blender FBX exporter wrongfully exporting the transformations. The fbx-conv utility will correct this and you can safely ignore the warning.