# Overview #

[![](/Images/Download-Button.png)](Image-Classification-with-PyTorch.tar.gz)

This examples runs a Python script for image classification with PyTorch. The following sections explain the parts of the script, `fmnist.py`.

# Prerequisites #

To run this script you should have PyTorch and other associated packages installed. We recommend that you set up a Python virtual environment in which you can install the packages. For an in-depth information on how to set up a virtual environment for Python 3.8+ see our [our online documentation](https://public.confluence.arizona.edu/display/UAHPC/Using+and+Installing+Python). You will also want to load the CUDA modules before you install PyTorch, so that PyTorch can use GPUs.

From an interactive session on El Gato:
``` console
[netid@gpu70 ~]$ module load python/3.8
[netid@gpu70 ~]$ module load cuda11/11.8 cuda11-dnn/8.9.2 cuda11-sdk/22.11
[netid@gpu70 ~]$ python3 -m venv --system-site-packages pyvenv
[netid@gpu70 ~]$ source pyvenv/bin/activate
(pyvenv) [netid@gpu70 ~]$ pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
(pyvenv) [netid@gpu70 ~]$ pip install torcheval
```

# Python script header #

``` python
#!/usr/bin/env python3

import time

import torch
import torch.nn as nn
from torchvision import datasets, models, transforms
from torch.utils.data import Dataset, DataLoader, random_split
from torcheval.metrics.functional import multiclass_accuracy
```

# The data #

In this example we look at the [Fashion-MNIST](https://github.com/zalandoresearch/fashion-mnist) dataset. Fashion-MNIST is a dataset consisting of a training set of 60000 examples and a test set of 10000 examples. Each example is a 28x28 grayscale image, associated with a label from 10 classes, each corresponding to a piece of clothing.

The following example shows how the data looks:
![fashion-mnist-data-example](fashion-mnist-sprite.png)

The labels are:
| Label | Description |
|-------|-------------|
| 0     | T-shirt/top |
| 1     | Trouser     |
| 2     | Pullover    |
| 3     | Dress       |
| 4     | Coat        |
| 5     | Sandal      |
| 6     | Shirt       |
| 7     | Sneaker     |
| 8     | Bag         |
| 9     | Ankle boot  |

The `datasets` module from PyTorch provides an API to download and transform the data. We will randomly select 20% of the training set to create a validation set. We will also resize the images, convert them to PyTorch tensors, and normalize them.

``` python
def get_dls(bs):
    root = "data/" # This is where the data will be downloaded
    dsets = datasets.FashionMNIST(root=root, download=True, train=True)
    
    train_set, valid_set = random_split(dsets, [0.8, 0.2])
    
    mean = dsets.data[train_set.indices].float().mean()
    std = dsets.data[train_set.indices].float().std()
    
    tfms = transforms.Compose([
        transforms.Resize((224, 224)),
        transforms.ToTensor(),
        transforms.Normalize((mean / 255,), (std / 255,))
    ])
    
    # Training and validation dataloaders which will load the data in batches.
    train_dl = DataLoader(
        DatasetFromSubset(train_set, transform=tfms),
        batch_size=bs,
        shuffle=True,
        **kwargs,
    )
    valid_dl = DataLoader(
        DatasetFromSubset(valid_set, transform=tfms),
        batch_size=2 * bs,
        shuffle=False,
        **kwargs,
    )
    
    # The test dataloader is created from the original test set. The model will not see
    # this data during training, we will use it for a final evaluation.
    test_dl = DataLoader(
        datasets.FashionMNIST(root=root, download=True, train=False, transform=tfms),
        batch_size=2 * bs,
        shuffle=True,
        **kwargs,
    )
    return train_dl, valid_dl, test_dl

# Helper class to create a PyTorch Dataset from a Subset
class DatasetFromSubset(Dataset):
    def __init__(self, subset, transform=None):
        super(Dataset, self).__init__()
        self.subset = subset
        self.transform = transform

    def __getitem__(self, index):
        x, y = self.subset[index]
        if self.transform:
            x = self.transform(x)
        return x, y

    def __len__(self):
        return len(self.subset)
```

# The model #

We will use the ResNet-18 model from [Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385). ResNet-18 is a 18 layers deep convolutional neural network (CNN). We will not implement this from scratch, rather we will use a version available with PyTorch that has been pretrained on the [IMAGENET](https://www.image-net.org/challenges/LSVRC/index.php) dataset, which has 1000 classes. This is an example of transfer learning.

By default the ResNet models expect three channel RGB images. We will modify the input layer so that it will accept our single channel grayscale images. We will also modify the output layer so that it outputs 10 classes instead of 1000.

``` python
def get_model():
    model = models.resnet18(weights=models.ResNet18_Weights.IMAGENET1K_V1)
    model.conv1 = nn.Conv2d(1, 64, kernel_size=7, stride=2, padding=3, bias=False)
    model.fc = nn.Linear(model.fc.in_features, 10)
    return model
```

# Training and testing #

We define the following two functions to train and test the model. We use the `.to()` method to load the model and data on the GPU.

We also turn on the cuDNN auto-tuner so that it may select the kernel with the best performance, to compute convolutions.

``` python
def fit(epochs, model, loss_func, opt, train_dl, valid_dl):
    start_time = time.time()
    for epoch in range(epochs):
        model.train()
        for data in train_dl:
            xb, yb = data[0].to(device), data[1].to(device)
            loss = loss_func(model(xb), yb)
            loss.backward()
            opt.step()
            opt.zero_grad()
        valid_loss, valid_acc = predict_stats(model, valid_dl)
        print(f"Epoch {epoch + 1}/{epochs}, Validation loss: {valid_loss:.4f}, Validation accuracy: {valid_acc:.4f}")
    print(f"Training time: {time.time() - start_time:.4f}s")
    return valid_loss, valid_acc
    
def predict_stats(model, dl):
    model.eval()
    if device == torch.device("cuda"):
        torch.cuda.empty_cache()
    with torch.no_grad():
        tot_loss = tot_acc = count = 0
        for data in dl:
            xb, yb = data[0].to(device), data[1].to(device)
            pred = model(xb)
            n = len(xb)
            count += n
            tot_loss += loss_func(pred, yb).item() * n
            tot_acc += multiclass_accuracy(pred, yb).item() * n
        return tot_loss / count, tot_acc / count


torch.backends.cudnn.benchmark = True
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = get_model().to(device)
opt = torch.optim.Adadelta(model.parameters())
loss_func = nn.CrossEntropyLoss()

# For faster and asynchronous memory copy to the GPU
kwargs = {"num_workers": 1, "pin_memory": True} if device==torch.device("cuda") else {}

train_dl, valid_dl, test_dl = get_dls()
epochs = 5

fit(epochs, model, loss_func, opt, train_dl, valid_dl)
test_loss, test_acc = predict_stats(model, test_dl)
print(f"Test loss: {test_loss:.4f}, Test accuracy: {test_acc:.4f}")
```

# Submission script #

``` bash
#!/bin/bash

#SBATCH --job-name=fashion-mnist-test-run
#SBATCH --time=00:30:00
#SBATCH --ntasks-per-node=1
#SBATCH --mem-per-cpu=6G
#SBATCH --partition=standard
#SBATCH --account=<YOUR_GROUP>
#SBATCH --gres=gpu:1

module load python/3.8
module load cuda11/11.8 cuda11-dnn/8.9.2 cuda11-sdk/22.11

source pyvenv/bin/activate

python3 -u fmnist.py
```

# Submit the job #

Submit the job on Ocelote.

``` console
[netid@junonia ~] sbatch fmnist.slurm
Submitted batch 2418647
```

# Output #

``` console
[netid@wentletrap ~] cat slurm-2417845.out
Epoch 1/5, Validation loss: 0.2412, Validation accuracy: 0.9153
Epoch 2/5, Validation loss: 0.2098, Validation accuracy: 0.9277
Epoch 3/5, Validation loss: 0.2232, Validation accuracy: 0.9230
Epoch 4/5, Validation loss: 0.1790, Validation accuracy: 0.9393
Epoch 5/5, Validation loss: 0.1955, Validation accuracy: 0.9359
Training time: 461.9267s
Test loss: 0.1955, Test accuracy: 0.9359
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=5&local_job_id=2417845 by 8am on 2023/09/01.
```

With just 5 epochs we got 93% accuracy on the test set. You can try image augmentation methods, changing the output layer of the model, larger models, etc to improve the accuracy. It takes around 8 minutes to train. You can try [tuning the performance](https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html) to improve the training time.
