# Guide for WRF-Hydro Model Installation in Standalone Mode

The WRF-Hydro an open-source community model, is used for a range of projects, including flash flood prediction, regional hydroclimate impacts assessment, seasonal forecasting of water resources, and land-atmosphere coupling studies. Initially developed by the National Center for Atmospheric Research (NCAR), WRF-Hydro supports various hydrologic applications, from flood forecasting to long-term water resource management. This guide provides step-by-step instructions for installing WRF-Hydro in Standalone Mode using the Noah-MP Land Surface Model, which enables it to run independently of the WRF atmospheric model.

## About this Guide

This installation guide is designed to help users:

1) Set up a working environment for compiling and running the WRF-Hydro model in standalone mode.

2) Download and install the necessary dependencies and libraries, such as zlib, HDF5, netCDF, MPICH, and others that enable WRF-Hydro’s high-performance and scientific capabilities.
3)  Configure and compile the WRF-Hydro model using specific compilation flags for optimized performance.

4) Run a test simulation using the Croton_NY test case as an example.

## Prerequisites

Before beginning the installation, ensure that you have:

1) A Linux-based system with a Bash shell environment.

2) Root or sudo access to install dependencies.

3) Familiarity with basic command-line operations, as well as experience in managing software installations on Linux.

4) Essential build tools installed, such as gcc, g++, and make. Additional compilers for Fortran are also required (gfortran is recommended).

- Required Libraries and Software: The WRF-Hydro model relies on several libraries to manage data processing and high-performance computation. Key libraries include:

- zlib: Provides compression capabilities, crucial for data handling in HDF5 and netCDF.
- HDF5: A hierarchical data format, essential for managing large data sets commonly found in scientific applications.
- netCDF: The network Common Data Form, which facilitates data sharing among scientific communities.
- MPICH: An implementation of the Message Passing Interface (MPI) standard, required for parallel processing capabilities.
- libpng: Enables PNG image support, used within visualization tools associated with WRF-Hydro.
- Jasper: Provides support for JPEG 2000 compression.

Each library will be configured, built, and installed with specific flags to ensure compatibility with WRF-Hydro’s requirements.

## Model Configurations

This guide will walk you through setting up the Noah-MP Land Surface Model configuration. The Noah-MP configuration provides an advanced representation of soil, vegetation, and snowpack processes, and it supports standalone simulations driven by predefined meteorological data. By setting specific flags, such as SPATIAL_SOIL and WRFIO_NCD_LARGE_FILE_SUPPORT, this setup maximizes WRF-Hydro’s capabilities for handling high-resolution datasets and complex environmental processes.

## Running WRF-Hydro
Once installation is complete, this guide includes instructions for downloading, setting up, and running a sample test case (Croton_NY). The test case demonstrates the essential model operations and verifies that the installation is functioning as expected. It will also help familiarize you with WRF-Hydro’s input and output file structure.

With this guide, you will be ready to leverage WRF-Hydro for hydrological research, forecasting, and operational applications in standalone mode.

## Create Directory Structure
```bash
mkdir $HOME/wh
ls -lstr
ls -d */
tree -d -L 1

cd $HOME/wh
mkdir Downloads
mkdir Libs
mkdir Libs/NETCDF
mkdir Libs/grib2
mkdir Libs/MPICH
tree -d
```


## Download Libraries
### Download zlib Library

```bash
### Change Directory:
cd Downloads
```

```bash
wget -c -4 https://github.com/madler/zlib/archive/refs/tags/v1.2.12.tar.gz
```

### Download HDF Library

```bash
wget -c -4 https://github.com/HDFGroup/hdf5/archive/refs/tags/hdf5-1_12_2.tar.gz
```

### Download netCDF-C Library

```bash
wget -c -4 https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.9.0.tar.gz
```
### Download netCDF-Fortran Library

```bash
wget -c -4 https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.6.0.tar.gz
```

### Download MPICH Library

```bash
wget -c -4 https://github.com/pmodels/mpich/releases/download/v4.0.2/mpich-4.0.2.tar.gz
```

### Download libpng Library

```bash
wget -c -4 https://download.sourceforge.net/libpng/libpng-1.6.37.tar.gz
```

### Download jasper Library

