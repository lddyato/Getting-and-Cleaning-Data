# Swirl
## Manipulating Data with dplyr Package

`dplyr` is a fast and powerful R package written by Hadley Wickham and Romain Francois.   
The dplyr philosophy is to have small functions that each do one thing well.  

One unique aspect of dplyr is that the same set of tools allow you to work with tabular data from a cariety of sources, including

*data frame*
*data tables*
*databases*
*multidimensional arrays*

**Step 1: download data and load it to R**
```r
if(!file.exists("data")) dir.create("data")
fileUrl <- "http://cran-logs.rstudio.com/2014/2014-07-08.csv.gz"
download.file(fileUrl, "./data/path2csv.csv")
mydf <- read.csv("./data/path2csv.csv", stringsAsFactors = FALSE)
```
**Step 2: summary statistics**
```r
dim(mydf)
head(mydf)
```
**Step 3: library dplyr package**
```r
library(dplyr)
packageVersion("dplyr") # check version: you need to have version 0.4.0 or later
```
From now on, we will focus on how to manipulate data with `dplyr` package.

**Step 4: load data**   
The first step of working with data in dplyr is to load the data into what the package author called a “data frame tbl” or “tbl_df”.
```r
cran <- tbl_df(mydf)
rm("mydf") #To avoid confusion and keep things running smoothly, remove the original data frame from your workspace.
cran
head(mydf)  # take `head` function to avoid too large data set printing 
```
The main advantage to using tbl_df over a regular data frame is the printing.   
The output of tbl_df is much more informative   
and compact than what we would get if we printed the original data frame(mydf)to the console.    
(dplyr shows us the first 10 rows of data and only as many as columns as fit neatly in our console.    
At the bottom, we see the names and classes for any variable for variables that didn’t fit on our screen.)

**Step 5: `dplyr` supplies five manipulation tasks**     
- `select()`
- `filter()`
- `arrange()`
- `mutate()`
- `summarize()`

**Step 6: `select()`**   
Select three variables of cran(we don’t need to use cran$ip_id in dplyr package, so $ can be ignored.)
```r
select(cran, ip_id, package, country) #select only the *ip_id*, *package*, and *country* variables from the cran dataset.
select(cran, r_arch:country) #select all columns starting from r_arch and ending with country.
select(cran, country:r_arch) #select the same columns in reverse order.
select(cran, -time) #throw away one column.(the negative sign in front of time tells us we don’t want the time column.)
select(cran, -(date:size)) #throw away multiple columns.
```

**Step 7: `filter()`**   
Use filter function to select all rows for which the package variable is equal to “swirl”.
```r
filter(cran, package == "swirl") #select all rows for which the package variable is equal to "swirl"
filter(cran, r_version == "3.1.1", country == "US") #filter multiple rows.(note that the conditions are separated by commas.)
filter(cran, country == "IN", r_version <= "3.0.2") #filter rows corresponding to users in “IN” running an R version that is less than or equal to “3.0.2”
filter(cran, country == "US" | country == "IN") #filter rows corresponding to users in “US” **OR** “IN”.
filter(cran, size > 100500, r_os == "linux-gnu") #filter rows for which size is strictly greater than 100500 **AND** r_os equals “linux-gnu”
filter(cran, !is.na(r_version)) #filter the rows for which the r_version is not missing.
```

**Step 8: `arrange()`**   
*Some times we want to reorder the rows of a dataset according to the value of a particular variable.   

```r
cran2 <- select(cran, size:ip_id)
arrange(cran2, ip_id) # order the ROWS of cran2 so that ip_id is in ascending order
arrange(cran2, desc(ip_id)) # order the ROWS of cran2 so that ip_id is in descending order
arrange(cran2, package, ip_id) #arrange by package names (ascending alphabetically), then by ip_id(ascending numerically).
arrange(cran2, country, desc(r_version), ip_id) #Arrange cran2 by country(ascending), r_version (descending), and ip_id (ascending).
```
**Step 9: `mutate()`**
```r
cran3 <- select(cran, ip_id, package, size)
cran3
mutate(cran3, size_mb = size / 2^20) #add a column called size_mb that contains the download size in megabytes.  
# One megabyte is equal to 2^20 bytes, a gigabyte (GB) is equal to 2^10 megabytes.
# One very nice feature of mutate() is that you can use the value computed for your second column(size_mb) to create a third column(size_rb).
mutate(cran3, size_mb = size/2^20, size_gb = size_mb/2^10)
mutate(cran3, correct_size = size + 1000) #add a new variable “correct_size = size+1000”
```

**Step 10: `summarize()`**    
summarize() collapses the dataset to a single row.   
summarize() is most useful when working with data that has been grouped by the values of a particular variable.

