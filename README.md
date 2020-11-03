## fastractor
 
> Aims to extract `.fasta` data using [DEXTRACTOR](https://github.com/thegenemyers/DEXTRACTOR) from a series of local (3) `.bax.h5` files and (1) `.bas.h5` file.  

- This guide uses a pre-built [hdf5 binary distribution](https://www.hdfgroup.org/downloads/hdf5/) for linux.  
- To use hdf5 **built from source code**, refer to the [previous installation](https://github.com/weaponsforge/fastractor/wiki/Dextractor:-Build-HDF5-from-Source-Code) guide for more information.

## Content

- [**Requirements**](#requirements)
- [**Install the DEXTRACTOR Dependencies**](#install-the-dextractor-dependencies)
	- [Download the hdf5 Binary Distribution](#download-the-hdf5-binary-distribution)
	- [Set the hdf5 Library Path](#set-the-hdf5-library-path)
- [**Install DEXTRACTOR**](#install-dextractor)
- [**Dextractor Sample Usage**](#dextractor-sample-usage)
	- [Download a Dataset](#download-a-dataset)
	- [Dextractor fasta Extractor Commands](#dextractor-fasta-extractor-commands)
- [**fastractor Usage**](#fastractor-usage)
- [**References**](#references)

## Requirements

The following requirements and dependencies were used for this project. Other system and software configurations are open for testing.

1. **Virtual Box version 6.14** (for Windows OS)
2. **Windows 10 Pro** (host OS)
	- Version 1909 (OS Build 18363.1082)
	- Processor: Intel(R) Core(TM) i7-6700HQ
	- CPU @2.60GHz 2.60 GHz
	- GPU: NVIDIA GeForce GTX 1060, 6 GB Dedicated GPU Memory
	- Memory: 16 GB
	- System type: 64-bit OS, x64-based processor
3. **CentOS Linux release 8.2.2004 (Core)** - VM (guest OS) running on Virtual Box
	- Memory: 4 GB
	- Processors: 2
	- Hard Disk: 95 GB
	- kernel 4.18.0-193.19.1.el8\_2.x86\_64
	- gcc 8.3.1
4. **Dextractor dependencies**
	- hdf5 v1.12.0 
		- a pre-built binary distribution [[hdf5-1.12.0-linux-centos7-x86_64-shared-production.tar.gz]](https://www.hdfgroup.org/downloads/hdf5/) was downloaded and set-up on the Centos OS for this project
	- Install the hdf5 dependency first if it is not yet installed. Read on [**Install the Dextractor Dependencies**](#install-the-dextractor-dependencies) for more information.


## Install the DEXTRACTOR Dependencies

The following dependencies must first be installed and configured before proceeding to build dextractor.

### Download the hdf5 Binary Distribution

1. Download the hdf5 version 1.12.0 **pre-built binary distribution** for linux from the official [hdf5 downloads](https://www.hdfgroup.org/downloads/hdf5/) website. **hdf5-1.12.0-linux-centos7-x86_64-shared-production.tar.gz** was used for this project.  
	- **NOTE:** You may need to create an hdf5 group account to view and access the download link below.
	- `wget https://hdf-wordpress-1.s3.amazonaws.com/wp-content/uploads/manual/HDF5/HDF5_1_12_0/binaries/unix/hdf5-1.12.0-linux-centos7-x86_64-shared-production.tar.gz`
2. Extract the downloaded binary distribution.  
`tar -zxvf hdf5-1.12.0-linux-centos7-x86_64-shared-production.tar.gz`  


### Set the hdf5 Library Path

1. Take note of the extracted binary distribution's **full path** and **/lib path** from the **Download the hdf5 Binary Distribution** section, #2:  
`/home/adminuser/hdf5-1.12.0-linux-centos7-x86_64-shared`.
2. Temporarily export the installed hdf5's `/lib` path from #1 to the `LD_LIBRARY_PATH` environment variable.
   - From the previous examples:  
`export LD_LIBRARY_PATH=/home/adminuser/hdf5-1.12.0-linux-centos7-x86_64-shared/lib`
   - **INFO:** Do this step every time the machine is rebooted, or every time you open a new terminal. This will fix the error:
   - >```dextract: error while loading shared libraries: libhdf5.so.200: cannot open shared object file: No such file or directory.```

## Install DEXTRACTOR

> **WARNING:** Proceed to build dextractor only after installing the required dependencies from the [**Install the Dextractor Dependencies**](#install-the-dextractor-dependencies) section.

1. Clone the dextractor repository.
	- DEXTRACTOR'S **master** branch @revision [63e0fdd
](https://github.com/thegenemyers/DEXTRACTOR/commit/63e0fdd78f14d7240c951d885773d7e12a46350b) was used for this project.  
	- `git clone https://github.com/thegenemyers/DEXTRACTOR.git dextractor`
2. Update the `dex2DB.c` source code with reference to Issue [[#26]](https://github.com/thegenemyers/DEXTRACTOR/issues/26).
	- Replace all occurrences of `DB_CSS` to `DB_CCS` in line `#650` and `#846`.
3. Adjust **hdf5**'s `PATH_HDF5` variable in the `Makefile` to specify the **hdf5** binary release installation location. for this example, hdf5's installation location is in `/home/adminuser/hdf5-1.12.0-linux-centos7-x86_64-shared`.
	- Open the `Makefile`  
`nano Makefile`
	- Update the **PATH_HDF5** variable on line #2 with your custom hdf5 installation directory, i.e.:  
`PATH_HDF5 = /home/adminuser/hdf5-1.12.0-linux-centos7-x86_64-shared`
4. Navigate to the `/dextractor` directory from the terminal.
	- Run `make`
5. For convenience, permanently make dextractor globally accessible anywhere from the terminal. Use the full path where dextractor's binaries are built, for example in **/home/adminuser/dextractor**: 

   ```
   cat <<EOF | sudo tee /etc/profile.d/dextractor.sh
   export PATH=$PATH:/home/adminuser/dextractor`EOF
   source /etc/profile.d/dextractor.sh
   ```


## DEXTRACTOR Sample Usage

### Download a Dataset

Obtain a single dataset generated by a *PacBio RS II* run. This should have (3) `*.bax.h5`  files (`G.1.bax.h5`, `G.2.bax.h5`, `G.3.bax.h5`) and (1) `.bas.h5` file.

- Create a directory to contain the `.h5` files input  
`mkdir files`

- Create a directory to contain the `.fasta` file(s) output  
`mkdir fasta`


### Dextractor fasta Extractor Commands



- **Extract `.fasta` data from a single `.bax.h5` file.**  
`./dextract -vf -ofasta/extracted.fasta files/m131019_072530_42175_c100583702550000001823087704281410_s1_p0.1.bax.h5`

- **Extract `.fasta` data from all (3) `.bax.h5` files**  
`./dextract -vf -ofasta/data.fasta files/m131019_072530_42175_c100583702550000001823087704281410_s1_p0.*.bax.h5`

## fastractor Usage

> This section is a WIP.

## References

[[1]](https://dazzlerblog.wordpress.com/command-guides/dextractor-command-guide/) - dextractor blog  
[[2]](https://dazzlerblog.wordpress.com/2014/03/22/the-dextractor-module-save-disk-space-for-your-pacbio-projects/) - use dextractor to save disk space  
[[3]](https://trello.com/c/SaI1183f) - install hdf5 using cmake

@weaponsforge  
20201001
