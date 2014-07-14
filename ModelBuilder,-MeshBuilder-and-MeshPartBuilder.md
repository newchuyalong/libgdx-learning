# ModelBuilder
[ModelBuilder](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/ModelBuilder.html) [(code)](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/ModelBuilder.java) is a utility class to create one or more [models](https://github.com/libgdx/libgdx/wiki/Models) on code. It allows you to include one or more [nodes](https://github.com/libgdx/libgdx/wiki/Models#nodes), each node consisting of one or more [parts](https://github.com/libgdx/libgdx/wiki/Models#nodepart). It does, however, not support building a node hierarchy (child nodes). Be aware that building a model on code can be a costly operation and might trigger the garbage collector.
## Building one or more models
To start building a model use the `begin()` method, after which you must call the `end()` when you're done building the model. The `end()` method will return the newly created model. You can build multiple models using the same ModelBuilder, but not at the same time. For example:
```
ModelBuilder modelBuilder = new ModelBuilder();

modelBuilder.begin();
... //build one or more nodes
Model model1 = modelBuilder.end();

modelBuilder.begin();
... //build one or more nodes
Model model2 = modelBuilder.end();
```
## Managing resources
Keep in mind that models contain one or more meshes and therefore [needs to be disposed](https://github.com/libgdx/libgdx/wiki/Models#managing-resources). A model build via ModelBuilder will always be responsible for disposing all meshes it contains, even if you provide the Mesh yourself. Do not share a Mesh along multiple Models.

A Model build via ModelBuilder will not be made responsible for disposing any textures or any other resources contained in the [materials](https://github.com/libgdx/libgdx/wiki/Material-and-environment). You can, however, use the `manage(disposable)` method to make the model responsible for disposing resources. For example:
```
ModelBuilder modelBuilder = new ModelBuilder();
modelBuilder.begin();
Texture texture = new Texture(...);
modelBuilder.manage(texture);
... //build one or more nodes
Model model = modelBuilder.end();
... //use the model and when done:
model.dispose(); // this will dispose the texture as well
```
## Creating nodes
To start building a new node inside the model you can use the `node()` method. This will add a new node and make it active for building. It will also return the `Node` so you can reference it for later use or for example set its `id`.
```
ModelBuilder modelBuilder = new ModelBuilder();
modelBuilder.begin();
Node node1 = modelBuilder.node();
node1.id = "node1";
node1.translation.set(1, 2, 3);
...//build node1
Node node2 = modelBuilder.node();
node2.id = "node2";
...//build node2
Model model = modelBuilder.end();
```
Note that you don't have to keep a reference to the `Node`, a typical use-case would be:
```
ModelBuilder modelBuilder = new ModelBuilder();
modelBuilder.begin();
modelBuilder.node().id = "node1";
...//build node1
modelBuilder.node().id = "node2";
...//build node2
Model model = modelBuilder.end();
```
Using the `node()` for the first node is optional. For example, for models consisting of only a single node, you can immediately start building the node without having to call the `node()` method.
## Creating node parts
To add a `NodePart` to the current node you can use one of the `part(...)` methods. A NodePart is basically the combination of a `MeshPart` and `Material`. You must always supply the material. For the `MeshPart`, `ModelBuilder` allows you to specify the (part of the) mesh yourself, or to start building the `MeshPart` using a `MeshPartBuilder`.

> Keep in mind that the `Model` will always be made responsible for disposing the `Mesh`, regardless the method used to create part.

A `MeshPartBuilder` is an interface (implemented by `MeshBuilder, see below) which contains various helper methods to create a mesh. If you use the `part(...)` method to construct the MeshPart using a MeshPartBuilder, then ModelBuilder will try to combine multiple parts into the same Mesh. This will in most cases reduce the number of Mesh binds. This is only possible if the parts are made up using the same vertex attributes. For example:
```
ModelBuilder modelBuilder = new ModelBuilder();
modelBuilder.begin();
MeshPartBuilder meshBuilder;
meshBuilder = modelBuilder.part("part1", GL20.GL_TRIANGLES, Usage.Position | Usage.Normal, new Material());
meshBuilder.cone(5, 5, 5, 10);
Node node = modelBuilder.node();
node.translation.set(10,0,0);
meshBuilder = modelBuilder.part("part2", GL20.GL_TRIANGLES, Usage.Position | Usage.Normal, new Material());
meshBuilder.sphere(5, 5, 5, 10, 10);
Model model = modelBuilder.end();
```
This will create a model consisting of two nodes. Each node consisting of one part. The Mesh of both parts is shared, so there's only a single Mesh created for this Model. Note that in this example the vertex attributes are specified using a bit mask, which will cause `ModelBuilder` to create default (3D) `VertexAttributes`. You could also specify the [`VertexAttributes`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/VertexAttributes.html) yourself. 

> Because `ModelBuilder` reuses the `MeshPartBuilder` instances for multiple parts, you cannot build multiple parts at the same time. Per `ModelBuilder` you can only build one `Model`, `Node` and `MeshPart` at any given time.

See the MeshPartBuilder section below for more information on how to use the `MeshPartBuilder` to create the shape of the part.
# MeshBuilder
[MeshBuilder](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/MeshBuilder.html) [(code)](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/MeshBuilder.java) is a utility class to construct one or more [meshes](https://github.com/libgdx/libgdx/wiki/Meshes), optionally consisting of one or more [MeshParts](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/model/MeshPart.html). While a `MeshBuilder` is typically constructed and maintained by the `ModelBuilder` using the `ModelBuilder#part(...)` method, it is possible to use `MeshBuilder` without using a `ModelBuilder`. For this, you can use the `begin(...)` method to start building a `Mesh`, after which you must call the `end()` method when you're done building the mesh. The begin methods accepts various arguments to indicate the vertex attributes and optionally primitive type (required when not using parts). The `end()` method will also return the newly created `Mesh`:
```
MeshBuilder meshBuilder = new MeshBuilder();
meshBuilder.begin(Usage.Position | Usage.Normal, GL20.GL_TRIANGLES);
...//build the mesh
Mesh mesh = meshBuilder.end();
```

> Keep in mind that the `Mesh` must be disposed when you no longer need it.

Use the `part(...)` method to create a Mesh consisting of multiple parts. This will create a new `MeshPart` and set it active for building.
```
MeshBuilder meshBuilder = new MeshBuilder();
meshBuilder.begin(Usage.Position | Usage.Normal);
MeshPart part1 = meshBuilder.part("part1", GL20.GL_TRIANGLES);
... // build the first part
MeshPart part2 = meshBuilder.part("part2", GL20.GL_TRIANGLES);
... // build the second part
Mesh mesh = meshBuilder.end();
```
While the `part(...)` method returns the `MeshPart` so you can reference it for later use, it will not be valid until the `end()` method is called.

`MeshBuilder` implements `MeshPartBuilder`. Creating the actual shape of the (part of the) mesh, is described in the MeshPartBuilder section.

# MeshPartBuilder
[MeshPartBuilder](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/MeshPartBuilder.html) is a utility interface which supplies various methods for creating a (part of a) mesh. You can either use `ModelBuilder.part(...)` or construct a `MeshBuilder` to obtain a `MeshPartBuilder`. All methods of the `MeshPartBuilder` interface can only be called as long as the `MeshPart` is being build (most commonly between the call to the `part(...)` method and the call to the next `part(...)` or `end()` method of either `ModelBuilder` or `MeshBuilder`).

Use the `getMeshPart()` method to obtain the `MeshPart` currently being build.
Use the `getAttributes()` method to obtain the `VertexAttributes` of the `Mesh` being build.

A `VertexAttribute` of `Usage.Position` (either 2D or 3D) is required. There are no further restriction on the specified vertex attributes. However, most (especially higher level) methods are only implemented for position, normal, color (either packed or unpacked) and texture coordinates attributes. If you use other attributes as well, then a default (commonly zero) value will be used.

Most methods allow multiple signatures to specify the values for the vertex attributes. A little helper class `VertexInfo` is used to specify these on a per vertex basis. For example the `rect(...)` method accepts a `VertexInfo` for each corner, instead of using a method with all possible combinations or arguments for the vertex values of each corner. Be aware that the `VertexInfo` keeps track of whether a specific value has been set, therefor you should always use the setXXX methods. Use `null` in case you want to unset a value.

Use the `setColor(...)` method to specify the default color that will be used when the `VertexAttributes` contain a color `VertexAttribute`, but no color is set in e.g. the `VertexInfo`. For example:
```
meshPartBuilder.setColor(Color.RED);
VertexInfo v1 = new VertexInfo()v1.setPos(0, 0, 0).setNor(0, 0, 1).setCol(null).setUV(0, 0);
// the same for v2, v3 and v4
meshPartBuilder.rect(v1, v2, v3, v4);
```
In this example, because the `VertexInfo` has no color set (`setCol(null)`), the default will be used which is set to a red color.

Use the `setUVRange` to specify the default texture coordinates range that will be used when no texture coordinates are specified. Along with the default color, this is especially useful for simple shapes where only positions are needed and other vertex information can be derived from the shape. For example:
```
meshPartBuilder.setColor(Color.RED);
meshPartBuilder.setUVRange(0.5f, 0f, 0f, 0.5f);
meshPartBuilder.rect(0,0,0, 1,0,0, 1,1,0, 0,1,0, 0,0,1); // the last three arguments specify the normal
```
Use the `setVertexTransform` to supply a transformation matrix that should be applied to all vertices following after that call.