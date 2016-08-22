
#Editing text variables

Important points about text in data set
**Names of variables should be **
* All lower cases when possible
* Descriptive (Diagnosis versus Dx)
* Not duplicated
* Not have underscores or dots or white spaces


**Variables with character values**
* Should usually be made into factor variables(depend on application)
* Should be descriptive(use TRUE/FALSE instead of 0/1 and Male/Femal versus 0/2 or M/F)


**Step 1:**   
Fixing charactre vectors topupper and tolower functions.

```r
if(!file.exists("./data")) dir.create("./data")
fileUrl <- "https://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileUrl, destfile = "./data/cameras.csv")
cameraData <- read.csv("./data/cameras.csv")
names(cameraData)
tolower(names(cameraData))
toupper(names(cameraData))
```
**Step 2:**   
Fixing character vectors strsplit function. 
* Good for automatically splitting variable names.
* Important paramters:*x and split*

```r
splitNames <- strsplit(names(cameraData), "\\.") #use this escape character because the period is a reserved character
splitNames[[5]]
splitNames[[6]]
```
**Step 3:**   
Quick aside lists
```r
myList <- list(letters = c("A", "b", "c"), numbers = 1:3, matrix(1:15, 5))
head(myList)1
```
**Step 4:**   
Fixing character vectors sapply 
* Applies a function to each element in a vector or list.
* Implortant parameted: x Fun

```r
splitNames[[6]][1]
firstElement <- function(x) x[1]
sapply(splitNames, firstElement)1
```
**Step 5:**    
Peer review data
```r
if(!file.exists("./data")) dir.create("./data")
# download data set
fileUrl1 <- "https://dl.dropbox.com/u/7710864/data/reviews-apr29.csv"
fileUrl2 <- "https://dl.dropbox.com/u/7710864/data/solutions-apr29.csv"
download.file(fileUrl1, destfile = "./data/reviews.csv")
download.file(fileUrl2, destfile = "./data/solution.csv")
# load data set
reviews <- read.csv("./data/reviews.csv")
solutions <- read.csv("./data/solution.csv")
# view data set
head(reviews, 2)
head(solutions, 2)
```
**Step 6:**  

Fixing character vectors sub()(replace the first match)
```r
names(reviews)
sub("_", "", names(reviews))1
```
**Step 7:**  
Fixing character vectors gsub() (replace globally)
```r
testName <- "this_is_a_test"
sub("_", "", testName)
gsub("_", "", testName)
```
**Step 8:**  
Find values grep() and grepl() functions
```r
grep("Alameda", cameraData$intersection) # return index
table(grepl("Alameda", cameraData$intersection)) # return true or false
cameraData2 <- cameraData[!grepl("Alameda", cameraData$intersection), ]
```
**Step 9:**   
More on grep()
```r
grep("Alameda", cameraData$intersection, value = TRUE) # retrun names containing "Aladema"
grep("JeffStreet", cameraData$intersection)
length(grep("JeffStreet", cameraData$intersection))
```
**Step 10:**  
More useful string functions
```r
library(stringr)
nchar("Jeffrey Leek")
substr("jeffrey Leek", 1, 7)
paste("Jeffrey", "Leek")
paste0("Jeffrey", "Leek")
str_trim("Jeff    ")
```
#Regular expressions

A ‘regular expression’ is a pattern that describes a set of strings.   
Two types of regular expressions are used in R, extended regular expressions (the default) and   
Perl-like regular expressions used by perl = TRUE.    
There is a also `fixed = TRUE` which can be considered to use a literal regular expression. 
Here we cansider the extended regular expressions used in `grep, grepl, regexpr, gregexpr, sub, gsub and strsplit`.

Most characters, including all letters and digits, are regular expressions that match themselves.    
Any metacharacter with special meaning may be quoted by preceding it with a backslash.   
The metacharacters in extended regular expressions are` . \ | ( ) [ { ^ $ * + ?`,    
but note that whether these have a special meaning depends on the context.


**Positions**   
1: `^` matches the begining.      
2: `$` matches the end.         
3: `\b` matches the empty string at either edge of a word.            
4: `\B` matches the empty string provided it is not at an edge of a word.           


**Quantifiers**    
1: `*` matches at least 0 times.        
2: `+` matches at least 1 times.           
3: `?` matches at most 1 times.       
4: `{m}` matches exactly m times.          
5: `{m.}` matches at least m times.          
6: `{n, m}` matches between n to m times.            


**Others**     
1: `[ ]` matches any character appearing in []. ex: [a-z]       
2: `[^ ]` matches any character not appearing in [ ].      
3: `.` matches any character.         
4: `|` matches alternative metacharacters.           
5: `\` suppress the special meaning of metacharacters in regular expression.       
6: `()` groups expression.           


**Character classes**    

1: `[:digit:]` or \d equivalent to [0-9].  
2: `[:lower:]` equivalent to [a-z].  
3: `[:upper:]` equivalent to [A-Z].  
4: `[:alpha:]` equivalent to [a-zA-Z] or [[:lower:][:upper:]].  
5: `[:alnum:]` equivalent to [A-z0-9] or [[:digit:][:alpha:]].  
6: `\w` equivalent to [[:apnum]_] or [A-z0-9_].  
7: `\W` equivalent [^A-z0-9].  
8: `[:xdigit:]` matches 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f.  
9: `[:blank:]` matches space or tab.  
10: `[:space:]` marches tab, newline, vertical tab, form feed, carriage return, space.   
11: `\s` space ” “.     
12: `\S` not space.   
13: `[:punct]` matches ! " # $ % & ’ ( ) * + , - . / : ; < = > ? @ [ ] ^ _ ` { | } ~.   
14: `[:graph:]` equivalent to [[:alnum:][:punct:]].   
15: `[:print:]` equivalent to [[:alnum:][:punct:]\\s].   
16: `[:cntrl:]` control characters, like \n or \r, [\x00-\x1F\x7F].    


**R function summary:**          
1: Identify match to a pattern:     
`grep(..., value = FALSE), grepl(), stringr::str_detect()`.   
2: Extract match to a pattern:      
`grep(..., value = TRUE), stringr::str_extract(), stringr::str_extract_all()`.   
3: Locate pattern within a string, i.e. give the start position of matched patterns.       
`regexpr(), gregexpr(), stringr::str_locate(), string::str_locate_all()`.        
4: Replace a pattern:     
`sub(), gsub(), stringr::str_replace(), stringr::str_replace_all()`.         
5: Split a string using a pattern:     
`strsplit(), stringr::str_split()`.       

