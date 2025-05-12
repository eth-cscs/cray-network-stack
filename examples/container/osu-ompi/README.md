# OSU Micro-Benchmarks container with CXI - OpenMPI variant

Self-sufficient container image with OSU Micro-Benchmarks built on OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/osu-mb (look for the `cxi` mention in the tag).

## Notes (see also EDF TOML for reference)
- This image is self-sufficient with respect to Slingshot connectivity, and does not require hooks to inject a custom CXI stack from the host.
- Interaction with host PMIx needs to be understood better: environment variables for PSEC and GDS parameters have to be set on Alps to prevent warnings and crashes due to missing components
- Running a container requires a hook for bridging the PMIx interface between host and container
- The image includes also the libfabric LINKx provider for experimentation.
- The use of the LINKx provider requires specific environment variables to be used. Please check the corresponding EDF for details.
- In order to run successfully with the LINKx provider and CUDA device buffers, the following are included:
    - An experimental fix for CUDA GDR support in libfabric (more details [here](https://github.com/ofiwg/libfabric/issues/10865#issuecomment-2735866065)); the fix is applied on top of libfabric's `main` commit at the moment of writing (commit faf13301a4a9628b6c9a28a06d936258c6d368af), and the code is available from [this forked branch](https://github.com/Madeeks/libfabric/tree/cuda_gdrcopy_unregister_fix).
    - OpenMPI 5.0.8RC1 to provide [this fix](https://github.com/open-mpi/ompi/pull/12290).
- The libfabric EFA provider is included to leave open the possibility to experiment with derived images on AWS infrastructure as well.
- Although only the libfabric framework and its CXI provider are required to support the Slingshot network, this image also packages the UCX communication framework to allow building a broader set of software (e.g. some OpenSHMEM implementations) and supporting optimized Infiniband communication as well.
- Two variants of the Containerfile are provided:
    1. An extended one with explicit build instructions for all the software components besides CUDA.
    2. A short one using other images from this Git repository as base, for a more modular approach.

## Examples

### Point-to-point bandwidth

#### Host buffers, inter-node communication
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.93
2                       1.86
4                       3.71
8                       7.47
16                     14.94
32                     29.75
64                     59.24
128                   119.64
256                   233.94
512                   466.15
1024                  932.37
2048                 1868.28
4096                 3717.03
8192                 6921.65
16384               13727.55
32768               19127.66
65536               22186.77
131072              23067.95
262144              23534.68
524288              23752.40
1048576             23875.19
2097152             23946.12
4194304             23979.28
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --environment=omb-ompi-lnx ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.89
2                       1.80
4                       3.62
8                       7.26
16                     14.47
32                     28.46
64                     58.10
128                   114.88
256                   235.90
512                   469.74
1024                  938.82
2048                 1867.47
4096                 3705.34
8192                 5582.02
16384               11031.67
32768               16429.50
65536               18607.93
131072              22213.18
262144              23023.93
524288              23468.66
1048576             23751.76
2097152             23879.32
4194304             23947.73
```

#### Device buffers, inter-node communication
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.89
2                       1.80
4                       3.65
8                       7.30
16                     14.72
32                     29.50
64                     55.71
128                   117.30
256                   236.74
512                   472.53
1024                  948.85
2048                 1899.67
4096                 3763.49
8192                 6788.86
16384               13646.37
32768               18632.53
65536               22150.03
131072              23101.69
262144              23541.76
524288              23747.82
1048576             23885.04
2097152             23954.78
4194304             23987.18
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --environment=omb-ompi-lnx ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.89
2                       1.82
4                       3.64
8                       7.36
16                     14.67
32                     28.59
64                     58.52
128                   116.43
256                   231.71
512                   474.66
1024                  951.78
2048                 1890.40
4096                 3780.36
8192                 5121.73
16384               11583.87
32768               16300.16
65536               20699.77
131072              22434.27
262144              23146.46
524288              23533.63
1048576             23782.29
2097152             23895.75
4194304             23961.15
```

#### Host buffers, intra-node communication
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.94
2                       1.86
4                       3.74
8                       7.44
16                     14.93
32                     29.81
64                     59.88
128                   119.11
256                   236.22
512                   470.73
1024                  944.61
2048                 1892.85
4096                 3496.41
8192                 6621.66
16384               13889.01
32768               19088.65
65536               22131.52
131072              23121.05
262144              23543.51
524288              23586.85
1048576             23886.85
2097152             23947.09
4194304             23983.30
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-lnx ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       1.87
2                       3.69
4                       7.41
8                      14.82
16                     29.66
32                     59.45
64                    118.56
128                   236.60
256                   367.49
512                   692.83
1024                 1237.49
2048                 2403.51
4096                 4689.73
8192                12128.50
16384               21656.90
32768               35406.83
65536               50833.24
131072              62508.75
262144              69064.68
524288              47060.39
1048576             38295.24
2097152             27093.04
4194304             23883.77
```

#### Device buffers, intra-node communication
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.90
2                       1.82
4                       3.66
8                       7.35
16                     14.71
32                     29.37
64                     58.40
128                   117.73
256                   236.06
512                   470.16
1024                  938.36
2048                 1893.62
4096                 3761.26
8192                 6354.87
16384               13501.31
32768               15878.39
65536               22097.93
131072              23100.37
262144              23542.56
524288              23750.17
1048576             23880.01
2097152             23953.17
4194304             23989.52
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-lnx ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.41
2                       0.82
4                       1.70
8                       3.41
16                      6.85
32                     13.53
64                     27.05
128                    55.17
256                   110.44
512                   221.50
1024                  444.12
2048                  889.00
4096                 1780.17
8192                 3598.23
16384                7108.33
32768               14629.03
65536               29571.60
131072              59580.04
262144             120224.38
524288             117802.04
1048576            145998.03
2097152            166490.79
4194304            180926.25
```

### Point-to-point latency

#### Device buffers, intra-node communication
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       4.00
2                       3.98
4                       3.97
8                       3.96
16                      3.97
32                      3.97
64                      3.97
128                     4.43
256                     4.41
512                     4.40
1024                    4.49
2048                    4.64
4096                    4.89
8192                    8.30
16384                   8.97
32768                   9.71
65536                  11.16
131072                 14.13
262144                 19.45
524288                 30.12
1048576                51.90
2097152                95.03
4194304               182.27
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-lnx ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       3.37
2                       3.21
4                       3.14
8                       3.19
16                      3.13
32                      3.12
64                      3.14
128                     3.13
256                     3.10
512                     3.11
1024                    3.11
2048                    3.13
4096                    3.12
8192                    3.11
16384                   3.10
32768                   3.11
65536                   3.11
131072                  3.10
262144                  3.14
524288                  2.93
1048576                 2.84
2097152                 2.85
4194304                 8.94
```

### All-to-all collective latency, 8 ranks over 2 nodes

#### Host buffers
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-cxi ./collective/osu_alltoall

# OSU MPI All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      12.06
2                      12.04
4                      11.99
8                      11.96
16                     11.93
32                     12.02
64                     12.11
128                    12.30
256                    13.18
512                    13.29
1024                   13.34
2048                   13.51
4096                   13.78
8192                   17.28
16384                  19.03
32768                  23.78
65536                  36.91
131072                 63.21
262144                118.87
524288                241.67
1048576               517.90
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-lnx ./collective/osu_alltoall

# OSU MPI All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      17.16
2                      16.88
4                      16.79
8                      16.85
16                     16.97
32                     16.99
64                     17.06
128                    17.36
256                    22.20
512                    22.79
1024                   24.83
2048                   26.25
4096                   27.69
8192                   24.61
16384                  26.34
32768                  29.78
65536                  38.33
131072                 56.20
262144                 92.90
524288                195.14
1048576               416.20
```

#### Device buffers
- CXI provider
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-cxi ./collective/osu_alltoall -d cuda

# OSU MPI-CUDA All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      22.33
2                      22.14
4                      22.17
8                      22.28
16                     22.31
32                     22.42
64                     22.27
128                    22.60
256                    22.26
512                    22.40
1024                   22.42
2048                   22.73
4096                   23.02
8192                   26.66
16384                  28.10
32768                  32.60
65536                  45.30
131072                 70.62
262144                124.48
524288                235.12
1048576               488.45
```

- LINKx provider (SHM+CXI)
```
[clariden][amadonna@clariden-ln003 .edf]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-lnx ./collective/osu_alltoall -d cuda

# OSU MPI-CUDA All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      38.33
2                      37.68
4                      37.48
8                      37.56
16                     37.62
32                     36.80
64                     36.75
128                    36.66
256                    36.59
512                    36.45
1024                   36.55
2048                   36.72
4096                   37.39
8192                   41.49
16384                  42.05
32768                  43.79
65536                  50.21
131072                 63.58
262144                 93.09
524288                131.46
1048576               221.46
```

## Known issues and limitations
- The open source libfabric+CXI seems to be noticeably slower to detect CXI devices compared to the custom 1.15.x implementation provided by HPE. This results in longer startup times for jobs.

