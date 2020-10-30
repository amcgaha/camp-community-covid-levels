# Summer Camp COVID Risk:
# Assessing Current Conditions by Customer Location

## Introduction
I work for a [summer camp](www.greenriverpreserve.org) in North Carolina. Our most important project this year is developing a safe opening plan in the context of COVID-19. With many best practices available, our team would like to know how restrictive our policies and procedures need to be this summer.

We hope to strike a smart balance between classic camp freedoms and a safe environment. We want our restriction level to be informed by the epidemiological conditions in our campers' communities when we are making plans this spring. We also want to be able to adjust our plans as those conditions change throughout the summer.

Right now we lack a robust way to incorporate COVID-19 data into our decision-making. How will we determine our risk level in the planning phase, as well as during the summer as conditions continue to evolve?

Our campers come from many different states and counties in the United States, but we have significant clusters in certain communities. We know that in the United States there are differences in COVID-19 outbreaks across communities, making location-specific data an important analytical tool.

This project connects the geography of COVID-19 data, including cases by county and positivity rates by state, with a database of enrolled campers that includes their zip codes. The result is an interactive dashboard that visualizes current COVID-19 risks based on geography to inform our planning process over time.

A preview of the dashboard is embedded below. To visit the interactive version, [click here.](https://public.tableau.com/views/CampCovidAssessCountyDashboard/CampRiskProfile?:language=en&:display_count=y&publish=yes&:origin=viz_share_link)

<div class='tableauPlaceholder' id='viz1604081238501' style='position: relative'><noscript><a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ca&#47;CampCovidAssessCountyDashboard&#47;CampRiskProfile&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='CampCovidAssessCountyDashboard&#47;CampRiskProfile' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ca&#47;CampCovidAssessCountyDashboard&#47;CampRiskProfile&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /><param name='filter' value='publish=yes' /></object></div>                

## Sources
This project connects three categories of data: dynamic COVID data, basic geographic and demographic data, and the latest information on the campers who are enrolled in summer camp.

### Camper Data
Information on our campers comes from our company's data management system, called CampMinder. This company does not have an API, so the information has to be downloaded manually. This data is also sensitive, because it shows campers' names and addresses.

To keep these parts confidential, as well as make querying specific information seamless, I created a local database using PostgreSQL. Throughout this project, we query camper details from this database in SQL via the Python library SQLalchemy.


### Geographic Data
Prior to this project, the geographic information we stored on campers was limited to home addresses. Because COVID data is reported by health departments at the county level, we needed a way to connect zip codes to counties.  We use a [conversion table from the U.S. Department of Housing and Urban Development](https://github.com/amcgaha/camp-community-covid-levels/blob/main/zip_to_county_hud.csv) to make this conversion.

In order to calculate the number of cases per 100,000 people, a metric that standardizes numbers according to population, we needed to find the population for each state and county. We add the state population with a convenient [table from the U.S. Census bureau](https://github.com/amcgaha/camp-community-covid-levels/blob/main/state_pop_2019_census_bureau.csv). We add the [county population from this webpage](https://github.com/amcgaha/camp-community-covid-levels/blob/main/covid_county_population_usafacts.csv) recommended by the CDC. These sources are ideal because the data is stripped down and relatively easy to process.


### Dynamic COVID-19 Data
We connect to two popular datasets to obtain the latest COVID-19 information. These datasets are ideal because they are carefully managed by experts in data science and health reporting. They also update frequently, have strong documentation, and are easy to access.

For county-level data, we download the most recent dataset from [The New York Times.](https://github.com/nytimes/covid-19-data/blob/master/README.md) We focus on the number of cases that each county has reported most recently. This number will show up on our analysis as the cumulative number of cases per county.

For state-level data, we connect to [The Atlantic's COVID-19 Tracking Project](https://covidtracking.com/) using their API. This source is unique because it includes information on the number of tests conducted in each state and how many return positive. This allows us to calculate the state's positive test rate.

We also need sources to help us complete "sanity checks" and validate certain information. Whenever we have doubts or questions we will consult the [Johns Hopkins University Coronavirus Resource Center](https://coronavirus.jhu.edu/us-map), which has a wealth of reliable state and county information.

For more information about the challenges of COVID-19 data collection and interpretation, [read this article in Wired.](https://www.wired.com/story/covid-19-data-in-the-us-is-an-information-catastrophe/)

## Methods & Tools
We process our data using __Python__ and the __Pandas__ library. Each step is completed in a series of __Jupyter Notebooks__ so the process can be followed in detail.

The data about our campers is stored in a __PostgreSQL__ relational database. I created the database beforehand. In this project we update the database with the latest information on our campers who've enrolled this year. We access the database using __SQL__ via the Python library __SQLalchemy__.

Finally, we explore and visualize the results in __Tableau__.


## Procedure
### 1. Add Geographic Details to Database
In this step we import, clean, and combine geographic information, and then add it to our database. Because this information will not change over the course of the project, we only need to complete this step once.

[View Notebook](https://github.com/amcgaha/camp-community-covid-levels/blob/main/county_details_to_database.ipynb)

### 2. Update Database with Latest Camper information
Campers enroll in summer camp throughout the year, and so we need to continually update our database to include the most recent information on who is enrolled and where they come from. In this step we process that information from our company data management system into a form that matches our database. Then we upload it.

[View Notebook](https://github.com/amcgaha/camp-community-covid-levels/blob/main/update_campers_in_database_public.ipynb)

### 3. Collect & Combine COVID Data
Next, we download the COVID datasets, process them, and combine them with information queried from the camper database. We create an output that is ready for exploration and visualization.

[View Notebook](https://github.com/amcgaha/camp-community-covid-levels/blob/main/collecting_covid_data.ipynb)

### 4. Generate Dashboard
In Tableau, we import the file created in the last step. After first exploring what visualizations will be the most helpful in describing the data, we create dashboards and combine them into a story (a series of dashboards) that we can update at any point with new data.

[View Tableau Workbook](https://public.tableau.com/views/CampCovidAssessCountyDashboard/CampRiskProfile?:language=en&:display_count=y&publish=yes&:origin=viz_share_link)

## Product
