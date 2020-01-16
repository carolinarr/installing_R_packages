# Installing R Packages on the HPC Clusters

## Quick Fix

If you encounter difficulties while trying to install a common R package then try this:

```
$ module load rh/devtoolset/7
$ R
> install.packages("<package-name>")
```

If the above fails, or you are interested in learning why the above is needed, then continue reading.

## Introduction

R packages may be distributed in source form or as compiled binaries. Packages that come in source form must be compiled before they can be installed in your `/home` directory. The recommended tool suite for doing this is the GNU Compiler Collection (GCC) and specifically g++, which is the C++ compiler.

To provide a stable environment for building software on our HPC clusters, the default version of GCC is kept the same for years at a time. To see the current version of g++, run the following command on one of the HPC clusters (e.g., Della):

```
$ g++ --version
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

While most R packages will compile with the current long-term version of GCC, some require a newer version. A newer version is made available by loading one of the latest Red Hat Developer Toolset (rh/devtoolset) modules:

```
$ module load rh/devtoolset/7
$ g++ --version
g++ (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)
```

After loading the `rh` module, start R and run `install.packages()` as shown above in the Quick Fix. Note that the C and Fortran compilers and related tools are also updated by this method which is important for some R packages.

Popular packages that require the `rh` module to be loaded for installation are `dplyr`, `tidyverse`, `rstan`, `devtools`, `rvest`, `argparse` and `lubridate`. There are many more. In fact, it is a good idea to always load the `rh` module before installing R packages.

## Before You Install

Make sure you have enough disk space before installing. This can be done by running the `checkquota` command:

```
$ checkquota

          Storage/size quota filesystem report for user: ceisgrub
Filesystem               Mount                 Used   Limit  MaxLim Comment
Adroit home              /home                8.3GB   9.3GB    10GB 
Adroit scratch           /scratch                 0       0       0 
Adroit scratch network   /scratch/network     8.2GB       0       0 

          Storage number of files used report for user: ceisgrub
Filesystem               Mount                 Used   Limit  MaxLim Comment
Adroit home              /home                52.9K    975K    1.0M 
Adroit scratch           /scratch                 1       0       0 
Adroit scratch network   /scratch/network     39.3K       0       0 

For quota increase requests please use this website:

         https://forms.rc.princeton.edu/quota
```

The difference between `Limit` and `Used` in the `/home` row is your available space. In the example above the user has `9.3 - 8.3 = 1 GB` available. Most packages require fewer than 0.1 GB. However, if you are installing many packages then disk space should be a concern. If you require more space then follow the link at the bottom of the output of `checkquota` to request more.

## Installing the First Package

After connecting to one of the clusters via ssh, load the `rh` module, start R and then install a package. The first time you do this you will want to answer 'yes' to the first two questions and then choose the value for 'USA (TN)' when asked to select a CRAN mirror.

Below is a full example session on Della:

```
$ ssh <YourNetID>@della.princeton.edu
$ module load rh/devtoolset/7
$ R

R version 3.6.1 (2019-07-05) -- "Action of the Toes"
Copyright (C) 2019 The R Foundation for Statistical Computing
Platform: x86_64-redhat-linux-gnu (64-bit)
...

> install.packages("argpase")
Installing package into ‘/usr/lib64/R/library’
(as ‘lib’ is unspecified)
Warning in install.packages("lubridate") :
  'lib = "/usr/lib64/R/library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel) yes
Would you like to create a personal library
‘~/R/x86_64-redhat-linux-gnu-library/3.6’
to install packages into? (yes/No/cancel) yes
--- Please select a CRAN mirror for use in this session ---
Secure CRAN mirrors 

 1: 0-Cloud [https]                   2: Algeria [https]                
 3: Australia (Canberra) [https]      4: Australia (Melbourne 1) [https]
 5: Australia (Melbourne 2) [https]   6: Australia (Perth) [https]      
 7: Austria [https]                   8: Belgium (Ghent) [https]        
 9: Brazil (BA) [https]              10: Brazil (PR) [https]            
