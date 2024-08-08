Logo Ai

In a recent project, I developed an AI model that distinguishes between the Costco and Trader Joe's logos with remarkable accuracy. The process began by gathering a diverse dataset of logo images from both brands, ensuring that various file types, resolutions, and angles were represented. I then employed a convolutional neural network (CNN) to train the AI, focusing on the unique visual features of each logo, such as color schemes and typography. By leveraging advanced image processing techniques and fine-tuning the model with iterative testing, I successfully enabled the AI to identify subtle differences and reliably classify the logos. This project not only highlighted the power of machine learning in visual recognition but also demonstrated the potential for AI applications in brand analysis and marketing.

Do It Yourself

Read this below to get instructions for making this yourself. Feel free to do this with any kind of images and data, just make sure you change the variables! 

Training a Network
First, you will obtain the data needed to train a network, then you will run the training script to train the network.
Change directories into jetson-inference/python/training/classification/data
 
Run this command to download the dataset.  
wget https://nvidia.box.com/shared/static/o577zd8yp3lmxf5zhm38svrbrv45am3y.gz -O cat_dog.tar.gz

 
Run this command to unzip the file you downloaded. 
tar xvzf cat_dog.tar.gz

 
cd back to nvidia/jetson-inference/
You may get an error later saying you're out of memory. You can ensure your system overcommits memory which allows it to have more memory for the task by running this now. Run this in your terminal when you're in your jetson-inference directory:
  echo 1 | sudo tee /proc/sys/vm/overcommit_memory
 
 
Once that has run and you're still back in the jetson-inference folder, run ./docker/run.sh to run the docker container. (You may have to re-enter your nvidia password at this step)

 
From inside the Docker container, change directories so you are in jetson-inference/python/training/classification

 
Now you are ready to run your script.

Run the training script to re-train the network where the model-dir argument is where the model should be saved and where the data is. 
python3 train.py --model-dir=models/cat_dog data/cat_dog
 
You should immediately start to see output, but it will take a very long time to finish running. It could take hours depending on how many epochs you run for your model.
 
When running the model you can also specify the value of how many epochs and batch sizes you want to run. For example at the end of that code you can add:
--batch-size=NumberOfBatchFiles --workers=NumberOfWorkers --epochs=NumberOfEpochs
 

While it's running, you can stop it at any time using Ctl+C. You can also restart the training again later using the --resume and --epoch-start flags, so you don't need to wait for training to complete before testing out the model.
Run python3 train.py --help for more information about each option that's available for you to use, including other networks that you can try with the --arch flag.


You should have followed the steps above to commit more memory. If you didn't do that step and you get an error saying you are out of memory you can ensure your system overcommits memory which allows it to have more memory for the task. If you get an error like that run this in your terminal:  echo 1 | sudo tee /proc/sys/vm/overcommit_memory
Exporting the Network
To run your re-trained ResNet-18 model for testing you will need to convert it into ONNX format. ONNX is an open model format that supports many popular machine learning frameworks, and it simplifies the process of sharing models between tools. 
Pytorch comes with built-in support to do this, so follow these steps to export your model 
Make sure you are in the docker container and in jetson-inference/python/training/classification
 
Run the onnx export script.
python3 onnx_export.py --model-dir=models/cat_dog
 
Look in the jetson-inference/python/training/classification/models/cat_dog folder to see if there is a new model called resnet18.onnx there. That is your re-trained model!

Processing Images
In order to see how your network functions you can run images through them. You can use the imageNet command line arguments to test out your re-trained network.
Exit the docker container by pressing Ctl + D.
 
On your nano, navigate to the jetson-inference/python/training/classification directory.
 
Use ls models/cat_dog/ to make sure that the model is on the nano. You should see a file called resnet18.onnx.


 
Set the NET and DATASET variables
NET=models/cat_dog
DATASET=data/cat_dog
 
Run this command to see how it operates on an image from the cat folder.
imagenet.py --model=$NET/resnet18.onnx --input_blob=input_0 --output_blob=output_0 --labels=$DATASET/labels.txt $DATASET/test/cat/01.jpg cat.jpg
 
Open VS Code to view the image output. 
































