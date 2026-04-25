# RESEARCH_TIMELINE.md - 5-Week Neural Coding Research Plan

Your structured research roadmap for May 1-31, 2026.

---

## Overview

This timeline parallels your RNA-seq learning. Both projects build toward integration by June.

**Goal:** Analyze neural encoding in visual cortex → Identify high-encoding neurons → Connect to transcriptomics

---

## Week 1: Data Exploration & Spike Train Analysis (May 1-7)

### Learning Outcomes
- Load Allen Brain Observatory data smoothly
- Understand spike timing and neural activity patterns
- Visualize firing across different brain areas
- Select sessions for detailed analysis

### Tasks
- [ ] Complete `ALLEN_QUICKSTART.md` installation
- [ ] Load 3 different sessions with drifting gratings
- [ ] Plot spike raster plots for 50 units per session
- [ ] Calculate basic firing rates across brain areas
- [ ] Document which sessions are best for analysis

### Code Template

```python
from allensdk.brain_observatory.ecephys.manifest import EcephysProjectCache
import matplotlib.pyplot as plt
import numpy as np

# Initialize
cache = EcephysProjectCache(manifest_file='ecephys_cache/manifest.json')
sessions = cache.get_session_table()

# Load first good session
session_id = sessions[sessions['has_drifting_gratings']].index[0]
session = cache.get_session_data(session_id)

spike_times = session.spike_times
unit_ids = session.units.index.values

# Plot raster for first 50 units
fig, ax = plt.subplots(figsize=(12, 8))
for idx, unit_id in enumerate(unit_ids[:50]):
    spikes = spike_times[unit_id]
    ax.vlines(spikes, idx - 0.5, idx + 0.5, color='black', linewidth=0.5)

ax.set_ylim(-1, 50)
ax.set_xlabel('Time (s)')
ax.set_ylabel('Unit ID')
ax.set_title(f'Spike Raster - Session {session_id}')
plt.tight_layout()
plt.savefig('results/figures/01_spike_raster.png', dpi=150)
plt.close()

# Firing rates
firing_rates = []
for unit_id in unit_ids:
    spikes = spike_times[unit_id]
    total_time = session.stimulus_timestamps[-1]
    fr = len(spikes) / total_time
    firing_rates.append(fr)

print(f"Mean firing rate: {np.mean(firing_rates):.2f} Hz")
print(f"Range: {np.min(firing_rates):.2f} - {np.max(firing_rates):.2f} Hz")
```

### Deliverable
- `results/figures/01_spike_raster.png` - Spike raster plots
- `data/session_summary.csv` - Table of 3-5 candidate sessions

**Progress: ⬜ Not started**

---

## Week 2: Firing Rate Analysis & Stimulus Response (May 8-14)

### Learning Outcomes
- Compute firing rates during different stimulus conditions
- Understand stimulus-response relationships
- Compare responses across brain areas
- Identify responsive vs non-responsive neurons

### Tasks
- [ ] Extract drifting grating stimulus parameters
- [ ] Compute mean firing rate per orientation
- [ ] Create tuning curves (prelim - just firing vs orientation)
- [ ] Compare VISp vs VISl vs VISpm response properties
- [ ] Identify "responsive" neurons (significant modulation)

### Code Template

```python
# Get stimulus data
stim_table = session.get_stimulus_table('drifting_gratings')
spike_times = session.spike_times
unit_ids = session.units.index.values

# For each unit, compute firing rate per orientation
orientations = stim_table['orientation'].unique()
firing_by_orientation = {}

for unit_id in unit_ids[:20]:  # First 20 units
    response = []
    
    for orientation in sorted(orientations):
        # Get trials with this orientation
        trials = stim_table[stim_table['orientation'] == orientation]
        
        # Count spikes during these trials
        spike_counts = []
        for idx, trial in trials.iterrows():
            start = trial['start_time']
            end = trial['stop_time']
            spikes = spike_times[unit_id]
            count = np.sum((spikes >= start) & (spikes <= end))
            spike_counts.append(count)
        
        mean_rate = np.mean(spike_counts) / (trials.iloc[0]['stop_time'] - trials.iloc[0]['start_time'])
        response.append(mean_rate)
    
    firing_by_orientation[unit_id] = response

# Plot tuning curves
fig, axes = plt.subplots(2, 2, figsize=(10, 10))
for idx, (unit_id, response) in enumerate(list(firing_by_orientation.items())[:4]):
    ax = axes.flat[idx]
    ax.plot(sorted(orientations), response, 'o-', linewidth=2, markersize=6)
    ax.set_xlabel('Orientation (°)')
    ax.set_ylabel('Firing Rate (Hz)')
    ax.set_title(f'Unit {unit_id}')

plt.tight_layout()
plt.savefig('results/figures/02_tuning_curves_preliminary.png', dpi=150)
plt.close()
```

