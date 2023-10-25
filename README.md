# PX915-Summer-Project

- To run CP2K on the hetmathsys nodes, you will need to build it. Alternatively you can use the precompiled version on Avon - if so I can provide a run script.
- Once you have built it, all relevant input files can found in the rep_res directiory. In each of the three calculation directories (Ag, PFOMA, and PFOMA_Ag) you will need to run the command:
  ```bash
  ../cp2k/exe/local/cp2k.popt -i PFOMA.inp -o output.out
  ```
  The total energies for each calculation should then be found in the output.out files, and can be inputted into the jupyter notebook to calculate the formation energy.

## Building CP2K (cp2k-2023.2) in Oct. 2023, Rocky 9, SCRTP

Get the source code:
```bash
wget https://github.com/cp2k/cp2k/releases/download/v2023.2/cp2k-2023.2.tar.bz2
```

Load the relevant modules:
```bash
module purge
module load GCC/11.3.0 OpenMPI/4.1.4 OpenBLAS/0.3.20 CMake/3.24.3 FFTW/3.3.10 Libint/2.7.2-lmax-6-cp2k libxc/5.2.3 ScaLAPACK/2.2.0-fb GSL/2.7 HDF5/1.13.1 
```

De-compress the archive:
```bash
tar -xf cp2k-2023.2.tar.bz2
```

Install pre-requisites (using 1 CPU here, "-j 1", adapt as needed):
```bash
cd ~cp2k-2023.2/tools/toolchain
./install_cp2k_toolchain.sh -j 1 --mpi-mode=openmpi --with-gcc=system --with-cmake=system --with-fftw=/software/easybuild/software/FFTW/3.3.10-GCC-11.3.0 --with-libxc=/software/easybuild/software/libxc/5.2.3-GCC-11.3.0 --with-libint=/software/easybuild/software/Libint/2.7.2-GCC-11.3.0-lmax-6-cp2k --with-libxsmm=no --with-openblas=/software/easybuild/software/OpenBLAS/0.3.20-GCC-11.3.0 --with-scalapack=/software/easybuild/software/ScaLAPACK/2.2.0-gompi-2022a-fb --with-elpa=no --with-gsl=/software/easybuild/software/GSL/2.7-GCC-11.3.0 --with-hdf5=/software/easybuild/software/HDF5/1.13.1-gompi-2022a --with-libvdwxc=no --with-spglib=no --with-sirius=no
```

Copying the Makefile(s) into the /arch dir:
```bash
cp ~cp2k-2023.2/tools/toolchain/install/arch/local* arch/
```

Modify ~ cp2k-2023.2/arch/local.psmp (mismatched llftw3!). Search for the line (~ line 18?) starting with LIBS, and comment/add as follows: 
```bash
#LIBS        = -lhdf5 -lhdf5_hl -lz -lgsl -lcosma_prefixed_pxgemm -lcosma -lcosta  -lscalapack -lxcf03 -lxc -lint2 -lfftw3_mpi -lfftw3 -lfftw3_omp   -lmpi  -lopenblas -lvori -lstdc++ -lstdc++
LIBS        = -lhdf5 -lhdf5_hl -lz -lgsl -lcosma_prefixed_pxgemm -lcosma -lcosta  -lscalapack -lxcf03 -lxc -lint2 -lfftw3 -lfftw3_omp   -lmpi  -lopenblas -lvori -lstdc++ -lstdc++
```

Move back the cp2k root dir and compile (using 1 CPU here, "-j 1", adapt as needed):
``` bash
cd ~cp2k-2023.2
make -j 1 ARCH=local VERSION="psmp"
```

Remember to source this file before running:
```bash
source ~cp2k-2023.2/tools/toolchain/install/setup
```

And remember to load all the modules above before running as well.
