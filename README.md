# BIOL358: Microbiology, Fall 2018
### Introduction to Microbiome data lab
### October 2, 2018


## Getting started
Today we will be exploring next-generation sequencing data from the human microbiome. The tools we are using will probably be unfamiliar. We will be using the computer's `terminal` or command-line interface, which many of you have probably never (or rarely) done. It is not as scary as it seems. At some point, you had never heard of, seen, or touched a pipet. Or a microscope. Maybe you have started to learn a second (or third) language. Using the command line is just another skill, and through practice and experience, you can learn to use this tool just as you have learned to use others in the past. And, just like learning to pipet, this skill opens up a whole world of experiments, research, and understanding about how we ask questions about biology in today's research. For example, almost all big computing servers that are necessary for "big data" work are *only* accessible through a command line!


We will only touch the surface of command line work today, because that's not the main objective of the lab. But you can practice any of these things on your own computers, and if this isn't new to you, please help your neighbors who may be learning this for the first time.


To begin, open the Application called `terminal`. It will launch a command-line window, which will be our virtual "laboratory" for the day.

When you are working on the command line, you are always working "inside" a folder (called "directories" by most computer scientists) or location on the hard drive. By default, you start out in your "Home" directory. Open a new Finder window, and navigate to your Home folder. Note what folders/files are in there. Now, go to the terminal and type the following command, then press Return:
```
ls -l
```
When typing commands on the terminal, if you make a mistake it will usually cause an error; but, *there is no undo on the command line*, so you also want to be careful when typing. That said, I want you to type the commands, not copy and paste, because you will learn better by doing.


In the above example, the *command* was `ls` which tells the computer to list the contents of your current location. We also added an *argument*, `-l`, which modifies or provides additional instruction to the command. Note that arguments are separated from the command by a *single* space (first hint why spaces in file names and folders can cause problems on the command line!). In this case, the argument `-l` tells the list command to output the "long format" with additional details about the contents. Try running it without the argument.
```
ls
```

Nice. Sometimes the bit with the hyphen and letter, e.g. `-l`, is called a "flag".
> Diving Deeper: To access the "user manual" for any built-in command, type `man [command]` e.g. `man ls`.
Most commands act on a directory or file. The directory/file can be specified by including it after the argument, again separated by a single space:
```
ls -l Desktop
```
This only works if Desktop is located in our current directory. Let's move into another directory so we can start to get to work. The command to move directories is `cd`, or "change directory" (this is analogous to double clicking on a folder in the finder window).


One other trick you can learn here is called **tab completion**. In the following command, type `cd Docu` and then press tab; see what happens.
```
cd Doc[press tab]
# Should become...
cd Documents
```

Neat, huh?! I also used something just now called a *comment* character. The pound sign `#` tells the computer to _ignore anything that comes after it on that line_ while processing any commands. People use it to add explanations or notes to code, like I just did, or to temporarily "blind" the computer to a command or series of commands. Try the following commands:
```
echo "Hello, friends!"
# the echo command makes the computer print out whatever you feed it within quotes, as above
# now try the same echo command after a comment:
# echo "Hello, friends!"
```


### Download the raw data
Download the data we'll be playing with today from Google Drive here. Use the finder to move the data directory into the Documents directory on the computer. (There are ways to do all of this on the command line, but those lessons are for another time...)

#### Raw sequencing data: the FASTQ
Next generation DNA sequencing produces files in a common format called FASTQ. It is similar to the FASTA files we looked at last week, but each sequence consumes 4 total lines, not 2. But, like FASTA files they are just plain text files so we can read them easily on the command line or with TextEdit. To peek at the first 10 lines of a text file, use the `head` command (to look at the _head_ of the document... there is also `tail` and I bet you can guess what that does...).
```
# Note that the file follows the command after one space
head data/***.fastq
```
The first line is the identifying information. The second line is the raw sequence, often called a "read". The third line is a spacer that precedes the fourth line. What is the fourth line of a FASTQ file generated by Illumina sequencing? Hint: use the internet.


