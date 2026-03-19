Protein Binding Pocket Prediction
Residue-level prediction of protein binding pockets using ESM protein language model embeddings and a feedforward neural network. 
Given a protein structure in PDB format, the model classifies each residue as binding pocket or non-binding pocket.

How It Works

Sequence extraction — amino acid sequences are parsed from PDB files using BioPython
ESM embedding — sequences are encoded with esm2_t6_8M_UR50D, producing per-residue embeddings (dim 320)
Feature construction — Cα coordinates (dim 3) are concatenated with ESM embeddings → 323-dim feature vector per residue
Classification — a small MLP (ImprovedPocketModel) predicts binding (1) vs non-binding (0) for each residue
Output — predictions are written to submission.csv in space-separated residue ID format

Model Architecture
Input (323) → Linear(512) → ReLU → Dropout(0.3)
            → Linear(256) → ReLU → Dropout(0.2)
            → Linear(128) → ReLU
            → Linear(2)

Loss: CrossEntropyLoss
Optimizer: Adam (lr=1e-3, weight_decay=1e-5)
Scheduler: ReduceLROnPlateau (patience=2, factor=0.5)
Metric: Jaccard score (IoU) on validation set


Output
submission.csv — one row per test protein:
idprediction
201A_12 A_13 B_7202
Empty prediction means no binding pocket residues were detected.