11: Brazil (RJ) [https]              12: Brazil (SP 1) [https]          
13: Brazil (SP 2) [https]            14: Bulgaria [https]               
15: Chile (Santiago) [https]         16: China (Hong Kong) [https]      
17: China (Lanzhou) [https]          18: China (Shanghai) [https]       
19: Colombia (Cali) [https]          20: Czech Republic [https]         
21: Denmark [https]                  22: Ecuador (Cuenca) [https]       
23: Ecuador (Quito) [https]          24: Estonia [https]                
25: France (Lyon 1) [https]          26: France (Lyon 2) [https]        
27: France (Marseille) [https]       28: France (Montpellier) [https]   
29: Germany (Erlangen) [https]       30: Germany (Göttingen) [https]    
31: Germany (Münster) [https]        32: Germany (Regensburg) [https]   
33: Greece [https]                   34: Hungary [https]                
35: Iceland [https]                  36: Indonesia (Jakarta) [https]    
37: Ireland [https]                  38: Italy (Padua) [https]          
39: Japan (Tokyo) [https]            40: Japan (Yonezawa) [https]       
41: Korea (Busan) [https]            42: Korea (Gyeongsan-si) [https]   
43: Korea (Seoul 1) [https]          44: Korea (Ulsan) [https]          
45: Malaysia [https]                 46: Mexico (Mexico City) [https]   
47: Morocco [https]                  48: Norway [https]                 
49: Philippines [https]              50: Russia [https]                 
51: Spain (Madrid) [https]           52: Sweden [https]                 
53: Switzerland [https]              54: Turkey (Denizli) [https]       
55: Turkey (Mersin) [https]          56: UK (Bristol) [https]           
57: UK (London 1) [https]            58: USA (CA 1) [https]             
59: USA (IA) [https]                 60: USA (KS) [https]               
61: USA (MI 1) [https]               62: USA (MI 2) [https]             
63: USA (OR) [https]                 64: USA (TN) [https]               
65: USA (TX 1) [https]               66: Uruguay [https]                
67: (other mirrors)                  

Selection: 64
```

Your desired package and its dependencies will be built and installed. To help with organization, you can make different libraries and install your packages into the library of your choosing.

After your first session, you will only be asked to select the CRAN mirror when installing a package.

## GSL and GDAL Environment Modules

In addition to the `rh` module, some R packages require a newer version of the GNU Scientific Library (GSL). This can be accomplished with:

```
$ module load rh/devtoolset/7
$ module load gsl
$ R
> install.packages("<package-name>")
```

Another common module to load is `gdal`. This is required for installing `rgdal`, for instance:

```
$ module load rh/devtoolset/7
$ module load gdal
$ R
> install.packages("<package-name>")
```

IMPORTANT: If you built a package with the `gsl` or `gdal` modules loaded then you will need to add `module load gsl` or `module load gdal`, respectively, to your slurm script. One does not need to include the `rh` module in the Slurm script.

## Sample Slurm Script

The following Slurm script could be used to run a serial R job:

```
#!/bin/bash
#SBATCH --job-name=R-serial      # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G per cpu-core is default)
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=all          # send email on start, end and fault
#SBATCH --mail-user=<YourNetID>@princeton.edu

