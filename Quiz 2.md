# Getting-and-Cleaning-Data
## Question 1
Register an application with the Github API here 

<https://github.com/settings/applications>.   

Access the API to get information on your instructors repositories   
(hint: this is the url you want "<https://api.github.com/users/jtleek/repos>").   
Use this data to find the time that the datasharing repo was created.   
What time was it created?  

This tutorial may be useful (<https://github.com/hadley/httr/blob/master/demo/oauth2-github.r>).  
You may also need to run the code in the base R package and not R studio.

**Answer：**
```R
library(httr)

# 1. Find OAuth settings for github:
#    http://developer.github.com/v3/oauth/
oauth_endpoints("github")

# 2. To make your own application, register at at
#    https://github.com/settings/applications. Use any URL for the homepage URL
#    (http://github.com> is fine) and  http://localhost:1410 as the callback url
#
#    Replace your key and secret below.
myapp <- oauth_app("github",
  key = "56b637a5baffac62cad9",
  secret = "8e107541ae1791259e9987d544ca568633da2ebf")

# 3. Get OAuth credentials
github_token <- oauth2.0_token(oauth_endpoints("github"), myapp)

# 4. Use API
gtoken <- config(token = github_token)
req <- GET("https://api.github.com/rate_limit", gtoken)
stop_for_status(req)

#5. Extract out the content from the request
json1 = content(req)
json2 = jsonlite::fromJSON(jsonlite::toJSON(json1)) #convert the list to json (data frame)
#jsonlite::toJSON(json1) put json1 into one looooooong line
json2[json2$full_name == "jtleek/datasharing", ]$created_at
#[[1]]
#[1] "2013-11-07T13:25:07Z"
json2[json2$full_name == "jtleek/datasharing", ]$pushed_at
#[[1]]
#[1] "2016-08-16T08:38:51Z"
```


## Question 2
The sqldf package allows for execution of SQL commands on R data frames.   
We will use the `sqldf` package to practice the queries we might send with the dbSendQuery command in RMySQL.

Download the American Community Survey data and load it into an R object called

```
acs
```
<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv>

Which of the following commands will select only the data for the probability weights 
pwgtp1 with ages less than 50?


sqldf("select * from acs where AGEP < 50 and pwgtp1")

sqldf("select * from acs")

**sqldf("select pwgtp1 from acs where AGEP < 50")**

sqldf("select pwgtp1 from acs")
```r
# load package: sqldf is short for SQL select for data frame.
library(sqldf)
# 1. download data 
download.file(url = "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv", destfile = "./data/acs.csv")
# 2. read data
acs <- read.csv("./data/acs.csv")
# 3. select using sqldf
#sqldf("select pwgtp1 from acs where AGEP<50", drv='SQLite')
```

## Question 3
Using the same data frame you created in the previous problem,  
what is the equivalent function to unique(acs$AGEP)

sqldf("select distinct pwgtp1 from acs")

**sqldf("select distinct AGEP from acs")**

sqldf("select unique * from acs")

sqldf("select AGEP where unique from acs")
```r
result <- sqldf("select distinct AGEP from acs", drv = "SQLite")
nrow(result)
length(unique(acs$AGEP))
```

## Question 4
How many characters are in the 10th, 20th, 30th and 100th lines of HTML from this page:

<http://biostat.jhsph.edu/~jleek/contact.html>

(Hint: the `nchar()` function in R may be helpful)

```r
con <- url("http://biostat.jhsph.edu/~jleek/contact.html")
htmlcode = readLines(con)
close(con)
sapply(htmlcode[c(10, 20, 30, 100)], nchar)
```

## Question 5
Read this data set into R and report the sum of the numbers in the fourth of the nine columns.

<https://d396qusza40orc.cloudfront.net/getdata%2Fwksst8110.for>

Original source of the data: <http://www.cpc.ncep.noaa.gov/data/indices/wksst8110.for>

(Hint this is a fixed width file format)
```r
# 1. read data
data <- read.fwf(file = "https://d396qusza40orc.cloudfront.net/getdata%2Fwksst8110.for",
                 skip = 4,
                 widths = c(12, 7,4, 9,4, 9,4, 9,4))
# 2. result
sum(as.numeric(data[,4]))
```
