# Downloading and Reading Data
## Get or set working directory
`getwd(), setwd()`
## checking for and creating drectories
```r

if (!file.exists("directoryName") {
dir.create("directoryName") }
```
## Download a file from the web
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
```
*Some more important parameters*   
* `quote` - you can tell R whether there are any quoted values quote = "" means no quotes
* `na.strings` - set the character that represents a missing value
* `nrows` - how many rows to read of the file
* `skip` - number of lines to skip before starting to read

## Reading Excel files
```r
if (!file.exists("data") {
dir.create("data") }
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl")   
# if the url starts with *https* on Mac, you may need to set method="curl"
dateDownloaded <- date() # Be sure to record when you downloaded
libaray(xlsx)
cameraData <- read.xlsx("./data/cameras.xlsx", sheetIndex = 1, header = TRUE)
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
> Markup - labels that give the text structure    
> Content- the actual text of the document    

**Tags, elements and attributes**
* tags correspond to general labels, example: <section> blah blah </section> - section is an element
* empty tags eg <line-break />  
* attributes are components of the label, eg <img src = "jeff.jpg" ... />

```r
library(XML)
fileurl <- "http://www.w3schools.com/xml/simple.xml"
doc <- xmlTreeParse(fileurl, useInternal = TRUE)
#xmlTreeParse does not support https... use instead:
#library(XML)
#library(RCurl)
#fileURL <- "..."
#xmlFile <- getURL(fileURL)
rootNode <- xmlRoot(doc) #wraps whole doc
xmlName(rootNode) # eg, breakfast menu
names(rootNode)  # tells nested elements
rootNode[[1]] # directly access parts of the XML document,like extracting from a list
rootNode[[1]][[1]]
xmlSApply(rootNode, xmlValue) # programmatically extract parts of the file, loops through to get all xmlValues, basically all text
#more specifically, use Xpath language-`/node`: top level node; `//node`: node at any level   
xpathSApply(rootNode, "//name", xmlValue) # get the name and prices on the menu
xpathSApply(rootNode, "//prices", xmlValue)
```
## Reading JSON
* structured, lightweight, common. Javascript Object Notation
* data stored as numbers, strings, boolean, arrays, objects

```r
library(jsonlite)
jsonData <- fromJSON("http://api.github.com/users/jtleek/repos") # puts in dataframe or nested dataframe
names(jsonData)
names(jsonData$owner)
names(jsonData$owner$login) # nested objects in JSON
# writing data frames to JSON
myjson <- toJSON(iris, pretty = TRUE)
cat(myjson)  # see structure
# convert back to JSON
iris2 <- fromJSON(myjson)
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
allTables <- dbListTables(hg19);
length(allTables); allTables[1:5]; # over 10949 tables
dbListFields(hg19,"affyU133Plus2") # dimensions of a specific table "affyU133Plus2"
dbGetQuery(hg19, "select count(*) from affyU133Plus2")
affyData <- dbReadQuery(hg19, "affyU133Plus2") #read from the table
head(affyData) # first 6 rows
query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3") 
# select a specific subset and stores query remotely in the database
affyMis <- fetch(query); quantile(affyMis$misMatches)
affyMisSmall <- fetch(query, n=10); dbClearResult(query); # to not suck down a huge chunk of data, using n=10... make sure to clear the query afterward
dbDisconnect(hg19)
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

**To read a html file, use readLines():**
```r
con = url("http://scholar.google.com/citations?user=HI-I6C0AAAAJ&h1=en")
htmlCode = readLines(con)
htmlCode # will print all on one line... you will want to parse this, eg with XML package:
close(con) #remember to close the connections
```

**Parsing with XML**
```r
library(XML)
url <- "http://scholar.google.com/citations?user=HI-I6C0AAAAJ&h1=en"
html <- htmlTreeParse(url, useInternalNodes=T)
xpathSApply(html, "//title", xmlValue)
xpathSApply(html, "//td[@id='col-citedby']", xmlValue)
```

**Alternative : Using Get from the httr package**
```r
library(httr)
html2 = GET(url)
content2 = content(html2,as="text")
parsedHtml = htmlParse(content2,asText=TRUE)
xpathSApply(parsedHtml, "//title", xmlValue)
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
