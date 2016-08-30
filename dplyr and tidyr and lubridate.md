#Manipulating Data with dplyr Package

**`dplyr`** is a fast and powerful R package written by Hadley Wickham and Romain Francois. 
The dplyr philosophy is to have small functions that each do one thing well.

One unique aspect of dplyr is that the same set of tools allow you to work with tabular data from a cariety of sources, including

* data frame
* data tables
* databases
* multidimensional arrays

##Step 1
Download data and load it to R.
```r
if(!file.exists("data")) dir.create("data")
fileUrl <- "http://cran-logs.rstudio.com/2014/2014-07-08.csv.gz"
download.file(fileUrl, "./data/path2csv.csv")
mydf <- read.csv("./data/path2csv.csv", stringsAsFactors = FALSE)
```
##Step 2
summary statistics.
```r
dim(mydf)
head(mydf)
```
##Step 3
library dplyr package.
```r
library(dplyr)
# check version: you need to have version 0.4.0 or later
packageVersion("dplyr")
```

From now on, we will focus on how to manipulate data with dplyr package.

##Step 4
The first step of working with data in dplyr is to load the data into what the package author called a “data frame tbl” or “tbl_df”.
```r
cran <- tbl_df(mydf)
```
##Step 5
The main advantage to using tbl_df over a regular data frame is the printing. The output of tbl_df is much more informative and compact than what we would get if we printed the original data frame(mydf) to the console.(dplyr shows us the first 10 rows of data and only as many as columns as fit neatly in our console. At the bottom, we see the names and classes for any variable for variables that didn’t fit on our screen.)
```r
cran
head(mydf)  # take `head` function to avoid too large data set printing 
```
Then we will focus the five manipulation tasks:

- `select()`: return a subset of the columns of a data frame, using a flexible notation
- `filter()`: extract a subset of rows from a data frame based on logical conditions
- `arrange()`: reorder rows of a data frame
- `rename`: rename variables in a data frame
- `mutate()`: add new variables/columns or transform existing variables
- `summarize()/summarise()`: generate summary statistics of different variables in the data frame, possibly within strata
- `%>%`: the “pipe” operator is used to connect multiple verb actions together into a pipeline

##Step 6 `select()`
select three variables of cran(we don’t need to use cran$ip_id in dplyr package, so $ can be ignored.)

```r
select(cran, ip_id, package, country)
```
##Step 7
select a sequence of columns.()
```r
select(cran, r_arch:country)
```
##Step 8
throw away one column.(the negative sign in front of time tells us we don’t want the time column.)
```r
select(cran, -time)
```
##Step 9
throw away multiple columns.
```r
select(cran, -(date:size))
```
##Step 10 `filter()`
use filter function to select all rows for which the package variable is equal to “swirl”.
```r
filter(cran, package == "swirl")
```
##Step 11
filter multiple rows.(note that the conditions are separated by commas.)
```r
filter(cran, r_version == "3.1.1", country == "US")
```
##Step 12
filter rows corresponding to users in “IN” running an R version that is less than or equal to “3.0.2”
```r
filter(cran, country == "IN", r_version <= "3.0.2")
```
##Step 13
filter rows corresponding to users in “US” or “IN”.
```r
filter(cran, country == "US" | country == "IN")
```
##Step 14
filter rows for which size is strictly greater than 100500 and r_os equals “linux-gnu”
```r
filter(cran, size > 100500, r_os == "linux-gnu")
```
##Step 15
filter the rows for which the r_version is not missing.
```r
filter(cran, !is.na(r_version))
```
##Step 16 `arrange()`
Some times we want to reorder the rows of a dataset according to the value of a particular variable. Reorder cran2 such that ip_id is in ascending.
```r
cran2 <- select(cran, size:ip_id)
arrange(cran2, ip_id)
```
##Step 17
reorder crans such that ip_id is in descending order.
```r
arrange(cran2, desc(ip_id))
```
##Step 18
reorder cran2 using multiple variables.
```r
arrange(cran2, package, ip_id)
```
##Step 19
reorder cran2 using this order: country(ascending), r_version(descending), ip_id(ascending).
```r
arrange(cran2, country, desc(r_version), ip_id)
```
##Step 20 `mutate()`
add a column called size_mb that contains the download size in megabytes.
```r
cran3 <- select(cran, ip_id, package, size)
cran3
mutate(cran3, size_mb = size / 2^20)
```
##Step 21
One very nice feature of mutate() is that you can use the value computed for your second column(size_mb) to create a third column(size_rb).
```r
mutate(cran3, size_mb = size/2^20, size_gb = size_mb/2^10)
```
##Step 22
add a new variable “correct_size = size+1000”
```r
mutate(cran3, correct_size = size + 1000)
```
##Step 23
summarize() collapses the dataset to a single row. Calculate the average download size.
```r
summarize(cran, ave_bytes = mean(size))
```

