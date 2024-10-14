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









