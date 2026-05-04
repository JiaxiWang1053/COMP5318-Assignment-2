---
output:
  pdf_document: default
  html_document: default
---
# COMP5318 Assignment 2 Report Draft

## Data

### Data description and exploration

The dataset used in this assignment is PathMNIST, a histopathology image classification benchmark derived from pathology slide images. In this task, each sample is a small RGB image patch of size 28 x 28 pixels, and the goal is to classify each image into one of nine categories. The version provided for the assignment contains 32,000 training images and 8,000 test images. The image arrays are stored separately from the label arrays, where `X_train` and `X_test` contain image data and `y_train` and `y_test` contain the corresponding class labels.

An initial inspection of the data showed that the images are stored as `uint8` arrays with pixel values in the range `[0, 255]`. The training set shape is `(32000, 28, 28, 3)` and the test set shape is `(8000, 28, 28, 3)`, confirming that the task uses colour images rather than grayscale images. The label values range from `0` to `8`, so this is a nine-class classification problem.

The class distribution plot in [output/class_distribution.png](D:/Documents/5318/Assignment2/output/class_distribution.png) shows that the training data is balanced across all nine classes. This is helpful because it reduces the risk of a model being biased toward a majority class and makes accuracy a more meaningful comparison metric across models.

The sample image grid in [output/sample_images_by_class.png](D:/Documents/5318/Assignment2/output/sample_images_by_class.png) shows that the dataset consists of low-resolution pathology patches with a broadly similar pink and purple colour palette, which is consistent with stained histopathology imagery. Although the classes share similar global colour characteristics, they differ in local texture, tissue arrangement, density, and structural patterns. Some classes appear visually distinct, while others seem more similar and may therefore be harder to separate. This suggests that the classification task is likely to depend on subtle texture-level information rather than simple colour-based differences.

The raw pixel distribution in [output/pixel_distribution.png](D:/Documents/5318/Assignment2/output/pixel_distribution.png) confirms that the images occupy the standard 8-bit image range. This supports the use of numeric scaling before model training, since many machine learning algorithms and neural networks perform more stably when inputs are standardised to a smaller and consistent numeric range.

Overall, the exploration indicates that the dataset is cleanly structured and balanced, but the task remains challenging because the images are small and the differences between some classes are visually subtle. These observations motivated the choice of minimal but appropriate preprocessing to preserve image content while improving numerical suitability for downstream models.

### Pre-processing

The preprocessing in this assignment was intentionally kept simple and consistent with the observations from the exploratory analysis. Since the images are already aligned to a uniform size of 28 x 28 pixels and there was no evidence of missing values or malformed samples in the provided arrays, no resizing or sample filtering was applied at this stage. Instead, the main preprocessing step was pixel-value normalisation.

Each image was converted from `uint8` to `float32`, and its pixel values were scaled from `[0, 255]` to `[0, 1]`. This transformation preserves the visual structure of the tissue images while making the numeric inputs more suitable for optimisation-based learning algorithms, especially neural networks. The visual comparison in [output/preprocessing_comparison.png](D:/Documents/5318/Assignment2/output/preprocessing_comparison.png) shows that the normalised images look almost identical to the raw images. This is expected, because the transformation changes the numeric representation rather than the semantic image content.

In addition, the original training set was split into a new training subset and a validation subset using a stratified 80:20 split. Stratification was used to preserve the class balance in both subsets. This validation split is important for the later stages of the assignment, since it provides a principled way to tune hyperparameters without using the test set during model selection.

To support different downstream model types, two processed versions of the input data were saved. The first version keeps the original image structure in the shape `(N, 28, 28, 3)`, which is suitable for convolutional neural networks. The second version flattens each image into a one-dimensional vector of length `2352`, producing arrays of shape `(N, 2352)`, which are more suitable for multilayer perceptrons and many traditional machine learning models from scikit-learn. This separation helps keep the preprocessing reusable for multiple models while maintaining a single consistent starting point.

The processed datasets were saved under the [processed](D:/Documents/5318/Assignment2/processed) directory as:

- `X_train_img.npy`
- `X_val_img.npy`
- `X_test_img.npy`
- `X_train_flat.npy`
- `X_val_flat.npy`
- `X_test_flat.npy`
- `y_train.npy`
- `y_val.npy`
- `y_test.npy`

Possible additional preprocessing methods such as data augmentation, colour normalisation, or more advanced stain normalisation were not applied in this stage. This was a deliberate choice because the current goal was to establish a clean and consistent baseline preprocessing pipeline that can support fair comparison across multiple models. More aggressive preprocessing could be considered later if justified by model performance or specific architectural needs.
