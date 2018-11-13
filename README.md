# Computational Neuroscience Final Project
## Background:
I spent my past summer working in the bioinformatics group of a small biotech company called Cell Signaling Technology. Our company focuses on the development of research antibodies, and much of the software I used involved structural modelling, binding-epitope prediction, binding-affinity prediction, etc. Many of these applications were built by implementing by pre-trained neural nets that could "map" antibody residue sequence to binding  site, structural features, etc.

## Research Proposal:

Although I used these machine-learning-based software packages, I've never done any actual machine learning coding myself. As a first step into machine learning, I attempted to perform a *somewhat* simple task involving antibody sequence processing:

- Identify the species of an antibody, based on the nucleotide sequence of its light and heavy chains.

It should be noted that there are no existing algorithms, to my knowledge, that attempt to identify the species from which an antibody sequence originated. This is probably because human researchers can do it pretty easily... nevertheless, I think this would be an interesting exercise. As inputs, I am considering looking into the features that Clusal Omega uses to determine sequence alignment, and seeing if using some of the insights from clustal omega could help build a more efficient neural net (to my untrained eye, testing the sequence alignment of two antibodies is the easiest way to see if they're part of the same species... two of the same species will often have ~%70 identity, whereas two of different species will often land around ~30-40% identity).

## Objectives of this project

- Prior to completing this project, I had never used any of the python libraries you see me using below. Thus, one of the most important objectives here was to begin introducing myself to machine learning through code, and to become familiar with some of the state-of-the-art libraries used to do that. I spent about 20-30 hours taking an udemy course, "Complete Guide to TensorFlow for Deep Learning with Python", and learned the basics of the following libraries:
    - tensorflow
    - pandas
    - numpy
    - scikitlearn
    - matplotlib
    
- A second objective of this work was to become familiar with how exactly to apply a knowledge of machine learning to code itself- to translate a conceptual understanding into a technical skill. To some extent, I believe that I've made good progress in this area.

- Thirdly, I hoped this project would give me a good understanding of how I might apply machine learning to real world data. In this case, I attempted to conjoin a domain-specific knowledge of antibody sequences and structure with a more general understanding of machine learning. Performing this exercise of combining my knowledge on a data set with a knowledge of machine learning will help me to reiterate this process on other forms of data in the time ahead of me. 

## Project

The project itself was originally aimed at identifying the species of origin for an antibody sequence using sequencing data alone. However, because this is a fairly difficult task, and working with data such as images or sequence data requires a more technically complicated recurrent neural net, I've decided to break the project into two pieces.

1. Identity the species of origin from which an antibody originated using a simple linear classifier or densely connected neural net, and continuous data points indicating various features about the antibody.

2. Attempt to classify the antibody's species of origin using only sequencing information. Specifically, this sequence information will include the amino acid sequence of the antibody's light chain, and the sequences of the it's three complimentary determining regions.

## Background

Although additionally information about each of the above sub-projects will be provided further down in the code, in order to understand the intent behind the code, a little background on antibodies in general and their place in this project specifically is neccessary. To cover all my ground, I'm going to assume zero prior knowledge on the subject for anyone reviewing this ipython notebook.

#### Antibodies Background:

Antibodies(Ab), or immunoglobulins (Ig) are immune proteins, generated by nearly countless forms of species, ranging from mammals all the way back to cartilagenous fishes [Schluter et. al, 1977](https://www.ncbi.nlm.nih.gov/pubmed/9386351). In all cases, the primary function of an antibody is to attempt to bind to and neutralize pathogens. When an antibody binds to its target, it can perform one of two things:
- Tag the protein, thus signaling the rest of the immune system of a potential intruder
- Directly neutralize the target, in the case that the antibody has bound to an area on the pathogen that is biologically relevant and/or necessary for the pathogen's survival

Antibodies thus form a critical piece of the body's immune system. The body develops antibodies in mass quantities to target a wide variety of pathogenic or potentially pathogenic proteins and ligands. By doing so, the body is typically able to create a subset of antibodies that can successfully bind to the non-native (or potentially pathogenic) protein that it is attempting to target. 

Further, once researchers realized the power of antibodies in the human body, they began to utilize antibodies for new tasks. Today, antibodies produced under the supervision of researchers from pharma, biotech, and academic research alike are used for a variety of tasks. Commercially produced antibodies now find therapeutic uses, and are especially useful for patients whose immune systems are not well-equipped to fight an ailment. Keytruda and Optivo, for example, use antibodies to fight a range of advanced cancers. In the case of cancer, the body's immune system does not often recognize cancer cells, causing the disease to spread. Antibody drugs can help to counteract this issue by triggering an immune response. Further, antibodies can be used in therapeutics to directly attack cancers or other pathogens by binding to a functionally critical location and performing neutralization directly. Finally, in addition to all of these therapeutics, antibodies can be used for research... for example, by enabling researchers to tag proteins they are studying in a tissue specimen with a florescent tag or a radioactive tag. These tags can help researchers to visualize and collect data on proteins they are studying.

##### Structure

To understand how exactly how the antibody has seen such enormous success, however, one has to take a look at its structure, and examine how the body introduces genetic variance. Antibodies express a unique Y-shape, as follows.

![Antibody Shape](https://www.immunology.org/sites/default/files/Generation-of-B-cell-antibody-diversity-Figure-1.png)

Taking a look at this Y shape, one can identify the various regions of an antibody. First of all, the antibody has two different "chains", meaning chains of ammino acid - the light chain and the heavy chain. Further, each of these chains can be broken down into two parts - the constant region, and the variable region. For a given antibody type, the constant region remains exactly the same. Two antibodies targeting entirely different pathogens will express the same constant regions nonetheless. The true power of the antibody, however, rests in the variable region- this is the region that actually binds to a target pathogen. 

To build the variable region, the body's B cells randomly pull from a gene pool in order to introduce genetic variance. The amino acid sequence of the variable region, therefore, is unique to each specific antibody. The body can generate virtually endless amino acid sequences in the varible region of the antibodies it produces; therefore, when these B cells are attempting to generate an antibody that can successfully bind a pathogen, they can merelly mass-produce antibodies, introducing genetic variance each time, until a successfully binding antibody emerges. Taking this approach results in an extremely high probability that the body can bind a given pathenogen.

#### Application to Project

Because of the genetic variance of these variable regions, there are no easy ways to perform any kind of analysis on them without data-driven techniques. In the case of this project, we will be feeding an algorithm only the sequence of the variable region of the antibody light chain. In addition, to provide a little extra data, we will be providing the algorithm data about the substructure of this light chain. To be more specific, all variable regions can be broken down into a couple different pieces, namely the complimentary-determining (CDR) regions, and the framework regions. The framework regions are more genetically stable than the CDR regions, so the CDR regions are really the  piece of the antibody that is primarily responsible for binding specificity. CDRs and framworks, in any given antibody, are always found in the antibody sequence in the following order:

- Framework 1, CDR1, Framework 2, CDR2, Framework 3, CDR3, Framework 4

Therefore, to provide the algorithm with additional data, we will be providing it the sequences of the CDR1,2, and 3 that form a piece of its variable light chain.

In this project, we will be feeding the algorithm mouse and human antibody sequences only, and attempting to enable the algorithm to identity the species using this sequence data.

Although this may seem like an impossible task, given the genetic variability/ randomness of the variable region, but keep in mind that each species is pulling from a different set of genes to create this variabilty. Therefore, across species, similar sequence motifs are expressed. Having this knowledge, we will attempt to see if our algorithm can identify some of these motifs, and make a distinction based on species.

## Downfalls:

1. The dataset I used, the Kabat antibody dataset, is from the late 1990s, and is formatted very poorly for usage in machine learning. In addition, there were numerous empty datafields, forcing me to cut my dataset to a mere 1000 sequences

2. As a result of this, an unbelievable ammount of my code is pure data preparation, and my voyage into machine learning here is pretty unreasonable

3. Currently, my reccurent neural net using sequence data alone cannot run... although I continue to work on getting it to go.
