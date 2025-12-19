*Azure Data Factory – Customer Data Cleansing & Validation (Data Flow)*

*Overview*

This project focuses on building a data cleansing and validation workflow using Azure Data Factory Mapping Data Flows. It showcases how to detect common data quality issues, classify customer records as valid(GoodRecords) or invalid(BadRecords), return the reason for rejection for invalid entries, and load the outputs into Azure SQL Database for structured downstream use.

A CSV stored in Azure Data Lake Storage contains customer details with quality issues such as:

	•	Duplicate rows
	•	Missing values in phone numbers
	•	Incorrect date formats

The goal was to automatically identify these issues, classify the records as Good Records or Bad Records, return the reason for rejection for invalid entries, and store them in separate SQL tables.


*Input Schema*

The dataset includes:

	•	CustomerID
	•	CustomerName
	•	CustomerLocation
	•	CustomerPhoneNumber
	•	CustomerJoinDate


*Data Flow Design*

 Source
 
	•	Connected to Azure Data Lake Storage (CSV)
	•	Adjusted projection:
	•	CustomerID → integer
	•	CustomerPhoneNumber → long

 Window Transformation

	•	Sorted by CustomerID
	•	Applied rowNumber() to flag duplicates

 Derived Column
 
 Created validation flags:
 
	•	phoneNumberNullFlag — identifies missing phone numbers
	•	dateFormatFlag — validates join date format using toDate()

 Conditional Split

 Separated the dataset into:

 Good Records
 
    •	rowNumber == 1 && 
    •	phoneNumberNullFlag == 'no' &&
    •	dateFormatFlag == 'no'
  
 Bad Records
 
	 •	Rows that are duplicates
	 •	Rows with null phone numbers
	 •	Rows with incorrect date formats

 Derived Column for Bad Records

 Added rejectReason to indicate why the record was rejected:
 
	  •	Duplicate row
	  •	Null phone number
	  •	Incorrect date format

 Select & Sink
 
	  •	Removed helper validation columns
	  •	Loaded both Good and Bad records into Azure SQL Database
	  •	Enabled table recreation during sink configuration

Pipeline

A pipeline was used to orchestrate and trigger the Data Flow execution.

*Key Concepts Practiced*

	  •	Window functions for detecting duplicates
	  •	Validation using derived columns
	  •	Conditional splitting of datasets
	  •	Returning rejection reasons for bad records
	  •	Loading curated datasets into SQL sinks


*Azure Services Used*

	•	Azure Data Factory
	•	Mapping Data Flows
	•	Azure Data Lake Storage
	•	Azure SQL Database
