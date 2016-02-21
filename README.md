Nanonet
=======

Nanonet provides recurrent neural network basecalling via currennt. Event data
is extracted from .fast5 files to create feature vectors to input into a
pretrained network. Output is as a single .fasta file.

Nanonet leverages currennt to run recurrennt neural networks. Currennt is
generally run with GPUs to aid performance but can be run in a CPU only
environment. 

Installation on Ubuntu
----------------------

The following was tested to work on a clean Ubuntu 14.04 Vagrant virtual machine.

**Installation of currennt**

Oxford Nanopore Technologies have provided a fork of currennt with minor
modifications to that which is available on
https://sourceforge.net/projects/currennt/. The changes serve to allow
currennt to run with current versions of Nvidia graphics cards and libraries.

Compilation of currennt requires a few dependencies to be fulfilled. We must
first install the NVIDIA CUDA libaries. Note that the NVIDIA website has two
versions of this package, a local and a network install. Here we use the
network package.

    # Install NVIDIA's meta package
    wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1410/x86_64/cuda-repo-ubuntu1410_7.0-28_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1410_7.0-28_amd64.deb

    sudo apt-get update
    sudo apt-get install -y cuda

At this point if you wish to use a GPU with currennt you will need to restart
your machine. Upon restart issuing the command:

    nvidia-smi

should show information about your GPU.

Building currennt has some further, more standard, requirements:

    sudo apt-get install libboost-all-dev libnetcdf-dev netcdf-bin cmake

after which we can build currennt:

    cd currennt
    mkdir build && cd build
    cmake ..
    make
    sudo cp currennt /usr/local/bin

A successful build will result in a single executable file named `currennt`.

**Installation of nanonet**

Nanonet is a mostly python with a single C library for performing Viterbi
decoding on the probability matrix output by currennt. Installation is
complicated only by the fact that currennt requires as input netCDF files.
We must first install some prequisites:

    sudo apt-get install -y netcdf-bin libhdf5-dev python-h5py python-numpy cython 

Nanonet should then install quite trivially using the standard python
mechanisms:

    cd nanonet
    python setup.py install --user


Installation on OSX
-------------------

Installation on Mac OSX mirrors that on Ubuntu. The following was performed on
a Mac OSX 10.11.3 development machine. Additional dependencies may be required
from those listed below. Most Macs do not have a CUDA enabled GPU.

**Installation of currennt**

First download and install NVIDIAs CUDA package:

    http://developer.download.nvidia.com/compute/cuda/7.5/Prod/local_installers/cuda_7.5.20_mac.dmg

It is not neccessary to install the samples.

Ensure that you have Xcode and Xcode Command Line Tools installed and that you
have a softlink to the /Developer folder present:

    sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer /Developer

Currennt can then be built as for Ubuntu:

    cd currennt
    mkdir build && cd build
    cmake ..
    make
    sudo cp currennt /usr/local/bin


**Installation of nanonet**
The easiest way to install the netCDF dependencies is via homebrew:

    brew install hdf5 netcdf

The python components can be installed using the setup script:

    cd nanonet
    python setup.py install --user


Installation on Windows
-----------------------
The following build of currennt was performed on Windows 10.

**Boost**

Download prebuild boost libraries from:

    https://sourceforge.net/projects/boost/files/boost-binaries/1.55.0-build2/boost_1_55_0-msvc-12.0-64.exe/download

and install to c:\boost_1_55_0 (this is not the default). If you choose a
location you will have to modify the Visual Studio project.

**Visual Studio**

Download and install Visual Studio Community 2013 with Update 5 from

    https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx

Do not download Visual Studio 2015, the NVIDIA CUDA libraries do not support
it.

**NVIDIA CUDA**

Download and install NVIDIA CUDA for Windows from:

    https://developer.nvidia.com/cuda-downloads

If you receive a message that no version of Visual Studio can be found, it is
likely that you installed Visual Studio 2015.
    
**Building currennt**

Open the Visual Studio solution currennt.sln. Ensure that the solution
configuration is set to Release on x64 (in the toolbar right below the
main menu). Compile the whole solution via Build > Build Solution. The
following files will be produced which should be copied to a location
from which you wish to run currennt:

    currennt/Release/cudart32_75.dll
    currennt/Release/cudart64_75.dll
    currennt.exe

To the same location you should copy all .dll files located under the curennt
folder.

**Installing nanonet**

Various python distributions are available for Windows. Here we will use a
the standard installer available from:

    https://www.python.org/downloads/release/python-2711/

Download and install the x86-64 version of Python 2.7 choosing to install
python into the path (not done by default).

__Installing python libraries__

Nanonet requires libraries for reading and writing HDF5 and netCDF4 files.
These libraries ordinarily must be compiled from source, however Christophe
Golke maintains a repository of compiled python wheels at:

   http://www.lfd.uci.edu/~gohlke/pythonlibs/

From this page locate and download the following packages:

    numpy-1.11.0b3+mkl-cp27-cp27m-win_amd64.whl
    h5py-2.5.0-cp27-none-win_amd64.whl
    netCDF4-1.2.2-cp27-none-win_amd64.whl

For each of these run the following at a command prompt:

    pip install <package>

__Compiling clib_viterbi__

The C components on nanonet all most easily compiled using the Visual Studio
2013 compiler installed above. To do this from a command prompt run

    cd nanonet/nanonet
	"C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\x86_amd64\vcvarsx86_amd64.bat"
	"C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\x86_amd64\cl.exe" /LD c_log_viterbi.c /link /out:clib_viterbi.dll


__Setup nanonet__

To finally set up nanonet in order for use run:

    cd nanonet
    python setup.py develop install



Peforming basecalling
---------------------



