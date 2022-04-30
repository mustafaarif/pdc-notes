# Crystal on Dardel

### Background
#### Caveats
1. glibc version on Dardel is 2.26, whereas Crystal precompiled binaries require 2.7 atleast.
```
ldd --version
ldd (GNU libc) 2.26
```
2. Crystal precompiled binaries are built against OpenMPI and IntelMPI, both of them are not available on Dardel.

#### Solutions
- To address glibc problem, we have created a simple Ubuntu container with gfortran installed. When Crystal is launched on Dardel, all the host libraries related to high speed network are bind mount inside the container. This way Crystal can utilize latest version of Glibc available inside the container while continue to use Host (Dardel) HSN libraries. 
- Another challenge was the host (Dardel) only has cray-mpich available. To address this issue, we have done an experimental compile of OpenMPI on Dardel. This installation of OpenMPI though can run independent on Dardel, however due to the Glibc issue we are still dependent on container. So when Crystal is launched on Dardel, the OpenMPI libraries are also bind mount inside the container. This way the Crystal utilizes the container Glibc, host OpenMPI and host HSN libraries.

**Note:** At the moment, Singularity and OpenMPI are not installed on System path and users would have to compile both packages in their home/project directories.

## Step 00: Download Source Codes

- Assuming you will be installing these packages under ~/opt and the source code will be downloaded to ~/source
- While on login node, download the source code

```
AUTO_VERSION=2.71
OPENMPI_VERSION=4.1.3

mkdir -p ~/source && cd ~/source
wget http://ftp.gnu.org/gnu/autoconf/autoconf-$AUTO_VERSION.tar.gz
wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-$OPENMPI_VERSION.tar.gz
tar -xvzf autoconf-$AUTO_VERSION.tar.gz
tar -xvzf openmpi-$OPENMPI_VERSION.tar.gz
```

## Step 01: Compile required packages i.e. Singularity, AutoConf, OpenMPI

## Step 02: Create SLURM.job

## Evaluate Results
