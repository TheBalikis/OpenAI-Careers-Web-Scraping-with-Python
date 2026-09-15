 # OpenAI Careers Job Listings - Ashby

---

### Project Overview:- Objectives and goals of the scraping Project
Extracting data from the web can often feel overwhelming. Web data retrieval allows us to automate the collection of structured information from websites, making it easier to organize, analyze, and generate insights from information that may otherwise require manual collection.

The goal of this project is to automate the retrieval of publicly available job listing data from the OpenAI Careers platform, transform the extracted data into a structured Pandas DataFrame, clean and prepare it for analysis, export the dataset to CSV for reuse or further analysis, and analyze it to identify patterns in hiring demand, job locations, workplace types, and advertised compensation.

---

## Tools and Libraries:
- **Jupyter Notebook**: environment used for writing and running code.
- **Requests**: retrieving data from the web.
- **Pandas**: structuring, cleaning, and analyzing the extracted data.
- **Matplotlib**: creating visualizations.

## Target Website Information: [URL](https://jobs.ashbyhq.com/openai)
  ---

  ## Data Extraction Logic:

* Fetching the Data: The script uses requests.get() to send a request to OpenAI's public job-posting endpoint.
* Parsing JSON: The response is received as JSON data, containing the full list of job postings.
* Extracting Data: The script loops through each job record and extracts relevant fields such as title, department, location, and compensation.
* Structuring Data: The extracted records are stored in a Python list, then converted into a Pandas DataFrame for easy analysis.
* Cleaning Data: The DataFrame is cleaned to handle missing values, inconsistent formatting, and other data quality issues.
* Export Data to CSV

---

## Viz

[Exploratory Report](your-link-here)

---

## Code:

```python
# first, import requests, pandas and matplotlib
import requests
import pandas as pd
import matplotlib.pyplot as plt
```

```python
# Retrieve the data
url = "https://api.ashbyhq.com/posting-api/job-board/openai?includeCompensation=true"
response = requests.get(url)
response.status_code
```

```python
# Convert response to JSON
data = response.json()
len(data["jobs
```

```python
# Extract the Job Information
jobs = data["jobs"]

records = []

for job in jobs:
    compensation = job.get("compensation") or {}
    salary_components = compensation.get("summaryComponents") or []

    salary = next(
        (
            component
            for component in salary_components
            if component.get("compensationType") == "Salary"
        ),
        {}
    )

    records.append({
        "Job Title": job.get("title"),
        "Department": job.get("department"),
        "Team": job.get("team"),
        "Employment Type": job.get("employmentType"),
        "Location": job.get("location"),
        "Workplace Type": job.get("workplaceType"),
        "Published Date": job.get("publishedAt"),
        "Salary Min": salary.get("minValue"),
        "Salary Max": salary.get("maxValue"),
        "Job URL": job.get("jobUrl")
    })

df = pd.DataFrame(records)
```

The extracted dataset was checked for:
* Duplicate records
* Duplicate job URLs
* Missing values
* Date formatting
* Salary midpoint calculation
* Unspecified workplace types


```python
# The published date was converted to a datetime format, and a salary midpoint was calculated from the minimum and maximum advertised salary.
df["Published Date"] = pd.to_datetime(df["Published Date"])
df["Salary Midpoint"] = (
    df["Salary Min"] + df["Salary Max"]
) / 2
df["Workplace Type"] = df["Workplace Type"].fillna("Not Specified")
```

```python
# convert dataframe to csv file
df.to_csv("openai_job_listings.csv", index=False)

