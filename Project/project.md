# Exploring Sweden’s Job Market Geography: A Look at Arbetsförmedlingen Data

---

## 1. Introduction

**Where are the jobs in Sweden, really?** Working with publicly available data from Arbetsförmedlingen (Sweden’s Public Employment Service), we can map out where employers are hiring, how many vacancies they are offering, and in which fields. By combining these job listings with **population statistics** and **municipality boundaries**, we can explore:

- Which municipalities (kommuner) have the most job listings in raw terms?
- Are there smaller municipalities where job density (jobs per capita) is unexpectedly high?
- Which **occupation fields** (e.g., Data/IT, Hälso- och sjukvård, Administration, etc.) are prominent in specific areas?
- Finally we'll look at the specifically the distribution of Data/IT over Sweden's regions.

This post has two goals: to show how to collect and prepare data and to display where jobs are located in Swedish municipalities. It provides useful information for job seekers, policymakers, and anyone interested in Sweden’s job market.

---

## 2. Data


This project relies on **three main data sources**:

1. **Arbetsförmedlingen Job Listings**  
   - Retrieved from the open API: `https://jobstream.api.jobtechdev.se/snapshot`.  
   - Contains ~39,000 job listings, with key fields:job location, occupational category (e.g., “Data/IT”), open positions per listing.

