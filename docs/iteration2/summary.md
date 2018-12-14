# Iteration 2

## Description of the update

Theo only comments we have received after Iteration 1 were concerned with our presentation style.
Therefore, we were not able to reflect these comments in the repository. However, we performed
significant amount of work to verify and fix our preprocessing. That update is described in the 
**Data Cleaning** section of this document.

Moreover, we included extra data in our dataset and performed data-cleanup on it. That update is also
described in the **Data Cleaning** section.

## Data Cleaning

**For more detailed information about our data cleanup method, see:**
  - [/preprocessing.ipynb](/preprocessing.ipynb)
  - [/TestScores.ipynb](/TestScores.ipynb)

### Data cleanup from iteration 1

For this data analysis task, we have two raw data files regarding the admission information of the
universities of California. The first file is dealing with various kinds of GPAs and the second file
contains valuable information regarding applicant numbers. Therefore, the plan for our data
cleaning task is to merge the two data files together. The main purpose for the data merging is
that, other than the two variables mentioned above (GPA and student number), the other variables
are consistent. It would be redundant and wasteful of space to keep two files separately. 

Before we proceed to the merging, we have to clean up individual files first. The first step of the
clean-up is to replace some confusing column names with the ones that could be easily manipulated,
such as, replacing "Uad Uc Ethn 6 Cat" with "ethnicity" and "Measure Name" with "type" etc. Then
some preliminary files merging could be initiated. Before the merging was happening, we have the
liberty to pick up columns that are useful to our analysis. This cleaning process will also reduce
the size of the combined data sets. As the two files getting merged, we could do some further
cleaning on the newly-created file, for example, we could delete rows that have NaN in the GPA column. 

As we are getting satisfied with dropping the rows that are useless, we would be able to notice
that there are some more cleaning work we could do by looking at the existing data file. First of
all, instead of having three different types - enrolled ('enr'), admitted ('adm') and applied
('app'), we could explicitly unpack this column and make seperate columns for specific variables
that are relevant to 'type', for example, we have columns like 'adm_gpa' to stand for GPA for
admission, and 'enr_num' for the number of students that enroll into any university etc. Next, as
there are only nine universities in University of California system, it becomes natural to regroup
the data by campus. By doing this, 'campus' becomes a categorical field.

### Data cleanup from iteration 2

During the second interation of our project, we focused on the following areas related to data cleanup:
 - Verifying the data cleanup process from our previous iteration
 - Fixing possible artifacts
 - Finding new data
 - Cleaning up and verifying the new data
 - Merging the new data with our main dataset

#### Verifying and fixing the data cleanup process from the previous iteration

Before proceeding to statistical analysis and data modelling we wanted to make sure that our cleanup process is
correct. To do that, we performed a few sanity checks.

----

First, we tried to verify our high school location data. We compared the number of California high schools as
yielded from our preprocessed dataset with the number received in our [DataExploration](/DataExploration.ipynb)
notebook. We expected that the numbers should be similar, however it turned out that we lost over 70% of CA
high schools. Upon further investigation, we realized that most of these schools were located internationally.
Clearly, our location algorithm was failing.

To fix the problem, we decided to create 3 location columns:
 - Country
 - State (only valid when country == 'USA')
 - County (only valid when state == 'California')

We used a method similar to the one we employed in [DataExploration](/DataExploration.ipynb). First we grouped
schools into location categories (California schools, US schools not in California, International schools). Next,
we used the categories to set the `Country`, `State` and `County` columns appropriately. To validate that our fix
worked, we performed our initial sanity checks - none of which showed any issues.

----

The second problem we found with our data was a dramatic decrease in the number of data rows after data cleanup.
We started with around a million GPA data rows, and a million Count data rows. We expected to have somewhere between
700k - 1000k output rows after merging the GPA and count data. However, after our cleanup process, we ended up
with only 140k rows. After careful debugging, we found out that the rows were dropped while merging the GPA and count
data. We considered possible explanations:
 - The data points for GPA and Counts do not have enough overlap. That is, high schools listed in the GPA datasets
   were mostly different from the high schools in the Counts dataset. However, since the datasets came from the same
   source, such explanation was quite unlikely.
 - Our merging code incorrectly rejects matches between datapoints in the datasets.

We decided to investigate the second possibility further. We dropped various `merge` columns to identify the column
responsible for dropping rows. It turned out that the `school` column was behaving incorrectly, and caused the data
loss. We investigated the values in the `school` column to find out that the datasets use a different format for
school codes.

