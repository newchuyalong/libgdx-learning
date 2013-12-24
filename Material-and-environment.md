In practice, when rendering, you are specifying what (the shape) to render and how (the material) to render. The shape is specified using the Mesh (or more commonly the MeshPart), which defines the vertices attributes for the shader. The material is most commonly used to specify the uniform values for the shader.

Uniforms can be grouped into model specific (e.g. the texture applied or whether or not to use blending) and environmental uniforms (e.g. the lights being applied or an environment cubemap). Likewise the 3D api allows you to specify a material and environment.

## Materials
Materials are model (or modelinstance) specific. You can access them by index `model.materials.get(0)`, by name `model.getMaterial("material3")` or by nodepart `model.nodes.get(0).parts(0).material`. Materials are copied when creating a ModelInstance, meaning that changing the material of a ModelInstance will not affect the original Model or other ModelInstances.

The Material class extends the Attributes class, see below for more information about Attributes.

## Environment
An Environment contains the uniform values specific for a location. For example, the lights are part of the Environment. Simple applications might use only Environment, while more complex applications might use multiple environments depending on the location of a ModelInstance. A ModelInstance (or Renderable) can only contain one Environment though.

The Environment class extends the Attributes class, see below for more information about Attributes.

## Attributes
Both the Environment and Material classes extend the Attributes the class. Most commonly, the Attributes class is used to specify uniform values. For example a TextureAttribute can be used to specify an uniform to bound for a shader. However, attributes don't have to be uniforms, for example DepthTestAttribute is used to alter the opengl state and doesn't set an uniform.

The Attributes class is most comparable with a Set. It can contain at most one value for each attribute, just like an uniform can only be set to one value. Theoretically both the Material and Environment can contain the same attribute, the actual behavior in this scenario depends on the shader used, but in most cases the Materials attribute will be used instead of the Environment attribute.

### Attribute type
Every attribute has a `type` long value, which is a bitmask used to identify the attribute. Therefor a complete material or environment can be represented with a single long, where each bit represents an attribute. Some attribute classes are dedicated to a single type value (bit). Others can be used for multiple type values (bits), in which case you must specify the type on construction. For example:
```
Attribute attribute = new ColorAttribute(ColorAttribute.Diffuse, Color.RED);
```
note that the ColorAttribute class contains a convenience method to do the same:
```
Attribute attribute = ColorAttribute.createDiffuse(Color.RED);
```

### Custom attribute types
It is possible to use a standard attribute class for a new custom type. For example when you want to use the ColorAttribute class to specify a custom type of color. There are three things you must consider in that case:
1. Name your attribute type. Each attribute type must have an _unique_ alias (name). You might want to (but don't have to) use the uniform name for that. The alias will also be used for debugging, e.g. when calling `attribute.toString()`.
2. Register your attribute type. This is to make sure there is only one attribute type for each bit. This can be done only from within the Attribute class or a subclass.
3. Make ColorAttribute accept the custom type. The ColorAttribute class and most other attribute classes checks the type on construction, this allows you to cast attributes without having to check anything other then the type. For example `(ColorAttribute)material.get(ColorAttribute.Diffuse)` will always work because ColorAttribute is the only attribute accepting the Diffuse type.

Because of step 2 and 3, you must extend the Attribute class to add a custom attribute type:
```
public class MyColorAttribute extends ColorAttribute {
    public final static String MyColorAlias = "myColor"; // step 1: name the type
    public final static long MyColor = register(MyColorAlias); // step 2: register the type
    static {
        Mask |= MyColor; // step 3: Make ColorAttribute accept the type
    }
}
```
You can then create the custom attribute type using:
```
Attribute attribute = new ColorAttribute(MyColorAttribute.MyColor, Color.RED);
```

### Custom attributes
It is possible to create a custom attribute, in which case the process is not much different from above. You must extend the Attribute class, register at least one type and of course add some data to pass on to the shader. For example to add an attribute to pass a double value to the shader:
```
public class DoubleAttribute extends Attribute {
    public final static String MyDouble1Alias = "myDouble1";
    public final static long MyDouble1 = register(MyDouble1Alias);
    public final static String MyDouble2Alias = "myDouble2";
    public final static long MyDouble2 = register(MyDouble2Alias);
    protected static long Mask = MyDouble1 | MyDouble2;
    /** Method to check whether the specified type is a valid DoubleAttribute type */
    public static Boolean is(final long type) {
        return (type & Mask) != 0;
    }

    public double value;

    public DoubleAttribute(final long type) {
        super(type);
        if (!is(type))
            throw new GdxRuntimeException("Invalid type specified");
    }

    public DoubleAttribute(final long type, final double value) {
        this(type);
        this.value = value;
    }
}
```
Of course `MyDouble1Alias`, `MyDouble1`, `"myDouble1"`, `MyDouble2Alias`, `MyDouble2` and `"myDouble2"` should be replaced by a more meaningful description.