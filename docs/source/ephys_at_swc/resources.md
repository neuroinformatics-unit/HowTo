# Resources

This section is by no means exhaustive, representing only a
small collection of resources that researchers at the SWC have found
useful in getting started.

## General Introduction

Below are a selection of papers that give a general background
and overview of the extracellular electrophysiology landscape:

[Steinmetz NA et al. (2018). Challenges and opportunities for large-scale electrophysiology with Neuropixels probes. *Current Opinion in Neurobiology*.](https://pubmed.ncbi.nlm.nih.gov/29444488/)

[Buccino AP et al. (2022). Spike sorting: new trends and challenges of the era of high-density probes. *Progress in Biomedical Engineering*.](https://iopscience.iop.org/article/10.1088/2516-1091/ac6b96/meta)

[Rey HG et al. (2015). Past, present and future of spike sorting techniques. *Brain Research Bulletin*.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4674014/)

[Carlson D et al. (2019). Continuing progress of spike sorting in the era of big data. *Current Opinion in Neurobiology*](https://pubmed.ncbi.nlm.nih.gov/30856552/)

## Technical Introduction

Below are more technical resources on the different stages of
extracellular electrophysiology analysis.

A particularly useful resource is the
[Neuropixels](https://www.ucl.ac.uk/neuropixels/courses) course, with their videos published online
(e.g. [2023](https://www.ucl.ac.uk/neuropixels/training/2023-neuropixels-course)).
While these are particularly useful for Neuropixels users, they are
useful resources for any researcher
approaching electrophysiology preprocessing and analysis.

### Preprocessing

The [IBL white paper](https://figshare.com/articles/online_resource/Spike_sorting_pipeline_for_the_International_Brain_Laboratory/19705522)
contains a clearly written overview of preprocessing steps. Similarly,
[Bill Karsh's guide](https://billkarsh.github.io/SpikeGLX/help/catgt_tshift/catgt_tshift/) on the
SpikeGLX website also gives a useful overview.

[de Cheveigné & Nelken (2019)](https://pubmed.ncbi.nlm.nih.gov/30998899/)
provide a more technical treatment of digital filtering, a key step in preprocessing and analysis.

### Spike Sorting

[This video on Spike Sorting with Christophe Pouzat](https://www.youtube.com/watch?v=vSydfDvsewY),
provides an excellent overview of the spike-sorting problem.

[This chapter](https://neurophysics.ucsd.edu/publications/obd_ch3_2.pdf) provides a
more detailed introduction to spike sorting and associated quality metrics.

It is also recommended to check out the
papers of existing spike sorting algorithms. A list of the main
spike sorters can be found
[on the SpikeInterface website](https://spikeinterface.readthedocs.io/en/latest/modules/sorters.html#supported-spike-sorters).


### Quality Metrics and Manual Curation

Assessing the quality of spike-sorting is a key to producing high-quality data.

These two papers provide a nice introduction to quality metrics of spike sorting:

[Hill et al. (2011). Quality Metrics to Accompany Spike Sorting of Extracellular Signals. *Journal of Neuroscience*.](https://www.jneurosci.org/content/31/24/8699)

[Harris et al. (2016). Improving data quality in neuronal population recordings. *Nature Neuroscience*.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5244825/)

[Phy](https://github.com/cortex-lab/phy)
is the most popular tool for performing manual curation of spike sorting results.
A great [guide by Steve Lenzi](https://phy.readthedocs.io/en/latest/sorting_user_guide/) takes you
through the key steps for manual curation.

More recently, advances in the automation of curation has been made using the
[Bombcell package](https://github.com/Julie-Fabre/bombcell).

SpikeInterface also maintain a set of quality metrics,
[explained in detail](https://spikeinterface.readthedocs.io/en/latest/modules/qualitymetrics.html)
in their documentation.

## SpikeInterface

Visit the SpikeInterface
[GitHub](https://github.com/SpikeInterface/spikeinterface)
and
[Documentation](https://spikeinterface.readthedocs.io/en/latest/index.html)
to get started. Note their documentation currently points to the developer
version, select your installed version from the list at the bottom-left
of the page.

## Other Community Tools

### Analysis

SpikeInterface
is mainly focused on preprocessing, spike sorting and quality metrics.
[Pynapple](https://github.com/pynapple-org/pynapple)
and
[Elephant]( https://neuralensemble.org/elephant/)
both provide nice toolboxes for analysing data post-sorting.

The [SpikeForest](https://spikeforest.flatironinstitute.org/)
project is an excellent resource for assessing the performance of
different spike-sorting algorithms across probe types and brain regions.

### Pipelines

[The Allen Spike sorting pipeline](https://github.com/AllenInstitute/ecephys_spike_sorting)

[The IBL sorting pipeline](https://github.com/int-brain-lab/ibl-neuropixel)

[Neuropixels Utils](https://djoshea.github.io/neuropixel-utils/) package
(MATLAB) and related [NeuroPyxels](https://github.com/m-beau/NeuroPyxels).
