---
title: "Session 2 - Data frames"
author: "Jongbin Jung"
date: "January 9-10, 2016"
output: 
  beamer_presentation: 
    fig_height: 3
    fig_width: 3
    fonttheme: professionalfonts
    highlight: zenburn
    theme: boxes
---

## Dependencies
- Latest version ($\ge$ `3.1.2`) of `R`  
  (*free* from <https://www.r-project.org/>)
- Latest version of `Rstudio` 
  (also *free* from <https://www.rstudio.com/>)
- A bunch of *free* packages


```r
install.packages('dplyr')
install.packages('tidyr')
install.packages('nycflights13')  # sample data frame
```


## Data Frames: Introduction
- Data frames are the primary representation of data in `R`
- You can think of a data frame as a two-dimensional *table* of data
- It helps your sanity to always think of data frames as a table where
```
Each column represents a variable/feature
Each row represents an observation/instance
```

- Conceptually, a data frame is also a collection of vectors, i.e., 
each column is a vector that belongs to the (parent) data frame
- The fastest path to achieving `R`-ninja status is to get familiar with 
data frames

## Data Frames: First Impression
- Let's load an existing data frame to take a look at

```r
# install data package (only need to do once)
install.packages('nycflights13')
```


```r
# load data package to workspace
library('nycflights13')
```
- The `nycflights13` package contains a single data frame named `flights`
- Contains data (16 variables) on all 336,776 flights that departed NYC 
(i.e. JFK, LGA, or EWR) in 2013
- See documentation for details on what the 16 variables are

```r
?flights
```

## Data Frames: First Impression (cont'd)


```r
head(flights)  # take a peek at the data frame
```

```
## Source: local data frame [6 x 16]
## 
##    year month   day dep_time dep_delay arr_time
##   (int) (int) (int)    (int)     (dbl)    (int)
## 1  2013     1     1      517         2      830
## 2  2013     1     1      533         4      850
## 3  2013     1     1      542         2      923
## 4  2013     1     1      544        -1     1004
## 5  2013     1     1      554        -6      812
## 6  2013     1     1      554        -4      740
## Variables not shown: arr_delay (dbl), carrier
##   (chr), tailnum (chr), flight (int), origin
##   (chr), dest (chr), air_time (dbl), distance
##   (dbl), hour (dbl), minute (dbl)
```

## Some Question
- What questions could you ask (and answer) with this data?
    - how many flights were there each day?
    - what was the mean departure delay for flights every month/day?
    - what is the proportion of annual departures from each of the three airports?
    - what else?

- By the end of this session, we'll have the tools to answer most (if not all) 
of the questions you can come up with!

# Data Frame Basics

## Simple Example
- Use `data.frame()` function to create a data frame
- Arguments of `data.frame()` are vectors (of equal length) that constitute each 
column (variable)
- For example, let's create a data frame of the following table:

Age | Personality | Income 
--- | ----------- | ------
24  | Good        | 2000
22  | Bad         | 5800
23  | Good        | 4200
25  | Bad         | 1500
22  | Good        | 6000

## Simple Example (cont'd)
- We'll save the data frame to an object (I'll call mine `data`)

```r
data <- data.frame(  # start the data.frame() 
    age = c(24, 22, 23, 25, 22),  
    personality = c('g', 'b', 'g', 'b', 'g'),
    income = c(2000, 5800, 4200, 1500, 6000)
)  # finish the data.frame() function
```
- Note that the new lines are just a matter of coding style, i.e., it makes the 
code easier to read
- The same data frame can be created in a single line:

```r
data <- data.frame(age = c(24, 22, 23, 25, 22), 
personality = c('g', 'b', 'g', 'b', 'g'), income
= c(2000, 5800, 4200, 1500, 6000))
```

## Simple Example (cont'd)
- Let's take a look at our new data frame

```r
data
```

```
##   age personality income
## 1  24           g   2000
## 2  22           b   5800
## 3  23           g   4200
## 4  25           b   1500
## 5  22           g   6000
```

## Indexing: The `$` Operator
- The `$` operator lets you reference elements of an object (e.g., column 
vectors of a data frame) in `R`

```r
data$age
```

```
## [1] 24 22 23 25 22
```

```r
data$personality
```

```
## [1] g b g b g
## Levels: b g
```
- Similar to a `.` operation in other programming languages (but note that `.` 
has no special meaning in `R`!)

## Indexing: Numeric Row/Column
- Since a data frame is a table of data, you can treat it like a matrix, and 
index its entries by `[row #, col #]` notation

```r
data[2, 3]  # item in row 2 column 3
```

```
## [1] 5800
```

```r
data[, 2]  # entire column 2
```

```
## [1] g b g b g
## Levels: b g
```

```r
data[4, ]  # entire row 4
```

```
##   age personality income
## 4  25           b   1500
```

## Indexing: Named Variables
- Since the columns represent variables with names, you can index columns by a 
string representing variable names

```r
data[, 'age']  # entire 'age' column
```

```
## [1] 24 22 23 25 22
```

```r
# entries 3~5 of 'personality' column
data[3:5, 'personality']
```

```
## [1] g b g
## Levels: b g
```

## Indexing: Vectors
- As with vectors/matrices, you can index a data frame with vectors 
(either numeric or string)

```r
data[1:3, c('age', 'income')]
```

```
##   age income
## 1  24   2000
## 2  22   5800
## 3  23   4200
```

```r
data[c(1,4), 2:3]
```

```
##   personality income
## 1           g   2000
## 4           b   1500
```

## Conditional Indexing
- Pick-out entries that match specific criteria by first creating a binary 
vector for indexing

```r
# find the 22-year-olds
ind <- data$age == 22
data[ind, ]  # index rows by binary vector ind
```

```
##   age personality income
## 2  22           b   5800
## 5  22           g   6000
```

## Chained Indexing
- Note that 
    - when you index rows of a single column, the result is a vector
    - when you index multiple columns, the result is a new data frame
- You can chain indices to pin-point elements of a data frame
- For example, all of the following operations are equivalent

```r
# Equivalent operations to get the age of 
# third observation (row 3)
data[3, 1]  # if you know that 'age' is column 1
data[3, 'age']
data[3,]$age  # get 'age' of row 3
data$age[3]  # get third observation of 'age' variable
```
 
## Column (Variable) Names
- To see the column name of a data frame, use the `colnames()` function
- The column names can be changed by directly assigning a new vector of names 
to the `colnames()` function

```r
colnames(data)
```

```
## [1] "age"         "personality" "income"
```

```r
colnames(data) <- c('age', 'attitude', 'income')
data
```

```
##   age attitude income
## 1  24        g   2000
## 2  22        b   5800
## 3  23        g   4200
## 4  25        b   1500
## 5  22        g   6000
```

## Write Data Frames to Files
- Use `write.table()` to write data frames to (text) files
- The syntax is
```
write.table(x, file = "", append = FALSE, 
    quote = TRUE, sep = " ", 
    row.names = TRUE, col.names = TRUE)
```
- For example, to save our sample data to a file named `data.tsv` with the 
entries of each row separated by a tab character, write

```r
write.table(data, file='data.tsv', sep='\t', 
    row.names=FALSE)  # row names are rarely needed 
```
- Recall, the default directory is the current working directory, specified with 
`setwd()`, and retrieved with `getwd()`
- For more options, see documentation

```r
?write.table
```

## Read Data Frames from Files
- To read data frames that exist as text files, use the general `read.table()` 
function
- Note that specific options for `read.table()` will depend on the structure of 
the text file you wish to read (e.g., comma-separated or tab-separated)
- For example, to read the file we just saved, 

```r
data <- read.table('data.tsv', header=TRUE, sep='\t')
```
- Some shortcuts for pre-defined (commonly used) formats

```r
read.csv(file)  # comma-separated values (.csv)
read.delim(file)  # tab-separated values (.tsv)
```
- See the documentation for more details

```r
?read.table
```

## Read Data from Online Database
- `read.table()` can also load data frames from an online database
- While loading data directly from the web is not recommended, this can be 
useful when making a local copy of an online database
- For example, to make a local copy of the dataset saved in 
`http://goo.gl/6fV7UT`

```r
address <- 'http://goo.gl/6fV7UT'
data <- read.table(address, header=TRUE)
write.table(data, file='data.tsv', sep='\t')
```
- Note that you can read data in one format (e.g., comma-separated) and save 
the local copy in another (e.g., tab-separated)

# Exploring Data Frames

## Example Data
- We'll use a sample dataset from `http://goo.gl/6fV7UT`
- First, load the data into your workspace

```r
address <- 'http://goo.gl/6fV7UT'
autompg <- read.table(address, header=TRUE)
```
- The data contains fuel consumption data of 398 vehicles 
- Originally from the [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/)
- See documentation [here](http://archive.ics.uci.edu/ml/machine-learning-databases/auto-mpg/auto-mpg.names)
    - http://archive.ics.uci.edu/ml/machine-learning-databases/auto-mpg/auto-mpg.names

## Display Structure with `str()`
- The `str()` function is useful for exploring the overall structure of a 
data frame

```r
str(autompg)
```

```
## 'data.frame':	398 obs. of  10 variables:
##  $ mpg         : num  18 15 18 16 17 15 14 14 14..
##  $ cylinders   : int  8 8 8 8 8 8 8 8 8 8 ...
##  $ displacement: num  307 350 318 304 302 429 45..
##  $ horsepower  : Factor w/ 94 levels "?","100.0"..
##  $ weight      : int  3504 3693 3436 3433 3449 4..
##  $ accel       : num  12 11.5 11 12 10.5 10 9 8...
##  $ year        : int  70 70 70 70 70 70 70 70 70..
##  $ origin      : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ model       : Factor w/ 305 levels "amc amba"..
##  $ make        : Factor w/ 37 levels "amc","aud"..
```

## `Factor`s
- Note that some variables are `factor`s
- A `factor` is a data frame representation of categorical variables
- The entries of a `factor` variable is defined by `levels`

```r
levels(autompg$make)
```
- Use `unique()` to list the unique values of any variable

```r
unique(autompg$year)
```

```
##  [1] 70 71 72 73 74 75 76 77 78 79 80 81 82
```
- Use `factor()` to make a factor varaible from non-factor variables

```r
autompg$year <- factor(autompg$year)
```

## Basic `plot`s
- Use `plot()` to generate quick and dirty (but often helpful) plots
- By default, `plot()` will generate histograms of categorical variables 
(`factor`s) and scatter plots (with respect to row index) of continuous 
variables

```r
plot(autompg$year)
```



\begin{center}\includegraphics{02-notes_files/figure-beamer/unnamed-chunk-30-1} \end{center}

## Basic `plot`s (cont'd)

```r
plot(autompg$mpg)
```



\begin{center}\includegraphics{02-notes_files/figure-beamer/unnamed-chunk-31-1} \end{center}

## Basic `plot`s (cont'd)
- Use syntax `plot(x, y)` to plot two variables

```r
plot(autompg$weight, autompg$mpg)
```



\begin{center}\includegraphics{02-notes_files/figure-beamer/unnamed-chunk-32-1} \end{center}

## Plotting `pairs`
- To plot more than two variables against each other, use `pairs()`

```r
pairs(autompg[, c('mpg', 'weight', 'year')])
```



\begin{center}\includegraphics{02-notes_files/figure-beamer/unnamed-chunk-33-1} \end{center}

- Note that you can plot the entire data frame with ```pairs(autompg)```

## Data Frame Basics: Exercise
- From the `autompg` data
    - create a new data frame with all the buick vehicles (i.e., `make=="buick"`)
    - generate a `summary()` of the buick vehicles' `mpg`
    - make the `cylinders` variable of the buick data frame into a factor
    - plot a histogram of the buick's `cylinders`
- These are just (very) basic operations
- For more complicated operations, we'll use `dlyr` and `tidyr` (covered next)
- For more sophisticated plots, we'll use `ggplot2` (covered in the next session)

# Exercise Solution

## WARNING
- Solutions to the exercise are presented in the next slide
- Try the exercise before proceeding!

## Solution

```r
buick_index <- autompg$make == 'buick'
buick <- autompg[buick_index, ]
summary(buick$mpg)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   12.00   14.00   17.70   19.18   22.40   30.00
```

```r
buick$cylinders <- factor(buick$cylinders)
plot(buick$cylinders)
```



\begin{center}\includegraphics{02-notes_files/figure-beamer/unnamed-chunk-34-1} \end{center}

# Munging Data with `dplyr`

## Introduction to `dplyr`
- `dplyr` is a package that provides a convenient framework (along with a 
handful of useful functions) for wrangling data (frames)
- Install and load the `dplyr` package like you would any other `R` package

```r
# Install, if you haven't already.
# Only need to do this once on a single machine.
install.packages('dplyr')
# load package into workspace
library('dplyr')
```
- We'll primarily use the `flights` data frame from the `nycflights13` package 
in this part

## Verbs
- A *verb* in the world of `dplyr` is a function that takes a data frame as its 
first argument, and returns another data frame as a result
- For example, the `head()` function can be considered a verb

```r
head(flights, n = 10)
```
- Note that the result of the `head()` function is another data frame 
(in this case, with 3 rows)
- The **core idea of `dplyr`** is that most of your data manipulation needs can 
be satisfied with 5 basic verbs (or 4, depending on how you categorize them)

## Five basic verbs
- The five basic verbs of `dplyr` and associated actions are presented below

verb | action
---- | ------
`filter()` | select a subset of *rows* by specified conditions
`select()` | select a subset of *columns* 
`mutate()` | create a *new column* \
(usually by operations of existing columns)
`arrange()` | reorder (sort) *rows* by values of specified *column*(s)
`summarize()` | aggregate values and reduce to single value

- Some verbs have additional options or convenient wrappers

## Selecting Rows: `filter()`
- Select a subset of *rows*
- Multiple conditions can be used
- Use `&` to specify `AND` conditions
- Use `|` to specify `OR` conditions
- `AND(&)`/`OR(|)` operations can be used together 
(where default behavior for multiple conditions is `AND`)

```r
filter(flights, tailnum == 'N14228' & arr_delay > 10)
filter(flights, 
       tailnum == 'N14228' |  tailnum == 'N24211')
filter(flights, 
       tailnum == 'N14228' |  tailnum == 'N24211', 
       arr_delay > 10)
```

## Selecting Rows: `slice()`
- To select rows by numerical index (position), use `slice()`
- For example, to select the first 10 rows

```r
slice(flights, 1:10)
```
- or to select the last 10 rows

```r
slice(flights, (n() - 9):n())
```
- Use `n()` inside a `dplyr` verb to to indicate the *number of rows* of the 
data frame

## Selecting Columns: `select()`
- Select a subset of *columns*
- Either specify the columns that you want to select

```r
select(flights, carrier, tailnum)
```
- Or specify the columns you wish to drop

```r
select(flights, -year, -month, -day)
```

## Selecting Columns: `select()` (cont'd)
- `dplyr` provides useful helper functions you can use to `select()` columns 
that match specific criteria such as
    - `starts_with(x)`: names that start with `x`
    - `ends_with(x)`: names that end with `x`
    - `contains(x)`: names that contain `x`
    - `matches(x)`: names that match the (regular expression) `x`
- See the documentation for more details

```r
?dplyr::select
```
- While you can assign new column names with `select()` the convenience function 
`rename()` lets you rename columns while retaining the rest of the data frame

```r
select(flights, tail_num = tailnum)
rename(flights, tail_num = tailnum)
```

## Create New Columns: `mutate()`
- Create new columns, usually as a function of existing columns
- You can refer to new columns you just created, inside the same `mutate()` 
function

```r
mutate(flights, gain = arr_delay - dep_delay,
       speed = distance / air_time * 60,
       # use the gain column we just created
       # to create yet another gain_per_hour column
       gain_per_hour = gain / (air_time / 60)
       )
```
- Use `transmute()` to create a new data frame *just from* the new column(s)

```r
transmute(flights, gain = arr_delay - dep_delay)
```

## Sorting Rows by Column Value: `arrange()`
- Reorder the rows of a data frame by the specified column's value
- Multiple conditions are arranged from left to right
- Use `desc()` to arrange in descending order

```r
arrange(flights, year, month, day)
arrange(flights, year, desc(month), day)
arrange(flights, year, month, desc(day))
arrange(flights, year, desc(month), desc(day))
```

## Aggregate Data: `summarize()`
- Aggregate/collapse the data into a single row
- Think of as applying a function to columns

```r
summarize(flights, delay = mean(dep_delay))
# Note that the mean function need help
# handling NA values
summarize(flights, 
          delay = mean(dep_delay, na.rm = TRUE))
```

- More useful as a grouped operation (see next)

## Grouped Operations
- If a data frame is *grouped*, operations are applied to each group separately, 
and the results are combined back to a single data frame
- Use the `group_by()` verb to specify variables to use for generating groups

```r
flights_by_day <- group_by(flights, day)
```
- Some verbs have specific behavior when applied to grouped data

verb | group specific action
--- | ---
`arrange()` | sort rows within each group
`slice()` | extract rows within each group
`summarize()` | aggregate values group-wise

## Grouped `slice()`
- Retrieve the first 2 departures (rows) of each day

```r
slice(flights_by_day, 1:2)
```

```
## Source: local data frame [62 x 16]
## Groups: day [31]
## 
##     year month   day dep_time dep_delay arr_time
##    (int) (int) (int)    (int)     (dbl)    (int)
## 1   2013     1     1      517         2      830
## 2   2013     1     1      533         4      850
## 3   2013     1     2       42        43      518
## 4   2013     1     2      126       156      233
## 5   2013     1     3       32        33      504
## 6   2013     1     3       50       185      203
## 7   2013     1     4       25        26      505
## 8   2013     1     4      106       141      201
## 9   2013     1     5       14        15      503
## 10  2013     1     5       37       127      341
## ..   ...   ...   ...      ...       ...      ...
## Variables not shown: arr_delay (dbl), carrier
##   (chr), tailnum (chr), flight (int), origin
##   (chr), dest (chr), air_time (dbl), distance
##   (dbl), hour (dbl), minute (dbl)
```

## Grouped `summarize()`
- Retrieve (1) number of departures (observations), (2) average distance, and  (3) 
average arrival delay for each day (i.e., for flights grouped by day)

```r
summarize(flights_by_day,  count = n(),
          dist = mean(distance, na.rm=TRUE),
          delay = mean(arr_delay, na.rm=TRUE))
```

```
##   day count     dist      delay
## 1   1 11036 1039.478  7.3636956
## 2   2 10808 1046.753  6.7680540
## 3   3 11211 1041.299  4.4699187
## 4   4 11059 1037.793 -1.7827199
## 5   5 10858 1037.845  0.4925064
## 6   6 11059 1040.868 -1.7489044
```

## Multiple (Chained) Operations
- Consider the following task

> find days when the mean arrival delay OR departure delay was greater than 30

- We can achieve the desired result with three operations
    1. **`group_by`** date (`year`, `month`, `day`)
    2. **`summarize`** mean arrival/departure delay
    3. **`filter`** summarized results (i.e., mean `arr_delay` > 30 
    | mean `dep_delay` > 30)
    
- Note that `dplyr` verbs do **not** modify the original data frame
    - This is generally a good thing, since it guarantees the integrity of your data 
    - But it makes multiple operations on a data frame difficult
- There are two (acceptable) ways to apply multiple operations on a data frame, 
and one is definitely prefered to the other

## Multiple Operations: The OK Way
- One way to perform multiple operations is to save intermediate data frames as 
new data frames
- This method delivers desired results, but makes your workspace quite messy 
(i.e., you'll end up with a workspace full of intermediate results)

```r
flights_by_date <- group_by(flights, year, month, day)
summary_by_date <- summarize(flights_by_date,
    arr = mean(arr_delay, na.rm=TRUE),
    dep = mean(dep_delay, na.rm=TRUE))
big_delay_dates <- filter(summary_by_date, 
                          arr > 30 | dep > 30)
```
- This method might be prefered if you need the intermediate results in 
the future
- If not, there is a better way to chain multiple operations with `dplyr`

## The Pipe Operator `%>%`
- The pipe operator, aka the 'magic' operator, takes the output from the verb on 
its left-hand side, and uses it as the first argument (data frame) for the verb
on the right-hand side

```r
big_delay_dates <- 
    group_by(flights, year, month, day) %>% 
    summarize(arr = mean(arr_delay, na.rm=TRUE),
              dep = mean(dep_delay, na.rm=TRUE)) %>% 
    filter(arr > 30 | dep > 30)
```
- No need to save intermediate results
- Easier to read (i.e., you can follow the operations step-by-step without too 
much mental accounting)

## `dplyr`: Exercise
- With the `flights` data
    1. find the average speed (`distance / air_time * 60`) by each carrier 
    (ignore `NA`), and sort the data in descending order of average speed
    2. find the number of flights and average flight time of all flights greater 
    than 10 hours by each carrier in April

# Exercise Solution

## WARNING
- Solutions to the exercise are presented in the next slide
- Try the exercise before proceeding!

## Solution 1

```r
speed_by_carrier <-
    group_by(flights, carrier) %>%
    mutate(speed = distance / air_time * 60) %>%
    summarize(avg_speed = mean(speed, na.rm=TRUE)) %>%
    arrange(desc(avg_speed))
speed_by_carrier
```

```
##   carrier avg_speed
## 1      HA  480.3577
## 2      VX  446.1749
## 3      AS  443.6789
## 4      F9  425.1721
## 5      UA  420.8838
## 6      DL  418.4628
## 7      AA  417.4727
## 8      WN  400.5320
```

## Solution 2

```r
april_long_flights <-
    group_by(flights, month, carrier) %>%
    filter(month == 4 & hour > 10) %>%
    summarize(avg = mean(hour, na.rm=TRUE), 
              count = n())
april_long_flights
```

```
##   month carrier      avg count
## 1     4      9E 16.72074  1085
## 2     4      AA 15.72994  1670
## 3     4      AS 18.36667    30
## 4     4      B6 16.98868  2916
## 5     4      DL 15.89183  2718
## 6     4      EV 16.38317  2876
## 7     4      F9 17.51613    31
## 8     4      FL 15.68398   231
```

# Reshape Data with `tidyr`

## Introduction to `tidyr`
- Recall, the prefered way to think about a data frame:
```
Each column represents a variable/feature
Each row represents an observation/instance
```
- Consider the following (fictional) data frame of students' homework grades

ID | HW1 | HW2 | HW3 | HW4 | HW5
--- | --- | --- | --- | --- | ---
jamie | 6 | 7 | 3 | 5 | 8
cersei | 8 | 5 | 2 | 1 | 9
hodor | 9 | 10 | 9 | 3 | 10

- What are the variables of this data?
- What are the potential issues with this representation?
- What are the benefits of this representation?

## Introduction to `tidyr`: An Example
- For data manipulation/visualization we often prefer to have data in the 
*long* form
- The *long* form of the previous data would be

ID | HW | Score
--- | --- | ---
jamie | 1 | 6
jamie | 2 | 7
$\vdots$ | $\vdots$ | $\vdots$
hodor | 4 | 3
hodor | 5 | 10

- `tidyr` is a package that provides a tools for converting data between *long* 
and *wide* forms

## Introduction to `tidyr`: Getting Started
- Install and load the `tidyr` package like you would any other `R` package

```r
# Install, if you haven't already.
# Only need to do this once on a single machine.
install.packages('tidyr')
```

```r
# load package into workspace
library('tidyr')
```

## Introduction to `tidyr`: Getting Started (cont'd)
- Create some random data

```r
grades <- 
    data.frame(ID = c('jamie', 'cersei', 'hodor'),
               matrix(runif(15, 1, 10), nrow=3))
colnames(grades) <-  c('ID',  paste0('HW', 1:5))
grades$info <- c('male/lannister', 
                 'female/lannister',
                 'male/stark')
```
- Take some time to understand what just happened

## Create *long* data with `gather()`
- With the `grades` data, we would like to create a data frame in the form of

ID | info | HW | score
--- | --- | --- | ---
- | - | - | -

- The verb for gathering multiple columns into key-value pairs in `tidyr` 
is `gather()`
- The syntax is

```r
gather(data, key, value, ...)
```
- where the `...` should be replaced by column specifications

## Create *long* data with `gather()` (cont'd)

```r
grades.tidy <- gather(grades, HW, score, HW1:HW5)
grades.tidy
```

```
##       ID             info  HW    score
## 1  jamie   male/lannister HW1 3.293486
## 2 cersei female/lannister HW1 9.232992
## 3  hodor       male/stark HW1 9.899013
## 4  jamie   male/lannister HW2 1.557327
## 5 cersei female/lannister HW2 9.137192
## 6  hodor       male/stark HW2 4.931162
```

## Split a Column to Multiple Variables with `separate()`
- Often, there will be column that you'd prefer to split into multiple 
variables, e.g., splitting a `date` column to `year`, `month`, and `day`
- From the `grades` data, notice that the `info` column combines two variables: 
`sex` and `house`
- We can split such columns to multiple variables with the `separate()` verb in `tidyr`

```r
grades.split <- 
    separate(grades.tidy, info, 
             into = c('sex', 'house'), sep = '/')
grades.split
```

```
##       ID    sex     house  HW    score
## 1  jamie   male lannister HW1 3.293486
## 2 cersei female lannister HW1 9.232992
## 3  hodor   male     stark HW1 9.899013
```

## Chaining `tidyr` verbs
- Note that `tidyr` operations are also verbs that can be chained with the pipe 
operator `%>%`
- For example, we can do the previous operations on the `grades` data with the 
chained operation

```r
grades.final <- grades %>%
    gather(HW, score, HW1:HW5) %>%
    separate(info, into=c('sex', 'house'), sep='/')
```
- `tidyr` verbs can also be chained with `dplyr` verbs (and any other function
that qualifies as a verb, i.e., takes a data frame as the first argument and
results in a new data frame)

## Exercise
- Create some random data

```r
finance <- 
    data.frame(ID = c('leia', 'han', 'luke'),
               matrix(rnorm(18, 1e4, 5e3), nrow=3))
colnames(finance) <- 
    c('ID', 
      paste('Income', 2013:2015, sep='_'),
      paste('Expense', 2013:2015, sep='_'))
```
1. Tidy the data to fit the form

ID | type | year | amount
--- | --- | --- | --- |
- | - | - | -

2. Find the mean and total Income/Expense for each ID across all years
    
# Exercise Solution

## WARNING
- Solutions to the exercise are presented in the next slide
- Try the exercise before proceeding!

## Solution 1

```r
finance <- finance %>%
    gather(year, amount, 2:7) %>%
    separate(year, c('type', 'year'), sep='_')
finance
```

```
##     ID   type year    amount
## 1 leia Income 2013  4074.325
## 2  han Income 2013  9277.578
## 3 luke Income 2013  5065.300
## 4 leia Income 2014 14249.437
## 5  han Income 2014  5007.509
## 6 luke Income 2014 -1530.526
## 7 leia Income 2015  6864.637
## 8  han Income 2015 11853.213
```

## Solution 2

```r
finance.summary <- finance %>%
    group_by(ID, type) %>% 
    summarize(mean=mean(amount), total=sum(amount)) 
finance.summary
```

```
## Source: local data frame [6 x 4]
## Groups: ID [?]
## 
##       ID    type      mean    total
##   (fctr)   (chr)     (dbl)    (dbl)
## 1    han Expense 14301.997 42905.99
## 2    han  Income  8712.767 26138.30
## 3   leia Expense  6414.693 19244.08
## 4   leia  Income  8396.133 25188.40
## 5   luke Expense  5814.854 17444.56
## 6   luke  Income  3609.054 10827.16
```

# Combining Data with `join`s

## Introduction to `join`s
- Sometimes, you will find relevant data across multiple datasets, e.g., a list 
of grades in one dataset and a list of contact information in another
- In such cases, you may want to `join` the two datasets into a single data frame
prior to further analysis
- For a successful `join` you must determine
    1. One or more variables that uniquely identify matching observations (rows) 
    in both datasets
    2. How you wish to join the data, i.e.,
        - **Left/right join** Retain one of the datasets entirely, while only 
        keeping matching entries of the other, possibly resulting in a few 
        missing values (`NA`)
        - **Inner join** Retain only complete matches, possibly dropping some rows 
        of both datasets
        - **Outer (full) join** Retain all rows of both datasets, but potentially 
        resulting in many missing values (`NA`)
        
## Example Datasets
- For illustration, let's create two data frames

```r
info <- data.frame(
    name=c('tony', 'tony', 'rey'),
    job=c('scientist', 'tiger', 'scavenger'),
    score=rnorm(3))

power <- data.frame(
    name=c('tony', 'hank', 'rey'),
    job=c('scientist', 'scientist', 'scavenger'),
    strength=rexp(3))
```
- How is an observation (row) uniquely identified?

## Left/Right `join`
- Retain rows of one dataset, and match the rows of the other

```r
left_join(info, power, by=c('name', 'job'))
```

```
##   name       job       score  strength
## 1 tony scientist -0.05760932 0.1790799
## 2 tony     tiger  1.31287626        NA
## 3  rey scavenger -0.76625326 1.8179469
```

```r
right_join(info, power, by=c('name', 'job'))
```

```
##   name       job       score  strength
## 1 tony scientist -0.05760932 0.1790799
## 2 hank scientist          NA 3.4484913
## 3  rey scavenger -0.76625326 1.8179469
```

## Inner `join`
- Retain only the rows that have matches on both datasets

```r
inner_join(info, power, by=c('name', 'job'))
```

```
##   name       job       score  strength
## 1 tony scientist -0.05760932 0.1790799
## 2  rey scavenger -0.76625326 1.8179469
```

## Outer (Full) `join`
- Retain all rows

```r
full_join(info, power, by=c('name', 'job'))
```

```
##   name       job       score  strength
## 1 tony scientist -0.05760932 0.1790799
## 2 tony     tiger  1.31287626        NA
## 3  rey scavenger -0.76625326 1.8179469
## 4 hank scientist          NA 3.4484913
```

## Reference
- A great "cheat sheet" for wrangling data with `dplyr` and `tidyr` is available
for free at 
https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf
