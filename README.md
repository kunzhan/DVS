# DVS-driven Graph Diffusion Sampler (DVS)

# Dependencies

Please refer to the **Dependencies** in [README.md](../README.md) of the root directory.

We recommend using **Python 3.9.15** and **PyTorch 1.12.1**.

Install requirements:

```bash
pip install -r requirements.txt
conda install -c conda-forge graph-tool=2.45
conda install -c conda-forge rdkit=2023.03.2
```

## Running Experiments

### 1. Dataset preparations

We provide two **general graph datasets** (Planar, SBM) and two **molecular graph datasets** (QM9 and ZINC250k).

Download datasets and move them to the `data` directory:

- **Planar** (`planar_64_200.pt`)
  https://drive.google.com/drive/folders/13esonTpioCzUAYBmPyeLSjXlDoemXXQB?usp=sharing
- **SBM** (`sbm_200.pt`)
  https://drive.google.com/drive/folders/1imzwi4a0cpVvE_Vyiwl7JCtkr13hv9Da?usp=sharing

To generate general graph datasets:

```bash
python data/data_generators.py --dataset <dataset> --mmd
```

where `<dataset>` ∈ `{planar, sbm}`.

------

### Molecular Dataset Preprocessing

```bash
python data/preprocess.py --dataset <dataset>
python data/preprocess_for_nspdk.py --dataset <dataset>
```

where `<dataset>` ∈ `{qm9, zinc250k}`.

------

### ORCA Compilation (for evaluation)

```bash
cd evaluation/orca
g++ -O2 -std=c++11 -o orca orca.cpp
```

------

### 2. Configurations

All configurations are provided in `config/*.yaml`.

**DVS-related parameters**

- `ref`: controls the **reference curvature**, which determines the baseline scale for adaptive step sizes.

-  `gamma`: controls the **adaptive strength / feedback intensity** of the sampler.

Both parameters are defined under the `sample` section in the config file and directly influence the behavior of the **DVS adaptive step-size controller**.

------

## 3. Training

```bash
CUDA_VISIBLE_DEVICES=0,1,2,3 python main.py --type train --config <dataset> --seed 42
```

 **Note:**

The above training process corresponds to the **original GruM graph diffusion model**. **DVS does not modify training** . It is a **plug-and-play sampling strategy** applied only during inference.

---

## 4. Generation and Evaluation

```bash
CUDA_VISIBLE_DEVICES=0,1,2,3 python main.py --type sample --config <dataset>
```

------

## 5. Pretrained Checkpoints

Download and place in `checkpoints/<dataset>`:

- Planar:
  https://drive.google.com/drive/folders/16lTBrYEqUncuck7k9YDxWuNjTM_PZ4vl?usp=sharing
- SBM:
  https://drive.google.com/drive/folders/1XXcmcexRgGou-DPrbs8LgGWUUAdnnu34?usp=sharing
- QM9:
  https://drive.google.com/drive/folders/1RokFFheV648c23KWt3tngh_ZFO3uYe1-?usp=sharing
- ZINC250k:
  https://drive.google.com/drive/folders/1-W0z3xQEz9To_ewJtLutEjU4SAvDZnAn?usp=sharing

------

## 6. Generated Results

Generated graphs and molecules are provided in:

```sh
generated_graphs/
```