# Waste-classification project 

we use both fundamental and YOLO model with pytorch to try to classify organic waste and anorganic waste. 
this project is inspired by Computer vision with python created by freecodecamp.org 

# How this repo works 
photos in data/  ->  split into train/val/test  ->  train a model  ->  evaluate  ->  run on a photo or webcam

You get the same pipeline twice, built two different ways:

	1_pytorch_classification.ipynb	2_yolo_classification.ipynb
Model	EfficientNet-B0 (ImageNet weights)	YOLO26n-cls (Ultralytics)
Training loop	Written by hand: forward, loss, backward, step	One call: model.train()
Augmentation, early stopping	Written by hand, so you can see how it works	Built in
Good for	Understanding what's going on under the hood	Getting results fast with little code

Both use transfer learning: the model has already learned to "see" from millions of ImageNet photos, and we only teach it our trash classes. That's why a few hundred photos per class is enough to get started.

Repo layout
.
├── 1_pytorch_classification.ipynb
├── 2_yolo_classification.ipynb
├── .gitignore
└── data/
    ├── organik/      <- organic trash photos (fruit peels, leaves, leftovers...)
    ├── anorganik/    <- inorganic trash photos (plastic bottles, cans, bags...)
    └── kosong/       <- "empty" photos: your camera's view with NO trash

Each folder only has a .gitkeep so Git keeps it. The notebooks create these folders for you if they're missing.

Generated while you run the notebooks (all git-ignored, none of it gets pushed):

Folder	What's in it	Created by
models/	Your trained models (pytorch_sampah.pt, yolo_sampah.pt)	both
runs/	YOLO charts and logs	YOLO notebook
datasets/	The train/val/test copy that YOLO reads	YOLO notebook
Quick start
1. Add your photos

Drop photos into the three folders under data/. File names don't matter, and JPG and PNG both work.

Aim for 100+ photos per class. Variety (angles, lighting, backgrounds) matters more than quantity.
Photograph the trash in the same place and with the same camera you'll use later. The model learns from those exact conditions.
Fill kosong/ with photos of that spot with no trash. This is what lets the model say "nothing here". If you skip it, the notebooks still run with just two classes.
Any class with fewer than 10 photos gets skipped, and you need at least 2 classes with photos.
2. Run a notebook

On your laptop

Clone the repo and open the folder in VS Code (or Jupyter).
Pick a Python 3.11+ kernel.
Run the cells from top to bottom. The first cell installs the libraries.

On Google Colab (free GPU)

Upload this whole folder to Google Drive at MyDrive/klasifikasi-sampah-notebook/.
Open a notebook from Drive, then choose Runtime -> Change runtime type -> T4 GPU.
Choose Runtime -> Run all.

On Colab the notebooks read your photos from Drive and save models and charts back to Drive, so nothing is lost when the session ends. If you put the folder somewhere else, change DRIVE_FOLDER in the configuration cell.

3. Try it live (laptop only)

In the last cell, set RUN_WEBCAM = True and run it. Press Q in the video window to stop. It's off by default so Run all doesn't get stuck waiting on a camera.

What each notebook does

Both notebooks follow the same order, one # heading per step with a short explanation.

Step	What happens
Install & imports	Sets up the libraries and picks the fastest device (GPU if you have one)
Configuration	Every setting lives in one CONFIGURATION dictionary
Check photos	Counts photos per class and skips broken files
Split	70% train, 20% validation, 10% test, with the same class balance in each
Look at the data	Shows a grid of training photos so you can spot wrong labels
Model & training	Starts from pretrained weights and learns your classes. It stops early if validation stops improving
Training curves	Plots loss and accuracy per epoch
Evaluate	Test-set accuracy, precision, recall, F1 and a confusion matrix
See predictions	Test photos with real vs predicted label (green = right, red = wrong)
Output rule	Turns probabilities into the table at the top of this README
Single photo & webcam	Runs the model through OpenCV (cv2)
The output rule

The model always gives a probability for every class. Two things decide whether it stays silent:

the top guess is kosong, or
the top probability is below CONF_THRESHOLD (default 0.6).

Otherwise it prints organik or anorganik. On the webcam it prints only when the result changes, so the terminal doesn't fill up with the same word 30 times a second.

Settings you'll probably touch

Everything is in the CONFIGURATION cell near the top of each notebook.

Setting	Default	When to change it
N_EPOCHS	15 (PyTorch) / 30 (YOLO)	Raise it if the loss is still dropping at the end
BATCH_SIZE	32	Lower it (16 or 8) if you run out of memory
CONF_THRESHOLD	0.6	Raise it if you get false alarms, lower it if trash gets missed
CLASS_NAMES	organik, anorganik, kosong	Only if you want different classes
SPLIT	(0.7, 0.2, 0.1)	If you have very few photos
MODEL (YOLO)	yolo26n-cls.pt	yolo26s-cls.pt is more accurate but slower
Reading the results
Both losses go down together: healthy.
Train loss drops but validation loss goes up: overfitting. The model is memorizing, so add more varied photos.
High test accuracy but it fails on your webcam: your training photos don't look like your camera's view. Add photos taken with that camera.
Empty table reads as "organik" or "anorganik": add more photos to kosong/, or raise CONF_THRESHOLD.
Precision: when it says "organik", how often is it right?
Recall: out of all the real organic photos, how many did it catch?
Confusion matrix: rows are the real class, columns are the guess. Anything off the diagonal is a mistake.
Troubleshooting
Problem	Fix
Need at least 2 classes with photos	Add photos to at least two folders under data/, then rerun the check cell
Webcam window doesn't open	Try cv2.VideoCapture(1), and on macOS allow camera access for VS Code or Terminal
Webcam doesn't work on Colab	Expected, because Colab can't reach your camera. Download the model and run the webcam cell on your laptop
Out-of-memory error	Lower BATCH_SIZE
Training is very slow	You're on CPU. Use Colab with a T4 GPU
YOLO prints requirement ['pi-heif'] not found	Normal. Ultralytics installs its own extras
Photos from an iPhone aren't picked up	.heic isn't supported. Convert to JPG first
Using a trained model later

Models are saved in models/ (on Colab, in your Drive). To use one in a new session without retraining, run the "Load the saved model later" cell, then the single-photo or webcam cell. If you trained on Colab, download the .pt file into your laptop's models/ folder first.

Tech

Python 3.11+, PyTorch, torchvision, Ultralytics YOLO26, OpenCV, scikit-learn, matplotlib. Library versions are pinned in each notebook's install cell.

Ultralytics is released under AGPL-3.0. That's fine for learning and personal projects, but check its license before using it in a closed-source product.

4 langkah sebelumnya
Cek versi library & dataset Kaggle
Tulis kode project (config, dataset, train, eval, infer)
Notebook Colab & README
Uji end-to-end dengan dataset kecil
Tulis 2 notebook (PyTorch & YOLO)
Uji notebook dari atas ke bawah
Readme
MD
Klasifikasi sampah notebook
ZIP
Klasifikasi sampah
ZIP
Unggahan
1791628929152_image.png, 1791628754380_image.png
Pencarian web
