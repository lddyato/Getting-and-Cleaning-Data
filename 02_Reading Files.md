# Downloading and Reading Data
## Get or set working directory
`getwd(), setwd()`

Relative versus absolute paths:
* **Relative**-`setwd("./data"), setwd("../")`
* **Absolute**-`setwd("Users/jtleek/data/")`
* In Windows, `setwd("C:\\Users\\Andrew\\Downloads")`

## checking for and creating drectories
```r
if (!file.exists("directoryName") {
dir.create("directoryName") }
```
## Getting data from the internet-download.file()
* Downloads a file from the internet
* Even if you could do this by hand, helps with reproducibility
* Important parameters are *url, destfile, method*
* Useful for downloading tab-delimited, csv, and other files.
http://data.baltimorecity.gov/Transportation/Baltimore-Fixed-Speed-Cameras/dz54-2aru
```r
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl") 
# if the url starts with *https* on Mac, you may need to set method="curl"
list.files("./data")
dateDownloaded <- date() # Be sure to record when you downloaded
```

## Reading local flat files- read.table()
* This is the main function for reading data into R
* Flexible and robust but requires more parameters
* Reads the data into RAM - big data can cause problems
* Important parameters `file, header, sep, row.names, nrows`
* Related: `read.csv(), read.csv2()`

```r
if (!file.exists("data") {
dir.create("data") }
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl")   
# if the url starts with *https* on Mac, you may need to set method="curl"
dateDownloaded <- date() # Be sure to record when you downloaded
cameraData <- read.table("./data/cameras.csv", sep = ",", header = TRUE) 
cameraData <- read.csv("./data/cameras.csv") #read.csv sets sep="," and header = TRUE
head(cameraData)
```
*Some more important parameters*   
* `quote` - you can tell R whether there are any quoted values quote = "" means no quotes
* `na.strings` - set the character that represents a missing value. The default is `na.strings=“NA”`, `WQ1=read.table("wq.txt",header=T,na.strings=9999)` # 9999 reprents missing values or "." reprents missing value in SAS
* `nrows` - how many rows to read of the file. The default is `nrows = -1`
* `skip` - number of lines to skip before starting to read. The skipped rows include the variable numbers. so in general, 
it always used by combining with the `col.names`, for example, `WQ4=read.table("wq.txt",header=T,skip=2,col.names=c("A","B","C","D"))`
* `check.names` - renames the repeated variable names, such as, B and B.1 if you set `check.names=T`
* `dec` - decimal. The default is `dec=”."`. If the data values reprents with comma, set `dec=","` 
```r
read.table(file, header = FALSE, sep = "", quote = "\"'",
           dec = ".", row.names, col.names,
           as.is = !stringsAsFactors,
           na.strings = "NA", colClasses = NA, nrows = -1,
           skip = 0, check.names = TRUE, fill = !blank.lines.skip,
           strip.white = FALSE, blank.lines.skip = TRUE,
           comment.char = "#",
           allowEscapes = FALSE, flush = FALSE,
           stringsAsFactors = default.stringsAsFactors(),
           fileEncoding = "", encoding = "unknown", text)

read.csv(file, header = TRUE, sep = ",", quote="\"", dec=".", fill = TRUE, comment.char="", ...)

read.csv2(file, header = TRUE, sep = ";", quote="\"", dec=",", fill = TRUE, comment.char="", ...)

read.delim(file, header = TRUE, sep = "\t", quote="\"", dec=".", fill = TRUE, comment.char="", ...)

read.delim2(file, header = TRUE, sep = "\t", quote="\"", dec=",", fill = TRUE, comment.char="", ...)
```
## fread() VS read.table()
Similar to **`read.table()`** but faster and more convenient. 
All controls such as sep, colClasses and nrows are automatically detected. 
bit64::integer64 types are also detected and read directly without needing to read as character before converting.

