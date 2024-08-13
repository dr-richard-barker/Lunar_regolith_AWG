# Regolith - Exploratory data analysis, GPT4 automated

* [1 Data pre-process](broken-reference)
* [2 Basic summary](broken-reference)
* [3 Missing values](broken-reference)
  * [3.1 Outliers](broken-reference)
* [4 Univariate distributions: Numeric](broken-reference)
* [5 Univariate distribution: Categorical variables](broken-reference)
* [6 Overview heatmap](broken-reference)
* [7 Bivariate correlation](broken-reference)
  * [7.1 Correlation matrix (Blank indicates not significant, P > 0.05)](broken-reference)
  * [7.2 Correlation between numeric variables](broken-reference)
  * [7.3 Correlation between a numeric and a categorical variable](broken-reference)
  * [7.4 Correlation between two categorical variables](broken-reference)

### Data pre-process

If a numeric variable has just a few unique values, it might make more sense to convert it into categorical variable. If the total number of unique values is less than 5% of the total rows, convert.

```
# Iterate over each column of the data frame
for (i in seq_along(df)) {
  # Check if the column is numeric and has less than 5 unique values
  if (is.numeric(df[[i]]) && length(unique(df[[i]])) / nrow(df) < 0.05 && length(unique(df[[i]])) < 13) {
    # Convert the column to a factor
    df[[i]] <- as.factor(df[[i]])
    cat("\nColumn ", colnames(df)[i], "was converted to a factor.")
  }
}
```

If a non-numeric variable has too many unique values, it might be names or IDs that is not useful in analysis.

```
cutoff <- 0.8
# Initialize a vector to store the names of columns to be removed
cols_to_remove <- c()

# Loop through each column
for (col_name in names(df)) {
  # Check if column is non-numeric and has unique values > 80% of total rows
  if (!is.numeric(df[[col_name]]) && length(unique(df[[col_name]])) > cutoff * nrow(df)) {
    cols_to_remove <- c(cols_to_remove, col_name)
    cat("\nColumn", cols_to_remove, " was excluded from analysis.")
  }
}

# Remove the identified columns
df <- df %>% select(-one_of(cols_to_remove))
```

If target variable is specified, bin this variable into another variable called target\_bin.

```
target_bin <- NULL
# Check if the target_var is in the dataframe and is not NA
if (!is.null(target_var)) {
  if (target_var %in% names(df)) {
    # Check if the target variable is numerical
    if (is.numeric(df[[target_var]])) {
      # Create bins for the numerical target variable
      target_bin <- cut(
        df[[target_var]],
        breaks = 5,
        labels = c("low", "low_mid", "mid", "upper_mid", "high"),
        include.lowest = TRUE
      )
      print(paste("Binned target variable", target_var, " as", "target_bin"))
    } else {
      print(paste("The target variable, ", target_var, ", is a categorical variable."))
    }
  } else {
    print("Selected variable is not part of the data. Proceeding with general EDA.")
    target_var <- NULL   
  }
} else {
  print("No valid target variable selected. Proceeding with general EDA.")
  target_var <- NULL 
}
```

```
## [1] "The target variable,  sample_name , is a categorical variable."
```

### Basic summary

```
str(df)
```

```
## 'data.frame':    135 obs. of  10 variables:
##  $ sample_name         : chr  "P1-JSC1A-1" "P1-A11" "P1-JSC1A-2" "P1-A12" ...
##  $ area                : int  1575 1063 1863 1133 1750 1400 1404 14149 10296 17976 ...
##  $ convex_hull_area    : num  2386 2005 4354 1290 4392 ...
##  $ solidity            : num  0.66 0.53 0.428 0.879 0.398 ...
##  $ perimeter           : num  421 267 529 163 480 ...
##  $ width               : int  58 56 64 52 82 58 50 160 155 186 ...
##  $ height              : int  102 61 114 40 91 56 65 275 166 267 ...
##  $ longest_path        : int  753 403 756 391 633 440 529 2022 1056 1763 ...
##  $ convex_hull_vertices: int  23 17 23 19 20 21 20 39 29 35 ...
##  $ age                 : Factor w/ 5 levels "2","4","6","8",..: 1 1 1 1 1 1 1 2 2 2 ...
```

```
summary(df)
```

