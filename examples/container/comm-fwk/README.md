# Communications framework image with HPE Slingshot connectivity

This image provides foundational libraries and frameworks to build HPC software and applications with support for the HPE Slingshot high-speed network and CUDA GPUs.

Slingshot connectivity is enabled through user-space CXI software components, which in turn allow interaction with the Cassini NICs.

This image is not intended to be used on its own, but to serve as a base to build higher-level software (e.g. MPI implementations) and application stacks.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/comm-fwk (look for the `cxi` mention in the tag).

## Contents

- CUDA 12.8.1 (including other dependencies like NCCL from the NVIDIA Docker Hub image)
- GDRCopy 2.5
- XPMEM (commit 0d0bad4e1d07b38d53ecc8f20786bb1328c446da - corresponds to version 2.6.5-36 in Spack)
- A patched Libfabric 2.1.0-dev (see notes) with the following providers explicitly enabled:
    - CXI
    - AWS EFA
    - LINKx
- UCX 1.18.0
- HPE Cassini headers (commit 9a8a738a879f007849fbc69be8e3487a4abf0952)
- HPE CXI driver (tag `release/shs-12.0.0`)
- HPE libcxi (tag `release/shs-12.0.0`)

## Notes

- This image and its derivatives are self-sufficient with respect to Slingshot connectivity, and do not require hooks to inject a custom CXI stack from the host.
- The libfabric in this image contains an experimental fix for CUDA GDR support (more details [here](https://github.com/ofiwg/libfabric/issues/10865#issuecomment-2735866065)); the fix is applied on top of libfabric's `main` commit at the moment of writing (commit faf13301a4a9628b6c9a28a06d936258c6d368af), and the code is available from [this forked branch](https://github.com/Madeeks/libfabric/tree/cuda_gdrcopy_unregister_fix).
- The libfabric EFA provider is included to leave open the possibility to experiment with derived images on AWS infrastructure as well.
- The libfabric LINKx provider is included to allow for experimentation.
- Although only the libfabric framework and its CXI provider are required to support the Slingshot network, this image also packages the UCX communication framework to allow building a broader set of software (e.g. some OpenSHMEM implementations) and supporting optimized Infiniband communication as well.
