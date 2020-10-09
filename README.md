# Computational Neuroscience Final Project -  Predicting Antibody Origin Species Using Only Amino Acid Sequence of the Variable Regions:
For my Computational Neuroscience final project, I decided to build a TensorFlow application. This project builds off of my former work in bioinformatics at Cell Signaling Technologies. Using only protein-sequence information as input, I designed a Convolutional Neural Net (CNN) to correctly predict antibody origin species.

## Project Background:

I spent my past summer working in the bioinformatics group of a small biotech company, Cell Signaling Technology. Our company focuses on the development of research antibodies, and much of the software I used involved structural modelling, binding-epitope prediction, binding-affinity prediction, etc. Many of these applications were built by implementing by pre-trained neural nets that could "map" antibody residue sequence to binding site, structural features, etc.

Despite making good use of these machine-learning-based programs & packages, I'd never gotten the chance to work with ML myself. As a first step into ML, I decided to leverage the background I'd gained through my experience at Cell Signaling, and choose antibody sequence data as the object of my first project in this realm of computer science. Using databases of antibody amino-acid sequence data, the goal of this project was to **build a model that could identify the orgin species that generated a given antibody, using only a small subset of amino-acid sequences derived from that specimen.** I'll go into more detail on exactly what I mean by that as we go along.


### Practical Objectives:

- Prior to completing this project, I had never used many of the python libraries you see me using below. Thus, one of the most important objectives here was to begin introducing myself to ML through code, and to become familiar with some of the commonly-used libraries used by ML software engineers. Although I was not able to apply everything I learned to my code, I spent about 20-30 hours consuming an Udemy course, "Complete Guide to TensorFlow for Deep Learning with Python." Through this course, I learned from scratch or improved my skills in each of the following libraries:
	- tensorflow
	- pandas
	- numpy
	- scikitlearn
	- matplotlib
    - matplotlib

- A second objective of this work was to become familiar with how exactly to apply a knowledge of machine learning to code itself- to translate a conceptual understanding into a technical skill.

- Thirdly, I hoped this project would give me a solid understanding of how I might apply machine learning to real world data. In this case, I attempted to conjoin a domain-specific knowledge of antibody sequences and structure with a more general understanding of machine learning. Performing this exercise of applying ML to a unique problem was a useful excercise for me to gain a more complete understanding of the obstancles and advantages of using machine learning, outside of the scripted classroom experience I gained through Udemy.

## Background

Although additional information about each of the above sub-projects will be provided further down in the code, in order to understand the intent behind the code, a little background on antibodies in general and their place in this project specifically is neccessary. To cover all my ground, I'm going to assume zero prior knowledge on the subject for anyone reviewing this ipython notebook.

#### Antibody Function:

