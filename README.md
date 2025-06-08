# Model Comparison

## Results Summary

| Model | Val/Test Acc | Parameters |
|-------|-------------|------------|
| LeNet | 63.89% | ~60K |
| AlexNet | 80.28% | ~61M |
| VGG16 | 87.63% | ~138M |
| VGG19 | 86.77% | ~143M |
| ResNet-50 | 84.20% | ~25.6M |
| ResNet-150 | 53.34% | ~60M |

## Inference

### Under-powered (LeNet)
- ~60K parameters therefore only ~64% accuracy
- Too few layers to capture CIFAR-10's diversity

### Over-parameterized (VGG16/19)
- ~140M parameters, ~87% accuracy
- Large capacity drives highest accuracy, but with very heavy compute and memory demands

### Sweet-spot (ResNet-50 & AlexNet)
- AlexNet (61M → 80%) and ResNet-50 (25.6M → 84%) hit a compromise:
  - ResNet's skip-connections lets us get close to VGG-level performance with ~⅙ the parameters
  - AlexNet's older design still manages respectable accuracy with moderate depth

### Diminishing returns (VGG19 vs. VGG16)
- Adding 5M params (VGG16 → VGG19) gives a slight drop (87.63 → 86.77%)
- Beyond a point, more depth/params adds cost but not accuracy

## Under- vs. Over-fitting Signals

### ResNet-150 under-fits
- Despite ~60M params, it only reached ~53%, train curves never got high
- Suggests optimization issues (too few epochs or sub-optimal LR for very deep nets)

### VGG16/19 moderate over-fit
- Train ≈ 99% vs. Validation ≈ 87% → 12% gap
- Dropout helps but we still see over-fit at later epochs

### AlexNet strong over-fit
- Train > 99% vs. Validation ≈ 80% → ~19% gap, indicating high capacity relative to CIFAR-10 size

## Generalization Strategies Used

### Dropout
Employed in LeNet and AlexNet to mitigate overfitting by randomly omitting activations and penalizing large weights. However, using a high dropout rate can sometimes hamper learning speed and leave model capacity underutilized.

### Data Augmentation
We enriched our training data with random flips, crops (with padding), and minor color shifts. This simple step substantially improved all architectures:
- Shallow nets like LeNet gained a couple of percentage points and showed tighter train-validation gaps
- Mid-size models (AlexNet, VGG) also saw accuracy boosts with less overfitting
- Even deep ResNets trained more effectively, learning richer features

### Learning Rate Scheduling
We've also used a learning rate scheduler to stabilize training.
