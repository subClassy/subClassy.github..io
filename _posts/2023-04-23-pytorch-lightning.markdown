---
layout: post
title:  "Getting Started with PyTorch Lightning"
date:   2023-04-23 06:51:00 -0700
categories: Deep Learning PyTorch Machine Learning Frameworks Computer Vision Neural Networks Model Training Lightning Module Best Practices Performance Optimization Data Science
permalink: pytorch-lightning
description: This blog provides an introduction to PyTorch Lightning, a 
             lightweight PyTorch wrapper that simplifies the process of training deep 
             learning models. Using a simple computer vision example, the blog 
             walks through the key features of PyTorch Lightning, as well as 
             best practices for performance optimization. 
comments: true
---

![PyTorch Lightning](../assets/images/pytorch-lightning/Lightning_Logo_v2.png "PyTorch-Lightning")

PyTorch Lightning is a popular open-source framework that provides a high-level 
interface for writing PyTorch code. It is designed to make the process of 
building, training, and deploying deep learning models faster, easier, and more 
scalable. It provides lightweight abstractions that allow you to focus on
building complex models without having to worry about the boilerplate code.

It is essentially a PyTorch wrapper that provides a standardized training loop, 
automatic batching, and easy distribution of work across multiple GPUs or nodes. 
It is designed to make PyTorch code more modular and easier to maintain by 
separating concerns such as data loading, model training, and validation. It 
also simplifies the process of training deep learning models on GPUs and 
multiple nodes.

#### PyTorch Lightning vs. Native PyTorch

Some advantages of using PyTorch Lightning over native PyTorch include 
standardization, simplification, reproducibility, and flexibility. Lightning 
provides a standardized interface for defining models, loading data, and 
training routines. This standardization makes it easier to collaborate with 
other researchers and reproduce experiments. It simplifies the process of 
training and testing models, automating common tasks such as data loading and 
checkpointing. This simplification makes it easier to focus on the core of the 
research, rather than the mechanics of the training process. PyTorch Lightning 
provides built-in support for reproducibility, including deterministic training,
automatic checkpointing, and early stopping. This makes it easier to ensure that
experiments can be reproduced and validated. Lightning is designed to be 
flexible, making it easy to experiment with different model architectures and 
data formats.

In addition to these advantages, PyTorch Lightning also allows you to train your
model on CPUs, GPUs, Multiple GPUs, or TPUs without changing a single line of 
your PyTorch code. This makes it easier to scale up your experiments and take 
advantage of more powerful hardware.

#### MNIST Demo

Now, let's demonstrate how to train a computer vision model using PyTorch 
Lightning.

##### Step 1: Install PyTorch Lightning
To use PyTorch Lightning, you first need to install it. You can install it 
using pip:

<div class="overflow-table custom-highlight">
{% highlight bash %}
pip install pytorch-lightning
{% endhighlight %}
</div>

##### Step 2: Import PyTorch Lightning and other dependencies
Once PyTorch Lightning is installed, you can import it along with other dependencies:

<div class="overflow-table custom-highlight">
{% highlight python %}
import torch
from torch import nn
from torch.nn import functional as F
from torch.utils.data import DataLoader, random_split
from torchvision.datasets import MNIST
from torchvision import transforms
from pytorch_lightning import LightningModule
from pytorch_lightning import Trainer
{% endhighlight %}
</div>

##### Step 3: Define the Model
Next, we need to define our model. In this example, I will use a simple 
convolutional neural network (CNN) that consists of two convolutional layers 
and two fully connected layers:

<div class="overflow-table custom-highlight">
{% highlight python %}
class LitModel(LightningModule):

    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(1, 32, 3, 1)
        self.conv2 = nn.Conv2d(32, 64, 3, 1)
        self.fc1 = nn.Linear(64 * 5 * 5, 128)
        self.fc2 = nn.Linear(128, 10)

    def forward(self, x):
        x = F.relu(self.conv1(x))
        x = F.max_pool2d(x, 2, 2)
        x = F.relu(self.conv2(x))
        x = F.max_pool2d(x, 2, 2)
        x = torch.flatten(x, 1)
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        
        return x
{% endhighlight %}
</div>

