# Getting Started

Getting started with extracellular electrophysiology can be intimidating!
With numerous acquisition setups, software packages, preprocessing, postprocessing,
and analysis steps to consider, it's understandable to feel daunted.

A good first step in getting started is to ask for advice. Every setup
and experiment is different, and it's often most effective to
seek guidance early on. See the [Community](community)
sections for details on where to get help.

Before diving into the analysis, it can be useful to get an understanding
for the history and landscape of extracellular electrophysiology.
See the resources section for [general introductions](#general-introduction)
as well as more technical reading for deeper background.

To get started with pipeline building, the
[SpikeInterface](#spikeinterface) resources are a good starting point.
We also have
[Examples](gallery/index)
from researchers at the
SWC who'd be happy to answer any questions you might have.

# Resources

This section is by no means exhaustive, representing only a
small collection of resources that researchers at the SWC have found
useful in getting started.

## General Introduction

Below are a selection of papers that give a general background
and overview of the extracellular electrophysiology landscape:

[Challenges and opportunities for large-scale electrophysiology with Neuropixels probes](https://pubmed.ncbi.nlm.nih.gov/29444488/)

[Spike sorting: new trends and challenges of the era of high-density probes](https://iopscience.iop.org/article/10.1088/2516-1091/ac6b96/meta)

[Past, present and future of spike sorting techniques](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4674014/)

[Continuing progress of spike sorting in the era of big data](https://pubmed.ncbi.nlm.nih.gov/30856552/)

## Technical Introduction

Below are more technical resources on the different stages of
extracellular electrophysiology analysis.

A particularly useful resource is the
[NeuroPixels](https://www.ucl.ac.uk/neuropixels/courses) course, with their videos published online
(e.g. [2023](https://www.ucl.ac.uk/neuropixels/training/2023-neuropixels-course)).
While these are particularly useful for NeuroPixels users, they are
useful resources for any researcher
approaching electrophysiology preprocessing and analysis.

### Preprocessing

The [IBL white paper](https://figshare.com/articles/online_resource/Spike_sorting_pipeline_for_the_International_Brain_Laboratory/19705522)
contains a clearly written overview of preprocessing steps. Similarly, Bill Karsh's
[guide](https://billkarsh.github.io/SpikeGLX/help/catgt_tshift/catgt_tshift/) on the
SpikeGLX website also gives a useful overview.

[This paper](https://pubmed.ncbi.nlm.nih.gov/30998899/) provides a more
technical treatment of digital filtering, a key step in preprocessing and analysis.

### Spike Sorting

[This video](https://www.youtube.com/watch?v=vSydfDvsewY) with Christophe Pouzat, provides
an excellent overview of the spike-sorting problem.

[This chapter](https://neurophysics.ucsd.edu/publications/obd_ch3_2.pdf) provides a more
technical overview of spike sorting. It is also recommended to check out the
papers of existing spike sorting algorithms. A list of the main
spike sorters can be found [here](https://spikeinterface.readthedocs.io/en/latest/modules/sorters.html#supported-spike-sorters).


### Quality Metrics and Manual Curation

Assessing the quality of spike-sorting is a key to producing high-quality data.

These two papers provide a nice introduction to quality metrics of spike sorting:

[Quality Metrics to Accompany Spike Sorting of Extracellular Signals](https://www.jneurosci.org/content/31/24/8699)

[Improving data quality in neuronal population recordings](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5244825/)

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

[NeuroPixels Utils](https://djoshea.github.io/neuropixel-utils/) package
(MATLAB) and related [NeuroPyxels](https://github.com/m-beau/NeuroPyxels).