```
##  sample_name             area       convex_hull_area      solidity         perimeter           width           height       longest_path    convex_hull_vertices age    
##  Length:135         Min.   :  108   Min.   :   100.5   Min.   :0.09951   Min.   :  41.46   Min.   : 13.0   Min.   : 11.0   Min.   :  78.0   Min.   :10.00        2 :28  
##  Class :character   1st Qu.: 2112   1st Qu.:  3604.8   1st Qu.:0.60043   1st Qu.: 374.77   1st Qu.: 75.5   1st Qu.: 80.0   1st Qu.: 665.5   1st Qu.:21.00        4 :28  
##  Mode  :character   Median : 6959   Median : 10074.5   Median :0.66133   Median : 669.81   Median :122.0   Median :130.0   Median :1196.0   Median :29.00        6 :28  
##                     Mean   :10579   Mean   : 17378.0   Mean   :0.66270   Mean   : 801.13   Mean   :143.2   Mean   :145.5   Mean   :1182.7   Mean   :27.84        8 :27  
##                     3rd Qu.:15908   3rd Qu.: 24964.5   3rd Qu.:0.75025   3rd Qu.:1090.77   3rd Qu.:208.5   3rd Qu.:201.5   3rd Qu.:1592.5   3rd Qu.:34.50        10:24  
##                     Max.   :69816   Max.   :190914.0   Max.   :1.07463   Max.   :4326.36   Max.   :620.0   Max.   :581.0   Max.   :4783.0   Max.   :42.00
```

### Missing values

```
# Calculate the total number of missing values per column
missing_values <- sapply(df, function(x) sum(is.na(x)))

# Calculate the number of cases with at least one missing value
cases_with_missing <- sum(apply(df, 1, function(x) any(is.na(x))))

# Check if there are any missing values
if (all(missing_values == 0)) {
  print("There is no missing data in any columns.")
} else {
  # Create a data frame for plotting
  missing_data_df <- data.frame(
    Column = c(names(missing_values), "At Least One Missing"),
    MissingValues = c(missing_values, cases_with_missing)
  )
  # Calculate the percentage of missing values per column
  # missing_percentage <- (missing_values / nrow(df)) * 100
  # Plot the number of missing values for all columns with labels
  ggplot(missing_data_df, aes(x = Column, y = MissingValues, fill = Column)) +
    geom_bar(stat = "identity") +
    geom_text(aes(label = sprintf("%.0f%%", MissingValues / nrow(df) * 100)), hjust = -0.3) + # Add labels to the bars
    # geom_text(aes(label = sprintf("%.2f%%", MissingPercentage)), hjust = -0.3) +
    coord_flip() + # Makes the bars horizontal
    labs(title = "Number of Missing Values by Column", x = "Column", y = "Number of Missing Values") +
    scale_fill_brewer(palette = "Set3") + # Use a color palette for different bars
    theme(legend.position = "none", axis.title.y = element_blank()) + # Remove the legend
    scale_y_continuous(expand = expansion(mult = c(0, 0.2))) # Extend the y-axis limits by 10%
}
```

```
## [1] "There is no missing data in any columns."
```

### Outliers

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

