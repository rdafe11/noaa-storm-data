---
title: "Reproducible Research: Peer Assessment 2"
output: 
  html_document:
    keep_md: true
---
# Health/Economic Consequences in U.S. caused by Storms ans Weather Events



# 1 - Introduction
Storms and other severe weather events can cause both public health and economic problems for communities and municipalities. Many severe events can result in fatalities, injuries, and property damage, and preventing such outcomes to the extent possible is a key concern.

This Data Science Project involves exploring the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States, including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.

# 2 - Synopsis
This report address questions related to Weather Events and Storms in U.S. that are most damaging in terms of Fatalities, Injuries and damages to properties and crop.

The two questions to be answered are:

1.Which types of events are most harmful with respect to population health?

2.Which types of events have the greatest economic consequences?

## Loading required libraries


```r
getwd()
```

```
## [1] "G:/r/noaa-storm-data"
```

```r
library("knitr")
library(RColorBrewer)
opts_chunk$set(echo = TRUE )
opts_chunk$set(cache = TRUE )
opts_chunk$set(out.width='1000px', dpi=200)
```
# 3 - Data Processing

## Loading NOAA data into R


```r
## Copy LinkLoaction for "Storm Data" and Download CSV File
if(!file.exists("./repdata_data_StormData.csv.bz2")){
    file.create("./repdata_data_StormData.csv.bz2")
    download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2",
                  "./repdata_data_StormData.csv.bz2")
}
## Read data as data frame
storm<-read.csv("./repdata_data_StormData.csv.bz2", header=T)
## backing up the original data and the data has to modified
stormunchanged<-storm
summary(storm)
```

```
##     STATE__       BGN_DATE           BGN_TIME          TIME_ZONE        
##  Min.   : 1.0   Length:902297      Length:902297      Length:902297     
##  1st Qu.:19.0   Class :character   Class :character   Class :character  
##  Median :30.0   Mode  :character   Mode  :character   Mode  :character  
##  Mean   :31.2                                                           
##  3rd Qu.:45.0                                                           
##  Max.   :95.0                                                           
##                                                                         
##      COUNTY       COUNTYNAME           STATE              EVTYPE         
##  Min.   :  0.0   Length:902297      Length:902297      Length:902297     
##  1st Qu.: 31.0   Class :character   Class :character   Class :character  
##  Median : 75.0   Mode  :character   Mode  :character   Mode  :character  
##  Mean   :100.6                                                           
##  3rd Qu.:131.0                                                           
##  Max.   :873.0                                                           
##                                                                          
##    BGN_RANGE          BGN_AZI           BGN_LOCATI          END_DATE        
##  Min.   :   0.000   Length:902297      Length:902297      Length:902297     
##  1st Qu.:   0.000   Class :character   Class :character   Class :character  
##  Median :   0.000   Mode  :character   Mode  :character   Mode  :character  
##  Mean   :   1.484                                                           
##  3rd Qu.:   1.000                                                           
##  Max.   :3749.000                                                           
##                                                                             
##    END_TIME           COUNTY_END COUNTYENDN       END_RANGE       
##  Length:902297      Min.   :0    Mode:logical   Min.   :  0.0000  
##  Class :character   1st Qu.:0    NA's:902297    1st Qu.:  0.0000  
##  Mode  :character   Median :0                   Median :  0.0000  
##                     Mean   :0                   Mean   :  0.9862  
##                     3rd Qu.:0                   3rd Qu.:  0.0000  
##                     Max.   :0                   Max.   :925.0000  
##                                                                   
##    END_AZI           END_LOCATI            LENGTH              WIDTH         
##  Length:902297      Length:902297      Min.   :   0.0000   Min.   :   0.000  
##  Class :character   Class :character   1st Qu.:   0.0000   1st Qu.:   0.000  
##  Mode  :character   Mode  :character   Median :   0.0000   Median :   0.000  
##                                        Mean   :   0.2301   Mean   :   7.503  
##                                        3rd Qu.:   0.0000   3rd Qu.:   0.000  
##                                        Max.   :2315.0000   Max.   :4400.000  
##                                                                              
##        F               MAG            FATALITIES          INJURIES        
##  Min.   :0.0      Min.   :    0.0   Min.   :  0.0000   Min.   :   0.0000  
##  1st Qu.:0.0      1st Qu.:    0.0   1st Qu.:  0.0000   1st Qu.:   0.0000  
##  Median :1.0      Median :   50.0   Median :  0.0000   Median :   0.0000  
##  Mean   :0.9      Mean   :   46.9   Mean   :  0.0168   Mean   :   0.1557  
##  3rd Qu.:1.0      3rd Qu.:   75.0   3rd Qu.:  0.0000   3rd Qu.:   0.0000  
##  Max.   :5.0      Max.   :22000.0   Max.   :583.0000   Max.   :1700.0000  
##  NA's   :843563                                                           
##     PROPDMG         PROPDMGEXP           CROPDMG         CROPDMGEXP       
##  Min.   :   0.00   Length:902297      Min.   :  0.000   Length:902297     
##  1st Qu.:   0.00   Class :character   1st Qu.:  0.000   Class :character  
##  Median :   0.00   Mode  :character   Median :  0.000   Mode  :character  
##  Mean   :  12.06                      Mean   :  1.527                     
##  3rd Qu.:   0.50                      3rd Qu.:  0.000                     
##  Max.   :5000.00                      Max.   :990.000                     
##                                                                           
##      WFO             STATEOFFIC         ZONENAMES            LATITUDE   
##  Length:902297      Length:902297      Length:902297      Min.   :   0  
##  Class :character   Class :character   Class :character   1st Qu.:2802  
##  Mode  :character   Mode  :character   Mode  :character   Median :3540  
##                                                           Mean   :2875  
##                                                           3rd Qu.:4019  
##                                                           Max.   :9706  
##                                                           NA's   :47    
##    LONGITUDE        LATITUDE_E     LONGITUDE_       REMARKS         
##  Min.   :-14451   Min.   :   0   Min.   :-14455   Length:902297     
##  1st Qu.:  7247   1st Qu.:   0   1st Qu.:     0   Class :character  
##  Median :  8707   Median :   0   Median :     0   Mode  :character  
##  Mean   :  6940   Mean   :1452   Mean   :  3509                     
##  3rd Qu.:  9605   3rd Qu.:3549   3rd Qu.:  8735                     
##  Max.   : 17124   Max.   :9706   Max.   :106220                     
##                   NA's   :40                                        
##      REFNUM      
##  Min.   :     1  
##  1st Qu.:225575  
##  Median :451149  
##  Mean   :451149  
##  3rd Qu.:676723  
##  Max.   :902297  
## 
```
Defining variables that will be used:

