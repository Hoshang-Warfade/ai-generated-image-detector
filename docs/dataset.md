# Dataset Plan

## 1. Dataset

**Dataset:** GenImage  
**Official source:** [https://github.com/GenImage-Dataset/GenImage](https://github.com/GenImage-Dataset/GenImage)  
**Access source currently used for experimentation:** Hugging Face community conversion: `nebula/GenImage-arrow`

GenImage contains real images and AI-generated images from multiple generators, including Stable Diffusion, ADM, BigGAN, GLIDE, Midjourney, VQDM, and others.

The official GenImage dataset is distributed for non-commercial use under its stated license terms. The dataset itself will not be committed to this repository.

---

## 2. Why This Dataset Was Chosen

This project needs more than a random real-vs-fake split.

GenImage is useful because it contains images from multiple generation methods. This lets us evaluate two different questions:

1. How well does the model perform on unseen images from the same generator it was trained on?
2. How well does the model generalize to images from a different generator that was not used during training?

This second evaluation is important because an AI-image detector may learn generator-specific artifacts instead of features that generalize across generators.

---

## 3. Initial Generator Choice

The initial baseline will use **Stable Diffusion 1.4** as the training generator.

Reasons:

- It provides a controlled starting point for the baseline experiment.
- Training on one generator makes cross-generator evaluation easier to interpret.
- It keeps the initial data preparation and training scope manageable within the project deadline.
- A second generator can then be used as a truly unseen evaluation distribution.

Stable Diffusion 1.4 is not assumed to represent all modern AI-image generators.

---

## 4. Proposed Dataset Split

### Stable Diffusion 1.4

| Split | Real Images | AI-Generated Images | Total |
|---|---:|---:|---:|
| Train | 2000 | 2000 | 4000 |
| Validation | 500 | 500 | 1000 |
| Same-generator test | 500 | 500 | 1000 |

### ADM

| Split | Real Images | AI-Generated Images | Total |
|---|---:|---:|---:|
| Cross-generator test | 500 | 500 | 1000 |

The exact sample counts may be adjusted if dataset access, duplication, or compute constraints make the proposed split unsuitable.

---

## 5. Purpose of Each Split

### Training Set

Used to optimize the model parameters.

### Validation Set

Used during model development for decisions such as:

- model selection
- threshold selection
- limited fine-tuning decisions

The validation set will not be used for model training.

### Same-Generator Test Set

Contains unseen Stable Diffusion 1.4 examples.

This measures how well the model generalizes to new samples from approximately the same generator distribution.

### Cross-Generator Test Set

Contains ADM-generated images that are not used during training.

This evaluates whether the detector generalizes beyond the generator used for training.

---

## 6. Class Labels

The Hugging Face conversion currently exposes:

- `0` = real
- `1` = fake / AI-generated

The dataset also provides a generator field identifying the generator-specific configuration.

---

## 7. Data Access Strategy

The original GenImage Google Drive distribution uses very large multipart archives, which are not practical for this project's time and storage constraints.

For initial experimentation, a Hugging Face community-hosted Arrow conversion is being used.

The dataset can be accessed using Hugging Face's `datasets` library with streaming enabled.

Streaming allows samples to be read progressively without requiring the entire dataset to be downloaded before inspection.

A small access test confirmed that both real and generated Stable Diffusion 1.4 examples can be retrieved successfully.

---

## 8. Sampling Strategy

The streamed dataset is ordered rather than randomly mixed.

For example, the first inspected samples all belonged to the real class.

Therefore, the project will not simply take the first N samples.

Instead, samples will be selected explicitly by class to create balanced subsets.

Before training, the selected samples will be shuffled.

---

## 9. Data Quality and Leakage Checks

Before finalizing the splits, the following checks will be performed:

- class counts
- exact duplicate detection
- image dimensions
- file formats
- possible overlap between real-image subsets
- train/validation/test separation
- generator metadata consistency

Exact duplicate checks will be performed to prevent the same image from appearing across train, validation, or test sets.

Real-image overlap between generator-specific subsets will also be checked before using ADM as the cross-generator evaluation set.

---

## 10. Known Limitations

- The initial training set is a small subset of the full GenImage dataset.
- Training on Stable Diffusion 1.4 does not make the detector universal.
- ADM is only one unseen generator and does not represent every possible generation system.
- The Hugging Face dataset is a community-hosted conversion, so its contents and metadata should be verified against the official GenImage source.
- Performance on this dataset should not be interpreted as proof that the model can determine the true origin of any arbitrary image.

---

## 11. Current Status

- GenImage official dataset access verified.
- Original archives found to be too large for practical use in this project.
- Hugging Face GenImage Arrow conversion inspected.
- Streaming access from Kaggle verified.
- Label mapping verified.
- Real and fake Stable Diffusion 1.4 samples successfully retrieved.
- Final subset creation and duplicate checks are still pending.