```
detect_outliers_mad <- function(df, accuracy = 0.99) {
  # Function to calculate MAD
  mad_function <- function(x) {
    median(abs(x - median(x)))
  }

  # Calculate z-score equivalent for the given accuracy
  z_threshold <- qnorm(accuracy + (1 - accuracy) / 2)

  # Calculate MAD threshold
  mad_threshold <- z_threshold

  # Initialize a list to store outlier indices for each numeric column
  outliers_list <- list()

  # Initialize a vector to keep track of rows with outliers
  rows_with_outliers <- rep(FALSE, nrow(df))

  # Loop through each column in the dataframe
  for (col_name in names(df)) {
    # Check if the column is numeric
    if (is.numeric(df[[col_name]])) {
      # Calculate MAD and median for the column
      mad_value <- mad_function(df[[col_name]])
      median_value <- median(df[[col_name]])

      # Calculate the deviation scores (using a modified z-score)
      deviation_scores <- 0.6745 * (df[[col_name]] - median_value) / mad_value

      # Identify indices of outliers
      outlier_indices <- which(abs(deviation_scores) > mad_threshold)

      # Store the indices in the list
      outliers_list[[col_name]] <- outlier_indices

      # Update rows with outliers
      rows_with_outliers[outlier_indices] <- TRUE
    }
  }

  # Calculate the number of outliers in each column
  num_outliers_each_col <- sapply(outliers_list, length)

  # Calculate the number of rows with at least one outlier
  num_rows_with_outliers <- sum(rows_with_outliers)

  # Combine the results into one vector
  combined_results <- c(num_outliers_each_col, "Rows w/ Outliers" = num_rows_with_outliers)

  # Return the combined results
  return(combined_results)
}

# Detect outliers using the previously defined function
outliers_info <- detect_outliers_mad(df)

# Check if there are any outliers
if (all(outliers_info == 0)) {
  print("There are no outliers in any columns.")
} else {
  # Create a data frame for plotting
  outliers_data_df <- data.frame(
    Column = names(outliers_info),
    Outliers = outliers_info,
    OutlierPercentage = (outliers_info / nrow(df)) * 100 # Calculate the percentage of outliers
  )

  # Plot the number of outliers for all columns with labels
  ggplot(outliers_data_df, aes(x = Column, y = Outliers, fill = Column)) +
    geom_bar(stat = "identity") +
    geom_text(aes(label = sprintf("%.2f%%", OutlierPercentage)), hjust = -0.3, vjust = 0) + # Add labels to the bars
    coord_flip() + # Makes the bars horizontal
    labs(title = "Number of Outliers by Column", x = "Column", y = "Number of Outliers") +
    scale_fill_brewer(palette = "Set3") + # Use a color palette for different bars
    theme(legend.position = "none", axis.title.y = element_blank()) + # Remove the legend
    scale_y_continuous(expand = expansion(mult = c(0, 0.2))) # Extend the y-axis limits
}
```

### Univariate distributions: Numeric

<figure><img src="../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (26) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

<pre><code><strong>library(gridExtra)
</strong>library(e1071) # for skewness

# Function to check if the variable is highly skewed
is_highly_skewed &#x3C;- function(x) {
  # Remove missing values before computing skewness
  x &#x3C;- na.omit(x)
  abs(e1071::skewness(x)) > 1
}

create_plots &#x3C;- function(df, var) {
  skewness_value &#x3C;- round(e1071::skewness(na.omit(df[[var]])), 2)

  # Histogram
  p1 &#x3C;- ggplot(df, aes_string(x = var)) +
    geom_histogram(bins = 15, fill = "skyblue", color = "black") +
    ggtitle(paste("Histogram of", var)) +
    annotate("text", x = Inf, y = Inf, label = paste("Skewness:", skewness_value), hjust = 1.1, vjust = 1.1)

  # QQ Plot with reference line
  p2 &#x3C;- ggplot(df, aes_string(sample = var)) +
    stat_qq() +
    stat_qq_line(color = "red") +
    ggtitle(paste("QQ Plot of", var))

  if (is_highly_skewed(df[[var]])) {
    df$log_transformed &#x3C;- log(df[[var]] + 1)
    skewness_log_value &#x3C;- round(e1071::skewness(na.omit(df$log_transformed)), 2)

    # Histogram after log transformation
    p3 &#x3C;- ggplot(df, aes(x = log_transformed)) +
      geom_histogram(bins = 15, fill = "lightgreen", color = "black") +
      ggtitle(paste("Log-transformed", var)) +
      annotate("text", x = Inf, y = Inf, label = paste("Skewness:", skewness_log_value), hjust = 1.1, vjust = 1.1)

    # QQ Plot after log transformation
    p4 &#x3C;- ggplot(df, aes(sample = log_transformed)) +
      stat_qq() +
      stat_qq_line(color = "red") +
      ggtitle(paste("log-transformed", var))

    return(grid.arrange(p1, p2, p3, p4, ncol = 2))
  } else {
    return(grid.arrange(p1, p2, ncol = 2))
  }
}
  # If the dataframe has more than 2000 rows, sample 2000 rows randomly
  df_reduced &#x3C;- df
  if (nrow(df) > max_data_points_eda) {
    set.seed(123) # Set a random seed for reproducibility
    df_reduced &#x3C;- df_reduced[sample(nrow(df_reduced), 2000), ]
    cat("Since there are too many rows, ", max_data_points_eda," randomly selected rows are shown scatter plots.")
  }