#Grouping and Chaining with dplyr package

The main idea behind grouping data is that you want to break up your dataset into groups of rows based on the values of one or more variables. THe group_by() function is reponsible for doing this.

##Step 1
group cran by the package variable and store the result in a new variable called by_package. (At the top of the output above, you’ll see ‘Groups:package’. Eveything looks the same, but now any operation we apply to the grouped data will tabke place on a per package basis.)
```r
by_package <- group_by(cran, package)
by_package
```
##Step 2
summarize the mean(size) to by_package
```r
summarize(by_package, mean(size))
```
##Step 3
Summarize more on by_package
```r
# Compute four values, in the following order, from
# the grouped data:
#
# 1. count = n()
# 2. unique = n_distinct(ip_id)
# 3. countries = n_distinct(country)
# 4. avg_bytes = mean(size)
#
# A few thing to be careful of:
#
# 1. Separate arguments by commas
# 2. Make sure you have a closing parenthesis
# 3. Check your spelling!
# 4. Store the result in pack_sum (for 'package summary')
#
# You should also take a look at ?n and ?n_distinct, so
# that you really understand what is going on.

# n() contains the total number of rows. n_distinct() gives the total number of unique downloads for each package.

pack_sum <- summarize(by_package, count = n(),  unique = n_distinct(ip_id), countries = n_distinct(country), avg_bytes = mean(size))
```
##Step 4
determine the 0.99 quantile.
```r
quantile(pack_sum$count, probs = 0.99)
```
##Step 5
filter rows with count larger than 0.99 quantile.
```r
top_counts <- filter(pack_sum, count > 679)
top_counts
```
##Step 6
view all rows sinze dplyr only shows us the first 10 rows.
```r
View(top_counts)
```r
##Step 7
arrange the rows of top_counts based on the “count” with descending order and assign the result to top_count_sorted.
```r
top_counts_sorted <- arrange(top_counts, desc(count))
```r
##Step 8
View top_counts_sorted
```r
View(top_counts_sorted)
```r
##Step 9
find 0.99 quantile for the “unique” vairable
```r
quantile(pack_sum$unique, probs = 0.99)
```
##Step 10
filter rows with unique > 465
```r
top_unique <- filter(pack_sum, unique > 465)
```
##Step 11
View the result
```r
View(top_unique)
```r
##Step 12
arrange top_unique by unque in descending order.
```r
top_unique_sorted <- arrange(top_unique, desc(unique))
```
##Step 13
View the sorted data
```r
View(top_unique_sorted)
```
##Step 14 chaining
Chaining allows you to string together multiple function calls in a way that is compact and readable, while still accomplishing the desired result. To make it more 
concrete, let’s compute our last popularity metric from scratch, starting with our original data.
```r
# Don't change any of the code below. Just type submit()
# when you think you understand it.

# We've already done this part, but we're repeating it
# here for clarity.

by_package <- group_by(cran, package)
pack_sum <- summarize(by_package,
                      count = n(),
                      unique = n_distinct(ip_id),
                      countries = n_distinct(country),
                      avg_bytes = mean(size))

# Here's the new bit, but using the same approach we've
# been using this whole time.

top_countries <- filter(pack_sum, countries > 60)
result1 <- arrange(top_countries, desc(countries), avg_bytes)

# Print the results to the console.
print(result1)
```
equal to
```r
# Don't change any of the code below. Just type submit()
# when you think you understand it. If you find it
# confusing, you're absolutely right!

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
equal to
```r
# Read the code below, but don't change anything. As
# you read it, you can pronounce the %>% operator as
# the word 'then'.
#
# Type submit() when you think you understand
# everything here.

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

