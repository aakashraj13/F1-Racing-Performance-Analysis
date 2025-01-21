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

```
