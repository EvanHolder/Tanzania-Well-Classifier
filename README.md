
# Module 3 Project - Predicting Functionality of Tanzanian Water Wells
Evan Holder
Flatiron School

## Repo Contents
* Readme
* well_functionality_presentation.pdf - PDF of my presentation 
* link to presentation: (https://drive.google.com/file/d/1hMDWyoc62mUxDucoXc9JPiGv_Yl1Wt9d/view?usp=sharing)
* mod_3_well_functionality.ipynb
* mod_3_well_functionality-Jupyter-Notebook.pdf
* 
* Data
    * testing_data_independent.csv
    * training data_independent.csv
    * training_data_labels.csv

## Introduction and Overview

Water has been scarce in Tanzania for a long time.  In recent years, a growing population, increased water consumption, and climate change have only exacerbated the water shortage.  Accorinding to the World Health Organization, one in six Tanzanians lack access to safe drinking water. Many must travel miles on foot just to find water. Now, imagine you've spent the first two hours of the day walking in the sun, only to find a hour long lines, or worse, the tap is not functioning.

In this project, I will be modeling Tanzanian well data from datadriven.org competition, *Pump it Up, Data Mining the Water Table*. The data includes information about the pump itself, and its operation.  The goal is to predict which wells are functional, non-functional, and which need repair.  Using the model, authorities will be able to better monitor and service pumps in need of repair and/or replacement.  This model will help ensure that clean, drinkable water is available to more people across Tanzanian and help curb the water shortage.

To build this model, I'll start with a decision tree. Next I'll make a random forest of trees, and finally I'll try out an XGBoost model.  The model with the best accuracy will be the final model.

## Business Problem

As stated above, this project is all about increasing access to clean, usable well water across the country.   So keeping that in mind, this project aims to increase the number of functional wells only. It does not aim to minimize cost, only to turn non-functional and functional needs repairs wells into functional wells.  Additionally, the model should be able to show which features are most important in determining well functionality.

## Data Understanding

In this section we will place all import statements and import the data. Then I'll run through all the information researched about each feature and decide which should remain and which should be removed.

## Data Preparation

This section focuses on preparing the data for modeling.
* Drop appropriate columns
* Investigate well locations on a map of Tanzania
* Create well 'age' feature
* Label encode categorical features with low cardinality
* For categorical features with high cardinality
    * Replace unique classes that only appear in the testing sets
    * Target encode and smooth 
* Label encode target feature

## Modeling

This section will be dedicated to modeling. Each new model will be iteratively named M_0, M_1,... M_n. I'll use three types of models:
* DecisionTreeClassifier()
* RandomForestClassifier()
* XGBoostClassifier()

## Model Evaluation

This section will focus on evaluation of the best model.
* Use XGBoost's plot_importance() to look at the most important features.
* Use a confusion matrix to look at where the model predicted correctly and incorrectly.

## Scatch Work

This section has left over scratch work used for the project. It mainly consists of grid_search's when tuning models in the Modeling section. This work was left here to remined myself which parameters I had already tried but did not produce models with better accuracy than the final model.