*Let's also just take a second here. What you are viewing on your screen is the human microbiome. This is the microscope slide of computational microbiologists. Everything that you read about the microbiome, all the research, news stories, medical implications, and the explosion in our understanding of microbial diversity in and on our bodies (and other environments)........ all starts with a few hundred gigabytes of these four lines, produced by an expensive instrument in a windowless room* (ok, I'm sure some have windows).


Ok, back to the data. So, if you've figured out what the fourth line indicates, and knowing that this is totally raw data, can you predict what the first thing we have to do is?... 


### Downloading the software
For the first step, we'll need to download an open-source bioinformatics "tool" (aka software). Almost all of the software used in academic research for microbiome work is open-source. What does this mean, beyond that it costs nothing to use? Hint: there are advantages and disadvantages.


There are many ways to complete this task, but the software we'll be using is called **SHI7** and is pronounced "shi-ZEN". Like many open-source tools, it is hosted on the community site GitHub: <https://github.com/knights-lab/shi7>. Download the most recent MacOSX version by clicking on the "releases" link near the top of the page. Hold *control* and click on the shi7_0.9.9_mac_release.zip link; rename it to "shi7.zip" and save it in "Documents > biol358_F18", where we are working. Using the finder, double-click to unzip, then delete the zip file. You should now have a directory called "shi7", and inside of it a number of files and directories.


If you didn't find it before, that last line of the FASTQ is a code, similar to the ones [described here](https://www.drive5.com/usearch/manual/quality_score.html). How do you think we will use this information in the next step?

> Diving deeper: What are the differences between 454, Illumina, and PacBio?

You got it! Like a chef preparing some homegrown organic produce, you sometimes just have to cut off the bruises and soggy bits to get the best product. This is the purpose of SHI7. For most open-source packages, you can get an explanation of the various commands and options by using the "help" argument, almost universally coded as `-h`.

```
python shi7/shi7.py -h
```
Let's unpack that. Like many bioinformatics tools, the software is written in the programming language Python (which you might remember if you've taken the intro CS course here). To tell the computer to interpret the program using Python, we start the command with the name `python` then a space, then the name of the program `shi7.py`, which is located within the directory `shi7` (the forward slash `/` separates directories on the command line. For example: `Desktop/My_Homework_Folder/Microbiology_folder/This_Class_Rocks.docx`).


### Run that SHI7
As you can see, there are a lot of ways to customize this software. Let's start by running with the default settings (chosen to work for _most_ people's cases). We just need to tell the command where the raw data is with the `-i` argument (for "input") and where to put the results with `-o` (for "output"). We also need to add `-SE` because this data happens a type called _single end_ (hence, SE).
```
python shi7/shi7.py -i data_final -o shi7_result_default -SE
# You can give the output any name, and it will make a directory with that name for the results
```
> Diving deeper: What are "paired end" DNA reads, vs "single end" DNA reads? What is a potential advantage of paired reads?
Take a look at the resulting output file. Remember the `head` command? Use it to peek at the output file, which should be located at this file location: `shi7_result_default/combined_seqs.fna`. What does this look like? What format is this? What's different about this and the FASTQ?


Next, sometimes it's useful to know how many reads (aka DNA sequences--in this case, individual 16S sequences) are in the output file. The simplest way to do this is to count the number of lines in the output file -- then divide by _what number_? (Defined by the structure of the FASTA file). Google how to count the number of lines in a text file. When you have a command, check with me.


So, how many reads are in your file? Every read/sequence could be a different bacteria in theory, so you see why our approach from last week is no longer feasible for these studies! And this is just a subset of a larger study...


Take another look at the shi7 help screen and see if you can determine what argument you could add to the shi7.py command above to change the quality threshold while trimming back the ends of the reads. The scale is 0-42 (low to high quality). What's the default? Remember to add a single space around the argument "flag" (the part starting with a hyphen) to separate it from the previous arguments and the number that you choose for a new quality threshold. Try a range higher and lower than the default, then count the number of sequences in the resulting file. 
>NOTE: you also need to change the output directory name from `shi7_result_default` to something else, perhaps `shi7_result_X` where `X` is the new score you've picked. That way you can compare results; otherwise it will overwrite your old results.


