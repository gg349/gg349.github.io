---
title: "Webapp for frame section design"
excerpt: "useful for the design of a frame section in civil engineering <br/><img src='/images/frame_design_shot.png'>"
collection: misc
---

The webapp is deployed [here](http://section-design.herokuapp.com/), check it out.

The frontend is written in HTML5 and javascript, using: the framework [Bootstrap](http://getbootstrap.com/), raw scripting of the HTML5 canvas [element](http://en.wikipedia.org/wiki/Canvas_element) for the live sketch of the frame section, a JSON rest call in javascript to get the server to calculate the resistance domain and the library [jsxgraph](http://jsxgraph.uni-bayreuth.de/wp/) for plotting it. I use [MathJax](http://www.mathjax.org/) to guarantee the rendering of mathematical equations across all browsers

The backend is the HTTP [Gunicorn](http://gunicorn.org/) WSGI server, that runs a [Flask](http://flask.pocoo.org/) application, on [Heroku](http://heroku.com/) for the time being. The server exposes a REST call, that delegates to a python package the calculation, described [here](http://section-design.herokuapp.com/static/sectest.html). This package depends on a python module written in [Cython](http://cython.org/), which wraps a C++11 library for all the 2D computational geometry, which was written from scratch mostly based on existing literature. I devised the algorithm for splitting a set of polygons and disks into parts by a set of parallel lines, and computing the geometrical properties of the parts. It is described in [here](http://section-design.herokuapp.com/static/splitting.html), and covers all possible degenerate cases.

The testing of the backend is carried out with [Pytest](http://pytest.org/latest/) on the python side and with [Catch](https://github.com/philsquared/Catch) on the C++ side. The python part of the application is pip-installable using [distutils](https://docs.python.org/2/library/distutils.html), and the build system of the C++ part is [Scons](http://www.scons.org/).

The python module was compiled on [this](https://github.com/ejholmes/vagrant-heroku) vagrant box matching the Heroku Celadon Cedar stack, because a modern compiler, Scons and [libc++](http://libcxx.llvm.org/) are missing on Heroku. The binary is then uploaded to Heroku via git as it is.
