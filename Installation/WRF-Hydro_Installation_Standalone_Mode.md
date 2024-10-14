# WRF-Hydro Model Installation in Standalone Mode

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
tar -xvzf v1.2.12.tar.gz
cd zlib-1.2.12/
CC= CXX= ./configure --prefix=$DIR/grib2
make
make install
# make check
```

### Compile and Install libpng

```bash
cd ..
export LDFLAGS=-L$DIR/grib2/lib
export CPPFLAGS=-I$DIR/grib2/include
tar -xvzf libpng-1.6.37.tar.gz
cd libpng-1.6.37/
./configure --prefix=$DIR/grib2
make
make install
```

### Install Jasper

```bash
cd ..
unzip jasper-1.900.1.zip
cd jasper-1.900.1/
autoreconf -i
./configure --prefix=$DIR/grib2
make
make install
export JASPERLIB=$DIR/grib2/lib
export JASPERINC=$DIR/grib2/include
```


### Install MPICH

```bash
cd ..
tar -xvzf mpich-4.0.2.tar.gz
cd mpich-4.0.2/
F90= ./configure --prefix=$DIR/MPICH --with-device=ch3 FFLAGS=$fallow_argument FCFLAGS=$fallow_argument
make
make install
export PATH=$DIR/MPICH/bin:$PATH
```

### Install HDF5

```bash
cd ..
tar -xvzf hdf5-1_12_2.tar.gz
cd hdf5-hdf5-1_12_2
./configure --prefix=$DIR/grib2 --with-zlib=$DIR/grib2 --enable-hl --enable-fortran
make
make install
export HDF5=$DIR/grib2
export LD_LIBRARY_PATH=$DIR/grib2/lib:$LD_LIBRARY_PATH
```


### Install netCDF-C

```bash
cd ..
export CPPFLAGS=-I$DIR/grib2/include 
export LDFLAGS=-L$DIR/grib2/lib
tar -xzvf v4.9.0.tar.gz
cd netcdf-c-4.9.0/
./configure --prefix=$DIR/NETCDF --disable-dap
make
make install
export PATH=$DIR/NETCDF/bin:$PATH
export NETCDF=$DIR/NETCDF
```


### Install netCDF-Fortran


```bash
cd ..
tar -xvzf v4.6.0.tar.gz
cd netcdf-fortran-4.6.0/
export LD_LIBRARY_PATH=$DIR/NETCDF/lib:$LD_LIBRARY_PATH
export CPPFLAGS=-I$DIR/NETCDF/include 
export LDFLAGS=-L$DIR/NETCDF/lib
./configure --prefix=$DIR/NETCDF --disable-shared
make
make install
```

## Installing, Configuring & Compiling WRF-HYDRO in Standalone Mode

### Download and Extract WRF-Hydro Source Code

```bash
cd ..
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/archive/refs/tags/v5.2.0.tar.gz -O WRFHYDRO.5.2.tar.gz
mkdir -p $HOME/wrfhydromodel
tar -xvzf WRFHYDRO.5.2.tar.gz -C $HOME/wrfhydromodel
ls $HOME/wrfhydromodel/
```

### Modify WRF-Hydro Environment

```bash
cd $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/template
sed -i 's/SPATIAL_SOIL=0/SPATIAL_SOIL=1/g' setEnvar.sh
echo " " >> setEnvar.sh
echo "# Large netcdf file support: 0=Off, 1=On." >> setEnvar.sh
echo "export WRFIO_NCD_LARGE_FILE_SUPPORT=1" >> setEnvar.sh
ln setEnvar.sh $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS
```

### Compile WRF-Hydro Offline with NoahMP

```bash
cd $HOME/wrfhydromodel/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS
./configure # Option 2
./compile_offline_NoahMP.sh setEnvar.sh
ls -lah Run/*.exe # Test if .exe files have compiled
```

### Copy .TBL and Executable Files

```bash
cd $HOME/wrfhydromodel
mkdir -p $HOME/wrfhydromodel/domain/NWM
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/*.TBL domain/NWM
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/wrf_hydro.exe domain/NWM
```

### Download and Run the Croton_NY Test Case

```bash
cd $HOME/wh/Downloads
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/releases/download/v5.2.0/croton_NY_training_example_v5.2.tar.gz
tar -xzvf croton_NY_training_example_v5.2.tar.gz
```

### Copy Forcing, Domain, & Other Configuration Files

```bash
cp -r example_case/FORCING $HOME/wrfhydromodel/domain/NWM
cp -r example_case/NWM/DOMAIN $HOME/wrfhydromodel/domain/NWM
cp -r example_case/NWM/RESTART $HOME/wrfhydromodel/domain/NWM
cp -r example_case/NWM/nudgingTimeSliceObs $HOME/wrfhydromodel/domain/NWM
cp -r example_case/NWM/referenceSim $HOME/wrfhydromodel/domain/NWM
cp example_case/NWM/namelist.hrldas $HOME/wrfhydromodel/domain/NWM
cp example_case/NWM/hydro.namelist $HOME/wrfhydromodel/domain/NWM
ls -R $HOME/wrfhydromodel/domain/NWM
```

### Running the WRF-Hydro Model

```bash
cd $HOME/wrfhydromodel/domain/NWM
mpirun -np 2 ./wrf_hydro.exe
ls -lah HYDRO_RST*

ncdump -h HYDRO_RST.2011-08-27_00:00_DOMAIN1
ncdump -h RESTART.2011082700_DOMAIN1
ncdump -h 201108260000.LDASOUT_DOMAIN1 
```












