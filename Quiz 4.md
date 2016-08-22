#Question 1

The American Community Survey distributes downloadable data about United States communities. Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here. And load the data into R. The code book, describing the variable names is here. Apply strsplit() to split all the names of the data frame on the characters “wgtp”. What is the value of the 123 element of the resulting list?

* “wgt” “15”

* “” “15”

* “wgtp” “15”

* “wgtp”
```r
# download data
if(!file.exists("./data")) dir.create("./data")
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
download.file(fileUrl, destfile = "./data/ACS.csv")
# load data into R
acs <- read.csv("./data/ACS.csv")
# split data name.
strsplit(names(acs), split = "wgtp")[[123]]
```
# Question 2

Load the Gross Domestic Product data for the 190 ranked countries in this data set here. Remove the commas from the GDP numbers in millions of dollars and average them. What is the average? Original data sources is here.

* 381615.4

* 387854.4

* 377652.4

* 381668.9
```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv"
download.file(fileUrl, destfile = "./data/GDP.csv")
gdp <- read.csv("./data/GDP.csv", skip = 4, nrows = 190, stringsAsFactors = FALSE)[,c(1, 2, 4, 5)]
colnames(gdp) = c("CountryCode", "Ranking", "Economy", "GDP")
obj <- gsub(",", "", gdp$GDP)
obj <- as.numeric(obj)
mean(obj, na.rm = TRUE)
```
#Question 3

In the data set from Question 2 what is a regular expression that would allow you to count the number of countries whose name begins with “United”? Assume that the variable with the country names in it is named countryNames. How many countries begin with United?

* grep("^United",countryNames), 3

* grep("United$",countryNames), 3

* grep("^United",countryNames), 4

* grep("*United",countryNames), 2

```r
grep("^United", gdp$Economy)
```
# Question 4

Load the Gross Domestic Product data for the 190 ranked countries in this data set here.

Load the educational data from this data set here. Match the data based on the country shortcode. Of the countries for which the end of the fiscal year is available, how many end in June?

Original data sources are here and here.
```r
# download data
fileUrl1 <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv"
fileUrl2 <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv"
download.file(fileUrl1, destfile = "./data/GDP.csv")
download.file(fileUrl2, destfile = "./data/EDU.csv")
# load data into R
gdp <- read.csv("./data/GDP.csv", skip = 4, nrow = 190, stringsAsFactors = FALSE)[,c(1,2,4,5)]
colnames(gdp) = c("CountryCode", "Ranking", "Economy", "GDP")
edu <- read.csv("./data/EDU.csv")
# merge data
mergeData <- merge(gdp, edu, by = "CountryCode")
# result 
indexFiscal <- grep("fiscal", tolower(mergeData$Special.Notes))
sum(grepl("june", tolower(mergeData$Special.Notes[indexFiscal])))
```

# Question 5

You can use the quantmod (http://www.quantmod.com/) package to get historical stock prices for publicly traded companies on the NASDAQ and NYSE. Use the following code to download data on Amazon’s stock price and get the times the data was sampled.
```r
library(quantmod)
amzn = getSymbols("AMZN",auto.assign=FALSE)
sampleTimes = index(amzn)
```
How many values were collected in 2012? How many values were collected on Mondays in 2012?

* 252, 47

* 251, 47

* 252, 50

* 250, 47
```r
# load data
library(lubridate)
library(quantmod)
amzn <- getSymbols("AMZN", auto.assign=FALSE)
sampleTimes <- index(amzn)
# result
index2012 <- grep("2012", sampleTimes)
length(index2012)
sum(grepl("Mon", wday(sampleTimes[index2012], label = TRUE)))
```
