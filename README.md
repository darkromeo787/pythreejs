pythreejs
=========

This is a _significant_ re-work of the pythreejs extension that introduces an "autogen" script that generates the majority of the ipython-widget code to wrap each of three.js's types.  It also takes a different view towards the pythreejs API.  Whereas pythreejs adds custom functionality to the classes, sometimes renaming, etc., this approach attempts to mimic the low-level three.js API as closely as possible, opening up the possibility for others to build utility libraries on top of this.  

This branch does not support all the functionality of the current pythreejs, but it is a significant step forward in terms of the potential to support the majority of three.js's features.  Currently supported features:
- Basic building blocks of a scene:
  - Textures (both Image-based and Data-based)
  - Materials
  - Geometries
  - Meshs
  - Scene, Lights, Cameras, WebGLRenderer

---

The autogen script, `generate-wrappers.js`, takes advantage of a config file `three-class-config.js` to auto-generate both javascript and python files to define the ipywidget wrappers for each three.js class.  The generated javascript files will have `.autogen.js` as the extension.  The generated python files have `_autogen.py` as their extension.  The script uses the handlebars template system to generate the various code files.

The autogen solution allows for overriding the default behavior of the generated classes.  E.g., if `Object3D.js` is present, then it will be loaded into the namespace as opposed to loading `Object3D.autogen.js`.  It is up to the author of the override classe to decide whether to inherit behavior from the autogen class or not.  Same goes for the python modules.  This allows for writing custom methods on both the python and javascript side when needed.  

The autogen script relies on a json-like config file (`three-class-config.js`) to describe the classes.  Reasonable defaults should take care of most, but it allows specifying the base class, constructor args, etc. for each of the wrappers.  A base version of this file can be generated by `generate-class-config.js`, but beware, it overwrites any customization to the config file that has already been done.

## Setup

```
# Initial setup
cd js
npm install

# Generate files
npm run autogen

# Build distribution files
npm run build

# Clean out generated files
npm run clean
```

List of relevant files:

```
js/package.json
js/webpack.config.js

# Files for auto-generation
js/scripts/clean-generated-files.js
js/scripts/generate-class-config.js
js/scripts/generate-wrappers.js
js/scripts/prop-types.js
js/scripts/templates/js_index.mustache
js/scripts/templates/js_param_obj.mustache
js/scripts/templates/js_wrapper.mustache
js/scripts/templates/py_top_level_init.mustache
js/scripts/templates/py_wrapper.mustache
js/scripts/three-class-config-defaults.js
js/scripts/three-class-config.js
js/scripts/three-class-config.js.backup

# New bases classes for supporting wrapper classes
js/src/_base/RendererPool.js
js/src/_base/Three.js
js/src/_base/enums.js
pythreejs/_base/Three.py
pythreejs/enums.py
pythreejs/traits.py

# Overridden classes
js/src/core/Object3D.js
js/src/textures/DataTexture.js
js/src/renderers/WebGLRenderer.js
pythreejs/core/Object3D.py
pythreejs/renderers/WebGLRenderer.py

# Custom classes
js/src/textures/ImageTexture.js  # created to ease image texture loading
js/src/textures/TextTexture.js  # created to ease creation of textures of rasterized text

# Updated examples notesbooks
examples/Examples.ipynb
examples/Geometries.ipynb
examples/Textures.ipynb
examples/renderer_limit.ipynb
examples/test.ipynb
examples/img/checkerboard.png
examples/img/earth.jpg

# Pulled in latest version of examples files from three.js source
js/src/examples/Detector.js
js/src/examples/controls/MomentumCameraControls.js
js/src/examples/controls/OrbitControls.js
js/src/examples/controls/TrackballControls.js
js/src/examples/js/controls/OrbitControls.js
js/src/examples/renderers/CanvasRenderer.js
js/src/examples/renderers/Projector.js
```


TODO: Everything below here is probably outdated. 

Getting Started
---------------

### Try it online with [Binder](http://mybinder.org/)

[![Binder](http://mybinder.org/badge.svg)](http://mybinder.org:/repo/jovyan/pythreejs/notebooks/examples)

Installation
------------

Using pip:

```
pip install pythreejs
jupyter nbextension enable --py --sys-prefix pythreejs
```

Using conda

```
$ conda install -c conda-forge pythreejs
```

For a development installation (requires npm):

```
$ git clone https://github.com/jovyan/pythreejs.git
$ cd pythreejs
$ pip install -e .
$ jupyter nbextension install --py --symlink --sys-prefix pythreejs
$ jupyter nbextension enable --py --sys-prefix pythreejs
```

Note for developers: the `--symlink` argument on Linux or OS X allows one to
modify the JavaScript code in-place. This feature is not available
with Windows.
