Libgdx has interfaces for direct OpenGL ES 1.0, 1.1 and 2.0 access. OpenGL ES is emulated on the desktop by mapping OpenGL ES functions to desktop OpenGL functions. On some platforms (iOS, GWT/HTML) only OpenGL ES 2.0 is supported.

## Precision modifiers ##
OpenGL ES 2.0 requires the specification of precision modifiers for attributes, uniforms and locals. Desktop OpenGL does not support this. You will have to guard against that in your fragment shader with something similar to this code snippet:

```java
#ifdef GL_ES 
#define LOW lowp
#define MED mediump
#define HIGH highp
precision mediump float;
#else
#define MED
#define LOW
#define HIGH
#endif
```

This will define the LOWP, MED, and HIGH macros to equivalent OpenGL ES precision modifiers and sets the default precision for float to medium. This will only happen on platforms actually running OpenGL ES, on the desktop, the macros are defined to be empty.