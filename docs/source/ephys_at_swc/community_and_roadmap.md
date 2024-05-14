# Community and Roadmap

## Community

The best place to get information and advice on extracellular ephys at the SWC is 
in the `#forum-extracellular-ephys` channel on the SWC slack. 

Outside the SWC, you can ask any questions or issues about 
[spikeinterface](https://github.com/SpikeInterface)
by raising an issue on their 
[github repo](https://github.com/SpikeInterface/spikeinterface/issues).
They are very friendly and
happy to answer any questions!

Finally, the 
[Neuropixels](https://neuropixelsgroup.slack.com/join/shared_invite/zt-2c8u0k21u-CC4wSkb8~U_Fkrf3HHf_kw#/shared-invite/email)
Slack is a great resource, with an active community asking and answering questions on both
extracellular ephys acquisition and analysis.


## Roadmap

The ultimate goal of the NIU is to create tools to centralise and automate 
electrophysiology preprocessing and analysis at the SWC. We would ultimately 
like to move away from researchers having to code their own bespoke pipelines,
and can instantly use centralised pipelines for their data.

Unfortunately, this takes a lot of time. In the first instance, we are
reccomending people use 
[spikeinterface]()
to build their pipelines, with examples, advice and support 
from the NIU and SWC community. [spikeinterface]() is the most widely used,
open source and community tooling in systems neuroscience for building extracellular
ephys pipelines. Please see the [Getting Started](#getting_started.md) page for
advice on building your own spikeinterface pipeline. We are working closely with
the [spikeinterface team]() to implement the tools required for researchers at the SWC.

Next, we are working to build tools to centralise preprocessing and analysis
and abstract away as much as possibly the individual script-building.
In the first instance, this requires that all data in the SWC is 
organised in the same way, othewise this goal is impossible. To this end,
we are working on [NeuroBlueprint]() and [**datashuttle**](). It would
be very useful for you to organise your data in this way to make use of
our tooling at the SWC.

Finally, we are working on the tool [spikewrap]() as a centralised
tool for ephys preprocessing and analysis. This will require only
setting a set of configs before running this on your data. Allowing you to
skip the pipeline-building step and get straight into preprocessing and analysis using community tools.

A rough timeline for this roadmap is below. We are very keen to make sure we are
heading in the right direction, so please don't hestitate to get in contact
with any feedback on this roadmap and your priorities. 

## Timeline

Q3 2024

Working directly with SpikeInterface to support their initiatives in:
- Improving the documentation
- Upgrades to their interface and stabilising the API

Q4 2024

Handle cross-session alignment in spikeinterface (a widely requested
feature from researchers at the SWC).

Q2 2025

Beta version of [spikewrap](), abstracting away building of ephys pipelines.



