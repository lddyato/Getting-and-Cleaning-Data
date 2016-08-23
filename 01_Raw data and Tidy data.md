# Getting-and-Cleaning-Data
## Data
Data are values of qualitative or quantitative variables, belonging to a set of items

### Raw data
* The original source of the data
* Often hard to use for data analysis
* Data analysis includes processing
* Raw data may only need to be processed once

### Processed Data
* Data that is ready for analysis
* Processing can include merging, subsetting, transforming, etc.
* There may be standards for processing
* All steps should be recorded

### Components of Tidy Data
The four things you should have in going from raw to tidy:

1. raw data  
2. tidy data  
3. code book describing variables and values in tidy data set (metadata)  
4. explicit and exact recipe you used to go from 1 to 2 and 3   

**1. raw data**  
* The strange binary file your measurement machine spits out
* The unformatted Excel file with 10 worksheets the company you contracted with sent you
* The complicated JSON data you got from scraping the Twitter API
* The hand-entered numbers you collected looking through a microscope

You know the raw data is in the right form if you:
* Ran no software on the data  
* You did not manipulate any of the numbers in the data  
* You did not remove any data from the data set  
* You did not summarize any data in any way

**2. The tidy data**
+ Each variable you measure should be in one column
+ Each different ovservation of that variable should be in a diferent row
+ There should be one table for each "kind" of variable
+ If you have multiple tables, they should include a column in the table that allows them to be linked.

*Some other important tips*
- Include a row at the top of each file with variable names.
- Make variable names human readable AgeAtDiagnosis instead of AgeDx
- In general data should be saved in one file per table.

**3. Code Book**
* Information about the variables (including units) in the data set not contained in the tidy data
* Information about the summary choices you made
* Information about the experimental study design you used

*Some other important tips*
* A common format for this document is a Word/text file.
* There should be a section called "Study design" that has a thorough description of how you collected the data.
* There must be a section called "Code book" that describes each variable and its units.

**4. The instruction list**
* Ideally a computer script
* The input for the script is the raw data
* The output is the processed, tidy data
* There are no parameters to the script

In some cases it will not be possible to script every step/ In that case you should provide instructions like:    
*Step 1* - take the raw file, run version 3.1.2 of summarize software with parameters a=1, b=2, c=3  
*Step 2* - run the software separately for each sample  
*Step 3* - take column three of outputfile out for each sample and that is the corresponding row in the output data set.  

