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
* Important parameters *file, header, sep, row.names, nrows*
* Related: *read.csv(), read.csv2()*

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
* *quote* - you can tell R whether there are any quoted values quote = "" means no quotes
* *na.strings* - set the character that represents a missing value
* *nrows* - how many rows to read of the file
* *skip* - number of lines to skip before starting to read

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
* The *write.xlsx* function will wirte out an Excel file with similar arguments
* *read.xlsx2* is much faster than *read.xlsx* but for reading subsets of rows may be slightly unstable
* The *XLConnect* package has more options for writing and manipulating Excel files
* The *XLConnect vignette* is a good place to start for that package  

In general it is adviced to store your data in either a database or in (.csv) or (.tab/txt) as they are easier to distribute
