# Dates
#Working with dates
**Step 1:**  
Starting simple. `date()` returns a character that gives you the date and time. 
```r
d1 <- date()
d1
class(d1)1
```
**Step 2: Data class**
```r
d2 <- Sys.Date()
d2
class(d2)1
```
**Step 3: Formatting dates.** 
%d = days as number(0-31).
%a = abbreviated weekday.
%A = unabbreviated weekday.
%m = month(00-12).
%b = abbreviated month.
%B = unabbreviated month.
%y = 2 digit year.
%Y = 4 digit year.

```r
format(d2, "%a %b %d")
```
**Step 4: Creating dates.**
```r
# if returns NA, please use
lct <- Sys.getlocale("LC_TIME")
Sys.setlocale("LC_TIME", "C")
x <- c("1jan1960", "2jan1960", "31mar1960", "30Jul1960")
z <- as.Date(x, "%d%b%Y")
z
z[1] - z[2]
as.numeric(z[1] - z[2])1
```
**Step 5: Converting to Julian.**
```r
weekdays(d2)
months(d2)
julian(d2)1
```
**Step 6: lubridate package.**
```r
library(lubridate)
ymd("20140108")
mdy("08/04/2013")
dmy("03-04-2013")1
```
**Step 7: Dealing with time.**
```r
ymd_hms("2011-08-03 10:15:03")
ymd_hms("2011-08-03 10:15:03", tz = "Pacific/Auckland")
```
**Step 8: Some functions have slightly different syntax.**
```r
x <- dmy(c("1jan2013", "2jan2013", "31mar2013", "30Jul2013"))
wday(x[1])
wday(x[1], label = TRUE)
ymd("1989 May 17")
mdy("March 12 1975")
dmy(25081985)
ymd("1920/1/2")
ymd_hms(now())
hms("03:22:14")1
```
**Step 9: Dealing with vector of dates.**
```r
dt2 <- c("2014-05-14", "2014-09-22", "2014-07-11")
ymd(dt2)
```
