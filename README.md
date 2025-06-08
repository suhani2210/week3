# Model Comparison

## Results Summary

| Model | Val/Test Acc | Parameters |
|-------|-------------|------------|
| LeNet | 63.89% | ~60K |
| ANN  | 53.34%  |~1.6M  |
| AlexNet | 80.28% | ~61M |
| VGG16 | 87.63% | ~138M |
| VGG19 | 86.77% | ~143M |
| ResNet-50 | 84.20% | ~25.6M |
| ResNet-150 | 83.43% | ~58M |

## Inference

### Under-powered (LeNet)
- ~60K parameters therefore only ~64% accuracy
- Too few layers to capture CIFAR-10's diversity
  
### Basic Architecture (ANN)
- ~1.71M parameters → only ~53% accuracy
- Too shallow to capture CIFAR-10’s visual complexity
- Lacks convolutional layers, leading to poor feature extraction and class confusion

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

### ResNet size vs CIFAR-10 accuracy (ResNet-50 vs. ResNet-152)
- ResNet-152 on CIFAR-10 is prone to overfitting because the model is very large for such a small dataset; adding dropout before the final layer can help reduce overfitting and improve test accuracy by a small margin (1–3%).
- Smaller models like ResNet-18 (~11M params) with augmentation often outperform larger ones, offering better accuracy and faster training.

## Under- vs. Over-fitting Signals

### ResNet-150 over-fits
- Train ≈ 96% vs. Test Accuracy = 83% → 13% gap
- Likely due to the small dataset size and large model 

### VGG16/19 moderate over-fit
- Train ≈ 99% vs. Validation ≈ 87% → 12% gap
- Dropout helps but we still see over-fit at later epochs

### AlexNet strong over-fit
- Train > 99% vs. Validation ≈ 80% → ~19% gap, indicating high capacity relative to CIFAR-10 size

### ANN strong over-fit
-  Train Acc: 70.22% vs Test Accuracy: 53.34%, 

## Generalization Strategies Used

### Dropout
Employed in LeNet and AlexNet to mitigate overfitting by randomly omitting activations and penalizing large weights. However, using a high dropout rate can sometimes hamper learning speed and leave model capacity underutilized.


### Data Augmentation
We enriched our training data with random flips, crops (with padding), and minor color shifts. This simple step substantially improved all architectures:
- Shallow nets like LeNet gained a couple of percentage points and showed tighter train-validation gaps
- Mid-size models (AlexNet, VGG) also saw accuracy boosts with less overfitting
- Even deep ResNets trained more effectively, learning richer features and benefiting from regularization like dropout to reduce overfitting.
- For fully connected ANNs, augmentation helped indirectly by providing more varied input, but their limited capacity makes convolutional architectures like ResNet-152 better suited for image tasks.
   

### Learning Rate Scheduling
We've also used a learning rate scheduler to stabilize training.

## Optimizer and Fine-tuning Strategy

### ResNet-152
- We modified the optimizer to use different learning rates for the final classification layer and the earlier pretrained layers—using a higher learning rate for the final layer to speed up adaptation and a lower rate for the backbone to preserve learned features.
- Freezing the earlier layers severely hurt accuracy, indicating the model benefited from fine-tuning the full network rather than training only the classifier.

## Comparison based on confusion matrix 

### ResNet-152
- Higher Overall Accuracy:
The diagonal values in the confusion matrix for ResNet-152 are generally higher than those of simpler models, indicating it correctly classifies more samples overall.
- Reduced False Positives:
Columns in the confusion matrix for ResNet-152 have lower sums outside the diagonal compared to simpler models, meaning it less frequently mislabels other classes as a given class.
- Improved Class-wise Recognition:
ResNet-152 shows fewer off-diagonal misclassifications, suggesting better feature extraction and representation for distinguishing between similar classes.
- Lower False Negatives:
Row-wise off-diagonal sums are smaller, indicating ResNet-152 misses fewer true samples for each class, showing higher recall.

