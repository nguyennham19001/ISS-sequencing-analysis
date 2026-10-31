# Quantitative biomedical image analysis methods and applications for In situ Sequencing

## 1. Motivation 

Established transcriptomics techniques can be used to get insights into gene expression profiles of tissues or single cells. Recent advances enable to further track the spatial localization and dynamics of transcripts at sub-cellular resolution [1]. Raw data of spatial transcriptomics experiments (e.g. in-situ-sequencing) are complex and diverse. In order to extract biologically meaningful insights from this data, they need to be computationally processed.

## 2. Research aim 

The primary purpose of this research is to learn and apply basic quantitative image analysis
techniques for spatial transcriptomics on a publicly available in-situ sequencing dataset from the
mouse brain. Based on these techniques, the refined pipeline can be developed to preprocess
images, quantify the number of cells and the expression of each gene in every field of view (FOV)
of the above dataset. 

## 3. Dataset 

In this research, the dataset is taken from published in-situ sequencing (ISS) data from a mouse
brain. In essence, it contained 171 selected tiles from the right hemisphere of a mouse brain.
(referred to as fields of view, FOV). For each field of view, imaging was conducted across four
5
sequencing rounds, each of which consists of six image channels (1000x1000 pixels), stained with
six different types of fluorophores. These include four channel images corresponding to the four
nucleobases (A, C, G, T), alongside a DAPI nuclear image and an anchor image. All images within
a sequencing round were already registered to each other.

In addition to the imaging data, the dataset contains other supporting files as follows:
* Tile_name.csv: names of the selected tiles
* Selected_tiles_map.png: Map of selected tiles
* Taglist_csv: codebook of barcodes used in the experiment
* Decoding: Decoding results of the original research with three methods: argmax, k-means,
and PoST

## 4. Results 

The computational analysis pipeline comprises two main stages:
nuclei segmentation from the DAPI channel and transcript detection with barcode decoding,
which produces a table of predicted transcripts and an aggregated gene count table for
downstream biological interpretation. The proposed pipeline was initially developed and
evaluated on a single field of view (FOV), expanded to multiple FOVs, and ultimately applied
across the full dataset for downstream biological interpretation.

### 4.1. Nuclei counting 

Before applying programming pipeline for this stages, the manual inspection need to be done in advance by using ImageJ. 
By exploring plausible size of nuclei, we can have the evidence to filter nuclei by size in the final step. 
The nuclei were then segmented and counted with a
programming-based pipeline, including image preprocessing (Gaussian filter, morphological
opening), segmentation (thresholding, watershed application), and size-based filtering of the
segmented objects. 

In this dataset, a significant difference in the number of nuclei across
four cycles was recorded (especially the second round compared to the remaining rounds).
Therefore, nuclei quantification can be achieved by calculating the mean of nuclei counts
obtained from DAPI images across all four imaging rounds.


### 4.2. Find and identify the transcripts 

#### Spot detection 
Four anchor channels of each FOV were combined through MIP.Before detecting bright spots, the MIP image quality was enhanced by applying a White
top-hat filter and a Gaussian filter, respectively. Finally, the spot detection step was conducted
by applying trackpy with the following parameters: diameter = 5, percentile = 0, separation = 2.

#### Barcode decoding 

The same concept of MIP was applied with sequencing channels across four cycles
(16 channels in total). all
sequencing images from a FOV could be stacked into a single 4D NumPy array (round, number,
height, width). Accordingly, all the intensities at a certain (y, x) coordinate could be selected
16
simultaneously. To deal with the effects of outliers and intensity bias between channels, the
intensities were normalized using min-max scaling and histogram matching (by Starfish) before
decoding. Finally, the decoded barcodes were mapped to gene names to achieve expression
profiles of genes across all FOVs.

#### Napari visualization 

The results can finally be visualized and inspected in Napari. Each sequencing channel can be set
to a specific color (A=magenta, G=green, T=yellow, C=red) and blended to "additive". In addition,
the DAPI channel and predicted spots could be added.

#### Expression pattern of a gene across FOVs 

Across all FOVs, Gapdh and Bcl11b were the two most highly expressed genes in the panel.
Gapdh, a housekeeping gene, also known as a key glycolytic enzyme that facilitates the
production of cellular energy was consistently abundant, supporting the reliability of the
decoding. Bcl11b, also known as B cell leukemia 11b which is a zinc finger protein transcription
factor with a multiplicity of functions including both genetic suppressor and activator. 

