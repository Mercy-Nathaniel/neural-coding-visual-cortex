# Neural Coding Project - Repository Structure

Organized by analysis stage and complexity.

```
neural-coding-visual-cortex/
│
├── README.md                    # Overview (already exists)
├── RESEARCH_TIMELINE.md         # 5-week research plan
├── PROJECT_STRUCTURE.md         # This file
│
├── data/                        # Data directory (in .gitignore for large files)
│   ├── allen_cache/             # AllenSDK cached data
│   └── processed/               # Preprocessed spike trains, tuning curves
│
├── notebooks/                   # Analysis notebooks (execute sequentially)
│   ├── 01_data_exploration.ipynb            # Load and explore ABO data
│   ├── 02_spike_train_basics.ipynb          # Extract and visualize spikes
│   ├── 03_firing_rate_analysis.ipynb        # Firing rate distributions
│   ├── 04_tuning_curves.ipynb               # Orientation tuning
│   ├── 05_selectivity_analysis.ipynb        # Selectivity indices
│   ├── 06_population_analysis.ipynb         # Population-level correlations
│   ├── 07_information_decoding.ipynb        # MI and decoding
│   ├── 08_comparative_analysis.ipynb        # Across cortical areas
│   └── 09_results_summary.ipynb             # Final synthesis
│
├── scripts/                     # Reusable analysis functions
│   ├── spike_analysis.py        # Spike train utilities
│   ├── tuning_analysis.py       # Tuning curve fitting
│   ├── population_analysis.py   # Population-level functions
│   ├── decoding.py              # Information & decoding
│   ├── allen_utils.py           # Allen Brain Observatory utilities
│   └── plotting_utils.py        # Consistent visualization
│
├── concepts/                    # Conceptual explanations
│   ├── 01_spike_trains.md
│   ├── 02_firing_rates.md
│   ├── 03_tuning_curves.md
│   ├── 04_selectivity.md
│   ├── 05_population_coding.md
│   ├── 06_information_theory.md
│   ├── 07_neural_decoding.md
│   └── 08_cortical_areas.md
│
├── results/                     # Analysis outputs (in .gitignore)
│   ├── figures/                 # Generated plots
│   ├── statistics/              # Numerical results
│   └── summaries/               # Analysis reports
│
├── resources/                   # References and documentation
│   ├── allen_brain_guide.md
│   ├── neuropixels_primer.md
│   ├── reference_papers.md
│   └── equations.md
│
├── requirements.txt             # Python dependencies
└── environment.yml              # Conda environment (optional)
```

---

## File Organization Guide

### Notebooks (in `notebooks/`)
- Sequential numbering for learning path
- Each focuses on one analysis stage
- Clear inputs/outputs documented
- Reproducible with comments

### Scripts (in `scripts/`)
- Reusable functions imported by notebooks
- Organized by analysis type
- Unit testable where possible
- Example usage in docstrings

### Concepts (in `concepts/`)
- Conceptual explanations (not code)
- Why methods are needed
- Interpretation guidance
- Key equations

---

## Data Workflow

```
Allen Brain Observatory
        ↓
Load session data (AllenSDK)
        ↓
Extract spike trains
        ↓
Compute firing rates & statistics
        ↓
Align to stimulus
        ↓
Fit tuning curves
        ↓
Compute selectivity indices
        ↓
Population analysis
        ↓
Information & decoding
        ↓
Compare across areas
        ↓
Results & visualization
```

---

## Key Analysis Outputs

| Week | Key Output | File |
|------|-----------|------|
| 1 | Spike visualization | `notebooks/02_spike_train_basics.ipynb` |
| 2 | Firing rate comparison | `notebooks/03_firing_rate_analysis.ipynb` |
| 3 | Tuning curves by area | `notebooks/05_selectivity_analysis.ipynb` |
| 4 | Decoding performance | `notebooks/07_information_decoding.ipynb` |
| 5 | Summary report | `notebooks/09_results_summary.ipynb` |

