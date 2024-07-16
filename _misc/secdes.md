---
title: "Webapp for frame section design"
excerpt: "useful for the design of a frame section in civil engineering <br/><img src='/images/frame_design_shot.png'>"
collection: misc
author_profile: false
---

This was a side project during my PhD.

The frontend is written in HTML5 and javascript, using [Bootstrap](http://getbootstrap.com/), raw scripting of the HTML5 canvas [element](http://en.wikipedia.org/wiki/Canvas_element) for the live sketch of the frame section, the library [jsxgraph](http://jsxgraph.uni-bayreuth.de/wp/) for plotting, and [Webpack](https://webpack.js.org/) for packaging.


The backend runs [Uvicorn](https://www.uvicorn.org/) on top of a [Gunicorn](http://gunicorn.org/) server. It exposes a REST API by means of a [FASTapi](https://fastapi.tiangolo.com/) Python app that relies for the calculation on a [Cython](http://cython.org/) module that wraps a C++11 library. The latter takes care of 2D computational geometry calculations on sets of polygons, disks and straight lines.

The testing of the backend is carried out with [Pytest](http://pytest.org/latest/) on the python side and with [Catch](https://github.com/philsquared/Catch) on the C++ side. The python part of the application is pip-installable using [distutils](https://docs.python.org/2/library/distutils.html), and the build system of the C++ part is [Scons](http://www.scons.org/). The deployment is carried out with multi-stage Docker containers on [Heroku](http://heroku.com/).
