# Intro to Machine Learning

## Contents
1. [Overview](#overview)
2. [Setting up](#setting-up)
    1. [Accessing the Data](#accessing-the-data)
    2. [Starting a Jupyter Session](#starting-a-jupyter-session)
    3. [Opening a Notebook](#opening-a-notebook)
3. [Linear Regression Example](#linear-regression-example)
    1. [Code](#regression-code)
5. [Clustering Model Example](#clustering-model-example)

## Get the Files
[![](/Images/Download-Button.png)](intro-to-ML.tar.gz)


--------------

# Overview

This short training class is available to users periodically throughout each academic year. Information on upcoming workshops can be found in our [training documentation](https://public.confluence.arizona.edu/display/UAHPC/Training). Before each workshop, an email will be sent to all HPC users through hpc-announce with information on dates, timing, location, and registration.

This workshop provides a brief introduction to key concepts of machine learning and is followed by two hands-on examples that emphasize running a Jupyter notebook on the HPC supercomputers. 

A PDF of the slides from past workshops can be found in our training documentation linked above. 



--------------

# Setting up
A component of these workshops is interactive where users will learn to:

1. Train and visualize a linear regression model using a training set.
2. Build and visualize a clustering model using the "elbow method".

Both of these exercises will make use of Python in a Jupyter Notebook through Open OnDemand.

## Accessing the Data
To begin, start a terminal to log into the system and copy the necessary files into your account. If you're unsure of how to use or access a terminal, see [our online documentation](https://public.confluence.arizona.edu/display/UAHPC/System+Access#SystemAccess-CommandLine/TerminalAccess) for information (or, if you're in a live workshop, flag one of us down and we can help). To get the files you need, use the following commands: 
```
ssh your_netid@hpc.arizona.edu
shell
ocelote
mkdir intro_to_hpc
cd intro_to_hpc
cp /xdisk/chrisreidy/workshops/* .
```
If you get a permission denied message using ```cp```, you likely haven't been added to the group ```chrisreidy```. If this is the case, stay in your ```intro_to_hpc``` directory and use:
```
wget https://ua-researchcomputing-hpc.github.io/Intro-to-Machine-Learning/intro-to-ML.tar.gz
tar xzvf intro-to-ML.tar.gz --strip-components=1
rm intro-to-ML.tar.gz
```

## Starting a Jupyter Session
For this tutorial, we'll use a Jupyter Notebook which is available as an interactive application and can be accessed through [Open OnDemand](https://ood.hpc.arizona.edu/).

Once you log in using your university credentials, click the **Interactive Apps** dropdown menu and select **Jupyter Notebook**. This will bring you to a web form that's used to request compute resources on one of our clusters. Use the following options in your request:

|Option|Value|
|------|-----|
|Cluster|Ocelote Cluter|
|Run Time | 2|
|Core count on a single node | 1 |
|Memory per core|6|
|GPUs required|0|
|PI Group | your group**|
|Queue | standard|

** If you don't know your group's name, go to a terminal session (see section above) and use the command ```va```.


<img src="application-select.png" alt="application-select" height="400"/> <img src="web-form.png" alt="web-form" height="400"/>





Once you complete the form, click **Launch**. This will bring you to a page with a tile that shows your pending job. When it's first submitted, its status will show as pending. Once it starts, it's status will change to Running and you'll be given a link you can use to connect. 


<img src="queued.png" alt="queued" width="600"/>


<img src="running.png" alt="running" width="600"/>

## Opening a Notebook


--------------


# Linear Regression Example


## Regression Code
Import Libraries
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
```
Use Pandas to load the data and view the first five rows
```
data = pd.read_excel("king_county_house_data.xls")
data.head(5)
```
Choose the columns from the data and split into train and test sets
```
space = data['sqft_living']
price = data['price']
# Change X into 2D array
X = np.array(space).reshape(-1, 1)
Y = np.array(price)
# Split data into train sets and test sets
X_train,X_test,Y_train,Y_test = train_test_split(X,Y,test_size=1/3,random_state=0)
```
Visualize the train set
```
# Visualize training set
plt.scatter(X_train,Y_train,color="red",label="Living Area")
plt.title("Housing Prices")
plt.xlabel("Area")
plt.ylabel("Price")
plt.legend()
plt.show()
```
Train the model with the training set and predict with the test set
```
# Train
regressor = LinearRegression()
regressor.fit(X_train, Y_train)
# Prediction
y_pred = regressor.predict(X_test)
```

Visualize the train data and the best fit line
```
# Visualize the data and the best fit line
plt.scatter(X_train,Y_train,color="red",label="Living Area")
plt.title("Housing Prices in King County")
plt.plot(X_train,regressor.predict(X_train),color="blue",label="Price")
plt.xlabel("Area")
plt.ylabel("Price")
plt.legend()
plt.show()
```
Predict the price of a house with a certain area
```
# Make a prediction
area = 5000
price = regressor.predict([[area]])
print('House of %d sq-ft costs about $%d' % (area, price))
```
Visualize the test data
```
# Visualize test set
plt.scatter(X_test,Y_test,color='red',label="Living Area")
plt.plot(X_test,regressor.predict(X_test),color="blue",label="Price")
plt.xlabel="Area (sq-ft)")
plt.ylabel("Price (USD)")
plt.legend()
plt.show()
```

--------------

# Clustering Model Example

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)

