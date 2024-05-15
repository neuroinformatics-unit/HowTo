# Roadmap

The ultimate goal of the NIU is to create tools to centralise and automate 
electrophysiology preprocessing and analysis at the SWC. We would ultimately 
like to move away from researchers having to code their own bespoke pipelines,
and can instantly use centralised pipelines for their data.
Unfortunately, this will take a lot of time due to the required infrastructure building. 

In the short-term, we are focusing on helping researchers use existing community
tools to build their electrophysiology pipelines, in particular with
[SpikeInterface](https://github.com/SpikeInterface)
We will provide support, advice, and examples from across the 
SWC community. We are working closely with
the SpikeInterface team to implement the tools required for researchers at the SWC.

Concurrently, we are working to build tools to centralise preprocessing and analysis
and abstract away as much as possibly the individual script-building.
In the first instance, this requires that all data in the SWC is 
organised in the same way. To this end,
we have released 
[NeuroBlueprint](https://neuroblueprint.neuroinformatics.dev/) 
and 
[**datashuttle**](https://datashuttle.neuroinformatics.dev/). 

To abstract away the details of pipeline development in SpikeInterface are building 
[spikewrap](https://github.com/neuroinformatics-unit/spikewrap) 
as a centralised tool for ephys preprocessing and analysis. This will require only
setting a set of configs before running this on your data. Allowing you to
skip the pipeline-building step and get straight into preprocessing and analysis using community tools.

## Timeline

A rough timeline for this roadmap is below. We are very keen to make sure we are
heading in the right direction, so please don't hestitate to get in contact
with any feedback on this roadmap and your priorities. 

### Q3 2024

Working directly with SpikeInterface to support their initiatives in:
- Improving the documentation
- Upgrading their interface and stabilising the API
- Adding new features (those requested by SWC researchers)

### Q4 2024

- Handle cross-session alignment in SpikeInterface (a widely requested
feature from researchers at the SWC).

### Q2 2025

- Beta version of 
[spikewrap](https://github.com/neuroinformatics-unit/spikewrap),
abstracting away building of ephys pipelines.



