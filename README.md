# BIOL358: Microbiology, Fall 2019
## Introduction to Microbiome data lab
### [View as webpage](https://rrshieldscutler.github.io/biol358-DryLab/)
### October 3, 2019
---

## Getting started
Today we will be exploring next-generation sequencing data from the human microbiome. The tools we are using will probably be unfamiliar. We will be using the computer's `terminal` or command-line interface, which many of you have never (or rarely) done. Using the command line is just another skill, and through practice and experience, you can learn to use this tool just as you have learned to use others in the past, like a micropipet. And, just like learning to pipet, this skill opens up a whole world of experiments, research, and understanding about how we ask questions about biology in today's research. For example, almost all of the big data servers that are necessary for "big data" work are *only* accessible through a command line!


You should keep some running notes in a notebook or Google/Word Doc, in particular quick answers and notes about the questions posed in this lab exercise. This will be helpful material as we explore the microbiome topic in the course, and as a reference and study guide. There are a series of questions to complete for this lab, which should be included in your lab notebook (therefore, not "due" or graded until the end of the semester as part of the lab notebook grade).


We will only touch the surface of command line work today, because that's not the main objective of the lab. But you can practice any of these things on your own computers, and if this isn't new to you, please help your neighbors who may be learning this for the first time.


To begin, open the Application called `terminal`. It will launch a command-line window, which will be our virtual "laboratory" for the day.

When you are working on the command line, you are always working "inside" a folder (called "directories" by most computer scientists, and here going forward) on the hard drive. By default, you start out in your "Home" user's directory. Open a new Finder window, and navigate to your Home folder. Note what folders/files are in there. Now, go to the terminal and type the following command, then press Return:
```sh
ls -l
```
When typing commands on the terminal, if you make a mistake it will usually cause an error; but, *there is no undo on the command line*, so you also want to be careful when typing. That said, I want you to type the commands, not copy and paste, because you will learn better by doing.


In the above example, the *command* was `ls` which tells the computer to list the contents of your current location. We also added a *flag* or *option*, `-l`, which modifies or provides additional instruction to the command. Note that flags are separated from the command by a *single* space (first hint why spaces in file names and folders can cause problems on the command line!). In this case, the flag `-l` tells the list command to output the "long format" with additional details about the contents. Try running it without the flag.
```sh
ls
```

Nice. Sometimes the words argument, flag, and option are used interchangably.
> To access the "user manual" for any built-in command, type `man [command]` e.g. `man ls`.

Most commands act on a directory or file. The directory/file can be specified by including it after the argument, again separated by a single space:
```sh
ls -l Desktop
```
This only works if Desktop is located in our current directory. Let's move into another directory so we can start to get to work. The command to move directories is `cd`, or "change directory" (this is analogous to double clicking on a folder in the finder window).


One other trick you can learn here is called **tab completion**. In the following command, type `cd Docu` and then press tab; see what happens.
```sh
cd Doc[press tab]
# Should become...
cd Documents
```

Neat, huh?!

Quick housekeeping task. Run the following command to keep your configuration from trying to break itself (software issue):
```sh
conda config --set auto_update_conda false
```

---
### Download the raw data
Download the data we'll be playing with today from Google Drive [here](https://drive.google.com/open?id=13qLNc7PXX3GNeHS6sUVjyt-RAjezXUDw)<sup>1</sup>. Use the Finder to move this data directory (called "data_final") into the Documents directory on the computer. (There are ways to do all of this on the command line, but those lessons are for another time...)

---
#### Raw sequencing data: the FASTQ
Next generation DNA sequencing produces files in a common format called FASTQ. It is similar to the FASTA files we looked at last week, but each sequence consumes 4 total lines, not 2. But, like FASTA files they are just plain text files so we can read them easily on the command line or with TextEdit. To peek at the first 10 lines of a text file, use the `head` command (to look at the _head_ of the document... there is also `tail` and I bet you can guess what that does...).
```sh
# Note that the file follows the command after one space
head data_final/SRR040913.fastq
```
The first line is the identifying information. The second line is the raw sequence, often called a "read". The third line is a spacer that precedes the fourth line. What is the fourth line of a FASTQ file from next-gen sequencing? Hint: use the internet.