Here, I define a convolutional neural network with two convolutional layers 
followed by two fully connected layers. The first convolutional layer has 1 
input channel, 32 output channels, and a kernel size of 3x3. The second 
convolutional layer has 32 input channels, 64 output channels, and a kernel 
size of 3x3. I then flatten the output and pass it through two fully 
connected layers. The final output has 10 classes (0-9).

##### Step 4: Define the Training and Validation Datasets
Next, we need to define the training and validation datasets. I will use the 
MNIST dataset and split it into 50,000 training samples and 10,000 validation 
samples:

<div class="overflow-table custom-highlight">
{% highlight python %}
# define dataset transform
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.1307,), (0.3081,))
])

# prepare data
dataset = MNIST(root='data/', train=True, download=True, transform=transform)
train_set, val_set = random_split(dataset, [50000, 10000])
{% endhighlight %}
</div>

Here, I define a transform to normalize the dataset and apply it to the MNIST
dataset. I then split the dataset into training and validation sets using the 
*random_split* method.

##### Step 5: Define the Data Loaders
Once we have defined the training and validation datasets, we need to define 
the data loaders to load batches of data during training:

<div class="overflow-table custom-highlight">
{% highlight python %}
# prepare data loaders
train_loader = DataLoader(train_set, batch_size=64)
val_loader = DataLoader(val_set, batch_size=64)
{% endhighlight %}
</div>

Here, I define two data loaders for the training and validation datasets with 
a batch size of 64.

##### Step 6: Define the Training Loop
Next, we need to define the training loop using PyTorch Lightning:

<div class="overflow-table custom-highlight">
{% highlight python %}
class LitModel(LightningModule):

    ...

    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self(x)
        loss = F.cross_entropy(y_hat, y)
        self.log('train_loss', loss)
        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=1e-3)
{% endhighlight %}
</div>

Here, I define the *training_step* method that takes a batch of data and 
calculates the loss. I use the *F.cross_entropy* method to calculate the loss 
and the *self.log* method to log the loss during training. I also define the 
*configure_optimizers* method that returns an Adam optimizer with a learning 
rate of 1e-3.

##### Step 7: Define the Validation Loop
We also need to define the validation loop:

<div class="overflow-table custom-highlight">
{% highlight python %}
class LitModel(LightningModule):

    ...

    def validation_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self(x)
        loss = F.cross_entropy(y_hat, y)
        self.log('val_loss', loss)
        return loss
{% endhighlight %}
</div>

Here, I define the *validation_step* method that takes a batch of data and 
calculates the validation loss. I use the *self.log* method to log the loss 
during validation.

##### Step 8: Train the Model using PyTorch Lightning
Finally, we can train the model using PyTorch Lightning:

<div class="overflow-table custom-highlight">
{% highlight python %}
# initialize model
model = LitModel()

# initialize trainer
trainer = Trainer(max_epochs=10)

# train the model
trainer.fit(model, train_loader, val_loader)
{% endhighlight %}
</div>

Here, I first initialize the model and then initialize the trainer with a 
maximum of 10 epochs. I then train the model using the *fit* method with the 
training and validation data loaders.

The output of the training process is shown below:

| ![Training Output](../assets/images/pytorch-lightning/train_output.png "Figure A.1") |
|:--:|
| *Figure A.1* |

##### Step 9: Evaluate the Model
After training the model, I can evaluate its performance on the validation set:

<div class="overflow-table custom-highlight">
{% highlight python %}
# Evaluate on the validation set
trainer.validate(model, val_loader)
{% endhighlight %}
</div>

The output of the validation process is shown below:

| ![Validation Output](../assets/images/pytorch-lightning/val_output.png "Figure A.2") |
|:--:|
| *Figure A.2* |

#### Conclusion
In this blog post, I have demonstrated how to train a computer vision model 
using PyTorch Lightning. PyTorch Lightning is a powerful tool that simplifies 
the process of training deep learning models by abstracting away many of the 
low-level details. By using PyTorch Lightning, we can focus on the high-level 
aspects of model development and let the framework take care of the rest.