# Apply the function to each numeric variable in the dataframe
lapply(names(df_reduced)[sapply(df_reduced, is.numeric)], function(var) create_plots(df, var))
</code></pre>

```
## [[1]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[2]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[3]]
## TableGrob (1 x 2) "arrange": 2 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 
## [[4]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[5]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[6]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[7]]
## TableGrob (2 x 2) "arrange": 4 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
## 3 3 (2-2,1-1) arrange gtable[layout]
## 4 4 (2-2,2-2) arrange gtable[layout]
## 
## [[8]]
## TableGrob (1 x 2) "arrange": 2 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
```

### Univariate distribution: Categorical variables

<figure><img src="../.gitbook/assets/image (30) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (31) (1).png" alt=""><figcaption></figcaption></figure>





```
# Function to create bar plots
create_bar_plot <- function(df, column_name) {
  # Checking if the column is a factor
  if (!is.numeric(df[[column_name]])) {
    df[[column_name]] <- as.factor(df[[column_name]])
    factor_levels <- levels(df[[column_name]])
  } else {
    factor_levels <- NULL
  }

  # Modify the data for plotting
  plot_data <- df %>%
    count(!!sym(column_name)) %>%
    arrange(desc(n)) %>%
    mutate(value = ifelse(row_number() > 12, "Other", as.character(!!sym(column_name)))) %>%
    group_by(value) %>%
    summarize(n = sum(n))

  # If the column is a factor, adjust the value names
  if (!is.null(factor_levels)) {
    plot_data$value <- factor(plot_data$value, levels = unique(c(factor_levels, "Other")))
  }

  # Finding the maximum value for adjusting y-axis
  max_value <- max(plot_data$n)

  # Creating the bar plot
  p <- ggplot(plot_data, aes(x = value, y = n, fill = value)) +
    geom_bar(stat = "identity") +
    geom_text(aes(label = paste0(round(n / sum(n) * 100, 1), "%")),
      vjust = -0.5
    ) +
    labs(title = paste("Bar Plot for", column_name), y = "Count") +
    theme(
      axis.text.x = element_text(angle = 45, hjust = 1),
      axis.title.x = element_blank()
    ) +
    theme(legend.position = "none") + # Remove the legend
    scale_y_continuous(limits = c(0, max_value * 1.2)) # Extend y-axis by 20%

  print(p) # Explicitly print the plot
}

# Iterating over each column
for (column_name in names(df)) {
  if (!is.numeric(df[[column_name]])) { # Checking if the column is non-numeric
    unique_values <- length(unique(df[[column_name]]))
    if (unique_values != nrow(df)) { # Ignoring columns with all unique values
      create_bar_plot(df, column_name)
    }
  }
}
```

### Overview heatmap

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption><p>Notice the "None" group have Z-score outliers.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (32) (1).png" alt=""><figcaption></figcaption></figure>