### Deliverable
- `results/figures/02_tuning_curves_preliminary.png` - Tuning curves for sample units
- `results/data/firing_rates_by_condition.csv` - Compiled firing rates

**Progress: ⬜ Not started**

---

## Week 3: Orientation Tuning Analysis (May 15-21)

### Learning Outcomes
- Quantify tuning curve properties (peak, bandwidth, selectivity)
- Compute information content (bits/spike)
- Understand neural coding efficiency
- Compare tuning across cortical areas

### Tasks
- [ ] Compute proper tuning curves with confidence intervals
- [ ] Calculate orientation selectivity index (OSI)
- [ ] Compute direction selectivity index (if moving bars)
- [ ] Estimate information content (mutual information)
- [ ] Create summary statistics table

### Code Template

```python
from scipy.stats import circmean, circstd
from scipy.special import i0

def compute_orientation_selectivity(mean_response, sem_response, orientations):
    """Compute OSI following standard neuroscience methods"""
    # Convert to radians
    ori_rad = np.radians(orientations * 2)  # Double because 180°=0°
    
    # Circular mean direction
    preferred_ori = circmean(ori_rad, weights=mean_response)
    
    # OSI = (Rpref - Rorth) / (Rpref + Rorth)
    # where Rpref is response at preferred orientation
    # and Rorth is response at orthogonal orientation
    
    pref_response = mean_response[np.argmin(np.abs(ori_rad - preferred_ori))]
    orth_idx = (np.abs(ori_rad - (preferred_ori + np.pi/2)) % np.pi).argmin()
    orth_response = mean_response[orth_idx]
    
    osi = (pref_response - orth_response) / (pref_response + orth_response + 1e-6)
    
    return osi, preferred_ori

# Compute for all units
osi_values = []
pref_orientations = []

for unit_id in unit_ids:
    mean_response = firing_by_orientation[unit_id]
    osi, pref_ori = compute_orientation_selectivity(
        np.array(mean_response), 
        np.zeros_like(mean_response),  # Would have SEM from real data
        sorted(orientations)
    )
    osi_values.append(osi)
    pref_orientations.append(np.degrees(pref_ori))

# Statistics
print(f"Mean OSI: {np.mean(osi_values):.3f}")
print(f"Selective units (OSI > 0.5): {np.sum(np.array(osi_values) > 0.5)}/{len(osi_values)}")
```

### Deliverable
- `results/figures/03_tuning_statistics.png` - OSI distributions
- `results/data/tuning_properties.csv` - OSI, preferred orientation for all units

**Progress: ⬜ Not started**

---

## Week 4: Population Coding & Cross-area Comparison (May 22-28)

### Learning Outcomes
- Understand population-level encoding
- Compare coding efficiency across brain areas
- Identify which areas represent visual information best
- Quantify information redundancy

### Tasks
- [ ] Compute population firing vectors per stimulus
- [ ] Decode orientation from population activity
- [ ] Calculate decoding accuracy per brain area
- [ ] Quantify population information content
- [ ] Create comparison plots (VISp vs VISl vs VISpm)

### Code Template

```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score
import pandas as pd

def decode_orientation(spike_data, stimuli, cv=5):
    """
    Decode stimulus orientation from population spike counts
    
    spike_data: (n_trials, n_neurons) array
    stimuli: (n_trials,) array of orientations
    """
    clf = LogisticRegression(multi_class='multinomial', max_iter=1000)
    scores = cross_val_score(clf, spike_data, stimuli, cv=cv, scoring='accuracy')
    return np.mean(scores), np.std(scores)

# Get population activity for each brain area
brain_areas = session.units['brain_area'].unique()
decoding_results = {}

for area in brain_areas:
    # Get units in this area
    area_units = session.units[session.units['brain_area'] == area].index
    
    # Prepare trial data
    stim_table = session.get_stimulus_table('drifting_gratings')
    spike_times = session.spike_times
    
    spike_matrix = []
    orientations = []
    
    for idx, trial in stim_table.iterrows():
        start = trial['start_time']
        end = trial['stop_time']
        
        trial_spikes = []
        for unit_id in area_units:
            spikes = spike_times[unit_id]
            count = np.sum((spikes >= start) & (spikes <= end))
            trial_spikes.append(count)
        
        spike_matrix.append(trial_spikes)
        orientations.append(trial['orientation'])
    
    spike_matrix = np.array(spike_matrix)
    orientations = np.array(orientations)
    
    # Decode
    accuracy, std = decode_orientation(spike_matrix, orientations)
    decoding_results[area] = {'accuracy': accuracy, 'std': std, 'n_units': len(area_units)}

# Summary
result_df = pd.DataFrame(decoding_results).T
print(result_df)
```

