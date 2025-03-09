# Hospital-Readmission-Insights-Dashboard-2
Project completed in pursuit of Master's of Science in Data Analytics.

## PART A:  DATA DASHBOARDS

### DATASETS AND DASHBOARD FILE

I have provided the twbx file to my Tableau dashboard that supports executive decision-making in this repo. I have also provided both datasets that I used to source my dashboard. I used the provided medical dataset along with a State population dataset that measures State population in 2010, 2018, percentage growth, and population density. In order for the twbx file to load properly, since it uses a custom SQL query, you must create a population table in PostgreSQL first. 
```sql
CREATE TABLE population (
	Rank VARCHAR(2),
	State VARCHAR(2),
	"2010_Population" INTEGER,
	"2018_Population" INTEGER,
	Current_Population INTEGER,
	Growth_Since_2010 DECIMAL,
	Growth_Since_2018 DECIMAL,
	Percent_of_Total_Population DECIMAL,
	Population_Density DECIMAL,
	PRIMARY KEY (Rank))
```

Download the population csv file to your machine, and then update the FROM statement in the code below to where the file exists on your machine, or in Labs On Demand. 
```sql
COPY population
FROM 'C:\Users\Public\Downloads\populationByState.csv'
DELIMITER ','
CSV HEADER;
```
Once this has been completed, then you can open the twbx file using Tableau.

### DASHBOARD INSTALLATION

My main dashboard is a Story made up of two distinct dashboards. The first dashboard is focused on the State population data, whereas the second dashboard is focused on specific patient data. 

#### Population Analysis Dashboard

This dashboard is derived from 4 different worksheets. I will outline how each worksheet is configured below. I added all 4 worksheets to this dashboard. I hid the titles to remove clutter, and removed several filter boxes as well. I applied the “Use as Filter” option on two worksheets (Medical Conditions and the State Map) so that anytime an option is selected, the other graph will update as well. 

*Top 10 States by Population worksheet*

To create this dashboard, add State to Columns. Edit the filter on State by going to the Top tab in the filter menu, and choosing the top 10 states by the maximum value of Current Population. Next, add the dimension Measure Names to the columns, and filter down to only display “2010_Population,” “2018_Population,” “Current_Population,” and “Medical Dataset.” Rename the Measure Names to “2010,” “2018,” and “Current” so that the card is more readable. Add Measure Values to Rows, and update the value of 2010, 2018, and Current to display the maximum value, leaving Medical Dataset as the sum. Add Measure Names to the Color mark. To display better on the dashboard, remove the Title and Headers. 

*Medical Conditions (Top 10 States) worksheet*

To create this dashboard, add State to Columns. Edit the filter on State by going to the Top tab in the filter menu, and choosing the top 10 states by the maximum value of Current Population. Next, drag Measure Names to Filters, and select only medical conditions: Overweight, High Blood Pressure, Back Pain, Reflux Esophagitis, Arthritis, Hyperlipidemia, Allergic Rhinitis, Asthma, Diabetes, Stroke, and Anxiety. Add Measure Values to Rows. Make sure that each value is calculating the sum in the marks card. Add Measure Names to the Color and Detail marks. Add Measure Values to the Label mark. Add Readmission to the Filter and only select 1. Custom sort the States by the highest number of total readmissions (i.e., tallest bars on the graph starting on the far left). Custom sort the Measure Names by highest number of readmissions (i.e., largest section of each bar from top to bottom). Note that sorting the measure names isn’t exactly perfect as some states have differing values, but this mostly shows a descending order of patient’s medical conditions for all 10 states.  

*Readmission State Map worksheet*

To create this dashboard, add Longitude (generated) and Latitude (generated) to the Columns and Rows, respectively. Add Readmission to the Filter and only select 1.  Add State to the Filter and filter by going to the Top tab in the filter menu, and choosing the top 10 states by the maximum value of Current Population.

*Population Growth Rates worksheet*

To create this dashboard, add Measure Names to Columns and only select “growth_since_2010” and “growth_since_2018”. Then add State to Rows, and filter to the top 10 states as before. Update the column headers to be “% Growth 2010” and “% Growth 2018”. Ensure the Measure Values are displaying the average. Format the number to display the percentage with 2 decimal points. 

#### Patient Analysis Dashboard

This dashboard is derived from 3 different worksheets. I will outline how each worksheet is configured below. I added all 3 worksheets to this dashboard. I hid the titles to remove clutter, and removed several filter boxes as well. I applied the “Use as Filter” option on all of the worksheets so that anytime an option is selected, the other graphs will update as well. 

*Patients Admission Type worksheet*

To create this dashboard, add the Initial Admin dimension to Columns, and Initial Admin measure of count to Rows. Click on Show Me and change it to Packed Bubbles. Update the Labels mark to display the percentage of total for each bubble. Edit the names of each admission type to “Elective,” “Observation,” and “Emergency Room.” 

*Age Group by Readmission worksheet*

