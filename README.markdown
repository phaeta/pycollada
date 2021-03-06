INTRODUCTION
============

This little python module is intented to be an easy way to load COLLADA
(http://en.wikipedia.org/wiki/COLLADA) files. It only supports triangle
set objects, (phong,lambert) materials, textures, cameras, scenes and
transforms. I did it for my own use and that is all I need right now.
I'm putting this online in case somebody finds it useful. If you would like
to see this package evolving and maintained, please, leave a comment in my
blog (this post). Feel free to request things you would like to be supported.

INSTALLATION
============

Download the code, uncompress and go into the directory. Then you can either
run:

    $ python setup.py install

Or just copy the directory named "collada" to your project. In a place you
can import from.

DESIGN
======

My goal was not having a full COLLADA standar compliant reader, but an
easy to use API. If you are looking for a better feature support, have a look
to the blender collada export script here 
(http://colladablender.illusoft.com/cms/). It uses a core library to access
data, so I'm sure you can get to use it for your own purpose.

Saving back to collada format is partially done. You can't use it right now
but since I'm going to need it, it's going to be done soon or later. Again,
leave me a comment if you are also looking for this feature.

FEATURES
========

Supported features right now are:

* Geometry
  * Triangle collections
    * Vertices, normals
    * Multiple texture coord channels
    * Constraint: now it needs the object to define normals, I'll fix it
* Materials
  * Read params from phong and lambert shading models
  * Color and float properties
  * Texture support
    * Image loading if PIL is installed (only when reading zae,kmz)
* Scene
  * Geometry instantiation
  * Material binding
  * Camera binding
  * Transforms (matrix, rotate, scale, translate)

API OVERVIEW
============

Collada standar is a bit messy cause it is meant to be flexible and support
a lot of things. I've tried to keep this as simple as possible, but yet, there
are some things that can't be simplified without breaking collada structure.
Opening a file (dae, zae or kmz) is something like this:

    >>> import collada
    >>> col = collada.Collada(filename)

If something weird is found in the file it will raise an exception. You can
tell it to ignore some exceptions and continue loading. For instance, I'm sure
you'll want to avoid unsupported things giving problems to you, so you do:

    >>> col = collada.Collada(filename, ignore=[collada.DaeUnsupportedError])

To avoid that exception getting to your code.

The way to access data in the default scene is through objects() function in 
the scene. That is a generator that will make you iterate through all the 
objects of the given type. Something like:

    >>> for camera in col.scene.objects('camera'):
    >>>     print camera

Only two types of objects are now recognized, "camera" and "geometry". And yes,
the objects you get in the loop are already transformed according to the scene
nodes. So you can just iterate triangles like this:

    >>> for geom in col.scene.objects('geometry'):
    >>>     for prim in obj.primitives():
    >>>         for tri in prim.triangles():
    >>>             print tri.vertices

And those vertices are already transformed. If you want the untransformed data
you'll have to go through the geometry library in collada object, See API 
specs. Note that all geometry data you find through this API are numpy arrays.
So that vertices attribute you find in each triangle is something like:

    >>> array( [ [0.2, 0.3, 1.0],
    >>>          [0.5, 0.2, 1.0],
    >>>          [0.2, 0.2, 1.5] ], dtype=float32)

Three vertices, one for each point of the triangle. And it is the same with 
normals and texture coordinates. You have all the vertices together in arrays
at the TriangleSet primitive object. See API for details.

COLLADA OBJECT SUMMARY
======================

These are some attributes in collada objects you may want to explore:

* materials (list) and materialById (dict): Material library
* images (list) and imageById (dict): Image library
* cameras (list) and cameraById (dict): Camera library
* geometries (list) and geometryById (dict): Geometry library
* scenes (list) and sceneById (dict): Scene library
* scene: The default scene (you normally want only this)

EXAMPLE SCRIPT
==============

This is a small example script that prints out some information from a
collada file.
