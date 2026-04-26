# Neural Coding Research Timeline (5 Weeks)

**Project**: Neural Coding in Visual Cortex Using Allen Brain Observatory
**Timeline**: April 25 - May 31, 2026 (36 days)
**Goal**: Complete multi-area neural encoding analysis with population-level insights

---

## Week 1: Setup & Data Exploration (Apr 25 - May 1)

### Objectives
- Install AllenSDK and dependencies
- Load data from Allen Brain Observatory
- Explore spike data structure
- Visualize neural activity

### Tasks
- [ ] Complete SETUP.md installation (requirements.txt)
- [ ] Read ALLEN_QUICKSTART.md thoroughly
- [ ] Run: Load first session data
- [ ] Notebook: 00_setup_and_verification.ipynb
- [ ] Notebook: 01_explore_spike_data.ipynb

### Code Tasks
```python
# Week 1 Challenge: Load data and create first visualizations
from allensdk.brain_observatory.ecephys.ecephys_project_cache import EcephysProjectCache

cache = EcephysProjectCache(manifest_path='manifest.json')
sessions = cache.get_sessions()
session = cache.get_session_data(session_id=sessions.iloc[0]['id'])

# Questions to answer:
# 1. How many neurons are in this session?
# 2. What brain areas are represented?
# 3. What's the range of firing rates?
# 4. How long is the recording?
```

**Deliverable**: 
- README file documenting your first session
- Basic statistics plot (firing rate distribution)
- Spike raster visualization for 5 neurons

---

## Week 2: Firing Rate Analysis & Cross-Area Comparison (May 2-8)

### Objectives
- Compute firing rates across stimulus types
- Compare neural activity across cortical areas
- Analyze response variability
- Understand stimulus effects on firing

### Tasks
- [ ] Notebook: 02_firing_rate_computation.ipynb
- [ ] Notebook: 03_cross_area_comparison.ipynb
- [ ] Analyze: Response to different stimulus types
- [ ] Create: Box plots comparing areas (VISp, VISl, VISrl)

### Code Tasks
```python
# Week 2 Challenge: Firing rate analysis pipeline

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# For each stimulus type, compute:
# 1. Mean firing rate per neuron
# 2. Firing rate variance
# 3. Peak firing rates
# 4. Response latency

# Compare across areas:
# - VISp vs VISl vs VISrl
# - Which area has highest mean FR?
# - Which area has most variable responses?
```

**Deliverable**: 
- Firing rate statistics table (brain area × stimulus type)
- Comparative plots (box plots, violin plots)
- Written interpretation: What does firing pattern tell us?

---

## Week 3: Orientation Tuning & Selectivity (May 9-15)

### Objectives
- Construct orientation tuning curves
- Quantify tuning selectivity
- Identify orientation-selective neurons
- Compare tuning properties across areas

### Tasks
- [ ] Notebook: 04_orientation_tuning.ipynb
- [ ] Notebook: 05_tuning_selectivity.ipynb
- [ ] Analyze: Find most tuned neurons in each area
- [ ] Create: Tuning curve plots for representative neurons

### Code Tasks
```python
# Week 3 Challenge: Orientation tuning analysis

# For drifting grating stimulus:
# 1. Extract responses to each orientation (0°-360°)
# 2. Compute mean firing rate per orientation
# 3. Fit Gaussian to tuning curve
# 4. Calculate: Tuning width, preferred orientation, DSI (direction selectivity)

# Metrics to compute:
# - Orientation Selectivity Index (OSI)
# - Direction Selectivity Index (DSI)
# - Tuning width (FWHM)
# - Response reliability (SNR)

# Questions:
# - What % neurons are significantly tuned?
# - How does tuning differ between V1 vs higher areas?
```

**Deliverable**: 
- Orientation tuning curves for 10+ representative neurons
- Scatter plots: OSI vs firing rate, DSI vs brain area
- Summary statistics: % tuned neurons by area
- Heatmap: Tuning properties across neurons

---

## Week 4: Population Coding & Information Analysis (May 16-22)

### Objectives
- Analyze population-level coding properties
- Compute decoding accuracy (stimulus from population)
- Quantify redundancy vs synergy
- Understand population geometry

### Tasks
- [ ] Notebook: 06_population_decoding.ipynb
- [ ] Notebook: 07_information_theory.ipynb
- [ ] Implement: Simple linear decoder (stimulus angle → population)
- [ ] Analyze: How many neurons needed for reliable decoding?

### Code Tasks
```python
# Week 4 Challenge: Population coding analysis

from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score

# Construct population response matrix:
# Rows: trials
# Columns: neuron firing rates
# Target: stimulus orientation

X = construct_population_response(session, neurons)
y = stimulus_orientations

# Decoding:
# 1. Train linear classifier (population → stimulus)
# 2. Cross-validate accuracy
# 3. Vary neuron count: how many needed for 80% accuracy?
# 4. Compare: V1 vs higher area decoding performance

# Information theory:
# - Mutual information (stimulus & population)
# - Redundancy & synergy
# - Information per neuron
```

