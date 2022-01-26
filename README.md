# :factory: Scraping Michigan's Database of Air Pollution Records

## Project Summary

Pollution happens. The Michigan Department of Environment, Great Lakes, and Energy (EGLE) tracks over [5,800 sources of air pollution](https://www.deq.state.mi.us/aps/downloads/SRN/Sources_By_ZIP.pdf) in the state.

The EGLE's Air Quality Division has a [database of directories](https://www.deq.state.mi.us/aps/downloads/SRN/) where records for each of these sources are stored. For my final project as part of Columbia's Data & Databases course, I scraped 18,000+ PDFs from this database and created a dataset of documents--like test restults, evaluation reports, and violation notices--by date. 

After converting the [EGLE's list of sources](https://www.deq.state.mi.us/aps/downloads/SRN/Sources_By_ZIP.pdf) of air pollution to a csv, I was able to include in my dataset 

* who the documents were issued to and
* the county, zip code and address where these sources are located 

The project resulted in [a robust, continually updating dataset](https://github.com/srjouppi/michigan-deq-auto-scraper) of documents from the EGLE database and [an interactive map](docs/map.html) of violations by zip code.

[Read more about the final product >>](https://github.com/srjouppi/michigan-air-polluters-dataset-map#the-final-product)

![violations-map.png](https://github.com/srjouppi/michigan-air-polluters-dataset-map/blob/main/screenshots/violations-map.png)

## :nut_and_bolt: Process

### :moneybag: Inside the database: the good stuff

When you get the [EGLE database](https://www.deq.state.mi.us/aps/downloads/SRN/) it looks like this:

![egle-source-directories.png](https://github.com/srjouppi/michigan-air-polluters-dataset-map/blob/main/screenshots/egle-source-directories.png)

Each folder is titled with a unique ID that is assigned by the state to each source of air pollution. 
And when you click those directories, then you get:

Inside these directories lie a treasure trove of information--test results, evaluation reports, and **violation notices** -- for each company the EGLE monitors or individual it has interacted with.

![violation-notice-example.png](https://github.com/srjouppi/michigan-air-polluters-dataset-map/blob/main/screenshots/violation-notice-example.png)

## :chart_with_upwards_trend: Scraping data for each source

I used the EGLE's [master list of sources](https://www.deq.state.mi.us/aps/downloads/SRN/Sources_By_ZIP.pdf) to generate a list of links of directories by inserting the source ID into the standard URL:

`www.deq.state.mi.us/aps/downloads/SRN/{source_id}`

The files in each directory are for the most part named predictably:

`{SOURCEID}_{TYPE OF DOCUMENT}_{DATE}.pdf`

[egle-source-directory-example.png](https://github.com/srjouppi/michigan-air-pollution-dataset-map/blob/main/screenshots/egle-source-directory-example.png)

:mag: I looped through the directory links and using Beautiful Soup and Regex I was able to extract the following data points from each PDF in the directory:

1. The source's ID
2. The code for the type of document *(ie 'FCE' for 'Full Compliance Evaluation' or 'VN' for 'Violation Notice')*
3. The date the correspondence was issued
4. The link to the pdf for future scraping

:file_folder: I saved the 18,000+ results to:

`csv/EGLE-AQD-documents.csv`

![source-documents-csv.png](https://github.com/srjouppi/michigan-air-pollution-dataset-map/blob/main/screenshots/source-documents-csv.png)

## :round_pushpin: Joining with name & location data

#### 1. PDF Conversion -- Camelot

`source-list-pdf-to-csv.ipynb`

The EGLE's master list of sources and their location information was saved in PDF format. I used the PDF parser Camelot to create a dataframe of the 72-page table of nearly 6,000 sources of air pollution.

#### 2. Data Cleaning -- Pandas

`source-list-csv-cleaning.ipynb`

The output from Camelot was a little wonky. Some columns were joined together, and it parsed the first page of the PDF different from the rest of the pages. It took some cleaning in Pandas.

#### 3. Geolocating -- Google API

`source-list-geocoding.ipynb`

Longterm, I wanted to visualize where all of the violations were occuring over time. I created a list of addresses from my clean source list and looped them through a Google API request to get geolocation data.

#### 4. The Great Merge -- Pandas

`source-list-documents-merge.ipynb`

I merged the documents I scraped from the EGLE database with the source list (aka directory) to create a dataset of over 18,000 documents, the date they were issued, the type of document, as well as the county, zip code and geolocation of where those sources are located.

## Analysis & mapping

#### Exploring the EGLE document dataset

`/analysis/egle-document-database-analysis.ipynb`

The dataset is quite robust. I did some initial analysis to show what was possible, answering questions like:

* Who had the most violation notices?
* Which zip codes and counties had the most violation notices?
* Which zip codes and counties had the most sources of air pollution?
* Which companies were tested most?
* Which counties had the lowest and highest testing rate?

#### Contextualizing with EPA emissions data

`/analysis/epa-nei-2017-analysis.ipynb`

Every three years the EPA conducts the [National Emissions Inventory.](https://www.epa.gov/air-emissions-inventories/2017-national-emissions-inventory-nei-data) I downloaded the latest data (collected 2017, released in 2020) to get some context for what Michigan air polluters were actually emitting.

The dataset actually contains the same EGLE issued source ID's, so I was able to get exact facility-level matches.

It's truly fascinating, and there is _so much_ to explore in this dataset. For my final project, I only ended up using a total count of Hazardous Air Pollutants by source ID -- a very crude data point, but nonetheless contextualizing. For instance, some zip codes had very few Violation Notices but facilities that collectively emitted hundreds of Hazardous Air Pollutants.

#### Contextualizing with census data

`acs2019-data-cleaning.ipynb`

For my final product -- a map of air pollution violations by zip code -- I wanted to include some demographic data for each zip code. I pulled race and poverty data from the Census Bureau's 2019 American Community survey.

#### Visualizing air pollution violations with a map
`violations-dataframe-map.ipynb`

My initial question when tackling this project was - where are the most violations taking place in the state of Michigan? Is there a connection to race or poverty level? I wanted to visualize the violations over time, perhaps by year, but needed more training in d3 to accomplish this.

I decided to [map the all-time total number of violations per zip code](https://srjouppi.github.io/michigan-air-pollution-dataset-map/) and include demographic information from the American Community Survey. I grouped the data by percentage of non-White popultion. When the map first loads you can see all the data at once, but then you can select a group:

* < 25% non-White
* 25% - 50% non-White
* Majority non-White

My hypothesis -- that majority non-White zip codes would be most likely to have the most violations -- did not always line up with the data. I think that a more interesting comparison would be to categorize the zip codes by poverty level or population.

## The Final Product

#### :gem: 1. The Master Dataset 

`csv/EGLE-AQD-documents-source-info.csv`

A 18,000+ record dataset of documents by source name, county, zip code and location. 

For the **_most up-to-date version of this dataset_** visit the output folder of [my automatic scraper](https://github.com/srjouppi/michigan-deq-auto-scraper), that searches the EGLE database daily for updates and adds them to the datasets.

![final-dataset.png](https://github.com/srjouppi/michigan-air-pollution-dataset-map/blob/main/screenshots/final-dataset.png)

#### :notebook: 2. The Source List

`csv/EGLE-AQD-source-list-final.csv`

A machine readable list of sources of air pollution tracked by the EGLE's Air Quality Division. 

#### :mag_right: 3. The Map

[An interactive map](https://srjouppi.github.io/michigan-air-pollution-dataset-map/) that allows you to explore the number of air pollution violations per zip code as well as see demographic information for that zip code.

I have high hopes to make this into a much more robust and useful interactive map using d3. Until then, happy exploring!


```python

```
