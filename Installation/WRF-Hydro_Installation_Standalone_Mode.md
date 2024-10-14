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

