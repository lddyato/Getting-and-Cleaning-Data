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
list.files("./data")    ## [1] "cameras.csv"
dateDownloaded <- date() # Be sure to record when you downloaded
```

## Reading local flat files- read.table()
* This is the main function for reading data into R
* Flexible and robust but requires more parameters
* Reads the data into RAM - big data can cause problems
* Important parameters `file, header, sep, row.names, nrows`
* Related: `read.csv(), read.csv2()`

```r
# Download the file to load
if (!file.exists("data") {
dir.create("data") }
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl")   
# if the url starts with *https* on Mac, you may need to set method="curl"
dateDownloaded <- date() # Be sure to record when you downloaded
# Loading flat files - read.table()
cameraData <- read.table("./data/cameras.csv", sep = ",", header = TRUE) # If there is no last two parameters, you'll get errors
cameraData <- read.csv("./data/cameras.csv") #read.csv sets sep="," and header = TRUE
head(cameraData)
```
* This is the main function for reading data into R
* Flexible and robust but requires more parameters
* Reads the data into RAM - big data can cause problems
* Important parameters file, header, sep, ro.names, nrows
* Related: read.csv(), read.csv2()

*Some more important parameters*   
* `quote` - you can tell R whether there are any quoted values quote = "" means no quotes
* `na.strings` - set the character that represents a missing value. The default is `na.strings=“NA”`, `WQ1=read.table("wq.txt",header=T,na.strings=9999)` # 9999 reprents missing values or "." reprents missing value in SAS
* `nrows` - how many rows to read of the file. The default is `nrows = -1`
* `skip` - number of lines to skip before starting to read. The skipped rows include the variable numbers. so in general, 
it always used by combining with the `col.names`, for example, `WQ4=read.table("wq.txt",header=T,skip=2,col.names=c("A","B","C","D"))`
* `check.names` - renames the repeated variable names, such as, B and B.1 if you set `check.names=T`
* `dec` - decimal. The default is `dec=”."`. If the data values reprents with comma, set `dec=","` 
* In my experience, the biggest trouble with reading flat files are quotation marks 'or " placed in data values, setting quote="" often resolves these.

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
* Extensible markup language
* Frequently used to store structured data
* Particularly widely used in internet applications
* Extracting XML is the basis for most web scraping
* Components
  * Markup - labels that give the text structure
  * Content - the actual text of the document

[http://en.wikipedia.org/wiki/XML](http://en.wikipedia.org/wiki/XML)

**Tags, elements and attributes**

* Tags correspond to general labels
  * Start tags `<section>`
  * End tags `</section>`
  * Empty tags `<line-break />`
* Elements are specific examples of tags
  * `<Greeting> Hello, world </Greeting>`
* Attributes are components of the label
  * `<img src="jeff.jpg" alt="instructor"/>`
  * `<step number="3"> Connect A to B. </step>`
  

[http://en.wikipedia.org/wiki/XML](http://en.wikipedia.org/wiki/XML)

# Example XML file

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/xmlexample.png", height=450>

[http://www.w3schools.com/xml/simple.xml](http://www.w3schools.com/xml/simple.xml)

```r
# Read the file into R
library(XML)
fileUrl <- "http://www.w3schools.com/xml/simple.xml"
doc <- xmlTreeParse(fileUrl,useInternal=TRUE)
rootNode <- xmlRoot(doc)
xmlName(rootNode)
```

```
[1] "breakfast_menu"
```

```r
names(rootNode)
```

```
  food   food   food   food   food 
"food" "food" "food" "food" "food" 
```


---


```r
rootNode[[1]] # Directly access parts of the XML document
```

```
<food>
  <name>Belgian Waffles</name>
  <price>$5.95</price>
  <description>Two of our famous Belgian Waffles with plenty of real maple syrup</description>
  <calories>650</calories>
</food> 
```

```r
rootNode[[1]][[1]]
```

```
<name>Belgian Waffles</name> 
```
```r
xmlSApply(rootNode,xmlValue)  # Programatically extract parts of the file
```

```
                                                                                                                    food 
                              "Belgian Waffles$5.95Two of our famous Belgian Waffles with plenty of real maple syrup650" 
                                                                                                                    food 
                   "Strawberry Belgian Waffles$7.95Light Belgian waffles covered with strawberries and whipped cream900" 
                                                                                                                    food 
"Berry-Berry Belgian Waffles$8.95Light Belgian waffles covered with an assortment of fresh berries and whipped cream900" 
                                                                                                                    food 
                                               "French Toast$4.50Thick slices made from our homemade sourdough bread600" 
                                                                                                                    food 
                        "Homestyle Breakfast$6.95Two eggs, bacon or sausage, toast, and our ever-popular hash browns950" 
```

**XPath**

* _/node_ Top level node
* _//node_ Node at any level
* _node[@attr-name]_ Node with an attribute name
* _node[@attr-name='bob']_ Node with attribute name attr-name='bob'

Information from: [http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)

```r
xpathSApply(rootNode,"//name",xmlValue)  # Get the items on the menu and prices
```

```
[1] "Belgian Waffles"             "Strawberry Belgian Waffles"  "Berry-Berry Belgian Waffles"
[4] "French Toast"                "Homestyle Breakfast"        
```

```r
xpathSApply(rootNode,"//price",xmlValue)
```

```
[1] "$5.95" "$7.95" "$8.95" "$4.50" "$6.95"
```

**Another example**


<img rsc="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ravens.png",height=450>

[http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens](http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens)

**Viewing the source**

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ravenssource.png", height=450>

[http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens](http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens)

**Extract content by attributes**

```r
fileUrl <- "http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens"
doc <- htmlTreeParse(fileUrl,useInternal=TRUE)
scores <- xpathSApply(doc,"//li[@class='score']",xmlValue)
teams <- xpathSApply(doc,"//li[@class='team-name']",xmlValue)
scores
```
```
 [1] "49-27"    "14-6"     "30-9"     "23-20"    "26-23"    "19-17"    "19-16"    "24-18"   
 [9] "20-17 OT" "23-20 OT" "19-3"     "22-20"    "29-26"    "18-16"    "41-7"     "34-17"   
```
```r
teams
```
```
 [1] "Denver"      "Cleveland"   "Houston"     "Buffalo"     "Miami"       "Green Bay"  
 [7] "Pittsburgh"  "Cleveland"   "Cincinnati"  "Chicago"     "New York"    "Pittsburgh" 
[13] "Minnesota"   "Detroit"     "New England" "Cincinnati" 
```

**Notes and further resources**

* Official XML tutorials [short](http://www.omegahat.org/RSXML/shortIntro.pdf), [long](http://www.omegahat.org/RSXML/Tour.pdf)
* [An outstanding guide to the XML package](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)

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

**Example**

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/githubjson.png">

```r
# reading data from JSON {jsonlite package}, puts in dataframe or nested dataframe
library(jsonlite)
jsonData <- fromJSON("http://api.github.com/users/jtleek/repos") 
names(jsonData)
```
```
 [1] "id"                "name"              "full_name"         "owner"            
 [5] "private"           "html_url"          "description"       "fork"             
 [9] "url"               "forks_url"         "keys_url"          "collaborators_url"
[13] "teams_url"         "hooks_url"         "issue_events_url"  "events_url"       
[17] "assignees_url"     "branches_url"      "tags_url"          "blobs_url"        
[21] "git_tags_url"      "git_refs_url"      "trees_url"         "statuses_url"     
[25] "languages_url"     "stargazers_url"    "contributors_url"  "subscribers_url"  
[29] "subscription_url"  "commits_url"       "git_commits_url"   "comments_url"     
[33] "issue_comment_url" "contents_url"      "compare_url"       "merges_url"       
[37] "archive_url"       "downloads_url"     "issues_url"        "pulls_url"        
[41] "milestones_url"    "notifications_url" "labels_url"        "releases_url"     
[45] "created_at"        "updated_at"        "pushed_at"         "git_url"          
[49] "ssh_url"           "clone_url"         "svn_url"           "homepage"         
[53] "size"              "stargazers_count"  "watchers_count"    "language"         
[57] "has_issues"        "has_downloads"     "has_wiki"          "forks_count"      
[61] "mirror_url"        "open_issues_count" "forks"             "open_issues"      
[65] "watchers"          "default_branch"    "master_branch"    
```

```r
jsonData$name
```

```
 [1] "ballgown"       "dataanalysis"   "datascientist"  "datasharing"    "derfinder"     
 [6] "derfinder-1"    "DSM"            "EDA-Project"    "futureofstats"  "googleCite"    
[11] "graduate"       "healthvis"      "jhsph753"       "jhsph753and4"   "leekasso"      
[16] "modules"        "rdsmGeneSig"    "reviews"        "rfitbit"        "rpackages"     
[21] "sva"            "swfdr"          "talks"          "testrepository" "tornado"       
[26] "tsp-devel"      "tspreg"        
```
```r
names(jsonData$owner) # nested objects in JSON
```

```
 [1] "login"               "id"                  "avatar_url"          "gravatar_id"        
 [5] "url"                 "html_url"            "followers_url"       "following_url"      
 [9] "gists_url"           "starred_url"         "subscriptions_url"   "organizations_url"  
[13] "repos_url"           "events_url"          "received_events_url" "type"               
[17] "site_admin"         
```

```r
jsonData$owner$login
```

```
 [1] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
[11] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
[21] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
```
```r
myjson <- toJSON(iris, pretty=TRUE) # writing data frames to JSON
cat(myjson)
```

```
[
	{
		"Sepal.Length" : 5.1,
		"Sepal.Width" : 3.5,
		"Petal.Length" : 1.4,
		"Petal.Width" : 0.2,
		"Species" : "setosa"
	},
	{
		"Sepal.Length" : 4.9,
		"Sepal.Width" : 3,
		"Petal.Length" : 1.4,
		"Petal.Width" : 0.2,
		"Species" : "setosa"
	},
	{
		"Sepal.Length" : 4.7,
		"Sepal.Width" : 3.2,
		"Petal.Length" : 1.3,
		"Petal.Width" : 0.2,
		"Species" : "setosa"
	},
           ... ....
 ```
 
[http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/](http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/)

```r
iris2 <- fromJSON(myjson)  ## Convert back to JSON
head(iris2)
```
```
  Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1          5.1         3.5          1.4         0.2  setosa
2          4.9         3.0          1.4         0.2  setosa
3          4.7         3.2          1.3         0.2  setosa
4          4.6         3.1          1.5         0.2  setosa
5          5.0         3.6          1.4         0.2  setosa
6          5.4         3.9          1.7         0.4  setosa
```

[http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/](http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/)

## Reading mySQL
* free, widely used open source db software
* structure: databases, tables (data frame) within, fields (columns) within; each row is a record
* install MySQL, then install RMySQL 
  + On a Mac: `install.packages("RMySQL")` 
  + On Windows:
    - Official instructions - http://biostat.mc.vanderbilt.edu/wiki/Main/RMySQL (may be useful for Mac/UNIX users as well)
    - Potentially useful guide - http://www.ahschulz.de/2013/07/23/installing-rmysql-under-windows/

**Example-UCSC database**

UCSC database, http://genome.ucsc.edu/ (web-facing, use sparingly, do NOT delete, add, join etc and class probably will overload so don't use)

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ucscmysql.png">

## Connecting and listing databases


```r
# connecting and listing databases
ucscDb <- dbConnect(MySQL(),user="genome", host="genome-mysql.cse.ucsc.edu") 
result <- dbGetQuery(ucscDb,"show databases;") # 'show databases' is SQL not R code
dbDisconnect(ucscDb);
# [1] TRUE
result

#               Database
# 1   information_schema
# 2              ailMel1
# 3              allMis1
# 4              anoCar1
# 5              anoCar2
# 6              anoGam1
# 7              apiMel1
# 8              apiMel2
# 9              aplCal1
# 10             bosTau2
...  ...  ...  ...  ...
# 170            tetNig2
# 171            triMan1
# 172            tupBel1
# 173            turTru2
# 174            uniProt
# 175            vicPac1
# 176            vicPac2
# 177           visiGene
# 178            xenTro1
# 179            xenTro2
# 180            xenTro3
```

```r
# Connecting to hg19, one of the databases, and listing tables
hg19 <- dbConnect(MySQL(),user="genome", db="hg19", host="genome-mysql.cse.ucsc.edu")
allTables <- dbListTables(hg19)
length(allTables) # over 10949 tables
# [1] 10949
allTables[1:5] # the first 5 tables
# [1] "HInv"         "HInvGeneMrna" "acembly"      "acemblyClass" "acemblyPep"  
dbListFields(hg19,"affyU133Plus2")  ## Get dimensions of a specific table
#  [1] "bin"         "matches"     "misMatches"  "repMatches"  "nCount"      "qNumInsert" 
#  [7] "qBaseInsert" "tNumInsert"  "tBaseInsert" "strand"      "qName"       "qSize"      
# [13] "qStart"      "qEnd"        "tName"       "tSize"       "tStart"      "tEnd"       
# [19] "blockCount"  "blockSizes"  "qStarts"     "tStarts"    
dbGetQuery(hg19, "select count(*) from affyU133Plus2") # 58463 columns in table "affyU133Plus2"
#     count(*)
# 1    58463
affyData <- dbReadTable(hg19, "affyU133Plus2") ## Read from the table
head(affyData)  # first 6 rows
#   bin matches misMatches repMatches nCount qNumInsert qBaseInsert tNumInsert tBaseInsert strand
# 1 585     530          4          0     23          3          41          3         898      -
# 2 585    3355         17          0    109          9          67          9       11621      -
# 3 585    4156         14          0     83         16          18          2          93      -
# 4 585    4667          9          0     68         21          42          3        5743      -
# 5 585    5180         14          0    167         10          38          1          29      -
# 6 585     468          5          0     14          0           0          0           0      -
#          qName qSize qStart qEnd tName     tSize tStart  tEnd blockCount
# 1  225995_x_at   637      5  603  chr1 249250621  14361 15816          5
# 2  225035_x_at  3635      0 3548  chr1 249250621  14381 29483         17
# 3  226340_x_at  4318      3 4274  chr1 249250621  14399 18745         18
# 4 1557034_s_at  4834     48 4834  chr1 249250621  14406 24893         23
# 5    231811_at  5399      0 5399  chr1 249250621  19688 25078         11
# 6    236841_at   487      0  487  chr1 249250621  27542 28029          1
#                                                                   blockSizes
# 1                                                          93,144,229,70,21,
# 2              73,375,71,165,303,360,198,661,201,1,260,250,74,73,98,155,163,
# 3                 690,10,32,33,376,4,5,15,5,11,7,41,277,859,141,51,443,1253,
# 4 99,352,286,24,49,14,6,5,8,149,14,44,98,12,10,355,837,59,8,1500,133,624,58,
# 5                                       131,26,1300,6,4,11,4,7,358,3359,155,
# 6                                                                       487,
                                                                                                 qStarts
# 1                                                                                    34,132,278,541,611,
# 2                        87,165,540,647,818,1123,1484,1682,2343,2545,2546,2808,3058,3133,3206,3317,3472,
# 3                   44,735,746,779,813,1190,1195,1201,1217,1223,1235,1243,1285,1564,2423,2565,2617,3062,
# 4 0,99,452,739,764,814,829,836,842,851,1001,1016,1061,1160,1173,1184,1540,2381,2441,2450,3951,4103,4728,
# 5                                                     0,132,159,1460,1467,1472,1484,1489,1497,1856,5244,
# 6                                                                                                     0,
# tStarts
# 1 14361,14454,14599,14968,15795,
# 2 14381,14454,14969,15075,15240,15543,15903,16104,16853,17054,17232,17492,17914,17988,18267,24736,29320,
# 3 14399,15089,15099,15131,15164,15540,15544,15549,15564,15569,15580,15587,15628,15906,16857,16998,17049,17492,
# 4 14406,20227,20579,20865,20889,20938,20952,20958,20963,20971,21120,21134,21178,21276,21288,21298,21653,22492,22551,22559,24059,24215,
# 5 19688,19819,19845,21145,21151,21155,21166,21170,21177,21535,24923,
# 6 27542,

query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3") 
## Select a specific subset and stores query remotely in the database
affyMis <- fetch(query) 
quantile(affyMis$misMatches)
#   0%  25%  50%  75% 100% 
#    1    1    2    2    3 
affyMisSmall <- fetch(query,n=10);  # to not suck down a huge chunk of data, using n=10... make sure to clear the query afterward
dbClearResult(query);
# [1] TRUE
dim(affyMisSmall)
# [1] 10 22

dbDisconnect(hg19)## Don't forget to close the connection!
# [1] TRUE
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
created
# [1] TRUE
```
* This will install packages from Bioconductor [http://bioconductor.org/](http://bioconductor.org/), primarily used for genomics but also has good "big data" packages
* Can be used to interface with hdf5 data sets. 
* This lecture is modeled very closely on the rhdf5 tutorial that
can be found here [http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf](http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf)

```r
#creating groups
created = h5createGroup("example.h5", "foo")
created = h5createGroup("example.h5", "bar")
created = h5createGroup("example.h5", "foo/foobar")
h5ls(("example.h5") # h5 then ls as in list, this lists files
#   group   name     otype dclass dim
# 0     /    baa H5I_GROUP           
# 1     /    foo H5I_GROUP           
# 2  /foo foobaa H5I_GROUP 

#writing to groups, say A is a matrix, B is an array
A = matrix(1:10, nr = 5, nc=2)
h5write(A, "example.h5", "foo/A") # adds another file in the h5ls in the /foo group
B = array(seq(0.1, 2.0, by=0.1), dim = c(5, 2, 2))
attr(B, "scale") <- "liter" 
h5write(B, "example.h5", "foo/foobaa/B") # adds another file in the h5ls in the /foo/foobar group, B name
h5ls("example.h5")
#         group   name       otype  dclass       dim
# 0           /    baa   H5I_GROUP                  
# 1           /    foo   H5I_GROUP                  
# 2        /foo      A H5I_DATASET INTEGER     5 x 2
# 3        /foo foobaa   H5I_GROUP                  
# 4 /foo/foobaa      B H5I_DATASET   FLOAT 5 x 2 x 2

#writing a dataset, say df is a dataframe:
df = dataframe(1L:5L, seq(0, 1, length.out = 5), c("ab", "cde", "fghi", "s"), stringsAsFactors = FALSE)
h5write(df, "example.h5", "df") # adds another file in the h5ls, in top level or root group, named df
h5ls("example.h5")
#         group   name       otype   dclass       dim
# 0           /    baa   H5I_GROUP                   
# 1           /     df H5I_DATASET COMPOUND         5
# 2           /    foo   H5I_GROUP                   
# 3        /foo      A H5I_DATASET  INTEGER     5 x 2
# 4        /foo foobaa   H5I_GROUP                   
# 5 /foo/foobaa      B H5I_DATASET    FLOAT 5 x 2 x 2

#reading data:
readA = h5read("example.h5", "foo/A")
readB = h5read("example.h5", "foo/foobaa/B")
readdf = h5read("example.h5", "df")
#     [,1] [,2]
# [1,]    1    6
# [2,]    2    7
# [3,]    3    8
# [4,]    4    9
# [5,]    5   10

#writing and reading chunks: 
h5write(c(12,13,14), "example.h5", "foo/A", index = list(1:3,1)) 
#note this OVERWRITES first 3 rows in col 1 of A:
# you can use index within h5read() as well
h5read("example.h5", "foo/A")
#      [,1] [,2]
# [1,]   12    6
# [2,]   13    7
# [3,]   14    8
# [4,]    4    9
# [5,]    5   10
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
