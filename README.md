# TorchKit

TorchKit is a custom library for PyTorch neural networks, designed to simplify working with image data and tabular data (supervised learning). It provides Keras-like `compile` and `fit` methods, as well as features like learning rate scheduling, gradient clipping, and plotting of losses and accuracy. Additionally, it includes methods for evaluating metrics like accuracy, F1 score, precision, mean squared error (MSE), root mean squared error (RMSE), mean absolute error (MAE), and R² score.

## Features

- Learning rate scheduler
- Gradient clipping
- Plotting of losses and accuracy (for classification)
- Plotting of R² score (for regression)
- Metrics method to easily evaluate model performance

## Usage

1. **Inherit from TorchKit classes:**
   - When working with image data, inherit from `TorchKit.ImageClassifier.ImageModel`.
   - When working with tabular data, inherit from `TorchKit.TabularData.TabularModel`.

2. **Create an instance of your model:**

   ```python
   model = YourCustomModel()
   ```

3. **Compile the model:**

   ```python
   model.compile(
       loss_function=nn.CrossEntropyLoss(),
       optimizer=optim.Adam(model.parameters(), lr=0.001),
       learning_rate_scheduler=optim.lr_scheduler.StepLR(optimizer, step_size=5, gamma=0.1),
       gradient_clip=1.0
   )
   ```

   For `TabularModel`, you can specify the task (default is 'classification'):

   ```python
   model.compile(
       loss_function=nn.MSELoss(),
       optimizer=optim.Adam(model.parameters(), lr=0.001, weight_decay=1e-4),
       learning_rate_scheduler=optim.lr_scheduler.StepLR(optimizer, step_size=5, gamma=0.1),
       gradient_clip=1.0,
       task='regression'
   )
   ```

4. **Fit the model:**

   ```python
   history = model.fit(
       epochs=10,
       train_loader=train_loader,
       val_loader=val_loader
   )
   ```

   To visualize the loss, accuracy, and R² score plots, make sure to create a variable for the fit method:

   ```python
   history = model.fit(...)
   ```

5. **Make predictions:**

   ```python
   predictions = model.predict(data_loader)
   ```

6. **Evaluate metrics:**

   For `ImageModel` and `TabularModel` (classification):

   ```python
   accuracy, f1_score, precision = model.metrics(dataset)
   ```

   For `TabularModel` (regression):

   ```python
   mse, rmse, mae, r2_score = model.metrics(dataset)
   ```

## Example

Here's a complete example of using TorchKit for image classification:

```python
import torch
import torch.nn as nn
from TorchKit.ImageClassifier import ImageModel

class YourCustomModel(ImageModel):
    def __init__(self):
        super(YourCustomModel, self).__init__()
        self.conv1 = nn.Conv2d(3, 16, 3, 1)
        self.conv2 = nn.Conv2d(16, 32, 3, 1)
        self.fc1 = nn.Linear(32*6*6, 128)
        self.fc2 = nn.Linear(128, 10)

    def forward(self, x):
        x = self.conv1(x)
        x = nn.ReLU()(x)
        x = self.conv2(x)
        x = nn.ReLU()(x)
        x = nn.MaxPool2d(2)(x)
        x = torch.flatten(x, 1)
        x = self.fc1(x)
        x = nn.ReLU()(x)
        x = self.fc2(x)
        return x

model = YourCustomModel()
model.compile(
    loss_function=nn.CrossEntropyLoss(),
    optimizer=torch.optim.Adam(model.parameters(), lr=0.001),
    learning_rate_scheduler=torch.optim.lr_scheduler.OneCycleLR(optimizer, epocks=10, steps_per_epoch=len(train_loader), max_lr=0.001),
    gradient_clip=1.0
)
history = model.fit(
    epochs=10,
    train_loader=train_loader,
    val_loader=val_loader
)

model.plot_accuracies()
model.plot_losses()
```

## Dependencies

- PyTorch
- Matplotlib (for plotting)