To create this dashboard, create a calculated field as such:

![IMG_1648](https://github.com/user-attachments/assets/4f54145f-33dc-4e71-a11f-c601a3f2d5cc)

Add this new field “Age Group” to Columns, and the count of Age to Rows. Add Readmission to the Color mark. 

Edit the Tooltip as such:

![IMG_1649](https://github.com/user-attachments/assets/4e2db2d4-bcb9-4c91-8d67-0c6646037bad)

Ensure that the percentage of Age count is calculated down and not across. Format the labels to display percentages with 2 decimal points. 

*Gender & Age Readmissions worksheet*

To create this dashboard, add the aforementioned Age Group field to Columns, and the Gender dimension to Rows. Then add count of Age to the Label and Color mark. Update the chart to Square. Also add the Percent of Total (across) from Age count to the Label mark.  

## DASHBOARD NAVIGATION

To navigate this dashboard, start out by selecting either story point at the top: Patient Analysis or Population Analysis. You can use the arrows to click back and forth between the two boards. On the Population Analysis Dashboard, the three graphs are all linked together, so that when you click on any one thing, the other graphs will adjust based on what you clicked. Try it out, click on Texas in the map and watch the other graphs alter to display only Texas’ information. You can even stack things together, by clicking on multiple things at a time. The Growth table stays the same, and is not linked. 

On the Patient Analysis Dashboard, each of the three graphs here are also linked. You can click any object/shape on any graph and each of the other two will adjust to match. Also, you can use the drop-down menus at the top of the page. Again, the options are stackable. For example, you can look at how many 50-year old males who were initially admitted via the ER were then readmitted. 

## SQL CODE
```sql
CREATE TABLE population (
	Rank VARCHAR(2),
	State VARCHAR(2),
	"2010_Population" INTEGER,
	"2018_Population" INTEGER,
	Current_Population INTEGER,
	Growth_Since_2010 DECIMAL,
	Growth_Since_2018 DECIMAL,
	Percent_of_Total_Population DECIMAL,
	Population_Density DECIMAL,
	PRIMARY KEY (Rank))

COPY population
FROM 'C:\Users\Public\Downloads\populationByState.csv'
DELIMITER ','
CSV HEADER;

SELECT p.lat, p.lng, p.population, p.children, p.age, p.income, p.marital,
    CASE WHEN p.readmis = 'Yes' THEN 1 ELSE 0 END AS readmis,
    p.gender, p.initial_days, p.totalcharge, p.additional_charges, p.vitd_levels,
    p.doc_visits, p.full_meals, p.vitd_supp, 
    CASE WHEN p.soft_drink = 'Yes' THEN 1 ELSE 0 END AS soft_drink,
    CASE WHEN p.hignblood = 'Yes' THEN 1 ELSE 0 END AS high_blood_pressure,
    CASE WHEN p.stroke = 'Yes' THEN 1 ELSE 0 END AS stroke,
    a.initial_admission,
    c.complication_risk,
    l.state, l.county, l.city, l.zip,
    svc.services, 
    CASE WHEN svc.overweight = 'Yes' THEN 1 ELSE 0 END AS overweight,
    CASE WHEN svc.arthritis = 'Yes' THEN 1 ELSE 0 END AS arthritis,
    CASE WHEN svc.diabetes = 'Yes' THEN 1 ELSE 0 END AS diabetes,
    CASE WHEN svc.hyperlipidemia = 'Yes' THEN 1 ELSE 0 END AS hyperlipidemia,
    CASE WHEN svc.backpain = 'Yes' THEN 1 ELSE 0 END AS backpain,
    CASE WHEN svc.anxiety = 'Yes' THEN 1 ELSE 0 END AS anxiety,
    CASE WHEN svc.allergic_rhinitis = 'Yes' THEN 1 ELSE 0 END AS allergic_rhinitis,
    CASE WHEN svc.reflux_esophagitis = 'Yes' THEN 1 ELSE 0 END AS reflux_esophagitis,
    CASE WHEN svc.asthma = 'Yes' THEN 1 ELSE 0 END AS asthma,
    pop.Rank, pop."2010_Population", pop."2018_Population", pop.Current_Population, pop.Growth_Since_2010,
    pop.Growth_Since_2018, pop.Percent_of_Total_Population, pop.Population_Density
FROM patient p
LEFT JOIN admission a
    ON p.admis_id = a.admins_id
LEFT JOIN complication c
    ON p.compl_id = c.complication_id
LEFT JOIN location l
    ON p.location_id = l.location_id
LEFT JOIN servicesaddon svc
    ON p.patient_id = svc.patient_id
LEFT JOIN population pop
    ON l.state = pop.state
```

## PART B: WRITTEN REPORT

### DASHBOARD ALIGNMENT

The data dictionary details that hospitals are penalized for excessive readmissions. As an analyst, it is my job to deliver a solution that helps the executive leaders at these institutions be able to reduce readmission rates which in turn reduces or even eliminates their penalties from the Centers for Medicare and Medicaid Services (CMS). 

With that in mind, I created several visualizations that all display different metrics for patient readmissions. I chose to display the top 10 states across the United States of all patient readmissions. Next, I displayed gender, then age. I also displayed how the patient was initially admitted to the hospital. I looked at patient medical conditions, and displayed population data as well. All these visualizations are set to only show if the patient was readmitted, therefore, the executive leaders can use this to understand more about the patients that are being readmitted and can set their sights on these in the future to lower readmission rates. 

### BUSINESS INTELLIGENCE TOOL

I chose to use Tableau for this project since they are one of the leaders in data visualization tools. Tableau is easy to use and can handle many complex datasets to display beautiful graphs. It can further analytics by allowing the user to click around easily to look at specific data. The dashboards are interactive and are easy to use by anyone without much training. 

### DATA CLEANING

I used Python to review both datasets, and checked for missing and duplicated values. I found none in either dataset. I wanted to be able to transform the variables with “Yes/No” responses as integers, so I used a CASE statement in my SQL query to change them to 1’s and 0’s. This way I can display counts better in my Tableau graphs. 

### DASHBOARD CREATION

I have outlined above how each worksheet was created, which is the bulk of what makes up the dashboards. I created two separate dashboards, and then dragged each relevant worksheet onto the pane. I then removed some of the filters that weren’t necessary, and hid the titles and headers to make room on the page. I also made each remaining filter a floating filter, and positioned it carefully on the dashboard to not cover up anything important. I did this for both dashboards. 

### DATA ANALYSIS RESULTS

There are many analytical results that are presented in my dashboards. I outlined which medical conditions in patients are more likely to be present in a readmission. The top 2 are overweight and high blood pressure which are more likely mutually exclusive medical conditions due to a patient’s BMI and overall health. Going down the list, back pain could also be directly related to a patient being overweight. The same could be also said for reflux. There is a high correlation to these medical conditions, so by focusing on a patient’s BMI the hospital has a direct connection to lowering patient readmission. 

Also, I displayed the top 10 states that have readmissions so the hospitals can focus on the highest offending institutions in those states to get the most bang for their buck. Florida would be a positive state to highlight in their further analysis as it is the 3rd highest population, but 3rd lowest in patient readmissions (based on top 10 states). Alternatively, Pennsylvania would be a negative state to highlight as they have one-third the population of the 2nd largest state (Texas), yet they are the 2nd highest state for readmissions. Actually, they have the highest number of overweight readmissions in the entire dataset, six times higher than Texas per capita. 

Looking specifically at patient data, we see that when a patient is admitted initially via the Emergency Room they are more than two times more likely to be readmitted than patients being admitted elsewhere. We also see that patients in their fifties are less likely to be readmitted than patient who are younger, in their thirties and forties. Further analysis into fifty-year-olds could reveal pertinent data that could lead the hospitals to lower admission rates in younger patients. Younger males up to thirties are more prone for readmissions than females, but once in their forties females have higher readmission rates. The highest readmission rates in males was among thirty-year-olds, whereas the highest in women was seventy-year-olds. 

### ANALYSIS LIMITATIONS

As with any data analysis, the data can tell countless stories that are seemingly endless. However, so as to not overwhelm your audience, you really need to be concise in your approach to a story you are trying to tell. For that reason, this data analysis is limited in several ways. First of all, I am only looking at the population data from the top 10 states, therefore, leaving out 40 other states that more than likely have very useful data as well. I also am filtering down to only patients who were readmitted, but you could learn a lot from non-readmitted patients as well. We’re also missing a lot of key demographic information and medical data that could be very relevant that could paint a broader picture here. However, we only have the attributes in the dataset that we’re using, so in that way we are limited as well. Regardless of limitations, we can still derive much from the datasets provided and can certainly provide a strong analysis that will give the executive leadership at this hospital system feedback on how to improve their patient readmissions. 

## SUPPORTING DOCUMENTATION

### WEB SOURCES 

Vardrup, K (2024, Jan 16). Mastering the Art of Storytelling for Stakeholder Engagement. Retrieved on April 21, 2024, from Mastering the Art of Storytelling for Stakeholder Engagement (trainingmag.com).

Tableau (N.D.). Connect to a Custom SQL Query. Retrieved on June 11, 2024, from https://help.tableau.com/current/pro/desktop/en-us/customsql.htm

### SOURCES

Knaflic, C.N. (2015). Storytelling with Data: A Data Visualization Guide for Business Professionals. John Wiley & Sons, Incorporated.

Wexler, S., Shaffer, J., Cotgreave, A. (2017). The Big Book of Dashboards: Visualizing Your Data Using Real-World Business Scenarios. John Wiley & Sons, Incorporated.

Loth, A. (2019, May 7). Visual Analytics with Tableau. John Wiley & Sons, Incorporated.
