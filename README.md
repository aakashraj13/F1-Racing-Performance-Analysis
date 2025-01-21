# F1-Racing-Performance-Analysis

## Overview
The objective of this project was to analyze and visualize Formula 1 race data to uncover key insights into driver performance, race outcomes, and technological trends. By focusing on metrics like driver wins, starting and finishing positions, and fastest lap speeds, the analysis aimed to identify trends over time and highlight the most successful drivers and teams. Key visualizations included bar plots, scatter plots, and speed trend analyses. The project provides valuable insights into how factors such as starting positions, team performance, and car technology impact race results.


## Objectives
The goal of this project is to analyze and visualize Formula 1 race data, focusing on key metrics such as driver performance, fastest lap speeds, and race results. The analysis aims to uncover trends over the years and identify which drivers and teams have been the most successful.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:**[F1 datasets](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020)
 

## Importing packages

```python
#import all packages and set plots to be embedded in line

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sb

%matplotlib inline

```
## load the data and assign the column
```python

results = pd.read_csv(r'C:\Users\Aakash\Downloads\F1 champian ship data\results.csv', 
                      names=['resultId', 'raceId', 'driverId', 'constructorId', 'number', 'grid', 'position',
                             'positionText', 'positionOrder', 'points', 'laps', 'time', 'milliseconds', 
                             'fastestLap', 'rank', 'fastestLapTime', 'fastestLapSpeed', 'statusId'],
                      header=None)

races = pd.read_csv(r'C:\Users\Aakash\Downloads\F1 champian ship data\races.csv',
                    names = ['raceId' , 'year', 'round', 'circuitId', 'name' ,'date','time',
                            'url','fp1_date','fp1_time','fp2_date','fp2_time','fp3_date', 'fp3_time',
                             'quali_date','quali_time','sprint_date','sprint_time'],
                    header = None)


drivers = pd.read_csv(r'C:\Users\Aakash\Downloads\F1 champian ship data\drivers.csv',
                      names = ['driverId' , 'driverRef' , 'number' , 'code' , 'forename', 'surname',
                               'dob', 'nationality','url'], header = None) 

constructors = pd.read_csv(r'C:\Users\Aakash\Downloads\F1 champian ship data\constructors.csv',
                           names = ['constructorId' , 'constructorRef' , 'name' , 'nationality' ,'url'],
                            header = None) 


```

## Data Cleaning & Preprocessing
 - Handling Missing Data: Rows with missing or incorrect values were removed or corrected.
 -  Renaming Columns: Column names were renamed for better readability and clarity.
 - Data Conversion: Ensured that columns such as year, fastestLapSpeed, and gp_name were in the appropriate data types (e.g., numeric).



```python
# merge datasets

df = pd.merge(results , races[[ 'raceId' , 'year', 'name', 'round']], on = 'raceId' , how = 'left')
df = pd.merge(df, drivers[[ 'driverId' , 'driverRef','nationality' ]], on = 'driverId' , how = 'left')
df = pd.merge(df, constructors[[ 'constructorId','name','nationality']], on = 'constructorId' , how = 'left')

# drop columns
df.drop(['number' , 'position', 'positionText','laps','fastestLap','statusId', 'resultId', 'raceId','driverId',
         'constructorId'],axis = 1, inplace = True)

#rename columns

df.rename(columns = {'rank': 'fastest_lap_rank','name_x':'gp_name','nationality_x':'driver_nationality',
                   'name_y':'constructor_name','nationality_y':'constructor_nationality', 'driver_ref': 'driver'},inplace = True)


#rearrange columns 
df = df[['year', 'gp_name','round','driverRef', 'constructor_name', 'grid','positionOrder','points',
        'time','milliseconds','fastest_lap_rank', 'fastestLapTime', 'fastestLapSpeed', 'driver_nationality', 'constructor_nationality']]


#drop season 2019

df = df[df['year']!= 2019]

#sort values

df = df.sort_values(by=['year','round','positionOrder'],ascending = [False , True, True]) 
# Replace \N values in time col

df.time.replace('\\N',np.nan,inplace= True)
df.milliseconds.replace('\\N',np.nan,inplace=True)
df.fastest_lap_rank.replace('\\N',np.nan,inplace=True)
df.fastestLapTime.replace('\\N',np.nan,inplace=True)
df.fastestLapSpeed.replace('\\N',np.nan,inplace=True)

# Convert columns to numeric, coerce non-numeric values to NaN
df['fastestLapSpeed'] = pd.to_numeric(df['fastestLapSpeed'], errors='coerce')
df['fastest_lap_rank'] = pd.to_numeric(df['fastest_lap_rank'], errors='coerce')
df['milliseconds'] = pd.to_numeric(df['milliseconds'], errors='coerce')

# Handle NaN values, e.g., by filling them with 0 or dropping them
df['fastestLapSpeed'].fillna(0, inplace=True)
df['fastest_lap_rank'].fillna(0, inplace=True)
df['milliseconds'].fillna(0, inplace=True)

#change datatypes

df.fastestLapSpeed = df.fastestLapSpeed.astype(float)
df.fastest_lap_rank = df.fastest_lap_rank.astype(float)
df.milliseconds = df.milliseconds.astype(float)

#reset index 
df.reset_index(drop=True ,inplace = True)
#shape

print(df.shape)

#info()
df.info()

#head()
df.head(8)

#head()

df.head(8)

```
## Data Visualization
### 1. Bar Plot
Most GP Wins by Driver
Purpose: Display the drivers with the most wins.
Insight: Shows the most successful drivers in terms of race wins.

### 2. Scatter Plot
Starting Position vs. Finishing Position
Purpose: Explore how starting positions influence race outcomes.
Insight: Understand how critical a good starting position is for a driver’s success.

