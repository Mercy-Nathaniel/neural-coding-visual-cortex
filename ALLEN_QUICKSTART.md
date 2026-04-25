# ALLEN_QUICKSTART.md - Allen Brain Observatory Guide

Getting started with the Allen Brain Observatory electrophysiology dataset for neural coding analysis.

---

## What is Allen Brain Observatory?

Large-scale Neuropixels recordings from awake, behaving mice viewing visual stimuli. Data includes:
- **Population recordings** from multiple brain areas simultaneously
- **Identified visual areas:** VISp, VISl, VISrl, VISam, VISpm
- **Stimulus types:** Drifting gratings, natural images, spontaneous activity
- **Cell type information** (available through separate database)

Official resource: https://allensdk.readthedocs.io/

---

## Installation

### 1. Install AllenSDK

```bash
pip install allensdk
```

### 2. Verify installation

```python
from allensdk.brain_observatory.ecephys.manifest import EcephysProjectCache
print("AllenSDK installed successfully!")
```

---

## Your First Dataset

### Step 1: Download Allen Cache

```python
from allensdk.brain_observatory.ecephys.manifest import EcephysProjectCache

# Creates ~/allensdk_cache/ directory
cache = EcephysProjectCache(manifest_file='ecephys_cache/manifest.json')

# Download metadata (required first)
sessions = cache.get_session_table()
print(f"Available sessions: {len(sessions)}")
print(sessions.head())
```

**Time to download:** ~2 minutes for metadata

### Step 2: Load Your First Session

```python
# Get a session with visual stimuli
session_id = sessions[sessions['has_drifting_gratings']].index[0]
session = cache.get_session_data(session_id)

print(f"Session loaded: {session_id}")
print(f"Duration: {session.session_start_time} to {session.session_stop_time}")
print(f"Units: {len(session.units)}")
```

### Step 3: Extract Spike Data

```python
import numpy as np

# Get spike times for all units
spike_times = session.spike_times
unit_ids = session.units.index.values

print(f"Total units recorded: {len(unit_ids)}")
print(f"First unit spike count: {len(spike_times[unit_ids[0]])}")

# Example: firing rate for first unit
spikes_unit_0 = spike_times[unit_ids[0]]
total_time = session.stimulus_timestamps[-1]
firing_rate = len(spikes_unit_0) / total_time

print(f"First unit firing rate: {firing_rate:.2f} Hz")
```

---

## Stimulus Information

### Drifting Gratings

```python
# Get stimulus data
stim_table = session.get_stimulus_table('drifting_gratings')
print(stim_table.head())

# Understand stimulus parameters:
# - orientation: 0-315 degrees
# - temporal_frequency: Hz
# - spatial_frequency: cycles/degree
# - phase: 0-360
```

### Extract Trial Data

```python
# For each trial, get spike times during stimulus window
trial_spike_counts = []

for idx, trial in stim_table.iterrows():
    start_time = trial['start_time']
    end_time = trial['stop_time']
    
    # Count spikes for first unit in this trial
    trial_spikes = spikes_unit_0[(spikes_unit_0 >= start_time) & (spikes_unit_0 <= end_time)]
    trial_spike_counts.append(len(trial_spikes))

print(f"Spike counts across trials: {trial_spike_counts[:10]}")
```

---

## Getting Unit Metadata

### Brain Area

```python
# Find which brain area each unit came from
unit_peak_channel = session.units[['brain_area']].head()

areas = unit_peak_channel['brain_area'].unique()
print(f"Brain areas in this session: {list(areas)}")

# Count units per area
for area in areas:
    count = (unit_peak_channel['brain_area'] == area).sum()
    print(f"{area}: {count} units")
```

### Cell Type (If Available)

```python
# Not all sessions have cell type info
# Check Allen's cell type database separately:
# https://celltypes.brain-map.org/
```

---

## Complete Minimal Example

```python
from allensdk.brain_observatory.ecephys.manifest import EcephysProjectCache
import numpy as np

# 1. Initialize cache
cache = EcephysProjectCache(manifest_file='ecephys_cache/manifest.json')

# 2. Get sessions
sessions = cache.get_session_table()
session_id = sessions[sessions['has_drifting_gratings']].index[0]

# 3. Load session
session = cache.get_session_data(session_id)

# 4. Get spike data
spike_times = session.spike_times
unit_ids = session.units.index.values

# 5. Get stimulus table
stim_table = session.get_stimulus_table('drifting_gratings')

# 6. Compute firing rate for first unit
unit_0_spikes = spike_times[unit_ids[0]]
total_time = session.stimulus_timestamps[-1]
firing_rate = len(unit_0_spikes) / total_time

print(f"✓ Loaded {len(unit_ids)} units")
print(f"✓ Unit 0 firing rate: {firing_rate:.2f} Hz")
print(f"✓ {len(stim_table)} drifting grating trials")
```

---

## Common Pitfalls

| Problem | Solution |
|---------|----------|
| "manifest.json not found" | Create directory: `mkdir -p ecephys_cache/` |
| Downloads are slow | Run at night; reuse cache once downloaded |
| Memory error on large sessions | Subset units using `session.units[session.units['brain_area']=='VISp']` |
| Spike times don't align | Always use `session.stimulus_timestamps` as reference |

---

## Data Organization

Recommended folder structure:

```
neural-coding-visual-cortex/
├── data/
│   ├── ecephys_cache/        # Allen data (auto-created)
│   └── session_metadata.csv  # Your summary of sessions
├── notebooks/
│   ├── 01_allen_loading.ipynb
│   ├── 02_firing_rates.ipynb
│   └── ...
└── results/
    ├── figures/
    └── data/
```

---

## Next Steps

1. **Run the minimal example** above
2. **Load 3-5 different sessions** and explore their properties
3. **Check RESEARCH_TIMELINE.md** for Week 1 tasks
4. **Document which sessions you'll analyze** in `data/session_metadata.csv`

---

## Useful Links

- AllenSDK docs: https://allensdk.readthedocs.io/
- Brain Observatory: https://brainobservatory.allen.org/web/static/visualization
- Paper: Harris et al. (2019) - "Functional properties of circuits in the visual cortex"