- EVTYPE: Event Type (Tornados, Flood, ....)

- FATALITIES: Number of Fatalities

- INJURIES: Number of Injuries

- PROGDMG: Property Damage

- PROPDMGEXP: Units for Property Damage (magnitudes - K,B,M)

- CROPDMG: Crop Damage

- CROPDMGEXP: Units for Crop Damage (magnitudes - K,BM,B)

e) Unknown symbols in PROPDMGEXP and CROPDMGEXP can be equated to 0.


```r
## Data Conversion: Factor data to character
storm$PROPDMGEXP<-as.character(storm$PROPDMGEXP)
storm$CROPDMGEXP<-as.character(storm$CROPDMGEXP)
## Property and crop damage units and respective column numbers
propcol<-grep("PROPDMGEXP",colnames(storm))
cropcol<-grep("CROPDMGEXP",colnames(storm))
storm[storm$PROPDMGEXP=="",propcol]<-"0"
storm[storm$PROPDMGEXP=="-",propcol]<-"0"
storm[storm$PROPDMGEXP=="?",propcol]<-"0"
storm[storm$PROPDMGEXP=="+",propcol]<-"0"
storm[storm$PROPDMGEXP=="1",propcol]<-"0"
storm[storm$PROPDMGEXP=="2",propcol]<-"0"
storm[storm$PROPDMGEXP=="3",propcol]<-"0"
storm[storm$PROPDMGEXP=="4",propcol]<-"0"
storm[storm$PROPDMGEXP=="5",propcol]<-"0"
storm[storm$PROPDMGEXP=="6",propcol]<-"0"
storm[storm$PROPDMGEXP=="7",propcol]<-"0"
storm[storm$PROPDMGEXP=="8",propcol]<-"0"
storm[storm$PROPDMGEXP=="h",propcol]<-"100"
storm[storm$PROPDMGEXP=="H",propcol]<-"100"
storm[storm$PROPDMGEXP=="K",propcol]<-"1000"
storm[storm$PROPDMGEXP=="m",propcol]<-"1000000"
storm[storm$PROPDMGEXP=="M",propcol]<-"1000000"
storm[storm$PROPDMGEXP=="B",propcol]<-"1000000000"
storm[storm$CROPDMGEXP=="",cropcol]<-"0"
storm[storm$CROPDMGEXP=="?",cropcol]<-"0"
storm[storm$CROPDMGEXP=="2",cropcol]<-"0"
storm[storm$CROPDMGEXP=="k",cropcol]<-"1000"
storm[storm$CROPDMGEXP=="K",cropcol]<-"1000"
storm[storm$CROPDMGEXP=="m",cropcol]<-"1000000"
storm[storm$CROPDMGEXP=="M",cropcol]<-"1000000"
storm[storm$CROPDMGEXP=="B",cropcol]<-"1000000000"
##Data Convertion: Property and crop damage units to numeric
storm$PROPDMGEXP<-as.numeric(storm$PROPDMGEXP)
storm$CROPDMGEXP<-as.numeric(storm$CROPDMGEXP)
## multiplying the unit and cost to get the cost in same unit
storm$PROPDMG<-storm$PROPDMG*storm$PROPDMGEXP
storm$CROPDMG<-storm$CROPDMG*storm$CROPDMGEXP
```
 