*What you are viewing on your screen is the human microbiome. This is the microscope slide equivalent for computational microbiologists. Everything that you read about the microbiome, all the research, news stories, medical implications, and the explosion in our understanding of microbial diversity in and on our bodies (and other environments)........ all starts with a few hundred gigabytes of these four lines, produced by an expensive instrument in a windowless room* (ok, I'm sure some have windows).


Ok, back to the data. So, if you've figured out what the fourth line indicates, and knowing that this is totally raw data, can you predict what the first thing we have to do is?... 

---
### Downloading the software
For the first step, we'll need to download an open-source bioinformatics "tool" (aka software). Almost all of the software used in academic research for microbiome work is open-source. What does this mean, beyond that it costs nothing to use? Hint: there are advantages and disadvantages.


There are many ways to complete this task, but the software we'll be using is called **SHI7** and is pronounced "shi-ZEN", another open-source tool. It is hosted on the community site GitHub: <https://github.com/knights-lab/shi7>. Download it [here](https://drive.google.com/file/d/11in6Mr3ddBPcjmuoEAZnh-M2jqfm4mRd/view?usp=sharing). Right click (control-click), and choose Download to put it in your Downloads folder, where it should automatically extract the .zip file.


To make it something the computer can run, enter these commands in order:
```
echo "PATH=~/Downloads/shi7_0.9.9_mac_release:$PATH" >> ~/.bash_profile
source ~/.bash_profile
```

Now we need to install QIIME, the final software we will use. More on it later, but we will install it now:
```sh
conda create -n qiime1 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda
# Say "y" when it prompts you.
```
To activate the QIIME environment on the computer, enter the following command: `conda activate qiime1`. You should now have a little `(qiime1)` indicator at the left side of your active terminal line. 


If you didn't find it before, that last line of the FASTQ is a code, similar to the ones [described here](https://www.drive5.com/usearch/manual/quality_score.html). How do you think we will use this information in the next step?

> Diving deeper: What are the differences between 454, Illumina, and PacBio?

You got it! Like a chef preparing some homegrown organic produce, you sometimes just have to cut off the bruises and soggy bits to get the best product. This is the purpose of SHI7. For most open-source packages, you can get an explanation of the various commands and options by using the "help" argument, almost universally coded as `-h`.

```sh
shi7.py -h
```
Let's unpack that. Like many bioinformatics tools, the software is written in the programming language Python (which you might remember if you've taken the intro CS course here). Also helpful to note how directories are structured on the command line. The forward slash `/` separates directories (aka Folders) on the command line. For example: 
```sh
# Desktop/My_Homework_Folder/Microbiology_folder/This_Class_Rocks.docx
```

---
### Run that SHI7
As you can see, there are a lot of ways to customize this software. Let's start by running with the default settings (chosen to work for _most_ people's cases). We just need to tell the command where the raw data is with the `-i` argument (for "input") and where to put the results with `-o` (for "output"). We also need to add `-SE` because this data happens a type called _single end_ (hence, SE).
```sh
shi7.py -i data_final -o shi7_result_default -SE
# You can give the output any name, and it will make a directory with that name for the results
```
> Diving deeper: What are "paired end" DNA reads, vs "single end" DNA reads? What is a potential advantage of paired reads?

Take a look at the resulting output file. Remember the `head` command? Use it to peek at the output file, which should be located at this file location: `shi7_result_default/combined_seqs.fna`. What does this look like? What format is this? What's different about this and the FASTQ?


