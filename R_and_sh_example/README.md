In this example, we provide another way to run simple parallel computing using an R script and a batch file. This involves an R file: `R_cluster_example.R` and an sh file: `example.sh`. First will walk through the `example.sh` file. 

## `example.sh`
This is the batch script that contains the `sbatch` parameters.

>#!/bin/bash<br/>
>#SBATCH --mail-user=<span>fred</span>@fredhutch.org <br/>
>#SBATCH --mail-type=END<br/>
>#SBATCH --array=[1-15]<br/>
>#SBATCH --nodes=1<br/>
>#SBATCH --output=Rout/par-%J.out<br/>
>#SBATCH --error=Rout/par-%J.err<br/>
>#SBATCH --cpus-per-task=1<br/>

>ml R/3.4.3-foss-2016b-fh2<br/>

>R CMD BATCH --no-save  R_cluster_example.R Rout/example_v_indep${SLURM_ARRAY_TASK_ID}.Rout<br/>

These options are explained in more detail elsewhere ([Using Slurm on Hutch Systems](#FredHutch/cluster_rhinoGizmo.md)). If using. Make sure to change the  --mail-user option as Fred does not need any more emails (in addition gizmo will only email to the email which the account is tied to). This will send you an email when the job is finished. In addition, The `Rout` option specified a directory which to output information on the job you've run and other errors. For our simulation the key line is
>#SBATCH --array=[1-15]
This tells the Hutch system that we are going to run 15 jobs. This can be changed to other options as well.

## `R_cluster_example.R`

This file has the actual R code which runs the simulations that we want to perform. While in this example it is simulations this can easily be changed to refer to files or any other parallel jobs want to perform. The key line in it is

>touse<-as.numeric(Sys.getenv('SLURM_ARRAY_TASK_ID'))

This picks which of the 15 jobs are being run. Then the additional file to ensure all 15 runs are different

>#######<br/>
>#Create table of simulation scenarios<br/>
>#######<br/>
>TabTabTabby<-expand.grid(c(0,0.5,1), #effect estimates<br/>
>                          seq(200,1000,by=200))# sample size<br/>

>#######<br/>
>#Grab what parameters using for this run<br/>
>#######<br/>
>BetaUse<-TabTabTabby[touse,1]<br/>
>N<-TabTabTabby[touse,2]<br/>

And then more things are done further on in the script `R_cluster_example.R`. The most important value in the above is the R object `TabTabTabby`. In this simulation it has information on the simulation parameters but it could be a vector of file locations that are then read in by R later on in the document. 

## Finally running the code

When you are ready to go. You simply run on the command line within gizmo

>sbatch example.sh

This is just one example of how to run parallel R jobs within slurm. More examples on how to run parallel jobs can be found in this repository. 