Rscript myscript.R
```

If you built a package with the `gsl` or `gdal` modules loaded then you will need to add `module load gsl` or `module load gdal`, respectively, before the `srun` command in the script above.

## Where to Run R Jobs

The Tiger cluster is designed for large parallel jobs. In an effort to dissaude users from running small jobs on Tiger, such as serial R jobs, the scheduler has been configured to penalize these submissions by causing long queue times. Della is ideal for serial R jobs. If you only have an account on Tiger and you want to run several small R jobs then please write to <a href="mailto:cses@princeton.edu">cses@princeton.edu</a> to request an account on Della. Be sure to explain the situation.

## Where to Store Your Files

You should run your jobs out of `/scratch/gpfs/<NetID>` on the HPC clusters. These filesystems are very fast and provide vast amounts of storage. Do not run jobs out of `/tigress`. That is, you should never be writing the output of actively running jobs to `/tigress`. The `/tigress` filesystem is slow and it should only be used for backing up the files that you produce on `/scratch/gpfs`. Your `/home` directory on all clusters is small and it should only be used for storing source code and executables.

The commands below give you an idea of how to properly run an R job:

```
$ ssh <NetID>@della.princeton.edu
$ cd /scratch/gpfs/<NetID>
$ mkdir myjob && cd myjob
# put R script and Slurm script in myjob
$ sbatch job.slurm
```

If the run produces data that you want to backup then copy or move it to `/tigress`:

```
$ cp -r /scratch/gpfs/<NetID>/myjob /tigress/<NetID>
```

For large transfers consider using `rsync` instead of `cp`. Most users only do back-ups to `/tigress` every week or so. While `/scratch/gpfs` is not backed-up, files are never removed. However, important results should be transferred to `/tigress`.

The diagram below gives an overview of the filesystems:

![tigress](https://tigress-web.princeton.edu/~jdh4/hpc_princeton_filesystems.png)

## Running RStudio on the HPC Clusters

RStudio is available through two web portals. If you have an account on Adroit or Della then browse to [https://myadroit.princeton.edu](https://myadroit.princeton.edu) or [https://mydella.princeton.edu](https://mydella.princeton.edu). To begin a session, click on "Interactive Apps" and then "RStudio Server".

![rstudio](https://tigress-web.princeton.edu/~jdh4/hpc_princeton_filesystems.png)

While most packages can be installed through RStudio, at times you will need to ssh to Adroit or Della and follow the Quick Fix directions at the top of this page to install certain packages. Or instead of ssh, one can get to the head node by clicking on "Clusters" and then "&lt;Name&gt; Cluster Shell Access" in the OnDemand menu.

For example, to install the `sf` package on Adroit, choose  "Clusters" and then "Adroit Cluster Shell Access". Then run these commands:

```
$ module load rh/devtoolset/7
$ module load gdal
$ R
> install.packages("sf")
> q()
```

Then start RStudio server from the "Interactive Apps" menu and in the "Console" tab do:

```
> dyn.load("/usr/local/gdal/2.2.4/lib64/libgdal.so.20.3.3")
> library("sf")
```

Or from the "Terminal" tab in RStudio do:

```
> module load gdal
> library("sf")
```

Note that graphics cannot be displayed in the "Terminal" tab. If the package you are installing does not depend on GDAL then you do not need to load that library or module.

## Optimizing Performance

The performance of numerically intensive packages such as `rstan` can be improved through compiler optimizations and vectorization. If you are an advanced user, before installing such a package, you may considering turning on these optimizations by creating a `~/.R/Makevars` file containing these lines:

```
CC = gcc
CXX = g++
FC = gfortran
CFLAGS = -O3 -ffast-math -march=native -mtune=native -fwhole-program -fpic -m64
CXXFLAGS = -O3 -ffast-math -march=native -mtune=native -fwhole-program -fpic -m64
FFLAGS = -O3 -ffast-math -march=native -mtune=native -fwhole-program -fpic -m64
```

After installing such a package with the `Makevars` settings above, you must remove or rename the `Makevars` file to prevent the optimizations from creating incompatibilities with packages to be installed at a later time.

Compiling packages in this way can create complications on Della. If you encounter an error such as `Illegal instruction` then add this line to your Slurm scripts:

```
#SBATCH --constraint=haswell|broadwell|skylake|cascade
```

The line above will cause your jobs to avoid the Ivybridge nodes which don't have the same vector instructions as the other nodes.

There may be times when you will need to specify a language standard. This can be done by adding a line to `Makevars` such as:

```CXX14STD = -std=c++14```


## Rmpi and HPC R

For directions on building `Rmpi` and approaches to parallelizing R scripts see [this repo](https://github.com/PrincetonUniversity/HPC_R_Workshop).

If you are using `Rmpi` on Della and you find that jobs hang, try adding this line to the end of your R script:

```
Rmpi::mpi.quit()
```

## FAQ

1. I tried to install an R package but the installation failed with this error message: `'for' loop initial declarations are only allowed in C99 mode`. What should I do?

   This problem can be solved by loading a newer version of GCC. To do this, before starting R, run this command on the command line: `module load rh/devtoolset/7`. Read the content above for the explanation for this solution.

2. Nothing is working properly and I want to delete all my R packages and start over. How do I do this?

   To delete all of your R packages and R files: `rm -rf ~/R ~/.R ~/.Rhistory ~/.Rprofile ~/.Rdata`. You may also need to remove lines from your `~/.bashrc` file if you added or modified environment variables.

3. How do I see where the R packages are installed?

   The paths to system and user packages can be seen with this R command: `> .libPaths()`. To specifically see the path to user packages use: `> Sys.getenv("R_LIBS_USER")`.

4. How do I see my installed packages?

   All base and user packages can be listed with the R command: `> installed.packages()`
   
5. How do I see the default packages?
   
   Default packages can be listed with the R command: `> getOption("defaultPackages")`
   
6. I have a list of packages. How do I install them all at once?

   `> install.packages(c("<package-name-1>", "<package-name-2>", ...))`
   
7. How do I remove a package?

   A package can be removed with the command: `> remove.packages("<package-name>")`
   
8. I have the source code for a package I want to install. How do I perform the installation?

   For RMPI, for instance, use this command on the command line:

   `$ R CMD INSTALL -l ~/.local/lib --no-test-load Rmpi_0.6-9.tar.gz`

   Then start R and do:

   `> library("Rmpi", lib.loc="/home/<NetID>/.local/lib")`

   Or one could set an R environment variable in `~/.bashrc`:

   `export R_LIBS=~/.local/lib:$R_LIBS`


## How to Contribute

If you have an improvement to this page such as a procedure to install a difficult package then please let us know. Please send an email to <a href="mailto:cses@princeton.edu">cses@princeton.edu</a> or sumbit a GitHub pull request.

## Getting Help from Data & Statistical Services (DSS)

For help on using R with data analysis please see the [DSS website](https://dss.princeton.edu). DSS offers online [tutorials](https://dss.princeton.edu/online_help/stats_packages/r/) and [training](https://dss.princeton.edu/training/) for performing data analysis with R as well as one-on-one appointments.

## Getting Help from Research Computing

If you encounter any difficulties while installing an R package on the HPC clusters then please
send an email to <a href="mailto:cses@princeton.edu">cses@princeton.edu</a> or attend a <a href="https://researchcomputing.princeton.edu/education/help-sessions">help session</a>.