Next, sometimes it's useful to know how many reads (aka DNA sequences--in this case, individual 16S sequences) are in the output file. The simplest way to do this is to count the number of lines in the output file -- then divide by _what number_? (Defined by the structure of the FASTA file). The command `wc` is "word count", and we then tell it to count 'lines' using the `-l` flag:

```sh
wc -l shi7_result_default/combined_seqs.fna
```

So, how many reads (aka sequences) are in your file? Every read/sequence could be a different bacteria in theory, so you see why our approach from last week is no longer feasible for these studies! And this is just a subset of a larger study...


Great work! You have just run quality control on next generation sequencing data from the human microbiome.

---
### Assigning names to those sequences
We now find ourselves at a similar point as we were at the beginning of last week's lab. We have DNA sequences (16S rDNA again), and we want to know _who_ is there. But now we have some problems: shorter sequences, and an unknown number of species per sample (whereas last week we had lab colony-purified isolates!). What do we need? You guessed it. More open-source bioinformatics software. Again, there are many many options, and there are even many approaches and theory for completing this step. We're going to use one called **NINJA-OPS**, partly because it has a cool name, but also because it can actually run on this iMac or your laptop. It doesn't do as much as some of the other software tools, but most of those only run on big computing servers.


Download the software using [this link](https://drive.google.com/drive/folders/1KmiitLbbWkRMGRRPKfogacOHhFLYmgaA?usp=sharing). NINJA-OPS comes with a database built from a large collection of 16S rRNA genes called [GreenGenes](http://greengenes.secondgenome.com/), which is one of the more commonly used databases for 16S microbiome studies. In short, the software uses some advanced algorithms, matrices, and efficient programming to match your sequences to their best match in the database. More accurately, it assigns each sequence to an OTU. [Check out the wikipedia article on OTUs](https://en.wikipedia.org/wiki/Operational_taxonomic_unit) for a decent introduction. So, at the end of this process you will have a table with "counts" or "hits" for each OTU in the database within each sample in our dataset (there are 30 separate microbiome samples in our dataset).

Again, use these commands to make it something the computer can recognize:
```sh
echo "PATH=~/Downloads/NINJA-OPS:$PATH" >> ~/.bash_profile
source ~/.bash_profile
```


NINJA-OPS is a self-referencing acronym that stands for "Ninja Is Not Just Another OTU-picking Solution". Let's see what options NINJA-OPS has. This is another program that's written (mostly) in Python:
```sh
bin/ninja.py -h
```
To run NINJA-OPS, you have to tell it where to find your sequences (`-i`), where to put the results (`-o`), and then any other modifications to the defaults that we need. Here, we are just going to use the flag `-z` to tell it to search against both strands of the DNA in the database, and `-d 2` to tell it to ignore any sequences that just occur once, since that's probably just noise or background error in the sequences (a species that only shows up once in the entire dataset won't have enough statistics to mean anything, regardless).
```sh
python ~/Downloads/NINJA-OPS/bin/ninja.py -i shi7_result_default/combined_seqs.fna -o ninja_results -z -d 2
```
When it's done, you can also see how much more efficient the computation is when you eliminate those super-rare sequences with `-d 2`. Change the name of the results output (like `ninja_results_2`) and run the command again without the `-d 2` on the end, and see how much longer it takes. Change the name of the results though, or you will overwrite your original data (if you forget, you can just run the first command over again).

> Diving deeper: What is parallel computing? Can your iMac do this? How many "threads" does this Mac have?

The output data is in a format called BIOM, for "biological observation	matrix". This is the format that we need to use our next tool.

