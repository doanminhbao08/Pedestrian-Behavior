# Shibuya Pedestrian Extraction - Minh Bao Doan

## Introduction
Hello! \
This folder ```/storage2/guccollab``` is used for extracting pedestrian data from Shibuya Live Camera using [YoLov5 model](https://github.com/ultralytics/yolov5?tab=readme-ov-file) . \
I will try to explain the code as clearly as I can, so I can save you a lot of time.

## Install
If you can access to this folder, I believe it is already enough. \
There is a folder ```venv1```, the virtual environment for this project, so you can activate it by running this code in terminal:
```
source venv1/bin/activate
```

## Single video process 
I will explain how you can use ```YoLov5 model``` to run a single video. <br> 

### Reduce video fps: <br>
I recieved a trimmed video from Alvin-sensei, ```trimmed.mp4```. <br>
The video is in "fps = 30", we should reduce it to "fps = 0.2" for less model processing time.
To do that, I use ```ffmpeg``` software by running this code in terminal:
```
ffmpeg -i trimmed.mp4 -r 0.2 trimmed_0.2fps.mp4
```
The file ```trimmed_0.2fps.mp4``` will appear.<br>
You can delete the current ```trimmed_0.2fps.mp4``` and try it yourself.

### Running YoLov5 model
To run this model, you have to access the folder by running this code in terminal:
```
cd /storage2/guccollab/bao/yolov5
```
And then run the model with the trimmed video in "fps =0.2":
```
python detect.py --weights yolov5x.pt --source /storage2/guccollab/trimmed_0.2fps.mp4 --save-csv
```
Now, the result will be in the folder ```/storage2/guccollab/bao/yolov5/runs/detect/exp```<br>
You can download the video ```trimmed_0.2fps.mp4``` in this folder to check the result.<br>
The file ```predictions.csv``` is what we want.<br>
Note: If we run the model again, in folder ```detect``` there will be folder ```exp2```. If you run one more time, there will be folder ```exp3``` and then ```exp4```, ```exp5```... This note will be useful for multi-video processing part.

### Extract data
Click in ```predictions.csv```, you can explore the data.
* The second column is detected object name
* The third column is confident of the model
* The forth column is the number of picture, or we can understan it as timestamp
I only want to keep the second and the forth column, so I running this code:
```
cd runs/detect/exp
cut -d',' -f2,4- predictions.csv > newfile0.csv
```
The file ```newfile0.csv``` is what we want.

## Multi-video processing 
In this part, I can only explain without indicating the exact step-by-step process due to enormous processing time.<br>
I had to process the videos in 2 folder: ```shibuyavids1``` and ```shibuyavids2```.<br>
To process, I do exactly the same as Sigle processing part but multi-video this time.<br>
To do that I run the shell script file ```script_train_1.sh```<br>
This will take a long time so I recommend you to ust ```tmux``` (Ask Alvin-sensei or ChatGPT)
It will reduce all the videos in ```shibuyavids1``` to "fps=0.2", the videos after reducing are in the folder ```/shibuyavids1/shibuyavids1_1fps```<br>
I run the model with these video, and crawl of the result into the folder ```shibuya1_result```, the ```.csv``` file here are the same as the file ```newfile0.csv``` in the Single processing part.<br>
The same procedure for  ```shibuyavids2```, ```script_train_2.sh```, ```/shibuyavids2/shibuyavids1_1fps``` and  ```shibuya2_result```.

## Merege the data for Multi-video processing 
After having, the ```.csv``` file above, we need to merge, and group by the data for analysis task.<br>
So I created the file ```merge.ipynb``` to merge the data.<br>
After running the file ```merge.ipynb```, you get the merged data in the file ```pedestrian.csv```
Note:
* The file ```merge.ipynb``` merge data by HOUR, you can adjust it if you want.<br>
Look at the second code box``` def data_explore```, you will find the line ```grouped_df['timestamp'] = round(grouped_df['timestamp(5s)'] /(720)) # per hour```.<br>
Our current fps is 0.2 => 5s per frame => 5*720 = 3600s = 1hour <br>
What you have to do is convert the group by time you want to second, divide it by 5 and substitute in the 720 current place<br>
If I want to merge data by DAY => 86400s => 86400/5 = 17280 => Substitute 720 with 17280<br>
```grouped_df['timestamp'] = round(grouped_df['timestamp(5s)'] /(17280)) # per day```<br>
But state that our data are recorded from 8am to 4pm.
* The video data aren't perfect, so I have to adjust some through the file ```merge.ipynb```<br>
I suggest you understand the code before applying this code to other data.
* The file ```merge.ipynb``` only extract data of pedestrian, you can adjust it to recieve other objects such as: car, umbrella, bus ...<br>
Pay attention to the line ```filtered_df = df[df['type'] == 'person']```

## Analyze the data
You can ask Alvin-sensei for weather data through ```.pickle``` file.<br>
After processing, I got the weather data in the file ```hourly_weather.csv``` and then I do some time-series analysis in the notebook ```analyze.ipynb``` if you want to refer.<br>

---
Goodluck!
Contact me if you need any help via email: doanminhbao08@gmail.com

