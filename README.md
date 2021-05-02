# pyglslify

A very thin wrapper for the [glslify](https://github.com/glslify/glslify) nodejs tool, to use [stack.gl](http://stack.gl) shader modules in Python during development.

(after development you're probably best just compiling the shaders with nodejs `glslify` cli tool to a file and using the result)

## Installation

The whole point is to be able to download and use GLSL shader modules from the WebGL community, which are distributed via `npm`.

So first you need to install nodejs/npm: https://www.npmjs.com/get-npm

Then install the `glslify` tool globally and verify it is on your path:
```
npm install -g glslify
glslify --help
```

Then add `pyglslify` in your Python project virtualenv:
```
pip install pyglslify
```

## Usage

We're still going to use `npm` to install stack.gl shaders. For example:

```
npm install glsl-noise
```

This will create `package.json` and `package-lock.json` files in your project root, which will track the `npm` dependencies of your project. These should be committed to source control along with the rest of your project. By default the modules will be downloaded to a `node_modules` dir in your project root, that dir should be added to `.gitignore`.

If you checkout your project repo on a new machine you can just run `npm install` to fetch the modules specified as dependencies in `package.json`.

On the Python side you can write GLSL shaders which make use of the glslify `require` pragma:
```glsl
#pragma glslify: noise = require('glsl-noise/simplex/3d')

precision mediump float;
varying vec3 vpos;

void main () {
    gl_FragColor = vec4(noise(vpos*25.0),1);
}
```
`fshader.glsl`

And then in your Python code, when you want to pass the shader to your rendering pipeline, use `pyglslify` to resolve the pragmas:
```python
import moderngl_window as mglw
from pyglslify import glslify


class RenderWindow(mglw.WindowConfig):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        vertex_shader = self.load_text("shaders/vshader.glsl")
        fragment_shader = self.load_text(
            glslify("shaders/fshader.glsl")
        )
        self.pipeline = self.ctx.program(
            vertex_shader=vertex_shader,
            fragment_shader=fragment_shader,
        )
```

You can also use glslify [Source Transforms](https://github.com/glslify/glslify#source-transforms).

e.g. `npm install glslify-hex` allows use of CSS-style hex strings for colors in place of `vec3`s.

See the glslify docs - they recommend specifying these as configuration in your `package.json`.

You can also apply local transforms to the shader file at runtime from Python, by passing their module names as args like:
```python
from pyglslify import glslify

fragment_shader = glslify("shaders/fshader.glsl", "glslify-hex", "glslify-import")
```

Happy shading!