---
### Behold, QIIME
Remember the final question last week? QIIME is one of the most used bioinformatics tools for studying microbiomes. [The paper introducing QIIME](http://www.nature.com/nmeth/journal/vaop/ncurrent/full/nmeth.f.303.html) has been cited 16,655 times as of this writing (that's a lot). They recently released version 2 (QIIME2), but it is a much steeper learning curve, and the core functions are identical to those in QIIME1. Therefore, we are using QIIME1 today. If you start doing this research in your future though, it is better to learn QIIME2 going forward (learning QIIME1 will still help ease that learning process). QIIME is a _much_ larger piece of software than SHI7 or NINJA. We installed it earlier and it's still "active" in our terminal.

> Diving deeper: Qiime has been installed in a Python virtual environment. What's that? What is "Anaconda"?

QIIME contains many commands. Some will run other programs on your data (this is called a "wrapper", because QIIME is wrapping up the other program with itself). Some are designed to run ecological algorithms about diversity. Some are designed to help visualize and make sense of these large datasets. You can type Qiime commands and use tab completion on Qiime commands.


Ok! Are you ready to visualize your data?


First, let's see what NINJA did with our FASTA sequences. This command will convert that BIOM format into a table that you can open with Excel. Make sure you still have the `(qiime1)` text at the beginning of your command line, and that you are still located in the "Documents" directory.
```sh
biom convert -i ninja_results/ninja_otutable.biom -o ninja_otu_table.txt --to-tsv
```


Go to the Finder window, and you should see this new file, "ninja_otu_table.txt". Control click, go to "Open With" and choose Microsoft Excel. NINJA took all those sequences and found the entry in the database that best match the sequence (there's some deeper algorithm stuff going on, but that's the essence). Now you have a table with the microbiome samples as columns, and each row is an OTU (numeric code, no names yet). The numbers in the table are the _counts_ or _hits_ to that OTU in each sample. What do you notice about the table? Is it totally full of big numbers? What does it mean about the communities in these people's samples?


Ok, time for pictures. We can use Qiime to make stacked bar plots that show the relative abundance of all the organisms we could detect in these microbiome communities. The following command will take our output file from NINJA and generate several data outputs:
```sh
summarize_taxa_through_plots.py -i ninja_results/ninja_otutable.biom -o taxa_summary
```
This may run for a minute or so. When it's done, go to the Finder and look at the files generated. What do each of the tables represent (labeled L2, L3, L4, etc)? Open the taxa_summary_plots directory and open the file "bar_charts.html" using a web browser. What do these represent? Hover over the bars with your mouse. What distinguishes the different graphs as you scroll down?


Do you notice that there seem to be some patterns, that some samples seem more alike than others? Why do you think that might be?


What if I told you that these were all human microbiomes, but they weren't all from the same place. Remember, you can use the `-h` flag with the command to learn more about the options (`summarize_taxa_through_plots.py -h`). Add a few arguments as shown below to group the samples by a common variable.
```sh
summarize_taxa_through_plots.py -i ninja_results/ninja_otutable.biom -o taxa_summary_site -m data_final/hmp_metadata_biol358.txt -c BodySite
```
Find the new results folder in the Finder and open the bar charts HTML file for this result. What do you see?


### Measuring and plotting microbial ecological diversity
Remember that the "E" in QIIME stands for Ecology. We are looking at complex populations of microorganisms that are surprisingly variable from person to person and can change within a person from day to day depending on countless factors. We want a way to compare communities based on their ecological diversity. With this we want to ask two major questions:
1. How diverse is a given sample?
2. How similar are two samples to one another, based on their microbiome profiles?


To answer #1, we will measure the _alpha diversity_ of a given sample. What is alpha diversity? Why is this potentially valuable information? What would typical ecological theory suggest about a healthy ecosystem? The alpha diversity command in Qiime contains a few arguments to make it run appropriately. If you run the help command, you can learn more about what each one denotes:
```sh
alpha_rarefaction.py -i ninja_results/ninja_otutable.biom -o alpha_rare -t data_final/gg97.tre -m data_final/hmp_metadata_biol358.txt -e 570 -n 5 -a -O 4 --min_rare_depth 50
```
This will take a little while to run. In the meantime, generate some hypotheses about the relative diversity of the three body sites here: stool, skin, and mouth. Which do you think is most diverse? Which sites are the most similar? Which is the most consistent across individuals? What about differences between males and females (each site is a mixture of males and females).


Once the alpha rarefaction is done, look at the results in the Finder. Open some of the plots. On the rarefaction plots, the x-axis is the number of randomly chosen sequences. What naturally happens to the diversity as you include more sequences (i.e. include more data points)? Explain this to your partner. Now, why is it important to sub-sample reads from samples with many more sequences to begin with?


Alpha diversity analyses yield a single value for each sample. Therefore, we can compare them with typical statistical tests and ask whether one group is significantly more diverse, by a given metric of diversity. Qiime enables this with the command `compare_alpha_diversity.py`. It's a little finicky in what files it takes, so here is an example that would compare body site Phylogenetic diversity (PD_whole_tree):
```sh
compare_alpha_diversity.py -i alpha_rare/alpha_div_collated/PD_whole_tree.txt -m data_final/hmp_metadata_biol358.txt -o alphatest_PD -c BodySite -p fdr
```

Look at the plots and the stats results. Are there any significant differences? Repeat this for the other two alpha diversity metrics saved in the alpha_div_collated directory. What is the difference between these three?


To answer #2 above, and any of your hypotheses surrounding the similarity between sites, we will measure the beta diversity of these samples. What is beta diversity? How does it compare to alpha diversity?


The command to run beta diversity analyses on our data looks similar to the alpha diversity command, but takes much less time.
```sh
beta_diversity_through_plots.py -i ninja_results/ninja_otutable.biom -t data_final/gg97.tre -m data_final/hmp_metadata_biol358.txt -o beta_diversity_result -e 570
```
Open the results directory in the Finder window. You should see two types of measures: weighted unifrac and unweighted unifrac. What is the difference between these? What do they measure?


Open the folders ending in "emperor_pcoa_plot" and open the `index.html` files in a web browser. What you are viewing now is a Principal Components Analysis (PCoA) plot of the beta diversity. You should read into what a PCoA is showing, but in essence: in 3D space, samples closer to one another are more similar by microbiome composition that those further apart. The algorithms determine the ecological distance from each sample to every other sample, which results in a big matrix of distances. The PCoA is a convenient way to visualize this dense information. Play around with the interactive viewer to help get the hang of this data presentation and to test your hypotheses. The "colors" tab will allow you to assign colors to the variables such as body site and individuals' sex. Write down your observations, especially with regard to your hypotheses. Beta diversity measures are a little harder to compare statistically, but I am happy to talk over some of these approaches with groups if you are curious.


Save or take screen shots of the figures that help you test your hypotheses, or anything else you found interesting. Use these to answer the questions in the report document (Question 4). Include this in your lab notebook entry for today.


### Stepping back
The analyses you've just performed make up some of the core techniques of any microbiome study. There are many directions from here, and variations on the diversity tests and/or ways of comparing or visualizing the results. But taxa summary plots and alpha and beta diversity comparisons are integral to the analysis in nearly every microbiome study. I hope also that by going through these steps you also appreciate the many stages where variability and the preferences of the researcher can affect the results. Quality filtering, choice of the database and OTU tool (GreenGenes and NINJA here, but there are many options), thresholds for including data in the diversity analyses, etc. The field is young and there are few "standard operating protocols" beyond what a particular lab does. Programs like Qiime try to make some elements standardized, but it has its limitations too.


## Clean up
Because you are logged in with your username, you can leave all the data and installed files where they are. To exit the Qiime environment, you can type `conda deactivate`. Or, you can just close the terminal. No harm done as long as it is not currently running a command (even then, it'll just stop the process, not kill the computer). Log out and lower the computer back into the desk. Thank you!

-----
<sup>1</sup> HMP Consortium (2012) Structure, function and diversity of the healthy human microbiome. _Nature_ doi: 10.1038/nature11234