```r
fread(input, sep="auto", sep2="auto", nrows=-1L, header="auto", na.strings="NA",
stringsAsFactors=FALSE, verbose=getOption("datatable.verbose"), autostart=1L,
skip=0L, select=NULL, drop=NULL, colClasses=NULL,
integer64=getOption("datatable.integer64"),         # default: "integer64"
dec=if (sep!=".") "." else ",", col.names, 
check.names=FALSE, encoding="unknown", strip.white=TRUE, 
showProgress=getOption("datatable.showProgress"),   # default: TRUE
data.table=getOption("datatable.fread.datatable")   # default: TRUE
)
```
```r
> fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv"
> download.file(fileUrl, destfile="D:./pid.csv", method="curl")
> library(data.table)
> DT <- fread("D:./pid.csv")
# DT <- read.table("./pid.csv", sep = ",", header = TRUE) 
```
## Reading Excel files
still probably the mose widely used format for sharing data
```r
if (!file.exists("data") {
dir.create("data") }
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl")   
# if the url starts with *https* on Mac, you may need to set method="curl"
dateDownloaded <- date() # Be sure to record when you downloaded
libaray(xlsx)
cameraData <- read.xlsx("./data/cameras.xlsx", sheetIndex = 1, header = TRUE)
head(cameraData)
```
**Reading sepcific rows and columns**
```r
colIndex <- 2:3
rowIndex <- 1:4
cameraDataSubset <- read.xlsx("./data/cameras.xlsx", sheetIndex = 1, colIndex = colIndex, rowIndex = rowIndex)
```
*Further Notes*
* The `write.xlsx` function will wirte out an Excel file with similar arguments
* `read.xlsx2` is much faster than *read.xlsx* but for reading subsets of rows may be slightly unstable
* The `XLConnect` package has more options for writing and manipulating Excel files
* The `XLConnect vignette` is a good place to start for that package  

In general it is adviced to store your data in either a database or in (.csv) or (.tab/txt) as they are easier to distribute

## Reading XML
**XML**
* Extensible markup language
* Frequently used to store structured data
* Particularly widely used in internet applications
* Extracting XML is the basis for most web scraping
* Components:    
 + Markup - labels that give the text structure    
 + Content- the actual text of the document    

**Tags, elements and attributes**
* tags correspond to general labels, 
 + Start tages: <section> 
 + End tages </section> 
 + Empty tages <line-break />
* Elements are specific examples of tags
 + <Greeting> Hello, world </Greeting>
* Attributes are components of the label
 + <img src = "jeff.jpg" alt="instructor"/>
 + <step number="3"> Connect A to B. </step>

**XPath**
* `/node` Top level node
* `//node` Node at any level
* node[@attr-name] Node with an attribute name
* node[@attr-name='bob'] Node with an attribute name attr-name='bob'

