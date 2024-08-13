# Regolith Image reanalysis stats

<figure><img src="../.gitbook/assets/P4-08.jpg" alt=""><figcaption><p>Images were quantified by SOAPP. </p></figcaption></figure>

Table of data then analysed using Rtutor.ai&#x20;

&#x20;

First read Read File

```{R,
df <- read.csv("regolith_results_merged.csv")
```

```{R,
df <- params$dfdf2 <- params$df2
```

**Natural language command:** "Use a different color to represent each treatment "group". plot the mean area and use the standard error for each group at each age. Use the x axis as the age. Use the Y axis as the mean area. Draw a line connecting the mean dot for each group. Show all data points as swarms around the mean. Use the standard error to create a grey confidence interval in either side of the line of best fit."

```
library(ggplot2)
library(dplyr)

# Calculate mean, standard error of the mean, and count by group and age
group_stats <- df %>%
  group_by(group, age) %>%
  summarise(mean_area = mean(area),
            se_area = sd(area) / sqrt(n()),
            .groups = 'drop')

# Plot
ggplot(group_stats, aes(x = as.numeric(age), y = mean_area, group = group, color = group)) +
  geom_line() +
  geom_point() +
  geom_errorbar(aes(ymin = mean_area - se_area, ymax = mean_area + se_area), width = 0.2, color = "grey") +
  geom_jitter(data = df, aes(x = as.numeric(age), y = area, color = group), width = 0.1, height = 0, alpha = 0.5) +
  labs(x = "Age", y = "Mean Area") +
  theme_minimal()
```

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>Notice the samples with unknown group "None" </p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>Notice the samples with unknown group "None" </p></figcaption></figure>

### Remove the "None" outliers&#x20;

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>What does Solidity mean? -> less gaps? -> does that mean more or less leaf curling? or more of less leaf etiolation? </p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Noe removed with "Swam of reps"</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Note that the Appolo samples weren't able to recover from the "thinning" stimuli, does that mean that the lunar regoltih makes the plants more sensitive to mechanical stimuli? </p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
library(dplyr)
library(ggplot2)

# Perform ANOVA to test if there are significant differences between groups for each numeric variable
results <- list()
numeric_vars <- c("area", "convex_hull_area", "solidity", "perimeter", "width", "height", "longest_path", 
                  "convex_hull_vertices", "ellipse_major_axis", "ellipse_minor_axis", "ellipse_angle", 
                  "ellipse_eccentricity", "linear_scale", "area_scale")

for (var in numeric_vars) {
  formula <- as.formula(paste(var, "~ group"))
  aov_result <- aov(formula, data = df)
  summary_result <- summary(aov_result)
  results[[var]] <- summary_result
}

# Display the results
results
```

**Results:**

```
$area
             Df    Sum Sq   Mean Sq F value   Pr(>F)    
group         4 7.793e+09 1.948e+09   26.08 7.01e-16 ***
Residuals   130 9.712e+09 7.471e+07                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$convex_hull_area
             Df    Sum Sq   Mean Sq F value Pr(>F)    
group         4 3.474e+10 8.685e+09   34.81 <2e-16 ***
Residuals   130 3.243e+10 2.495e+08                   
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$solidity
             Df Sum Sq Mean Sq F value Pr(>F)  
group         4 0.2192 0.05480   2.603 0.0389 *
Residuals   130 2.7374 0.02106                 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$perimeter
             Df   Sum Sq Mean Sq F value  Pr(>F)    
group         4 16547205 4136801    20.4 4.6e-13 ***
Residuals   130 26360351  202772                    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$width
             Df Sum Sq Mean Sq F value   Pr(>F)    
group         4 453812  113453   21.37 1.46e-13 ***
Residuals   130 690222    5309                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$height
             Df Sum Sq Mean Sq F value   Pr(>F)    
group         4 325199   81300   15.47 2.24e-10 ***
Residuals   130 683195    5255                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$longest_path
             Df   Sum Sq Mean Sq F value   Pr(>F)    
group         4 24395763 6098941   21.75 9.33e-14 ***
Residuals   130 36456962  280438                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$convex_hull_vertices
             Df Sum Sq Mean Sq F value   Pr(>F)    
group         4   2382   595.6   12.93 6.89e-09 ***
Residuals   130   5990    46.1                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$ellipse_major_axis
             Df Sum Sq Mean Sq F value   Pr(>F)    
group         4 391147   97787   21.25 1.68e-13 ***
Residuals   130 598270    4602                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$ellipse_minor_axis
             Df Sum Sq Mean Sq F value   Pr(>F)    
group         4 170834   42709   13.45 3.35e-09 ***
Residuals   130 412786    3175                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

$ellipse_angle
             Df Sum Sq Mean Sq F value Pr(>F)
group         4   9123    2281   0.832  0.507
Residuals   130 356192    2740               

$ellipse_eccentricity
             Df Sum Sq Mean Sq F value Pr(>F)
group         4  0.022 0.00557   0.174  0.951
Residuals   130  4.159 0.03199               

$linear_scale
             Df    Sum Sq   Mean Sq F value Pr(>F)
group         4 3.000e-11 7.410e-12   0.184  0.946
Residuals   130 5.238e-09 4.029e-11               

$area_scale
             Df    Sum Sq   Mean Sq F value Pr(>F)
