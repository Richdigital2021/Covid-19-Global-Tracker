# 📊 Global COVID-19 Data Report
## 📌 1. Notebook Title & Overview
This notebook analyses global COVID-19 data, focusing on key trends in cases, deaths, recoveries (where applicable), and vaccinations. 

✅ Data Source: [Our World in Data](https://ourworldindata.org/coronavirus)  
✅ Libraries used: pandas, numpy, matplotlib, seaborn, plotly  
✅ Goals:
- Load and clean data
- Analyse global and country-level trends
- Calculate death and vaccination rates
- Visualize patterns with charts and maps

## 📦 2. Import Libraries
```bash
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px

# Plot style
plt.style.use('seaborn-v0_8')
sns.set_context('notebook')
```

## 📥 3. Load the Dataset
```
# Load directly from Our World in Data
url = "https://covid.ourworldindata.org/data/owid-covid-data.csv"
df = pd.read_csv(url)
df.shape, df.columns
```
## 🧹 4. Data Cleaning & Preparation
```
# Keep only relevant columns
df = df[['location', 'date', 'total_cases', 'new_cases', 'total_deaths', 'new_deaths',
         'total_vaccinations', 'people_fully_vaccinated', 'population']]

# Convert date
df['date'] = pd.to_datetime(df['date'])

# Drop rows without population info
df = df.dropna(subset=['population'])

# Fill NAs in new cases/deaths with 0
df[['new_cases', 'new_deaths']] = df[['new_cases', 'new_deaths']].fillna(0)

# Filter to top 10 most populous countries
top_countries = df.groupby('location')['population'].max().sort_values(ascending=False).head(10).index.tolist()
df_top = df[df['location'].isin(top_countries)]
```
## 🔍 5. Exploratory Data Analysis (EDA)
🧵 A. Total Cases Over Time
```
plt.figure(figsize=(12,6))
for country in top_countries:
    country_data = df_top[df_top['location'] == country]
    plt.plot(country_data['date'], country_data['total_cases'], label=country)
plt.legend()
plt.title("Total COVID-19 Cases Over Time")
plt.xlabel("Date")
plt.ylabel("Total Cases")
plt.grid(True)
plt.tight_layout()
plt.show()
```
🩸 B. Daily New Cases (Heatmap-style)
```
# Pivot table for heatmap
pivot = df_top.pivot_table(values='new_cases', index='location', columns='date', fill_value=0)

plt.figure(figsize=(14,6))
sns.heatmap(pivot, cmap='Reds', cbar_kws={'label': 'Daily New Cases'})
plt.title('Daily New COVID-19 Cases (Heatmap View)')
plt.xlabel('Date')
plt.ylabel('Country')
plt.tight_layout()
plt.show()
```
## ⚖️ 6. Calculate Critical Indicators
A. Death Rate
```
df_top['death_rate'] = df_top['total_deaths'] / df_top['total_cases']
df_top['death_rate'] = df_top['death_rate'].fillna(0)

latest = df_top[df_top['date'] == df_top['date'].max()]
latest_death = latest[['location', 'death_rate']].sort_values(by='death_rate', ascending=False)

sns.barplot(data=latest_death, x='death_rate', y='location')
plt.title('Death Rate by Country (Latest)')
plt.xlabel('Death Rate')
plt.ylabel('Country')
plt.show()
```
B. Vaccination Rate
```
latest['vaccination_rate'] = latest['people_fully_vaccinated'] / latest['population']
latest['vaccination_rate'] = latest['vaccination_rate'].fillna(0)

sns.barplot(data=latest.sort_values(by='vaccination_rate', ascending=False),
            x='vaccination_rate', y='location')
plt.title('Vaccination Coverage by Country')
plt.xlabel('Vaccination Rate')
plt.ylabel('Country')
plt.show()
```
## 🗺️ 7. Choropleth Map (Optional)
```
global_latest = df[df['date'] == df['date'].max()]
global_latest['vaccination_rate'] = global_latest['people_fully_vaccinated'] / global_latest['population']
global_latest = global_latest.dropna(subset=['vaccination_rate'])

fig = px.choropleth(global_latest, locations='location',
      locationmode='country names',
      color='vaccination_rate',
      title='Global Vaccination Coverage',
      color_continuous_scale='Viridis')
      fig.show()
```
## 💡 8. Narrative & Key Insights

### 📌 Insights

- Most populous countries (e.g., India, USA) had high case loads.
- Death rate varies significantly by country, with some exceeding 2–3% early in the pandemic.
- Vaccination rollout showed major disparities between rich and developing nations.
- Some countries experienced steep second/third waves as shown in the heatmap.

### ✅ Recommendations

- Strengthen global vaccine equity.
- Improve real-time data availability for all nations.
## 📁 9. Next Steps (Optional)

### 📈 Extensions

- Add recovery data (if available).
- Build a dashboard using Streamlit or Dash.
- Compare government responses using Oxford Stringency Index.

## ✅ Summary
This notebook successfully:

- Loads and cleans real-world COVID-19 data

- Analyzes trends across countries and over time

- Calculates key metrics like death rate and vaccination rate

- Visualizes trends clearly and interactively

- Includes written narratives and is fully reproducible

## For Collaboration contact:
Author: Akintunde Richard

Email: akintunderichard28@gmail.com

Objective: Load, clean, analyse, and visualize COVID-19 data globally using Python
