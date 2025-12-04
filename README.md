# IndySCC-HPL
IN PROGRESS REPO NOT DONE
HPL tuning for Jetstream2 Cluster

---
 High Performance Computing Linpack Benchmark (HPL)
 HPL - 2.3 - December 2, 2018

 HPL is a software package that solves a (random) dense linear
 system  in   double  precision  (64   bits)   arithmetic   on 
 distributed-memory  computers.   It can thus be regarded as a
 portable as well as  freely  available implementation  of the
 High Performance Computing Linpack Benchmark.

 The  HPL  software  package requires the availibility on your
 system of an implementation of the  Message Passing Interface
 MPI  (1.1 compliant).  An  implementation of either the Basic
 Linear Algebra Subprograms  BLAS  or the  Vector Signal Image
 Processing Library VSIPL is also needed.  Machine-specific as
 well as generic implementations of MPI, the  BLAS  and  VSIPL
 are available for a large variety of systems.

 Install See the file INSTALL in this directory.

 Tuning  See the file TUNING in this directory.

 Bugs  Known  problems and bugs with this release are documen-
ted in the file hpl/BUGS.

 Check out  the website  www.netlib.org/benchmark/hpl  for the
 latest information.

---
HPL Benchmark Results

Project Description
High Performance Linpack (HPL) benchmark test on a Slurm cluster of 3 AMD EPYC Milan 64-core nodes.

Hardware Configuration
Login Node: m3.medium Jetstream2 flavor (8 cores)
CPUS: AMD EPYC 7713 Milan Processor
Cores: 64 cores per processor
Frequency: 2.0 GHz
Architecture: Zen 3 with AVX2

Software Environment
Workload Manager: Slurm
Share Filesystem: Manila 
Compiler: GCC 13.2.0 
MPI: OpenMPI 5.0.0 with pmix
Math Library: OpenBLAS

Configuration Process

1. Prepare compilation configuration
./configure   
--prefix=/mnt/hpl_manila/opt/openmpi  
--with-pmix=/mnt/hpl_manila/opt/pmix
--enable-orterun-prefix-by-default 
--with-slurm 


3. Compile HPL
cd /mnt/spack_hpl_manila/hpl-2.3/
make arch=Linux_ATHLON_CBLAS

4. Run HPL with Slurm
```#!/bin/bash
#SBATCH -p nsss
#SBATCH -J HEROHPL
#SBATCH -e /mnt/hpl_manila/hplmpirun_%j.err
#SBATCH -o /mnt/hpl_manila/hplmpirun_%j.out
#SBATCH -N 3
#SBATCH --ntasks=192
#SBATCH --ntasks-per-node=64
#SBATCH --cpus-per-task=1
#SBATCH --mem=240G
#SBATCH -t 15:00:00

cd /mnt/hpl_manila
export HPL_DIR="/mnt/spack_hpl_manila/opt/hpl"
export BLAS_DIR="/mnt/spack_hpl_manila/opt/OpenBLAS"
export MPI_DIR="/mnt/spack_hpl_manila/opt/openmpi"

export PATH="$MPI_DIR/bin:$HPL_DIR/bin:$PATH"
export LD_LIBRARY_PATH="$MPI_DIR/lib:$BLAS_DIR/lib:$HPL_DIR/lib:$LD_LIBRARY_PATH"


export OPENBLAS_NUM_THREADS=1
export GOTO_NUM_THREADS=1
export OMP_NUM_THREADS=1
export OMPI_MCA_btl_tcp_if_include=enp1s0 
##export OMPI_MCA_btl_base_verbose=100
export OMPI_MCA_btl=self,vader,tcp # only shared memory and “self”
##export OMPI_MCA_btl=^openib
export OMPI_MCA_pml=ob1

/mnt/hpl_manila/opt/openmpi/bin/mpirun -np 192 /mnt/manila/opt/hpl/bin/xhpl
```

Test Results
Problem Size (N): 290,048
Block Size (NB): 256
Process Grid: 6 x 62
Total Processes: 192
Compute Time: 2921.59 seconds
Performance: 5.5680 Tflops
Residual Check: PASSED

File Description
HPL.dat: HPL runtime parameter configuration
hpl_end.txt: Complete performance test results
README: This project documentation

Note
The HPL benchmark completed successfully with all residual checks passed. The configuration and compilation process involved setting up the NVHPC environment, configuring the Makefile for local system paths, and optimizing runtime parameters for the 64-core AMD EPYC Milan system.
