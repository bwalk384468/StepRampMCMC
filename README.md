StepRampMCMC
=========================


Matlab+CUDA code to run the stepping/ramping model comparison described in
[Latimer et al, *Science*  2015](http://pillowlab.princeton.edu/pubs/abs_Latimer15_Sci.html).

This code takes in spike data observed over many trials, fits "stepping"
and "ramping" models to the data, and compares the model fits with the Deviance
Information Criterion (DIC).

*********************

### Requirements: ###

- MATLAB version R2013a (or above) with statistics toolbox
- Nvidia CUDA toolkit (version 7.0 recommended)
- Nvidia graphics card (compute version 1.3 or above. See the list provided by Nvidia at https://developer.nvidia.com/cuda-gpus  At least 1GB of
graphics memory is recommended. Apologies to AMD users.) The code uses double precision floating point arithmetic. Although the code will run on Nvidia cars with the proper compute version, it may be extremely slow in some lines of gaming cards. For example, the GTX 900 series runs double precision operations at approximately 1/32 the speed of single precision operations.


### Recommended: ###

-   Ubuntu 15.04
-   16+GB RAM
-   NVidia Titan, Titan Z, or Tesla card


Release Notes
===============

The tools provided run in MATLAB and use Nvidia's CUDA toolkit to accelerate
compution. Running this package requires an Nvidia GPU.
This code has been tested with MATLAB R2013a with CUDA version 7.0 running on
Ubuntu 15.04. The graphics card used was an Nvidia Titan Z.

Previous versions of this package have been tested on Windows 7 and Mac OSX.
However, this release is targeted towards Ubuntu users.
Compiling the CUDA tools on OSX works similarly to the compilation on Ubuntu.
If running this code on Windows is absolutely necessary, the author may provide
limited support in getting the GPU code functional on windows.

Setup / Installation
====

Make sure you have installed the CUDA toolkit from https://developer.nvidia.com/cuda-downloads
We recommend installing the CUDA samples to the 'samples' folder of your CUDA install directory.

To complete the CUDA setup, you must include the following lines to the end of your
`.bashrc` file in your home directory:

    export PATH=/usr/local/cuda-7.0/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/cuda-7.0/lib64:$LD_LIBRARY_PATH

(make sure the directories match your CUDA install directory!)

To compile the CUDA files into MEX files that can be executed by
MATLAB, `cd` into the directory `code/CUDAlib/` and then:

1.   Edit `myPath.m` and execute `myPaths;`  
 This tells the compiler where to find your MATLAB and CUDA
 installations. The MATLAB directory should be detected automatically. However, you may need to set the correct CUDA path.
 If your GPU has compute version less than 3.0, you will need to change the GPUArchitecture variable to match your GPU's compute version.
 If executing this function reports an error, open `myPaths.m` and edit the
 relevant path name so it points to the correct directory.
 
2.   Execute `compileAllMexFiles;`  
   This should compile/link all scripts. 
    
**WARNING**: Using these CUDA tools and MATLAB's native GPU functions  in the same
         session may cause a library conflict that will crash MATLAB.

### Known problems and warning messages setting up the CUDA/MEX files:

-    `Compiler error: "mex.h" not found`  
     **Solution**: this probably means your MATLAB directory is not set up properly
              in code/CUDAlib/myPath.m
    
-    ``Linking error: undefined reference to `__cxa_atexit'``  
      **Solution**: append `-lm -lstdc++` to the `CXXLIBS` option in your `mexopts.sh` file

-    MATLAB's mex linker may report a gcc version warning such as 
     `Warning: You are using gcc version "4.9.1".  The version
     currently supported with MEX is "4.4.x".` 
     **Solution**: We have tested that this warning can be ignored in MATLAB R2013a.

-    Compiler gives a warning about a CUDA header file such as
     ``/usr/local/cuda/samples/common/inc/exception.h``
     **Solution**: This problem is associated with older versions of CUDA and can be ignored. Upgrading to version 7.0 eliminates this warning.
     
-    Receiving error message while running the example script `Warning: Unknown problem with sampling drift variance (most likely numerical error).` 
     **Solution**: The CUDA files may not have compiled correctly. Check the `GPUArchitecture` variable in coda/CUDAlib/myPath.m to make sure it matches your GPU's compute version and recompile.

This code was made before the current MATLAB versions (R2015) with better built-in functionality between gpuArrays and mex files.
Therefore, we include our own wrapper to place data on the GPU from MATLAB for mex functions along with these specific compiling functions.


Running the Model Comparison
===================

The script `exampleScript.m` simulates data from one of the two models and runs
the model comparison function.

This script calls `code/runModelComparison.m`, which takes the data in the form of a `timeSeries` object described in
the function header. The `timeSeries` is a processed form of the spike data
observed over all trials. Only the window of interest in each trial is included.
(i.e., spikes before integration and after integration have already been pruned)

For examples of what the `timeSeries` structure should look like, see the 
functions

-    `simulateRampingModel.m`
-    `simulateSteppingModel.m`

These functions generate a simulated `timeSeries` object with all the necessary
parts.

All settings, including how many MCMC samples to use, are given in
    `setupMCMCParams.m`



