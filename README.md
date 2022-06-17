# [fNIRS and EEG-fNIRS (Kernel Flow) models for The Virtual Brain](https://summerofcode.withgoogle.com/programs/2022/projects/5Q2vnqyX)

Supervisor: [John Griffiths](https://github.com/JohnGriffiths) @ [Krembil Centre for Neuroinformatics/CAMH](https://camh.ca/en/science-and-research/institutes-and-centres/krembil-centre-for-neuroinformatics)

Organisation: [International Neuroinformatics Coordinating Facility (INCF)](https://www.incf.org)

[The Virtual Brain (TVB)](https://thevirtualbrain.org/) is a widely-used software library for connectome-based modelling of large-scale neural dynamics and neuroimaging data. To date, TVB models have mainly focused on brain dynamics as measured by fMRI, EEG, and LFPs. Functional Near Infrared Spectroscopy (fNIRS) is another noninvasive neuroimaging modality, which like fMRI measures haemodynamic signals reflecting neural activity, that has major potential in cognitive and clinical neuroscience. 

The aim of this GSoC project will be to build out the modelling and analysis capacity of TVB for simulations of whole-head, high-resolution fNIRS signals, as well as concurrently recorded EEG. The project is structured around four key deliverables and will implement a temporal forward model for fNIRS-specific haemodynamic signals, a spatial forward model for optical sensor projection, and spatial forward models specific to the [Kernel Flow fNIRS+EEG system](https://www.kernel.com), that we will have access to and be using as part of the project. These implementations are to be supported via extensive documentation and ecosystem outreach.
