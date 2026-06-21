# AerialVLN Reproduction for CSIE 2103

This repository records my reproduction process for **AerialVLN: Vision-and-Language Navigation for UAVs** (ICCV 2023).  The goal of this project is not only to run the official code, but also to document the environment setup, data collection, data cleaning, training process, limitations, and future evaluation plan.

## Paper

- **Title:** AerialVLN: Vision-and-Language Navigation for UAVs
- **Venue:** IEEE/CVF International Conference on Computer Vision (ICCV), 2023
- **Task:** UAV vision-and-language navigation in a city-level 3D simulator
- **Input:** RGB image, depth observation, natural-language instruction, and previous action
- **Output:** one of 8 low-level UAV actions

## Reproduction Scope

This reproduction focuses on the official Seq2Seq baseline training pipeline.

Completed:

- Built the AirVLN / AirSim reproduction environment.
- Started the simulator server on port `30000`.
- Collected the full train split trajectories.
- Checked and cleaned action labels.
- Trained Seq2Seq baseline for **10 epochs**.
- Generated training loss curve and checkpoint files.

Not completed:

- Full validation evaluation on `val_seen` / `val_unseen`.
- Official metric comparison using NE, SR, OSR, and SDTW.
- CMA / LAG baseline reproduction.

## Dataset and Cleaning

The raw collected training data contained **16,386 samples**. During action-label consistency checking, 6 samples contained labels outside the current 8-action UAV action space. These samples were removed before training.

| Item | Count |
|---|---:|
| Raw collected samples | 16,386 |
| Invalid action-label samples | 6 |
| Clean training samples | 16,380 |

The final clean sample count, **16,380**, matches the train split size reported in the paper.

## Training Setting

| Item | Setting |
|---|---|
| Model | Seq2Seq baseline |
| Training mode | Teacher Forcing |
| Clean train samples | 16,380 |
| Epochs | 10 |
| Batch size | 1 |
| Learning rate | 0.00025 |
| Environment | Ubuntu server, AirVLN310 conda env |
| GPU | NVIDIA RTX 5090 server |

Important note: the paper text does not explicitly state the number of training epochs. In this reproduction, I trained for 10 epochs based on the official code workflow and course-report requirement.

## Results

The 10-epoch training completed successfully and generated the following checkpoints:

- `ckpt.4.pth`
- `ckpt.9.pth`
- `ckpt.LAST.pth`

The training log was parsed to generate a loss curve. The loss curve is used as training-process evidence. The original paper does not provide a training loss curve, so this plot is not used for direct paper comparison.

## Validation Evaluation Status

The original paper reports navigation metrics such as:

- NE: Navigation Error
- SR: Success Rate
- OSR: Oracle Success Rate
- SDTW: Success weighted DTW

I attempted a small validation run with `EVAL_DATASET=val_seen` and `EVAL_NUM=1`, but the evaluation process stopped at initialization and did not produce NE, SR, OSR, or SDTW. Therefore, this repository does **not** claim to reproduce the official Table 4 metric values yet.

## Suggested Repository Structure

```text
AerialVLN-Reproduction-CSIE2103/
├── README.md
├── scripts/
│   ├── make_clean_lmdb.py
│   ├── scan_invalid_actions.py
│   └── plot_loss_curve.py
├── results/
│   ├── train_loss_curve_10epochs.png
│   ├── data_cleaning_summary.png
│   └── screenshots/
├── logs/
│   └── AirVLN-seq2seq-pickle-clean_20260615-054747-786898.log
└── slides/
    └── AerialVLN_CSIE2103_final_template_style.pptx
```

## Large Files Policy

The following files are not uploaded to GitHub because they are too large:

- Collected LMDB features
- RGB / depth feature databases
- Checkpoint `.pth` files
- Full compressed result archive

If needed, these files should be stored on the lab server, Google Drive, or GitHub Releases / Git LFS.

## Future Work

- Fix the validation evaluation pipeline.
- Run `val_seen` and `val_unseen` evaluation.
- Compare NE, SR, OSR, and SDTW with the paper's Table 4.
- Reproduce stronger baselines such as CMA and LAG.
- Test cross-dataset generalization on AerialVLN-S.
