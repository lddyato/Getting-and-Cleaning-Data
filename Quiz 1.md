# Getting-and-Cleaning-Data  
## Question 1
The American Community Survey distributes downloadable data about United States communities.   
Download the 2006 microdata survey about housing for the state of Idaho using `download.file()` from here:  
<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv>  
and load the data into R. The code book, describing the variable names is here:  
<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf>  
How many properties are worth $1,000,000 or more?  

**Answer:**  

```r
>getwd()  
[1] "D:/coursera/Getting and cleaning data"  
> fileurl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
> download.file(fileurl, destfile= "D:/coursera/Getting and cleaning data/2006survey")
试开URL’https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv'   
Content type 'text/csv' length 4246554 bytes (4.0 MB)  
downloaded 4.0 MB`  
> list.files()   
[1] "2006survey"                        
[2] "getdata%2Fdata%2FPUMSDataDict06.pdf"  
> housing <- read.csv("D:/coursera/Getting and cleaning data/2006survey")
> ncol(housing)
[1] 188
> nrow(housing)
[1] 6496
> nrow(housing[which(housing$VAL == 24), ])
[1] 53
```
## Question 2
Use the data you loaded from Question 1. Consider the variable *FES* in the code book.  
Which of the "tidy data" principles does this variable violate?  

Tidy data has variable values that are internally consistent.  
**Tidy data has one variable per column.**  
Tidy data has no missing values.  
Tidy data has one observation per row.  

## Question 3
Download the Excel spreadsheet on Natural Gas Aquisition Program here:  
<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FDATA.gov_NGAP.xlsx>  
Read rows 18-23 and columns 7-15 into R and assign the result to a variable called:  

`dat`  

What is the value of:   

`sum(dat$Zip*dat$Ext,na.rm=T)`  

(original data source: <http://catalog.data.gov/dataset/natural-gas-acquisition-program>)

**Answer:**
```R
>filexlsx <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FDATA.gov_NGAP.xlsx"
>download.file(filexlsx, destfile="D:/coursera/Getting and cleaning data/dataNGAP.xlsx", method="curl")
> colIndex <- 7:15
> rowIndex <- 18:23
> dat <- read.xlsx("D:/coursera/Getting and cleaning data/dataNGAP.xlsx", sheetIndex=1, colIndex=colIndex,   
                    rowIndex=rowIndex, header=TRUE)
> sum(dat$Zip*dat$Ext,na.rm=T)
[1] 36534720
```
## Question 4
Read the XML data on Baltimore restaurants from here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Frestaurants.xml>

How many restaurants have zipcode 21231?

**Answer:**

```r
> fileUrl <- "http://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Frestaurants.xml"
> download.file(fileUrl, destfile="D:/coursera/Getting and cleaning data/restaurants.xml")
>install.packages("XML")
> library(XML)
> doc <- xmlTreeParse("D:/coursera/Getting and cleaning data/restaurants.xml", useInternal=TRUE)
#if there is saying that this doesn't seem to be xml, then remove the letter "s" from "https",   
#and use the "http" instead
> rootNode <- xmlRoot(doc)
> sum(xpathSApply(rootNode, "//zipcode", xmlValue)==21231)
> [1] 127
```
## Question 5
The American Community Survey distributes downloadable data about United States communities.   
Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv>

using the `fread()` command load the data into an R object

 `DT`  
Which of the following is the fastest way to calculate the average value of the variable  

`pwgtp15`  
broken down by sex using the data.table package?  

**Answer:**
```r
> fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv"
> download.file(fileUrl, destfile="D:/coursera/Getting and cleaning data/pid.csv", method="curl")
> library(data.table)
> DT <- fread("D:/coursera/Getting and cleaning data/pid.csv")
> file.info("D:/coursera/Getting and cleaning data/pid.csv")$size
> system.time(mean(DT[DT$SEX==1,]$pwgtp15))+system.time(mean(DT[DT$SEX==2,]$pwgtp15))
> system.time(sapply(split(DT$pwgtp15,DT$SEX),mean))
> system.time(tapply(DT$pwgtp15,DT$SEX,mean))
> system.time(rowMeans(DT)[DT$SEX==1])+system.time(rowMeans(DT)[DT$SEX==2])
```



