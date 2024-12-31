# $C^2$ Hacks Project - Verdora

## Overview

The theme of the hackathon was sustainability, and our app was intended to be a one-stop solution for all things recycling, reselling, and compost. I worked on a feature that would allow a user to upload an image containing trash and immediately be presented with information about what things should or should not be recycled, which should be composted, and which items (such as clothing) can be listed on our reusable marketplace.

Due to time constraints, as well as the fact that this was just a proof-of-concept, I decided to reuse pre-trained computer vision models from Huggingface since several models already existed for sustainability-related tasks, in order to save compute on training the models, and not have to worry about picking the right architectures and hyperparameters.

Using pre-trained models is also a sustainable development practice in itself. Furthermore, even combining the output of all 7-8 models can be done on a CPU instance, which meant that our product could eventually also be able to perform inference locally on the users' machines.

## Files in the Repo

- `/images` contains the images we used to test/demo our app. Some were taken in real life, some were downloaded from the web.

- `/sample_outputs` contains some outputs of the object detection model, which were removed from the notebook to reduce the file size (since it wasn't being uploaded/displayed on GitHub correctly at 100+ MB).

- `verdora.ipynb` is the notebook I used for all my experimentation, downloaded straight from Google Colab. If running locally, more `pip install`s will be needed depending on your system. The original project repo (link in the repo description) contains a `requirements.txt` which could be helpful if you want to run locally.

- `verdora_microservice.py` contains the functions I wrote to be able to actually run inference using the pipeline I created after all the experimentation. The intention was to have one or two easy to use functions that could be imported and used in the API endpoint with a few lines.

## Models used

Object detection:

- YOLOv5s - object detection for 6 recycling/compost categories: **glass, cardboard, biodegradable, plastic, metal, paper**. The bounding boxes would be used to crop the images and further classify the types of recyclables.

- YOLOv8n - object detection for clothing in 3 categories: **clothing, accessories, shoes, bags**. This was used to detect what could be resold on the marketplace.

Classification:

These models were taken from https://huggingface.co/pyesonekyaw/recycletree_materials.

- 1 general classification model with 5 classes: **paper, plastic, glass, metal, others**. This was used to verify the output of the object detection labels, by cropping the input image using bounding boxes and checking if the classifier agrees. If the labels output by both models is the same, we go to the next step.

- 5 specific models from specifically to classify subcategories of waste depending on the label output by the above model.

For example: the plastic classification model had the following (sub)classes:
  - CD Disk, Straw, Plastic Bag, Clothes Hanger, Plastic Container or Bottle, Disposable Cutlery, Plastic Packaging, Plastic Packaging With Foil, Styrofoam

We also had **specific models for paper, plastic, glass, metal, others** where others was mostly E-waste. From the output of these models, as well as the helpful information provided in the GitHub repo which was mentioned on the HuggingFace page, we output specific recycling suggestions depending on the material.
