Project Title: Classification of Ocean Microbes in "R"
-----------------------------------------------------

This is a project of Practical Predictive Analytics: Models and Methods by University of Washington (Coursera), working with data from the SeaFlow environmental flow cytometry instrument.A flow cytometer delivers a flow of particles through capilliary. By shining lasers of different wavelengths and measuring the absorption and refraction patterns, you can determine how large the particle is and some information about its color and other properties, allowing you to detect it.The technology was developed for medical applciations, where the particles were potential pathogens in, say, serum, and the goal was to give a diagnosis. But the technology was adapted for use in environmental science to understand microbial population profiles.The SeaFlow instrument, developed by the Armbrust Lab at the University of Washington, is unique in that it is deployed on research vessels and takes continuous measurements of population profiles in the open ocean.

Dataset:
-------

The given dataset represents a 21 minute sample from the vessel in a file seaflow_21min.csv. This sample has been pre-processed to remove the calibration “beads” that are passed through the system for monitoring, as well as some other particle types.

The columns of this dataset are as follows:
file_id, time, cell_id, d1, d2, fsc_small, fsc_perp, fsc_big, pe, chl_small, chl_big, pop

Step 1: Read and Summarize the Data 
-----------------------------------

data <- read.csv("seaflow_21min.csv")
summary(data)

   file_id           time          cell_id            d1       
 Min.   :203.0   Min.   : 12.0   Min.   :    0   Min.   : 1328  
 1st Qu.:204.0   1st Qu.:174.0   1st Qu.: 7486   1st Qu.: 7296  
 Median :206.0   Median :362.0   Median :14995   Median :17728  
 Mean   :206.2   Mean   :341.5   Mean   :15008   Mean   :17039  
 3rd Qu.:208.0   3rd Qu.:503.0   3rd Qu.:22401   3rd Qu.:24512  
 Max.   :209.0   Max.   :643.0   Max.   :32081   Max.   :54048  
       d2          fsc_small        fsc_perp        fsc_big     
 Min.   :   32   Min.   :10005   Min.   :    0   Min.   :32384  
 1st Qu.: 9584   1st Qu.:31341   1st Qu.:13496   1st Qu.:32400  
 Median :18512   Median :35483   Median :18069   Median :32400  
 Mean   :17437   Mean   :34919   Mean   :17646   Mean   :32405  
 3rd Qu.:24656   3rd Qu.:39184   3rd Qu.:22243   3rd Qu.:32416  
 Max.   :54688   Max.   :65424   Max.   :63456   Max.   :32464  
       pe          chl_small        chl_big           pop       
 Min.   :    0   Min.   : 3485   Min.   :    0   crypto :  102  
 1st Qu.: 1635   1st Qu.:22525   1st Qu.: 2800   nano   :12698  
 Median : 2421   Median :30512   Median : 7744   pico   :20860  
 Mean   : 5325   Mean   :30164   Mean   : 8328   synecho:18146  
 3rd Qu.: 5854   3rd Qu.:38299   3rd Qu.:12880   ultra  :20537  
 Max.   :58675   Max.   :64832   Max.   :57184                  


table(data$file_id)


  203   204   205   206   207   208   209 
10454  9435  8376  9215 11444 11995 11424 


hist(data$time)





