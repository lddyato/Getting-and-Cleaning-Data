# Getting-and-Cleaning-Data
## Question 1
The American Community Survey distributes downloadable data about United States communities.   
Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv>

and load the data into R. The code book, describing the variable names is here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf>

Create a logical vector that identifies the households on greater than 10 acres who sold more than $10,000 worth of agriculture products. Assign that logical vector to the variable agricultureLogical. Apply the which() function like this to identify the rows of the data frame where the logical vector is TRUE.

which(agricultureLogical)

What are the first 3 values that result?
* 59, 460, 474   
* 125, 238,262  
* 403, 756, 798  
* 25, 36, 45   

**Answer:**
```r
if(!file.exists("D:/coursera/Getting and cleaning data/week3"))
        {dir.create("D:/coursera/Getting and cleaning data/week3")}
fileurl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
download.file(fileurl, destfile="D:/coursera/Getting and cleaning data/week3/hid.csv")
data <- read.csv("D:/coursera/Getting and cleaning data/week3/hid.csv")
data$agricultureLogical <- data$ACR>=3 & data$AGS >= 6
which(data$agricultureLogical == T)
```
## Question 2
Using the jpeg package read in the following picture of your instructor into R

<https://d396qusza40orc.cloudfront.net/getdata%2Fjeff.jpg>

Use the parameter native=TRUE. What are the 30th and 80th quantiles of the resulting data? (some Linux systems may produce an answer 638 different for the 30th quantile)  

* -16776430 -15390165  
* -10904118 -10575416   
* -15259150 -10575416  
* 10904118 -594524  

**Answer:**
```r
install.packages("jpeg")
library(jpeg)
fileurl1 <- "https://d396qusza40orc.cloudfront.net/getdata%2Fjeff.jpg"
download.file(fileurl1, destfile="D:/coursera/Getting and cleaning data/week3/jeff.jpg", mode="wb")
img <- readJPEG("D:/coursera/Getting and cleaning data/week3/jeff.jpg", native=TRUE)
quantile(img, probs=c(0.3, 0.8), na.rm=TRUE)
```

## Question 3
Load the Gross Domestic Product data for the 190 ranked countries in this data set:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv>

Load the educational data from this data set:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv>

Match the data based on the country shortcode. How many of the IDs match? Sort the data frame in descending order by GDP rank (so United States is last). What is the 13th country in the resulting data frame?

Original data sources:

<http://data.worldbank.org/data-catalog/GDP-ranking-table>

<http://data.worldbank.org/data-catalog/ed-stats>  
* 234 matches, 13th country is Spain  
* 190 matches, 13th country is Spain  
* 190 matches, 13th country is St. Kitts and Nevis  
* 189 matches, 13th country is St. Kitts and Nevis  
* 189 matches, 13th country is Spain   
* 234 matches, 13th country is St. Kitts and Nevis   

**Answer:**
```r
gdpurl <-"https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv"
eduurl <-"https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv"
download.file(gdpurl, destfile="G:/gdp.csv")
download.file(eduurl, destfile="G:/edu.csv")
gdp <- read.csv("G:/gdp.csv", skip = 4, nrow=190, stringsAsFactors = FALSE)
#skip the first 4 rows before reading data
edu <- read.csv("G:/edu.csv", stringsAsFactors = FALSE)
#if drop the "skip = 4, nrow = 190", then the result turns to 224, why?
mergedata <- merge(gdp, edu, by.x='X', by.y='CountryCode')
nrow(mergedata)
View(mergedata)
library(dplyr)
arrangedata <- arrange(mergedata, desc(X.1))
arrangedata[13, "X.3"]
```

## Question 4
What is the average GDP ranking for the "High income: OECD" and "High income: nonOECD" group?
* 30, 37  
* 23, 30  
* 32.96667, 91.91304  
* 23, 45  
* 23.966667, 30.91304  
* 133.72973, 32.96667   
 
**Answer:**
```r
group_income <- group_by(mergedata, as.factor(Income.Group))
summarise(group_income, avg = mean(X.1, na.rm=TRUE))
```

## Question 5
Cut the GDP ranking into 5 separate quantile groups. Make a table versus Income.Group. How many countries
are Lower middle income but among the 38 nations with highest GDP?
* 13
* 12
* 5
* 0

**Answer:**
```r
install.packages("Hmisc")
library(Hmisc)
mergedata$X.4 <- cut2(mergedata$X.1, g = 5)
table(mergedata$X.4, mergedata$Income.Group)
```
