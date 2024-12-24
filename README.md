# Hospital-Patient-Waitlist-Analysis-with-Power-BI

Waitlists.

In this project, I analyze healthcare data for three different patient groups to see how the waitlist varies between medical specialties, patient types, and age groups.

# 1. Define the Problem
Management at the hospital wants to understand how the waitlist varies. My goal for this project is to analyze the given Healthcare data and provide information and insights regarding the waitlists so that they can better manage their hospital.

The information they require includes:

Tracking the current status of patient waitlists.
Analyzing the historical monthly trend of waiting lists in Inpatient and Outpatient categories.
Providing detailed specialty-level and age profile analysis.
Determining the average and median waitlist.
Determining the current total waitlist.

# 2. Collecting the Data
The Healthcare Waitlist data we are provided with includes 4 CSV tables for Inpatient data, and 4 CSV tables for Outpatient data. 

The scope of data is from January 2018 to March 2021; each year represents one CSV file per patient type.

Inpatients are further classified as Inpatients or Day Cases.
Here are some important definitions for the fields in our datasets:

> Outpatient is a patient who comes in only for a short appointment or consultation.
> Inpatient is a patient who stays in the hospital for one or more nights.
> Day Case is a patient who comes in for a more involved procedure than an Outpatient, but can go home the same day.
> Archive_Date is the date of record.
> Specialty_HIPE is the specialty code.
> Specialty is the medical specialty being waited for.
> Case_Type refers to the type of patient.
> Age_Profile is the age group of the person on the waitlist.
> Time_Bands are the waitlist time intervals.
![image](https://github.com/user-attachments/assets/b408c845-51b2-476b-a21e-e19ef48be33a)
# 3. Preparing the Data
There are 8 patient waitlist files in total, corresponding to the years 2018 to 2021. Taking a cursory look at the datasets, we can see that all four of the inpatient files have the same structure; the four outpatient files also have the same structure. This means that we can append the four files for each patient type using Power BI.

Before we do that, though, we notice that the inpatient and outpatient files are also similar, with a few minor differences (see snapshots above for reference):

Specialty_Name (Inpatient data) and Speciality (Outpatient data) refer to the same fields but are labeled differently.
Case_Type in the Outpatient data is missing -- this is because Outpatient is the only case type.

We can see that appending all of the data would be viable in Power BI to obtain one unified data source, but we first need to tidy up the two sets of data so that they match:

The Speciality field in the Outpatient files was renamed to "Specialty_Name" to match the inpatient field.
A new field called "Case_Type" was added to the Outpatient data, with all rows containing the value "Outpatient."

Other changes to the data that I made were:

Changing the data type for Archive_Date to the proper date format as it was defaulting to text. 
Updating the Time_Band and Age_Profile fields to remove the extra spaces and formatting issues causing duplicates. For example, there were time bands showing "18 Months +" and "18+ Months" when they are the same thing, just written differently.

Once that was fixed, the files were ready to be appended together to create a unified data source called All_data:

The data reflects many specialty names, and our stakeholder wants us to classify them based on groups. They provided a file called Mapping_Specialty which we can load into Power BI.


![image](https://github.com/user-attachments/assets/8d7c37b9-1186-43f9-9205-7f56a9002066)
 
 
# 4. Analyzing the Data
We need to create two pages in Power BI: 

a Summary page
a Detailed/Granular page



For the Summary page, we want to create a few different charts:

Total Current Month Waitlist vs. Total Previous Month Waitlist

We want a card that shows total current month waitlist vs total previous month waitlist, and we want it to be dynamic. So we can’t just use the "Total" field in the card; we need to create a new metric to make it dynamic.

We have to use DAX and create new measures:

DAX measure for last month's waitlist:

Last Month Waitlist = CALCULATE(SUM(All_Data[Total]), All_Data[Archive_Date]=MAX(All_Data[Archive_Date])) + 0
DAX measure for same month, last year's wait list:

PY Latest Month Waitlist = CALCULATE(SUM(All_Data[Total]), All_Data[Archive_Date]=EDATE(MAX(All_Data[Archive_Date]), -12)) + 0
Some explanation:

"PY" means the previous year.
"MAX" is to obtain the latest month in the Archive_Date field (which varies based on the Archive_Date filter we will add to the dashboard).
"EDATE" enables us to move back a specified number of months.
"-12" is how far back we want to go, so back 12 months (i.e. last year).
"+0" is to show all blank values as 0 if the date range selected has no data.

These are the cards we created using these metrics:


2. Average vs. Median Toggle buttons

Next, we want to create a few more charts but we don’t want to just look at the average, as we have seen some outliers in the data. We want to show median as well, and to show both in the charts we need to have a toggle for the average and median.

After creating two slicers called "Average" and "Median," we will create two new DAX measures for Average Waiting List and Median Waiting List:

Average Waitlist = AVERAGE(All_Data[Total])
Median Waitlist = Median(All_Data[Total])
Now create one more measure that will allow us to interact with the slicer buttons:

Avg/Med Waitlist = SWITCH(VALUES('Calculation Method'[Calc Method]), "Average", [Average Waitlist], "Median", [Median Waitlist])
This is what our toggle button looks like:

# 5. Sharing the Insights
Some important findings when reviewing the average and median waitlist statistics over the past few years are:

The number of people on a waitlist has increased dramatically since the previous year (640K last year vs. 709K for the current year).
Most patients on a waitlist are outpatients.
The highest waitlist periods are between 0 to 3 months and 18+ months, based on median waitlist.
The waitlist for Inpatients and Day Cases have remained pretty steady over the past few years, while the number of Outpatients on a waitlist has continuedt to increase over the same period.
The top 5 highest specialties based on median waitlist are: Accident & Emergency, Dermatology, Clinical Genetics, Cardiology, and Pain Relief.

With these insights, these top five specialties should prioritize hiring more doctors and nurses to help with this clinic's high wait times and outpatients.

Click on the image below to view and explore the final Power BI dashboard:


![1720585635841](https://github.com/user-attachments/assets/bd437dcb-dc09-4a21-8199-e3cb19dbe04f)


