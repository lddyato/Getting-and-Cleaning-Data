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

<http://data.baltimorecity.gov/Transportation/Baltimore-Fixed-Speed-Cameras/dz54-2aru>

```r
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl") 
# if the url starts with *https* on Mac, you may need to set method="curl"
list.files("./data")    ## [1] "cameras.csv"
dateDownloaded <- date() # Be sure to record when you downloaded
```

# Reading local flat files- read.table()
* This is the main function for reading data into R
* Flexible and robust but requires more parameters
* Reads the data into RAM - big data can cause problems
* Important parameters `file, header, sep, row.names, nrows`
* Related: `read.csv(), read.csv2()`

## Download the file to load
```r
if (!file.exists("data") {
dir.create("data") }
fileurl <- "http://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileurl, destfile="./data/cameras.csv", method="curl")   
# if the url starts with *https* on Mac, you may need to set method="curl"
dateDownloaded <- date() # Be sure to record when you downloaded
```
## Loading flat files - read.table()
```r
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
# Reading Excel files
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
```
                         address direction      street  crossStreet               intersection
1       S CATON AVE & BENSON AVE       N/B   Caton Ave   Benson Ave     Caton Ave & Benson Ave
2       S CATON AVE & BENSON AVE       S/B   Caton Ave   Benson Ave     Caton Ave & Benson Ave
3 WILKENS AVE & PINE HEIGHTS AVE       E/B Wilkens Ave Pine Heights Wilkens Ave & Pine Heights
4        THE ALAMEDA & E 33RD ST       S/B The Alameda      33rd St     The Alameda  & 33rd St
5        E 33RD ST & THE ALAMEDA       E/B      E 33rd  The Alameda      E 33rd  & The Alameda
6        ERDMAN AVE & N MACON ST       E/B      Erdman     Macon St         Erdman  & Macon St
                       Location.1
1 (39.2693779962, -76.6688185297)
2 (39.2693157898, -76.6689698176)
3  (39.2720252302, -76.676960806)
4 (39.3285013141, -76.5953545714)
5 (39.3283410623, -76.5953594625)
6 (39.3068045671, -76.5593167803)
```
## Reading sepcific rows and columns**
```r
colIndex <- 2:3
rowIndex <- 1:4
cameraDataSubset <- read.xlsx("./data/cameras.xlsx", sheetIndex = 1, colIndex = colIndex, rowIndex = rowIndex)
```
```
  direction      street
1       N/B   Caton Ave
2       S/B   Caton Ave
3       E/B Wilkens Ave
```

*Further Notes*
* The `write.xlsx` function will wirte out an Excel file with similar arguments
* `read.xlsx2` is much faster than *read.xlsx* but for reading subsets of rows may be slightly unstable
* The `XLConnect` package has more options for writing and manipulating Excel files
* The `XLConnect vignette` is a good place to start for that package  

In general it is adviced to store your data in either a database or in (.csv) or (.tab/txt) as they are easier to distribute

# Reading XML
* Extensible markup language
* Frequently used to store structured data
* Particularly widely used in internet applications
* Extracting XML is the basis for most web scraping
* Components
  * Markup - labels that give the text structure
  * Content - the actual text of the document

[http://en.wikipedia.org/wiki/XML](http://en.wikipedia.org/wiki/XML)

## Tags, elements and attributes

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

## Example XML file

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/xmlexample.png", height=450>

[http://www.w3schools.com/xml/simple.xml](http://www.w3schools.com/xml/simple.xml)

### Read the file into R
```r
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
### Directly access parts of the XML document
```r
rootNode[[1]] 
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
### Programatically extract parts of the file
```r
xmlSApply(rootNode,xmlValue) 
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

*XPath*

* _/node_ Top level node
* _//node_ Node at any level
* _node[@attr-name]_ Node with an attribute name
* _node[@attr-name='bob']_ Node with attribute name attr-name='bob'

Information from: [http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)

### Get the items on the menu and prices
```r
xpathSApply(rootNode,"//name",xmlValue)  
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

## Another XML example


<img rsc="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ravens.png",height=450>

[http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens](http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens)

### Viewing the source

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ravenssource.png", height=450>

[http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens](http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens)

### Extract content by attributes

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

## Notes and further resources

* Official XML tutorials [short](http://www.omegahat.org/RSXML/shortIntro.pdf), [long](http://www.omegahat.org/RSXML/Tour.pdf)
* [An outstanding guide to the XML package](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)

xmlTreeParse does not support https... use instead:
```r
library(XML)
library(RCurl)
fileURL <- "http://www.w3schools.com/xml/simple.xml"
xmlFile <- getURL(fileURL)
```

# Reading JSON
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

## Example

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/githubjson.png">

### reading data from JSON {jsonlite package}, puts in dataframe or nested dataframe
```r
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

### nested objects in JSON
```r
names(jsonData$owner) 
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
### writing data frames to JSON
```r
myjson <- toJSON(iris, pretty=TRUE) 
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

### Convert back to JSON
```r
iris2 <- fromJSON(myjson) 
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

## Further resources

* [http://www.json.org/](http://www.json.org/)
* A good tutorial on jsonlite - [http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/](http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/)
* [jsonlite vignette](http://cran.r-project.org/web/packages/jsonlite/vignettes/json-mapping.pdf)

# Reading mySQL
* free, widely used open source db software
* structure: databases, tables (data frame) within, fields (columns) within; each row is a record
* install MySQL<http://dev.mysql.com/doc/refman/5.7/en/installing.html>, then install RMySQL 
  + On a Mac: `install.packages("RMySQL")` 
  + On Windows:
    - Official instructions - http://biostat.mc.vanderbilt.edu/wiki/Main/RMySQL (may be useful for Mac/UNIX users as well)
    - Potentially useful guide - http://www.ahschulz.de/2013/07/23/installing-rmysql-under-windows/

##Example-UCSC database

UCSC database, http://genome.ucsc.edu/ (web-facing, use sparingly, do NOT delete, add, join etc and class probably will overload so don't use)

<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/ucscmysql.png">

### Connecting and listing databases
```r
ucscDb <- dbConnect(MySQL(),user="genome", host="genome-mysql.cse.ucsc.edu")
result <- dbGetQuery(ucscDb,"show databases;")
dbDisconnect(ucscDb);
```

```
[1] TRUE
```

```r
result
```

```
              Database
1   information_schema
2              ailMel1
3              allMis1
4              anoCar1
5              anoCar2
6              anoGam1
7              apiMel1
8              apiMel2
9              aplCal1
10             bosTau2
...  ...  ...  ...  ...
170            tetNig2
171            triMan1
172            tupBel1
173            turTru2
174            uniProt
175            vicPac1
176            vicPac2
177           visiGene
178            xenTro1
179            xenTro2
180            xenTro3
```
### Connecting to hg19, one of the databases, and listing tables
```r
hg19 <- dbConnect(MySQL(),user="genome", db="hg19", host="genome-mysql.cse.ucsc.edu")
allTables <- dbListTables(hg19)
length(allTables) # over 10949 tables
# [1] 10949
allTables[1:5] # the first 5 tables
# [1] "HInv"         "HInvGeneMrna" "acembly"      "acemblyClass" "acemblyPep"  
```
### Get dimensions of a specific table
```r
dbListFields(hg19,"affyU133Plus2") 
#  [1] "bin"         "matches"     "misMatches"  "repMatches"  "nCount"      "qNumInsert" 
#  [7] "qBaseInsert" "tNumInsert"  "tBaseInsert" "strand"      "qName"       "qSize"      
# [13] "qStart"      "qEnd"        "tName"       "tSize"       "tStart"      "tEnd"       
# [19] "blockCount"  "blockSizes"  "qStarts"     "tStarts"    
dbGetQuery(hg19, "select count(*) from affyU133Plus2") # 58463 columns in table "affyU133Plus2"
#     count(*)
# 1    58463
```

### Read from the table
```r
affyData <- dbReadTable(hg19, "affyU133Plus2") 
head(affyData)  # first 6 rows
```
```
  bin matches misMatches repMatches nCount qNumInsert qBaseInsert tNumInsert tBaseInsert strand
1 585     530          4          0     23          3          41          3         898      -
2 585    3355         17          0    109          9          67          9       11621      -
3 585    4156         14          0     83         16          18          2          93      -
4 585    4667          9          0     68         21          42          3        5743      -
5 585    5180         14          0    167         10          38          1          29      -
6 585     468          5          0     14          0           0          0           0      -
         qName qSize qStart qEnd tName     tSize tStart  tEnd blockCount
1  225995_x_at   637      5  603  chr1 249250621  14361 15816          5
2  225035_x_at  3635      0 3548  chr1 249250621  14381 29483         17
3  226340_x_at  4318      3 4274  chr1 249250621  14399 18745         18
4 1557034_s_at  4834     48 4834  chr1 249250621  14406 24893         23
5    231811_at  5399      0 5399  chr1 249250621  19688 25078         11
6    236841_at   487      0  487  chr1 249250621  27542 28029          1
                                                                  blockSizes
1                                                          93,144,229,70,21,
2              73,375,71,165,303,360,198,661,201,1,260,250,74,73,98,155,163,
3                 690,10,32,33,376,4,5,15,5,11,7,41,277,859,141,51,443,1253,
4 99,352,286,24,49,14,6,5,8,149,14,44,98,12,10,355,837,59,8,1500,133,624,58,
5                                       131,26,1300,6,4,11,4,7,358,3359,155,
6                                                                       487,
                                                                                                 qStarts
1                                                                                    34,132,278,541,611,
2                        87,165,540,647,818,1123,1484,1682,2343,2545,2546,2808,3058,3133,3206,3317,3472,
3                   44,735,746,779,813,1190,1195,1201,1217,1223,1235,1243,1285,1564,2423,2565,2617,3062,
4 0,99,452,739,764,814,829,836,842,851,1001,1016,1061,1160,1173,1184,1540,2381,2441,2450,3951,4103,4728,
5                                                     0,132,159,1460,1467,1472,1484,1489,1497,1856,5244,
6                                                                                                     0,
                                                                                                                                     tStarts
1                                                                                                             14361,14454,14599,14968,15795,
2                                     14381,14454,14969,15075,15240,15543,15903,16104,16853,17054,17232,17492,17914,17988,18267,24736,29320,
3                               14399,15089,15099,15131,15164,15540,15544,15549,15564,15569,15580,15587,15628,15906,16857,16998,17049,17492,
4 14406,20227,20579,20865,20889,20938,20952,20958,20963,20971,21120,21134,21178,21276,21288,21298,21653,22492,22551,22559,24059,24211,24835,
5                                                                         19688,19819,19845,21145,21151,21155,21166,21170,21177,21535,24923,
6                                                                                                                                     27542,
```
### Select a specific subset and stores query remotely in the database
```r
query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3")
affyMis <- fetch(query)
quantile(affyMis$misMatches)
```

```
  0%  25%  50%  75% 100% 
   1    1    2    2    3 
