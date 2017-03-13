
This is Project write up for CARND program of Udacitys Vehicle Detection.

Files to look in :
lesson_function.py has all the api needed .
P5Feat_classify.ipynb is the main file
p5_exploration.ipnb was used for exploartion and saving images.


In this project we start with ample images  from a combination of the GTI vehicle image database, the KITTI vision benchmark suite, Inital exploration showed there were 8000 images for both cars and no cars.
Out Of which I used just 3000 For training model.The images were .png images in training data.While images from video are .jpg so proper scaling of images was done in Process_Image to put .jpg images in range of 0 to 0.1.

The images of car and non car objects were detailed studied and supplied to model.
The various features of supplied to model were raw pixel,Histogram,Hog.Template matching was not considered.






Exploring Histogram Feature:
In lesson_function.py color_hist is used to explore the histogram of car and non car images.Histogram is similar for similar kind of objects.There could be false detections but is still better than regular image template
Look at Histogram of some car and non car objects.

Histogram of Car objects
<img src="output_images\colorhistcar.png">

Histogram of Non Car objects
<img src="output_images\colorhistnotcar1.png">




Exploring Color Space:
The image when view transformed and viewed under HSV,LUV,YCrCb and analysed for car and non car objects carries information which can be extracted by model.These specs can be used Inidiviually or combined .Look at the graphs below which shows the Saturation HSV and YCrCb transforms carry a good information which is used to form feature vector and fed to mode.

Color Space for Car Images
<img src="output_images\carRGBHSV1.png">
<img src="output_images\carRGBHSV2.png">

Color Space for Not Car Images
<img src="output_images\notcar1.png">
<img src="output_images\notcar2.png">


The Raw Images and Color Spaces together are used to form vectors as spatially binned vector.Looking at these I have used HSV and YrCrCb. And reized the image to use raw pixel information without loading the Feature vector size

Spatial Binned  for Car Images

<img src="output_images\ALL.png">
<img src="output_images\ALL2.png">

Spatialbinned for Not Car Images


<img src="output_images\allNotCar.png">
<img src="output_images\allNotCar2.png">


Exploring Hog:
    Gradient is a very important feature of image which gives us the shape info.HOG is nothing but histogram of gradient.In Hog feature the Histogram gradients orientation is used to construct the shape of image.Its generic signature for a class of objects like car.The HOG Feature has following features to tweak:
    
CellSize:The image gradient is broken in grids of smaller sqaures like 8*8(was used in prj) over which we compute the histogram of gradients and the Hog Function computes the direction of gradient for that grid.
Block:Is the combination of few Cells over which we normalize the HOG(2*2 cells block was used)
Orienation: This stands for the nos of bins over which the histogram is taken over for each cell.A choice of 8 orientaion was made.These vote of histogram decides the orienation of the cell.


HOG  for Car Images

<img src="output_images\hogcar.png">


HOG for Not Car Images


<img src="output_images\hognotcar.png">


    

We use all these features together to form a single vector.extract_features fuction does the Job of getting combined vector.
StandardScaler function is used to scale combined vactors and X_scaler.transform(X) sckit function uses the the same type of scaling as done on training images to test images coming from the video.
Well we are ready then split the data into training and  test data and fed the Linear SVC model.


Linear SVC is Support Vector Machines. It is a simple classifier and as name suggest Linear draws a linear hyper plane between Car and Not car vectors. It comes under supervised learning as we need to provide training Data for the model to learn . 

Sliding Search Window:
Now the image of size to search was reduced to image of 1280,256. As the Car objects were visible only in this range.This would reduce our false positives.Sliding window search was implemented .Where this reduced image was searched in small cell of 100*100  and a overlap of0.5 used. These features have to be otimally used as every Cell will be again checked over for features and ultimately nos of searches will load the system and make it slow.

SLiding windows on test Image

<img src="output_images\drawbox on testimage.png">


Test Image cropped to reduce False positives
<img src="output_images\cropedImage.png">

Applying the SVM model on single image using SearchWindow function.

<img src="output_images\drawwindowstestimg.png">

Using Find Cars function to search the image for car like objects and bound the predicted object


<img src="output_images\Hog sub sampling.png">

As we can see there are false positives. Now we are going to assign labels and use HeatMap and thresholding to reduce the False Positives

<img src="output_images\HeatLabel.png">


All Done now we are ready to run our Pipeline and process our video images .
Here in Process_Image function, we search the Image for cars and label them draw boxes.
To reduce false Positives. we compute Boxes over 10 frames and apply a higher threshold to reduce the False detection.
This can be still enhance by checking over consecutive frames if detections are Positive 
<img src="output_images\frame06.jpeg"> 

Here is the link to video: 


  
  <video width="320" height="240" controls>
  <source src="/output_images/out_project_video_subclip.mp4" type="video/mp4">
  </video>

Well the project was good for understanding classifiers and Pattern detection in same class of objects.
Most of the job was stitching up of code given in our lessons.
The challange was smoothing the transition over frames . Drawing the
Detection over the car object exactly and using scaling windows .Scaling the search also can be done improve results.
There are false detction which can be eliminated by using better averaging over frames.Having a class to hold boxes over each frame. Compare consecutive frames and add filters. Making it more robust than this
