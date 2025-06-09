# NCCL Tests container with CXI

Self-sufficient container image with [NCCL Tests](https://github.com/NVIDIA/nccl-tests) 2.16.4, installed on top of OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/nccl-tests (look for the `cxi` mention in the tag).

## Notes

- This image is self-sufficient with respect to Slingshot connectivity, and does not require hooks to inject a custom CXI stack from the host.
- Running a container requires a hook for bridging the PMIx interface between host and container; PMIx is still used by the tests for initial wire-up.
- This image is build on top of the OpenMPI (`ompi`) and communications framework (`comm-fwk`) images from this repository; please refer to the documentation of those images for more details about the software available in this image.

## Examples

- Allreduce test, 2 nodes, 8 GPUs
```
[santis][amadonna@santis-ln002 ~]$ srun -N2 --ntasks-per-node=4 --mpi=pmix --environment=nccl-tests-cxi /nccl-tests-2.16.4/build/all_reduce_perf -b 8 -e 128M -f 2
# nThread 1 nGpus 1 minBytes 8 maxBytes 134217728 step: 2(factor) warmup iters: 5 iters: 20 agg iters: 1 validation: 1 graph: 0
#
# Using devices
#  Rank  0 Group  0 Pid 267630 on  nid005098 device  0 [0009:01:00] NVIDIA GH200 120GB
#  Rank  1 Group  0 Pid 267631 on  nid005098 device  1 [0019:01:00] NVIDIA GH200 120GB
#  Rank  2 Group  0 Pid 267632 on  nid005098 device  2 [0029:01:00] NVIDIA GH200 120GB
#  Rank  3 Group  0 Pid 267633 on  nid005098 device  3 [0039:01:00] NVIDIA GH200 120GB
#  Rank  4 Group  0 Pid 283806 on  nid005099 device  0 [0009:01:00] NVIDIA GH200 120GB
#  Rank  5 Group  0 Pid 283807 on  nid005099 device  1 [0019:01:00] NVIDIA GH200 120GB
#  Rank  6 Group  0 Pid 283808 on  nid005099 device  2 [0029:01:00] NVIDIA GH200 120GB
#  Rank  7 Group  0 Pid 283809 on  nid005099 device  3 [0039:01:00] NVIDIA GH200 120GB
#
#                                                              out-of-place                       in-place          
#       size         count      type   redop    root     time   algbw   busbw #wrong     time   algbw   busbw #wrong
#        (B)    (elements)                               (us)  (GB/s)  (GB/s)            (us)  (GB/s)  (GB/s)       
           8             2     float     sum      -1    18.74    0.00    0.00      0    18.28    0.00    0.00      0
          16             4     float     sum      -1    18.21    0.00    0.00      0    18.21    0.00    0.00      0
          32             8     float     sum      -1    18.50    0.00    0.00      0    18.44    0.00    0.00      0
          64            16     float     sum      -1    20.28    0.00    0.01      0    20.18    0.00    0.01      0
         128            32     float     sum      -1    19.89    0.01    0.01      0    19.59    0.01    0.01      0
         256            64     float     sum      -1    19.94    0.01    0.02      0    19.91    0.01    0.02      0
         512           128     float     sum      -1    20.52    0.02    0.04      0    20.56    0.02    0.04      0
        1024           256     float     sum      -1    22.14    0.05    0.08      0    22.01    0.05    0.08      0
        2048           512     float     sum      -1    24.00    0.09    0.15      0    23.36    0.09    0.15      0
        4096          1024     float     sum      -1    34.41    0.12    0.21      0    33.73    0.12    0.21      0
        8192          2048     float     sum      -1    39.07    0.21    0.37      0    38.00    0.22    0.38      0
       16384          4096     float     sum      -1    39.81    0.41    0.72      0    39.00    0.42    0.74      0
       32768          8192     float     sum      -1    40.36    0.81    1.42      0    39.33    0.83    1.46      0
       65536         16384     float     sum      -1    65.50    1.00    1.75      0    70.09    0.94    1.64      0
      131072         32768     float     sum      -1    80.50    1.63    2.85      0    87.44    1.50    2.62      0
      262144         65536     float     sum      -1    123.3    2.13    3.72      0    113.7    2.30    4.03      0
      524288        131072     float     sum      -1    122.7    4.27    7.48      0    120.9    4.34    7.59      0
     1048576        262144     float     sum      -1    136.7    7.67   13.43      0    167.0    6.28   10.99      0
     2097152        524288     float     sum      -1    182.1   11.52   20.16      0    178.4   11.76   20.57      0
     4194304       1048576     float     sum      -1    294.6   14.24   24.91      0    288.0   14.56   25.49      0
     8388608       2097152     float     sum      -1    341.2   24.58   43.02      0    347.4   24.15   42.26      0
    16777216       4194304     float     sum      -1    442.8   37.89   66.31      0    451.5   37.16   65.03      0
    33554432       8388608     float     sum      -1    686.2   48.90   85.57      0    684.2   49.04   85.82      0
    67108864      16777216     float     sum      -1   1014.8   66.13  115.73      0   1015.0   66.12  115.71      0
   134217728      33554432     float     sum      -1   1773.7   75.67  132.43      0   1779.6   75.42  131.99      0
# Out of bounds values : 0 OK
# Avg bus bandwidth    : 20.7448
#
```
