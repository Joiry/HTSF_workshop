# RNA-seq Count & Differential Expression

Welcome to an introduction of RNA-seq analysis, a part of the High Throughput Sequencing Facility introduction to Deep Sequencing Analysis.

&nbsp;

&nbsp;
____

## Setup

#### Log into killdevil using your X11 or Xquartz options.

MAC Users   

+ Open XQuartz
+ Login using `ssh -Y <youronyen>@killdevil.unc.edu

PC Users    

+ Tunnel in with X11

#### Load modules

We will need htseq-counts, and r

```
#EXECUTE THESE COMMANDS
$ module load htseq-count
$ module load r
```

## Get the .bam files for all the alignments

```
# NAVIGATE TO <yourversion>/01_RNASeqDemo/04_results
# EXECUTE THE CODE:
$ mv tophat/ ../06_test/in_class_session
$ 
$ pwd                   # Check that you are in <yourversion>/01_RNASeqDemo/04_results
$ cp -r /netscr/tristand/HTSF_RNASeq_Demo/01_RNASeqDemo/04_results/tophat/ .
```



&nbsp;

&nbsp;
____


## HTseq-count Demo

Will read as input an accepted_hits_bam file from tophat.

```
htseq-count \                                           # Literally call htseq-count
    --format=bam \                                      # input format of the incoming file is bam
    --order=pos \                                       # .bam file has been ordered
    --stranded=no \                                     # RNA-seq is NOT stranded
    --minaqual=20 \                                     # Throw out any alignments with minquality scores less than 20
    --type=exon \                                       # Use concatenated exons as "genes"
    --mode=union                                        # Run in "union" mode   
    ../04_results/tophat/${file}_opd/accepted_hits.bam \      # Here is the place to put the output
    /proj/seq/data/HG19_UCSC/Annotation/Genes/genes.gtf \     # Here is the .gtf annotation file
    1> ../04_results/htseqcounts/${file}_counts.txt           # Here is the input, a .bam file from tophat
```
  
I have already written the code for you in a script called **script06_htseq-count.sh***. This script loops through every sample and counts all reads associated with the gene models in the human genome annotation file (a .gtf document).


```
# NAVIGATE TO <yourversion>/01_RNASeqDemo/05_scripts
# EXECUTE THE CODE:
$ pwd
$ bsub -q week -n 1 -o ../00_logs/%J_htseqcount.log "bash script06_htseq-count.sh"  
```

To see what happened
+ read the log file
+ Look at the output of htseq-counts in `/04_results/htseqcounts/`



&nbsp;

&nbsp;
____

## DESeq2 Demo

Ensure you have done the following setup:
## Setup

MAC Users   

+ Open XQuartz
+ Login using `ssh -Y <youronyen>@killdevil.unc.edu

PC Users    

+ Tunnel in with X11

#### Load modules

We will need htseq-counts, and r

```
#EXECUTE THESE COMMANDS
$ module load r
$ module load pico
```

#### Get the DESeq2 files...

```
Make a file structure

#Navigate to <yourversion>

#EXECUTE THESE COMMANDS:    
$ pwd                                   # Check you are in <yourversion> which is probably /netscr/<youronyen>
$ cp -r /netscr/erinosb/HTSF_RNASeq_Demo/02_DESeq2Demo .
$ ls
```

You have just copied over a downsampled set of the Male versus Female dataset, a small table on the expeirment's design, and an .R code containing a demonstration DESeq run.

** Watch demo of R**

#### Play around with interactive R.

Copy and paste each line of code from `deseq2_demo_EON_150725.R` into an interactive browser session using 

```
#Navigate to <yourversion>/02_DESeqDemo/
$ pwd                               # check that you are in <yourversion>/02_DESeqDemo
$ bsub --Ip R
# We will copy and paste commands into the interactive prompt
```

We will start by loading up some packages into R.
Copy and paste the following things into your command line:

```R
library("DESeq2")
library("RColorBrewer")
library("gplots")
library("ggplot2")
```

Next, tell the script your current working directory
You will need to substitute "<yourversion>/02DESeqDemo/" with something that is specific to your current working directory. This should be the same as what you call when you type `$pwd` in double quotes.


```R
homedir=("<yourversion>/02_DESeqDemo/")
```

Now let's load up the data from the `input/` folder

```R
#Switch to the input folder
inputdir = paste(homedir, "input", sep = "")
setwd(inputdir)

#Import the dataset
Counts_bbmap <- read.table("counts_bbmap.txt", header = TRUE, row.names=1)  
```


Great now we have the data uploaded! Let's look at it.
Execute each line one by one

```R
head(Counts_bbmap)        # Peek at Counts_bbmap
dim(Counts_bbmap)         # What are the dimensions?
summary(Counts_bbmap)     # What do the values of each sample represent?
str(Counts_bbmap)         # What is the structure of the dataset

```

To quit out of R interactive, type

```R
q()
```



Alternatively, you can execute R on the command line like so...

```
$ bsub -q week -n 1 -o %J_deseq.log "R --vanilla < deseq2_demo_EON_150725.R"
```

For this to work, you will need to change one line of code...

```R
#homedir="/netscr/erinosb/HTSF_RNASeq_Demo/02_DESeq2Demo" ##  CHANGE this PATH TO YOUR CURRENT WORKING DIRECTORY ($ pwd)
```

You'll need to remove the # sign so this line will execute
You'll need to add your `$pwd` to this line as you did in the interactive session.

To do this, use fugu, cyberduck, or pico...

```
pico deseq2_demo_EON_150725.R
```

* scroll down with `CTRL+V` or `down keys`
* Exit with `CTRL+X`
* SAVE changes. `Y` then `RETURN`



## Getting R and Rstudio to work for you.

You can download R locally:

[R for MACS](https://cran.r-project.org/bin/macosx/)

[R for windows](https://cran.r-project.org/bin/windows/)


You can also load Rstudio locally. 

[https://www.rstudio.com/](https://www.rstudio.com/)

&nbsp;

&nbsp;
____






