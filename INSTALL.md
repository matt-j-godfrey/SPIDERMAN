# SPIDERMAN

welcome to spiderman!

Read the docs at [spiderman.readthedocs.io](http://spiderman.readthedocs.io)

Or check out some of the example ipython notebooks in the example folder

---

# SPIDERMAN installation (conda, macOS Apple Silicon) - MATTS

These instructions install SPIDERMAN into a dedicated conda environment and build the compiled C extension (`spiderman._web`).

If you are cloning https://github.com/tomlouden/SPIDERMAN.git (the upstream/original repository), you will need to apply a few small compatibility edits (`setup.py` and `spiderman/plot.py`) as described below.

If you are using my fork/branch that already includes these fixes, you can skip the “Patch upstream” step.

---

## 1) Create the conda environment

```bash
conda create -n spiderman_env -c conda-forge -y --override-channels \
  python=3.10 \
  "numpy=1.22.*" \
  scipy \
  matplotlib \
  pillow \
  openjpeg \
  pip \
  "setuptools<60" \
  wheel

conda activate spiderman_env
```

Check that worked okay:

``` bash
python -c "import sys; print(sys.version)"
python -c "import numpy as np; print('numpy', np.__version__)"
python -c "from PIL import Image; print('PIL ok')"
```

Hopefully all good

## 2) Install dependencies

``` bash
conda install -c conda-forge -y astropy fitsio
python -m pip install batman-package
```

if batman fails, try: 
``` bash
conda install -c conda-forge -y batman-package
```

## 3) Clone SPIDERMAN

``` bash
cd /path/where/you/want/to/save/spiderman
git clone https://github.com/matt-j-godfrey/SPIDERMAN.git
cd SPIDERMAN
rm -rf build *.egg-info spiderman/_web*.so
```

If cloning from my github fork, no changes are needed.
If cloning from Tom's (https://github.com/tomlouden/SPIDERMAN.git), then will need to make the following changes:

edit setup.py: change
- license = ['GNU GPLv3'] -> license = 'GNU GPLv3'
- ”from distutils.core import setup” -> ”from setuptools import setup”
- comment out "import numpy.distutils.misc_util"
- move "include_dirs=[np.get_include()]" to inside the "_web" object

edit plot.py: change
- "from matplotlib._png import read_png" to "from matplotlib.image import imread as read_png"


## 4) Build compiled extension

``` bash
python setup.py build_ext --inplace
```

and check the .so file is created, should see something like "_web.cpython-310-darwin.so.":

``` bash
ls -l spiderman/_web*.so
```

## 5) Install

``` bash
python setup.py install
```

Done! (hopefully)

## Appendix

I have tested the "quickstart.ipynb" and "Brightness maps.ipynb" and it seems to work fine. Some of the plots look a bit clunky in a Jupyter notebook, but seems to work. 

You will need to update the "% matplotlib inline" call to "# matplotlib inline" if using in a Jupyter notebook, since this is quite old and using the regular Python interpreter. If it still does not work reinstall the inline feature without updating the numpy package:

``` bash
python -m pip uninstall -y matplotlib-inline
python -m pip install --no-deps matplotlib-inline==0.1.6
```

The code has some warnings when running, I would imagine just due to being outdated/deprecated, but hopefully that is not a problem.

The full list of dependencies are found in
environment_full.yml
and 
environment.yml