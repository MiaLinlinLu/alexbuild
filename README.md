# trdops

# SingularityCE (Community Edition)

## Installation on your own machine
To install singularity on your own machine you can follow: [Quick Start](https://sylabs.io/guides/latest/user-guide/quick_start.html)

### Example on Ubuntu 21.10 machine
#### Install system dependencies
```bash
# Ensure repositories are up-to-date
sudo apt-get update
# Install debian packages for dependencies
sudo apt-get install -y \
   build-essential \
   libseccomp-dev \
   pkg-config \
   squashfs-tools \
   cryptsetup
```
#### Installing Go
```bash
# Set latest stable version of Go from https://go.dev/dl/
GO_VERSION=1.17.6
# Download & install go
export VERSION=${GO_VERSION} OS=linux ARCH=amd64 && \  # Replace the values as needed
  wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz && \ # Downloads the required Go package
  sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz && \ # Extracts the archive
  rm go$VERSION.$OS-$ARCH.tar.gz    # Deletes the ``tar`` file`
# Set the enviroment variable PATH to point to go. I used .zshrc instead of .bashrc
echo 'export PATH=/usr/local/go/bin:$PATH' >> ~/.bashrc && \ source ~/.zshrc
# Check go version
go version
```

#### Installing SingularityCE
```bash
# Set latest stable version of Singularity from https://github.com/sylabs/singularity/releases
SINGULARITY_VERSION=3.9.5
# Download the Singularity
export VERSION=${SINGULARITY_VERSION} && # adjust this as necessary \
    wget https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce-${VERSION}.tar.gz && \
    tar -xzf singularity-ce-${VERSION}.tar.gz && \
    cd singularity-ce-${VERSION}
# Compile the Singularity
./mconfig && \
    make -C builddir && \
    sudo make -C builddir install
# Check singularity version
singularity version
```

## Common usage cases of Singularity

### Pulling an image
```bash
# Pull nvidia docker image with CUDA 11.3.1 CUDDN8 Dev version with Ubuntu 20.04
# from https://hub.docker.com/r/nvidia/cuda/tags
singularity pull docker://nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04
# Pull neurodebian nd18.04 version from https://hub.docker.com/_/neurodebian?tab=tags
singularity pull docker://neurodebian:nd18.04-non-free
```

### Running an Interactive Shell
```bash
# Run without gpu
singularity shell cuda_11.3.1-cudnn8-devel-ubuntu20.04.sif
# To run with gpu you need to add flag --nv
singularity shell --nv cuda_11.3.1-cudnn8-devel-ubuntu20.04.sif
```

### Running command inside the image
```bash
# For example, to print version of the nvcc
singularity exec cuda_11.3.1-cudnn8-devel-ubuntu20.04.sif nvcc --version
```

### Binding your directory to the Singularity Image
```bash
# To bind your directory with the image use --bind
# Here we binding your home directory with path /data and lists all its files using ls
singularity exec --bind $HOME:/data neurodebian_nd_afni.sif ls /data
```

### Building an image using sandbox to install AFNI
```bash
# Build a sandbox
singularity build --sandbox neurodebian_nd neurodebian_nd18.04-non-free.sif
# Since we need to use apt-get we have to put sudo ahead of the following commands
sudo singularity shell --writable neurodebian_nd
# Update repositories
apt-get update
# Install afni
apt-get install afni
# Exit and build image with afni
sudo singularity build neurodebian_nd_afni.sif neurodebian_nd
# Then you can run new image to check afni version
singularity exec neurodebian_nd_afni.sif afni --version
```

### Building an image using definitions files
```bash
# First you need to specify definition file for Singularity
# Then you can build an image accord to the definition file
sudo singularity build alex_build_v01.sif alex_build_v01.def
```