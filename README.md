# Using Graph Neural Networks for Site-of-Metabolism Prediction and its Applications to Ranking Promiscuous Enzymatic Products

This repository contains data and code for running the GNN-SOM models for site-of-metabolism prediction.

## Environment Setup

We recommend using [conda](https://docs.conda.io/en/latest/) for managing this environment. Our implementation requires the following software toolkits to be installed:
    * [PyTorch](https://pytorch.org/)
    * [PyTorch Geometric](https://pytorch-geometric.readthedocs.io/)
    * [RDKit](https://www.rdkit.org/)

## Project Structure

```
GNN-SOM/
├── GNN-SOM.ipynb          # Jupyter notebook demonstrating SOM prediction workflow
├── README.md
├── LICENCE.md
├── data/
│   ├── config.json        # Feature mappings and model architecture parameters
│   └── model0.pt ~ model9.pt  # Pre-trained model weights (10 models)
└── gnn_som/
    ├── __init__.py        # Model creation (createGnnSom) and weight loading (loadGnnSomState)
    └── MolFromKcf.py      # KEGG KCF format parser → RDKit molecule objects
```

## Model Weights

All pre-trained model weights are stored in the `data/` directory as PyTorch state dictionaries (`model0.pt` through `model9.pt`, ~208 MB total). These files are committed directly to the Git repository.

The project uses an **ensemble of 10 GNN models**, each trained on a different data split. Each model uses Chebyshev polynomial graph convolutions (`ChebConv`) with varying configurations:

| Model | Convolution | K | Width | Depth | Input Features |
|-------|-------------|---|-------|-------|----------------|
| model0 | ChebConv | 5 | 512 | 4 | 157 |
| model1 | ChebConv | 15 | 512 | 2 | 157 |
| model2 | ChebConv | 10 | 512 | 3 | 157 |
| model3 | ChebConv | 10 | 512 | 4 | 157 |
| model4 | ChebConv | 10 | 512 | 3 | 157 |
| model5 | ChebConv | 15 | 512 | 2 | 157 |
| model6 | ChebConv | 15 | 512 | 2 | 157 |
| model7 | ChebConv | 15 | 512 | 3 | 157 |
| model8 | ChebConv | 10 | 256 | 3 | 157 |
| model9 | ChebConv | 5 | 512 | 3 | 157 |

Each model architecture consists of:
- An input layer accepting 157 features (66 enzyme EC classes + 20 elements + 71 KEGG atom types)
- Multiple hidden layers with ReLU activation and 0.5 dropout
- A single output neuron producing a SOM prediction score

The 157 input features per atom are one-hot encoded from three categories:
- **Enzyme features** (66): EC classification combinations (e.g., 1.1, 1.2, ..., 6.6)
- **Element features** (20): Atom element type (C, N, O, S, P, etc.)
- **KCF type features** (71): KEGG atom type classifications (C1a, N1a, O1a, etc.)

## Usage

The example code for making SOM predictions on a given enzyme-molecule pair is presented as a Jupyter notebook, which can be found in [GNN-SOM.ipynb](GNN-SOM.ipynb). This notebook makes use of various commonly-used functions provided in the [gnn_som](gnn_som) directory as well as the model state and configuration files in the [data](data) directory.

## License

This project is licensed under the MIT license.
