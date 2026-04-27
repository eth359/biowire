# BioWire 🧠⚡

> **Biologically constrained spiking neural network simulation of the *Drosophila melanogaster* optic lobe, built from the FlyWire electron-microscopy connectome.**

[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue)](https://www.python.org/)
[![Brian2](https://img.shields.io/badge/simulator-Brian2-green)](https://brian2.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-brightgreen)](CHANGELOG.md)

---

## Overview

BioWire simulates the **left medulla (ME_L)** of the *Drosophila* optic lobe using a conductance-based leaky integrate-and-fire (LIF) network driven by real connectome data from [codex.flywire.ai](https://codex.flywire.ai).

### Key Features

- **FlyWire connectome integration** — real synapse counts from codex.flywire.ai
- **Offline / mock mode** — synthetic connectome auto-generated when data files are absent
- **E/I classification** — 30+ neurotransmitter-typed inhibitory types (DOI-cited)
- **4-bit signed weight quantisation** — memory-efficient sparse matrices
- **Parameter optimisation** — grid search calibrated to in-vivo electrophysiology
- **Three-layer negative control** — rewired / shuffled / random topology comparison

---

## Pipeline

| Step | Description |
|------|-------------|
| 1 | Install dependencies & write `environment.yml` |
| 2 | Load FlyWire connectome (or generate mock) |
| 3 | Extract ME_L circuit, build weight matrices |
| 4 | E/I classification + 4-bit weight quantisation |
| 5 | Network topology analysis |
| 6 | Visualise signed weight matrices |
| 7 | Define conductance-based LIF model (Brian2) |
| 8 | Core 8-type circuit simulation |
| 9 | Full 100-type + mid-tier 20-type simulation |
| 10 | Stimulus–response visualisation + directional tuning |
| 11 | Electrophysiology comparison (sim vs. in-vivo) |
| 12 | Three-layer negative control (Hamming distance) |

---

## Quick Start

### Option A — Conda (recommended)

```bash
conda env create -f environment.yml
conda activate biowire
jupyter notebook biowire_v1_.ipynb
```

### Option B — pip

```bash
pip install -r requirements.txt
jupyter notebook biowire_v1_.ipynb
```

> **Note:** Python 3.10+ is required.

---

## Data

The notebook runs in **two modes**:

| Mode | Behaviour |
|------|-----------|
| **Real data** | Download the two files below and place them in the project root |
| **Offline / mock** | If files are absent, a synthetic connectome is auto-generated |

Files to download from [codex.flywire.ai/api/download](https://codex.flywire.ai/api/download):

- `consolidated_cell_types.csv.gz`
- `connections_princeton_no_threshold.csv.gz`

These files are excluded from the repository via `.gitignore` due to their size.

---

## Model Architecture

| Component | Implementation |
|-----------|----------------|
| Connectome source | FlyWire ME_L (Dorkenwald et al. 2024) |
| Weight format | scipy.sparse CSR → 4-bit signed integer |
| Simulator | Brian2, conductance-based LIF, dt = 0.5 ms |
| E/I classification | 30+ inhibitory types (DOI-cited) |
| Parameter search | Grid search calibrated to in-vivo ephys |
| Negative control | Degree-preserving rewire · weight shuffle · random |
| Visualisation | Dark-background matplotlib, 6 figures |

### Neuron Models

Three circuit tiers are simulated:

| Model | Neuron types | Purpose |
|-------|-------------|---------|
| Core | 8 | Canonical motion circuit (L1, L2, L3, L5, Mi1, Mi4, C3, Tm1) |
| Mid | 20 | Extended medulla coverage |
| Full | 100 | Full ME_L representation |

---

## Simulation Results (v1.0.0)

Results obtained with the real FlyWire connectome, random seed = 42:

| Metric | Value |
|--------|-------|
| Neuron types modelled | 100 (ME_L) |
| Synaptic edges | 308 |
| Calibration (ephys target) | **5 / 8 neurons in range** |
| Grid search best score | 14.80 |
| Best params | G_on=0.33, G_off=0.15, W_scale=0.06 |
| DSI (ON-only vs OFF-only) | 1.00 |
| Null stimulus silence | ✅ confirmed |
| Negative control (Random Hamming) | 0.003 ± 0.004, p = 0.057 (n.s.) |

---

## E/I Classification

Inhibitory types are sourced from:

| Neurotransmitter | Neurons | Reference |
|-----------------|---------|-----------|
| GABA | C2, C3, Mi9, Dm1–Dm14, Am, Pm1/2, Lai, aMe12 | Takemura et al. 2017; Eckstein et al. 2024 |
| Glutamate (inh.) | L4, Tm4, Tm9, Tm20, TmY4/5a/10, Lawf1/2 | Shinomiya et al. 2019 |
| Acetylcholine | L1, L2, L3, L5, Mi1, Mi4, T4*, T5*, R* | Excitatory (default) |

---

## References

- Clark et al. (2011). *Defining the computational structure of the motion detector in Drosophila.* Neuron. [doi:10.1016/j.neuron.2011.06.004](https://doi.org/10.1016/j.neuron.2011.06.004)
- Takemura et al. (2017). *Connectome of the fly visual circuitry.* eLife. [doi:10.7554/eLife.24394](https://doi.org/10.7554/eLife.24394)
- Strother et al. (2017). *The emergence of directional selectivity in the visual motion pathway of Drosophila.* Neuron. [doi:10.1016/j.neuron.2017.07.025](https://doi.org/10.1016/j.neuron.2017.07.025)
- Shinomiya et al. (2019). *Comparisons between the ON- and OFF-edge motion pathways in the Drosophila visual system.* eLife. [doi:10.7554/eLife.37833](https://doi.org/10.7554/eLife.37833)
- Maisak et al. (2013). *A directional tuning map of Drosophila elementary motion detectors.* Nature. [doi:10.1038/nature12320](https://doi.org/10.1038/nature12320)
- Eckstein et al. (2024). *Neurotransmitter classification from electron microscopy images at synaptic sites in Drosophila.* Nature. [doi:10.1038/s41586-024-07558-y](https://doi.org/10.1038/s41586-024-07558-y)
- Dorkenwald et al. (2024). *FlyWire: online community for whole-brain connectomics.* Nature Methods. [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Nern et al. (2015). *Quantitative neuroanatomy for connectomics in Drosophila.* eLife. [doi:10.7554/eLife.04693](https://doi.org/10.7554/eLife.04693)

---

## Author

Erdem B.

## License

MIT License — see [LICENSE](LICENSE) for details.
