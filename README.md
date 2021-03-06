## Introduction

In this project, we mainly focus on the detection of learners' mind-wandering during watching lecture videos. Since mind-wandering may have negative effects on learners' learning performance in video watching. Previous studies show that there are some relationships between mind-wandering and gaze movements. However, most of these studies are based on specific eye tracking devices, which are expensive. It is not applicable for average learners using them in MOOCs. Therefore, in our project, we investigate whether we can detect learners' mind-wandering based on webcam-based eye tracking.

In this page, we first introduce our experiment settings. The experiment settings include our participants, hardware, software and the Web application in the experiments. Then we release the data about participants' mind-wandering reports and the raw gaze data collected by a professional eye tracker and a webcam. After a brief introduction of the dataset, the experiment results and classifier parameters are introduced, which are included in our papers partially due to the space limitation. In the last part, we introduce the scripts used in our project for data processing and mind-wandering detection.

For detail content about our project, please check [our paper](https://github.com/Yue-ZHAO/MWDET_Project/blob/master/ECTEL_2017_mindwandering.pdf)

## Experiment Settings

### Participants
We recruited our study participants (six females, seven males, all with a computer science background) through an internal mailing list and did not pay them. Six of them wore glasses or contact lenses.

### Hardware and Software
We used two eye-tracking devices in the study, a high-quality one as a reference and a low-quality webcam. Concretely, we made use of the professional [Tobii X2-30 eye tracker](https://www.tobiipro.com/product-listing/tobii-pro-x2-30/) and its corresponding software [Tobii Studio](https://www.tobiipro.com/product-listing/tobii-pro-studio/) to estimate participants' gaze points. Our webcam is the built-in camera of our experimental laptop, a Dell Inspiron 5759 with a 17-inch screen and a 1920*1080 resolution. To estimate the gaze points based on a live webcam feed, we relied on [WebGazer.js](http://webgazer.cs.brown.edu/)[1], an open source eye-tracking library written in JavaScript.

### Web Application

During the experiments, participants are asked to watch two lecture videos (i.e. [solar energy](https://www.youtube.com/watch?v=SNmKom56HqE) and [nuclear energy](https://www.youtube.com/watch?v=BW4Q9YQ2gAQ)). Before each video, there is a webcam setup and a calibration for WebGazer.js. In the webcam setup, participants are asked to make their faces fully detected before the calibration. In the calibration, participants are asked to click 40 dots randomly appearing on the screen. The calibration of Tobii is conducted before the participants start interacting with the Web application.
When participants are watching lecture videos, their gaze data is recorded by both Tobii and the Web application at the same time. Their reports of mind-wandering are recorded by the Web application.

If you are interested in this Web application, the detail information and setup can be found in [this repository](https://github.com/Yue-ZHAO/MWDET_WebApp).

### Experiment Dataset

You can download the dataset from either [Github](https://github.com/Yue-ZHAO/MWDET_Project/blob/master/Data_Publish.zip) or [Dropbox](https://www.dropbox.com/s/1gl7mov3q0pbqrk/Data_Publish.zip?dl=0)

There are three folders for the following 3 kinds of data in the dataset. In each folder, a file contains the data of a participant.
1. Tobii Data (.csv files)
  - Coordinates (X, Y), timestamps, and durations of fixations
  - Angles of saccades
  - Coordinates (X, Y) and timestamps of gaze point

2. WebGazer Data (.csv files)
  - Raw data: coordinates (X, Y) and timestamp of gaze point 
  - Prcossed data: coordinates (X, Y) and timestamp of gaze point; coordinates (X, Y), timestamps, and durations of fixations; angles of saccades

3. Event Data (.json files)
  - Participants' report about their mind-wandering
  - The bell rings
  - Video playing status

The timestamps used in this experiments are based on [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) (e.g. 2017-04-26T14:38:29.235Z) with UTC+0 timezone.

The video information (i.e. video playing time and video length) can be found from the event data. The lengths of the videos about [solar energy](https://www.youtube.com/watch?v=SNmKom56HqE) and [nuclear energy](https://www.youtube.com/watch?v=BW4Q9YQ2gAQ) are about 468 seconds and about 400 seconds respectively.

## Detection Results for Mind-Wandering

In the following table, we first list classifiers with grid search parameters in our experiments. All of them are based on modules in scikit-learn.

| Classifier | Grid Search Parameters | module |
| ---------- | ---------- | ------ |
| Logistic Regression | penalty=['l1', 'l2'], tol=[0.1, 0.01, 0.001, 0.0001], C=[100, 10, 1, 0.1], class_weight=[None, 'balanced'] | [sklearn.linear_model.LogisticRegression](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html) |
| GaussianNB | Default Prior Probabilities | [sklearn.naive_bayes.GaussianNB](http://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.GaussianNB.html) |
| Linear SVM | loss=['hinge', 'squared_hinge'], tol=[0.1, 0.01, 0.001, 0.0001], C=[100, 10, 1, 0.1], class_weight=[None, 'balanced'] | [sklearn.svm.SVC](http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC) |

Then we list all the results generated by each classifier as follows:

| Method | Precision | Recall | F1-measure |
| ------ | ---------:| ------:| ----------:|
| Baseline | 0.290 | 0.291 | 0.290 |

The experiment results with Tobii Data are as follows:

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| Tobii Data | Logistic Regression | Global | No | 0.316 | 0.515 | 0.350 |
| Tobii Data | Logistic Regression | Global | Yes | 0.358 | 0.487 | 0.336 |
| Tobii Data | Logistic Regression | Local | No | 0.263 | 0.625 | 0.309 |
| Tobii Data | Logistic Regression | Local | Yes | 0.284 | 0.367 | 0.258 |
| Tobii Data | Logistic Regression | Global+Local | No | 0.302 | 0.505 | 0.330 |
| Tobii Data | Logistic Regression | Global+Local| Yes | 0.301 | 0.487 | 0.310 |

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| Tobii Data | Linear SVM | Global | No | 0.224 | 0.197 | 0.132 |
| Tobii Data | Linear SVM | Global | Yes | 0.207 | 0.345 | 0.250 |
| Tobii Data | Linear SVM | Local | No | 0.160 | 0.444 | 0.222 |
| Tobii Data | Linear SVM | Local | Yes | 0.212 | 0.526 | 0.252 |
| Tobii Data | Linear SVM | Global+Local | No | 0.115 | 0.369 | 0.167 |
| Tobii Data | Linear SVM | Global+Local | Yes | 0.346 | 0.502 | 0.330 |

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| Tobii Data | GaussianNB | Global | No | 0.343 | 0.462 | 0.325 |
| Tobii Data | GaussianNB | Global | Yes | 0.245 | 0.510 | 0.304 |
| Tobii Data | GaussianNB | Local | No | 0.397 | 0.298 | 0.245 |
| Tobii Data | GaussianNB | Local | Yes | 0.294 | 0.682 | 0.364 |
| Tobii Data | GaussianNB | Global+Local | No | 0.342 | 0.486 | 0.335 |
| Tobii Data | GaussianNB | Global+Local | Yes | 0.286 | 0.526 | 0.325 |

The experiment results with WebGazer Data are as follows:

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| WebGazer Data | Logistic Regression | Global | No | 0.319 | 0.595 | 0.357 |
| WebGazer Data | Logistic Regression | Global | Yes | 0.239 | 0.384 | 0.246 |
| WebGazer Data | Logistic Regression | Local | No | 0.294 | 0.534 | 0.366 |
| WebGazer Data | Logistic Regression | Local | Yes | 0.256 | 0.425 | 0.310 |
| WebGazer Data | Logistic Regression | Global+Local | No | 0.270 | 0.553 | 0.310 |
| WebGazer Data | Logistic Regression | Global+Local| Yes | 0.261 | 0.535 | 0.299 |

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| WebGazer Data | Linear SVM | Global | No | 0.059 | 0.308 | 0.093 |
| WebGazer Data | Linear SVM | Global | Yes | 0.232 | 0.607 | 0.319 |
| WebGazer Data | Linear SVM | Local | No | 0.203 | 0.332 | 0.189 |
| WebGazer Data | Linear SVM | Local | Yes | 0.145 | 0.500 | 0.210 |
| WebGazer Data | Linear SVM | Global+Local | No | 0.154 | 0.429 | 0.217 |
| WebGazer Data | Linear SVM | Global+Local | Yes | 0.228 | 0.642 | 0.303 |

| Data | Classifier | Feature | SMOTE | Precision | Recall | F1-measure |
| ---- | ---------- | ------- | ----- | ---------:| ------:| ----------:|
| WebGazer Data | GaussianNB | Global | No | 0.309 | 0.671 | 0.395 |
| WebGazer Data | GaussianNB | Global | Yes | 0.306 | 0.744 | 0.405 |
| WebGazer Data | GaussianNB | Local | No | 0.313 | 0.650 | 0.394 |
| WebGazer Data | GaussianNB | Local | Yes | 0.320 | 0.691 | 0.403 |
| WebGazer Data | GaussianNB | Global+Local | No | 0.289 | 0.696 | 0.378 |
| WebGazer Data | GaussianNB | Global+Local | Yes | 0.286 | 0.674 | 0.378 |

## Scripts

For the data processing and mind-wandering prediction, we have [3 scripts](https://github.com/Yue-ZHAO/MWDET_Project/tree/master/Scripts) in following. Two scripts in Python are written as Jupyter notebooks. The scripts in R contains several functions for data preprocessing, in which the saccade detection is based on an R package named [Saccades](https://github.com/tmalsburg/saccades)

1. Scripts 01 (in Python) for feature extraction and mind-wandering prediction based on Tobii data.
2. Scripts 02 (in R) for saccade detection based on WebGazer data (detection algorithm is based on [2])
3. Scripts 03 (in Python) for feature extraction and mind-wandering prediction based on WebGazer data preprocessed in 2.

Due to the randomization of the techniques SMOTE [3], which is used in our scripts for unbalanced data, the prediction results might be slightly different in each runs.

## Reference
[1] Papoutsaki, Alexandra, et al. "WebGazer: scalable webcam eye tracking using user interactions." IJCAI, 2016.

[2] Engbert, Ralf, and Reinhold Kliegl. "Microsaccades uncover the orientation of covert attention." Vision research 43.9 (2003): 1035-1045.

[3] Chawla, Nitesh V., et al. "SMOTE: synthetic minority over-sampling technique." Journal of artificial intelligence research 16 (2002): 321-357.
