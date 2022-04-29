# openmpi installation on Dardel

Assuming you will be installing openmpi under ~/opt

## Step 00 - Download source codes

## Step 01 - Compile Autoconf

AUTO_VERSION=2.7.1 
mkdir -p ~/opt/autoconf/$AUTO_VERSION
cd ~/source/
./configure --prefix=/home/path/to/install/autoconf/$AUTO_VERSION
make
make install