```
data_for_heatmap <- df
if(!is.null(target_bin)) { #numeric target variable
  data_for_heatmap$target_bin <- target_bin
} else if(!is.null(target_var)) { # categorical target
  target_bin <- target_var
}

numeric_cols <- sapply(data_for_heatmap, is.numeric)

# Prepare data for the heatmap
# Filter out rows with missing values
data_for_heatmap <- na.omit(data_for_heatmap)

# more than 2 numeric columns
if (sum(numeric_cols) > 1) {
  # If the dataframe has more than 2000 rows, sample 2000 rows randomly
  if (nrow(data_for_heatmap) > max_data_points_eda) {
    set.seed(123) # Set a random seed for reproducibility
    data_for_heatmap <- data_for_heatmap[sample(nrow(data_for_heatmap), 2000), ]
    cat("Since there are too many rows, ", max_data_points_eda," randomly selected rows are shown in this heatmap")
  }
  
  # Determine whether to include row names based on the number of rows
  show_row_names <- nrow(data_for_heatmap) <= 100
  
  # Define a color palette from yellow to blue
  my_palette <- colorRampPalette(c("yellow", "blue"))(n = 299)
  
  # Initialize the RowSideColors parameter as NULL
  row_side_colors <- NULL
  
  dist_pearson <- function(x, ...)
    as.dist(1-cor(t(x), method="pearson"))
    
  # Check if target_var is provided and not null
  if (!is.null(target_var) && target_var %in% names(data_for_heatmap)) {
    # Use the 'target_bin' column if it exists, otherwise use the column specified by target_var
    target_col <- ifelse("target_bin" %in% names(data_for_heatmap), 'target_bin',  target_var)
    
    # define groups of rows
    groups <- data_for_heatmap[[target_col]]
    groups_colors <- as.numeric(factor(groups))
    unique_groups <- unique(groups)
    row_side_colors <- rainbow(length(unique_groups))[groups_colors]
    
    # Create the heatmap with clustering trees and color bar
    gplots::heatmap.2(
      as.matrix(data_for_heatmap[numeric_cols]),
      scale = "column",  # Data is already scaled, so no scaling is needed here
      distfun = dist_pearson,  # for distance between rows
      hclustfun = function(x) hclust(x, method = "average"),  # for hierarchical clustering
      dendrogram = "both",  # only row dendrograms
      trace = "none",  # turns off trace lines inside the heatmap
      density.info = "none",  # turns off density plot inside color legend
      margins = c(8, 8),  # adjusts the margins around the plot
      Colv = TRUE,  # cluster columns
      Rowv = TRUE,  # always cluster rows
      labRow = if(show_row_names) rownames(data_for_heatmap) else NA,  # show/hide row names
      key = TRUE,  # whether to show the color key
      keysize = 1,  # size of the color key
      symbreaks = FALSE , # whether to make color breaks symmetrical around zero
      col = my_palette, # yellow and blue
      RowSideColors = row_side_colors,  # add side color bar if side_colors is not NULL
      cexCol = 0.9,         # Make column labels smaller
      srtCol = 45,          # Rotate column labels 45 degrees
      adjCol = c(1,0)   # Adjust the position of the column labels
    )
    legend("topright", legend = unique_groups, fill = rainbow(length(unique_groups)), title = target_var)
    
  } else {   # RowSideColors = NULL gives errors. 
    # Create the heatmap without the color bar
    gplots::heatmap.2(
      as.matrix(data_for_heatmap[numeric_cols]),
      scale = "column",  # Data is already scaled, so no scaling is needed here
      distfun = dist_pearson,  # for distance between rows
      hclustfun = function(x) hclust(x, method = "average"),  # for hierarchical clustering
      dendrogram = "both",  # only row dendrograms
      trace = "none",  # turns off trace lines inside the heatmap
      density.info = "none",  # turns off density plot inside color legend
      margins = c(8, 8),  # adjusts the margins around the plot
      Colv = TRUE,  # cluster columns
      Rowv = TRUE,  # always cluster rows
      labRow = if(show_row_names) rownames(data_for_heatmap) else NA,  # show/hide row names
      key = TRUE,  # whether to show the color key
      keysize = 1,  # size of the color key
      symbreaks = FALSE , # whether to make color breaks symmetrical around zero
      col = my_palette, # yellow and blue
      cexCol = 0.9,         # Make column labels smaller
      srtCol = 45,          # Rotate column labels 45 degrees
      adjCol = c(1,0)   # Adjust the position of the column labels
    )
  }
}
```

### Bivariate correlation

### Correlation matrix (Blank indicates not significant, P > 0.05)

<figure><img src="../.gitbook/assets/image (33) (1).png" alt=""><figcaption></figcaption></figure>

```
library(corrplot)
df2 <- df[sapply(df, is.numeric)]
df2 <- na.omit(df2)
M <- cor(df2)
testRes <- cor.mtest(df2, conf.level = 0.95)
## leave blank on non-significant coefficient
## add significant correlation coefficients
corrplot(M,
  p.mat = testRes$p, method = "circle", type = "lower", insig = "blank",
  addCoef.col = "black", number.cex = 0.8, order = "AOE", diag = FALSE
)
```

### Correlation between numeric variables



<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../.gitbook/assets/image (34) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (35) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (36) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (37) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>





