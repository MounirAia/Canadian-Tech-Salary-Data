# Introduction
Welcome to the Canadian Salary Data repository. This repository aims to provide a comprehensive and unified dataset of Canadian salary information derived from the Stack Overflow surveys. The data spans from 2011 to 2023 and includes additional datasets to enhance the accuracy and depth of the salary analysis. This repository serves as a valuable resource for understanding salary trends and distributions across various roles, industries, and cities in Canada. The data processing steps, from collection to cleaning and integration, ensure that the dataset is robust and ready for further analysis.

The final dataset can be found at _Canadian Data/CanadaData.csv:_.

Kaggle Dataset: https://www.kaggle.com/datasets/moun12345/canadian-salary-data-from-stack-overflow-survey

## File Structure

- _Canadian Data Preprocessing/*:_ Contains the jupyter notebook used to clean the data.

- _Canadian Data/CanadaData.csv:_ Contains the final data set for the Canadian data. This data set is used to create the final machine learning model. The salary column is in USD dollars.

- _Canadian Data/GovSalaryCombined.csv:_ Dataset that was constructed to map canadian city to the salary of the stack overflow survey. All the salary in this dataset is in CAD dollars.

- _Canadian Data/CanadianCitiesPopulation.csv:_ Contains the population of the Canadian cities.

- _Canadian Data/Stack Overflow/stack-overflow-canada.csv:_ Contains the unified and uncleaned view of all Canadian salary data from the Stack Overflow survey.

-  _Canadian Data/Stack Overflow/*:_ Normally contains all the stack overflow survey, but since they are too large for the repo. You need to download them [Stack Overflow Survey](https://insights.stackoverflow.com/survey) and store them in this format (up to 2023.csv):
	- <img src="https://github.com/user-attachments/assets/e2e62d2a-bb4f-4e99-bcac-89a1f0bd610c" alt="Desktop Application" width="400"/> 

## Data Processing Pipeline

This is a brief description of the few noticeable problems that I had to solve to build the dataset used for this application.
I refer to "user" as the person who had answered the Stack Overflow Survey.

A visual representation of the data used can be found at: https://www.canada-tech-salary.tech/data

The Repository of the website can be found at: https://github.com/MounirAia/Canadian-Tech-Salary-Prediction-App

### Data Collection

#### Sources

My primary source of data is coming from the [Stack Overflow Survey](https://insights.stackoverflow.com/survey). I treated the data from 2011 up to 2023. All the datasets that are not from the Stack Overflow Survey were used to enhance the Stack Overflow data. I treated in total 25 files.

- 13 [Stack Overflow Survey](https://insights.stackoverflow.com/survey) files
	- Only processed the data of users working in Canada
- [Canada Government data](https://www.jobbank.gc.ca/trend-analysis)
- [Canada's most populous city](https://en.wikipedia.org/wiki/List_of_the_largest_population_centres_in_Canada)

### Data integration
All stack overflow datasets differs in how they store their data (Column name, and Column value).  I had to integrate all the different columns to follow a uniform structure, to make the model training possible. This step of data processing was marked by a heavy use of ChatGPT that really shines for string processing column.

The data collected in the Stack Overflow Survey was:
- Company size of the person's company
- Industry type of the company
- Title of the job
- Years of experience of the person
- Country (fix to Canada)
- City of the person's job
- Salary

#### Title problem
For each dataset, it had different ways of storing a similar role name (the Title of the user job). This is problematic when you want to aggregate all the similar into similar sections, to strengthen the model. For example, the system needs to understand that: 'Full-stack developer' == 'Developer, full-stack' and this Title should be considered as the same role.

To solve this problem, I used predefined bins of various title in the tech industry, using the titles coming from the 2023 survey. I mapped all the titles of the other surveys in those bins. I extensively used ChatGPT for the mapping.

<img src="https://github.com/MounirAia/Canadian-Tech-Salary-Prediction-App/assets/86434940/e2b4ebbe-4e28-440f-984f-55d51eea94c2" alt="Desktop Application" width="400"/>

### Data Cleaning

#### Missing Values - City Problem 
None of the stack overflow datasets contained the column City, which is a key predictive feature for the model.

To solve this problem, I guessed the city of the user based on its: salary, title and experience. I found the most plausible Canadian city where he/she is working. I collected the: average salary for each role title associated with a certain level of experience for each Canadian city using the [Canada Government data](https://www.jobbank.gc.ca/trend-analysis). I then mapped each user to the city that minimizes the distance between its salary and the average city’s salary for his/her info.

#### Missing Values - Industry Problem 
Many Stack overflow datasets did not contain the company industry where the person was working in.

To solve this problem, I collected the proportion distribution of the different industries for each survey that I had the data of the industry. I then mapped each user randomly for the stack overflow that I did not have the industry for, to match the closest (in terms of year) industry proportion.

### Outliers - Salary problem
Various Salary were too high or too low. Also, the Stack Overflow did not ask for the base salary of the user, but rather the total salary (including stocks and advantages) of the user.

To solve this problem, I computed the Z-score of the salaries’ users based on the mean and the cities’ standard deviation and average salary for each Experience category [0 to 1 year, 2-4, 5-9, 10-*] (using the [Canada Government data](https://www.jobbank.gc.ca/trend-analysis)). I removed all the data that had a standard deviation > |2|.

### Data Exploration

This step was mainly done with the goal of finding anomalies in the dataset.

For example, I found that the highest average salary was attributed to people that had 0 to 1 year of experience, which is not logical. This is why the Z-score computation above considers the average salary per city by experience.

#### Before Outliers Removal
<img src="https://github.com/MounirAia/Canadian-Tech-Salary-Prediction-App/assets/86434940/315e14da-2106-4030-8a1d-d4fe9c8d8683" alt="Desktop Application" width="400"/>

#### After Outliers Removal
<img src="https://github.com/MounirAia/Canadian-Tech-Salary-Prediction-App/assets/86434940/dfdc3e57-6ec0-4d41-8b62-005e2988c213" alt="Desktop Application" width="400"/>