To fix the problem, we split the school code from the school name, and normalized the school code format. After those
steps, we ended up with 1000k rows after the merge, which seemed plausible considering the datasets came from the same
source.

----

The last problem we found in the preprocessing was dropping `NaN` rows. We realized that we never used the cleaned
`DataFrame`, and kept on using the pre-cleanup one. We switched the dataframes to use the clean dataset instead.

#### High school test data

While performing statistical analysis on our data, we became concerned that we might not have enough information to
create an accurate model. We decided that we should extend our dataset with new datapoints: information about high
school tests (AP, SAT, ACT).

**See [TestScores.ipynb](/TestScores.ipynb) for the Jupyter notebook described in this section.**

We have found a dataset with California SAT, ACT and AP test scores by year and high school:
https://www.cde.ca.gov/ds/sp/ai/. The dataset consists of multiple `.xls` files grouped by:
 - year
 - test
 
Moreover, the `.xls` files have different formats. Examples of differences include:
 - extra/missing columns
 - number of rows before the header
 - different column names
 
Our preprocessing consisted mostly of:
 - Automatically reading multiple .xls files and reading them into Pandas dataframes
 - Adding/Droping/Renaming columns in the datasets. That step was necessary to concat
   test data from multiple years.
 - Adjusting the year column to match our main dataset.
 - Imputing data in the SAT score section. That was necessary since in 2008, the SAT tests
   changed their format, and started using 3 sections instead of 2. We needed to put our
   test scores on the same scale.
 - Changing missing values into NaNs.
 - Converting cell values into numbers.
 - Saving the preprocessed data into new .csv files.

We wanted to merge the test datasets with our main dataset using the `school_num` column. Unfortunately,
we found out that the school codes in our main datasets do not corespond to California CDS codes. For our
next iteration, we will try to find a different method to merge the datasets.

#### Distance to college data

Another metric we were trying to introduce into our dataset is the distance between the high school and the
UC campus. We used the Google Maps API to locate the high schools from our dataset and compute their distance
to each UC campus.

Unfortunately we encountered some major problems while using this approach:
 - Multiple schools were returned for the same name, making it difficult to find the correct location
 - Google Maps API allows for very small numbers of API calls per day, and we were repeatedly hitting
   these limits.

## Plan for completion

There are multiple areas we would like to improve on in the next/final iteration.

### Data collection and preprocessing

As discussed in the *Data Cleaning* section there are a few steps we will need to take to fix existing data
and add new datasets:
 - Finish finding distances between high schools and university campuses. This data could be used to show if
   the students are more likely to accept an offer from a school located near their residence.
 - Finish integrating test scores information with the main dataset.
 - Perform basic statistical analysis on the new data.
 - Use the collected test and location data as additional features for our model.

### Prediction model

The work we need to perform for our prediction includes a combination of finding predictive features and
evaluating different machine learning models. We will focus on the following:

  - Use distance, test score, gpa, and year to predict the yield for a school
    - Perhaps, we will create other features that seem to be correlated with yield
	- For our models we will consider:
  	- multivariate linear regression
  	- cross-validation (Since we only have data from the past few years)
  	- We will also consider perturbing our data points to increase the size of our training set.
    	- E.g. we could split out dataset into sets of N random high-schools
  - in order to keep information from bleeding from the training to testing data, we won't mix a year's worth of data between training and testing sets
		- for instance, we will use all data from 2014-16 as testing, and won't use any of it for training
	- We will use average yield from previous years as a baseline model

## Implementation summary:
- Found datasets of SAT, AP and ACT per California High School (Mike)
- Merged and cleaned the SAT and AP data (Mike)
- Fixed high school location in the preprocessing section (Mike)
- Fixed merging the GPA and Count datasets in the preprocessing section -- 
  the fix moved us from 140k datapoints to 1000k datapoints (Mike)
- Written up the Data Cleanup documentation (Mike)

- heatmap of yield rate vs adm_gpa - app_gpa (Nelson)
- line plot of yield rate vs year for various races (Nelson)
- line plot of yield rate vs year for various campuses (Nelson)
- yield rate distributions for different campuses (Nelson)
- ppt slides (Nelson)

- Exploring the Google Maps API to find the distance between high schools and UC campuses (Nick)
- Preparing new visualizations from our cleaned dataset (Nick)
- Preparing statistical analysis on our cleaned dataset (Nick)
- Fixed a bug in preprocessing in filtering NaN rows (Nick)
-