# Print result to console
print(result3)
```

#Tidying Data with tidyr package

Tidy data is formatted in a standard way that facilitates exploration and analysis and works seamlessly with other tidy data tools.    

Specifically, tidy data satisfies three conditions:   
* Each variable forms a column.
* Each observation forms a row.
* Each type of observation unit forms a table.

five types of messy data are:
* Column headers are values not variable names.
* Multiple vatiables are stored in one column.
* Variables are stored in both rows and columns.
* Multiple types of observational units are stored in the same table.
* A single observational unit is stored in multiple tables.

##Type one: column headers are values not variable names

###Step 1
Create a data frame “students” that satisfies the type one condition. The students dataset actually has three variables: grade, sex and count.
```r
students <- data.frame(grade = toupper(letters[1:5]), male = c(1, 5, 5, 5, 7), female = c(5, 0, 2, 5, 4))
```
###Step 2
To tidy the students data, we need to have one column for rach of these three variables.(gather in tidyr package takes multiple columns and collapses into key-value pairs, duplicating all other columns as needed. You use gather() when you notice that you have columns that are not variables.)(Note that the minus sign before grade, which says we want to gather all columns except grade.)
```r
library(tidyr)
gather(students, sex, count, -grade)
```
##Type two: multiple variables are stored in one columnm.

###Step 1
Create data set “student2” to satisfy the type two condition.(1 and 2 means class)
```r
students2 <- data.frame(grade = LETTERS[1:5], male_1 = c(3, 6, 7, 4, 1), female_1 = c(4, 4, 4, 0, 1), male_2 = c(3, 3, 3, 8, 2), female_2 = c(4, 5, 8, 1, 7))
```
###Step 2
Using gather to stack columns of students2, like we just did with students.
```r
res <- gather(students2, sex_class, count, - grade)
```
###Step 3
separate function is for the purpose of separating one column into multiple columns.
```r
separate(data = res, col = sex_class, into = c("sex", "class"))
```
###Step 4
Using chain %>% function to do step 2-3.
```r
library(dplyr)
students2 %>%
        gather(sex_class, count, - grade) %>%
        separate(col = sex_class, into = c("sex", "class")) %>%
        print
```
##Type three: Variables are stored in both rows and columns.

###Step 1
Create dataset ‘students3’ satisfying type three condition.
```r
students3 <- data.frame(name = rep(c("Sally", "Jeff", "Roger", "Karen", "Brian"), each = 2), test = rep(c("midterm", "final"), 5), class1 = c("A", "C", rep(NA, 6), "B", "B"), class2 = c(rep(NA, 2), "D", "E", "C", "A", rep(NA, 4)), class3 = c("B", "C", rep(NA, 4), rep("C", 2), rep(NA, 2)), class4 = c(rep(NA, 2), "A", "C", rep(c(NA, "A", NA), each = 2)), class5 = c(rep(NA, 4), "B", "A", rep(NA, 2), "A", "C"))
# set attributes to the five column same
attributes(students3$class1) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class2) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class3) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class4) <- list(levels = LETTERS[1:5], class = "factor")
attributes(students3$class5) <- list(levels = LETTERS[1:5], class = "factor")
```
###Step 2
gether() function should be used to gether class.
```r
students3 %>%
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        print
```
###Step 3
spread() function should be used to spread test column. (Spread a key-value pair across multiple columns)
```r
students3 %>%
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        spread(test, grade) %>%
        print
```
###Step 4
Set the class to be 1:5.
```r
students3 %>% 
        gather(class, grade, class1:class5, na.rm = TRUE) %>%
        spread(test, grade) %>% 
        mutate(class = extract_numeric(class)) %>%
        print