```bash
wget -c -4 https://www.ece.uvic.ca/~frodo/jasper/software/jasper-1.900.1.zip
```

## Setting Compilers

### Set GCC Compiler

```bash
export CC=gcc
```

### Set G++ Compiler

```bash
export CXX=g++
```

### Set Fortran Compiler

```bash
export FC=gfortran
export F77=gfortran
```

### Print Compiler Versions

###  Print GCC version

```bash
export gcc_version="$(gcc -dumpversion)"
echo "$gcc_version"
export gcc_version="$(gcc -dumpversion)" && echo "$gcc_version"
gcc -dumpversion
gcc --version
```

### Print gfortran version

```bash
export gfortran_version="$(gfortran -dumpversion)"
```

### Print G++ version

```bash
export gplusplus_version="$(g++ -dumpversion)"
```

## Setting Compiler Flags

```bash
export version_10="10"
if [ $gcc_version -ge $version_10 ] || [ $gfortran_version -ge $version_10 ] || [ $gplusplus_version -ge $version_10 ]
then
  export fallow_argument=-fallow-argument-mismatch 
  export boz_argument=-fallow-invalid-boz
else 
  export fallow_argument=
  export boz_argument=
fi
```
Note: This checks if the compiler versions are 10 or higher. If yes, it sets specific compiler flags for compatibility (-fallow-argument-mismatch and -fallow-invalid-boz). If no, it leaves these flags as empty variables.

## Pass Flags to Fortran Compilers

Note: 
- FFLAGS is typically used for general Fortran compilation flags.
- FCFLAGS is  flags specifically for the Fortran compiler.
  
```bash
export FFLAGS="$fallow_argument"
export FCFLAGS="$fallow_argument"
```

## Installing Main Libraries

### Install zlib

```bash
# Extracts the v1.2.12.tar.gz archive
Options:
-x: Extracts the files.
-v: Verbose mode, which displays the names of files as they are extracted.
-z: Specifies that the archive is compressed with gzip.
-f: Indicates that the following string is the name of the file.
tar -xvzf v1.2.12.tar.gz
# Changing to the Directory
cd zlib-1.2.12/
# Configuring the Build and specifies the installation directory
CC= CXX= ./configure --prefix=$DIR/grib2
# Building the Software: compiles the source code into executable binaries based on the instructions in the Makefile generated by ./configure. 
make
# Installing the Software: installs the compiled binaries, libraries, and other files to the directory specified by the --prefix option
make install
# Error Checking: To catch errors at each step, you can add && between commands to ensure that the next command only runs if the previous one succeeds.
# make check
```

### Compile and Install libpng

```bash
cd ..
# LDFLAGS Environment variable specifies the directory for the linker to look for libraries & -L flag tells the linker where to find library files
export LDFLAGS=-L$DIR/grib2/lib
# Setting the Include Path: environment variable is used to specify additional preprocessor flags & -I flag tells the preprocessor where to look for header files.
export CPPFLAGS=-I$DIR/grib2/include
# Extracting the libpng Archive:
tar -xvzf libpng-1.6.37.tar.gz
# Changing Directory
cd libpng-1.6.37/
# Configuring the Build: Configures the source code for compilation & Sets the installation directory. 
./configure --prefix=$DIR/grib2
# Building the Software
make
# Installing the Software
make install
```

### Install Jasper

```bash
# Changing Directory
cd ..
# Unzipping the Archive
unzip jasper-1.900.1.zip
# Changing Directory
cd jasper-1.900.1/
# Running autoreconf: Rebuilds the configure script and other auto-generated files. This is typically required when the source does not include a configure script or when you need to regenerate it due to system-specific requirements.
# The -i option stands for --install, which installs any missing files (like config.sub and config.guess) necessary for building on different architectures.
autoreconf -i
# Configuring the Build
./configure --prefix=$DIR/grib2
# Building the Library:
make
# Installing the Library:
make install
# Sets JASPERLIB to the path where the compiled jasper libraries are installed.
export JASPERLIB=$DIR/grib2/lib
# Sets JASPERINC to the path where the jasper header files are located.
export JASPERINC=$DIR/grib2/include
```


### Install MPICH

