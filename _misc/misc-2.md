---
title: "Index of smallest element in a list in Python?"
excerpt: "Really easy right? But what's the fastest option?<br/><img src='/images/SOargmin.png'>"
collection: misc
author_profile: false
---

Say that you have a list `values = [3,6,1,5]`, and need the index of the smallest element, i.e. `index_min = 2` in this case.

Avoid the standard solution using `itemgetter()`:

<pre>
import operator
index_min, _ = min(enumerate(values), key=operator.itemgetter(1))
</pre>

and use instead:

<pre>
index_min = min(xrange(len(values)), key=values.__getitem__)
</pre>


because it doesn't require to `import operator` nor to use `enumerate`, and it is always faster(benchmark below) than a solution using `itemgetter()`.

If you are dealing with numpy arrays or can afford `numpy` as a dependency, consider also using

<pre>
import numpy as np
index_min = np.argmin(values)
</pre>

This will be faster than the first solution even if you apply it to a pure Python list if:

 - it is larger than a few elements (about 2**4 elements on my computer)
 - you can afford the memory copy from a pure list to a `numpy` array

as this benchmark points out:<br/>
<img src='/images/SOargmin.png'>


I have run the benchmark on my computer with python 2.7 for the two solutions above (blue: pure python, first solution) (red, numpy solution) and for the standard solution based on `itemgetter()` (black, reference solution).
The same benchmark with python 3.5 showed that the methods compare exactly the same of the python 2.7 case presented above.

The code running the benchmark for python 3.X follows. This is based on my original Stackoverflow post [here](https://stackoverflow.com/a/11825864/1409938)

<pre>
import sys
import random
from timeit import timeit

from numpy import array, fromiter, empty, arange
from numpy.random import rand

# progress bar taken from Brian Khuu's answer at:
# http://stackoverflow.com/questions/3160699/python-progress-bar/15860757#15860757
def update_progress(progress):
    bar_length = 20  # Modify this to change the length of the progress bar
    status = ""
    if isinstance(progress, int):
        progress = float(progress)
    if not isinstance(progress, float):
        progress = 0
        status = "error: progress var must be float\r\n"
    if progress < 0:
        progress = 0
        status = "Halt......\r\n"
    if progress >= 1:
        progress = 1
        status = "Done......\r\n"
    block = int(round(bar_length * progress))
    text = "\rPercent: [{0}] {1}% {2}".format(
        "#" * block + "-" * (bar_length - block), progress * 100, status)
    sys.stdout.write(text)
    sys.stdout.flush()


code_itemgetter = """\
min_index, min_value = min(enumerate(L), key=operator.itemgetter(1))
"""

code_getitem = """\
min_index = min(range(len(L)),key=L.__getitem__)
min_value = L[min_index]
"""

code_numpy = """\
Lnp = numpy.array(L)
min_index = Lnp.argmin()
min_value = Lnp[min_index]
"""
l_max = 10
lens = 2 ** arange(1, l_max + 1)
l_tot = lens.sum()

t_itemgetter, t_getitem, t_numpy = empty(l_max), empty(l_max), empty(l_max)

state = 0
for n in arange(l_max):
    L = rand(lens[n]).tolist()
    t_itemgetter[n] = timeit(code_itemgetter, number=10000,
                             setup='import operator;from __main__ import L')
    t_getitem[n] = timeit(code_getitem, number=10000,
                          setup='from __main__ import L')
    t_numpy[n] = timeit(code_numpy, number=10000,
                        setup='import numpy;from __main__ import L')
    state += lens[n] / l_tot
    update_progress(state)

import matplotlib.pylab as plt

fig, (ax, bx) = plt.subplots(1, 2)
ax.plot(lens, t_itemgetter, 'k', label='itemgetter()', lw=2)
ax.plot(lens, t_getitem, 'b', label=' __getitem__()', lw=2)
ax.plot(lens, t_numpy, 'r', label=' np.argmin()', lw=2)
ax.set_xlabel('len(list)')
ax.set_ylabel('time required for the calculation [ms]')
ax.set_xscale('log', basex=2)
ax.set_yscale('log')
ax.legend(loc=2)
ax.grid()

bx.set_xscale('log', basex=2)
bx.plot(lens, 100 * (1 - t_getitem / t_itemgetter), 'b', lw=2)
bx.plot(lens, 100 * (1 - t_numpy / t_itemgetter), 'r', lw=2)
bx.set_ylabel('speed improvement over itemgetter solution [%]')
bx.set_xlabel('len(list)')
bx.axhline(color='k', lw=0)

# bx.set_ylim(-10, 80)
bx.grid()
fig.show()
</pre>