### Deliverable
- `results/figures/04_decoding_accuracy.png` - Cross-area comparison
- `results/data/population_coding.csv` - Decoding accuracies per area

**Progress: ⬜ Not started**

---

## Week 5: Synthesis & Bridge to Transcriptomics (May 29-31)

### Learning Outcomes
- Synthesize findings into narrative
- Identify candidate neurons for transcriptomics
- Create publication-quality figures
- Plan next research steps

### Tasks
- [ ] Write 2-page summary of findings
- [ ] Create final figure set (3-4 high-quality plots)
- [ ] Identify "high-value" neurons (high information content, selective)
- [ ] Create candidate list with metadata
- [ ] Plan how to target these neurons for sequencing

### Code Template

```python
# Create summary figure
fig = plt.figure(figsize=(16, 10))

# 1. Example tuning curves (top left)
ax1 = plt.subplot(2, 3, 1)
# Plot best tuning curve...

# 2. OSI distribution (top middle)
ax2 = plt.subplot(2, 3, 2)
ax2.hist(osi_values, bins=30, color='steelblue', edgecolor='black')
ax2.set_xlabel('Orientation Selectivity Index')
ax2.set_ylabel('Number of Units')

# 3. Decoding accuracy (top right)
ax3 = plt.subplot(2, 3, 3)
areas = list(decoding_results.keys())
accuracies = [decoding_results[a]['accuracy'] for a in areas]
ax3.bar(areas, accuracies, color='coral', edgecolor='black')
ax3.set_ylabel('Decoding Accuracy')
ax3.set_ylim([0, 1])

# Continue for lower panels...

plt.tight_layout()
plt.savefig('results/figures/05_summary_figure.png', dpi=300, bbox_inches='tight')
plt.close()

# Create candidate neuron list
candidates = pd.DataFrame({
    'unit_id': unit_ids,
    'brain_area': [session.units.loc[u, 'brain_area'] for u in unit_ids],
    'firing_rate': firing_rates,
    'osi': osi_values,
    'information_content': info_values,  # Computed earlier
    'preferred_orientation': pref_orientations
})

candidates = candidates.sort_values('information_content', ascending=False)
candidates.to_csv('results/data/high_value_neurons.csv', index=False)

print(f"Top 20 candidates identified:")
print(candidates.head(20))
```

### Deliverable
- `ANALYSIS_SUMMARY.md` - 2-page written summary
- `results/figures/05_summary_figure.png` - Publication-quality summary
- `results/data/high_value_neurons.csv` - Candidates for transcriptomics

**Progress: ⬜ Not started**

---

## Success Criteria by May 31

✅ Loaded and explored Allen Brain Observatory data  
✅ Computed firing rates and tuning curves  
✅ Quantified orientation selectivity  
✅ Performed population decoding  
✅ Identified candidate neurons for transcriptomics  
✅ 5+ publication-ready figures created  

---

## Integration with RNA-seq Learning

By end of May, you'll have:

**From neural coding:**
- High-information neurons identified
- Brain areas ranked by coding efficiency
- Cell types (from metadata) associated with best encoding

**From RNA-seq learning:**
- Complete Scanpy workflow mastery
- Understanding of single-cell data analysis

**June onwards:** Combine them!

---

## File Organization

```
neural-coding-visual-cortex/
├── data/
│   ├── ecephys_cache/           # Allen data
│   └── session_summary.csv      # Week 1 output
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_firing_rates.ipynb
│   ├── 03_tuning_analysis.ipynb
│   ├── 04_population_decoding.ipynb
│   └── 05_synthesis.ipynb
└── results/
    ├── figures/
    │   ├── 01_spike_raster.png
    │   ├── 02_tuning_curves_preliminary.png
    │   ├── 03_tuning_statistics.png
    │   ├── 04_decoding_accuracy.png
    │   └── 05_summary_figure.png
    └── data/
        ├── firing_rates_by_condition.csv
        ├── tuning_properties.csv
        ├── population_coding.csv
        └── high_value_neurons.csv
```

---

Good luck! You've got 36 days to make this work. 🧠⚡

