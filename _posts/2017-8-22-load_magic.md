---
layout: post
title: Save typing with %load magic!
tags: python, jupyter
---


Tired of typing the same lines at the beginning of every Ipython notebook? Use some %load magic!

I find myself typing the same lines at the top of of almost every Ipython/Jupyter notebook I create. I import numpy, pandas, matplotlib, etc. and set some other defaults. There had to be a better way of doing this! Thats where the %load magic comes in. I created a file *jupyter_imports.py* with my standard imports:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

# make plots look nice
plt.rcParams['font.size'] = 14
plt.rcParams['axes.labelsize'] = 'large'
plt.rcParams['xtick.labelsize'] = 'large'
plt.rcParams['ytick.labelsize'] = 'large'
plt.rcParams['lines.linewidth'] = 3
```

Then when I create a new notebook, I simply use
```python
%load jupyter_imports.py
```

One worry I had with this method is that it would make it harder to share notebooks, since other people would not have my  *jupyter_imports.py* file. But no problem! When the %load is executed the 1st time, it actually pastes in the contents, and comments out the %load line:

```python
# %load /Users/Andy/jupyter_imports.py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

# make plots look nice
plt.rcParams['font.size'] = 14
plt.rcParams['axes.labelsize'] = 'large'
plt.rcParams['xtick.labelsize'] = 'large'
plt.rcParams['ytick.labelsize'] = 'large'
```
