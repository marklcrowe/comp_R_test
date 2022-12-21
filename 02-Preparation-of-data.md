---
title: "Preparation of Data"
teaching: 45
exercises: 10
---

::::::::::::::::::::::::::::::::::::: questions

- How can I import a dataset into R?

::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: objectives

- Review the structure of a CSV datafile
- Read a CSV file into a dataframe
- Manage the assignment of data as factors or character strings

::::::::::::::::::::::::::::::::::::: 



 
## Previewing data

Prior to uploading any dataset into R for analysis, it is useful to review the 
contents of that file to understand the structure of the data. This will allow
you to choose the best data structure for your dataset, and will identify the 
relevant command and settings you need when uploading the data to R. 

For small files, like the one we are using today, this review can be done with a
simple text editor, either an app on your computer or from within RStudio. For 
very large datasets this is not advisable - files may take a very long time to 
open and may even crash your computer. Such large files may have documentation 
explaining their format, or you may need to use the `head` function to look at 
just the first part of the file.

Today we will be working with a datafile based on a study into the effects of
treatment with the drug ursodeoxycholic acid on gallstones. This is based on a 
real study ([Hood et al, 1993](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1375471/)).
In this study, detailed follow-up records were kept for 93 patients receiving
various combinations of dietary intervention and ursodeoxycholic acid treatment
to investigate ways of reducing the risk of gallstone recurrence. For our
exercises today we will just use a subset of the data, with dummy demographic
data substituted for real patient information.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1

Use RStudio “Files” window, your text editor of choice, or a spreadsheet 
program to open and review the data file _gallstones.csv_ (do not overwrite 
the file by saving afterwards). Consider the meaning of the headers and what
the nature of the data in each column might be; perhaps discuss with your 
neighbour if you are in an in-person session. How many patient records are 
there in the dataset?

::::::::::::::::::::::::::::::::::::: solution

## Solution to challenge 1

The field contents are as follows:

* Patient_ID – a unique numerical identifier for each patient
* Gender – female or male: one-letter character string
* Age – patient’s age: numerical value in years
* Height – patient’s height: numerical value in (probably) cm
* Weight – patient’s weight: numerical value in (probably) kg
* BMI – patient’s body mass index (weight/height in m<sup>2</sup>)
* Obese – is the patient obese (BMI>30): 0=no, 1=yes;
* Smoking.Status – does the patient smoke: 1=non-smoker, 2=smoker; 
* Alcohol.Consumption – does the patient drink alcohol: 1=no, 2=previously,
3=currently; 
* Treatment – did the patient receive the ursodeoxycholic acid treatment: 
0=untreated, 1=treated; 
* Rec – did gallstones recur: 0=no recurrence, 1=recurrence; 
* Mult – did the patient have multiple gallstones: 0=single stone, 1=multiple 
stones; 
* Diam – original gallstone diameter in mm; 
* Dis – time in months for gallstone dissolution

There are a total of 37 patient records in this file

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

## Importing data

Once you've reviewed the contents of the data file, you can decide how best to
upload it into R. In this case, we have a tabular layout with fields separated
by commas - a CSV file - so we will use the `read.csv` function to import this
dataset.

::::::::::::::::::::::::::::::::::::: callout

When reading in tabular data in R, you can use either `read.table` or a range
of pre-configured aliases: `read.csv`, `read.csv2`, `read.delim` and
`read.delim2`. These have function arguments already set to correspond to a
range of different formats of CSV (comma separated value) and TSV (tab
separated value) files.

::::::::::::::::::::::::::::::::::::: 


```r
# The default settings of `read.csv` differ between R versions, so when running
# the command, we need to specify the stringsAsFactors parameter
gallstones <- read.csv("data/gallstones.csv", stringsAsFactors = TRUE)
```

Now the file is uploaded, you can find out more about this dataset using the 
`head`, `str` and `summary` functions.

```r
head(gallstones)
```

```{.output}
  Patient_ID Gender Age Height Weight      BMI Obese Smoking.Status
1        P25      F  64    147     65 30.08006     1              2
2        P28      F  81    151     69 30.26183     1              2
3        P17      M  77    156     59 24.24392     0              2
4        P27      F  80    156     47 19.31295     0              2
5         P5      F  86    156     53 21.77844     0              2
6         P6      F  69    157     48 19.47341     0              1
  Alcohol.Consumption Treatment Rec Mult Diam Dis
1                   1         1   1    1    6   8
2                   2         0   1    1    7   6
3                   1         0   0    0   20  20
4                   3         1   0    0   15   2
5                   2         0   1    0   18  14
6                   3         1   0    0   19   8
```

```r
str(gallstones)
```