### 3. FacetGrid 
Fastest Lap Speed Trends
Purpose: Show the evolution of the fastest lap speed at individual Grand Prix over the years.
Insight: Provides a view of how the car speeds have evolved over time at each race.


## Data Analysis
### Driver Wins
Goal: Identify the drivers with the most race wins.
Method: Filtered data to include only first-place finishes (positionOrder = 1).
Result: Counted wins for each driver and displayed the most successful drivers.

```python

import seaborn as sb
import matplotlib.pyplot as plt

# Set seaborn color palette
sb.set_palette('deep')

# Adjust figure size
plt.rcParams['figure.figsize'] = 10, 6

# Drop non-numeric rows from 'positionOrder'
df = df[df['positionOrder'] != 'positionOrder']

# Convert 'positionOrder' to numeric
df['positionOrder'] = pd.to_numeric(df['positionOrder'], errors='coerce')

# Drop rows where 'positionOrder' became NaN
df.dropna(subset=['positionOrder'], inplace=True)

print(df['positionOrder'].unique())  # Should now only have valid numeric values

driver_winner = (
    df.loc[df['positionOrder'] == 1]
    .groupby('driverRef')['positionOrder']
    .count()
    .sort_values(ascending=False)
    .to_frame()
    .reset_index()
)
print(driver_winner.head())  # Check the resulting DataFrame

```
### Create the barplot for driver winner 

```python
import seaborn as sb
import matplotlib.pyplot as plt

# Create the barplot
sb.barplot(data=driver_winner, y='driverRef', x='positionOrder',color = 'green', alpha=0.8)

# Set plot title and labels
plt.title('Most GP Winner in F1')
plt.ylabel('Driver Name')
plt.xlabel('Number of GP Wins')
plt.yticks([])  # Remove y-ticks for cleaner presentation

# Show the plot
plt.show()

```

### New dataframe of top 10 GP winners
```python
top10drivers = driver_winner.head(10)

print(top10drivers)

# top 10 drivers plot

sb.barplot(data = top10drivers,  y= 'driverRef', x = 'positionOrder', color = 'blue', alpha = 0.8, linewidth = .8, edgecolor = 'black')
plt.title('Most GP Winner in F1')
plt.ylabel('Driver Name')
plt.xlabel('Number of GP wins');

```


## GP constructors winners 
```python
constructor_winner = (df.loc[df['positionOrder'] == 1]  # Use square brackets
                      .groupby('constructor_name')['positionOrder']
                      .count()
                      .sort_values(ascending=False).to_frame() 
                      .reset_index()
)

# top 10 constructor wins

top10constructor = constructor_winner.head(10)

print(top10constructor)

#constructor wins plot

sb.barplot(data = top10constructor, y='constructor_name', x = 'positionOrder', color = 'yellow',
           alpha = 0.8, linewidth = .8, edgecolor = 'black')
plt.title('Most GP wins for Constructors')
plt.ylabel('constructor_name')
plt.xlabel('Number of GP wins');

```

## Performance Analysis
Goal: Explore the relationship between starting position (grid) and finishing position (positionOrder).
Method: Visualized how drivers’ starting positions correlate with their final race positions.

```python
# Ensure data is numeric
df['grid'] = pd.to_numeric(df['grid'], errors='coerce')
df['positionOrder'] = pd.to_numeric(df['positionOrder'], errors='coerce')

# Filter out rows with grid = 0
df_no_zero = df[df['grid'] != 0]

# Create the scatterplot with regression line
plt.figure(figsize=[12, 7])
sb.regplot(data=df_no_zero, x='grid', y='positionOrder', 
           x_jitter=0.3, y_jitter=0.3, scatter_kws={'alpha': 1/5})
plt.title('Starting Position vs. Finish Place')
plt.ylabel('Finish Place')
plt.xlabel('Starting Position')
plt.show()

```
## Grand Prix Speed Trends
Goal: Analyze average fastest lap speeds across different Grand Prix over the years.
Method: Grouped the data by race name (gp_name) and year, then calculated the mean fastest lap speed for each group.

```python

df['gp_name'] = df['gp_name'].astype(str)
print(df['gp_name'].unique())

df_cleaned = df[df['gp_name'].notna() & (df['gp_name'] != '')]

# Check again
print(df_cleaned['gp_name'].unique())


df_speed = df_cleaned[df_cleaned['year'] >= 2004]
df_group_speed = df_speed.groupby(['gp_name', 'year'])['fastestLapSpeed'].mean().to_frame().reset_index()

# Create a FacetGrid
g = sb.FacetGrid(data=df_group_speed, col='gp_name', col_wrap=5)
g.map(plt.scatter, 'year', 'fastestLapSpeed', alpha=0.8, linewidth=0.8, edgecolor='black', s=100)
g.set_titles("{col_name}")
g.set_xlabels('Year')
g.set_ylabels('Average Fastest Speed (km/h)')

# Adjust layout and title

g.fig.suptitle('Average Speed During Fastest Lap at Individual GPs ')

plt.show()

```
## Key Findings & Insights
### Driver Performance: 
Top drivers can consistently perform well, with a noticeable trend in race wins for a select few.

### Team Performance: 
Some teams (like Red Bull) show dominance in both driver and constructor standings.

### Speed Trends:
Fastest lap speeds have been increasing over the years, indicating advancements in car technology.


## Conclusion
This project provides a comprehensive overview of F1 racing performance, focusing on key aspects such as driver success, race results, and technological progress in terms of lap speeds. By analyzing the data, we can better understand how starting positions, team strategy, and car technology influence race outcomes.
 