group         4 1.020e-19 2.552e-20   0.193  0.942
Residuals   130 1.717e-17 1.321e-19   
```





###





<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>



aa



**Catagories of data within the images**

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Frequencies of Categories**&#x20;

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption><p><strong>plot_qq() and plot_histogram() functions in the</strong> <a href="https://cran.r-project.org/web/packages/DataExplorer/vignettes/dataexplorer-intro.html"><strong>DataExplorer</strong></a> <strong>package.</strong></p></figcaption></figure>

**Phenotype correlation analysis**

<figure><img src="../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p><strong>Generated by the corr_plot() functions in the</strong> <a href="https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html"><strong>corrplot</strong></a> <strong>package. Blanks indicate no significant correlations.</strong></p></figcaption></figure>



GG pairs all potential group phenotypes (age coloring)

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



\
GG pairs all potential phenotypes

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption><p><strong>ggpairs() functions in the</strong> <a href="https://cran.r-project.org/web/packages/GGally/index.html"><strong>GGally</strong></a> <strong>package.</strong></p></figcaption></figure>





**Select variables (Age comparison)**

heightellipse\_major\_axisareaconvex\_hull\_areaperimeterwidthlongest\_pathsolidity

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>





**Select variables (Sample name (Regolith type))**

heightellipse\_major\_axisareaconvex\_hull\_areaperimeterwidthlongest\_pathsolidity

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>







### **Data  summary**

```
    samples          sample_name             area       convex_hull_area      solidity         perimeter           width           height       longest_path    center_of_mass     convex_hull_vertices ellipse_center     ellipse_major_axis ellipse_minor_axis ellipse_angle      ellipse_eccentricity  image_name         linear_scale         area_scale        age    
 Length:135         Length:135         Min.   :  108   Min.   :   100.5   Min.   :0.09951   Min.   :  41.46   Min.   : 13.0   Min.   : 11.0   Min.   :  78.0   Length:135         Min.   :10.00        Length:135         Min.   : 12.09     Min.   : 10.11     Min.   :  0.2271   Min.   :0.1828       Length:135         Min.   :2.029e-05   Min.   :4.118e-10   2 :28  
 Class :character   Class :character   1st Qu.: 2112   1st Qu.:  3604.8   1st Qu.:0.60043   1st Qu.: 374.77   1st Qu.: 75.5   1st Qu.: 80.0   1st Qu.: 665.5   Class :character   1st Qu.:21.00        Class :character   1st Qu.: 94.64     1st Qu.: 47.55     1st Qu.: 37.7413   1st Qu.:0.6559       Class :character   1st Qu.:3.569e-05   1st Qu.:1.274e-09   4 :28  
 Mode  :character   Mode  :character   Median : 6959   Median : 10074.5   Median :0.66133   Median : 669.81   Median :122.0   Median :130.0   Median :1196.0   Mode  :character   Median :29.00        Mode  :character   Median :171.34     Median : 72.75     Median : 79.3123   Median :0.7923       Mode  :character   Median :3.633e-05   Median :1.320e-09   6 :28  
                                       Mean   :10579   Mean   : 17378.0   Mean   :0.66270   Mean   : 801.13   Mean   :143.2   Mean   :145.5   Mean   :1182.7                      Mean   :27.84                           Mean   :162.66     Mean   : 98.18     Mean   : 84.0545   Mean   :0.7584                          Mean   :3.318e-05   Mean   :1.140e-09   8 :27  
                                       3rd Qu.:15908   3rd Qu.: 24964.5   3rd Qu.:0.75025   3rd Qu.:1090.77   3rd Qu.:208.5   3rd Qu.:201.5   3rd Qu.:1592.5                      3rd Qu.:34.50                           3rd Qu.:209.46     3rd Qu.:143.65     3rd Qu.:132.6964   3rd Qu.:0.9191                          3rd Qu.:3.653e-05   3rd Qu.:1.335e-09   10:24  
                                       Max.   :69816   Max.   :190914.0   Max.   :1.07463   Max.   :4326.36   Max.   :620.0   Max.   :581.0   Max.   :4783.0                      Max.   :42.00                           Max.   :573.70     Max.   :370.06     Max.   :177.0225   Max.   :0.9781                          Max.   :3.675e-05   Max.   :1.351e-09          
 
