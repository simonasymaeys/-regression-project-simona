[README (1).md](https://github.com/user-attachments/files/33046978/README.1.md)
# Predicting AI Training Energy Consumption

An interpretable machine-learning project that estimates the electricity used to train AI models from model size, training compute, dataset size, and contextual information. The project uses the open **Epoch AI Data on AI Models** dataset and combines sustainability analysis with responsible AI-governance considerations.

## Research question

> To what extent can model size, training compute, training dataset size, and contextual characteristics predict the estimated energy consumption of AI model training?

The model is intended as an early planning and governance aid. It can help AI developers, sustainability teams, managers, and funders identify potentially energy-intensive training projects before training begins. It is not designed for exact electricity billing, carbon accounting, or regulatory compliance decisions.

## Why this matters

AI training can require substantial computing resources and electricity. However, reliable energy measurements are often unavailable during project planning. An early estimate can support:

- comparison of proposed training configurations;
- identification of projects requiring closer sustainability review;
- discussion of compute efficiency and resource allocation;
- more consistent documentation of environmental impacts; and
- evidence-informed AI governance.

## Dataset

The analysis uses the [Epoch AI Data on AI Models](https://epoch.ai/data/ai-models) collection.

- Original dataset: more than 3,600 AI models
- Models with sufficient information to estimate energy: 384
- Complete modelling dataset: 185 models
- Licence: [Creative Commons Attribution 4.0](https://creativecommons.org/licenses/by/4.0/)

The target is not directly reported. Estimated training energy is derived from training power draw and training time:

$$
\text{Estimated energy (kWh)} =
\frac{\text{power draw (W)} \times \text{training time (hours)}}{1000}
$$

Training time and power draw are excluded from the predictors because they are used to construct the target. Including either would create target leakage.

## Workflow

The notebook covers the complete regression workflow:

1. Problem framing using sustainability and stakeholder needs
2. Data validation, cleaning, and missing-value analysis
3. Complete-case and selection-bias assessment
4. Exploratory analysis and log transformations
5. Leakage-safe train/test splitting and preprocessing pipelines
6. Mean-prediction baseline
7. Linear, Ridge, Lasso, Random Forest, and Gradient Boosting models
8. Five-fold cross-validation and hyperparameter tuning
9. Residual and multicollinearity diagnostics
10. Technical and stakeholder-facing interpretation

## Model selection

Candidate models were compared using training-set cross-validation. Gradient Boosting achieved slightly stronger cross-validation performance, but Ridge regression was selected because its performance was close while its coefficients are substantially easier to interpret for governance stakeholders.

The held-out test set was reserved for the final evaluation and was not used to choose the model.

## Final results

The selected model is **Ridge regression with alpha = 3**.

| Metric | Test result |
|---|---:|
| R² | 0.829 |
| RMSE | 0.785 log₁₀ kWh |
| MAE | 0.570 log₁₀ kWh |
| Median multiplicative error | 2.41× |

The model explains approximately 83% of the variation in log-transformed estimated energy in the held-out data. The median error factor of 2.41× means it is more suitable for estimating the likely scale of electricity use than for producing an exact energy budget.

Training compute is the strongest model driver, followed by dataset size and parameter count. These relationships are predictive associations and should not be interpreted as causal effects.

## Project structure

```text
.
├── README.md
├── regression_capstone.ipynb
└── data/
    ├── ai_models_raw_epoch_ai.csv
    └── ai_training_energy_clean.csv
```

- `regression_capstone.ipynb` contains the fully executed analysis and narrative.
- `ai_models_raw_epoch_ai.csv` is the source dataset used in the project.
- `ai_training_energy_clean.csv` is the cleaned modelling dataset produced by the notebook.

## How to run

1. Clone or download this repository.
2. Create a Python environment.
3. Install the required packages:

```bash
pip install jupyter numpy pandas matplotlib seaborn scipy scikit-learn
```

4. Start Jupyter:

```bash
jupyter notebook
```

5. Open `regression_capstone.ipynb` and run the cells from top to bottom.

The notebook expects the two CSV files to remain inside the `data/` directory.

## Limitations and responsible use

- The target is an estimate derived from reported or estimated power draw and training duration, not independent electricity-meter data.
- Dataset-size reporting is incomplete, which reduces the usable sample and may introduce selection bias.
- The complete sample overrepresents larger, better-documented, and more energy-intensive models.
- The dataset is small relative to the diversity of AI systems and hardware configurations.
- Predictions for future architectures or models outside the observed range may be unreliable.
- Original-scale errors are strongly influenced by a small number of extremely large training runs.
- The model does not account fully for hardware efficiency, data-centre conditions, software optimisation, electricity mix, or operational overhead.

Predictions should therefore support human review and early sustainability screening. They should be validated against independently metered electricity consumption before operational deployment.

## Future improvements

- Validate predictions using directly metered energy data.
- Add prediction intervals for individual estimates.
- Expand coverage of smaller and less-documented AI systems.
- Incorporate hardware efficiency and data-centre characteristics.
- Monitor model performance as AI architectures and training practices evolve.

## Attribution

Dataset: Epoch AI, *Data on AI Models*, available at [epoch.ai/data/ai-models](https://epoch.ai/data/ai-models), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Author

**Simona Barbuceanu**  
BSc Artificial Intelligence & Sustainable Technologies — Machine Learning Impact Certificate capstone

