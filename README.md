# COVID-19 

## Data source

This dataset was gotten from Our World in data's github account, access it [here](https://github.com/owid/covid-19-data/tree/master/public/data)

It contains information about COVID-19 confirmed cases, death cases, vaccination records and population of countries and other specific locations in the world.

**Dataset before cleaning**


![image](https://user-images.githubusercontent.com/109418747/190338893-be9bb01f-90e6-40a2-a611-8fd338444616.png)



![image](https://user-images.githubusercontent.com/109418747/190339602-15f0327a-a863-4996-8146-f2460ee2feb3.png)



![image](https://user-images.githubusercontent.com/109418747/190339678-63d45c22-c590-40f3-b0f3-898c5637c520.png)


## Data cleaning with SQL

```
--- To know the distinct countries in the data

SELECT DISTINCT location FROM [dbo].['owid-covid-data$'ExternalData_1]

--- To determine the distinct continents in the data
SELECT DISTINCT continent FROM [dbo].['owid-covid-data$'ExternalData_1]


 /* Selecting required columns for the analysis, I used the Cast function to change the data type of the selected columns from varchar to float and the Coalesce
 function to convert null values to '0'*/
 
SELECT [continent],
   [location],
   [date],
   [population],
   COALESCE ([new_cases],0) new_cases,
   COALESCE ([total_cases],0) total_cases,
   COALESCE(CAST([total_deaths] AS float),0) total_deaths,
   COALESCE(CAST([new_deaths] AS float),0) new_deaths,
   COALESCE(CAST([total_tests] AS float),0) total_tests,
   COALESCE(CAST([new_tests] AS float),0) new_tests,
   COALESCE(CAST([total_vaccinations] AS float),0) total_vaccinations,
   COALESCE(CAST([people_vaccinated] AS float),0) people_vaccinated,
   COALESCE(CAST([people_fully_vaccinated] AS float),0) people_fully_vaccinated,
   COALESCE(CAST([total_boosters]AS float),0) total_boosters
   INTO #Clean_data
   FROM [dbo].['owid-covid-data$'ExternalData_1];

---Insert result of the above query into local temporary table

   SELECT *
   FROM #Clean_data;

/* 1.Year function to extract year from date.
   The dateset contain cumulative records for some locations and cumulative records for all continents which were excluded from data required for analysis.*/

   SELECT continent, 
   [location],
   [population],
   CAST([date] AS date ) date, 
   YEAR([date]) year,
   SUM([new_cases]) AS confirmed,
   SUM([new_deaths]) death,
   [total_tests] total_tests,
   [total_vaccinations] vaccination,
   [people_fully_vaccinated] people_fully_vaccinated,
   [people_vaccinated] people_vaccinated,
   [total_boosters] total_boosters
   FROM #Clean_data
   WHERE continent IS NOT NULL
   AND location NOT IN ('World','Asia','Africa','Europe','European Union','Lower middle income',
   'North America','South America','Oceania','Low income','International','Upper middle income','High income')
   GROUP BY [location], continent,YEAR([date]),[date], [population]
   ORDER BY [location]; 
   ```
   
   ## Analysis
   
   Click [here](
   https://public.tableau.com/app/profile/lawal.kaosarah/viz/covid19_16629089640860/Dashboard1?publish=yes) for the interactive dashboard and to view insights generated
   
   
   ## Findings and Recommendations
   
   As at September 1, 2022 there were 602,534,782 confirmed cases and 6,458,913 death cases. United state has the highest confirmed and death cases.
   United state, India, France, Germany, South Korea, United Kingdom, Italy, Russia and Japan have the highest covid prevalence.
   
   China records the highest vaccination history with 88% of it population vaccinated, follow by Brazil(80.12% of entire population),India (66.96%) and United state  (66.5%). 
   
  Papua New Guinea(2.86%), Madagascar(4.84%), Cameroon(4.51%), Burundi(0.14%), Haiti(1.46%) and Yemen(0.13%) are the countries with the lowest population vaccination records.
  
 Awareness about the importance of the vaccines should be in place in Countries with low vaccination history and more vaccines should be provided.
 
 Individuals showing any form of Covid19 symptoms should visit the clinic for proper checkup and treatment as there is low death rate and high recovery rate.
 
 Proper preventive measures to eliminate or minimize the transimmision of the infection should not be neglect.
   
   