###subsetting the data for our analysis

```r
storm.h <- subset(storm, select = c(EVTYPE,FATALITIES,INJURIES ))
storm.e <- subset(storm, select = c(EVTYPE,PROPDMG,CROPDMG))
```

##Summarize Data

```r
##Summarizing the fatalities and injuries
fatalities<- tapply(storm.h$FATALITIES, storm.h$EVTYPE, sum)
fatalitiestop10  <- sort(fatalities,decreasing = TRUE)[1:10]
injuries    <- tapply(storm.h$INJURIES, storm.h$EVTYPE, sum)
injuriestop10  <- sort(injuries,decreasing = T)[1:10]
##summarizing the property and crop damage
propdmg   <- tapply(storm.e$PROPDMG,  storm.e$EVTYPE, sum)
propdmgtop10 <- sort(propdmg,decreasing = T)[1:10]
cropdmg   <- tapply(storm.e$CROPDMG,  storm.e$EVTYPE, sum)
cropdmgtop10 <-sort(cropdmg,decreasing = T)[1:10]
```

##Plot Data

```r
##plotting the Ten most severe types of weather events in terms of human health impacts
par(mfrow = c(1,2 ),mar=c(2,5,2,2), oma = c(3,1,3,1))
  barplot(fatalitiestop10, names.arg=names(fatalitiestop10), cex.names=0.5, las=3, col = heat.colors(10), 
        space=0.1,  offset = 0.1, ylab = "number of fatalities")
barplot(injuriestop10, names.arg=names(injuriestop10), cex.names=0.5, las=3, col = heat.colors(10), 
        space=0.1,  offset = 0.1, ylab = "number of injuries" )
title(main = 
      "Ten most severe types of weather events \n in terms of human health impacts",
      outer = TRUE)
```

<img src="noaa_files/figure-html/unnamed-chunk-6-1.png" width="1000px" />


```r
##ploting the Ten most severe types of weather events \n in terms of economic loss
par(mfrow = c(1,2 ), mar = c(7,4,2,0), oma = c(0,0,2,0))
barplot(propdmgtop10, names.arg=names(propdmgtop10), cex.names=0.5, las=3, col = terrain.colors(10), 
        space=0.1,  offset = 0.1, ylab = "property damage in US-dollar")
barplot(cropdmgtop10, names.arg=names(cropdmgtop10), cex.names=0.5, las=3, col = grey.colors(10), 
        space=0.1,  offset = 0.1, ylab = "crop damage in US-dollar" )
title(main = "Ten most severe types of weather events \n in terms of economic loss",
      outer = TRUE)
```

<img src="noaa_files/figure-html/unnamed-chunk-7-1.png" width="1000px" />
# Results and Conclusions

### Answering Question 1
As demonstrated by the Graphs, Tornados causes the greatest number of Fatalities and Injuries. 

Specifically in FATALITIES, after Tornados, EXCESSIVE HEAT, FLASH FLOOD and HEAT are the next ones.

Specifically in INJURIES, after tornados we have TSTM WIND, FLOOD and EXCESSIVE HEAT. 

### Answering Question 2
The impact on property and crop damage is different: 

The largest damage on properties are caused by flood.

But drought has the highest impact on crop.

### Conclusions
Based on evidences demonstrated previously, tornados, floods and droughts have priorities to minimize the impact in human and economic costs of Weather Events. Government and people have to be prepared for each type of events. For safety, it's important to population to know what to do during such events.