```{.output}
'data.frame':	37 obs. of  14 variables:
 $ Patient_ID         : Factor w/ 37 levels "P1","P10","P11",..: 18 21 9 20 33 34 35 19 28 30 ...
 $ Gender             : Factor w/ 2 levels "F","M": 1 1 2 1 1 1 1 1 1 1 ...
 $ Age                : int  64 81 77 80 86 69 75 77 73 88 ...
 $ Height             : int  147 151 156 156 156 157 157 160 160 160 ...
 $ Weight             : int  65 69 59 47 53 48 46 55 51 54 ...
 $ BMI                : num  30.1 30.3 24.2 19.3 21.8 ...
 $ Obese              : int  1 1 0 0 0 0 0 0 0 0 ...
 $ Smoking.Status     : int  2 2 2 2 2 1 2 2 2 1 ...
 $ Alcohol.Consumption: int  1 2 1 3 2 3 2 3 3 3 ...
 $ Treatment          : int  1 0 0 1 0 1 0 1 0 1 ...
 $ Rec                : int  1 1 0 0 1 0 1 0 1 0 ...
 $ Mult               : int  1 1 0 0 0 0 1 0 0 1 ...
 $ Diam               : int  6 7 20 15 18 19 14 18 15 5 ...
 $ Dis                : int  8 6 20 2 14 8 8 4 15 3 ...
```

```r
summary(gallstones)
```

```{.output}
   Patient_ID Gender      Age            Height          Weight     
 P1     : 1   F:21   Min.   :31.00   Min.   :147.0   Min.   : 46.0  
 P10    : 1   M:16   1st Qu.:67.00   1st Qu.:160.0   1st Qu.: 58.0  
 P11    : 1          Median :77.00   Median :163.0   Median : 65.0  
 P12    : 1          Mean   :72.57   Mean   :164.6   Mean   : 69.7  
 P13    : 1          3rd Qu.:85.00   3rd Qu.:168.0   3rd Qu.: 79.0  
 P14    : 1          Max.   :90.00   Max.   :189.0   Max.   :109.0  
 (Other):31                                                         
      BMI            Obese        Smoking.Status  Alcohol.Consumption
 Min.   :18.66   Min.   :0.0000   Min.   :1.000   Min.   :1.000      
 1st Qu.:21.78   1st Qu.:0.0000   1st Qu.:1.000   1st Qu.:2.000      
 Median :24.46   Median :0.0000   Median :2.000   Median :2.000      
 Mean   :25.51   Mean   :0.2973   Mean   :1.649   Mean   :2.243      
 3rd Qu.:30.09   3rd Qu.:1.0000   3rd Qu.:2.000   3rd Qu.:3.000      
 Max.   :34.72   Max.   :1.0000   Max.   :2.000   Max.   :3.000      
                                                                     
   Treatment           Rec              Mult             Diam      
 Min.   :0.0000   Min.   :0.0000   Min.   :0.0000   Min.   : 3.00  
 1st Qu.:0.0000   1st Qu.:0.0000   1st Qu.:0.0000   1st Qu.: 6.00  
 Median :1.0000   Median :0.0000   Median :1.0000   Median :10.00  
 Mean   :0.5405   Mean   :0.4324   Mean   :0.5135   Mean   :11.41  
 3rd Qu.:1.0000   3rd Qu.:1.0000   3rd Qu.:1.0000   3rd Qu.:15.00  
 Max.   :1.0000   Max.   :1.0000   Max.   :1.0000   Max.   :27.00  
                                                                   
      Dis       
 Min.   : 2.00  
 1st Qu.: 6.00  
 Median : 8.00  
 Mean   :10.68  
 3rd Qu.:12.00  
 Max.   :48.00  
                
```

## Columns as factors

This shows, among other details, that the dataset consists of records from 37 
patients, aged 31 to 90, of whom 21 are female and 16 male. Looking further, you
may notice that patient identifier, which should be unique, has been imported 
as a factor - this is probably not the best option for a unique identifier so it 
would be best to convert this to character strings.


```r
gallstones$Patient_ID <- as.character(gallstones$Patient_ID)
str(gallstones$Patient_ID)
```

```{.output}
 chr [1:37] "P25" "P28" "P17" "P27" "P5" "P6" "P7" "P26" "P34" "P36" "P11" ...
```

::::::::::::::::::::::::::::::::::::: callout

Sometimes patient ID is numeric and ordered according to recruitment. In this
situation, it can be used as a surrogate for time to check for any biases over 
the course of the study.

::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2

How could you have modified your `read.csv` command so that patient identifiers
were imported as character strings in the first place? What other issues might
this have caused?

::::::::::::::::::::::::::::::::::::: solution

## Solution to challenge 2

You could use the `stringsAsFactors = FALSE` argument to `read.csv`. But this
would mean that Gender was also read in as character strings, and that is best
represented as a factor.

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 


::::::::::::::::::::::::::::::::::::: keypoints

- Open a data file in a text editor or RStudio file viewer
- Use `read.table` or `read.csv` to import data
- Review a dataframe using `str` and `summary`
- Convert columns from factors to string using `as.character`

::::::::::::::::::::::::::::::::::::: 

[r-markdown]: https://rmarkdown.rstudio.com/
