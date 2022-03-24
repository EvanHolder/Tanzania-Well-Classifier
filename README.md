<table><tr>
<td><img src="https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/drinking-water-wells-social-media.jpg" style="width:476px;height:250px"/></td>
<td><img src= "https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/African-water-pump.jpg" style="width:376px;height:250px"/></td>
</tr></table>


# Module 3 Project - Predicting Functionality of Tanzanian Water Wells
**Author**: Evan Holder<br>
**Flatiron Data Science**:  Phase 3 Project <br>
**Github**: [Link](https://github.com/EvanHolder/Tanzania-Well-Classifier)<br>
**Presentation**: [Link](https://drive.google.com/file/d/1hMDWyoc62mUxDucoXc9JPiGv_Yl1Wt9d/view?usp=sharing)<br>

# Repo Contents
* README.MD
* data
* images
* results_submissions
* [mod_3_well_functionality.ipynb](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/mod_3_well_functionality.ipynb) - full analysis here

# Introduction and Overview

Water has been scarce in Tanzania for a long time.  In recent years, a growing population, increased water consumption, and climate change have only exacerbated the water shortage.  Accorinding to the World Health Organization, one in six Tanzanians lack access to safe drinking water. Many must travel miles on foot just to find water. Now, imagine you've spent the first two hours of the day walking in the sun, only to find a hour long lines, or worse, the tap is not functioning.

In this project, I will be modeling Tanzanian well data from datadriven.org [competition](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/), *Pump it Up, Data Mining the Water Table*. The data includes information about the pump itself, and its operation.  The goal is to predict which wells are functional, non-functional, and which need repair.  Using the model, authorities will be able to better monitor and service pumps in need of repair and/or replacement.  This model will help ensure that clean, drinkable water is available to more people across Tanzanian and help curb the water shortage.

To build this model, I'll start with a decision tree. Next I'll make a random forest of trees, and finally I'll try out an XGBoost model.  The model with the best accuracy will be the final model.

## Business Problem

This project is all about increasing access to clean, usable well water across the country.   So keeping that in mind, this project aims to increase the number of functional wells only. It does not aim to minimize cost, only to turn non-functional and functional needs repairs wells into functional wells.  Additionally, the model shows which features are most important in determining well functionality.

# Data Understanding

The data includes 41 columns, many of which contain similar names and overlapping information. The first step was to research the columns, and remove features which do not help classify well functionality. In general, features I found important included:
* Pump Design - Any feature which indicated information about the type of pump and/or pumping mechanism
* Water Source - Any feature that described the water source, well type, or water quality
* Pump Quality - Any feature that could indicate the quality of the pump, from original build/installation through continued maintenance and operation
* Location/Government - Any feature that indicated the location of the pump
* Monetary - Any feature that described how people pay for the pump


# Data Preparation

The next step was to prepare the data for modeling.  The first thing I did was investigate the location feature for any relevant patterns.  Using the longitude and latitude coordinate, I was able to plot each well according to its functional status overlayed on top of a map of Tanzania.

[](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/well_points.jpg)

In the above map, you can see that functional and non-functional wells are mixed quite homogenously.  Therefore it is unlikely that the raw coordinates are going to play a role in predicting their status.  Just to be sure, I plotted the well points again with changing colors for indicating which local government authorities (LGA) they are under.

[](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/well_points_fnr.jpg)

Once again, as you can see. The functional (X's) and non-functional (O's) are not mixed in with each other evenly. This is a good indicator that location and will not be good predictors of functionality.

## Feature Engineering
From here, I engineered a new variable `age` and label encoded a few of my categorical features.  Here I ran into a challenge.  A few categorical columns (`installer`, `lga`, `ward`, `scheme_name`) included from 100-2000+ unique elements per column.  Encoding dummies for these variables would increase the dimensionality of the dataset and cause issues for the ML algorithms down the line.  Another option was label encoding these variables as I had done with the other categoricals, but the issue there was many of the unique elements had a count of 5 or less.  This would introduce an unwanted bais for/against these particular elements with few instances.


So to solve this issue I built a target encoder which encodes the smoothed probability of the target for each unique element in a column. While there were target encoders available on the web, I built my own because:
 1. Ternary target - Other probability encoders online are available to support a boolean target, but I could not find one for ternary. 
 2. Smoothing - I want to custom build a mechanism into the function to smooth the probabilities of elements that occured less than 10 times. This way the encoder would weight dataset average of the target class more heavily when there are fewer instances of a particular element.

I wrote a blog post on this function which you can read [here](https://medium.com/@evanholder_40386/target-encoding-and-smoothing-for-ternary-targets-476db9f40ceb). In summary, the `target_encode_columns` function will encode high cardinality categorical columns with each unique element's probability of each target class, and then smooth them with the dataset average for that target class according to the frequency with which it appears in the training set.


# Modeling
I trained three different types of models Decision Trees, Random Forest, and XGBoost. Choose these algorithms as they easily model non-linear relationships, are easy to tune, and have high performance.  A deep XGBoostClassifier proved to have the best performance on the data set.  Parameters tuned included:
* Tree depth (max_depth)
* The learning rate
* Minimum loss reduction require to make a leaf (gamma)
* Subsample used to train a tree (subsample)
* Number of columns to use in a tree (colsample_bytree)
* Minimum instance weight needed in a child (min_child_weight)

## Model Evaluation
On the training set, the model performed at an 80% accuracy rate in predicting functional, non-function, or needs repair. When tested on the holdout set, the model achieved an accuracy of 81.6% - meaning it generalized well and was not overfit. The below chart shows the feature importances in our data:

[](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/featurue_importance.jpg)

As shown, the most import feature in predicting functional status was the quanitity of water at the pump, then the waterpoint type, the which ward the well was located in. 

In terms in real-life well functionality, the below confusion matrix highlights which wells are functional before application of the model 53.6%.  

[](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/matrix_existing.jpg)

Using the model, we've correctly identified 396 model that need repair and 4486 non functional wells. These correctly identified wells could be fixed.  In terms of resource saving, if the workers sent to replace the 168 wells (which were actually only in need of a fix) actually just replaced the well instead, we could consider these fixed as well. 

[](https://github.com/EvanHolder/Tanzania-Well-Classifier/blob/main/images/matrix_final.jpg)

In the end, using the model, we could increase the number of working wells by 5050 to a total of 13046 or 86.7%


# Next Steps

An issue with the model is that it incorrectly predicts that about 1600 wells are functional, meaning that if utilizing the model, we would never send workers out to fix or replace them, and would never find out that they were not working. Additional work is needed to minimize the number wells predicted functional but in reality don’t work.
