---
layout: post
title:  "Theano and Lasagne Installation with GPU support on Windows!"
date:   2016-12-16 12:12:06 +1300
categories: machine-learning theano windows gpu
---

THEANO & LASAGNE
-------------------------------

Theano is a cross-platform python based low-level ML library. Basically it does all the maths.

Lasagne is a higher-level NN wrapper for Theano to abstract the creation of NNs in a much easier way,
by creating them layer upon layer (meat, spinach, pasta, chesse sauce... the layers you need for a lasagne).

## Some brief points
-----------------------------------

This is not an easy installation, nor are the following installation instructions going to be complete.
This set of instructions contains many of the steps involved in getting two separate machines up to the 
point of full use of Lasagne but each was already a dev machine with quite different setups. In essence,
you need the following software, roughly installed in this order, and you should make sure you are using
64bit versions of everything as appropriate.

- Bash
- Git
- GCC
- Python 2.7 (using conda or another package to give jupyter and ipython)
- All the lasagne dependencies in python
  - numpy
  - scipy
  - mingw
  - libpython
- BLAS
- Theano
- Lasagne
- CUDA (i.e., GPU support)
- nvcc
- cuDNN (i.e., GPU NN optimisation)

The following guides were of a lot of assistance to me
- http://deeplearning.net/software/theano/install.html
- https://github.com/Lasagne/Lasagne/wiki/From-Zero-to-Lasagne-on-Windows-7-(64-bit)

## Installation
----------------------------------

https://github.com/Lasagne/Lasagne/wiki/From-Zero-to-Lasagne-on-Windows-7-(64-bit) as an 
additional reference. This install is heavily based on theirs, but with adjusted instructions
that have worked for me. If there is not enough detail in a section, check this as a reference
for more info.

In particular, getting theano running within a conda environment so that you can have more versions
of python installed is kinda important if you're going to be messing around with other similar libraries.
Theano is not python 3 compatible **YET** and so you will need to be going with 2.7.

### Bash for windows

https://git-scm.com/download/win

### GCC
Install TDM GCC compiler 64 bit and ensure that it is the default gcc compiler by adjusting path variables

```
$ gcc --version
gcc.exe (tdm64-1) 5.1.0
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

http://tdm-gcc.tdragon.net/

### Python
Need to have anaconda with python 2.7, 64bit everything

https://www.continuum.io/downloads

```
$ python -V
Python 2.7.12 :: Anaconda 4.2.0 (64-bit)
```

### python dependencies

```
conda create --clone root --name snakes python=2 # for multi-conda users

source activate snakes

conda remove conda-env
```

You may have to change conda for conda.bat at this point (conda.exe may not be in the path any more, and *.bat may not
be an autocomplete file extension. You can change this in system environment variables)

```

conda install six nose numpy scipy # can also install pip if not in a cloned environment and force the upgrade

conda install mingw libpython

```

### Theano

```

d: (You should use your own directory here)

cd Git

pip install --upgrade --no-deps git+git://github.com/Theano/Theano.git

# git clone https://github.com/Theano/Theano.git

# cd Theano

# python setup.py develop && python setup.py install # Uncertain which works

```

And test it 

```
python -i # import theano

conda list

$ conda list | grep Thea
Theano                    0.9.0.dev4                <pip>

```
### Lasagne

```
pip install --upgrade https://github.com/Lasagne/Lasagne/archive/master.zip # lasagne

python -i

import lasagne

$ conda list | grep asagn
Lasagne                   0.2.dev1                  <pip>

```

### Ensuring BLAS is installed 

BLAS is Basic Linear Algebra Subprograms. I.e., this is going to be doing the really low level stuff, so worth making sure it's good, and right

```

python src/install/check_blas.py

```

You are looking for "with direct Theano binding to blas". I don't have notes on installation of this however https://github.com/Lasagne/Lasagne/wiki/From-Zero-to-Lasagne-on-Windows-7-(64-bit)#blas will give you some info


### CUDA

CUDA == GPU support

I have tested this on CUDA versions:

- 7.5 (GTX 780)
- 8.0 (GTX 1060)

Install CUDA from nvidia site https://developer.nvidia.com/cuda-toolkit

Add a new System Environment Variable (either for your user, or for all) THEANO_FLAGS='floatX=float32,device=gpu0,lib.cnmem=1'

If you are having issues with NVCC stuff because of .net 4 - https://stackoverflow.com/questions/32091593/cannot-install-windows-sdk-7-1-on-windows-10 - follow the ISO instructions there

You will need to create a file in your home folder "C:/users/[user name here]" called .theanorc.txt and you should add 

```
[global]
device=gpu
floatX=float32

[nvcc]
compiler_bindir=C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\bin\amd64
flags=--cl-version=2010
```

to that file. After running the check_blas.py file, you should get something like 

```
We executed 10 calls to gemm with a and b matrices of shaUsing gpu device 0: GeForce GTX 1060 6GB (CNMeM is enabled with initial size: 95.0% of memory, cuDNN not available)
pes (5000, 5000) and (5000, 5000).

Total execution time: 0.78s on GPU.

```

### cuDNN

https://developer.nvidia.com/rdp/cudnn-download - you will need to register first as well. Make sure you get the correct version for the CUDA library you installed (7.5 or 8.0)

Installation is just copy pasting the three folders into `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\Vx.x`

```
check_blas.py


We executed 10 calls to gemm with a and b matrices of shaUsing gpu device 0: GeForce GTX 1060 6GB (CNMeM is enabled with initial size: 95.0% of memory, cuDNN 5105)
pes (5000, 5000) and (5000, 5000).

Total execution time: 0.70s on GPU.
```

### nolearn

```
pip install nolearn
```
Really... that's all there is to it.

This wraps lasagne and will allow for saving neural networks, and structures, even easier

### Notes

- Depending on PATH and the extensions system variable, you may need to replace conda with conda.bat (if it doesn't already include *.bat in path files)


