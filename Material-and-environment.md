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

Every attribute has a `type` long value, which is a bitmask used to identify the attribute. Therefor a complete material or environment can be represented with a single long, where each bit represents an attribute.
