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
```
#!/bin/bash
#SBATCH -J Crystal
#SBATCH -p main
#SBATCH --time=24:00:00
##SBATCH --reservation=sing
#SBATCH -N 2
#SBATCH -A pdc.staff
#SBATCH --ntasks-per-node=128
#SBATCH --output=slurm-%j.out
#SBATCH --error=slurm-%j.err

module unload cray-mpich

export PATH=/cfs/klemming/home/m/muarif/opt/openmpi/4.1.3/bin:$PATH
export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/hostlibs:/usr/hostlibs/slurm:/usr/hostlibs/libibverbs:/opt/cray/libfa                                                                                       bric/1.11.0.4.67/lib64:/opt/cray/xpmem/2.2.40-7.0.1.0_2.4__g1d7a24d.shasta/lib64:/opt/cray/pe/pmi                                                                                       /default/lib:/opt/cray/pe/pals/1.0.17/lib:/opt/cray/pe/pmi/default/lib:/cfs/klemming/home/m/muari                                                                                       f/opt/openmpi/4.1.3/lib:$LD_LIBRARY_PATH
export SINGULARITYENV_LD_LIBRARY_PATH=$LD_LIBRARY_PATH
#echo $SINGULARITYENV_LD_LIBRARY_PATH

CRYSTALBIN=/cfs/klemming/home/m/muarif/opt/crystal/v102/bin
TMPDIR=$SCRATCHDIR/Crystal/$SLURM_JOB_ID
mkdir -p ${TMPDIR}
cp INPUT ${TMPDIR}
cd ${TMPDIR}

echo "Starting at.." ; date
mpirun -np 256 ~/opt/singularity/bin/singularity exec -B /cfs/klemming/scratch:/cfs/klemming/scra                                                                                       tch -B /etc/libibverbs.d:/etc/libibverbs.d:ro -B /var/spool:/var/spool -B /usr/lib64:/usr/hostlib                                                                                       s -B /opt:/opt:ro -B /var/opt:/var/opt:ro ~/opt/openmpi-hybrid02 $CRYSTALBIN/Pcrystal > OUTPUT 2>                                                                                       &1
echo "Ending at.." ; date

# Change to the original directory and copy the OUTPUT file from TMPDIR
cd -
cp ${TMPDIR}/OUTPUT .
```

## Evaluate Results