What happens to the number of sequences when you change the quality threshold? What effect do you think this has on the downstream analysis?


Great work! You have just run quality control on next generation sequencing data from the human microbiome.


### Assigning names to those sequences
We now find ourselves at a similar point as we were at the beginning of last week's lab. We have DNA sequences (16S rDNA again), and we want to know _who_ is there. But now we have some problems: shorter sequences, and an unknown number of species per sample (whereas last week we had lab colony-purified isolates!). What do we need? You guessed. More open-source bioinformatics software. Again, there are many many options, and there are even many approaches and theory for completing this step. We're going to use one called **NINJA-OPS**, partly because it has a cool name, but also because it can actually run on this iMac or your laptop. It doesn't do as much as some of the other software tools, but most of those only run on big computing servers.


Download the software using this link. NINJA-OPS comes with a database built from a large collection of 16S rRNA genes called [GreenGenes](http://greengenes.secondgenome.com/), which is one of the more commonly used databases for 16S microbiome studies. In short, the software uses some advanced algorithms, matrices, and efficient programming to match your sequences to their best match in the database. More accurately, it assigns each sequence to an OTU. [Read the wikipedia article on OTUs](https://en.wikipedia.org/wiki/Operational_taxonomic_unit) for a decent introduction. So, at the end of this process you will have a table with "counts" or "hits" for each OTU in the database within each sample in our dataset (there are 30 separate microbiome samples in our dataset).
> NINJA-OPS actually stands for "Ninja Is Not Just Another OTU-picking Solution".
Let's see what options NINJA-OPS has. This is another program that's written (mostly) in Python:
```
python NINJA-OPS/bin/ninja.py -h
```
To run NINJA-OPS, you have to tell it where to find your sequences (`-i`), where to put the results (`-o`), and then any other modifications to the defaults that we need. Here, we are just going to use the flag `-z` to tell it to search against both strands of the DNA in the database, and `-d 2` to tell it to ignore any sequences that just occur once, since that's probably just noise or background error in the sequences (a species that only shows up once in the entire dataset won't have enough statistics to mean anything, regardless).
```
python NINJA-OPS/bin/ninja.py -i shi7_result_default/combined_seqs.fna -o ninja_results -z -d 2
```
When it's done, you can also see how much more efficient the computation is when you eliminate those super-rare sequences with `-d 1`. Run the command again without the `-d 2` on the end, and see how much longer it takes. Change the name of the results though, or you will overwrite your original data (if you forget, you can just run the first command over again).
> Diving deeper: What is parallel computing? Can your iMac do this? How many "threads" does this Mac have?
The output data is in a format called BIOM, for "biological observation	matrix". This is the format that we need to use our next tool.

