# Roadmap

The ultimate goal of the NIU is to centralise and automate
electrophysiology preprocessing and analysis at the SWC. We aim to move away
from researchers having to create their own customized pipelines.

In the short-term, we are focusing on assisting researchers use existing community
tools to build their electrophysiology pipelines, encouraging the use of
[SpikeInterface](https://github.com/SpikeInterface)
We will provide support, advice, and examples from across the
SWC community. We are working closely with
the SpikeInterface team to implement the tools
required for researchers at the SWC.

Concurrently, we are developing centralise tools to facilitate
automated analysis and minimize the need for custom scripts.
Initially, this involves standardizing data organization across the SWC.
To this end, we have released
[NeuroBlueprint](https://neuroblueprint.neuroinformatics.dev/)
and
[datashuttle](https://datashuttle.neuroinformatics.dev/) to promote
project folder standardisation.
We are now building
[spikewrap](https://github.com/neuroinformatics-unit/spikewrap)
as a centralised tool for ephys preprocessing and analysis with SpikeInterface.
This aims to require only setting a configuration file specifying the type of analysis you'd like to run.

## Timeline

A rough timeline for this roadmap is below. We are very keen to make sure we are
heading in the right direction, so please get in contact
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
[spikewrap](https://github.com/neuroinformatics-unit/spikewrap).
