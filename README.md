# Neural Coding in Visual Cortex Using Spike Data

## Overview

This project investigates how populations of neurons in the visual cortex encode sensory stimuli using large-scale extracellular electrophysiology data. The goal is to understand how neural activity patterns differ across cortical regions and how these differences relate to stimulus encoding.

This work is part of my transition into computational neuroscience, focusing on structure–function relationships in cortical circuits.

---

## Scientific Motivation

A central question in systems neuroscience is how sensory information is represented in distributed neural populations. While it is well established that neurons in early visual areas are highly selective for basic stimulus features, less is understood about how encoding properties change across cortical hierarchy in large-scale neural recordings.

This project explores these differences using real spike data from the Allen Brain Observatory.

---

## Data

Data is obtained from the Allen Brain Observatory, a large-scale electrophysiology dataset provided by the Allen Institute for Brain Science. The dataset contains Neuropixels recordings from visual cortical areas during presentation of controlled visual stimuli such as drifting gratings.

---

## Methods

Analysis is performed in Python using standard scientific computing tools.

Steps include:

- Loading electrophysiology session data
- Extracting spike times from single units
- Computing firing rates
- Visualizing spike trains
- (In progress) Constructing tuning curves for stimulus encoding

---

## Current Progress

- Successfully loaded raw spike data from Neuropixels recordings  
- Extracted spike trains from individual neurons  
- Visualized neuronal firing activity over time  

Further analysis will focus on comparing encoding properties across cortical areas.

---

## Tools

- Python  
- NumPy  
- Matplotlib  
- Pandas  
- AllenSDK  
- Jupyter Notebook  
- Git / GitHub  

---

## Reproducibility

All analysis is performed in Jupyter notebooks with fully reproducible code. Data is loaded directly from the Allen Brain Observatory cache system.

---

## Future Work

- Compute orientation tuning curves  
- Compare firing rate distributions across cortical areas  
- Quantify variability in neural responses  
- Extend analysis to population-level coding models  

---

## Author

Mercy Nathaniel  
Transitioning into Computational Neuroscience