```bash
# Changing Directory
cd ..
# Extracting the Archive
tar -xvzf mpich-4.0.2.tar.gz
# Changing Directory
cd mpich-4.0.2/
# Configuring the Build: Runs the configuration script to set up the build environment.
F90= ./configure --prefix=$DIR/MPICH --with-device=ch3 FFLAGS=$fallow_argument FCFLAGS=$fallow_argument
# --with-device=ch3: Specifies the communication device for MPI. Here, ch3: which is the default device for MPICH. The ch3 device provides support for various communication channels, typically including TCP.
# Building the Software
make
# Installing the Software
make install
# Seth the MPICH bin into the PATH
export PATH=$DIR/MPICH/bin:$PATH
```

### Install HDF5

```bash
# Changing Directory
cd ..
# Extracting the Archive:
tar -xvzf hdf5-1_12_2.tar.gz

# Changing Directory
cd hdf5-hdf5-1_12_2

# Configuring the Build: 
./configure --prefix=$DIR/grib2 --with-zlib=$DIR/grib2 --enable-hl --enable-fortran

Note: 
--enable-hl: Enables the high-level library API for HDF5, which provides easier access to some HDF5 features.
--enable-fortran: Enables the Fortran API, allowing HDF5 to be used with Fortran programs.

# Building the Library: Compiles the HDF5 source code using the Makefile generated by the configuration script. This produces the HDF5 binaries and libraries.
make

#  Installs the compiled binaries, libraries, and headers for HDF5 into the directory specified by --prefix
make install

# Setting Environment Variables: Sets the HDF5 environment variable to the installation path
export HDF5=$DIR/grib2

# Adds $DIR/grib2/lib to the LD_LIBRARY_PATH, which is the system’s library search path. This ensures that the dynamic linker can locate the HDF5 libraries when they are required by other programs.
export LD_LIBRARY_PATH=$DIR/grib2/lib:$LD_LIBRARY_PATH
```


### Install netCDF-C

```bash
# Changing Directory
cd ..

# Setting Environment Variables: where headers for dependencies like zlib and HDF5 may be located.
export CPPFLAGS=-I$DIR/grib2/include 
export LDFLAGS=-L$DIR/grib2/lib

# Extracting the Archive
tar -xzvf v4.9.0.tar.gz

# Changing Directory
cd netcdf-c-4.9.0/

# Configuring the Build: Prepares the build environment by checking for dependencies and generating the Makefile.
./configure --prefix=$DIR/NETCDF --disable-dap

# --disable-dap: Disables the DAP (Data Access Protocol) support. DAP allows remote access to data over HTTP, but it’s not required for basic netCDF functionality. Disabling it simplifies the build and avoids unnecessary dependencies.

# Building the Library: Compiles the netCDF source code based on the configuration settings. This command builds the netCDF binaries and libraries.
make

# Installing the Library: Installs the compiled binaries, libraries, and headers to the directory specified by --prefix
make install

# Setting Environment Variables:
# Adds $DIR/NETCDF/bin to the PATH environment variable, allowing you to access netCDF binaries (like ncdump and ncgen) from any directory in the terminal.
export PATH=$DIR/NETCDF/bin:$PATH
# Sets the NETCDF environment variable.
export NETCDF=$DIR/NETCDF
```

### Install netCDF-Fortran


```bash
# Changing Directory
cd ..

# Extracting the Archive
tar -xvzf v4.6.0.tar.gz

# Changing Directory
cd netcdf-fortran-4.6.0/

# Adds $DIR/NETCDF/lib to the library path, allowing the linker to locate netCDF C libraries during compilation.
export LD_LIBRARY_PATH=$DIR/NETCDF/lib:$LD_LIBRARY_PATH
# Sets the include path to $DIR/NETCDF/include, where headers for netCDF C are located. The -I flag tells the compiler where to find header files.
export CPPFLAGS=-I$DIR/NETCDF/include
# Sets the library path to $DIR/NETCDF/lib. The -L flag tells the linker where to find libraries during compilation.
export LDFLAGS=-L$DIR/NETCDF/lib

# Configuring the Build
./configure --prefix=$DIR/NETCDF --disable-shared
# --disable-shared: Disables the creation of shared libraries, so only static libraries will be built and installed. This can be useful if you prefer to link statically to reduce dependencies on shared libraries.

# Building the Library
make

# Installing the Library
make install
```