```

```r
affyMisSmall <- fetch(query,n=10) # to not suck down a huge chunk of data, using n=10... make sure to clear the query afterward 
dbClearResult(query);
```

```
[1] TRUE
```

```r
dim(affyMisSmall)
```
```
[1] 10 22
```

### Don't forget to close the connection!
```r
dbDisconnect(hg19)
# [1] TRUE
```

## Further resources
* do not delete, add or join things from ensembl. Only select.
* be careful with mysql commands 
* see <http://www.r-bloggers.com/mysql-and-r/>
* `dbConnect, dbGetQuery, dbListTables, dbListFields, dbReadQuery, dbSendQuery, dbClearResult, fetch (or dbFetch), dbDisconnect`
* REMEMBER TO CLOSE CONNECTIONS

# Reading from HDF5
* HDF5 for storing large datasets, structural datasets
* supports range of data types
* hdf = hierarchical data format
* groups containing 0 or more data sets and metadata: have a group header with group name and list of attributes; have a group symbol table with a list of objects in group
* datasets multidimensional array of data elements with metadata: have a header with name, datatype, dataspace, storage layout; have a data array with the data
* www.hdfgroup.org

## R HDF5 package
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

## creating groups
```r
created = h5createGroup("example.h5", "foo")
created = h5createGroup("example.h5", "bar")
created = h5createGroup("example.h5", "foo/foobar")
h5ls(("example.h5") # h5 then ls as in list, this lists files
```
```
  group   name     otype dclass dim
0     /    baa H5I_GROUP           
1     /    foo H5I_GROUP           
2  /foo foobaa H5I_GROUP           
```

## writing to groups, say A is a matrix, B is an array
```r
A = matrix(1:10, nr = 5, nc=2)
h5write(A, "example.h5", "foo/A") # adds another file in the h5ls in the /foo group
B = array(seq(0.1, 2.0, by=0.1), dim = c(5, 2, 2))
attr(B, "scale") <- "liter" 
h5write(B, "example.h5", "foo/foobaa/B") # adds another file in the h5ls in the /foo/foobar group, B name
h5ls("example.h5")
```
```
        group   name       otype  dclass       dim
0           /    baa   H5I_GROUP                  
1           /    foo   H5I_GROUP                  
2        /foo      A H5I_DATASET INTEGER     5 x 2
3        /foo foobaa   H5I_GROUP                  
4 /foo/foobaa      B H5I_DATASET   FLOAT 5 x 2 x 2
```

## writing a dataset, say df is a dataframe
```r
df = dataframe(1L:5L, seq(0, 1, length.out = 5), c("ab", "cde", "fghi", "s"), stringsAsFactors = FALSE)
h5write(df, "example.h5", "df") # adds another file in the h5ls, in top level or root group, named df
h5ls("example.h5")
```
```
        group   name       otype   dclass       dim
0           /    baa   H5I_GROUP                   
1           /     df H5I_DATASET COMPOUND         5
2           /    foo   H5I_GROUP                   
3        /foo      A H5I_DATASET  INTEGER     5 x 2
4        /foo foobaa   H5I_GROUP                   
5 /foo/foobaa      B H5I_DATASET    FLOAT 5 x 2 x 2
```

## reading data
```r
readA = h5read("example.h5", "foo/A")
readB = h5read("example.h5", "foo/foobaa/B")
readdf = h5read("example.h5", "df")
```
```
     [,1] [,2]
[1,]    1    6
[2,]    2    7
[3,]    3    8
[4,]    4    9
[5,]    5   10
```

## writing and reading chunks
```r
h5write(c(12,13,14), "example.h5", "foo/A", index = list(1:3,1)) 
#note this OVERWRITES first 3 rows in col 1 of A:
# you can use index within h5read() as well
h5read("example.h5", "foo/A")
```
```
     [,1] [,2]
[1,]   12    6
[2,]   13    7
[3,]   14    8
[4,]    4    9
[5,]    5   10
```
## Notes and further resources

* hdf5 can be used to optimize reading/writing from disc in R
* The rhdf5 tutorial: 
  * [http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf](http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf)
* The HDF group has informaton on HDF5 in general [http://www.hdfgroup.org/HDF5/](http://www.hdfgroup.org/HDF5/)


# Reading data from the web - readLines()
## Webscraping 
Webscraping: programatically extracting data from the HTML code of websites

* It can be a great way to get data, but be careful, sometimes against the terms of service
* attempting to read too many pages too quickly can get your IP address blocked
* robots.txt are at for example: http://www.facebook.com.br/robots.txt

## Getting data off webpages - readLines()
```r
con = url("http://scholar.google.com/citations?user=HI-I6C0AAAAJ&hl=en")
htmlCode = readLines(con)
close(con)
htmlCode
```

```r
[1] "<!DOCTYPE html><html><head><title>Jeff Leek - Google Scholar Citations</title><meta name=\"robots\" content=\"noarchive\"><meta http-equiv=\"Content-Type\" content=\"text/html;charset=ISO-8859-1\"><meta http-equiv=\"X-UA-Compatible\" content=\"IE=Edge\"><meta name=\"format-detection\" content=\"telephone=no\"><link rel=\"canonical\" href=\"http://scholar.google.com/citations?user=HI-I6C0AAAAJ&amp;hl=en\"><style type=\"text/css\" media=\"screen, projection\">html,body,form,table,div,h1,h2,h3,h4,h5,h6,img,ol,ul,li,button{margin:0;padding:0;border:0;}table{border-collapse:collapse;border-width:0;empty-cells:show;}#gs_top{position:relative;min-width:964px;_width:expression(document.documentElement.clientWidth<966?\"964px\":\"auto\");-webkit-tap-highlight-color:rgba(0,0,0,0);}#gs_top>*:not(#x){-webkit-tap-highlight-color:rgba(204,204,204,.5);}.gs_el_ph #gs_top,.gs_el_ta #gs_top{min-width:300px;_width:expression(document.documentElement.clientWidth<302?\"300px\":\"auto\");}body,td{font-size:13px;font-family:Arial,sans-serif;line-height:1.24}body{background:#fff;color:#222;-webkit-text-size-adjust:100%;-moz-text-size-adjust:none;}.gs_gray{color:#777777}.gs_red{color:#dd4b39}.gs_grn{color:#3d9400}.gs_lil{font-size:11px}.gs_med{font-size:16px}.gs_hlt{font-weight:bold;}a:link,a:visited{color:#1155cc;text-decoration:none}a:hover,a:active,a:hover .gs_lbl,a:active .gs_lbl,a .gs_lbl:active{text-decoration:underline;outline:none;}a:active,a:active .gs_lbl,a .gs_lbl:active{color:#d14836}a:visited{color:#6611cc}.gs_a,.gs_a a:link,.gs_a a:visited{color:#009933}.gs_a a:active{color:#d14836}a.gs_fl:link,.gs_fl a:link{color:#1155cc}a.gs_fl:visited,.gs_fl a:visited{color:#6611cc}a.gs_fl:active,.gs_fl a:active{color:#d14836}.gs_fl{color:#767676}.gs_ctc,.gs_ctu{font-size:11px;font-weight:bold}.gs_ctc{color:#1122cc}.gs_ctg,.gs_ctg2{font-size:13px;font-weight:bold}.gs_ctg{color:#1155cc}a.gs_pda,.gs_pda a{padding:7px 0 5px 0}a:hover{*background-position:0 0}a:active{*background-attachment:fixed}a.gs_ico:active{*background-attachment:scroll}.gs_alrt{background:#f9edbe;border:1px solid #f0c36d;padding:0 16px;text-align:center;-webkit-box-shadow:0 2px 4px rgba(0,0,0,.2);-moz-box-shadow:0 2px 4px rgba(0,0,0,.2);box-shadow:0 2px 4px rgba(0,0,0,.2);-webkit-border-radius:2px;-moz-border-radius:2px;border-radius:2px;}.gs_spc{display:inline-block;width:12px}.gs_br{width:0;font-size:0}.gs_ibl{display:inline-block;*display:inline;zoom:1;}.gs_scl{*zoom:1}.gs_scl:after{content:\"\";display:table;clear:both;}.gs_ind{padding-left:8px;text-indent:-8px}.gs_ico{display:inline-block;background:no-repeat url(/intl/en/scholar/images/sprite.png);_background-image:url(/intl/en/scholar/images/sprite.gif);width:21px;height:21px;font-size:0;}.gs_el_ta .gs_nta,.gs_ota,.gs_el_ph .gs_nph,.gs_oph{display:none}.gs_el_ta .gs_ota,.gs_el_ph .gs_oph{display:inline}.gs_el_ta div.gs_ota,.gs_el_ph div.gs_oph{display:block}#gs_ftr{width:650px;margin-top:1em;text-align:center;clear:both;}.gs_el_ta #gs_ftr,.gs_el_ph #gs_ftr{width:auto;max-width:600px;_width:expression(document.documentElement.clientWidth>616?\"600px\":\"auto\");}#gs_ftr a{display:inline-block;margin:0 12px;line-height:29px;}#gs_ftr a:link,#gs_ftr a:visited{color:#1155cc}#gs_ftr a:active{color:#d14836}button{position:relative; z-index:1; -moz-box-sizing:border-box;-webkit-box-sizing:border-box;box-sizing:border-box;font-size:11px;font-weight:bold;cursor:default;height:29px;min-width:72px;_width:72px;overflow:visible;color:#444;border:1px solid #dcdcdc;border:1px solid rgba(0,0,0,.1);-webkit-border-radius:2px;-moz-border-radius:2px;border-radius:2px;text-align:center;background-color:#f5f5f5;background-image:-webkit-gradient(linear,left top,left bottom,from(#f5f5f5),to(#f1f1f1));background-image:-webkit-linear-gradient(top,#f5f5f5,#f1f1f1);background-image:-moz-linear-gradient(top,#f5f5f5,#f1f1f1);background-image:-o-linear-gradient(top,#f5f5f5,#f1f1f1);background-image:linear-gradient(to bottom,#f5f5f5,#f1f1f1);-webkit-transition:all .218s;-moz-transition:all .218s;-o-transition:all .218s;transition:all .218s;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none;}button .gs_wr{line-height:27px;_line-height:24px;}button .gs_bg{position:absolute;top:0;left:0;width:100%;height:100%;*display:none;z-index:-1;filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#f5f5f5',endColorstr='#f1f1f1');}button.gs_btn_mini{min-width:26px;_width:26px;height:26px;}.gs_btn_mini .gs_wr{line-height:24px;_line-height:21px;}button:hover,button.gs_hvr{z-index:2;color:#222;border:1px solid #c6c6c6;-webkit-box-shadow:0px 1px 1px rgba(0,0,0,.1);-moz-box-shadow:0px 1px 1px rgba(0,0,0,.1);box-shadow:0px 1px 1px rgba(0,0,0,.1);background-color:#f8f8f8;background-image:-webkit-gradient(linear,left top,left bottom,from(#f8f8f8),to(#f1f1f1));background-image:-webkit-linear-gradient(top,#f8f8f8,#f1f1f1);background-image:-moz-linear-gradient(top,#f8f8f8,#f1f1f1);background-image:-o-linear-gradient(top,#f8f8f8,#f1f1f1);background-image:linear-gradient(to bottom,#f8f8f8,#f1f1f1);-webkit-transition:all 0s;-moz-transition:all 0s;-o-transition:all 0s;transition:all 0s;}button:hover .gs_bg,button.gs_hvr .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorStr='#f8f8f8',EndColorStr='#f1f1f1');}button.gs_sel{color:#333;border:1px solid #ccc;-webkit-box-shadow:inset 0 1px 2px rgba(0,0,0,.1);-moz-box-shadow:inset 0 1px 2px rgba(0,0,0,.1);box-shadow:inset 0 1px 2px rgba(0,0,0,.1);background-color:#e8e8e8;background-image:-webkit-gradient(linear,left top,left bottom,from(#eee),to(#e0e0e0));background-image:-webkit-linear-gradient(top,#eee,#e0e0e0);background-image:-moz-linear-gradient(top,#eee,#e0e0e0);background-image:-o-linear-gradient(top,#eee,#e0e0e0);background-image:linear-gradient(to bottom,#eee,#e0e0e0);}button.gs_sel .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorStr='#eeeeee',EndColorStr='#e0e0e0');}button:active,button.gs_act{z-index:2;color:#333;background-color:#f6f6f6;background-image:-webkit-gradient(linear,left top,left bottom,from(#f6f6f6),to(#f1f1f1));background-image:-webkit-linear-gradient(top,#f6f6f6,#f1f1f1);background-image:-moz-linear-gradient(top,#f6f6f6,#f1f1f1);background-image:-o-linear-gradient(top,#f6f6f6,#f1f1f1);background-image:linear-gradient(to bottom,#f6f6f6,#f1f1f1);-webkit-box-shadow:inset 0px 1px 2px rgba(0,0,0,.1);-moz-box-shadow:inset 0px 1px 2px rgba(0,0,0,.1);box-shadow:inset 0px 1px 2px rgba(0,0,0,.1);}button:active .gs_bg,button.gs_act .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorStr='#f6f6f6',EndColorStr='#f1f1f1');}button:focus,button.gs_fcs{z-index:2;outline:none;border:1px solid #4d90fe;}button::-moz-focus-inner{padding:0;border:0}button .gs_lbl{padding:0px 8px;}a.gs_in_ib{position:relative;display:inline-block;line-height:16px;padding:5px 0 6px 0;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none;}a.gs_btn_mini{height:24px;line-height:24px;padding:0;}a .gs_lbl,button .gs_lbl{vertical-align:baseline;}a.gs_in_ib .gs_lbl{display:inline-block;padding-left:21px}button.gs_in_ib .gs_lbl{padding-left:29px;}button.gs_btn_mini .gs_lbl,button.gs_btn_eml .gs_lbl{padding:11px;}.gs_in_ib .gs_ico{position:absolute;top:2px;*top:3px;left:8px;}.gs_btn_mini .gs_ico{top:1px;*top:2px;left:2px;}.gs_btn_eml .gs_ico{left:25px}button.gs_btn_mini,.gs_btn_eml{_font-size:0}a.gs_in_ib .gs_ico{top:3px;left:0}a.gs_in_ib-gs_md_li .gs_ico{left:14px}.gs_el_tc a.gs_in_ib-gs_md_li .gs_ico{top:11px}a.gs_btn_mini .gs_ico{top:1px;left:0}button.gs_btn_act{color:#fff;-webkit-font-smoothing:antialiased;border:1px solid #3079ed;background-color:#4d90fe;background-image:-webkit-gradient(linear,left top,left bottom,from(#4d90fe),to(#4787ed));background-image:-webkit-linear-gradient(top,#4d90fe,#4787ed);background-image:-moz-linear-gradient(top,#4d90fe,#4787ed);background-image:-o-linear-gradient(top,#4d90fe,#4787ed);background-image:linear-gradient(to bottom,#4d90fe,#4787ed);}button.gs_btn_act .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#4d90fe',endColorstr='#4787ed');}button.gs_btn_act:hover,button.gs_btn_act-gs_hvr{color:#fff;border:1px solid #2f5bb7;background-color:#357ae8;background-image:-webkit-gradient(linear,left top,left bottom,from(#4d90fe),to(#357ae8));background-image:-webkit-linear-gradient(top,#4d90fe,#357ae8);background-image:-moz-linear-gradient(top,#4d90fe,#357ae8);background-image:-o-linear-gradient(top,#4d90fe,#357ae8);background-image:linear-gradient(to bottom,#4d90fe,#357ae8);-webkit-box-shadow:inset 0px 1px 1px rgba(0,0,0,.1);-moz-box-shadow:inset 0px 1px 1px rgba(0,0,0,.1);box-shadow:inset 0px 1px 1px rgba(0,0,0,.1);}button.gs_btn_act:hover .gs_bg,button.gs_btn_act-gs_hvr .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#4d90fe',endColorstr='#357ae8');}button.gs_btnG{width:70px;min-width:0;}button.gs_btn_act:focus{-webkit-box-shadow:inset 0 0 0 1px rgba(255,255,255,.5);-moz-box-shadow:inset 0 0 0 1px rgba(255,255,255,.5);box-shadow:inset 0 0 0 1px rgba(255,255,255,.5);}button.gs_btn_act:focus,button.gs_btn_act-gs_fcs{border-color:#404040;}button.gs_btn_act:active,button.gs_btn_act-gs_act{border:1px solid #315da3;background-color:#2f6de1;background-image:-webkit-gradient(linear,left top,left bottom,from(#4d90fe),to(#2f6de1));background-image:-webkit-linear-gradient(top,#4d90fe,#2f6de1);background-image:-moz-linear-gradient(top,#4d90fe,#2f6de1);background-image:-o-linear-gradient(top,#4d90fe,#2f6de1);background-image:linear-gradient(to bottom,#4d90fe,#2f6de1);}button.gs_btn_act:active .gs_bg,button.gs_btn_act-gs_act .gs_bg{filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#4d90fe',endColorstr='#2f6de1');}button.gs_btn_act:active{-webkit-box-shadow:inset 0 1px 2px rgba(0,0,0,.3);-moz-box-shadow:inset 0 1px 2px rgba(0,0,0,.3);box-shadow:inset 0 1px 2px rgba(0,0,0,.3);}.gs_ttp{position:absolute;top:100%;right:50%;z-index:10;pointer-events:none;visibility:hidden;opacity:0;-webkit-transition:visibility 0s .13s,opacity .13s ease-out;-moz-transition:visibility 0s .13s,opacity .13s ease-out;-o-transition:visibility 0s .13s,opacity .13s ease-out;transition:visibility 0s .13s,opacity .13s ease-out;}button:hover .gs_ttp,button.gs_hvr .gs_ttp,button:focus .gs_ttp,button.gs_fcs .gs_ttp,a:hover .gs_ttp,a:focus .gs_ttp{-webkit-transition:visibility 0s .3s,opacity .13s ease-in .3s;-moz-transition:visibility 0s .3s,opacity .13s ease-in .3s;-o-transition:visibility 0s .3s,opacity .13s ease-in .3s;transition:visibility 0s .3s,opacity .13s ease-in .3s;visibility:visible;opacity:1;}.gs_ttp .gs_aro,.gs_ttp .gs_aru{position:absolute;top:-2px;right:-5px;width:0;height:0;line-height:0;font-size:0;border:5px solid transparent;_border:5px solid #f5f5f5;border-top:none;border-bottom-color:#2a2a2a;z-index:1;}.gs_ttp .gs_aro{top:-3px;right:-6px;border-width:6px;border-top:none;border-bottom-color:white;}.gs_ttp .gs_txt{display:block;position:relative;top:2px;right:-50%;padding:7px 9px;background:#2a2a2a;color:white;font-size:11px;font-weight:bold;line-height:normal;white-space:nowrap;border:1px solid white;-webkit-box-shadow:inset 0 1px 4px rgba(0,0,0,.2);-moz-box-shadow:inset 0 1px 4px rgba(0,0,0,.2);box-shadow:inset 0 1px 4px rgba(0,0,0,.2);}.gs_in_se,.gs_tan{-ms-touch-action:none;touch-action:none;}.gs_in_se .gs_lbl{padding-left:8px;padding-right:22px;}.gs_in_se .gs_ico{position:absolute;top:8px;right:8px;width:7px;height:11px;margin:0;background-position:-63px -115px;}a.gs_in_se .gs_ico{background-position:-100px -26px;}.gs_in_se:hover .gs_ico,.gs_in_se-gs_hvr .gs_ico,a.gs_in_se-gs_hvr .gs_ico{background-position:-166px -110px;}.gs_in_se:active .gs_ico,.gs_in_se .gs_ico:active,.gs_in_se.gs_in_se-gs_act .gs_ico{background-position:-89px -26px;}.gs_in_se :active~.gs_ico{background-position:-89px -26px;}.gs_btn_mnu .gs_ico{height:7px;background-position:-63px -119px;}.gs_btn_mnu:hover .gs_ico,.gs_btn_mnu-gs_hvr .gs_ico{background-position:-166px -114px;}.gs_btn_mnu:active .gs_ico,.gs_btn_mnu .gs_ico:active,.gs_btn_mnu.gs_btn_mnu-gs_act .gs_ico{background-position:-89px -30px;}.gs_btn_mnu :active~.gs_ico{background-position:-89px -30px;}.gs_md_se,.gs_md_wn,.gs_el_ph .gs_md_wp{position:absolute;top:0;left:0;border:1px solid #ccc;border-color:rgba(0,0,0,.2);background:white;-webkit-box-shadow:0px 2px 4px rgba(0,0,0,.2);-moz-box-shadow:0px 2px 4px rgba(0,0,0,.2);box-shadow:0px 2px 4px rgba(0,0,0,.2);z-index:1100; display:none;opacity:0;-webkit-transition:opacity .13s;-moz-transition:opacity .13s;-o-transition:opacity .13s;transition:opacity .13s;}.gs_md_se.gs_vis,.gs_md_wn.gs_vis,.gs_el_ph .gs_md_wp.gs_vis{opacity:1;-webkit-transition:all 0s;-moz-transition:all 0s;-o-transition:all 0s;transition:all 0s;}.gs_el_tc .gs_md_se,.gs_el_tc .gs_md_wn,.gs_el_ph.gs_el_tc .gs_md_wp{-webkit-transform-origin:100% 0;-moz-transform-origin:100% 0;-o-transform-origin:100% 0;transform-origin:100% 0;-webkit-transform:scale(1,0);-moz-transform:scale(1,0);-o-transform:scale(1,0);transform:scale(1,0);-webkit-transition:opacity .218s ease-out,-webkit-transform 0s .218s;-moz-transition:opacity .218s ease-out,-moz-transform: 0s .218s;-o-transition:opacity .218s ease-out,-o-transform: 0s .218s;transition:opacity .218s ease-out,transform 0s .218s;}.gs_el_ios .gs_md_se,.gs_el_ios .gs_md_wn,.gs_el_ph.gs_el_ios .gs_md_wp{-webkit-backface-visibility:hidden;}.gs_el_tc .gs_md_wn.gs_ttss,.gs_el_ph.gs_el_tc .gs_md_wp.gs_ttss{-webkit-transform:scale(0,1);-moz-transform:scale(0,1);-o-transform:scale(0,1);transform:scale(0,1);}.gs_el_tc .gs_md_wn.gs_ttzi,.gs_el_ph.gs_el_tc .gs_md_wp.gs_ttzi{-webkit-transform-origin:50% 50%;-moz-transform-origin:50% 50%;-o-transform-origin:50% 50%;transform-origin:50% 50%;-webkit-transform:scale(0,0);-moz-transform:scale(0,0);-o-transform:scale(0,0);transform:scale(0,0);}.gs_el_tc .gs_md_se.gs_vis,.gs_el_tc .gs_md_wn.gs_vis,.gs_el_ph.gs_el_tc .gs_md_wp.gs_vis{-webkit-transform:scale(1,1);-moz-transform:scale(1,1);-o-transform:scale(1,1);transform:scale(1,1);-webkit-transition:-webkit-transform .218s ease-out;-moz-transition:-moz-transform .218s ease-out;-o-transition:-o-transform .218s ease-out;transition:transform .218s ease-out;}.gs_md_se{white-space:nowrap}.gs_md_se ul{list-style-type:none;word-wrap:break-word;display:inline-block;*display:inline;zoom:1;vertical-align:top;}.gs_md_se li,.gs_md_li,.gs_md_li:link,.gs_md_li:visited{display:block;padding:6px 44px 6px 16px;font-size:13px;line-height:16px;color:#222;cursor:default;text-decoration:none;background:white;-moz-transition:background .13s;-o-transition:background .13s;-webkit-transition:background .13s;transition:background .13s;}a.gs_md_li:hover .gs_lbl,a.gs_md_li:active .gs_lbl{text-decoration:none}.gs_el_tc .gs_md_se li,.gs_el_tc .gs_md_li{padding-top:14px;padding-bottom:10px;}.gs_md_se li:hover,.gs_md_se li.gs_hvr,.gs_md_se li.gs_fcs,.gs_md_li:hover,.gs_md_li:focus,.gs_md_li-gs_fcs:link,.gs_md_li-gs_fcs:visited{background:#f1f1f1;-moz-transition:background 0s;-o-transition:background 0s;-webkit-transition:background 0s;transition:background 0s;}.gs_md_se li.gs_sel{color:#dd4b39}.gs_md_wn:focus,.gs_md_se li:focus,.gs_md_li:focus{outline:none}button.gs_btnG .gs_ico{width:14px;height:13px;top:7px;left:27px;background-position:-152px -68px;_background:transparent;_filter:progid:DXImageTransform.Microsoft.AlphaImageLoader(src='/intl/en/scholar/images/sprite/search-shared.png');}p,dl,dt,dd,caption,tbody,tfoot,thead,tr,th,td,fieldset{margin:0;padding:0;border:0}body {font-size:62.5%;color:#000;}#gs_top {padding:3px 8px;min-width:none;width:auto}a:link,a:visited {color:#00c;}a:active {color:red}ul {padding:0 0 1em 1em}ol {padding:0 0 1em 1.3em}li {line-height:1.5em;padding:0 0 .5em 0}p {padding:0 0 1em 0}h1,h2,h3,h4,h5 {padding:0 0 1em 0}h1,h2 {font-size:1.3em}h3 {font-size:1.1em}h4,h5,table {font-size:1em}sup,sub {font-size:.7em}input,select,textarea,option{font-family:inherit;font-size:inherit}.g-doc,.g-doc-1024,.g-doc-800 {font-size:130%}.g-doc {width:100%;text-align:left}.g-section:after {content:\".\";display:block;height:0;clear:both;visibility:hidden}.g-unit .g-section:after {clear:none}.g-unit .g-section {width:100%;overflow:hidden}.g-section,.g-unit {zoom:1}.g-split .g-unit {text-align:right}.g-split .g-first {text-align:left}.g-tpl-50-50 .g-unit,.g-unit .g-tpl-50-50 .g-unit,.g-unit .g-unit .g-tpl-50-50 .g-unit,.g-unit .g-unit .g-unit .g-tpl-50-50 .g-unit {width:49.999%;float:right;display:inline;margin:0}.g-unit .g-unit .g-unit .g-tpl-50-50 .g-first,.g-unit .g-unit .g-tpl-50-50 .g-first,.g-unit .g-tpl-50-50 .g-first,.g-tpl-50-50 .g-first {width:49.999%;float:left;display:inline;margin:0}.g-tpl-50-50-alt .g-unit,.g-unit .g-tpl-50-50-alt .g-unit,.g-unit .g-unit .g-tpl-50-50-alt .g-unit,.g-unit .g-unit .g-unit .g-tpl-50-50-alt .g-unit {width:49.999%;float:left;display:inline;margin:0}.g-unit .g-unit .g-unit .g-tpl-50-50-alt .g-first,.g-unit .g-unit .g-tpl-50-50-alt .g-first,.g-unit .g-tpl-50-50-alt .g-first,.g-tpl-50-50-alt .g-first {width:49.999%;float:right;display:inline;margin:0}.g-tpl-nest .g-unit,.g-unit .g-tpl-nest .g-unit,.g-unit .g-unit .g-tpl-nest .g-unit,.g-unit .g-unit .g-unit .g-tpl-nest .g-unit {float:left;width:auto;display:inline;margin:0}.g-tpl-nest-alt .g-unit,.g-unit .g-tpl-nest-alt .g-unit,.g-unit .g-unit .g-tpl-nest-alt .g-unit,.g-unit .g-unit .g-unit .g-tpl-nest-alt .g-unit {float:right;width:auto;display:inline;margin:0}.g-doc-1024 {width:73.074em;_width:950px;min-width:950px;margin:0 auto;text-align:left}.cit-bluegray, .cit-bluegray:visited {color: #77c;}.cit-gray {color: #666;}table.cit-bodytable {width: 100%;}td.cit-contentcell {vertical-align: top;}.cit-body b {background:#9fd9a7;display:block;height:1px;overflow:hidden;margin:0 2px;font-size:1px;line-height:1px;}.cit-body i {background:#9fd9a7;display:block;height:1px;overflow:hidden;margin:0 1px;font-size:1px;line-height:1px;}.cit-messagebox {text-align: center;}.cit-messagebox b {background: #fff1a8;display:block;height:1px;overflow:hidden;margin:0 2px;font-size:1px;line-height:1px;}.cit-messagebox i {background: #fff1a8;display:block;height:1px;overflow:hidden;margin:0 1px;font-size:1px;line-height:1px;}.cit-messagebox a {color: #00c;}.cit-messagebox.reqfield {text-align: left;padding: 0 0 0 0;color: #000;}.cit-messagebox table {display: inline;text-align: center;}.cit-messagebox div#message_text {background: #fff1a8;padding: 0.1em 0.5em;font-weight: bold;}.cit-actionbar {background: #9fd9a7;padding: 0.3em 0.6em;}.cit-actionbar.pagination {padding: 0 0 0 0;float: right;}.cit-fieldrows {padding-top: 0.5em;}.g-tpl-200 .g-unit,.g-unit .g-tpl-200 .g-unit,.g-unit .g-unit .g-tpl-200 .g-unit,.g-unit .g-unit .g-unit .g-tpl-200 .g-unit {display: block;margin: 0 0 0 200px;width: auto;float: none;}.g-unit .g-unit .g-unit .g-tpl-200 .g-first,.g-unit .g-unit .g-tpl-200 .g-first,.g-unit .g-tpl-200 .g-first,.g-tpl-200 .g-first {display: block;margin: 0;width: 200px;float: left;}.cit-near-box {background-color: #FFC;position: absolute;z-index: 1;/* more padding on right makes \"close window\" img prettier */padding: 5px 20px 5px 5px;border: 1px solid #CCC;}/* rounded corner outside */div.cit-rco {display:block;height:1px;overflow:hidden;margin:0 2px;font-size:1px;line-height:1px;}/* rounded corner inside */div.cit-rci {display:block;height:1px;overflow:hidden;margin:0 1px;font-size:1px;line-height:1px;}/* light blue background */.cit-lbb {background-color:#E8F4F7;}/* dark green background */.cit-dgb {background-color:#9FD9A7;}/* light green background */.cit-lgb {background-color:#DCF6DB;}.cit-dark-link {text-decoration: none;}.cit-light-link,.cit-light-link:visited {text-decoration: none;color: #4272db;}.cit-bg-link,.cit-bg-link:visited {text-decoration: none;color: #77c;}.cit-dark-large-link {font-size: 120%;text-decoration: none;}.cit-lclgs {padding-left: 1em;padding-top: 0.33em;padding-bottom: 0.33em;border-right: 2px solid #9fd9a7;}.cit-lclgsr {float: right;padding-right: 1em;}.cit-lclgsc {margin-top: 0.6em;float: left;}.cit-scdel {margin-top: 0.6em;margin-bottom: 0.6em;}/* Used for profile photo and name/affiliation */.g-tpl-150 .g-unit,.g-unit .g-tpl-150 .g-unit,.g-unit .g-unit .g-tpl-150 .g-unit,.g-unit .g-unit .g-unit .g-tpl-150 .g-unit {display: block;margin: 0 0 0 150px;width: auto;float: none;}.g-unit .g-unit .g-unit .g-tpl-150 .g-first,.g-unit .g-unit .g-tpl-150 .g-first,.g-unit .g-tpl-150 .g-first,.g-tpl-150 .g-first {display: block;margin: 0;width: 150px;float: left;}.g-tpl-100 .g-unit,.g-unit .g-tpl-100 .g-unit,.g-unit .g-unit .g-tpl-100 .g-unit,.g-unit .g-unit .g-unit .g-tpl-100 .g-unit {display: block;margin: 0 0 0 100px;width: auto;float: none;}.g-unit .g-unit .g-tpl-100 .g-first,.g-unit .g-unit .g-unit .g-tpl-100 .g-first,.g-unit .g-tpl-100 .g-first, .g-tpl-100 .g-first {display: block;margin: 0;width: 100px;float: left;}.cit-photo-placeholder {text-align: center;}.cit-photo {padding: 1px;border: solid #9fd9a7 1px;vertical-align: top;}.cit-definition {font-size: 1em;padding: 1em;}.cit-definition.emphasis {font-style: italic;}/* BEGIN used for in-place editing */#cit-name-display,#cit-name-input {font-size: 32px;}#cit-name-input {background-color: #FFFFCC;}#cit-affiliation-input,#cit-int-input,#cit-domain-input,#cit-homepage-input {background-color: #FFFFCC;font-size: 120%}#cit-affiliation-display,#cit-int-display,#cit-domain-display,#cit-publish-display,#cit-homepage-display {font-size: 120%;}#cit-name-input {width: 10em;}#cit-affiliation-input,#cit-int-input,#cit-domain-input,#cit-homepage-input {width: 25em;}.cit-in-place-hover {background-color: #FFC;border: 1px solid #CCC;}.cit-in-place-nohover {background-color: auto;border: 1px solid white;}.cit-inplaceaction, cit-inplaceaction:visited {font-size: 0.85em;text-decoration: none;color: #4272db;}.cit-inplaceform {padding: 1px;}/* END used for in-place editing */.cit-user-info {margin-top: 8px;}/* 250px alt *//* Right-column on myworks page. */.g-tpl-250-alt .g-unit,.g-unit .g-tpl-250-alt .g-unit,.g-unit .g-unit .g-tpl-250-alt .g-unit,.g-unit .g-unit .g-unit .g-tpl-250-alt .g-unit {margin: 0 250px 0 0;width: auto;float: none;}.g-unit .g-unit .g-unit .g-tpl-250-alt .g-first,.g-unit .g-unit .g-tpl-250-alt .g-first,.g-unit .g-tpl-250-alt .g-first,.g-tpl-250-alt .g-first {margin: 0;width: 250px;float: right;}.cit-table {width: 100%;}.cit-table.header {background: #dcf6db;}.cit-table.header td {padding-top: 0.3em;padding-bottom: 0.3em;border-top: 1px solid #AAA;}.cit-table.item {background: #e8f4f7;}.cit-table.item td {padding: 0.2em 0.5em 0.2em 0;}.cit-table.item td#col-year a {text-decoration: none;color: black;}#col-select,#col-citelink,#col-title,#col-citedby,#col-asterisk,#col-year,#col-choose {border-bottom: 1px solid #AAA;}#col-citedby {text-align:right;width:60px;}#col-year {text-align:center;width:50px;}#col-select {padding-right: 0;}#col-citelink {width: 4em;padding-right: 1em;}#col-title {padding-left: 0.5em;}table#stats {border-color: #aaa;border-width: 0 0 1px 1px;border-style: solid;margin: auto;}table#stats tr td {border-color: #aaa;border-width: 1px 1px 0 0;border-style: solid;padding: 0.2em 0.2em;}table#stats tr td.cit-caption {text-align: left;}table#stats tr.cit-headerrow td,table#stats tr td.cit-data {text-align: center;}</style><script type=\"text/javascript\">function citGetById(id) {return document.getElementById(id);}function citHide(id) {var elem = citGetById(id);if (!elem) return;elem.style.display = \"none\";return false;}function citShow(id, type) {var elem = citGetById(id);if (!elem) return;elem.style.display = type;return false;}function citSubmit(id) {var form = citGetById(id);if (form) form.submit();return false;}function citFocus(id) {var elem = citGetById(id);if (!elem) return;elem.focus();return false;}function citGo(url) {window.location = url;}function citOnActionsSelect(id, add_url, list_trash_url,list_updates_url) {var select = citGetById(id);if (!select) {return;}if (select.value == \"add_opt\") {citGo(add_url);} else if (select.value == \"list_trash_opt\") {citGo(list_trash_url);} else if (select.value == \"list_updates_opt\") {citGo(list_updates_url);} else {citSubmit(\"citationsForm\");}}function citToggleElem(id, displaytype) {var elem = citGetById(id);if (!elem) return;if (elem.style.display == displaytype) {elem.style.display = \"none\";} else {elem.style.display = displaytype;}return false;}function citClearTextHint(id, hint) {var elem = citGetById(id);if (!elem) return;if (elem.style.color == \"gray\" && elem.value == hint) {elem.value = \"\";elem.style.color = \"black\";} else if (elem.style.color == \"gray\" && elem.value != hint) {elem.style.color = \"black\";}return false;}function citSetTextHint(id, hint) {var elem = citGetById(id);if (!elem) return;if (elem.style.color == \"black\" && elem.value == \"\") {elem.value = hint;elem.style.color = \"gray\";}return false;}function citShowNearBox(near_me, message, vurl, vtext, hurl, htext) {var box = citGetById(\"cit-near-box\");if (!box) {box = document.createElement(\"div\");box.setAttribute(\"id\", \"cit-near-box\");box.setAttribute(\"class\", \"cit-near-box\");near_me.parentNode.appendChild(box);}box.innerHTML = message + \" (<a href=\\\"\" + vurl + \"\\\">\" + vtext + \"</a> - <a href=\\\"\" + hurl + \"\\\">\" + htext + \"</a>)  <img onclick=\\\"citHideNearBox()\\\" style=\\\"position: absolute; top:2px; right: 2px;\\\" src=\\\"/citations/images/remove.gif\\\"/>\";box.style.display = \"block\";}function citHideNearBox() {var box = citGetById(\"cit-near-box\");if (box) {box.style.display = \"none\";box.parentNode.removeChild(box);}}function citToggleNearBox(near_me, message, vurl, vtext, hurl, htext) {var box = citGetById(\"cit-near-box\");if (box &&box.style.display == \"block\" &&near_me.parentNode == box.parentNode) {citHideNearBox();} else {citHideNearBox();citShowNearBox(near_me, message, vurl, vtext, hurl, htext);}}function citMessageDisplay(msgBox, display) {if (!msgBox) return;try {msgBox.style.visibility = display;} catch (ex) {msgBox.setAttribute(\"style\", \"visibility: \" + display);}}function citSelectAll() {var form = citGetById(\"citationsForm\");for (var i = 0; i < form.elements.length; i++) {if (form.elements[i].type != \"checkbox\") continue;form.elements[i].checked = true;}}function citSelectNone() {var form = citGetById(\"citationsForm\");for (var i = 0; i < form.elements.length; i++) {if (form.elements[i].type != \"checkbox\") continue;form.elements[i].checked = false;}}function citInPlaceMouseOver(field) {citGetById(\"cit-\" + field + \"-display\").className = \"cit-in-place-hover\";}function citInPlaceMouseOut(field) {citGetById(\"cit-\" + field + \"-display\").className = \"cit-in-place-nohover\";}function citInPlaceResetAndEdit(field) {citInPlaceReset(field);citInPlaceEdit(field);}function citGetTextContent(id) {var elem = citGetById(id);if (!elem) return \"\";if (elem.innerText != undefined) {/* IE, Safari, Chrome, Opera */return elem.innerText;} else if (elem.textContent) {/* IE9, Firefox, Safari, Chrome, Opera */return elem.textContent;}return \"\";}function citInPlaceReset(field) {/* Reset the edit field to the current value. */if (field == \"int\" || field == \"domain\" || field == \"homepage\") {var currval = citGetTextContent(\"cit-\" + field + \"-prefill\");} else {var currval = citGetTextContent(\"cit-\" + field + \"-display\");}var input = citGetById(\"cit-\" + field + \"-input\");input.value = currval;}function citInPlaceEdit(field) {/* Hide display field and show input box. */citHide(\"cit-\" + field + \"-read\");citShow(\"cit-\" + field + \"-write\", \"inline\");var input = citGetById(\"cit-\" + field + \"-input\");if (input) input.focus();}function citInPlaceShow(field) {citHide(\"cit-\" + field + \"-read\");citShow(\"cit-\" + field + \"-write\", \"inline\");}function citInPlaceCancel(field) {/* Hide input box and show display field. */citHide(\"cit-\" + field + \"-write\");citShow(\"cit-\" + field + \"-read\", \"inline\");}function citToggleIndexDef(id) {var index_def = citGetById(id);if (!index_def) return;if (index_def.style.display &&index_def.style.display == \"block\") {citHideAllIndexDefs();} else {citHideAllIndexDefs();citShow(id, \"block\");}return false;}function citHideAllIndexDefs() {citHide('h_index_definition');citHide('i_ten_index_definition');citHide('total_citations_definition');citHide('h5_index_definition');citHide('h5_median_definition');return false;}function citMessageBox(messageBox, messageText) {this.messageBox = citGetById(messageBox);this.messageText = citGetById(messageText);this.timeoutId = null;}citMessageBox.prototype.hideMessage = function () {citMessageDisplay(this.messageBox, \"hidden\");};citMessageBox.prototype.showMessage = function (newText) {if (!newText) {this.hideMessage();} else {this.messageText.innerHTML = newText;citMessageDisplay(this.messageBox, \"visible\");this.setHideMessageTimeout();}};citMessageBox.prototype.setHideMessageTimeout = function () {this.cancelHideMessageTimeout();var _self = this;this.timeoutId = setTimeout(function () {_self.hideMessage();}, 60000);};citMessageBox.prototype.cancelHideMessageTimeout = function () {try {clearTimeout(this.timeoutId);} catch (ex) {/* Don't care. */}this.timeoutId = null;};var citGCMessageBox;function citInitGlobals() {citGCMessageBox = new citMessageBox(\"message_box\", \"message_text\");var showMsg = citGetById(\"cit-show-msg\");var shownMsg = citGetById(\"cit-shown-msg\");if (showMsg && showMsg.value && !(shownMsg && shownMsg.value)) {if (shownMsg) shownMsg.value = \"1\";citMessageDisplay(citGCMessageBox.messageBox, \"visible\");citGCMessageBox.setHideMessageTimeout();}if (window.addEventListener) {window.addEventListener(\"unload\", function() { citGCMessageBox.hideMessage(); }, false);} else if (window.attachEvent) {window.attachEvent(\"onunload\", function() {citGCMessageBox.hideMessage(); });}}if (window.addEventListener) {window.addEventListener(\"load\", citInitGlobals, false);} else if (window.attachEvent) {window.attachEvent(\"onload\", citInitGlobals);}</script><script>var gs_ie_ver=100;</script><!--[if IE 6]><script>gs_ie_ver=6;</script><![endif]--><!--[if IE 7]><script>gs_ie_ver=7;</script><![endif]--><!--[if IE 8]><script>gs_ie_ver=8;</script><![endif]--><script>function gs_id(i){return document.getElementById(i)}function gs_ch(e,t){return e?e.getElementsByTagName(t):[]}function gs_ech(e){return e.children||e.childNodes}function gs_atr(e,a){return e.getAttribute(a)}function gs_hatr(e,a){var n=e.getAttributeNode(a);return n&&n.specified}function gs_xatr(e,a,v){e.setAttribute(a,v)}function gs_uatr(e,a){e.removeAttribute(a)}function gs_catr(e,a,v){gs_hatr(e,a)&&gs_xatr(e,a,v)}function gs_ctai(e,v){gs_hatr(e,\"tabindex\")&&(e.tabIndex=v)}function gs_uas(s){return (navigator.userAgent||\"\").indexOf(s)>=0}var gs_is_tc=/[?&]tc=([01])/.exec(window.location.search||\"\"),gs_is_ios=gs_uas(\"iPhone\")||gs_uas(\"iPod\")||gs_uas(\"iPad\");if(gs_is_tc){gs_is_tc=parseInt(gs_is_tc[1]);}else if(window.matchMedia&&matchMedia(\"(pointer),(-moz-touch-enabled),(-moz-touch-enabled:0)\").matches){gs_is_tc=matchMedia(\"(pointer:coarse),(-moz-touch-enabled)\").matches;}else{gs_is_tc=gs_is_ios||gs_uas(\"Android\")||gs_uas(\"Windows Phone\")||gs_uas(\"BlackBerry\")||(gs_uas(\"AppleWebKit\")&&(gs_uas(\"Mobile\")||gs_uas(\"Tablet\")))||gs_uas(\"Opera Mini\")||gs_uas(\"Opera Mobi\")||gs_uas(\"IEMobile\")||('ontouchstart' in window)||(navigator.msMaxTouchPoints||0)>0;}var gs_re_sp=/\\s+/,gs_re_sel=/(?:^|\\s)(\\S*-)?gs_sel(-\\S*)?(?!\\S)/g,gs_re_hvr=/(?:^|\\s)(\\S*-)?gs_hvr(-\\S*)?(?!\\S)/g,gs_re_fcs=/(?:^|\\s)(\\S*-)?gs_fcs(-\\S*)?(?!\\S)/g,gs_re_act=/(?:^|\\s)(\\S*-)?gs_act(-\\S*)?(?!\\S)/g,gs_re_dis=/(?:^|\\s)(\\S*-)?gs_dis(-\\S*)?(?!\\S)/g,gs_re_vis=/(?:^|\\s)(\\S*-)?gs_vis(-\\S*)?(?!\\S)/g,gs_re_bsp=/(?:^|\\s)(\\S*-)?gs_bsp(-\\S*)?(?!\\S)/g,gs_re_cb=/(?:^|\\s)gs_in_cb(?!\\S)/,gs_re_ra=/(?:^|\\s)gs_in_ra(?!\\S)/;function gs_xcls(e,c){var l=e.className.split(gs_re_sp),n=l.length,i;l.push(c);for(i=0;i<n;i++){l[i]&&l.push(l[i]+\"-\"+c)}gs_scls(e,l.join(\" \"));}function gs_ucls(e,r){gs_scls(e,e.className.replace(r,\"\"))}function gs_scls(e,c){if(e.className!=c){e.className=c}}function gs_usel(e){gs_ucls(e,gs_re_sel)}function gs_xsel(e){gs_usel(e);gs_xcls(e,\"gs_sel\")}function gs_isel(e){(e.className.match(gs_re_sel)?gs_usel:gs_xsel)(e)}function gs_uhvr(e){gs_ucls(e,gs_re_hvr)}function gs_xhvr(e){gs_uhvr(e);gs_xcls(e,\"gs_hvr\");}function gs_ufcs(e){gs_ucls(e,gs_re_fcs)}function gs_xfcs(e){gs_ufcs(e);gs_xcls(e,\"gs_fcs\")}function gs_uact(e){gs_ucls(e,gs_re_act)}function gs_xact(e){gs_uact(e);gs_xcls(e,\"gs_act\")}function gs_udis(e){gs_ucls(e,gs_re_dis)}function gs_xdis(e){gs_udis(e);gs_xcls(e,\"gs_dis\")}function gs_uvis(e){gs_ucls(e,gs_re_vis)}function gs_xvis(e){gs_uvis(e);gs_xcls(e,\"gs_vis\")}function gs_ubsp(e){gs_ucls(e,gs_re_bsp)}function gs_xbsp(e){gs_ubsp(e);gs_xcls(e,\"gs_bsp\")}var gs_gcs=window.getComputedStyle?function(e){return getComputedStyle(e,null)}:function(e){return e.currentStyle};var gs_ctd=function(){var s=document.documentElement.style,p,l=['OT','MozT','webkitT','t'],i=l.length;function f(s){return Math.max.apply(null,(s||\"\").split(\",\").map(parseFloat))||0;}do{p=l[--i]+'ransition'}while(i&&!(p in s));return i?function(e){var s=gs_gcs(e);return f(s[p+\"Delay\"])+f(s[p+\"Duration\"]);}:function(){return 0};}();function gs_vis(e,v,c){var s=e&&e.style,n=\"webkitTransitionEnd\",h,f;if(s){gs_catr(e,\"aria-hidden\",v?\"false\":\"true\");if(v){s.display=v===2?\"inline\":\"block\";gs_ctd(e);gs_xvis(e);h=function(){e.removeEventListener(n,h,false);var t=pageYOffset+e.getBoundingClientRect().bottom,x=gs_id(\"gs_top\");t>x.offsetHeight&&(x.style.minHeight=t+\"px\");};f=gs_ctd(e);\"on\"+n.toLowerCase() in window&&f&&e.addEventListener(n,h,false);c&&(f?setTimeout(c,1000*f):c());}else{gs_uvis(e);h=function(){s.display=\"none\";var x=gs_id(\"gs_top\").style;x.minHeight&&(x.minHeight=\"\");c&&c();};f=gs_ctd(e);f?setTimeout(h,1000*f):h();}}}function gs_visi(i,v,c){gs_vis(gs_id(i),v,c)}function gs_sel_clk(p,t){var l=gs_ch(gs_id(p),\"li\"),i=l.length,c,x,s;while(i--){if((c=gs_ch(x=l[i],\"a\")).length){s=c[0]===t;(s?gs_xsel:gs_usel)(x);gs_catr(c[0],\"aria-selected\",s?\"true\":\"false\");}}return false;}function gs_efl(f){if(typeof f==\"string\"){var c=f.charAt(0),x=f.slice(1);if(c===\"#\")f=function(t){return t.id===x};else if(c===\".\")f=function(t){return (\" \"+t.className+\" \").indexOf(\" \"+x+\" \")>=0};else{c=f.toLowerCase();f=function(t){return t.nodeName.toLowerCase()===c};}}return f;}function gs_dfcn(d){return (d?\"last\":\"first\")+\"Child\"}function gs_dnsn(d){return (d?\"previous\":\"next\")+\"Sibling\"}var gs_trv=function(){function h(r,x,f,s,n,c){var t,p;while(x){if(x.nodeType===1){if(f(x)){if(c)return x;}else{for(p=x[s];p;p=p[n])if(t=h(p,p,f,s,n,1))return t;}}c=1;while(1){if(x===r)return;p=x.parentNode;if(x=x[n])break;x=p;}}}return function(r,x,f,d){return h(r,x,gs_efl(f),gs_dfcn(d),gs_dnsn(d))};}();function gs_bind(){var a=Array.prototype.slice.call(arguments),f=a.shift();return function(){return f.apply(null,a.concat(Array.prototype.slice.call(arguments)))};}var gs_evt_ie=[],gs_evt1=window.addEventListener?function(e,n,f){e.addEventListener(n,f,false);}:function(e,n,f){n=\"on\"+n;e.attachEvent(n,f);gs_ie_ver<=6&&gs_evt_ie.push(function(){e.detachEvent(n,f)});};function gs_evt(e,n,f){if(typeof n===\"string\")gs_evt1(e,n,f);else for(var i=n.length;i--;)gs_evt1(e,n[i],f);}function gs_evt_all(l,n,f){for(var i=l.length;i--;){gs_evt(l[i],n,gs_bind(f,l[i]))}}function gs_evt_dlg(p,c,n,f){p!==c&&(c=gs_efl(c));gs_evt(p,n,p===c?function(e){f(p,e)}:function(e){var t=gs_evt_tgt(e);while(t){if(c(t))return f(t,e);if(t===p)return;t=t.parentNode;}});}function gs_evt_sms(v){var L=[],l=[\"mousedown\",\"click\"],i=l.length;function s(e){for(var l=L,n=l.length,i=0,x=e.clientX,y=e.clientY;i<n;i+=2){if(Math.abs(x-l[i])<10&&Math.abs(y-l[i+1])<10){gs_evt_ntr(e);break;}}}while(i--)document.addEventListener(l[i],s,true);gs_evt_sms=function(e){var l=e.changedTouches||[],h=l[0]||{};L.push(h.clientX,h.clientY);setTimeout(function(){L.splice(0,2)},2000);};gs_evt_sms(v);v=0;}function gs_evt_clk(e,f,w,k){return gs_evt_dlg_clk(e,e,function(t,e){f(e)},w,k);}function gs_evt_dlg_clk(p,c,f,w,k){k=\",\"+(k||[13,32]).join(\",\")+\",\";return gs_evt_dlg_xclk(p,c,function(t,e){if(e.type==\"keydown\"){if(k.indexOf(\",\"+e.keyCode+\",\")<0)return;gs_evt_ntr(e);}f(t,e);},w);}var gs_evt_cl=[\"touchstart\",\"touchend\",\"touchcancel\",\"mousedown\",\"keydown\",\"keyup\",\"click\"];function gs_evt_xclk(e,f,w){return gs_evt_dlg_xclk(e,e,function(t,e){f(e)},w);}function gs_evt_dlg_xclk(p,c,f,w){var T,S=0,X=0,Y=0,O=0,V=0;function u(t,e){var n=e.type,h,l;if(t!==T){T=t;S=0;}if(!gs_evt_spk(e)){if(n===\"mousedown\"){if(w!==2)return S=0;S=1;}else if(n===\"click\"){if(S){gs_evt_ntr(e);return S=0;}}else if(n===\"touchstart\"&&w){if(e.timeStamp-V<200){gs_evt_ntr(e);return S=0;}if(w===2){S=0;}else{if((l=e.touches).length!==1)return S=-3;h=l[0];X=h.pageX;Y=h.pageY;O=pageYOffset;return S=3;}}else if(n===\"touchcancel\"){return S=0;}else if(n===\"touchend\"&&w){gs_evt_sms(e);V=e.timeStamp;if(w===2){gs_evt_ntr(e);return S=0;}if(S!==3||(l=e.changedTouches).length!==1||Math.abs(X-(h=l[0]).pageX)>10||Math.abs(Y-h.pageY)>10||Math.abs(O-pageYOffset)>1){return S=(e.touches.length?-4:0);}S=0;}else if(n===\"keydown\"){f(t,e);return;}else if(n===\"keyup\"){e.keyCode===32&&gs_evt_pdf(e);return;}else{return}gs_evt_ntr(e);f(t,e);}}gs_evt_dlg(p,c,w?gs_evt_cl:[\"click\",\"keydown\",\"keyup\"],u);return u;}function gs_evt_inp(e,f){gs_evt(e,[\"input\",\"keyup\",\"cut\",\"paste\",\"change\"],function(){setTimeout(f,0)});}function gs_evt_fcs(e,f){e.addEventListener(\"focus\",f,true)}function gs_evt_blr(e,f){e.addEventListener(\"blur\",f,true)}if(\"onfocusin\" in document){gs_evt_fcs=function(e,f){gs_evt(e,\"focusin\",f)};gs_evt_blr=function(e,f){gs_evt(e,\"focusout\",f)};}function gs_evt_stp(e){e.cancelBubble=true;e.stopPropagation&&e.stopPropagation();return false;}function gs_evt_pdf(e){e.returnValue=false;e.preventDefault&&e.preventDefault();}function gs_evt_ntr(e){gs_evt_stp(e);gs_evt_pdf(e);}function gs_evt_tgt(e){var t=e.target||e.srcElement;t&&t.nodeType===3&&(t=t.parentNode);return t;}function gs_evt_spk(e){return e.ctrlKey||e.altKey||e.metaKey||e.shiftKey}function gs_tfcs(t){if(!gs_is_tc||(gs_uas(\"Windows\")&&!gs_uas(\"IEMobile\"))){t.focus();t.value=t.value;}}var gs_raf=window.requestAnimationFrame||window.webkitRequestAnimationFrame||window.mozRequestAnimationFrame||function(c){setTimeout(c,33)};var gs_evt_rdy=function(){var d=document,l=[],h=function(e){var s=d.readyState,n=l.length,i=0;if(!s||s==\"interactive\"||s==\"complete\"){while(i<n)l[i++]();l=[];}};gs_evt(d,\"DOMContentLoaded\",h);gs_evt(d,\"readystatechange\",h);gs_evt(window,\"load\",h);return function(f){l.push(f)};}();function gs_evt_raf(e,n){var l=[],t=0,h=function(){var x=l,n=x.length,i=0;while(i<n)x[i++]();t=0;};return function(f){l.length||gs_evt(e,n,function(){!t++&&gs_raf(h)});l.push(f);};}var gs_evt_wsc=gs_evt_raf(window,\"scroll\"),gs_evt_wre=gs_evt_raf(window,\"resize\");var gs_md_st=[],gs_md_lv={},gs_md_fc={},gs_md_if,gs_md_is=0;function gs_md_ifc(d,f){gs_md_fc[d]=f}function gs_md_sif(){gs_md_if=1;setTimeout(function(){gs_md_if=0},0);}function gs_md_plv(n){var l=gs_md_lv,x=0;while(n&&!x){x=l[n.id]||0;n=n.parentNode;}return x;}gs_evt(document,\"click\",function(e){var m=gs_md_st.length;if(m&&!gs_evt_spk(e)&&m>gs_md_plv(gs_evt_tgt(e))){(gs_md_st.pop())();gs_evt_pdf(e);}});gs_evt(document,\"keydown\",function(e){e.keyCode==27&&!gs_evt_spk(e)&&gs_md_st.length&&(gs_md_st.pop())();});gs_evt(document,\"selectstart\",function(e){gs_md_is&&gs_evt_pdf(e)});gs_evt_fcs(document,function(e){var l=gs_md_lv,m=gs_md_st.length,x,k,v,d;if(m&&!gs_md_if){x=gs_md_plv(gs_evt_tgt(e));while(x<m){v=0;for(k in l)l.hasOwnProperty(k)&&l[k]>v&&(v=l[d=k]);if(v=gs_md_fc[d]){gs_evt_stp(e);gs_id(v).focus();break;}else{(gs_md_st.pop())(1);--m;!gs_md_is++&&setTimeout(function(){gs_md_is=0},1000);}}}});function gs_md_cls(d,e){var x=gs_md_lv[d]||1e6;while(gs_md_st.length>=x)(gs_md_st.pop())();return gs_evt_stp(e);}function gs_md_opn(d,e,c,z){if(!gs_md_lv[d]){var n=gs_id(d),x=gs_md_plv(n),a=document.activeElement;while(gs_md_st.length>x)(gs_md_st.pop())();gs_visi(d,1);gs_md_st.push(function(u){gs_md_lv[d]=0;gs_visi(d,0,z);try{u||a.focus()}catch(_){}c&&c(u);});gs_md_lv[d]=gs_md_st.length;return gs_evt_stp(e);}}function gs_evt_md_mnu(d,b,f,a,w){var O,X;d=gs_id(d);b=gs_id(b);f=f?gs_efl(f):function(t){return (gs_hatr(t,\"data-a\")||t.nodeName===\"A\"&&t.href)&&t.offsetWidth;};a=a||function(t){var c=gs_atr(t,\"data-a\");c?eval(c):t.nodeName===\"A\"&&t.href&&(location=t.href);};function u(e){if(e.type==\"keydown\"){var k=e.keyCode;if(k==38||k==40){if(O){try{gs_trv(d,d,f,k==38).focus()}catch(_){}gs_evt_ntr(e);return;}}else if(k!=13&&k!=32){return;}gs_evt_pdf(e);}if(O){gs_md_cls(d.id,e);}else{gs_md_sif();O=1;gs_xsel(b);gs_md_opn(d.id,e,function(){O=0;gs_usel(b);try{gs_ufcs(X);X.blur()}catch(_){}});w&&w();}}function c(x,r){var p=x.parentNode,c=gs_ech(p),i=c.length,l=\"offsetLeft\";if(p!==d){while(c[--i]!==x);p=p[gs_dnsn(r)]||p.parentNode[gs_dfcn(r)];c=gs_ech(p);if(i=Math.min(i+1,c.length)){p=c[i-1];if(p.nodeType==1&&f(p)&&p[l]!=x[l])return p;}}}function g(t,e){function m(x){if(x){gs_evt_ntr(e);x.focus();}}if(O){if(e.type==\"keydown\"){var k=e.keyCode;if(k==13||k==32){}else{if(k==38||k==40){m(gs_trv(d,t,f,k==38)||gs_trv(d,d,f,k==38));}else if(k==37||k==39){m(c(t,k==37));}return;}}gs_md_cls(d.id,e);gs_evt_pdf(e);gs_md_sif();a(t);}}gs_evt_xclk(b,u,2);gs_evt_fcs(d,function(e){var x=gs_evt_tgt(e);if(x&&f(x)){gs_ctai(x,0);gs_xfcs(x);X=x;}});gs_evt_blr(d,function(e){var x=gs_evt_tgt(e);if(x&&f(x)){gs_ctai(x,-1);gs_ufcs(x);X=0;}});gs_evt_dlg_xclk(d,f,g,1);return u;}function gs_evt_dlg_md_mnu(p,d,b,f,a,w){var r={};gs_evt_dlg(p,function(t){return d(t.id)},gs_evt_cl,function(t,e){var x=t.id;r[x]=r[x]||gs_evt_md_mnu(d(x),x,f,a,w)(e)||1;});}function gs_evt_md_sel(d,b,h,c,s,u){h=gs_id(h);c=gs_id(c);s=gs_id(s);return gs_evt_md_mnu(d,b,function(t){return gs_hatr(t,\"data-v\")},function(t){h.innerHTML=t.innerHTML;c.value=gs_atr(t,\"data-v\");if(t!==s){gs_usel(s);gs_uatr(s,\"aria-selected\");gs_xsel(s=t);gs_xatr(s,\"aria-selected\",\"true\");}u&&u();},function(){s.focus()});}function gs_xhr(){if(window.XMLHttpRequest)return new XMLHttpRequest();var c=[\"Microsoft.XMLHTTP\",\"MSXML2.XMLHTTP\",\"MSXML2.XMLHTTP.3.0\",\"MSXML2.XMLHTTP.6.0\"],i=c.length;while(i--)try{return new ActiveXObject(c[i])}catch(e){}}function gs_ajax(u,d,c){var r=gs_xhr();r.onreadystatechange=function(){r.readyState==4&&c(r.status,r.responseText);};r.open(d?\"POST\":\"GET\",u,true);d&&r.setRequestHeader(\"Content-Type\",\"application/x-www-form-urlencoded\");d?r.send(d):r.send();}var gs_lst,gs_xlst;if(window.localStorage){gs_lst=function(k){return localStorage[k]};gs_xlst=function(k,v){localStorage[k]=v};}else if(gs_ie_ver<8)!function(){var c;function l(){if(c)return c;c=document.createElement(\"div\");c.style.display=\"none\";c.style.behavior=\"url('#default#userdata')\";document.body.appendChild(c);return c;}gs_lst=function(k){l().load(\"s\");return gs_atr(l(),k)};gs_xlst=function(k,v){gs_xatr(l(),k,v);l().save(\"s\")};}();function gs_init_el(){var r=document.documentElement,w=window.innerWidth||r.offsetWidth,h=window.innerHeight||r.offsetHeight,m=\"\";if(w&&h){if(w<1060||h<590)m=\"gs_el_sm\";else if(w<1252||h<640)m=\"gs_el_me\";gs_is_tc&&gs_ie_ver>=8&&(m+=\" gs_el_tc\");gs_is_ios&&(m+=\" gs_el_ios\");gs_scls(r,m);}}gs_init_el();gs_evt_wre(gs_init_el);gs_evt(window,[\"pageshow\",\"load\"],gs_init_el);function gs_is_cb(e){var n=e.className||\"\";return n.match(gs_re_cb)||n.match(gs_re_ra);}function gs_ssel(e){}(function(d){function c(){var v=l,i=v.length,k=p,e,x=gs_id(\"gs_top\");if(x&&!r){gs_evt(x,\"click\",function(){});r=1;if(!s){clearInterval(t);t=null}}p=i;while(i-->k)gs_is_cb((e=v[i]).parentNode)&&gs_ssel(e);}var s=gs_ie_ver<=8,l=[],p=0,t=setInterval(c,200),r;gs_evt_rdy(function(){c();l=[];clearInterval(t)});if(!s&&gs_is_tc){s=/AppleWebKit\\/([0-9]+)/.exec(navigator.userAgent||\"\");s=s&&parseInt(s[1])<535;}if(!s)return;l=gs_ch(d,\"input\");gs_ssel=function(e){var p=e.parentNode,l,i,r;(e.checked?gs_xsel:gs_usel)(p);if(p.className.match(gs_re_ra)){l=e.form.elements[e.name]||[];for(i=l.length;i--;)((r=l[i]).checked?gs_xsel:gs_usel)(r.parentNode);}};gs_evt(d,\"click\",function(e){var x=gs_evt_tgt(e),p=x.parentNode;gs_is_cb(p)&&x.nodeName===\"INPUT\"&&gs_ssel(x);});if(gs_ie_ver>7)return;function w(t){var n=t&&t.nodeName;while(n===\"SPAN\"||n===\"B\"||n===\"I\")n=(t=t.parentNode).nodeName;return t;}var A;function y(f,e){var t=w(e.srcElement),n=t&&t.nodeName,p;if(n===\"INPUT\"||n===\"BUTTON\"||n===\"LABEL\"){p=t.parentNode;f(gs_is_cb(p)?p:t);}}gs_evt(d,\"mousedown\",gs_bind(y,function(t){gs_xact(A=t)}));gs_evt(d,\"mouseup\",function(){A&&(gs_uact(A),A=0)});gs_evt(d,\"focusin\",gs_bind(y,gs_xfcs));gs_evt(d,\"focusout\",gs_bind(y,gs_ufcs));if(gs_ie_ver>6)return;gs_evt(window,\"unload\",function(){var l=gs_evt_ie,i=l.length;while(i--){(l[i])()}l.length=0;});gs_evt(window,\"load\",function(){try{document.execCommand(\"BackgroundImageCache\",false,true)}catch(_){}});function h(t){var n=(t=w(t))&&t.nodeName,p;return n===\"BUTTON\"||n===\"LI\"?t:(n===\"INPUT\"||n===\"LABEL\")&&(gs_is_cb(p=t.parentNode)?p:t);}gs_evt(d,\"mouseover\",function(e){var f=h(e.fromElement),t=h(e.srcElement);t&&t!==f&&gs_xhvr(t);});gs_evt(d,\"mouseout\",function(e){var f=h(e.srcElement),t=h(e.toElement);f&&f!==t&&gs_uhvr(f);});function a(t){var n=(t=w(t))&&t.nodeName;return n===\"BUTTON\"&&t.type===\"submit\"&&t;}function k(f,b){var l=gs_ch(f,\"button\"),i=l.length,r=[],x;while(i--)(x=l[i]).type===\"submit\"&&r.push(x);if((i=r.length)>1){while(i--)(x=r[i])!==b&&(x.disabled=true);f.submit();}}gs_evt(d,\"keydown\",function(e){var v=e.srcElement,f,b;if(e.keyCode===13&&!gs_evt_spk(e)){for(f=v;f&&f.nodeName!==\"FORM\";f=f.parentNode);f&&((b=a(v))||v.nodeName===\"INPUT\"&&v.type===\"text\"?k(f,b):gs_evt_pdf(e));}});gs_evt(d,\"click\",function(e){var f,b;!gs_evt_spk(e)&&(b=a(e.srcElement))&&(f=b.form)&&k(f,b);});})(document);</script><script type=\"text/javascript\"></script></head><body><style>#gs_gb{position:relative;height:30px;background:#2d2d2d;z-index:950;font-size:13px;line-height:16px;-webkit-backface-visibility:hidden;}#gs_gb_lt,#gs_gb_rt,#gs_gb_lp{position:absolute;top:0;white-space:nowrap;}#gs_gb_lt{left:34px}.gs_el_me #gs_gb_lt{left:18px}.gs_el_sm #gs_gb_lt{left:6px}.gs_el_ph #gs_gb_lt{display:none}#gs_gb_lp{display:none}#gs_gb_lp:hover,#gs_gb_lp:focus,#gs_gb_lp:active{-webkit-filter:brightness(100%);}.gs_el_ph #gs_gb_lp{display:block;z-index:1;cursor:pointer;top:8px;left:8px;width:50px;height:17px;background:url('/intl/en/scholar/images/google_logo_tg_2012.png');_background:transparent;_filter:progid:DXImageTransform.Microsoft.AlphaImageLoader(src='/intl/en/scholar/images/google_logo_tg_2012.png');}#gs_gb_rt{right:36px}.gs_el_me #gs_gb_rt{right:18px}.gs_el_sm #gs_gb_rt{right:6px}.gs_el_ta #gs_gb_rt,.gs_el_ph #gs_gb_rt{right:0}#gs_gb_lt a:link,#gs_gb_lt a:visited,#gs_gb_rt a:link,#gs_gb_rt a:visited{display:inline-block;vertical-align:top;height:29px;line-height:27px;padding:2px 10px 0 10px;font-weight:bold;color:#bbb;cursor:pointer;text-decoration:none;}#gs_gb_rt a:link,#gs_gb_rt a:visited{padding:2px 8px 0 8px}#gs_gb_lt a:hover,#gs_gb_lt a:focus,#gs_gb_lt a:active,#gs_gb_rt a:hover,#gs_gb_rt a:focus,#gs_gb_rt a:active{color:white;outline:none;}#gs_gb_ac{top:30px;left:auto;right:6px;width:288px;white-space:normal;}#gs_gb_aw,#gs_gb_ap,.gs_gb_am,#gs_gb_ab{display:block;padding:10px 20px;}#gs_gb_aw{background:#fef9db;font-size:11px;}#gs_gb_ap,.gs_gb_am{border-bottom:1px solid #ccc;}#gs_gb_aa:link,#gs_gb_aa:visited{padding:7px 0 5px 0;float:right;margin-left:8px;color:#1155cc;}#gs_gb_aa:active{color:#d14836}.gs_gb_am:link,.gs_gb_am:visited{color:#222;text-decoration:none;}.gs_gb_am:hover,.gs_gb_am:focus{background:#f1f1f1}.gs_gb_am:active{background:#eee}#gs_gb_ab{background:#fbfbfb;overflow:auto;zoom:1;}#gs_gb_aab{float:left;margin-right:16px}#gs_gb_aso{float:right}</style><div id=\"gs_gb\" role=\"navigation\"><div id=\"gs_gb_lt\"><a href=\"http://www.google.com/webhp?hl=en&amp;\">Web</a><a href=\"http://www.google.com/imghp?hl=en&amp;\">Images</a><a href=\"http://www.google.com/intl/en/options/\">More&hellip;</a></div><a id=\"gs_gb_lp\" aria-label=\"Web\" href=\"http://www.google.com/webhp?hl=en&\"></a><div id=\"gs_gb_rt\"><a href=\"https://accounts.google.com/Login?hl=en&amp;continue=http://scholar.google.com/citations%3Fuser%3DHI-I6C0AAAAJ%26hl%3Den\">Sign in</a></div></div><div id=\"gs_top\"><div class=\"g-doc-1024\"><script type=\"text/javascript\">function citToggleEmbedLink() {citHide(\"cit-photo-form\");var elem = citGetById(\"cit-public-url\");citToggleElem(\"cit-public-url\", \"block\");return false;}function citTogglePhotoForm() {citHide(\"cit-public-url\");citToggleElem(\"cit-photo-form\", \"inline\");return false;}</script><div class=\"g-section g-tpl-250-alt\"><div class=\"g-unit g-first\"><div style=\"padding-left:1em\"><a href=\"http://scholar.google.com/schhp?hl=en&amp;oe=ASCII\" target=\"_top\"><img src=\"http://scholar.google.com/intl/en/scholar/images/scholar_logo_md_2011.gif\" width=\"194\" height=\"40\" alt=\"Scholar Home\" border=0></a><form method=\"get\" action=\"/citations\"><input type=hidden name=hl value=\"en\"><input type=hidden name=oe value=\"ASCII\"><input type=\"hidden\" name=\"view_op\" value=\"search_authors\"><input type=\"text\" name=\"mauthors\" style=\"width:100%\"><br><input type=\"submit\"  value=\"Search Authors\"></form><br><div style=\"font-size:120%\"><a href=\"/citations?hl=en&amp;oe=ASCII\" class=\"cit-dark-link\" target=\"_top\">Get my own profile</a> - <a class=\"cit-dark-link\" href=\"/intl/en/scholar/citations.html\">Help</a></div><br></div><div style=\"padding-left:1em\"><div class=\"g-section\"><div class=\"cit-rco cit-dgb\"></div><div class=\"cit-rci cit-dgb\"></div><div class=\"cit-dgb\" style=\"font-weight: bold\">&nbsp;Follow this author</div><div style=\"padding:.5em; border:.3em solid #9FD9A7\"><div>29 Followers</div><hr><div><a class=\"cit-dark-link\" href=\"javascript:void(0)\" onclick=\"citHide('cit-follow-citations-form');citToggleElem('cit-follow-articles-form', 'block')\">Follow new articles</a></div><div id=\"cit-follow-articles-form\" style=\"display:none\"><form method=\"post\" action=\"/citations?hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works\" style=\"border:2px solid #9FD9A7; padding:0.5em;margin-top:0.5em\">Create email alert for new articles in this profile<input type=\"hidden\" name=\"xsrf\" value=\"AMstHGQAAAAAUtTAfwu0eEKbKa4CwRVSEsbSP-uCuF4e\"><input type=\"hidden\" name=\"user\" value=\"HI-I6C0AAAAJ\"><input type=\"text\" size=\"25\" maxlength=\"100\" id=\"cit-articles-email\" style=\"color:gray;\" name=\"email_for_op\" value=\"Email\"onfocus=\"citClearTextHint('cit-articles-email', 'Email')\" onblur=\"citSetTextHint('cit-articles-email','Email')\"><br><input type=\"submit\"  name=\"follow_articles_btn\" value=\"Create alert\"> <input type=\"button\" name=\"cancel_follow_articles_btn\" value=\"Cancel\" onclick=\"citHide('cit-follow-articles-form', 'block')\"></form></div><div><a class=\"cit-dark-link\" href=\"javascript:void(0)\" onclick=\"citHide('cit-follow-articles-form');citToggleElem('cit-follow-citations-form', 'block')\">Follow new citations</a></div><div id=\"cit-follow-citations-form\" style=\"display:none\"><form method=\"post\" action=\"/citations?hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works\" style=\"border:2px solid #9FD9A7; padding:0.5em;margin-top:0.5em\">Create email alert for new citations to articles in this profile<input type=\"hidden\" name=\"xsrf\" value=\"AMstHGQAAAAAUtTAfwu0eEKbKa4CwRVSEsbSP-uCuF4e\"><input type=\"hidden\" name=\"user\" value=\"HI-I6C0AAAAJ\"><input type=\"text\" size=\"25\" maxlength=\"100\" id=\"cit-citations-email\" style=\"color:gray;\" name=\"email_for_op\" value=\"Email\"onfocus=\"citClearTextHint('cit-citations-email', 'Email')\" onblur=\"citSetTextHint('cit-citations-email','Email')\"><br><input type=\"submit\"  name=\"follow_citations_btn\" value=\"Create alert\"> <input type=\"button\" name=\"cancel_follow_citations_btn\" value=\"Cancel\" onclick=\"citHide('cit-follow-citations-form','block')\"></form></div></div></div><br><div class=\"g-section\"><div class=\"cit-rco cit-dgb\"></div><div class=\"cit-rci cit-dgb\"></div><div class=\"cit-dgb\" style=\"font-weight: bold\">&nbsp;Co-authors</div><div style=\"padding:.5em; border:.3em solid #9FD9A7\"><span style=\"color:#000\">No co-authors</span></div></div></div></div><div class=\"g-unit\"><div class=\"cit-user-info\"><div class=\"g-section g-tpl-150\"><div class=\"g-unit g-first\"><div style=\"text-align: center;\"><a href=\"/citations?user=HI-I6C0AAAAJ&amp;hl=en&amp;oe=ASCII\"><img width=\"113\" height=\"150\" src=\"/citations?view_op=view_photo&amp;user=HI-I6C0AAAAJ&amp;citpid=1\"></a><br><a class=\"cit-inplaceaction\" href=\"javascript:void(0)\" onclick=\"citTogglePhotoForm();\"></a></div></div><div class=\"g-unit\"><div style=\"margin-left:6px\"><form id=\"cit-name-form\" method=\"post\" class=\"cit-inplaceform\" action=\"\"><span id=\"cit-name-read\"><span id=\"cit-name-display\" class=\"cit-in-place-nohover\">Jeff Leek</span></span></form><form id=\"cit-affiliation-form\" method=\"post\" class=\"cit-inplaceform\" action=\"\"><span id=\"cit-affiliation-read\"><span id=\"cit-affiliation-display\" class=\"cit-in-place-nohover\">Assistant Professor of Biostatistics, Johns Hopkins Bloomberg School of Public Health</span></span></form><form id=\"cit-int-form\" method=\"post\" class=\"cit-inplaceform\" action=\"\"><span id=\"cit-int-read\"><a class=\"cit-dark-large-link\" href=\"/citations?view_op=search_authors&amp;hl=en&amp;oe=ASCII&amp;mauthors=label:statistics\">Statistics</a>&nbsp;<span style=\"font-size:120%\">- </span><a class=\"cit-dark-large-link\" href=\"/citations?view_op=search_authors&amp;hl=en&amp;oe=ASCII&amp;mauthors=label:computing\">Computing</a>&nbsp;<span style=\"font-size:120%\">- </span><a class=\"cit-dark-large-link\" href=\"/citations?view_op=search_authors&amp;hl=en&amp;oe=ASCII&amp;mauthors=label:genomics\">Genomics</a>&nbsp;<span style=\"font-size:120%\">- </span><a class=\"cit-dark-large-link\" href=\"/citations?view_op=search_authors&amp;hl=en&amp;oe=ASCII&amp;mauthors=label:personalized_medicine\">Personalized Medicine</a>&nbsp;<span style=\"font-size:120%\">- </span><a class=\"cit-dark-large-link\" href=\"/citations?view_op=search_authors&amp;hl=en&amp;oe=ASCII&amp;mauthors=label:scientific_communication\">Scientific Communication</a>&nbsp;</span></form><form id=\"cit-domain-form\" method=\"post\" class=\"cit-inplaceform\" action=\"\"><span id=\"cit-domain-read\"><span id=\"cit-domain-display\" class=\"cit-in-place-nohover\">Verified email at jhsph.edu</span></span></form><form id=\"cit-homepage-form\" method=\"post\" class=\"cit-inplaceform\" style=\"display:inline\" action=\"\"><span id=\"cit-homepage-read\"><a class=\"cit-dark-large-link\" href=\"http://biostat.jhsph.edu/~jleek/research.html\" rel=\"nofollow\" target=\"_blank\">Homepage</a></span></form></div></div></div></div><div class=\"g-section\"><div class=\"cit-messagebox\" id=\"message_box\" style=\"visibility:hidden\"><table style=\"display:inline-table\"><tr><td><b></b><i></i><form style=\"display:none\"><input type=\"hidden\" id=\"cit-show-msg\" value=\"\"><input type=\"text\" id=\"cit-shown-msg\" value=\"\"></form><form action=\"\" method=\"post\" name=\"undoFrm\"><input type=\"hidden\" name=\"\" value=\"\"/><div id=\"message_text\">&nbsp;</div></form><i></i><b></b></td></tr></table></div></div><div class=\"cit-rco cit-lbb\"></div><div class=\"cit-rci cit-lbb\"></div><div class=\"cit-lbb\"><table style=\"text-align:center;width:100%\"><tr><td valign=top><b>Citation indices</b><br><table id=\"stats\" cellspacing=\"0\" cellpadding=\"0\" width=\"205\"><tr class=\"cit-headerrow\"><td class=\"cit-caption\">&nbsp;</td><td class=\"cit-borderleft\">All</td><td class=\"cit-borderleft\">Since 2009</td></tr><tr class=\"cit-borderbottom\"><td class=\"cit-caption\"><a href=\"#\" class=\"cit-dark-link\" onclick=\"return citToggleIndexDef('total_citations_definition')\" title='This is the number of citations to all publications. The second column has the &quot;recent&quot; version of this metric which is the number of new citations in the last 5 years to all publications.'>Citations</a></td><td class=\"cit-borderleft cit-data\">1874</td><td class=\"cit-borderleft cit-data\">1606</td></tr><tr class=\"cit-borderbottom\"><td class=\"cit-caption\"><a href=\"#\" class=\"cit-dark-link\" onclick=\"return citToggleIndexDef('h_index_definition')\" title='h-index is the largest number h such that h publications have at least h citations. The second column has the &quot;recent&quot; version of this metric which is the largest number h such that h publications have at least h new citations in the last 5 years.'>h-index</a></td><td class=\"cit-borderleft cit-data\">14</td><td class=\"cit-borderleft cit-data\">14</td></tr><tr><td class=\"cit-caption\"><a href=\"#\" class=\"cit-dark-link\" onclick=\"return citToggleIndexDef('i_ten_index_definition')\" title='i10-index is the number of publications with at least 10 citations. The second column has the &quot;recent&quot; version of this metric which is the number of publications that have received at least 10 new citations in the last 5 years.'>i10-index</a></td><td class=\"cit-borderleft cit-data\">18</td><td class=\"cit-borderleft cit-data\">18</td></tr></table></td><td valign=top width=\"475\"><b>Citations to my articles</b><br><img src=\"http://www.google.com/chart?chs=475x90&amp;cht=bvs&amp;chf=bg,s,e8f4f7&amp;chco=1111cc&amp;chbh=r,2.0,0.0&amp;chxt=x,y&amp;chxr=1,0,537,537&amp;chd=t:1.5,8.0,15.1,22.7,19.0,35.8,52.0,83.2,100.0,5.0&amp;chxl=0:|2005|||2008|||2011|||2014\" height=\"90\" width=\"475\" alt=\"Citations to my articles\"></td></tr></table><div id=\"h_index_definition\" style=\"display:none\" class=\"cit-definition emphasis\">h-index is the largest number h such that h publications have at least h citations. The second column has the &quot;recent&quot; version of this metric which is the largest number h such that h publications have at least h new citations in the last 5 years. &nbsp;<font size=\"-1\"><a class=\"cit-light-link\" href=\"#\" onclick=\"return citHide('h_index_definition')\">hide</a></font></div><div id=\"i_ten_index_definition\" style=\"display:none\" class=\"cit-definition emphasis\">i10-index is the number of publications with at least 10 citations. The second column has the &quot;recent&quot; version of this metric which is the number of publications that have received at least 10 new citations in the last 5 years. &nbsp;<font size=\"-1\"><a class=\"cit-light-link\" href=\"#\" onclick=\"return citHide('i_ten_index_definition')\">hide</a></font></div><div id=\"total_citations_definition\" style=\"display:none\" class=\"cit-definition emphasis\">This is the number of citations to all publications. The second column has the &quot;recent&quot; version of this metric which is the number of new citations in the last 5 years to all publications. &nbsp;<font size=\"-1\"><a class=\"cit-light-link\" href=\"#\" onclick=\"return citHide('total_citations_definition')\">hide</a></font></div></div><form method=\"post\" action=\"/citations?hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works\" id=\"citationsForm\"><input type=\"hidden\" name=\"xsrf\" value=\"AMstHGQAAAAAUtTAfwu0eEKbKa4CwRVSEsbSP-uCuF4e\"><div class=\"g-section cit-dgb\"><div style=\"padding:0.3em 0.6em;\"><table style=\"width:100%\"><tr><td></td><td style=\"text-align:right;\">Show:<select onchange=\"citGo('/citations?hl\\x3den\\x26oe\\x3dASCII\\x26user\\x3dHI-I6C0AAAAJ\\x26view_op\\x3dlist_works\\x26pagesize\\x3d100')\"><option selected>20</option><option>100</option></select> <span style=\"font-weight: bold; margin: 0 0.5em 0 0.5em;\">1-20</span><a href=\"/citations?hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works&amp;cstart=20\" class=\"cit-dark-link\">Next &gt;</a></td></tr></table></div></div><div class=\"g-section\"><div style=\"min-height: 200px; height: auto !important;height: 200px; border-left: 0.4em solid #9fd9a7;border-bottom: 0.3em solid #9fd9a7;\"><table class=\"cit-table\"><tr class=\"cit-table header\"><td id=\"col-title\"><a href=\"/citations?sortby=title&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works\" class=\"cit-dark-link\">Title / Author</a></td><td id=\"col-citedby\">Cited by</td><td id=\"col-asterisk\">&nbsp;</td><td id=\"col-year\"><a href=\"/citations?sortby=pubdate&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works\" class=\"cit-dark-link\">Year</a></td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:u5HHmVD_uO8C\" class=\"cit-dark-large-link\">Significance analysis of time course microarray experiments</a><br><span class=\"cit-gray\">JD Storey, W Xiao, JT Leek, RG Tompkins, RW Davis</span><br><span class=\"cit-gray\">Proceedings of the National Academy of Sciences of the United States of ...</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=8692182196465189153\">397</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2005</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:d1gkVwhDpl0C\" class=\"cit-dark-large-link\">Capturing heterogeneity in gene expression studies by surrogate variable analysis</a><br><span class=\"cit-gray\">JT Leek, JD Storey</span><br><span class=\"cit-gray\">PLoS Genetics 3 (9), e161</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=14943880347723800617\">259</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2007</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:UeHWp8X0CEIC\" class=\"cit-dark-large-link\">Tackling the widespread and critical impact of batch effects in high-throughput data</a><br><span class=\"cit-gray\">JT Leek, RB Scharpf, HC Bravo, D Simcha, B Langmead, WE Johnson, D Geman, K ...</span><br><span class=\"cit-gray\">Nature Reviews Genetics 11 (10), 733-739</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=16403016020126782741\">237</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2010</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:u-x6o8ySG0sC\" class=\"cit-dark-large-link\">EDGE: extraction and analysis of differential gene expression</a><br><span class=\"cit-gray\">JT Leek, E Monsen, AR Dabney, JD Storey</span><br><span class=\"cit-gray\">Bioinformatics 22 (4), 507-508</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=8862746171832697560\">172</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2006</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:IjCSPb-OGe4C\" class=\"cit-dark-large-link\">Cloud-scale RNA-sequencing differential expression analysis with Myrna</a><br><span class=\"cit-gray\">B Langmead, KD Hansen, JT Leek</span><br><span class=\"cit-gray\">Genome Biol 11 (8), R83</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=9392678635432440961\">138</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2010</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:qjMakFHDy7sC\" class=\"cit-dark-large-link\">Systems-level dynamic analyses of fate change in murine embryonic stem cells</a><br><span class=\"cit-gray\">R Lu, F Markowetz, RD Unwin, JT Leek, EM Airoldi, BD MacArthur, A Lachmann ...</span><br><span class=\"cit-gray\">Nature 462 (7271), 358-362</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=4114573275173642545\">125</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2009</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:9yKSN-GCB0IC\" class=\"cit-dark-large-link\">The optimal discovery procedure for large-scale significance testing, with applications to comparative microarray experiments</a><br><span class=\"cit-gray\">JD Storey, JY Dai, JT Leek</span><br><span class=\"cit-gray\">bepress</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=16099641174606741636\">122</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2005</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:2osOgNQ5qMEC\" class=\"cit-dark-large-link\">A general framework for multiple testing dependence</a><br><span class=\"cit-gray\">JT Leek, JD Storey</span><br><span class=\"cit-gray\">Proceedings of the National Academy of Sciences 105 (48), 18718-18723</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=6862569568109470250\">109</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2008</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:Se3iqnhoufwC\" class=\"cit-dark-large-link\">Temporal dynamics and genetic control of transcription in the human prefrontal cortex</a><br><span class=\"cit-gray\">C Colantuoni, BK Lipska, T Ye, TM Hyde, R Tao, JT Leek, EA Colantuoni, AG ...</span><br><span class=\"cit-gray\">Nature 478 (7370), 519-523</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=15401379057477374674\">101</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:LkGwnXOMwfcC\" class=\"cit-dark-large-link\">Sequencing technology does not eliminate biological variability</a><br><span class=\"cit-gray\">KD Hansen, Z Wu, RA Irizarry, JT Leek</span><br><span class=\"cit-gray\">Nature biotechnology 29 (7), 572-573</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=13984583295080419572\">34</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:8k81kl-MbHgC\" class=\"cit-dark-large-link\">The sva package for removing batch effects and other unwanted variation in high-throughput experiments</a><br><span class=\"cit-gray\">JT Leek, WE Johnson, HS Parker, AE Jaffe, JD Storey</span><br><span class=\"cit-gray\">Bioinformatics 28 (6), 882-883</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=8144311546232376767\">26</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2012</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:0EnyYjriUFMC\" class=\"cit-dark-large-link\">ReCount: a multi-experiment resource of analysis-ready RNA-seq gene count datasets</a><br><span class=\"cit-gray\">A Frazee, B Langmead, J Leek</span><br><span class=\"cit-gray\">BMC bioinformatics 12 (1), 449</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=5499264026160610251\">26</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:5nxA0vEk-isC\" class=\"cit-dark-large-link\">Bump hunting to identify differentially methylated regions in epigenetic epidemiology studies</a><br><span class=\"cit-gray\">AE Jaffe, P Murakami, H Lee, JT Leek, MD Fallin, AP Feinberg, RA Irizarry</span><br><span class=\"cit-gray\">International journal of epidemiology 41 (1), 200-209</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=13917393765835926103\">24</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2012</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:hqOjcs7Dif8C\" class=\"cit-dark-large-link\">Significance analysis and statistical dissection of variably methylated regions</a><br><span class=\"cit-gray\">AE Jaffe, AP Feinberg, RA Irizarry, JT Leek</span><br><span class=\"cit-gray\">Biostatistics 13 (1), 166-178</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=8713725236803269731\">19</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2012</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:WF5omc3nYNoC\" class=\"cit-dark-large-link\">Cooperation between referees and authors increases peer review accuracy</a><br><span class=\"cit-gray\">JT Leek, MA Taub, FJ Pineda</span><br><span class=\"cit-gray\">PloS one 6 (11), e26895</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=2817868503282973196\">13</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:Tyk-4Ss8FVUC\" class=\"cit-dark-large-link\">Asymptotic Conditional Singular Value Decomposition for High&#8208;Dimensional Genomic Data</a><br><span class=\"cit-gray\">JT Leek</span><br><span class=\"cit-gray\">Biometrics 67 (2), 344-352</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=16751206081910057670\">12</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:_FxGoFyzp5QC\" class=\"cit-dark-large-link\">Dissecting inflammatory complications in critically injured patients by within-patient gene expression changes: a longitudinal clinical genomics study</a><br><span class=\"cit-gray\">KH Desai, CS Tan, JT Leek, RV Maier, RG Tompkins, JD Storey</span><br><span class=\"cit-gray\">PLoS medicine 8 (9), e1001093</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=10923603496726693889\">10</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:Y0pCki6q_DkC\" class=\"cit-dark-large-link\">The tspair package for finding top scoring pair classifiers in R</a><br><span class=\"cit-gray\">JT Leek</span><br><span class=\"cit-gray\">Bioinformatics 25 (9), 1203-1204</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=10521226315216814638\">10</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2009</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:ufrVoPGSRksC\" class=\"cit-dark-large-link\">The joint null criterion for multiple hypothesis tests</a><br><span class=\"cit-gray\">JT Leek, JD Storey</span><br><span class=\"cit-gray\">Statistical Applications in Genetics and Molecular Biology 10 (1)</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=4881367676737276124\">7</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2011</td></tr> <tr class=\"cit-table item\"><td id=\"col-title\"><a href=\"/citations?view_op=view_citation&amp;hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;citation_for_view=HI-I6C0AAAAJ:zYLM7Y9cAGgC\" class=\"cit-dark-large-link\">The optimal discovery procedure II: applications to comparative microarray experiments</a><br><span class=\"cit-gray\">JD Storey, JY Dai, JT Leek</span><br><span class=\"cit-gray\">UW Biostatistics Working Paper Series</span></td><td id=\"col-citedby\"><a class=\"cit-dark-link\" href=\"http://scholar.google.com/scholar?oi=bibs&amp;hl=en&amp;oe=ASCII&amp;cites=2916467767938830107\">6</a></td><td id=\"col-asterisk\"></td><td id=\"col-year\">2005</td></tr></table></div></div><div class=\"g-section cit-dgb\"><div style=\"padding:0.3em 0.6em;\"><table style=\"width:100%\"><tr><td></td><td style=\"text-align:right;\">Show:<select onchange=\"citGo('/citations?hl\\x3den\\x26oe\\x3dASCII\\x26user\\x3dHI-I6C0AAAAJ\\x26view_op\\x3dlist_works\\x26pagesize\\x3d100')\"><option selected>20</option><option>100</option></select> <span style=\"font-weight: bold; margin: 0 0.5em 0 0.5em;\">1-20</span><a href=\"/citations?hl=en&amp;oe=ASCII&amp;user=HI-I6C0AAAAJ&amp;view_op=list_works&amp;cstart=20\" class=\"cit-dark-link\">Next &gt;</a></td></tr></table></div></div></form><div class=\"cit-rci cit-dgb\"></div><div class=\"cit-rco cit-dgb\"></div></div></div><div class=\"g-section\"><div style=\"margin-top: 1em; text-align: center; font-size: 1em;\"><div style=\"margin-bottom: 1em;font-style: italic\">Dates and citation counts are estimated and are determined automatically by a computer program.</div>&copy;2013 Google - <a href=\"/intl/en/scholar/about.html\" class=\"cit-dark-link\" target=\"_top\">About Google Scholar</a> - <a href=\"http://www.google.com/intl/en/about.html\" class=\"cit-dark-link\" target=\"_top\">All About Google</a> - <a href=\"http://www.google.com/support/scholar/bin/request.py?contact_type=general\" class=\"cit-dark-link\" target=\"_top\">Provide feedback</a> - <a href=\"/citations?hl=en&amp;oe=ASCII\" class=\"cit-dark-link\" target=\"_top\">My Citations</a></div></div></div></div></body></html>"
```






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

## Parsing with XML

```r
library(XML)
url <- "http://scholar.google.com/citations?user=HI-I6C0AAAAJ&hl=en"
html <- htmlTreeParse(url, useInternalNodes=T)
html
#<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "http://www.w3.org/TR/REC-html40/loose.dtd">
<?xml version="1.0" encoding="UTF-8"?><html><body><breakfast_menu><food><name>Belgian Waffles</name><price>$5.95</price><description>Two of our famous Belgian Waffles with plenty of real maple syrup</description><calories>650</calories></food> .......
# The output is in one line

xpathSApply(html, "//title", xmlValue)
```

```
[1] "Jeff Leek - Google Scholar Citations"
```

```r
xpathSApply(html, "//td[@id='col-citedby']", xmlValue)
```

```
 [1] "Cited by" "397"      "259"      "237"      "172"      "138"      "125"      "122"     
 [9] "109"      "101"      "34"       "26"       "26"       "24"       "19"       "13"      
[17] "12"       "10"       "10"       "7"        "6"       
```

## Alternative : Using `Get` from the httr package
```r
library(httr)
html2 = GET(url)
content2 = content(html2,as="text")
parsedHtml = htmlParse(content2,asText=TRUE)
xpathSApply(parsedHtml, "//title", xmlValue) 
```
```
[1] "Jeff Leek - Google Scholar Citations"
```

## Accessing websites with passwords
```r
pg1 = GET("http://httpbin.org/basic-auth/user/passwd")
pg1
```

```
Response [http://httpbin.org/basic-auth/user/passwd]
  Status: 401
  Content-type: 
 
```
[http://cran.r-project.org/web/packages/httr/httr.pdf](http://cran.r-project.org/web/packages/httr/httr.pdf)

```r
pg2 = GET("http://httpbin.org/basic-auth/user/passwd",
    authenticate("user","passwd"))
pg2
```

```
Response [http://httpbin.org/basic-auth/user/passwd]
  Status: 200
  Content-type: application/json
{
  "authenticated": true,
  "user": "user"
} 
```

```r
names(pg2)
```

```
[1] "url"         "handle"      "status_code" "headers"     "cookies"     "content"    
[7] "times"       "config"     
```
## Using handles
```r
google = handle("http://google.com")
pg1 = GET(handle=google,path="/")
pg2 = GET(handle=google,path="search")
```
## Notes and further resources

* R Bloggers has a number of examples of web scraping [http://www.r-bloggers.com/?s=Web+Scraping](http://www.r-bloggers.com/?s=Web+Scraping)
* The httr help file has useful examples [http://cran.r-project.org/web/packages/httr/httr.pdf](http://cran.r-project.org/web/packages/httr/httr.pdf)
* See later lectures on APIs

# Reading from APIs

## Application programming interfaces
<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/twitter.png">

* APIs are where you can download data, with GET requests with specific url arguments
* you'll need to create a developer account for each one, eg for Twitter: <https://dev.twitter.com/docs/api/1/get/blocks/blocking>
* creating a Twitter app: <https://dev.twitter.com/apps>; Sign in with your Twitter account-My applications--create a new application

## Accessing Twitter from R
```r
myapp = oauth_app("twitter", key = "yourKey", secret="yourSecret") # first parameter 'twitter' is your choice to make
sig = sign_oauth1.0(myapp, token="yourToken", token_secret="yourTokenSecret")
homeTL = GET("https://api.twitter.com/1.1/statuses/home_timeline.json", sig) # note how you're passing authentication here
```
## then to convert json object
```r
json1 = content(homeTL) # recognizes and extracts json data
json2 = jsonlite::fromJSON(toJSON(json1)) # reformats json extract as a data frame
json2[1,1:4]
```

**what url to use?** 
<img src="https://raw.githubusercontent.com/DataScienceSpecialization/courses/master/assets/img/03_ObtainingData/twitterurl.png">
<https://dev.twitter.com/docs/api/1.1/get/search/tweets>, Resource URL

**In general, look at (Twitter) documentation**
* httr allows `GET, POST, PUT, DELETE` when you are authorized
* you can authenticate with a user name or a password
* most modern APIs use something like oauth for authentication
* httr works well with Facebook, Google, Twitter, Github, etc.


# Reading from other sources

Google "data storage mechanism R package", eg. "MySQL R package"   

## Interacting more directly with files     
* file: open connection to text file
* url: open conn to url
* gzfile, bzfile (for .bz2): open connection to a .gz/.bz2 file 
* *?connections* for more info.
* <redtext>Remember to close connections </redtext>

## Foreign package  
* Loads data from Minitab, S, SAS, SPSS, Stata,Systat
* Basic functions _read.foo_
  * read.arff (Weka)
  * read.dta (Stata)
  * read.mtp (Minitab)
  * read.octave (Octave)
  * read.spss (SPSS)
  * read.xport (SAS)
* See the help page for more details [http://cran.r-project.org/web/packages/foreign/foreign.pdf](http://cran.r-project.org/web/packages/foreign/foreign.pdf)

## Examples of other database packages

* RPostresSQL provides a DBI-compliant database connection from R. Tutorial-[https://code.google.com/p/rpostgresql/](https://code.google.com/p/rpostgresql/), help file-[http://cran.r-project.org/web/packages/RPostgreSQL/RPostgreSQL.pdf](http://cran.r-project.org/web/packages/RPostgreSQL/RPostgreSQL.pdf)
* RODBC provides interfaces to multiple databases including PostgreQL, MySQL, Microsoft Access and SQLite. Tutorial - [http://cran.r-project.org/web/packages/RODBC/vignettes/RODBC.pdf](http://cran.r-project.org/web/packages/RODBC/vignettes/RODBC.pdf), help file - [http://cran.r-project.org/web/packages/RODBC/RODBC.pdf](http://cran.r-project.org/web/packages/RODBC/RODBC.pdf)
* RMongo [http://cran.r-project.org/web/packages/RMongo/RMongo.pdf](http://cran.r-project.org/web/packages/RMongo/RMongo.pdf) (example of Rmongo [http://www.r-bloggers.com/r-and-mongodb/](http://www.r-bloggers.com/r-and-mongodb/)) and [rmongodb](http://cran.r-project.org/web/packages/rmongodb/rmongodb.pdf) provide interfaces to MongoDb. 


## Reading images

* jpeg - [http://cran.r-project.org/web/packages/jpeg/index.html](http://cran.r-project.org/web/packages/jpeg/index.html)
* readbitmap - [http://cran.r-project.org/web/packages/readbitmap/index.html](http://cran.r-project.org/web/packages/readbitmap/index.html)
* png - [http://cran.r-project.org/web/packages/png/index.html](http://cran.r-project.org/web/packages/png/index.html)
* EBImage (Bioconductor) - [http://www.bioconductor.org/packages/2.13/bioc/html/EBImage.html](http://www.bioconductor.org/packages/2.13/bioc/html/EBImage.html)

## Reading GIS data

* rgdal - [http://cran.r-project.org/web/packages/rgdal/index.html](http://cran.r-project.org/web/packages/rgdal/index.html)
* rgeos - [http://cran.r-project.org/web/packages/rgeos/index.html](http://cran.r-project.org/web/packages/rgeos/index.html)
* raster - [http://cran.r-project.org/web/packages/raster/index.html](http://cran.r-project.org/web/packages/raster/index.html)

## Reading music data

* tuneR - [http://cran.r-project.org/web/packages/tuneR/](http://cran.r-project.org/web/packages/tuneR/)
* seewave - [http://rug.mnhn.fr/seewave/](http://rug.mnhn.fr/seewave/)


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
data <- read.fwf(file = "https://d396qusza40orc.cloudfront.net/getdata%2Fwksst8110.for", skip = 4,
                widths = c(12, 7, 4, 9, 4, 9, 4, 9, 4))
```
? how to change the varaible names