```
library(GGally)
library(hexbin)

num_cols <- names(df)[sapply(df, is.numeric)]
if(length(num_cols) > 1) {
  num_col_pairs <- combn(num_cols, 2, simplify = FALSE)

  for (pair in num_col_pairs) {
    col_x <- pair[1]
    col_y <- pair[2]

    # Perform correlation test
    corr_test <- cor.test(df[[col_x]], df[[col_y]], method = "pearson")

    if (corr_test$p.value < 0.01 && abs(corr_test$estimate) > 0.1) {
      corr_label <- paste("R =", round(corr_test$estimate, 2), "\nP =", format(corr_test$p.value, scientific = TRUE, digits = 2))

      if (nrow(df) > 5000) {
        # Use hexbin plot
        p <- ggplot(df, aes_string(x = col_x, y = col_y)) +
          labs(title = paste(col_x, "vs", col_y), x = col_x, y = col_y) +
          geom_hex(bins = 70) +
          scale_fill_continuous(type = "viridis")
      } else {
        # Use scatter plot
        if (!is.null(target_var) && !is.na(target_var)) {
          if(target_var != col_x && target_var != col_y){
            df_ggplot <- df
            if(!is.null(target_bin)) {
              df_ggplot$target_bin <- target_bin
            }
            
            color_var <- ifelse(!is.numeric(df[[target_var]]), target_var, "target_bin")
            p <- ggplot(df_ggplot, aes_string(x = col_x, y = col_y, color = color_var)) +
              geom_point(alpha = 0.7) +
              labs(title = paste(col_x, "vs", col_y), x = col_x, y = col_y) +
              guides(color = guide_legend(title = color_var))
          }
        } else {
          p <- ggplot(df, aes_string(x = col_x, y = col_y)) +
            geom_point(alpha = 0.7) +
            labs(title = paste(col_x, "vs", col_y), x = col_x, y = col_y)
        }
      }

      p <- p + annotate("text", x = Inf, y = Inf, label = corr_label, hjust = 1.1, vjust = 1.1, size = 4)
      print(p)
    }
  }
}  else {
  cat("Not applicable.")
}
```

### Correlation between a numeric and a categorical variable



<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>





```
num_cols <- sapply(df, is.numeric)
cat_cols <- sapply(df, is.factor)
if(length(num_cols) > 1 & length(cat_cols) > 1) {
  # Perform ANOVA and create violin plots for significant cases
  for (num_var in names(df)[num_cols]) {
    for (cat_var in names(df)[cat_cols]) {
      if (cat_var != "target_bin") {
        anova_result <- aov(df[[num_var]] ~ df[[cat_var]], data = df)
        p_value <- summary(anova_result)[[1]]$"Pr(>F)"[1]
        if (p_value < 0.01) {
          plot <- ggplot(df, aes_string(x = cat_var, y = num_var)) +
            geom_violin(trim = FALSE, fill = "lightblue", color = "black") +
            #geom_boxplot(width = 0.2, fill = "white", color = "black") +
            labs(title = paste(num_var, "by", cat_var, "(ANOVA P =", format(p_value, scientific = TRUE, digits = 2), ")"), x = cat_var, y = num_var)

          if (nrow(df) < 300) {
            plot <- plot + geom_jitter(width = 0.2, color = "black")
          }
          print(plot)
        }
      }
    }
  }
}  else {
  cat("Not applicable.")
}
```

### Correlation between two categorical variables

```
cat_cols <- !(sapply(df, is.numeric)) & names(df) != "target_bin"
cat_var_names <- names(df)[cat_cols]
if(length(cat_var_names) > 1) {
  # Perform chi-squared tests and create stacked bar plots if p-value < 0.01
  for (i in 1:(length(cat_var_names) - 1)) {
    for (j in (i + 1):length(cat_var_names)) {
      tab <- table(df[[cat_var_names[i]]], df[[cat_var_names[j]]])
      chi_test <- chisq.test(tab)
      if (is.na(chi_test$p.value)) next
      if (chi_test$p.value < 0.01) {
        p <- ggplot(df, aes_string(x = cat_var_names[i], fill = cat_var_names[j])) +
          geom_bar(position = "fill") +
          labs(
            title = paste(cat_var_names[i], "vs", cat_var_names[j], "(Chisq P =", format(chi_test$p.value, scientific = TRUE, digits = 2), ")"),
            x = paste(cat_var_names[i]),
            y = "Proportion"
          ) +
          coord_flip()
        print(p)
      }
    }
  }
} else {
  cat("There is one or zero categorical variable")
}
```