Antibodies(Ab), or immunoglobulins (Ig) are immune proteins, generated by nearly countless forms of species, ranging from mammals all the way back to cartilagenous fishes [Schluter et. al, 1977](https://www.ncbi.nlm.nih.gov/pubmed/9386351). In all cases, the primary function of an antibody is to attempt to bind to and neutralize pathogens. When an antibody binds to its target, it can perform one of two things:
- Tag the protein, thus signaling to the rest of the immune system of a potential intruder
- Directly neutralize the target (in the case that the antibody has bound to an area on the pathogen that is biologically relevant and/or necessary for the pathogen's survival)

Antibodies form a critical piece of the body's immune system, and the body develops antibodies in mass quantities to target a wide variety of pathogenic or potentially pathogenic proteins and ligands. The cells responsible for this production continualy vary the structure of the antibodies they produce, altering the arangement of amino acids to obtain unique protein conformations. By generating such a wide variety of antibody variants, the body bolsters its odds of generating enough protein conformations to successfully bind any pathogen that enters the body. In this way, the body creates a kind of surveilance system to tag and identify all pathogenic protein types and alert the immune system.

Once researchers realized the power of antibodies in the human body, they began to utilize antibodies for new tasks. Today, antibodies produced under the supervision of researchers from pharma, biotech, and academic research alike are used for a variety of tasks. Commercially produced antibodies now find therapeutic uses, and are especially useful for patients whose immune systems are not well-equipped to fight an ailment. Keytruda and Optivo, for example, use antibodies to fight a range of advanced cancers. In the case of cancer, the body's immune system does not often recognize cancer cells, causing the disease to spread. Antibody drugs can help to counteract this issue by triggering an immune response. Further, antibodies can be used in therapeutics to directly attack cancers or other pathogens by binding to a functionally critical location and performing neutralization directly. In addition to all of these therapeutics applications, antibodies can be used for research... for example, by enabling researchers to tag proteins they are studying in a tissue specimen with a florescent tag or a radioactive tag. These tags enable researchers to visualize and collect data on proteins they are studying.

##### Structure

To understand how exactly how the antibody has seen such enormous success, however, one has to take a look at its structure, and examine how the body introduces genetic variance. Antibodies express a unique Y-shape, as follows.

![Antibody Shape](https://www.immunology.org/sites/default/files/Generation-of-B-cell-antibody-diversity-Figure-1.png)

Taking a look at this Y shape, one can identify the various regions of an antibody. First of all, the antibody has two different "chains" (of ammino acids) - the light chain and the heavy chain. Each of these chains can be broken down into two parts - the constant region, and the variable region. For a given antibody type, the constant region remains exactly the same. Two antibodies targeting entirely different pathogens will express the same constant regions nonetheless. This region is responsible for maintaining the structure of the antibody. The true power of the antibody, however, rests in the variable region- this is the region that actually binds to a target pathogen. 

To build the variable region, the body's B cells randomly pull from a gene pool in order to introduce genetic variance. The amino acid sequence of the variable region, therefore, is unique to each specific antibody. The body can generate virtually endless amino acid sequences in the varible region of the antibodies it produces; therefore, when these B cells are attempting to generate an antibody that can successfully bind a pathogen, they can merely mass-produce antibodies, introducing genetic variance each time, until a successfully binding antibody emerges. Taking this approach results in an extremely high probability that the body can create an antibody to bind a given pathenogen.

#### Application to Project

Because of the genetic variance of these variable regions, there are no easy ways for us to determine their species of origin without data-driven techniques. In the case of this project (or, the second half of the project, to be more specific), we will be feeding an algorithm only the amino acid sequence of the variable region of the antibody light chain. In addition, to provide a little extra data, we will be providing the algorithm with data about the substructure of this light chain. All variable regions can be broken down into a couple different pieces, namely the complimentary-determining (CDR) regions, and the framework regions. The framework regions are more genetically stable than the CDR regions, so the CDR regions are really the  piece of the antibody that is primarily responsible for binding specificity. 

CDRs and framworks, in any given antibody, are always found in the antibody sequence in the following order:

- Framework 1, CDR1, Framework 2, CDR2, Framework 3, CDR3, Framework 4

Therefore, to provide the algorithm with additional data, we will be providing it the sequences of the CDR1, CDR2, and CDR3 that form a piece of its variable light chain.

In this project, we will be feeding the algorithm mouse and human antibody sequences only, and will attempt to enable the algorithm to identity the species of origin, using just these data inputs.

Although this may seem like an impossible task, given the genetic variability/ randomness of the variable region, keep in mind that each species is pulling from a different (species-specific) gene pool to create this variabilty. Therefore, across species, similar sequence motifs may be expressed. Having this knowledge, we will attempt to see if our algorithm can identify some of these motifs, and categorize antibodies by origin species.

## Project Objectives
I broke the project down into several steps. The first half of the project will forego the usage of antibody sequence data, and will instead use continuous data points that describe the sequences (i.e. length of CDR1 region, length of Framework 2, % similarity to human antibodies, etc.) in order to make build a simple linear classifier and Deep Neural Network (DNN). The second half the project will feed the variable region amino-acid sequences of our mouse and human antibodies to a convolutional neural network, and we will use this network to attempt to classify the antibodies. For the final leg of the project, we will remove the framework regions from the variable regions, leaving only the CDR regions, the least conserved region of the antibody. We will feed these CDR regions only to our CNN algorithm, attempting to discern whether or these regions contain species-specific amino-acid motifs (arrangements of amino acids that are unique to the species). In other words, we would like to see whether or not the mouse's version of "randomness" is different than the human's.

In summary:

1. Identity the species of origin from which an antibody originated using a simple linear classifier & a densely connected neural net, with continuous data points indicating various features about the antibody.

2. Attempt to classify antibody origin species using only amino acid sequence of the entire variable region of the antibody light chains in our database

3. Attempt to clasify antibody origin species using only amino acid sequence of the CDR regions of the antibody light chains.


## Downfalls:

1. The dataset I used, the Kabat antibody dataset, is from the late 1990s, and is formatted very poorly for usage in machine learning. In addition, there were numerous empty datafields, forcing me to cut my dataset to hardly over 1000 sequences

2. As a result of this, an unbelievable ammount of my code is pure data preparation. (Actually much more than even appears to the eye here, I've abstracted most of it away).


## Successes:

1. I built up my skills in data processing and ML significantly through this project.

1. I was able to build successful models for all three project objectives.

2. Each of these models was hugely successful at performing its job (98% accuracy or so, with strong precision and recall measures as well.

3. I was able to generate a successful convolutional neural net that could be fed only CDR Loop regions of an antibody, all of which are completely genetically variable and unique for each antibody seqeunce. This model, to my amazement, actually performed at about 98% accuracy. It had clearly identified robust, species-specific motifs in the CDRs.