**Deliverable**: 
- Decoding accuracy curves (% correct vs neurons used)
- Comparison: V1 vs other areas decoding capability
- Information summary: bits per neuron by area
- Interpretation: What does population tell us about coding?

---

## Week 5: Synthesis & Spatial Transcriptomics Integration (May 23-31)

### Objectives
- Synthesize 5 weeks of findings
- Create comprehensive results report
- Plan integration with spatial transcriptomics
- Prepare for next research phase

### Tasks
- [ ] Notebook: 08_comprehensive_analysis.ipynb (all previous steps)
- [ ] Notebook: 09_results_summary.ipynb
- [ ] Create: Publication-quality figures (4-6 key plots)
- [ ] Write: 2-page results summary
- [ ] Plan: How to integrate with scRNA-seq data

### Code Tasks
```python
# Week 5 Challenge: Complete integrated analysis

# Capstone workflow:
# 1. Load multi-area session
# 2. Full preprocessing: unit filtering, response extraction
# 3. Firing rate analysis
# 4. Orientation tuning (if available)
# 5. Population decoding
# 6. Cross-area comparison
# 7. Generate summary statistics

# Create results document:
# - Session metadata
# - Number of neurons per area
# - Key findings (tuning, decoding, etc.)
# - Comparison to literature values
# - High-quality visualizations

# Plan integration:
# - Which neurons to look up in scRNA-seq?
# - What cell types might show orientation selectivity?
# - How to link electrophysiology → transcriptomics?
```

**Deliverable**: 
- Complete analysis notebook (reproducible, well-commented)
- Figure package (4-6 publication-ready plots):
  - Firing rate comparison across areas
  - Orientation tuning examples
  - Population decoding accuracy
  - Information content summary
- 2-page results write-up with:
  - Key findings
  - Biological interpretation
  - Connections to transcriptomics
- GitHub commit log showing progress (at least 1 commit/week)

---

## Success Criteria

By May 31, you should be able to:

- [ ] Load Allen Brain Observatory data from any session
- [ ] Extract and visualize spike trains
- [ ] Compute firing rates and compare across areas
- [ ] Construct and interpret orientation tuning curves
- [ ] Implement population decoding (stimulus from neural activity)
- [ ] Calculate information-theoretic metrics
- [ ] Interpret results in context of visual neuroscience
- [ ] Create publication-quality visualizations
- [ ] Link neural coding to molecular (transcriptomics) data

---

## Weekly Schedule Template

### Daily (45-60 min)
```
Monday-Wednesday:
- 15 min: Review previous week concept
- 30 min: Write analysis code
- 15 min: Test & debug

Thursday:
- 45 min: Complete weekly analysis task
- 15 min: Create visualization

Friday:
- 30 min: Interpret results
- 15 min: Write summary notes
- 15 min: Plan next week
```

---

## Key Concepts by Week

| Week | Concept | Why It Matters |
|------|---------|----------------|
| 1 | Spike data structure | Foundation for all analysis |
| 2 | Firing rates | Basic neural response metric |
| 3 | Orientation tuning | Measure neural selectivity |
| 4 | Population coding | Information representation |
| 5 | Integration | Connect to transcriptomics |

---

## Data Organization

```
results/
├── week1_exploration/
│   ├── session_summary.csv
│   └── spike_rasters.png
├── week2_firing_rates/
│   ├── firing_rate_stats.csv
│   └── area_comparison.png
├── week3_tuning/
│   ├── tuning_curves.png
│   ├── osi_dsi_stats.csv
│   └── tuned_neurons.txt
├── week4_population/
│   ├── decoding_curves.png
│   ├── information_summary.csv
│   └── population_analysis.png
└── week5_synthesis/
    ├── comprehensive_results.md
    ├── publication_figures/
    └── final_report.pdf
```

---

## Troubleshooting

| Challenge | Solution |
|-----------|----------|
| Data download too slow | Check internet, use different session |
| Memory issues | Process fewer neurons per session |
| Tuning curves look weird | Check stimulus parameters, response window |
| Decoding accuracy too low | Try more neurons, different classifier |
| "Don't understand why result makes sense" | Check neuroscience background reading |

---

## Resources

### Papers to Reference
- **Allen Brain Observatory paper**: Nature 2021
- **Visual cortex encoding**: Schrader et al., Nature Neuroscience
- **Population coding**: Gross et al., recent reviews

### Code References
- AllenSDK documentation
- scikit-learn for decoding
- Information theory packages (optional)

---

## Tracking Progress

Update weekly:

**Week 1 Completion**: ___ / 5 tasks | Confidence: ___ / 10
**Week 2 Completion**: ___ / 4 tasks | Confidence: ___ / 10
**Week 3 Completion**: ___ / 4 tasks | Confidence: ___ / 10
**Week 4 Completion**: ___ / 4 tasks | Confidence: ___ / 10
**Week 5 Completion**: ___ / 5 tasks | Confidence: ___ / 10

**Most interesting finding so far**: ...

**Ready for transcriptomics integration**: Yes / No