```

### **Data structure**&#x20;

```
'data.frame':	135 obs. of  20 variables:
 $ samples             : chr  "plant9" "plant13" "plant18" "plant22" ...
 $ sample_name         : chr  "P1-JSC1A-1" "P1-A11" "P1-JSC1A-2" "P1-A12" ...
 $ area                : int  1575 1063 1863 1133 1750 1400 1404 14149 10296 17976 ...
 $ convex_hull_area    : num  2386 2005 4354 1290 4392 ...
 $ solidity            : num  0.66 0.53 0.428 0.879 0.398 ...
 $ perimeter           : num  421 267 529 163 480 ...
 $ width               : int  58 56 64 52 82 58 50 160 155 186 ...
 $ height              : int  102 61 114 40 91 56 65 275 166 267 ...
 $ longest_path        : int  753 403 756 391 633 440 529 2022 1056 1763 ...
 $ center_of_mass      : chr  "(835.4069841269841,614.4298412698413)" "(2190.9407337723424,620.6124176857949)" "(1145.6543209876543,915.8733225979603)" "(2484.693733451015,950.4333627537511)" ...
 $ convex_hull_vertices: int  23 17 23 19 20 21 20 39 29 35 ...
 $ ellipse_center      : chr  "(835.1513061523438,615.0159912109375)" "(2184.1015625,629.1188354492188)" "(1150.724365234375,933.5895385742188)" "(2484.66455078125,950.39404296875)" ...
 $ ellipse_major_axis  : num  111.6 61.6 99 60.2 90.4 ...
 $ ellipse_minor_axis  : num  23.4 49.4 60 25.9 78.8 ...
 $ ellipse_angle       : num  24.8 28.6 165.9 62.7 173.7 ...
 $ ellipse_eccentricity: num  0.978 0.598 0.795 0.903 0.49 ...
 $ image_name          : chr  "GLDS-476_image-analysis_210502-P1.tiff" "GLDS-476_image-analysis_210502-P1.tiff" "GLDS-476_image-analysis_210502-P1.tiff" "GLDS-476_image-analysis_210502-P1.tiff" ...
 $ linear_scale        : num  3.6e-05 3.6e-05 3.6e-05 3.6e-05 3.6e-05 ...
 $ area_scale          : num  1.3e-09 1.3e-09 1.3e-09 1.3e-09 1.3e-09 ...
 $ age                 : Factor w/ 5 levels "2","4","6","8",..: 1 1 1 1 1 1 1 2 2 2 ...
 
```

**Generated by the** [**summarytools**](https://cran.r-project.org/web/packages/summarytools/vignettes/introduction.html) **package using the command:summarytools::dfSummary(df).**

### **Data frame summary**

```
Data Frame Summary  
Dimensions: 135 x 20  
Duplicates: 0  

----------------------------------------------------------------------------------------------------------------------------
No   Variable               Stats / Values                  Freqs (% of Valid)    Graph                 Valid      Missing  
---- ---------------------- ------------------------------- --------------------- --------------------- ---------- ---------
1    samples                1. plant10                       1 ( 0.7%)                                  135        0        
     [character]            2. plant13                      18 (13.3%)            II                    (100.0%)   (0.0%)   
                            3. plant17                       1 ( 0.7%)                                                      
                            4. plant18                      20 (14.8%)            II                                        
                            5. plant22                      20 (14.8%)            II                                        
                            6. plant25                      19 (14.1%)            II                                        
                            7. plant29                      17 (12.6%)            II                                        
                            8. plant34                      19 (14.1%)            II                                        
                            9. plant9                       20 (14.8%)            II                                        

2    sample_name            1. P1-A12                        6 ( 4.4%)                                  135        0        
     [character]            2. P1-JSC1A-2                    6 ( 4.4%)                                  (100.0%)   (0.0%)   
                            3. P1-JSC1A-3                    6 ( 4.4%)                                                      
                            4. P1-JSC1A-4                    6 ( 4.4%)                                                      
                            5. P1-A11                        5 ( 3.7%)                                                      
                            6. P1-JSC1A-1                    5 ( 3.7%)                                                      
                            7. P2-A11                        5 ( 3.7%)                                                      
                            8. P2-A12                        5 ( 3.7%)                                                      
                            9. P2-A17                        5 ( 3.7%)                                                      
                            10. P2-JSC1A-1                   5 ( 3.7%)                                                      
                            [ 19 others ]                   81 (60.0%)            IIIIIIIIIIII                              

3    area                   Mean (sd) : 10579.1 (11429.5)   134 distinct values   :                     135        0        
     [integer]              min < med < max:                                      :                     (100.0%)   (0.0%)   
                            108 < 6959 < 69816                                    :                                         
                            IQR (CV) : 13797 (1.1)                                : :                                       
                                                                                  : : . .                                   

4    convex_hull_area       Mean (sd) : 17378 (22389.7)     134 distinct values   :                     135        0        
     [numeric]              min < med < max:                                      :                     (100.0%)   (0.0%)   
                            100.5 < 10074.5 < 190914                              :                                         
                            IQR (CV) : 21359.8 (1.3)                              : .                                       
                                                                                  : : .                                     

5    solidity               Mean (sd) : 0.7 (0.1)           134 distinct values             : :         135        0        
     [numeric]              min < med < max:                                                : :         (100.0%)   (0.0%)   
                            0.1 < 0.7 < 1.1                                                 : :                             
                            IQR (CV) : 0.1 (0.2)                                          . : : .                           
                                                                                      . : : : : :   .                       

6    perimeter              Mean (sd) : 801.1 (565.9)       134 distinct values   : :                   135        0        
     [numeric]              min < med < max:                                      : :                   (100.0%)   (0.0%)   
                            41.5 < 669.8 < 4326.4                                 : : :                                     
                            IQR (CV) : 716 (0.7)                                  : : :                                     
                                                                                  : : : : .                                 

7    width                  Mean (sd) : 143.2 (92.4)        110 distinct values     :                   135        0        
     [integer]              min < med < max:                                      : :                   (100.0%)   (0.0%)   
                            13 < 122 < 620                                        : : : :                                   
                            IQR (CV) : 133 (0.6)                                  : : : :                                   
                                                                                  : : : : :   .                             

