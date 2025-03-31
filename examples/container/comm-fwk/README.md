# Communications framework image with HPE Slingshot connectivity

This image provides foundational libraries and frameworks to build HPC software and applications with support for the HPE Slingshot high-speed network and CUDA GPUs.

Slingshot connectivity is enabled through user-space CXI software components, which in turn allow interaction with the Cassini NICs.

This image is not intended to be used on its own, but to serve as a base to build higher-level software (e.g. MPI implementations) and application stacks.

Builds of the image are currently hosted on the Quay.io registry at https://quay.io/repository/ethcscs/comm-fwk.

## Contents

- CUDA 12.8.0 (including other dependencies like CUDNN and NCCL from the NVIDIA Docker Hub image)
- GDRCopy 2.4.4
- Libfabric (commit c17230da527aada634e0cc50d9feafc83d7106ce) with the following providers explicitly enabled:
    - CXI
    - AWS EFA
    - LINKx
- UCX 1.18.0
- HPE Cassini headers (commit 9a8a738a879f007849fbc69be8e3487a4abf0952)
- HPE CXI driver (commit af5d2ed4114134ea4eaf095d16af619573729045)
- HPE libcxi (commit 3a33b3966d2898ab129458c8813b0cbcdcec06f5)

## Notes

- This image and its derivatives are self-sufficient with respect to Slingshot connectivity, and do not require hooks to inject a custom CXI stack from the host.
- The libfabric EFA provider is included to leave open the possibility to experiment with derived images on AWS infrastructure as well.
- The libfabric LINKx provider is included to allow for experimentation.
- Although only the libfabric framework and its CXI provider are required to support the Slingshot network, this image also packages the UCX communication framework to allow building a broader set of software (e.g. some OpenSHMEM implementations) and supporting optimized Infiniband communication as well.