## Installing, Configuring & Compiling WRF-HYDRO in Standalone Mode

### Download and Extract WRF-Hydro Source Code

```bash
# Changing Directory
cd ..

# Download the WRF-Hydro Model 
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/archive/refs/tags/v5.2.0.tar.gz -O WRFHYDRO.5.2.tar.gz

# Create a directory 
mkdir -p $HOME/wrfhydromodel

# Extracting the archive into wrfhydromodel path 
tar -xvzf WRFHYDRO.5.2.tar.gz -C $HOME/wrfhydromodel

# Verifying the Download 
ls $HOME/wrfhydromodel/
```

### Modify WRF-Hydro Environment

Note: Modifies an environment setup script (setEnvar.sh), and then creates a symbolic link to this modified script in another directory.

2) Modifies setEnvar.sh to enable spatial soil functionality and support for large NetCDF files.

3) Adds comments and spacing for clarity.

4) Links the modified script to another directory, making it available elsewhere within the project structure.

```bash
# Navigating to the Template Directory where setEnvar.sh is located. 
cd $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/template

# Changes SPATIAL_SOIL from 0 to 1, enabling spatial soil functionality in the model configuration.
sed -i 's/SPATIAL_SOIL=0/SPATIAL_SOIL=1/g' setEnvar.sh

- sed: A command-line text editor for finding and replacing text in files.
- i: Edits the file in place, applying changes directly to setEnvar.sh.
- Replaces all instances of SPATIAL_SOIL=0 with SPATIAL_SOIL=1.
- g: Global flag to replace all occurrences on each line.

# Adding an Empty Line: Adds an empty line to the end of setEnvar.sh for readability before adding the next settings.
echo " " >> setEnvar.sh

# Adding a Comment Line: Appends the comment to the end of setEnvar.sh.
echo "# Large netcdf file support: 0=Off, 1=On." >> setEnvar.sh

# Enabling Large NetCDF File Support: export WRFIO_NCD_LARGE_FILE_SUPPORT=1: Sets the WRFIO_NCD_LARGE_FILE_SUPPORT environment variable to 1, enabling support for large NetCDF files (files larger than 2GB). This can be essential for working with high-resolution model outputs.
echo "export WRFIO_NCD_LARGE_FILE_SUPPORT=1" >> setEnvar.sh

# Creating a Symbolic Link: Allows setEnvar.sh to be accessible in another part of the project structure without duplicating the file itself.
ln setEnvar.sh $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS
```

### Compile WRF-Hydro Offline with NoahMP

1) Navigate to the NDHMS directory where the main configuration files are located.
2) Configure WRF-Hydro to use the Noah-MP model in offline mode by choosing Option 2.
3) Compile the model, applying settings from setEnvar.sh.
4) Verify that the .exe files have been created, indicating a successful build.

```bash
# Navigating to the NDHMS Directory
cd $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS

# Select a compiler in this case fortran
./configure # Option 2

# Compiling WRF-Hydro with Noah-MP in Offline Mode
./compile_offline_NoahMP.sh setEnvar.sh

Note: This script is specific to the WRF-Hydro build system and is designed to compile the model in offline mode (i.e., without atmospheric coupling) with the Noah-MP land surface model.

Once the compilation process finishes successfully, you'll see the message 'Make was successful. 🥇🥇🥇

*****************************************************************
Make was successful

*****************************************************************

# Verifying the Compilation: Checks whether the expected .exe files (executables) have been created, confirming a successful compilation. Typical executables might include wrf_hydro_NoahMP.exe 
ls -lah Run/*.exe # Test if .exe files have compiled
```

### Copy .TBL and Executable Files

1) Navigate to the main WRF-Hydro working directory.
2) Create a directory (domain/NWM) to store the required domain files for the WRF-Hydro simulation.
3) Copy the necessary .TBL files, which contain model configurations, to the NWM directory.
4) Copy the main executable, wrf_hydro.exe, to the NWM directory for easy access when running the model.

