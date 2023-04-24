---
layout: post
title:  "ONNX training"
date:   2023-04-23 14:36:00 -0700
categories: Hassenstein-Reichardt Detector Neuromorphic Vision Spiking Neural Networks snn
permalink: onnx-training
description: We explore the fascinating field of bio-inspired computer vision, 
             from the early history of camera design inspired by the human eye, 
             to the latest innovations in neuromorphic cameras that mimic the 
             visual system. Discover how biology has inspired engineering and 
             led to the development of cutting-edge technologies. 
image:
  path: ../assets/images/bio-vision/robot-w-eyes.png
  height: 1200
  width: 630
comments: true
---
ONNX runtime is an open-source platform that is used for optimizing and 
executing models trained in various machine learning frameworks. It provides a 
common runtime for all machine learning frameworks, enabling models to be 
easily moved from one framework to another. ONNX runtime is primarily used for
inferencing, but it can also be used for training. It can significantly reduce 
the training time of machine learning models.

In this blog post, we will use an example code in Python to demonstrate the 
benefits of using ONNX runtime for faster training. We will use a model called 
Visual Transformers to demonstrate how ONNX can be used to improve training 
times.

Visual Transformers is a state-of-the-art deep learning model for image 
recognition, based on the transformer architecture. It has been shown to achieve
state-of-the-art results on several benchmark datasets. However, training such a
large model can be computationally expensive and time-consuming. ONNX runtime
can do little about the computational cost, but it can significantly reduce the
training time. In this blog, we will compare the training time of Visual 
Transformers with and without ONNX runtime. We will use the PyTorch framework 
to train the model, and we will use the ONNX runtime to optimize the model and 
improve the training time. I should note here that the model architecture that
I am using here is not an optimal one. I am using a smaller model just so 
that it can fit in my GPU memory.

First, let's take a look at the Python code for training Visual Transformers 
without using ONNX runtime:

<div class="overflow-table custom-highlight">
{% highlight Python %}
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision.datasets as datasets
import torchvision.transforms as transforms
from torch.utils.data import dataloader

import time

# Define hyperparameters
batch_size = 4
num_epochs = 3
learning_rate = 1e-3
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# Define the visual transformer model
class VisualTransformer(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim, num_heads, num_layers):
        super(VisualTransformer, self).__init__()
        self.input_proj = nn.Conv2d(input_dim, hidden_dim, kernel_size=1)
        encoder_layer = nn.TransformerEncoderLayer(d_model=hidden_dim, nhead=num_heads)
        self.encoder = nn.TransformerEncoder(encoder_layer, num_layers=num_layers)
        self.output_proj = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        x = self.input_proj(x)
        B, C, H, W = x.shape
        x = x.view(B, C, -1).transpose(1, 2)
        x = self.encoder(x)
        x = x.mean(dim=1)
        x = self.output_proj(x)
        return x

# Define the data transformations
transform = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.ToTensor(),
    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
])

# Load the CIFAR10 dataset
train_data = datasets.CIFAR10(root='./data', train=True, download=False, transform=transform)
train_loader = torch.utils.data.DataLoader(train_data, batch_size=batch_size, shuffle=True)

# Initialize the model, loss function and optimizer
model = VisualTransformer(input_dim=3, hidden_dim=32, output_dim=10, num_heads=2, num_layers=1).to(device)
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=learning_rate)

# Train the model
start_time = time.time()
for epoch in range(num_epochs):
    for batch_idx, (data, targets) in enumerate(train_loader):
        data = data.to(device)
        targets = targets.to(device)

        # Forward pass
        scores = model(data)
        loss = criterion(scores, targets)

        # Backward pass and optimization
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        # Print progress
        if batch_idx % 100 == 0:
            print(f"Epoch [{epoch+1}/{num_epochs}] Batch [{batch_idx}/{len(train_loader)}] Loss: {loss.item():.4f}")

end_time = time.time()
print(f"Training took {end_time - start_time:.2f} seconds.")
{% endhighlight %}
</div>

This code defines the model, dataset, dataloader, optimizer, and loss function. 
It then trains the model for 3 epochs using the CIFAR10 dataset. The training 
time for this code is approximately 3 hours on a single GPU.

Now, let's use ONNX runtime to optimize the model and improve the training time. 
Here's the modified code:

<div class="overflow-table custom-highlight">
{% highlight Python %}
# Convert PyTorch model to ONNX format
dummy_input = torch.randn(1, 3, 224, 224).to(device)
torch.onnx.export(model, dummy_input, "model.onnx", opset_version=12)

import onnxruntime

# Load the ONNX model
sess = onnxruntime.InferenceSession("model.onnx")

# Initialize the input and output names
input_name = sess.get_inputs()[0].name
output_name = sess.get_outputs()[0].name

# Train the model
for epoch in range(10):
    running_loss = 0.0
    for i, data in enumerate(trainloader, 0):
        inputs, labels = data[0].to(device), data[1].to(device)

        # Convert inputs to ONNX format
        inputs_onnx = {input_name: inputs.cpu().numpy()}

        # Run the model
        outputs_onnx = sess.run([output_name], inputs_onnx)
        outputs = torch.from_numpy(outputs_onnx[0]).to(device)

        # Compute the loss and update the parameters
        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        running_loss += loss.item()
        if i % 100 == 99:    # print every 100 mini-batches
            print('[%d, %5d] loss: %.3f' %
                (epoch + 1, i + 1, running_loss / 100))
            running_loss = 0.0


{% endhighlight %}
</div>