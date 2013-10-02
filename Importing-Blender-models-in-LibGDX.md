Blender is an open source modeling application you can use to create 3D models, scenes and animations. You can get Blender at [blender.org](http://www.blender.org/). If you are new to creating 3D models using Blender, you can checkout the [blender tutorials](http://www.blender.org/education-help/tutorials/). This page provides practical tips on preparing and converting your Blender model for use in LibGDX.

### Exporting to FBX and converting to G3DB
The default (preferred) method is to export to FBX. Make sure you select all and only those options (e.g. nodes and animations) you want to actually include. Don't include your camera, lights, etc. Next download the latest version of [fbx-conv](https://github.com/libgdx/fbx-conv) and convert the FBX file to G3DB. You'll need to flip texture coordinates by using the `-f` commandline option.
`fbx-conv -f file.fbx`

Optionally, you may also convert your file to the G3DJ format, which is a JSON format which is readily viewable with a simple text editor. `fbx-conv -o G3DJ file.fbx` Please note that G3DJ will take longer to load when you run your application, as it is not a binary format.

### Troubleshooting missing textures
Please ensure you limit the size of your texture files to power of two dimensions (e.g. 32x32, 64x64 etc). A maximum recommended size would be 1024x1024 for widespread support, however larger sizes will work. Devices may render a black object if the texture dimensions are not supported by the device.

To check the contents of your converted files, you may convert 

### RrSs warning
When using the Blender FBX exporter, you might receive a RrSs warning when converting the FBX file. This is due to the Blender FBX exporter wrongfully exporting the transformations. The fbx-conv utility will correct this and you can safely ignore the warning.