```

##Type four: multiple types of observational units are stored in the same table.

###Step 1
Create a dataset satisfying type four condition.
```r
students4 <- data.frame(id = rep(c(168, 588, 710, 731, 908), each = 2), name = rep(c("Brian", "Sally", "Jeff", "Roger", "Karen"), each = 2), sex = c(rep(c("F", "M"), each = 2), rep(c("M", "F", "M"), each = 2)), class = c(1, 5, 1, 3, 2, 4, 2, 5, 3, 4), midterm = c("B", "A", "A", "B", "D", "A", "C", "B", "C", "A"), final = c("B", "C", "C", "C", "E", "C", "A", "A", "C", "A"))
```
###Step 2
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
##Type five: a single observation unit is stored in multiple tables.

###Step 1
Create dataset satisfying type five condition.
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
###Step 2
using mutate() to add a column to passed dataset.
```r
passed <- mutate(passed, status = "passed")
failed <- mutate(failed, status = "failed")
```

###Step 3
bind passed and failed datasets.
```r
bind_rows(passed, failed)
```

##Brings five type together to deal with real data.

###Step 1
Create data set “sat”.
```r
sat <- data.frame(score_range = c("700-800", "600-690", "500-590", "400-490", "300-390", "200-290"), 
                  read_male = c(40151, 121950, 227141, 241554, 113568, 30728),
                  read_fem = c(38898, 126084, 259553, 296793, 133473, 29154),
                  read_total = c(79049, 24803, 486694, 539347, 247041, 59882),
                  math_male = c(74461, 162564, 233141, 204670, 82468, 18788),
                  math_fem = c(46040, 133954, 257678, 288696, 131025, 16562),
                  math_toal = c(120501, 196518, 490819, 493366, 213493, 45350),
                  write_male = c(31574, 100963, 202326, 262623, 146106, 32500),
                  write_fem = c(39101, 125368, 247239, 302933, 144381, 24933),
                  write_total = c(60675, 226331, 449565, 565556, 2904787, 57433))
sat <- tbl_df(sat)
```
###Step 2: select(), gather(), separate()
```r
# Accomplish the following three goals:
#
# 1. select() all columns that do NOT contain the word "total",
# since if we have the male and female data, we can always
# recreate the total count in a separate column, if we want it.
# Hint: Use the contains() function, which you'll
# find detailed in 'Special functions' section of ?select.
#
# 2. gather() all columns EXCEPT score_range, using
# key = part_sex and value = count.
#
# 3. separate() part_sex into two separate variables (columns),
# called "part" and "sex", respectively. You may need to check
# the 'Examples' section of ?separate to remember how the 'into'
# argument should be phrased.
#
sat %>% 
        select(-contains("total")) %>%
        gather(part_sex, count, -score_range) %>%
        separate(col = part_sex, into = c("part", "sex")) %>%
        print
```
###Step 3
group_by() function to group the data by part and sex. And mutate() function to add two new columns, whose values automativally computed group-by-group.
```r
# Append two more function calls to accomplish the following:
#
# 1. Use group_by() (from dplyr) to group the data by part and
# sex, in that order.
#
# 2. Use mutate to add two new columns, whose values will be
# automatically computed group-by-group:
#
#   * total = sum(count)
#   * prop = count / total
#
sat %>%
        select(-contains("total")) %>%
        gather(part_sex, count, -score_range) %>%
        separate(part_sex, c("part", "sex")) %>%
        group_by(part, sex)  %>%
        mutate(total = sum(count),
               prop = count/ total) %>% 
        print
```

#Dates and Times with lubridate

##step 1: load package and deal with date.
```r
lct <- Sys.getlocale("LC_TIME")
Sys.setlocale("LC_TIME", "C")
library(lubridate)
this_day <- today()
this_day
year(this_day)
month(this_day)
day(this_day)
wday(this_day)
wday(this_day, label = TRUE)
```
##Step 2: deal with date and time combinations.
```r
this_moment <- now()
this_moment
hour(this_moment)
minute(this_moment)
second(this_moment)
```r
##Step 3: ymd() dmy() hms() ymd_hms(), etc.
```r
my_date <- ymd("1989-05-17")
my_date
class(my_date)
update(this_moment, hours = 8, minutes = 34, seconds = 55)
this_moment
this_moment <- update(this_moment, hours = 15, minutes = 54)
this_moment
```

##Step 4: Different time zone.
```r
nyc <- now("America/New_York")
depart <- nyc + days(2)
depart
depart <- update(depart, hours = 17, minutes = 34)
arrive <- depart + hours(15) + minutes(50)
arrive <- with_tz(arrive, "Asia/Hong_Kong")
```
##Step 5: Time difference.
```r
last_time <- mdy("June 17, 2008", tz = "Singapore") 
how_long <- interval(last_time, arrive)
how_long
as.period(how_long)
```