### Behold, QIIME
Remember the final question last week? QIIME is one of the most used bioinformatics tools for studying microbiomes. [The paper introducing QIIME](http://www.nature.com/nmeth/journal/vaop/ncurrent/full/nmeth.f.303.html) has been cited 12,291 times as of this writing (that's a lot). They recently released version 2 (QIIME2), but it is a much steeper learning curve, and the core functions are identical to those in QIIME1. Therefore, we are using QIIME1 today. If you start doing this research in your future though, it is better to learn QIIME2 going forward (learning QIIME1 will still help ease that learning process). QIIME is a _much_ larger piece of software than SHI7 or NINJA, and has already been installed on each of the computers.
> Digging deeper: Qiime has been installed in a Python virtual environment. What's that? What is "Anaconda"?
QIIME contains many commands. Some will run other programs on your data (this is called a "wrapper", because QIIME is wrapping up the other program inside its command). Some are designed to run ecological algorithms about diversity. Some are designed to help visualize and make sense of these large datasets. We'll focus on the last two.


To activate the QIIME environment on the computer, enter the following command: `source activate qiime1`. You should now have a little `(qiime1)` indicator to the left of your active terminal line. You can now type Qiime commands and use tab completion on Qiime commands without typing Python first.


Ok! Are you ready to visualize your data? We can use Qiime to make stacked bar plots that show the relative abundance of all the organisms we could detect in these microbiome communities. The following command will take our output file from NINJA and generate several data outputs:
```
summarize_taxa_through_plots.py -i ninja_results/ninja_otutable.biom -o taxa_summary
```
This may run for a minute or so. When it's done, go to the Finder and look at the files generated. What do each of the tables represent (labeled L2, L3, L4, etc)? Open the taxa_summary_plots directory and open the file "bar_charts.html" using a web browser. What do these represent? Hover over the bars with your mouse. What distinguishes the different graphs as you scroll down?


Do you notice that there seem to be some patterns, that some samples seem more alike than others? Why do you think that might be?


What if I told you that these were all human microbiomes, but they weren't all from the same place. Remember, you can use the `-h` flag with the command to learn more about the options (`summarize_taxa_through_plots.py -h`). Add a few arguments as shown below to group the samples by a common variable.
```
summarize_taxa_through_plots.py -i ninja_results/ninja_otutable.biom -o taxa_summary_site -m data_final/hmp_metadata_biol358.txt -c BodySite
```
Find the new results folder in the Finder and open the bar charts HTML file for this result. What do you see?


### Measuring and plotting microbial ecological diversity
Remember that the "E" in QIIME stands for Ecology. We are looking at complex populations of microorganisms that are surprisingly variable from person to person and can change within a person from day to day depending on countless factors. We want a way to compare communities based on their ecological diversity. With this we want to ask two major questions:
1. How diverse is a given sample?
2. How similar are two samples to one another, based on their microbiome profiles?


To answer #1, we will measure the _alpha diversity_ of a given sample. What is alpha diversity? Why is this potentially valuable information? What would typical ecological theory suggest about a healthy ecosystem? The alpha diversity command in Qiime contains a few arguments to make it run appropriately. If you run the help command, you can learn more about what each one denotes:
```
alpha_rarefaction.py -i ninja_results/ninja_otutable.biom -o alpha_rare -t data_final/gg97.tre -m data_final/hmp_metadata_biol358.txt -e 600 -n 20
```
This will take a while to run. In the meantime, generate a few hypotheses about the relative diversity of the three body sites here: stool, skin, and mouth. Which do you think is most diverse? Which sites are the most similar? Which is the most consistent across individuals? What about differences between males and females (each site is a mixture of males and females)


Once the alpha rarefaction is done, look at the results in the Finder. Open some of the plots. On the rarefaction plots, the x-axis is the subsampling, or the number of randomly chosen sequences. What naturally happens to the diversity as you include more sequences? Explain this to your partner. Now, why is it important to sub-sample reads from samples with many more sequences to begin with?


To answer #2, and any of your hypotheses surrounding the similarity between sites, we will measure the beta diversity of these samples. What is beta diversity? How does it compare to alpha diversity?


The command to run beta diversity analyses on our data looks similar to the alpha diversity command, but takes much less time.
```
beta_diversity_through_plots.py -i ninja_results/ninja_otutable.biom -t data_final/gg97.tre -m data_final/hmp_metadata_biol358.txt -o beta_diversity_result -e 570
```
Open the results directory in the Finder window. You should see two types of measures: weighted unifrac and unweighted unifrac. What is the difference between these? What do they measure?


Open the folders ending in "emperor_pcoa_plot" and open the `index.html` files in a web browser. What you are viewing now is a Principal Components Analysis (PCoA) plot of the beta diversity. You should read into what a PCoA is showing, but in essence: in 3D space, samples closer to one another are more similar by microbiome composition that those further apart. Play around with the interactive viewer to help get the hang of this data presentation and to test your hypotheses. The "colors" tab will allow you to assign colors to the variables such as body site and individuals' sex. Write down your observations, especially with regard to your hypotheses.





> Diving deeper: 


> Diving deeper: What is parallel computing? Can your iMac do this? How many "threads" does this Mac have?

How much does the quality filtering affect the final tables?
Come up with a hypothesis about the relative diversity of the sites/sexes? Is it correct?