```bash
# Navigating to the Main Directory
cd $HOME/wrfhydromodel

# Creating the NWM Directory
mkdir -p $HOME/wrfhydromodel/domain/NWM

# Copying the .TBL Files
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/*.TBL domain/NWM

# Copying the Executable
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/wrf_hydro.exe domain/NWM
```

### Download and Run the Croton_NY Test Case

```bash

# Move to Download Directory
cd $HOME/wh/Downloads

# Download the example dataset
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/releases/download/v5.2.0/croton_NY_training_example_v5.2.tar.gz

# Extracting the Archive
tar -xzvf croton_NY_training_example_v5.2.tar.gz
```

### Copy Forcing, Domain, & Other Configuration Files

1) Copy directories and files from example_case to $HOME/wrfhydromodel/domain/NWM.
   
2) Prepare the necessary input data (forcing, domain, and restart files) and configuration files (namelist.hrldas and hydro.namelist) required to run the WRF-Hydro simulation.
   
```bash
# Copying the FORCING Directory
cp -r example_case/FORCING $HOME/wrfhydromodel/domain/NWM

# Copying the DOMAIN Directory
cp -r example_case/NWM/DOMAIN $HOME/wrfhydromodel/domain/NWM

# Copying the RESTART Directory
cp -r example_case/NWM/RESTART $HOME/wrfhydromodel/domain/NWM

# Copying the nudgingTimeSliceObs Directory
cp -r example_case/NWM/nudgingTimeSliceObs $HOME/wrfhydromodel/domain/NWM

# Copying the referenceSim Directory
cp -r example_case/NWM/referenceSim $HOME/wrfhydromodel/domain/NWM

# Copying the namelist.hrldas File
cp example_case/NWM/namelist.hrldas $HOME/wrfhydromodel/domain/NWM

# Copying the hydro.namelist File
cp example_case/NWM/hydro.namelist $HOME/wrfhydromodel/domain/NWM

# Verifying the copied file 
ls -R $HOME/wrfhydromodel/domain/NWM
```

### Running the WRF-Hydro Model

1) Change to the directory containing the model executable and input data.
2) Run WRF-Hydro with MPI using 2 processes.
3) Check for the output restart files to confirm successful execution.
4) Print a success message if the expected output files are present.

```bash

# Navigating to the NWM Directory
cd $HOME/wrfhydromodel/domain/NWM

# Running WRF-Hydro with MPI: run programs in parallel using MPI (Message Passing Interface): This command runs the model in offline mode with Noah-MP based on the compiled executable.
mpirun -np 2 ./wrf_hydro.exe

Optional:
# Running WRF-Hydro  while Error Checking
# mpirun -np 2 ./wrf_hydro.exe > wrf_hydro_run.log 2>&1

# Verifying the Output
ls -lah HYDRO_RST*

# Note: HYDRO_RST* matches any file that starts with HYDRO_RST. These are the model’s restart files, which contain the state of the simulation at specific intervals, allowing the simulation to resume from that point if needed.

# Inspect Restart Files: You can use commands like ncdump to inspect the contents of the files
ncdump -h HYDRO_RST.2011-08-27_00:00_DOMAIN1
ncdump -h RESTART.2011082700_DOMAIN1
ncdump -h 201108260000.LDASOUT_DOMAIN1 
```


## Conclusion

Congratulations on completing the installation and configuration of the WRF-Hydro model in standalone mode! With the successful installation of the necessary libraries and compilation of the model using the Noah-MP Land Surface Model, you now have a powerful tool at your disposal for hydrological modeling and analysis.

## Summary: 

Throughout this guide, you have:

1) Set up a dedicated environment for compiling WRF-Hydro, ensuring that all dependencies are properly installed.
2) Configured essential libraries, including zlib, HDF5, netCDF, MPICH, and others, with specific flags to optimize performance for WRF-Hydro.
3) Modified configuration files, such as setEnvar.sh, to enable specific model functionalities, including support for large NetCDF files and spatial soil modeling.
4) Compiled WRF-Hydro offline, allowing it to run independently of atmospheric models, ideal for hydrological and land surface simulations driven by predefined meteorological data.
5) Successfully run a test case with the Croton_NY dataset, confirming that the installation was successful and that the model is operational.

"Happy modeling, and may your simulations provide valuable insights into the hydrological processes you seek to understand!"







