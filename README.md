# nemsio to netCDF converter
Converter for GFS Gaussian grid nemsio format files to netCDF format.

### Prerequisites:
- Compiler (Intel, GNU, Clang/AppleClang)
- [CMake](https://cmake.org/)
- [NetCDF](https://www.unidata.ucar.edu/software/netcdf/)
- [NCEPLIBS-nemsio](https://github.com/noaa-emc/nceplibs-nemsio.git)

Modulefiles are provided for certain NOAA machines under [modulefiles](./modulefiles/)

```
module use $PWD/modulefiles
module load platform.compiler
```

e.g. `module load hera.intel`

### Building:
```
mkdir build; cd build
cmake -DCMAKE_INSTALL_PREFIX=<install-prefix> ..
make -j2
make install
```

The executable `nemsioatm2nc` will be installed under `install-prefix/bin`

### Usage:
```
nemsioatm2nc nemsio_file netcdf_file
```