```r
summarize(cran, ave_bytes = mean(size)) #Calculate the average download size.
by_package <- group_by(cran, package) #Group cran by the package variable
#Everything else looks the same, but now any operation we apply to the grouped data will take place on a per package basis.
summarize(by_package, mean(size)) # returns the mean size for EACH package in our dataset.
pack_sum <- summarize(by_package, 
                      count = n(), # contains the total number of rows (i.e. downloads) for each package
                      unique = n_distinct(ip_id),  #gives the total number of unique downloads for each package as measured by the number of distinct ip_id's
                      countries = n_distinct(country), #provides the number of countries in which each package was downloaded
                      avg_bytes = mean(size)) #contains the mean download size (in bytes) for each package
quantile(pack_sum$count, probs = 0.99) #99%  679.56
#know the value of 'count' that splits the data into the top 1% and bottom 99% of packages based on total downloads
top_counts <- filter(pack_sum, count > 679) #select all rows from pack_sum for which 'count' is strictly greater (>) than 679
View(top_counts)
top_counts_sorted <- arrange(top_counts, desc(count)) #arrange() the rows of top_counts based on the 'count' column
View(top_counts_sorted)

#Perhaps we're more interested in the number of *unique* downloads on this particular day
quantile(pack_sum$unique, probs = 0.99) #99%   465
top_unique <- filter(pack_sum, unique > 465)
View(top_unique)
top_unique_sorted <- arrange(top_unique, desc(unique))
View(top_unique_sorted)
```
## Grouping and Chaining with `dplyr`  

**summarize2.R**   
```r
by_package <- group_by(cran, package)
pack_sum <- summarize(by_package,
                      count = n(),
                      unique = n_distinct(ip_id),
                      countries = n_distinct(country),
                      avg_bytes = mean(size))
top_countries <- filter(pack_sum, countries > 60)
result1 <- arrange(top_countries, desc(countries), avg_bytes) #sorted primarily by country, but used avg_bytes (in ascending order) as a tie breaker
print(result1)
```
**summarize3.R**   
The result is equivalent to summarize2.R, but the code is much less readable and some of the arguments are   
far away from the function to which they belong. This requires embedding function calls within one another.

```r
result2 <-
  arrange(
    filter(
      summarize(
        group_by(cran,
                 package
        ),
        count = n(),
        unique = n_distinct(ip_id),
        countries = n_distinct(country),
        avg_bytes = mean(size)
      ),
      countries > 60
    ),
    desc(countries),
    avg_bytes
  )
print(result2)
```
**summarize4.R**   
The benefit of %>% is that it allows us to chain the function calls in a linear fashion.   
The code to the right of %>% operates on the result from the code to the left of %>%.

```r
result3 <-
  cran %>%
  group_by(package) %>%
  summarize(count = n(),
            unique = n_distinct(ip_id),
            countries = n_distinct(country),
            avg_bytes = mean(size)
  ) %>%
  filter(countries > 60) %>%
  arrange(desc(countries), avg_bytes)
print(result3)
```

**Chain1~4.R**   
Keep in mind that when you're using the chaining operator,   
you don't need to specify the name of the data tbl in your call to select().  
```r
cran %>% select(ip_id, country, package, size) %>% print
cran %>% select(ip_id, country, package, size) %>% mutate(size_mb = size / 2^20) %>% print
cran %>% select(ip_id, country, package, size) %>% mutate(size_mb = size / 2^20) %>% filter(size_mb <= 0.5) %>% print
cran %>% select(ip_id, country, package, size) %>% mutate(size_mb = size / 2^20) %>% filter(size_mb <= 0.5) %>% arrange(desc(size_mb)) %>% print
```
##Tidying Data with tidyr
Tidy data is formatted in a standard way that facilitates exploration and analysis and works seamlessly with other tidy data tools.   
Specifically, tidy data satisfies three conditions:

* Each variable forms a column.
* Each observation forms a row.
* Each type of observation unit forms a table.

*five types of messy data are:*

* Column headers are values not variable names.
* Multiple vatiables are stored in one column.
* Variables are stored in both rows and columns.
* Multiple types of observational units are stored in the same table.
* A single observational unit is stored in multiple tables.

**Type one: column headers are values not variable names**

**Step 1:**   
Create a data frame “students” that satisfies the type one condition.  
The students dataset actually has three variables: grade, sex and count.
```r
students <- data.frame(grade = toupper(letters[1:5]), male = c(1, 5, 5, 5, 7), female = c(5, 0, 2, 5, 4))
```

*Step 2:**    
To tidy the students data, we need to have one column for rach of these three variables.  
`gather` in tidyr package takes multiple columns and collapses into key-value pairs, duplicating all other columns as needed.   
You use gather() when you notice that you have columns that are not variables.
Note that the minus sign before grade, which says we want to gather all columns except grade.
```r
library(tidyr)
gather(students, sex, count, -grade)
```
**Type two: multiple variables are stored in one columnm.**

