# NVIDIA HPC Benchmarks container with CXI

Self-sufficient container image with pre-compiled [NVIDIA HPC Benchmarks](https://developer.nvidia.com/nvidia-hpc-benchmarks-downloads) 25.04, installed on top of OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

## Notes

- This image is self-sufficient with respect to Slingshot connectivity, and does not require hooks to inject a custom CXI stack from the host.
- Running a container requires a hook for bridging the PMIx interface between host and container
- The image installs the UCX communication framework so that OpenSHMEM can be built. OpenSHMEM is in turn a dependency for NVSHMEM
- NVSHMEM is rebuilt from source because the build bundled with the benchmarks incurs in a segmentation fault when using its libfabric backend
- The image includes also the libfabric LINKx provider for experimentation.
- Two variants of the Containerfile are provided:
    1. An extended one with explicit build instructions for all the software components besides CUDA.
    2. A short one using other images from this Git repository as base, for a more modular approach.


## Examples

- HPL, 2 nodes, 8 GPUs, sample DAT included with benchmarks
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --ntasks-per-node=4 --mpi=pmix --environment=nv-hpc-bench ./hpl.sh --dat /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/sample-dat/HPL-GH-8GPUs.dat

[... HPL output ...]

================================================================================
T/V                N    NB     P     Q         Time          Gflops (   per GPU)
--------------------------------------------------------------------------------
WC0           299008  1024     2     4        52.88       3.370e+05 ( 4.213e+04)

HPL_pdgesv() start time Sun Jun  8 23:23:45 2025
HPL_pdgesv() end time   Sun Jun  8 23:24:38 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   0.000550841189 ...... PASSED
||Ax-b||_oo  . . . . . . . . . . . . . . . . . = 0.0000000064268288
||A||_oo . . . . . . . . . . . . . . . . . . . = 75091.3885435541742481
||x||_oo . . . . . . . . . . . . . . . . . . . = 4.6804384307618703
||b||_oo . . . . . . . . . . . . . . . . . . . = 0.4999994346680141
================================================================================

Finished      1 tests with the following results:
              1 tests completed and passed residual checks,
              0 tests completed and failed residual checks,
              0 tests skipped because of illegal input values.
--------------------------------------------------------------------------------

End of Tests.
================================================================================

```