2. **Municipality Population**  
    - Scraped from a [Wikipedia table of Swedish municipalities](https://sv.wikipedia.org/wiki/Lista_%C3%B6ver_Sveriges_kommuner), including population and region (län).  



3. **Geographic Boundaries (GeoJSON)**  
   - Files `swedish_municipalities.geojson` and `swedish_regions.geojson` sourced from [Open Knowledge Foundation Sweden](https://github.com/okfse) for **choropleth** maps to visualize data.


---

## 3. Data Collection & Wrangling
We gather data from various sources, including job listings, population stats, and geographic boundaries, then clean and format it to ensure accurate, compatible visuals.

### 3.1 Job Listings
We collect job listing data from Arbetsförmedlingen's API, including location, categories, and vacancies, and store it locally for reproducibility. Missing `number_of_vacancies` values are set to `1`, assuming at least one position. The cleaned dataset is then prepared for integration. The cleaned dataset is saved as a JSON file, which couldn't be uploaded to GitHub due to being larger than 100 MB.

### 3.2 Population Data

We scrape a Wikipedia page to collect data on Swedish municipalities, their region names (län), and populations. This information is essential for analyzing job density and creating meaningful visualizations. 

To align the scraped data with the job listing dataset, we clean it by:  
1. Removing the "kommun" suffix from municipality names (e.g., "Stockholms kommun" becomes "Stockholm").  
2. Adjusting names ending in 's' after a non-vowel (e.g., "Danderyds" becomes "Danderyd") while keeping exceptions like "Degerfors" unchanged.  
3. Applying manual fixes for cases like "Falun" and "Grums" due to cleaning quirks.

The cleaned data is saved to a CSV file, `swedish_municipalities_population.csv`, for reproducibility.

### 3.3 Aggregating Vacancies and Merging with Population Data

We calculate total vacancies for each municipality by summing the `number_of_vacancies` from all job listings. This data is then merged with the population dataset to compute job density, measured as jobs per 1,000 people (`Jobs_per_1000`) for later use. This metric highlights areas with notably high or low job availability relative to their population.

## 4. Visual Analysis  

We explore the data through various visualizations:  
- **Bar Diagrams** to compare job vacancies across municipalities relative to population.  
- **Choropleth Maps** to show overall job density (jobs per 1,000 residents) and specific fields like Health Services, IT, and other key sectors.  
- **Tree Maps** to display job distribution across different occupation fields.  
- **Regional IT Maps** to highlight the geographic spread of Data/IT vacancies.  


### 4.1 Bar Chart Comparing Job Vacancies and Population
We create a bar chart showing the top 15 municipalities by total job vacancies, comparing vacancies (blue bars) to population (orange bars). Stockholm serves as the baseline, with equal-length bars for jobs and population. For other municipalities, longer blue bars indicate a job surplus, while longer orange bars indicate a population surplus. This provides a clear comparison of job availability relative to population.


    
![png](project_files/project_7_0.png)
    


### Interpretation: Job Vacancies Across Municipalities

1. **Major Economic Hubs (Stockholm, Göteborg, Malmö)**:  
Stockholm, Göteborg, and Malmö have large populations but fewer jobs per capita, indicating higher competition or a mismatch between labor supply and demand.  

2. **Smaller and Mid-Sized Municipalities**:  
Towns like Växjö, Karlskrona, and Gävle have more jobs relative to their populations, likely due to specific industries, while Uppsala shows fewer vacancies, suggesting an oversaturated market.  

This challenges the assumption that larger cities offer the most opportunities, likely due to regional competition, economic factors, or incomplete job listings. Smaller and mid-sized municipalities often provide better job availability per capita.


### 4.2 Choropleth Map of Job Density Across Sweden

We create a choropleth map using `GeoPandas` to visualize job density across Sweden’s municipalities, measured as jobs per 1,000 residents. Darker colors represent areas with more jobs relative to their population. The map is generated by merging municipal boundaries with job density data and plotting a color gradient to show the distribution of job opportunities nationwide.

    Skipping field geo_point_2d: unsupported OGR type: 3



    
![png](project_files/project_9_1.png)
    


### Interpretation: Job Density Across Municipalities

1. **High Job Density in Smaller Municipalities**:  
some municipalities in northern Sweden, often have high job density due to specialized industries or large employers relative to their populations, leading to fluctuations in job availability per resident.  

2. **Lower Density Around Urban Centers**:  
Urban centers like Stockholm, Göteborg, and Malmö have lower job density in surrounding areas, as large populations dilute the metric despite significant economic activity.  

3. **Southern Sweden**:  
Southern Sweden generally shows higher and more stable job density, which we needs to be investigated more closely.

---


### 4.3 Treemap of Job Vacancies by Industry 

Using `squarify`, we create a **treemap** showing job vacancies across Sweden's occupation fields. Each section's size represents total vacancies, with unspecified fields labeled "Unknown." This visualization reveals dominant industries in the dataset, providing context for geographic patterns and aiding the analysis of job market trends.


    
![png](project_files/project_11_0.png)
    


### Interpretation: Treemap of Job Vacancies by Occupation Field  

The treemap reveals key patterns in Sweden’s job market:  

1. **Healthcare**: With 40,487 vacancies, healthcare is the largest sector. Its demand may stabilize job density across regions, but the opposite could be true—areas with aging populations and fewer younger workers might see higher concentrations of vacancies. Further analysis is needed to confirm these trends.

2. **Stable Sectors**: Fields like pedagogy likely have steady demand nationwide, contributing to a more uniform distribution of job density in some regions.  

3. **Urban Industries**: Sectors such as Sales, Administration, and Data/IT dominate in urban centers like Stockholm, Gothenburg, and Malmö, influencing job density and vacancy distribution in these areas.  

4. **Localized Industries**: Manufacturing, Agriculture, and Military Careers tend to concentrate in low-population areas, where large employers can create significant job density spikes.  

To deepen this analysis, we propose focusing on:  
- **Data/IT Jobs**: To explore regional demand.  
- **Service Jobs**: To examine urban-rural trends, particularly in hospitality and retail.  
- **Healthcare Jobs**: Assessing healthcare’s influence on job density across regions.

### 4.4 Job Density by Sector: Health, Service, and Data/IT  
We create three **choropleth maps** to illustrate job density (jobs per 1,000 residents) for **Health**, **Service**, and **Data/IT** sectors across Sweden. Occupation fields are grouped, and job density is normalized by municipality. Each map uses distinct colors, with high-density municipalities labeled for clarity. Service jobs, such as sales, hospitality, cleaning, and security, often reflect local needs or tourism-driven demand. These maps reveal how job opportunities differ across sectors and regions in Sweden.


    
![png](project_files/project_14_0.png)
    


### Job Vacancies by Sector: Health, Service, Data/IT

The maps highlight job density (jobs per 1,000 residents) using thresholds of **19 for Health**, **8 for Service**, and **1 for Data/IT**.

- **Health Jobs**: Urban areas like Stockholm show lower density, while rural municipalities such as Vilhelmina and Överkalix have higher densities likely due to smaller populations. Jobs are concentrated in southern eastern Sweden, likely reflecting greater healthcare demand in aging populations. Health jobs partly explain the job concentration observed in section 4.2.

- **Service Jobs**: High density in tourist-driven areas like **Borgholm** and **Vimmerby**, while urban centers like Stockholm show lower density due to large populations diluting the metric.

- **Data/IT Jobs**: Concentrated in and around urban hubs like **Stockholm**, **Solna**, **Lund** and **Göteborg**, with rural areas largely excluded. Surprisingly, **Malmö** falls below the threshold despite its urban status. 


### 4.5 Data/IT Job Vacancies by Region

Given the potential interest in Data/IT roles, this section focuses on their regional distribution, as these jobs are concentrated in a few municipalities. A regional perspective is more practical for understanding commuting patterns and broader trends. The analysis filters and aggregates job data by region, cleans region names, and merges it with Sweden's boundaries to create a choropleth map, labeling regions with over 200 vacancies.


    
![png](project_files/project_17_0.png)
    


### Interpretation: Data/IT Vacancies by Region

Data/IT jobs are concentrated in urban areas, with **Stockholm** leading as the top tech hub with over 1,400 vacancies. **Västra Götaland** and **Skåne** also stand out as key centers. Most other regions have minimal opportunities, demonstrating the sector's urban focus and dependence on proximity to tech companies, universities, and skilled labor, which naturally aligns with expectations.

---

## 5. Observations & Discussion

### Potential Extensions

- Analyze other occupation fields to identify other patterns or causes behind their distribution.  
- Incorporate alternative data sources like **LinkedIn** or **Indeed**, especially for tech-related jobs. LinkedIn, for example, shows significantly more computer science roles compared to **Arbetsförmedlingen**.

---

## 6. Conclusion

By merging **Arbetsförmedlingen’s** open job listings with **population** and **municipality boundary** data, we gained an understanding of job opportunities across Sweden.

- **Highly Populated Areas**: While municipalities in and around Stockholm dominate in total job vacancies, they do not exhibit much higher jobs per capita maybe due to large populations which breaks expectations.
- **Healthcare's Influence**: Healthcare drives a significant portion of job vacancies, particularly in southern Sweden and specific rural municipalities. Its demand shapes job density and affects overall job availability analysis.  
- **Tourism-Driven Jobs**: Some municipalities with tourism-oriented economies show higher service job densities, particularly in sectors like hospitality and retail.  
- **Data/IT Jobs**: Data/IT vacancies are concentrated in major economic hubs like Stockholm, Västra Götaland , and Skåne, aligning with expectations.


---

