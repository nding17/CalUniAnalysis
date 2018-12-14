# Predicting admission yield for University of California schools

Nelson, Michal, and Nick's final project for CP341 Data Science.

## Structure of the repository

We have prepared multiple Jupyter notebooks for viewing the data:

 - [**DataExploration.ipynb**](/DataExploration.ipynb): Goes into detail about our initial datasets - the GPAs and numbers of applying, admitted and enrolled students in the UC system. Also, explains the purpose of the project and our choice of datasets. Contains sample visualizations using our preprocessed data.

 - [**preprocessing.ipynb**](/preprocessing.ipynb): Explains why and in what ways we preprocessed our datasets. 

 - [**TestScores.ipynb**](/TestScores.ipynb): Contains preprocessing of our high school test scores data (AP,
 SAT, ACT). 

 - [**Visualizations2.ipynb**](/Visualizations2.ipynb): Visualizations from our second iteration - uses the clean, preprocessed data to show new insights.

 - [**cross_validation.ipynb**](/cross_validation.ipynb): Explores using cross validation on our prediction model

 - [**distances.ipynb**](/distances.ipynb): Finds distances between each UC campus and each high school using the Google Maps API.

 - [**FindingCdsNumbers.ipynb**](/FindingCdsNumbers.ipynb): Our main dataset does not use the CDS school codes. We try to find the appropriate CDS codes by matching the school names using fuzzy logic.

 - [**MergingTestScores.ipynb**](/MergingTestScores.ipynb): Uses the newly acquired CDS school codes to merge our main dataset with our test scores datasets.

 - [**Modeling.ipynb**](/Modeling.ipynb): Contains the modeling we performed on our data to predict yields in the UC campuses.

Moreover, we decided to store the data inside of the repository. The data can be found in the [**/data**](/data) catalog.

Additional documentation about the project can be found in the [**/docs**](/docs) catalog.

To see the latest progress we have made, see [**/experiments**](/experiments).

