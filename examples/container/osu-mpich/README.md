# OSU Micro-Benchmarks container with CXI - MPICH variant

Self-sufficient container image with OSU Micro-Benchmarks built on MPICH 4.2.1 with CUDA and CXI (i.e. HPE Slingshot) support.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/osu-mb (look for the `cxi` mention in the tag).

## Notes (see also EDF TOML for reference)
- The image does not require hooks to inject a custom CXI stack from the host
- The image includes also the libfabric LINKx provider for experimentation.
- Two variants of the Containerfile are provided:
    1. An extended one with explicit build instructions for all the software components besides CUDA.
    2. A short one using other images from this Git repository as base, for a more modular approach.

## Examples

- Point-to-point latency
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --mpi=pmi2 --environment=omb-cxi-mpich ./pt2pt/osu_latency

# OSU MPI Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       3.19
2                       3.19
4                       3.15
8                       3.18
16                      3.19
32                      3.19
64                      3.27
128                     4.07
256                     4.09
512                     4.13
1024                    4.18
2048                    4.38
4096                    4.79
8192                    9.94
16384                  10.56
32768                  11.39
65536                  12.73
131072                 15.47
262144                 20.88
524288                 31.66
1048576                53.23
2097152                96.37
4194304               182.64
```

- Point-to-point latency with GPU device buffers
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --mpi=pmi2 --environment=omb-cxi-mpich ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      21.80
2                      21.94
4                      21.90
8                      22.01
16                     21.78
32                     22.04
64                     22.20
128                    23.19
256                    23.67
512                    23.21
1024                   22.98
2048                   23.60
4096                   25.23
8192                   32.46
16384                  33.61
32768                  35.80
65536                  40.14
131072                 50.44
262144                 70.56
524288                117.67
1048576               214.15
2097152               412.46
4194304               825.47
```

- All-to-all collective latency, 8 ranks over 2 nodes
```
[clariden][amadonna@clariden-ln002 ~]$ srun -N2 --ntasks-per-node=4 --mpi=pmi2 --environment=omb-cxi-mpich ./collective/osu_alltoall

# OSU MPI All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      22.31
2                      22.15
4                      22.04
8                      22.13
16                     22.02
32                     21.96
64                     21.99
128                    22.26
256                    22.41
512                    23.00
1024                   23.99
2048                   24.25
4096                   25.56
8192                   28.16
16384                  66.81
32768                  91.14
65536                 182.75
131072                303.41
262144                522.80
524288                961.68
1048576              1824.77
```

- All-to-all collective latency with GPU buffers, 8 ranks over 2 nodes
```
[clariden][amadonna@clariden-ln002 ~]$ srun -N2 --ntasks-per-node=4 --mpi=pmi2 --environment=omb-cxi-mpich ./collective/osu_alltoall -d cuda

# OSU MPI-CUDA All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                     135.89
2                     133.64
4                     133.22
8                     132.98
16                    132.87
32                    132.75
64                    132.57
128                   132.57
256                   133.93
512                   135.03
1024                  137.20
2048                  139.85
4096                  144.11
8192                  146.80
16384                 170.30
32768                 189.67
65536                 207.48
131072                238.45
262144                302.85
524288                438.77
1048576               744.85
```

## Known issues and limitations
- The open source libfabric+CXI seems to be noticeably slower to detect CXI devices compared to the custom 1.15.x implementation provided by HPE. This results in longer startup times for jobs.

## TODO

- XPMEM support
