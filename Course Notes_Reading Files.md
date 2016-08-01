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
download.file(fileurl, destfile="./data/cameras.csv", method="curl") # method="curl" is for mac
list.files("./data")
dateDownloaded <- date() # record the download time
```
