# Scraping the Michigan DEQ's Database of Correspondence with Air Polluters :factory:

Pollution happens. The Michigan Department of Environmental Quality (MDEQ) tracks over [5,800 sources of air pollution](https://www.deq.state.mi.us/aps/downloads/SRN/Sources_By_ZIP.pdf) in the state.

The MDEQ has a [database of directories](https://www.deq.state.mi.us/aps/downloads/SRN/) containing correspondence with companies and individuals. Each folder is titled with a unique ID that is assigned to each entity by the state.

![MDEQ Source Directories](https://github.com/srjouppi/michigan-deq-doc-scraper/blob/main/screenshots/mideq-source-directories.png)


## Inside the directories: the good stuff :moneybag:

Inside these directories lie a treasure trove of information for each company the MDEQ monitors or individual it has interacted with. This includes:

* test results
* evaluation reports
* **violation notices** 
* and more

![Violation Notice Example](https://github.com/srjouppi/michigan-deq-doc-scraper/blob/main/screenshots/violation-notice-example.png)

---

The files are for the most part named predictably, including the **unique id**, the **type of document** and the **date** it was issued.

The directory URLs are predictable as well, and only require switching out the source ID.

![Source Directory Example](https://github.com/srjouppi/michigan-deq-doc-scraper/blob/main/screenshots/source-directory-example.png)

---

## Scraping data  for each company :chart_with_upwards_trend:

I used the MDEQ's [master list of sources](https://www.deq.state.mi.us/aps/downloads/SRN/Sources_By_ZIP.pdf) to generate a list of links of directories by inserting the source ID into the standard URL:

`www.deq.state.mi.us/aps/downloads/SRN/{source_id}`

:mag: Using Beautiful Soup and Regex I was able to extract the following data points from each PDF in the directory:
1. The source's ID
2. The code for the type of document *(ie 'FCE' for 'Full Compliance Evaluation' or 'VN' for 'Violation Notice')*
3. The date the correspondence was issued
4. The link to the pdf for future scraping

:file_folder: And saved the 18,000+ results to:

`MDEQ-SRN-documents-12092021.csv`

![Final CSV](https://github.com/srjouppi/michigan-deq-doc-scraper/blob/main/screenshots/source-documents-csv.png)
