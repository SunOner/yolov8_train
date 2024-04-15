# Example of model training
- The tutorial will be updated gradually in the future.

## Requirements
- Make sure you have python (version >= 3.11.6) and ultralytics package installed.

## Unpacking the repository
- Download (Green button download/download zip) and unzip the archive to a place convenient for you, so that everything is in one folder.

## Image Annotation / Dataset / tips
- There are many different programs and services for annotating images, but if you are doing this for the first time, then use Labelimg.
- First download [Labelimg](https://github.com/HumanSignal/labelImg).
- Unzip the program and transfer the file predefined_classes.txt (The file is located in the repository) to the labelimg/data folder.
- The repository already contains examples of annotated images. `yolov8_train\datasets\game`.
	- `images` - This folder contains images that are used for training the model.
	- `labels` - This folder contains annotations for the corresponding images. These are text files that list the coordinates of the bounding boxes and the class IDs of objects detected in the images.
	- `val` - This folder is used for storing images and labels for the validation dataset. The validation dataset is used to check the model's efficiency during training, to ensure that the model generalizes well and does not overfit on the training data.
- After opening the labelimg, click Open Dir and select the path to the images to train the model. (`yolov8_train\datasets\game\images`).
- Next, click Change Save Dir to select where your annotations will be saved. (`yolov8_train\datasets\game\labels`).
- You can read how to use the program [here](https://github.com/HumanSignal/labelImg?tab=readme-ov-file#steps-yolo).
- (Optionaly) How to achieve good results in training a model, you can read [this lesson](https://docs.ultralytics.com/yolov5/tutorials/tips_for_best_training_results/).
- In short, annotate from 500 to 2500 images, as well as use empty images so that there are no players, weapons, fire on them. For example, just upload a large number of images with trees, chairs, grass, objects that look like people, empty locations from games and move these images to the dataset.
- The more complex the game looks for AI (for example, cs2 is a more formulaic game than battlefield 2042), the more data you will need to train the model (You may need at least 5000-10000 images).
- Images can be in 4k resolution and 100x100 pixels.
- Ready-made datasets can be found [here](https://universe.roboflow.com/).
- Also, do not forget to add images and annotated files to the val folder, the same rules apply for this folder as for the train folder. There may be few images for validation, approximately if you have 1000 images for training, take and add 10 images for validation.
- Pay attention to the game.yaml file.
	- An example dataset is called game, the file can be renamed to the name of your project.
	- In the file you will find values such as:
		- path: game (The name of your dataset)
		- train: images (Folder with images)
		- val: val (Folder with images for validation)
		- test: (Folder with images for testing, In this example, it's empty)
- To find out the ultralytics settings file, type `yolo settings` in the command line.

## Training the model
- (Optionaly) You can find everything about the training [here](https://docs.ultralytics.com/modes/train/).
- After you have finished annotating the dataset, go to the yolov8_train folder with the `cd` command.
- First, choose an pretrained model. There are differences in them.
	- YOLOv8n (The fastest and stupidest. It is not demanding of resources at all)
	- YOLOv8s (Just fast, and not so stupid. More demanding of resources)
	- YOLOv8m (Currently the smartest for real-time work. A video card more powerful than the rtx 2060 is needed.)
	- YOLOv8l and YOLOv8x (They are not suitable for our case, the most intelligent and voracious for detection.)
- Let's choose YOLOv8n.
- Now you need to choose the image size that the model will work with. The lower the resolution, the faster the model will be trained and the fewer objects the model will detect.
- Choose between 320, 480 and 640. Let's choose 320.
- Now let's choose how many epochs the model will be trained. The larger the dataset, the more epochs it will take to learn. Do not overdo it, many epochs can also affect the retraining of the model, keep an eye on the indicators during training in the future. Let's imagine that we have set 40 epochs.
- (optional) All training options are described [here](https://docs.ultralytics.com/modes/train/#train-settings)
- Now let's enter a command for training. `yolo detect train data=game.yaml model=yolov8n.pt epochs=40 imgsz=320`
- After successful training, go to the runs/detect/train/weights folder.
- You will see 2 files.
	- best.pt (This is the file with the best training, that is, we select it.)
	- last.pt (This file is like a checkpoint, if the training has stopped in any way, then you will be able to continue training from the era where you left off. Type `yolo train resume model=runs/detect/train/weights/last.pt`)
- To test the model for performance, write little program.
- Create `test.py` file.
- Write:
```python
from ultralytics import YOLO

model = YOLO('./runs/detect/train/weights/best.pt')

# Define source as YouTube video URL
source = 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'

# Run inference on the source
results = model(source, stream=True)  # generator of Results objects
```
- And run it `python test.py`