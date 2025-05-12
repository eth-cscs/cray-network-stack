# OpenMPI container with HPE Slingshot support

Container image providing OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support. Intended to be used as base to build HPC application stacks.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/ompi (look for the `cxi` mention in the tag).

Please refer to the communications framework (`comm-fwk`) container image for a more detailed overview of the software available in this image.

## Notes
Currently using OpenMPI 5.0.8RC1 to include [this fix](https://github.com/open-mpi/ompi/pull/12290), which was needed to run successfully with the LINKx provider and CUDA device buffers.
