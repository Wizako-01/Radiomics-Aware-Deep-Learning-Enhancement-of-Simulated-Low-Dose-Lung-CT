# Radiomics-Aware Deep Learning Enhancement of Simulated Low-Dose Lung CT

## Project Overview

This project investigates the effects of deep learning-based CT image enhancement on quantitative lung tumour imaging features, with particular emphasis on preserving tumour phenotypic characteristics during reconstruction.

Deep learning methods can improve the visual quality of degraded CT images. However, improvements in image fidelity do not necessarily guarantee preservation of quantitative image features used in radiomics.

This study explores whether incorporating feature-preservation objectives into a deep learning reconstruction model can improve tumour feature stability.

**Current status:** Preliminary experimental study.

## Research Objectives

1. Simulate image degradation representing increased noise in lung CT.
2. Develop a conventional U-Net model for CT image enhancement.
3. Develop a modified U-Net incorporating a tumour feature-preservation loss.
4. Quantify changes in tumour intensity and texture characteristics following enhancement.
5. Compare conventional and feature-preserving models in terms of reconstruction accuracy and feature stability.

## Dataset

**Medical Segmentation Decathlon — Lung Tumor Segmentation (Task06)**

- Imaging modality: Computed tomography (CT)
- Anatomical region: Lung
- Cases analyzed: 63
- Image format: NIfTI
- Annotations: Corresponding 3D tumour segmentation masks

Dataset source:

https://www.kaggle.com/datasets/jadhavrajveer/medical-decathlon-lung-tumor-segmentation

Original dataset:

https://medicaldecathlon.com/

The dataset is not redistributed in this repository. Users should consult the original data licence and attribution requirements.

## Methodology

### 1. Data preprocessing

CT images and corresponding tumour segmentation masks are loaded and checked for spatial consistency.

CT intensity values are clipped and normalized before model training.

### 2. Simulated low-dose image generation

Synthetic noise is introduced into the reference CT images to generate degraded inputs.

This procedure approximates selected image-noise characteristics but does not reproduce the complete physical process of reduced-dose CT acquisition.

### 3. Conventional reconstruction model

A two-dimensional U-Net is trained to reconstruct reference CT images from synthetically degraded images.

Mean squared error (MSE) is used as the reconstruction objective.

### 4. Feature-preserving reconstruction model

A modified U-Net incorporates an additional loss term that penalizes differences in selected tumour intensity statistics and local image gradients.

The combined objective is: 
$$
\mathcal{L}_{\mathrm{total}} =
\mathcal{L}_{\mathrm{MSE}} +
\lambda \mathcal{L}_{\mathrm{feature}}
$$

- $L_{\mathrm{MSE}}$ measures reconstruction error.
- $L_{\mathrm{feature}}$ represents the feature-preservation penalty.
- $\lambda$ controls the relative contribution of the feature-preservation objective.
The feature loss uses differentiable intensity and gradient statistics rather than the full standardized radiomic feature set.

### 5. Model evaluation

Models are evaluated using held-out patients.

The experiment measures:

- Tumour-region reconstruction error
- Peak signal-to-noise ratio (PSNR)
- Changes in tumour intensity distributions
- Histogram entropy
- Local intensity differences
- Selected tumour feature errors relative to reference CT

## Experimental Configuration

| Parameter | Value |
|---|---|
| Total cases | 63 |
| Training patients | 44 |
| Validation patients | 9 |
| Test patients | 10 |
| Architecture | 2D U-Net |
| Training epochs | 8 |
| Batch size | 16 |
| Patch size | 128 × 128 |
| Optimizer | Adam |
| Learning rate | 0.0002 |
| Random seed | 20261025 |
| Feature loss weight | 0.2 |

Patient-level splitting is used to reduce data leakage.

## Preliminary Results

Both models completed training and evaluation.

### Reconstruction performance

| Model | Mean test tumour-region PSNR |
|---|---:|
| Simulated low-dose input | 26.66 dB |
| Conventional U-Net | 51.24 dB |
| Feature-preserving U-Net | 41.04 dB |

The conventional reconstruction model achieved better image fidelity than the feature-preserving model.

### Feature preservation

The feature-preserving model did not outperform conventional reconstruction on the reported exploratory tumour intensity and texture measurements.

These results suggest that the initial feature-preservation strategy requires further optimization.

The findings should be interpreted as preliminary and hypothesis-generating.

## Reproducibility

The experimental pipeline includes:

- Fixed random seeds
- Patient-level data partitioning
- Documented preprocessing
- Explicit synthetic noise parameters
- Saved model checkpoints
- Training history
- Patient-level evaluation results
- Exported feature measurements

### Running the notebook

1. Open the notebook in Google Colab.
2. Enable an NVIDIA GPU when available.
3. Run the setup and dataset-loading cells.
4. Verify image–mask pairing.
5. Execute preprocessing and model training.
6. Run evaluation and export the results.

The notebook downloads the public dataset using KaggleHub.

Execution time and package compatibility may vary across environments.

## Limitations

This preliminary implementation has several limitations:

1. The degraded CT images were generated using synthetic image-domain noise rather than physically validated low-dose CT simulation.
2. The reference images are not confirmed paired high-dose acquisitions.
3. The feature analysis uses exploratory measurements rather than a comprehensive IBSI-standardized radiomics pipeline.
4. The feature-preservation model was trained for only eight epochs.
5. The experiment uses a relatively small, single-source dataset.
6. External validation has not been performed.
7. The reconstruction models are research prototypes and are not intended for clinical use.

## Future Work

Planned methodological improvements include:

- Physically informed CT noise simulation
- Standardized radiomic feature extraction
- Improved differentiable feature-preservation losses
- Hyperparameter optimization and longer training
- Evaluation of radiomic feature stability using ICC and complementary agreement metrics
- Uncertainty quantification and external validation
- Investigation of trade-offs between image quality and radiomic feature preservation

## Research Significance

This project contributes to the investigation of quantitative image-feature stability following AI-based CT enhancement.

The broader objective is to develop reconstruction strategies that improve image quality while minimizing alterations to tumour characteristics that may be relevant to downstream quantitative imaging analysis.

## References

Antonelli, M., et al. (2022). The Medical Segmentation Decathlon. *Nature Communications*, 13, 4128.

https://doi.org/10.1038/s41467-022-30695-9

## Disclaimer

This project is intended exclusively for research and educational purposes.

The models have not been clinically validated and should not be used for patient diagnosis, treatment planning, or medical decision-making.
