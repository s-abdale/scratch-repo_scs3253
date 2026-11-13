# Topics

**Goal:** flesh out project topics to discuss with team & present to Carl
**Reference:** Topics [Google doc](https://docs.google.com/document/d/19V1aN-p6TagRctTDrJu4Lug_M8ZFspxRrJtdDuLNeEY/edit?usp=sharing)


## Proposed ideas

### Project 1: Predicting Changes in Canadian Job Demand
  * **Summary:** Predicts whether job vacancies for specific Canadian occupations will increase or decrease next quarter by merging labor stats with AI-exposure metrics.
  * **Datasets:** [Statistics Canada Job Vacancies & Wages](https://www.statcan.gc.ca/), [Labour Force Characteristics](https://www.statcan.gc.ca/), and [Occupational AI-Exposure Study](https://www.statcan.gc.ca/).
  * **Techniques Proposed:** Logistic Regression, SVC, Random Forest Classifier.
  * **Review:** 
   - Can bring in Unsupervised Learning / Clustering (Ch 9) here. Before running classification, use K-Means or hierarchical clustering to group occupations into clusters based on skill similarity or baseline wage, and use cluster membership as a feature.
   - Stat Can dataset can have missing values or sparse matrices for niche jobs to protect privacy, *and* definitions change over time (NOC codes changed from 2016 to 2021). We'd have to map the differences (mapping old job categories/classifications to new ones)
   - Quarterly data sometimes isn't updated for several months, which can make real-time predictions tricky

### Project 2: Predicting Wildfire Intensity
  * **Summary:** Forecasts daily wildfire intensity and high-risk cells using satellite-observed fire activity and atmospheric weather conditions.
  * **Datasets:** [NASA FIRMS (Fire Information for Resource Management System)](https://firms.modaps.eosdis.nasa.gov/) and [NASA POWER (Prediction of Worldwide Energy Resources)](https://power.larc.nasa.gov/).
  * **Techniques Proposed:** Regression (predicting max daily Fire Radiative Power) and Classification (predicting high-intensity wildfire activity).
  * **Review:** 
    - *if combining wildfires & vegetation:*
      - Since vegetation stress often precedes or correlates with wildfire vulnerability, could combine this with Project 3
      - Can combine MODIS, SMAP, and FIRMS into a single unified dataframe, if we're combining this with vegetation stress
    - Could use spatial clustering (like DBSCAN from Ch 9) to automatically define geographic high risk zones instead of arbitrary grid cells
    - Practical implementation:
      - If we're focusing on Canada only, we can use the FIRMS Archive Download Tool, or spatial filtering APIs with box coordinates set to cover latitude & longitude ranges over Canada
      - When preparing the data, would make sense to divide land by ecological zone, instead of province or arbitrary grids. Layer the weather & fire datasets over these zones, then track fire occurrences.

### Project 3: Predicting Vegetation Stress Using Satellite and Climate Data
  * **Summary:** Predicts future vegetation greenness (NDVI) and stress levels using weather, soil moisture, and satellite vegetation indices.
  * **Datasets:** [MODIS Vegetation Indices](https://modis.gsfc.nasa.gov/), NASA POWER, and [NASA SMAP (Soil Moisture Active Passive)](https://smap.jpl.nasa.gov/).
  * **Techniques Proposed:** Regression (predicting future NDVI) and Classification (predicting stress severity classes: normal, moderate, severe).
  * **Review:** See Project 2 notes

### Project 4: Predicting Road Accident Severity in Canada
  * **Summary:** Predicts whether a vehicle collision results in fatal or non-fatal injuries based on environmental, road, and vehicle characteristics.
  * **Dataset:** [Transport Canada National Collision Database (NCDB)](https://www.google.com/search?q=https://tc.canada.ca/en/road-transport/statistics-data/national-collision-database-ncdb).
  * **Techniques Proposed:** Logistic Regression, Decision Trees, Random Forest, and Gradient Boosting.
  * **Review:** 
    - Rando Forest will probably work best here
    - Will likely need to consider someting *other* than a precison-based model, since the majority of road accidents are property-damage-only or minor injuries. Fatalities are a tiny fraction of the total dataset, so a precision based model would still technically "work" if it completely ignored the fatalities (e.g., since ~98% of road accidents are non-fatal, the model would have a great accuracy score of 98% if it said that all road accidents are non-fatal. this is inaccurate, so we'd need to use an imbalanced evaluation metric, sort of like the banking examples Carl mentioned in class)