**Step 1:**   
Create data set “student2” to satisfy the type two condition.(1 and 2 means class)
```r
students2 <- data.frame(grade = LETTERS[1:5], male_1 = c(3, 6, 7, 4, 1), female_1 = c(4, 4, 4, 0, 1), male_2 = c(3, 3, 3, 8, 2), female_2 = c(4, 5, 8, 1, 7))
```
**Step 2:**  
Using gather to stack columns of students2, like we just did with students.

```r
res <- gather(students2, sex_class, count, - grade)
```
**Step 3:**   
separate function is for the purpose of separating one column into multiple columns.
```r
separate(data = res, col = sex_class, into = c("sex", "class"))
```
**Step 4:** Using chain %>% function to do step 2-3.
```r
library(dplyr)
students2 %>%
        gather(sex_class, count, - grade) %>%
        separate(col = sex_class, into = c("sex", "class")) %>%
        print
```

**Type three: Variables are stored in both rows and columns.**

**Step 1:**   
Create dataset ‘students3’ satisfying type three condition.
```r
students3 <- data.frame(name = rep(c("Sally", "Jeff", "Roger", "Karen", "Brian"), each = 2), 
                        test = rep(c("midterm", "final"), 5), 
                        class1 = c("A", "C", rep(NA, 6), "B", "B"), 
                        class2 = c(rep(NA, 2), "D", "E", "C", "A", rep(NA, 4)), 
                        class3 = c("B", "C", rep(NA, 4), rep("C", 2), rep(NA, 2)), 
                        class4 = c(rep(NA, 2), "A", "C", rep(c(NA, "A", NA), each = 2)), 
                        class5 = c(rep(NA, 4), "B", "A", rep(NA, 2), "A", "C"))
# set attributes to the five column same
attributes(students3$class1) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class2) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class3) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class4) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class5) <- list(levels = LETTERS[1:5], class = "factor")
```
**Step 2:**   
gether() function should be used to gether class.
```r
students3 %>%
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        print
```
**Step 3:**   
spread() function should be used to spread test column. (Spread a key-value pair across multiple columns)
```r
students3 %>%
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        spread(test, grade) %>%
        print
```
**Step 4:**   
Set the class to be 1:5.
```r
students3 %>% 
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        spread(test, grade) %>% 
        mutate(class = extract_numeric(class)) %>%
        print
```
**Type four: multiple types of observational units are stored in the same table.**

**Step 1:**    
Create a dataset satisfying type four condition.
```r
students4 <- data.frame(id = rep(c(168, 588, 710, 731, 908), each = 2), 
name = rep(c("Brian", "Sally", "Jeff", "Roger", "Karen"), each = 2), 
sex = c(rep(c("F", "M"), each = 2), rep(c("M", "F", "M"), each = 2)), 
class = c(1, 5, 1, 3, 2, 4, 2, 5, 3, 4), 
midterm = c("B", "A", "A", "B", "D", "A", "C", "B", "C", "A"), 
final = c("B", "C", "C", "C", "E", "C", "A", "A", "C", "A"))
```
**Step 2:**    
At first glance, there doesn’t seem to be much of a problem with students4. All columns are variables and all rows are observations. However, notice that each id, name, and sex is repeated twice, which seems quite redundant. This is a hint that our data contains multiple observational units in a single table.Our solution will be to break students4 into two separate tables – one containing basic student information (id, name, and sex) and the other containing grades (id, class, midterm, final).
```r
student_info <- students4 %>%
        select(id, name, sex) %>%
        unique %>%
        print
gradebook <- students4 %>%
        select(id, class, midterm, final) %>%
        print
```
**Type five: a single observation unit is stored in multiple tables.**

**Step 1:** Create dataset satisfying type five condition.
```r
# passed
passed <- data.frame(name = c("Brian", "Roger", "Roger", "Karen"), class = c(1, 2, 5, 4), final = c("B", "A", "A", "A"))
attributes(passed$final) <- list(levels = LETTERS[1:5], class = "factor")
passed$name <- as.character(passed$name)
# failed
failed <- data.frame(name = as.character(c("Brian", "Sally", "Sally", "Jeff", "Jeff", "Karen")), class = c(5, 1, 3, 2, 4, 3), final = c("C", "C", "C", "E", "C", "C"))
attributes(failed$final) <- list(levels = LETTERS[1:5], class = "factor")
failed$name <- as.character(failed$name)
```
**Step 2:** using mutate() to add a column to passed dataset.
```r
passed <- mutate(passed, status = "passed")
failed <- mutate(failed, status = "failed")
```
**Step 3:** bind passed and failed datasets.
```r
bind_rows(passed, failed)
```

