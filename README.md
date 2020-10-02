## fastractor
 
> Aims to extract `.fasta` data using [DEXTRACTOR](https://github.com/thegenemyers/DEXTRACTOR) from a series of remote `.bax.h5` and `.bas.h5` files served via Http GET.

## Content

- [**Requirements**](#requirements)
- [**Install the DEXTRACTOR Dependencies**](#install-the-dextractor-dependencies)
	- [Install zlib-devel](#a-install-zlib-devel)
	- [Build and Install hdf5](#b-build-and-install-hdf5)
- [**Install DEXTRACTOR**](#install-dextractor)
- [**Dextractor Sample Usage**](#dextractor-sample-usage)
	- [Download a Dataset](#download-a-dataset)
	- [Dextractor fasta Extractor Commands](#dextractor-fasta-extractor-commands)
- [**fastractor Usage**](#fastractor-usage)
- [**References**](#references)

## Requirements

The following requirements and dependencies were used for this project. Other system and software configurations are open for testing.

1. **VMWare Workstation Player 15** (for Windows OS)
2. **Windows 10 Pro** (host OS)
	- Version 1909 (OS Build 18363.1082)
	- Processor: Intel(R) Core(TM) i7-6700HQ
	- CPU @2.60GHz 2.60 GHz
	- GPU: NVIDIA GeForce GTX 1060, 6 GB Dedicated GPU Memory
	- Memory: 16 GB
	- System type: 64-bit OS, x64-based processor
3. **CentOS Linux release 8.2.2004 (Core)** - VM (guest OS) running on VMWare Player 
	- Memory: 4 GB
	- Processors: 2
	- Hard Disk: 32 GB
	- kernel 4.18.0-193.19.1.el8\_2.x86\_64
	- gcc 8.3.1
	- make 4.2.1
	- perl 5 version 26
4. **Dextractor dependencies**
	- zlib-devel
		- version: 1.2.11
		- release: 16.el8_2
		- architecture: i686
	- hdf5 v1.12.0 
		- version [[1.12.0]](https://www.hdfgroup.org/downloads/hdf5/source-code/) was built from source on the Centos OS for this project
	- Install these dependencies first if they are not yet installed. Read on [**Install the Dextractor Dependencies**](#install-the-dextractor-dependencies) for more information.


## Install the DEXTRACTOR Dependencies

The following dependencies must first be installed and configured before proceeding to build dextractor.

### A. Install zlib-devel

1. zlib-devel version 1.2.11-16.el8_2 was used for this project:  

		architecture: x86_64
		version: 1.2.11-16.el8_2
		repository: BaseOS

2. Install zlib-devel.

		yum info zlib-devel
		sudo yum install zlib-devel

### B. Build and Install hdf5

1. Download the latest hdf5 source code for UNIX from the official [hdf5 downloads](https://www.hdfgroup.org/downloads/hdf5/source-code/) website. **hdf5-1.12.0.tar.gz** was used for this project.  
	- `wget https://hdf-wordpress-1.s3.amazonaws.com/wp-content/uploads/manual/HDF5/HDF5_1_12_0/source/hdf5-1.12.0.tar.gz`
2. Build and install hdf5 to `/usr/local/hdf5`.  

		$tar -zxvf hdf5-1.12.0.tar.gz
		$cd hdf5-1.12.0
		$./configure --prefix=/usr/local/hdf5
		$make
		$make check # run test suite.
		$sudo make install
		$sudo make check-install # verify installation.

	> **INFO:**
	> 
	> - If you want to change hdf5's **default** installation directory, use a different path in `./configure --prefix=<path_to_new_hdf5_directory>`.
	> - The custom installation directory should be different from the extracted `/hdf5-1.12.0` directory.
3. Verify the hdf5 installation.
	- Check if a `/usr/local/hdf5`directory exists (for default installation)
	- Check if you your custom hdf5 directory exists, if you specified a different installation location in `./configure --prefix`
	- Re-check your installation process if the hdf5 installation directory doesn't exist by this point
3. Add the installed hdf5 lib to `/etc/ld.so.conf`
	- `sudo nano /etc/ld.so.conf`
	- Add the installed hdf5 lib path at the end of the line and save.  

			include ld.so.conf.d/*.conf
			/usr/local/hdf5/lib/
		> **INFO:** The sample code uses hdf5's **default installation** directory. Encode your custom hdf5 installation directory, if you specified a different location during installation.
	- Run `sudo ldconfig`


## Install DEXTRACTOR

> **WARNING:** Proceed to build dextractor only after installing the required dependencies from the [**Install the Dextractor Dependencies**](#install-the-dextractor-dependencies) section.

1. Clone the dextractor repository.
	- DEXTRACTOR'S **master** branch @revision [63e0fdd
](https://github.com/thegenemyers/DEXTRACTOR/commit/63e0fdd78f14d7240c951d885773d7e12a46350b) was used for this project.  
	- `git clone https://github.com/thegenemyers/DEXTRACTOR.git`
2. Update the `dex2DB.c` source code with reference to Issue [[#26]](https://github.com/thegenemyers/DEXTRACTOR/issues/26).
	- Replace all occurrences of `DB_CSS` to `DB_CCS` in line `#650` and `#846`.
3. (Optional) Adjust **hdf5**'s `PATH_HDF5` variable in the `Makefile` if you specified a different **hdf5** installation location on `./configure --prefix`. The default installation location is `/usr/local/hdf5`.
	- Open the `Makefile`  
`nano Makefile`
	- Update the **PATH_HDF5** variable on line #2 with your custom hdf5 installation directory, i.e.:  
`PATH_HDF5 = /home/adminuser/hdf5`
4. Navigate to the `/DEXTRACTOR` directory from the command line.
	- Run `make`


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

@weaponsforge  
20201001