8    height                 Mean (sd) : 145.5 (86.7)        99 distinct values      :                   135        0        
     [integer]              min < med < max:                                      : : :                 (100.0%)   (0.0%)   
                            11 < 130 < 581                                        : : : :                                   
                            IQR (CV) : 121.5 (0.6)                                : : : : :                                 
                                                                                  : : : : : .                               

9    longest_path           Mean (sd) : 1182.7 (673.9)      127 distinct values     : :                 135        0        
     [integer]              min < med < max:                                        : : :               (100.0%)   (0.0%)   
                            78 < 1196 < 4783                                      : : : :                                   
                            IQR (CV) : 927 (0.6)                                  : : : : .                                 
                                                                                  : : : : : .                               

10   center_of_mass         1. (883.3741528703301,492.22      2 ( 1.5%)                                 135        0        
     [character]            2. (1031.1911882663,1165.300      1 ( 0.7%)                                 (100.0%)   (0.0%)   
                            3. (1039.5303664921466,1590.      1 ( 0.7%)                                                     
                            4. (1064.0066869300913,888.2      1 ( 0.7%)                                                     
                            5. (1070.120302528953,1578.3      1 ( 0.7%)                                                     
                            6. (1073.5754716981132,879.7      1 ( 0.7%)                                                     
                            7. (1101.8269024011468,1723.      1 ( 0.7%)                                                     
                            8. (1122.8067928730513,1643.      1 ( 0.7%)                                                     
                            9. (1136.7397725940687,997.1      1 ( 0.7%)                                                     
                            10. (1136.8004977600797,1731.     1 ( 0.7%)                                                     
                            [ 124 others ]                  124 (91.9%)           IIIIIIIIIIIIIIIIII                        

11   convex_hull_vertices   Mean (sd) : 27.8 (7.9)          31 distinct values            :             135        0        
     [integer]              min < med < max:                                        : : . : .           (100.0%)   (0.0%)   
                            10 < 29 < 42                                            : : : : :                               
                            IQR (CV) : 13.5 (0.3)                                   : : : : :                               
                                                                                  : : : : : : .                             

12   ellipse_center         1. (874.7323608398438,495.27      2 ( 1.5%)                                 135        0        
     [character]            2. (1036.8955078125,1586.932      1 ( 0.7%)                                 (100.0%)   (0.0%)   
                            3. (1038.439208984375,1185.4      1 ( 0.7%)                                                     
                            4. (1063.705322265625,888.40      1 ( 0.7%)                                                     
                            5. (1071.85595703125,1578.89      1 ( 0.7%)                                                     
                            6. (1075.643310546875,878.42      1 ( 0.7%)                                                     
                            7. (1102.4412841796875,1723.      1 ( 0.7%)                                                     
                            8. (1121.7054443359375,1640.      1 ( 0.7%)                                                     
                            9. (1137.292724609375,997.11      1 ( 0.7%)                                                     
                            10. (1138.8546142578125,930.2     1 ( 0.7%)                                                     
                            [ 124 others ]                  124 (91.9%)           IIIIIIIIIIIIIIIIII                        

13   ellipse_major_axis     Mean (sd) : 162.7 (85.9)        134 distinct values     .   :               135        0        
     [numeric]              min < med < max:                                        :   :               (100.0%)   (0.0%)   
                            12.1 < 171.3 < 573.7                                    : : :                                   
                            IQR (CV) : 114.8 (0.5)                                : : : : :                                 
                                                                                  : : : : : . .                             

14   ellipse_minor_axis     Mean (sd) : 98.2 (66)           134 distinct values     :                   135        0        
     [numeric]              min < med < max:                                      : :                   (100.0%)   (0.0%)   
                            10.1 < 72.8 < 370.1                                   : : .                                     
                            IQR (CV) : 96.1 (0.7)                                 : : : :                                   
                                                                                  : : : : :                                 

15   ellipse_angle          Mean (sd) : 84.1 (52.2)         134 distinct values     :                   135        0        
     [numeric]              min < med < max:                                        : : . .     .       (100.0%)   (0.0%)   
                            0.2 < 79.3 < 177                                      : : : : : . : : .                         
                            IQR (CV) : 95 (0.6)                                   : : : : : : : : :                         
                                                                                  : : : : : : : : :                         

16   ellipse_eccentricity   Mean (sd) : 0.8 (0.2)           134 distinct values                   :     135        0        
     [numeric]              min < med < max:                                                  . . :     (100.0%)   (0.0%)   
                            0.2 < 0.8 < 1                                                   : : : :                         
                            IQR (CV) : 0.3 (0.2)                                          . : : : :                         
                                                                                    . . . : : : : :                         

17   image_name             1. GLDS-476_image-analysis_2     7 ( 5.2%)            I                     135        0        
     [character]            2. GLDS-476_image-analysis_2     7 ( 5.2%)            I                     (100.0%)   (0.0%)   
                            3. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            4. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            5. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            6. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            7. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            8. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            9. GLDS-476_image-analysis_2     7 ( 5.2%)            I                                         
                            10. GLDS-476_image-analysis_2    7 ( 5.2%)            I                                         
                            [ 10 others ]                   65 (48.1%)            IIIIIIIII                                 

18   linear_scale           Mean (sd) : 0 (0)               19 distinct values                    :     135        0        
     [numeric]              min < med < max:                                                      :     (100.0%)   (0.0%)   
                            0 < 0 < 0                                                             :                         
                            IQR (CV) : 0 (0.2)                                                    :                         
                                                                                  : .           . :                         

19   area_scale             Mean (sd) : 0 (0)               19 distinct values                      :   135        0        
     [numeric]              min < med < max:                                                        :   (100.0%)   (0.0%)   
                            0 < 0 < 0                                                               :                       
                            IQR (CV) : 0 (0.3)                                                      :                       
                                                                                  : .               :                       

20   age                    1. 2                            28 (20.7%)            IIII                  135        0        
     [factor]               2. 4                            28 (20.7%)            IIII                  (100.0%)   (0.0%)   
                            3. 6                            28 (20.7%)            IIII                                      
                            4. 8                            27 (20.0%)            IIII                                      
                            5. 10                           24 (17.8%)            III                                       
----------------------------------------------------------------------------------------------------------------------------
```



**Sample category for strata (sample name -> aka regolith type)**

```
                                          Stratified by sample_name
                                            Other               P1-A11            P1-A12            P1-JSC1A-1          P1-JSC1A-2          P1-JSC1A-3          P1-JSC1A-4          P2-A11             P2-A12            P2-A17             P2-JSC1A-1          P2-JSC1A-2          P2-JSC1A-3          p      test
  n                                               71                  5                 6                  5                   6                   6                   6                  5                  5                  5                  5                   5                   5                       
  samples (%)                                                                                                                                                                                                                                                                                           <0.001     
     plant10                                       1 ( 1.4)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant13                                       8 (11.3)           5 (100.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          5 (100.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant17                                       1 ( 1.4)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant18                                       9 (12.7)           0 (  0.0)         0 (  0.0)          0 (  0.0)           6 (100.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           5 (100.0)           0 (  0.0)               
     plant22                                       9 (12.7)           0 (  0.0)         6 (100.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          5 (100.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant25                                       8 (11.3)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           6 (100.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           5 (100.0)               
     plant29                                      12 (16.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          5 (100.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant34                                      13 (18.3)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           6 (100.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     plant9                                       10 (14.1)           0 (  0.0)         0 (  0.0)          5 (100.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          5 (100.0)           0 (  0.0)           0 (  0.0)               
  area (mean (SD))                          11067.49 (13241.16) 2717.00 (4275.45) 2563.67 (2758.81) 13920.60 (11225.40) 15461.83 (10368.32) 17443.67 (11468.85) 13161.67 (10268.73) 5330.00 (6301.12)  6145.40 (5883.05)  7002.60 (4588.05) 12411.60 (9716.11)  10643.40 (8234.22)  11948.40 (8729.21)   0.459     
  convex_hull_area (mean (SD))              18561.08 (27363.81) 4347.30 (6924.79) 3609.75 (3534.09) 21243.40 (19156.84) 25090.17 (17162.64) 28690.58 (19374.56) 20012.92 (17063.78) 9190.90 (11870.50) 9958.00 (9032.64) 10887.20 (7807.61) 22299.70 (17388.22) 17217.30 (13950.02) 17609.70 (14496.61)  0.758     
  solidity (mean (SD))                          0.67 (0.16)        0.76 (0.23)       0.68 (0.13)        0.69 (0.08)         0.61 (0.10)         0.59 (0.11)         0.70 (0.08)        0.63 (0.14)        0.60 (0.09)        0.66 (0.08)        0.55 (0.18)         0.63 (0.17)         0.72 (0.06)      0.630     
  perimeter (mean (SD))                       826.08 (659.42)    296.04 (269.86)   344.64 (132.71)    899.50 (477.00)    1058.05 (448.83)    1154.85 (485.31)     853.49 (432.21)    594.66 (417.95)    703.56 (186.83)    688.23 (305.67)    948.57 (475.70)     867.10 (480.28)     809.25 (442.58)    0.355     
  width (mean (SD))                           146.03 (104.68)     60.80 (55.76)     63.50 (37.91)     154.60 (91.00)      163.50 (74.25)      202.67 (67.22)      156.83 (79.32)     109.40 (94.72)     133.20 (60.99)     108.60 (55.07)     189.40 (75.86)      149.60 (57.09)      184.40 (59.15)     0.227     
  height (mean (SD))                          147.65 (94.65)      70.00 (60.47)     78.67 (35.32)     203.80 (69.78)      203.00 (64.47)      195.17 (96.26)      168.50 (77.70)     104.60 (38.12)      95.60 (32.11)     138.40 (52.45)     161.00 (90.09)      163.00 (96.16)      120.20 (62.81)     0.099     
  longest_path (mean (SD))                   1188.96 (763.93)    478.20 (386.45)   584.83 (248.76)   1544.60 (581.30)    1499.67 (514.97)    1544.50 (584.78)    1315.33 (512.22)    915.60 (524.31)    943.60 (388.18)   1035.60 (329.27)   1516.80 (501.48)    1305.60 (535.17)    1376.00 (435.63)    0.095     
  convex_hull_vertices (mean (SD))             27.38 (8.02)       19.60 (6.15)      22.00 (6.45)       33.20 (6.34)        32.33 (6.62)        32.83 (7.52)        32.00 (6.84)       25.00 (6.04)       24.20 (7.09)       26.80 (4.76)       31.60 (9.24)        28.00 (8.66)        31.40 (7.23)      0.039     
  ellipse_major_axis (mean (SD))              162.84 (97.17)      71.57 (59.96)     89.23 (40.87)     221.46 (81.66)      200.36 (61.81)      206.18 (69.73)      184.24 (68.62)     131.73 (66.14)     133.03 (54.02)     144.15 (40.65)     201.86 (58.10)      173.34 (56.93)      186.32 (59.33)     0.095     
  ellipse_minor_axis (mean (SD))               99.14 (69.58)      52.05 (54.35)     43.88 (20.51)     100.88 (78.65)      134.55 (66.46)      148.85 (62.27)      112.52 (72.93)      68.68 (47.49)      80.18 (34.68)      88.36 (49.04)     124.38 (70.93)      102.08 (57.05)       98.57 (67.95)     0.270     
  ellipse_angle (mean (SD))                    87.37 (50.04)      72.87 (55.51)     48.06 (50.80)      39.46 (35.36)      110.53 (64.15)      121.20 (47.91)       91.98 (66.94)      64.82 (58.23)      89.12 (21.07)     124.59 (65.72)      57.79 (25.43)       45.98 (49.59)       88.08 (42.87)     0.069     
  ellipse_eccentricity (mean (SD))              0.76 (0.17)        0.62 (0.19)       0.84 (0.13)        0.85 (0.17)         0.72 (0.17)         0.65 (0.16)         0.76 (0.24)        0.85 (0.09)        0.78 (0.11)        0.72 (0.22)        0.74 (0.23)         0.81 (0.12)         0.79 (0.28)      0.585     
  image_name (%)                                                                                                                                                                                                                                                                                         0.789     
     GLDS-476_image-analysis_210502-P1.tiff        1 ( 1.4)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210502-P2.tiff        1 ( 1.4)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     GLDS-476_image-analysis_210502-P3.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210502-P4.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P1.tiff        1 ( 1.4)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P2.tiff        1 ( 1.4)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     GLDS-476_image-analysis_210504-P3.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P4.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P1.tiff        1 ( 1.4)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P2.tiff        1 ( 1.4)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     GLDS-476_image-analysis_210506-P3.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P4.tiff        7 ( 9.9)           0 (  0.0)         0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)           0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)          0 (  0.0)           0 (  0.0)           0 (  0.0)               
     Other                                        23 (32.4)           2 ( 40.0)         3 ( 50.0)          2 ( 40.0)           3 ( 50.0)           3 ( 50.0)           3 ( 50.0)          2 ( 40.0)          2 ( 40.0)          2 ( 40.0)          2 ( 40.0)           2 ( 40.0)           2 ( 40.0)               
  linear_scale (mean (SD))                      0.00 (0.00)        0.00 (0.00)       0.00 (0.00)        0.00 (0.00)         0.00 (0.00)         0.00 (0.00)         0.00 (0.00)        0.00 (0.00)        0.00 (0.00)        0.00 (0.00)        0.00 (0.00)         0.00 (0.00)         0.00 (0.00)      1.000     
  area_scale (mean (SD))                        0.00 (0.00)        0.00 (0.00)       0.00 (0.00)        0.00 (0.00)         0.00 (0.00)         0.00 (0.00)         0.00 (0.00)        0.00 (0.00)        0.00 (0.00)        0.00 (0.00)        0.00 (0.00)         0.00 (0.00)         0.00 (0.00)      1.000     
  age (%)                                                                                                                                                                                                                                                                                                1.000     
     2                                            16 (22.5)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     4                                            16 (22.5)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     6                                            16 (22.5)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     8                                            15 (21.1)           1 ( 20.0)         1 ( 16.7)          1 ( 20.0)           1 ( 16.7)           1 ( 16.7)           1 ( 16.7)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)               
     10                                            8 (11.3)           1 ( 20.0)         2 ( 33.3)          1 ( 20.0)           2 ( 33.3)           2 ( 33.3)           2 ( 33.3)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)          1 ( 20.0)           1 ( 20.0)           1 ( 20.0)         
```

**Generated by the CreateTableOne() function in the** [**tableone**](https://cran.r-project.org/web/packages/tableone/vignettes/introduction.html) **package.**



**Sample category for strata (sample age)**

```
                                           Stratified by age
                                            2                 4                  6                 8                   10                  p      test
  n                                              28                 28                28                 27                  24                       
  samples (%)                                                                                                                               1.000     
     plant10                                      0 ( 0.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           1 (  4.2)               
     plant13                                      4 (14.3)           4 (14.3)          4 (14.3)           3 ( 11.1)           3 ( 12.5)               
     plant17                                      0 ( 0.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           1 (  4.2)               
     plant18                                      4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           4 ( 16.7)               
     plant22                                      4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           4 ( 16.7)               
     plant25                                      4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           3 ( 12.5)               
     plant29                                      4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           1 (  4.2)               
     plant34                                      4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           3 ( 12.5)               
     plant9                                       4 (14.3)           4 (14.3)          4 (14.3)           4 ( 14.8)           4 ( 16.7)               
  sample_name (%)                                                                                                                           1.000     
     Other                                       16 (57.1)          16 (57.1)         16 (57.1)          15 ( 55.6)           8 ( 33.3)               
     P1-A11                                       1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P1-A12                                       1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           2 (  8.3)               
     P1-JSC1A-1                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P1-JSC1A-2                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           2 (  8.3)               
     P1-JSC1A-3                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           2 (  8.3)               
     P1-JSC1A-4                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           2 (  8.3)               
     P2-A11                                       1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P2-A12                                       1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P2-A17                                       1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P2-JSC1A-1                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P2-JSC1A-2                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
     P2-JSC1A-3                                   1 ( 3.6)           1 ( 3.6)          1 ( 3.6)           1 (  3.7)           1 (  4.2)               
  area (mean (SD))                          1973.79 (910.59)  14321.43 (5319.05) 4999.46 (3520.89) 10018.81 (7328.74)  23392.21 (18298.91) <0.001     
  convex_hull_area (mean (SD))              3330.43 (1878.61) 22188.89 (8901.96) 7479.41 (5513.07) 15842.09 (11646.98) 41430.15 (40500.47) <0.001     
  solidity (mean (SD))                         0.65 (0.16)        0.67 (0.10)       0.71 (0.16)        0.65 (0.17)         0.63 (0.14)      0.380     
  perimeter (mean (SD))                      379.24 (161.22)   1027.22 (374.01)   538.43 (293.66)    814.13 (364.11)    1321.41 (879.85)   <0.001     
  width (mean (SD))                           73.14 (20.05)     186.75 (47.01)     99.68 (48.72)     141.70 (69.88)      226.79 (143.12)   <0.001     
  height (mean (SD))                          71.71 (24.31)     187.25 (53.66)    110.61 (57.47)     150.96 (64.22)      217.25 (124.87)   <0.001     
  longest_path (mean (SD))                   590.82 (170.97)   1495.21 (345.45)   932.79 (398.35)   1222.41 (468.86)    1755.42 (1034.92)  <0.001     
  convex_hull_vertices (mean (SD))            19.79 (2.36)       34.25 (3.47)      25.82 (6.86)       28.07 (6.31)        31.83 (9.61)     <0.001     
  ellipse_major_axis (mean (SD))              82.13 (19.93)     208.83 (43.95)    142.03 (59.74)     161.00 (55.89)      228.68 (130.40)   <0.001     
  ellipse_minor_axis (mean (SD))              49.82 (20.40)     128.84 (40.20)     57.00 (28.81)     102.87 (55.85)      161.59 (90.61)    <0.001     
  ellipse_angle (mean (SD))                   89.92 (46.46)      87.96 (50.39)     73.06 (53.27)      73.28 (54.30)       97.61 (56.29)     0.343     
  ellipse_eccentricity (mean (SD))             0.76 (0.15)        0.74 (0.18)       0.88 (0.13)        0.72 (0.21)         0.68 (0.15)     <0.001     
  image_name (%)                                                                                                                           <0.001     
     GLDS-476_image-analysis_210502-P1.tiff       7 (25.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210502-P2.tiff       7 (25.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210502-P3.tiff       7 (25.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210502-P4.tiff       7 (25.0)           0 ( 0.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P1.tiff       0 ( 0.0)           7 (25.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P2.tiff       0 ( 0.0)           7 (25.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P3.tiff       0 ( 0.0)           7 (25.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210504-P4.tiff       0 ( 0.0)           7 (25.0)          0 ( 0.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P1.tiff       0 ( 0.0)           0 ( 0.0)          7 (25.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P2.tiff       0 ( 0.0)           0 ( 0.0)          7 (25.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P3.tiff       0 ( 0.0)           0 ( 0.0)          7 (25.0)           0 (  0.0)           0 (  0.0)               
     GLDS-476_image-analysis_210506-P4.tiff       0 ( 0.0)           0 ( 0.0)          7 (25.0)           0 (  0.0)           0 (  0.0)               
     Other                                        0 ( 0.0)           0 ( 0.0)          0 ( 0.0)          27 (100.0)          24 (100.0)               
  linear_scale (mean (SD))                     0.00 (0.00)        0.00 (0.00)       0.00 (0.00)        0.00 (0.00)         0.00 (0.00)     <0.001     
  area_scale (mean (SD))                       0.00 (0.00)        0.00 (0.00)       0.00 (0.00)        0.00 (0.00)         0.00 (0.00)     <0.001     
```



**Sample category for strata (Sample catagory)**

```
                                         Stratified by group
                                            A11               A12               A17               JSC1A               None                 p      test
  n                                              18                20                17                 78                    2                       
  samples (%)                                                                                                                              <0.001     
     plant10                                      0 (  0.0)         0 (  0.0)         0 (  0.0)          0 ( 0.0)             1 ( 50.0)               
     plant13                                     18 (100.0)         0 (  0.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     plant17                                      0 (  0.0)         0 (  0.0)         0 (  0.0)          0 ( 0.0)             1 ( 50.0)               
     plant18                                      0 (  0.0)         0 (  0.0)         0 (  0.0)         20 (25.6)             0 (  0.0)               
     plant22                                      0 (  0.0)        20 (100.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     plant25                                      0 (  0.0)         0 (  0.0)         0 (  0.0)         19 (24.4)             0 (  0.0)               
     plant29                                      0 (  0.0)         0 (  0.0)        17 (100.0)          0 ( 0.0)             0 (  0.0)               
     plant34                                      0 (  0.0)         0 (  0.0)         0 (  0.0)         19 (24.4)             0 (  0.0)               
     plant9                                       0 (  0.0)         0 (  0.0)         0 (  0.0)         20 (25.6)             0 (  0.0)               
  sample_name (%)                                                                                                                          <0.001     
     Other                                        8 ( 44.4)         9 ( 45.0)        12 ( 70.6)         40 (51.3)             2 (100.0)               
     P1-A11                                       5 ( 27.8)         0 (  0.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     P1-A12                                       0 (  0.0)         6 ( 30.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     P1-JSC1A-1                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          5 ( 6.4)             0 (  0.0)               
     P1-JSC1A-2                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          6 ( 7.7)             0 (  0.0)               
     P1-JSC1A-3                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          6 ( 7.7)             0 (  0.0)               
     P1-JSC1A-4                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          6 ( 7.7)             0 (  0.0)               
     P2-A11                                       5 ( 27.8)         0 (  0.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     P2-A12                                       0 (  0.0)         5 ( 25.0)         0 (  0.0)          0 ( 0.0)             0 (  0.0)               
     P2-A17                                       0 (  0.0)         0 (  0.0)         5 ( 29.4)          0 ( 0.0)             0 (  0.0)               
     P2-JSC1A-1                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          5 ( 6.4)             0 (  0.0)               
     P2-JSC1A-2                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          5 ( 6.4)             0 (  0.0)               
     P2-JSC1A-3                                   0 (  0.0)         0 (  0.0)         0 (  0.0)          5 ( 6.4)             0 (  0.0)               
  area (mean (SD))                          3253.22 (4425.22) 5383.40 (5655.33) 5277.47 (4792.69) 13454.36 (10358.54)  61394.50 (11909.80) <0.001     
  convex_hull_area (mean (SD))              5368.11 (7620.95) 9060.02 (9933.66) 9178.09 (7767.97) 21003.60 (17200.10) 136945.00 (76323.69) <0.001     
  solidity (mean (SD))                         0.73 (0.23)       0.65 (0.18)       0.59 (0.15)        0.67 (0.10)          0.50 (0.19)      0.039     
  perimeter (mean (SD))                      396.76 (330.63)   635.52 (455.56)   663.87 (384.76)    904.27 (453.47)     3240.94 (1535.01)  <0.001     
  width (mean (SD))                           78.67 (68.33)    107.40 (63.22)    100.29 (48.81)     167.14 (78.54)       515.50 (147.79)   <0.001     
  height (mean (SD))                          80.33 (52.33)     99.05 (54.95)    132.47 (72.61)     167.90 (76.46)       431.50 (211.42)   <0.001     
  longest_path (mean (SD))                   659.06 (495.46)   841.70 (457.61)   975.35 (465.42)   1369.04 (545.40)     3799.50 (1390.88)  <0.001     
  convex_hull_vertices (mean (SD))            21.50 (7.15)      22.95 (6.25)      24.35 (5.38)       31.09 (7.13)         36.50 (2.12)     <0.001     
  ellipse_major_axis (mean (SD))              94.11 (67.05)    117.97 (59.71)    137.25 (61.62)     187.19 (70.05)       485.70 (124.45)   <0.001     
  ellipse_minor_axis (mean (SD))              51.26 (41.40)     72.46 (45.38)     75.93 (40.62)     115.09 (63.47)       307.04 (89.13)    <0.001     
  ellipse_angle (mean (SD))                   86.78 (55.06)     79.82 (41.58)    104.71 (59.98)      80.31 (52.62)        72.12 (32.09)     0.507     
  ellipse_eccentricity (mean (SD))             0.76 (0.16)       0.76 (0.16)       0.79 (0.16)        0.75 (0.19)          0.78 (0.02)      0.951     
  image_name (%)                                                                                                                            1.000     
     GLDS-476_image-analysis_210502-P1.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210502-P2.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210502-P3.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210502-P4.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210504-P1.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210504-P2.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210504-P3.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210504-P4.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210506-P1.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210506-P2.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210506-P3.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     GLDS-476_image-analysis_210506-P4.tiff       1 (  5.6)         1 (  5.0)         1 (  5.9)          4 ( 5.1)             0 (  0.0)               
     Other                                        6 ( 33.3)         8 ( 40.0)         5 ( 29.4)         30 (38.5)             2 (100.0)               
  linear_scale (mean (SD))                     0.00 (0.00)       0.00 (0.00)       0.00 (0.00)        0.00 (0.00)          0.00 (0.00)      0.946     
  area_scale (mean (SD))                       0.00 (0.00)       0.00 (0.00)       0.00 (0.00)        0.00 (0.00)          0.00 (0.00)      0.942     
  age (%)                                                                                                                                   0.800     
     2                                            4 ( 22.2)         4 ( 20.0)         4 ( 23.5)         16 (20.5)             0 (  0.0)               
     4                                            4 ( 22.2)         4 ( 20.0)         4 ( 23.5)         16 (20.5)             0 (  0.0)               
     6                                            4 ( 22.2)         4 ( 20.0)         4 ( 23.5)         16 (20.5)             0 (  0.0)               
     8                                            3 ( 16.7)         4 ( 20.0)         4 ( 23.5)         16 (20.5)             0 (  0.0)               
     10                                           3 ( 16.7)         4 ( 20.0)         1 (  5.9)         14 (17.9)             2 (100.0)               
```



