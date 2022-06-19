# [fNIRS and EEG-fNIRS (Kernel Flow) models for The Virtual Brain (TVB)](https://summerofcode.withgoogle.com/programs/2022/projects/5Q2vnqyX)

## Overview

Supervisor: [Dr. John D. Griffiths](https://github.com/JohnGriffiths) @ [Krembil Centre for Neuroinformatics/CAMH](https://camh.ca/en/science-and-research/institutes-and-centres/krembil-centre-for-neuroinformatics)

Organisation: [International Neuroinformatics Coordinating Facility (INCF)](https://www.incf.org)

[The Virtual Brain (TVB)](https://thevirtualbrain.org/) is a widely-used software library for connectome-based modelling of large-scale neural dynamics and neuroimaging data. To date, TVB models have mainly focused on brain dynamics as measured by fMRI, EEG, and LFPs. Functional Near Infrared Spectroscopy (fNIRS) is another noninvasive neuroimaging modality, which like fMRI measures haemodynamic signals reflecting neural activity, that has major potential in cognitive and clinical neuroscience. 

The aim of this GSoC project will be to build out the modelling and analysis capacity of TVB for simulations of whole-head, high-resolution fNIRS signals, as well as concurrently recorded EEG. The project is structured around four key deliverables and will implement a temporal forward model for fNIRS-specific haemodynamic signals, a spatial forward model for optical sensor projection, and spatial forward models specific to the [Kernel Flow fNIRS+EEG system](https://www.kernel.com), that we will have access to and be using as part of the project. These implementations are to be supported via extensive documentation and ecosystem outreach.

## Impact
 - Extends the TVB capability to include fNIRS, a major methodology in e.g., infant, performance, mobile, and multi-modal neuroimaging.
 - Enables researchers using fNIRS to augment their analyses with the TVB platform, and to use multimodal simulation capacities.
 - Enables researchers using Kernel Flow convenient custom monitors appropriate for use with studies featuring the device. 
 - Supports the work of Prof. Griffiths and the Centre for Addiction and Mental Health (CAMH) in using novel neurotechnology for “brain stimulation, schizophrenia, concussion, and computational neuroscience”.
 
 ## Background
 
fNIRS enables the observation of haemodynamic responses, providing an indirect measure of brain activity via neurovascular coupling similar to the BOLD repsonse,  however the technology is different from magnetic resonance imaging. fNIRS data is collected from a subject and recorded at channels, intermediary locations between a source, which emits near-infrared light, and a detector, which retrieves it. Hemoglobin dependent differences in light absorption are detected at different wavelengths and measure changes in oxyhemoglobin (HbO) and deoxyhemoglobin (HbR) concentration in the blood. fNITS optodes are mounted in a montage arrangement on the subjects head, traditionally using a fitted cap. The Kernel Flow (Ban et al., 2021) is unique as a next-generation noninvasive device enabling mobile combined electrophysiological and haemodynamic functional analysis via EEG and time domain fNIRS (TD-fNIRS).

<div align="center">
<img width="468" alt="The TVB Hybrid Modeling Framework (Schirner et al. 2018, Fig. 1)" src="https://user-images.githubusercontent.com/49303905/174487649-b99d63a8-a2cf-4393-bdad-a2baf99e3a94.png">

The TVB Hybrid Modeling Framework (Schirner et al. 2018, Fig. 1)
</div>

The above schematic depicts the framework TVB uses in its essential function as a model relating the brain's structure to its function. Looking at the upper right, the connectivity is defined by a network model. This can be either inherited from public datasets, e.g. from the Human Connectome Project (as we will do in this project), or constructed based on the connectomics of an individual via appropriate neuroimaging method for tractography and brain parcellation.  The model produces vertices at which are the locations for activity of a neural generative model, in our project these are the Jansen-Rit and Wong-Wang-Deco (‘reduced Wong-Wang’) Neural Mass Models. These provide outputs of neural activity, that function as inputs for an observation mode that depending on its type transforms neural activity into a time series that is typically observed using one of the imaging modalities, e.g. EEG or fMRI. This simulated activity can then be modified via simulation parameters, providing insight into the effects of generative causes for observed phenomena.

TVB functions by splitting brain simulations into generative neural models representing the behaviour of neural populations via coupled stochastic delay differential equations, seen below, and a forward observation model that operates on their output to produce phenomena observed in imaging recordings of e.g., EEG, or as a novel contribution of this project, fNIRS. 

<div align="center">
<img width="391" alt="Evolution equation of a TVB brain model (TVB course 2020/21)" src="https://user-images.githubusercontent.com/49303905/174487834-0a7830e6-1e19-43a1-8f47-68008e1b6169.png">

Evolution equation of a TVB brain model (TVB course 2020/21)
</div>

There are a variety of specific neural models and more general neural model types that have been developed to emphasise different aspects of neural systems. In TVB models appropriate for representing mesoscale phenomena are typically used. Scientifically, this project is interested in enabling the use of e.g., fNIRS and EEG co-simulation to explore reproducability of empirically observed phenomena of alpha-BOLD anticorrelation in EEG-haemodynamic brain recordings.  This phenomena has previously been reproduced in TVB (Schirner et al., 2018) and the neural models used in this project are known to produce these behaviours.

<div align="center">
<img width="418" alt="The reduced Wong-Wang Neural Mass Model (TVB course 2020/21)" src="https://user-images.githubusercontent.com/49303905/174487852-20c78ab2-f32d-4620-8847-4203ca2bbb4f.png">

The reduced Wong-Wang Neural Mass Model (TVB course 2020/21)
</div>

## Roadmap
[View here](https://github.com/pfcrowe/gsoc-tvb-fnirs/blob/main/ROADMAP.md) 

### Key References
- Ban, H. Y., Barrett, G. M., Borisevich, A., Chaturvedi, A., Dahle, J. L., Dehghani, H., ... & Zhu, Z. (2021, March). Kernel Flow: a high channel count scalable TD-fNIRS system. In Integrated Sensors for Biological and Neural Sensing (Vol. 11663, pp. 24-42). SPIE.
 - Schirner, M., McIntosh, A. R., Jirsa, V., Deco, G., & Ritter, P. (2018). Inferring multi-scale neural mechanisms with brain network modelling. Elife, 7, e28927.
 - Schirner, M., Domide, L., Perdikis, D., Triebkorn, P., Stefanovski, L., Pai, R., ... & Ritter, P. (2022). Brain simulation as a cloud service: The Virtual Brain on EBRAINS. NeuroImage, 251, 118973.
 - Tak, S., Kempny, A., Friston, K. J., Leff, A. P., & Penny, W. D. (2015). Dynamic causal modelling for functional near-infrared spectroscopy. Neuroimage, 111, 338-349.





 
