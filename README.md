# F1-Racing-Performance-Analysis

## Overview
The objective of this project was to analyze and visualize Formula 1 race data to uncover key insights into driver performance, race outcomes, and technological trends. By focusing on metrics like driver wins, starting and finishing positions, and fastest lap speeds, the analysis aimed to identify trends over time and highlight the most successful drivers and teams. Key visualizations included bar plots, scatter plots, and speed trend analyses. The project provides valuable insights into how factors such as starting positions, team performance, and car technology impact race results.


## Objectives
The goal of this project is to analyze and visualize Formula 1 race data, focusing on key metrics such as driver performance, fastest lap speeds, and race results. The analysis aims to uncover trends over the years and identify which drivers and teams have been the most successful.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:**[F1 datasets](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020)
 



```python
#import all packages and set plots to be embedded in line

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sb

%matplotlib inline

```

## Data Cleaning & Preprocessing
### Handling Missing Data: Rows with missing or incorrect values were removed or corrected.
### Renaming Columns: Column names were renamed for better readability and clarity.
### Data Conversion: Ensured that columns such as year, fastestLapSpeed, and gp_name were in the appropriate data types (e.g., numeric).



```python
