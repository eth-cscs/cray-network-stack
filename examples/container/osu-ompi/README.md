# OSU Micro-Benchmarks container with CXI - OpenMPI variant

Self-sufficient container image with OSU Micro-Benchmarks built on OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/osu-mb (look for the `cxi` mention in the tag).

## Notes (see also EDF TOML for reference)
- This image is self-sufficient with respect to Slingshot connectivity, and does not require hooks to inject a custom CXI stack from the host.
- Interaction with host PMIx needs to be understood better: environment variables for PSEC and GDS parameters have to be set on Alps to prevent warnings and crashes due to missing components
- Running a container requires a hook for bridging the PMIx interface between host and container
- The image includes also the libfabric LINKx provider for experimentation.
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
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       1.47
2                       3.00
4                       5.72
8                      12.03
16                     23.95
32                     47.92
64                     95.84
128                   190.46
256                   380.65
512                   763.58
1024                 1525.94
2048                 3038.59
4096                 6028.66
8192                10906.83
16384               18650.62
32768               21252.10
65536               22596.68
131072              23308.82
262144              23659.17
524288              23847.87
1048576             23935.47
2097152             23982.65
4194304             24005.74
```

#### Device buffers, inter-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 .edf]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       1.42
2                       2.85
4                       5.76
8                      11.48
16                     22.87
32                     45.65
64                     92.10
128                   181.26
256                   374.63
512                   744.66
1024                 1433.94
2048                 2946.06
4096                 5933.90
8192                10484.11
16384               14229.83
32768               19542.37
65536               22453.07
131072              23287.28
262144              23617.02
524288              23802.35
1048576             23909.87
2097152             23967.44
4194304             23995.77
```

#### Host buffers, intra-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_bw

# OSU MPI Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.88
2                       1.77
4                       3.53
8                       7.07
16                     14.07
32                     28.30
64                     56.71
128                   112.48
256                   221.80
512                   449.73
1024                  891.19
2048                 1754.09
4096                 3335.74
8192                 6291.86
16384               12683.76
32768               19022.51
65536               22586.17
131072              23297.32
262144              23663.76
524288              23834.21
1048576             23939.81
2097152             23987.19
4194304             24007.35
```

#### Device buffers, intra-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_bw D D

# OSU MPI-CUDA Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       0.86
2                       1.72
4                       3.45
8                       6.90
16                     13.80
32                     27.68
64                     55.04
128                   109.67
256                   214.21
512                   431.83
1024                  860.26
2048                 1683.27
4096                 3193.20
8192                 6049.43
16384               12182.97
32768               18965.54
65536               22430.21
131072              23297.94
262144              23642.05
524288              23811.59
1048576             23925.42
2097152             23971.60
4194304             23994.38
```

### Point-to-point bi-directional bandwidth

#### Host buffers, inter-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bibw

# OSU MPI Bi-Directional Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       1.49
2                       3.01
4                       6.08
8                      12.17
16                     24.32
32                     48.57
64                     97.20
128                   193.81
256                   387.57
512                   774.13
1024                 1549.50
2048                 3099.12
4096                 6196.92
8192                11733.62
16384               22015.19
32768               29944.20
65536               36536.17
131072              41016.28
262144              43677.34
524288              45144.12
1048576             45916.67
2097152             46306.38
4194304             46506.47
```

### Device buffers, inter-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N2 --environment=omb-ompi-cxi ./pt2pt/osu_bibw D D

# OSU MPI-CUDA Bi-Directional Bandwidth Test v7.5
# Datatype: MPI_CHAR.
# Size      Bandwidth (MB/s)
1                       1.54
2                       3.15
4                       6.32
8                      12.69
16                     25.34
32                     50.55
64                    100.98
128                   201.22
256                   409.19
512                   818.15
1024                 1638.67
2048                 3270.55
4096                 6540.91
8192                11535.23
16384               19214.88
32768               29017.79
65536               36941.88
131072              41300.76
262144              43828.50
524288              45220.24
1048576             45944.63
2097152             46327.35
4194304             46520.75
```

### Point-to-point latency

#### Device buffers, intra-node communication
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N1 -n2 --environment=omb-ompi-cxi ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       3.95
2                       3.91
4                       3.92
8                       3.89
16                      3.92
32                      3.91
64                      3.88
128                     4.24
256                     4.33
512                     4.38
1024                    4.46
2048                    4.63
4096                    4.90
8192                    7.92
16384                   8.64
32768                   9.53
65536                  10.90
131072                 13.73
262144                 19.25
524288                 30.03
1048576                51.81
2097152                95.06
4194304               181.57
```

### All-to-all collective latency, 8 ranks over 2 nodes

#### Host buffers
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-cxi ./collective/osu_alltoall

# OSU MPI All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      12.45
2                      12.41
4                      12.42
8                      12.38
16                     12.37
32                     12.44
64                     12.48
128                    12.69
256                    13.62
512                    13.66
1024                   13.74
2048                   13.96
4096                   14.17
8192                   17.74
16384                  19.37
32768                  23.81
65536                  36.67
131072                 62.84
262144                119.25
524288                237.68
1048576               508.22
```

#### Device buffers
- CXI provider
```
[santis][amadonna@santis-ln002 ~]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-ompi-cxi ./collective/osu_alltoall -d cuda

# OSU MPI-CUDA All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      22.07
2                      21.89
4                      21.94
8                      21.95
16                     22.02
32                     22.06
64                     21.93
128                    21.81
256                    22.10
512                    22.22
1024                   22.34
2048                   22.61
4096                   22.89
8192                   26.57
16384                  27.94
32768                  32.64
65536                  45.05
131072                 70.24
262144                123.81
524288                232.66
1048576               482.51
```

## Known issues and limitations
- The open source libfabric+CXI seems to be noticeably slower to detect CXI devices compared to the custom 1.15.x implementation provided by HPE. This results in longer startup times for jobs.
- Reliabli using the LINKx provider at full performance on all the test cases is still work in progress