```r
# The following is the doc 
<?xml version="1.0" encoding="UTF-8"?> #By adding this line this R code is doc, by deleting this line is a rootNode.
<breakfast_menu>
<food>
<name>Belgian Waffles</name>
<price>$5.95</price>
<description>
Two of our famous Belgian Waffles with plenty of real maple syrup
</description>
<calories>650</calories>
</food>
<food>
<name>Strawberry Belgian Waffles</name>
<price>$7.95</price>
<description>
Light Belgian waffles covered with strawberries and whipped cream
</description>
<calories>900</calories>
</food>
```
```r
library(XML)
fileurl <- "http://www.w3schools.com/xml/simple.xml"
doc <- xmlTreeParse(fileurl, useInternal = TRUE)
rootNode <- xmlRoot(doc) #wraps whole doc
xmlName(rootNode) # OUtput is "breakfast menu"
names(rootNode)  # tells nested elements, output is food food food ... food
rootNode[[1]] # directly access parts of the XML document,like extracting from a list, eg, the whole <food> ...</food> section
rootNode[[1]][[1]] # <name>Belgian Waffles</name>
rootNode[[1]][[2]] # <price>$5.95</price> 
xmlSApply(rootNode, xmlValue) # programmatically extract parts of the file, loops through to get all xmlValues, basically all text
#more specifically, use Xpath language-`/node`: top level node; `//node`: node at any level   
xpathSApply(rootNode, "//name", xmlValue) # get the name on the menu
xpathSApply(rootNode, "//prices", xmlValue) # get the prices on the menu
```
```r
fileurl <- "http://espn.go.com/nf1/team/_/name/bal/baltimore-ravens"
doc <- htmlTreeParse(fileurl, useInternal = TRUE)
scores <- xpathSApply(doc, "//li[@class='score']", xmlValue)
teams <- xpathSApply(doc, "//li[@class='team-name']", xmlValue)
```

xmlTreeParse does not support https... use instead:
```r
library(XML)
library(RCurl)
fileURL <- "http://www.w3schools.com/xml/simple.xml"
xmlFile <- getURL(fileURL)
```

## Reading JSON
* Javascript Object Notation
* Lightweight data storage
* Common format for data from application programming interfaces (APIs)
* Similar structures to XML but differenet syntax/format
* data stored as 
 + numbers(double), 
 + strings(souble quoted), 
 + boolean(TRUE or FALSE),
 + arrays(ordered, comma separated enclosed in square brackets[]), 
 + objects(unordered, comma separated collection of key value pairs in curley brackets{})

```r
library(jsonlite)
jsonData <- fromJSON("http://api.github.com/users/jtleek/repos") # reading data from JSON, puts in dataframe or nested dataframe
names(jsonData)
names(jsonData$owner)
names(jsonData$owner$login) # nested objects in JSON
myjson <- toJSON(iris, pretty = TRUE) # writing data frames to JSON
cat(myjson)  # see structure
iris2 <- fromJSON(myjson) # convert back to JSON
```

## Reading mySQL
* free, widely used open source db software
* structure: databases, tables (data frame) within, fields (columns) within; each row is a record
* install MySQL, then install RMySQL # Windows, more complicated... well, Mac Mavericks had problems too, no binaries, installed other ways
**Example**
UCSC database, http://genome.ucsc.edu/ (web-facing, use sparingly, do NOT delete, add, join etc and class probably will overload so don't use)
```r
# connecting and listing databases
ucscDb <- dbConnect(MySQL(), user="genome", host="genome-mysql.cse.ucsc.edu") # ucscDb is a handle
result <- dbGetQuery(ucscDb, "show databases"); dbDisconnect(ucscDb); # 'show databases' is SQL not R code
# connecting to hg19, one of the databases, and listing tables
hg19 <- dbConnect(MySQL(), user="genome", db="hg19", host="genome-mysql.cse.ucsc.edu")
allTables <- dbListTables(hg19)
length(allTables) # over 10949 tables
allTables[1:5] # the first 5 tables
dbListFields(hg19,"affyU133Plus2") # dimensions of a specific table "affyU133Plus2"
dbGetQuery(hg19, "select count(*) from affyU133Plus2") # 58463 columns in table "affyU133Plus2"
affyData <- dbReadQuery(hg19, "affyU133Plus2") #read from the table
head(affyData) # first 6 rows
query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3") 
# select a specific subset and stores query remotely in the database
affyMis <- fetch(query) 
quantile(affyMis$misMatches)
affyMisSmall <- fetch(query, n=10) 
dbClearResult(query) # to not suck down a huge chunk of data, using n=10... make sure to clear the query afterward
dim(affyMisSmall) # 10  22
dbDisconnect(hg19) # Dont forget to close the connection
```
*Note*
* do not delete, add or join things from ensembl. Only select.
* be careful with mysql commands 
* see <http://www.r-bloggers.com/mysql-and-r/>
* `dbConnect, dbGetQuery, dbListTables, dbListFields, dbReadQuery, dbSendQuery, dbClearResult, fetch (or dbFetch), dbDisconnect`
* REMEMBER TO CLOSE CONNECTIONS

## Reading from HDF5
* HDF5 for storing large datasets, structural datasets
* supports range of data types
* hdf = hierarchical data format
* groups containing 0 or more data sets and metadata: have a group header with group name and list of attributes; have a group symbol table with a list of objects in group
* datasets multidimensional array of data elements with metadata: have a header with name, datatype, dataspace, storage layout; have a data array with the data
* www.hdfgroup.org
```r
source("http://bioconductor.org/biocLite.R")
biocLite("rhdf5")
library(rhdf5)
created = h5createFile("example.h5")
#(primarily for genomics, but also has good big data packages. can use to interfact with any hdf5 data sets
#creating groups
created = h5createGroup("example.h5", "foo")
created = h5createGroup("example.h5", "bar")
created = h5createGroup("example.h5", "foo/foobar")
h5ls(("example.h5") # h5 then ls as in list, this lists files
#shows 3 groups, 0 is /, bar; 1 is /, foo; and 2 is /foo, foobar (group, name)
#writing to groups, say A is a matrix, B is an array
A = matrix(1:10, nr = 5, nc=2)
h5write(A, "example.h5", "foo/A") # adds another file in the h5ls in the /foo group
B = array(seq(0.1, 2.0, by=0.1), dim = c(5, 2, 2))
attr(B, "scale") <- "liter" 
h5write(B, "example.h5", "foo/foobar/BA") # adds another file in the h5ls in the /foo/foobar group, B name
h5ls("example.h5")
#writing a dataset, say df is a dataframe:
df = dataframe(1L:5L, seq(0, 1, length.out = 5), c("ab", "cde", "fghi", "s"), stringsAsFactors = FALSE)
h5write(df, "example.h5", "df") # adds another file in the h5ls, in top level or root group, named df
#reading data:
readA = h5read("example.h5", "foo/A")
readB = h5read("example.h5", "foo/foobar/B")
readdf = h5read("example.h5", "df")
#writing and reading chunks: 
h5write(c(12,13,14), "example.h5", "foo/A", index = list(1:3,1)) 
#note this OVERWRITES first 3 rows in col 1 of A:
# you can use index within h5read() as well
h5read("example.h5", "foo/A")
```
SUMMARY: hdf5 can be used to optimize reading / writing from disk in R. see tutorial in (I saved copy of pdf): <http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf>
also <http://www.hdfgroup.org/HDF5>

## Reading data from the web - readLines()
**Webscraping:** programatically extracting data from the HTML code of websites
* It can be a great way to get data, but be careful, sometimes against the terms of service
* attempting to read too many pages too quickly can get your IP address blocked
* robots.txt are at for example: http://www.facebook.com.br/robots.txt

**Getting data off webpages - readLines():**
```r
con = url("http://scholar.google.com/citations?user=HI-I6C0AAAAJ&h1=en")
htmlCode = readLines(con)
htmlCode # will print all on one line... you will want to parse this, eg with XML package:
# [1] "<?xml version=\"1.0\" encoding=\"UTF-8\"?>"                                                                        
# [2] "<breakfast_menu>"                                                                                                  
# [3] "\t<food>"                                                                                                          
# [4] "\t\t<name>Belgian Waffles</name>"                                                                                  
# [5] "\t\t<price>$5.95</price>"                                                                                          
# [6] "\t\t<description>Two of our famous Belgian Waffles with plenty of real maple syrup</description>"                  
## [7] "\t\t<calories>650</calories>"                                                                                      
# [8] "\t</food>"                                                                                                         
# ...
# [33] "</breakfast_menu>" 
close(con) #remember to close the connections
```

**Parsing with XML**
```r
library(XML)
url <- "http://scholar.google.com/citations?user=HI-I6C0AAAAJ&h1=en"
html <- htmlTreeParse(url, useInternalNodes=TRUE)
html
#<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "http://www.w3.org/TR/REC-html40/loose.dtd">
<?xml version="1.0" encoding="UTF-8"?><html><body><breakfast_menu><food><name>Belgian Waffles</name><price>$5.95</price><description>Two of our famous Belgian Waffles with plenty of real maple syrup</description><calories>650</calories></food> .......
# The output is in one line
xpathSApply(html, "//title", xmlValue)
xpathSApply(html, "//td[@id='col-citedby']", xmlValue)
```

**Alternative : Using `Get` from the httr package**
```r
library(httr)
html2 = GET(url)
content2 = content(html2,as="text")
parsedHtml = htmlParse(content2,asText=TRUE)
xpathSApply(parsedHtml, "//title", xmlValue) #"Jeff Leek - Google Scholar Citations"
```
**Accessing websites with passwords**
```r
pg1 = GET("http://httpbin.org/basic-auth/user/passwd")
#you will get 401 status message if you print result, whereas it will be 200 status with:
pg2 = GET("http://httpbin.org/basic-auth/user/passwd", authenticate("user", "passwd"))
names(pg2) # not names of users; 'content' is where the html code is
#Using handles: allows you to save authentication across additional calls, cookies get saved
google = handle("http://google.com")
pg1 = GET(handle=google,path="/")
pg2 = GET(handle=google,path="search")
```
see the cran for more on httr
also see R Bloggers for web scraping examples: http://www.r-bloggers.com/?s=Web+Scraping

## Reading from APIs
* APIs are where you can download data, with GET requests with specific url arguments
* you'll need to create a developer account for each one, eg for Twitter: <https://dev.twitter.com/docs/api/1/get/blocks/blocking>
* creating a Twitter app: https?//dev.twitter.com/apps

**Accessing Twitter from R**
```r
myapp = oauth_app("twitter", key = "yourKey", secret="yourSecret") # first parameter 'twitter' is your choice to make
sig = sign_oauth1.0(myapp, token="yourToken", token_secret="yourTokenSecret")
homeTL = GET("https://api.twitter.com/1.1/statuses/home_timeline.json", sig) # note how you're passing authentication here
```
**then to convert json object**
```r
json1 = content(homeTL) # recognizes and extracts json data
json2 = jsonlite::fromJSON(toJSON(json1)) # reformats json extract as a data frame
json2[1,1:4]
```
**what url to use?** 
see twitter documentation (<dev.twitter.com/docs/api/1.1/get/search/tweets, also has parameters list>. <dev.twitter.com/docs/api/1.1/overview> is not just your user info but more globally)
**In general, look at (Twitter) documentation**
* httr allows `GET, POST, PUT, DELETE` when authorized
* you can authenticate with a user name or a password
* most modern APIs use something like oauth for authentication
* httr works well with Facebook, Google, Twitter, Github, etc.
* this lecture demo is modeled from one on Github.

## Reading from other sources
**Google "data storage mechanism R package", eg. "MySQL R package"**   
**Interacting more directly with files**     
* file: open connection to text file
* url: open conn to url
* gzfile, bzfile (for .bz2): open connection to a .gz/.bz2 file 
* ?connections for more info.
* REMEMBER TO CLOSE CONNECTIONS
* 
**Foreign package**    
loads data from Minitab, S, SAS, SPSS< Stata, Systat, Weka, etc.
basically, `read.foo`, like`read.arff (Weka), read.xport(SAS), read.dta (Stata), read.mtp (Minitab), read.octave (Octave), read.spss (SPSS)`
**other database packages**    
`RPostgreSQL, RODBC (interfaces to databases incl MySQL, MS Access), RMongo`

## Reading images 
jpeg, readbitmap, png, EBImage (Bioconductor)

##reading GIS data
rdgal, rgeos, raster

##reading music data
from mp3s: tuneR, seewave

## Reading a table of fixed width formatted data into a data.frame-read.fwf()
```r
read.fwf(file, widths, header = FALSE, sep = "\t",
         skip = 0, row.names, col.names, n = -1,
         buffersize = 2000, fileEncoding = "", ...)
```
Multiline records are concatenated to a single line before processing.   
Fields that are of zero-width or are wholly beyond the end of the line in file are replaced by NA.      
Negative-width fields are used to indicate columns to be skipped, e.g., -5 to skip 5 columns.    
```r
# 1. read data
data <- read.fwf(file = "https://d396qusza40orc.cloudfront.net/getdata%2Fwksst8110.for",
                 skip = 4,
                 widths = c(12, 7, 4, 9, 4, 9, 4, 9, 4))
```
? how to change the varaible names
