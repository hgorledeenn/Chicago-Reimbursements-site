# City of Chicago Employee Reimbursements

An auto-updating, scraper-driven web resource

Created by **[Holden Green](https://www.holdengreen.me)** in December 2025 <br>
Columbia Journalism School, Foundations of Computing (Final Project)

<br>

## The Project
Reimbursements paid to employees of the City of Chicago are made public in [this dataset](https://data.cityofchicago.org/Administration-Finance/Employee-Reimbursements/g5h3-jkgt/explore/query/SELECT%0A%20%20%60voucher_number%60%2C%0A%20%20%60amount%60%2C%0A%20%20%60payment_date%60%2C%0A%20%20%60vendor_name%60%2C%0A%20%20%60description%60%2C%0A%20%20%60department%60%0AORDER%20BY%20%60payment_date%60%20DESC%20NULL%20FIRST/page/filter). The data is updated weekly, and old transactions are stored up to Jan. 1 of the previous calendar year. But the interface is a little bit finicky (especially if you’re not a data journalist) and it can be a little bit slow to run queries and get results.

The goals of this project were twofold:

**(1)&ensp;Present the reimbursement data in a way that increases functionality and load speeds, while not compromising on the specificity of the data in the original dataset**

I increased readability in my visualizations through the use of color-coded variables, opening with a summary table as opposed to raw row-by-row data and removing unnecessary elements, allowing the viewer to quickly focus on the important pieces of information.

**(2)&ensp;Create a place to house more years of data than those included in the public data set**

The data shared in the public dataset changes year to year (e.g. on Jan. 1, 2026, all of the 2024 transactions will be removed). I circumvented this limitation by creating my own csv backup which is only added to, not written over entirely, when new scraped data is found.

I am also in the process of filing FOIA requests with the Chicago Department of Finance (the department that manages this dataset) for employee reimbursement data through 2011. Once I receive the historical data, I will add it to my site, which will allow analysis of trends on a much longer time scale than before.

<br>

## Data Collection and Wrangling
I created a scraper (with this [Magical Prompt](https://gist.github.com/jsoma/dd28675ab27c5d46b6261b93f1252307) from Jonathan Soma) to pull data from this [public dataset](https://data.cityofchicago.org/Administration-Finance/Employee-Reimbursements/g5h3-jkgt/explore/query/SELECT%0A%20%20%60voucher_number%60%2C%0A%20%20%60amount%60%2C%0A%20%20%60payment_date%60%2C%0A%20%20%60vendor_name%60%2C%0A%20%20%60description%60%2C%0A%20%20%60department%60%0AORDER%20BY%20%60payment_date%60%20DESC%20NULL%20FIRST/page/filter) of Chicago Employee Reimbursements. The Chicago Data Portal does have an API – which I would use in future similar projects –  but the specific goal of this class assignment was to create a scraper-driven website.

Once I had the data, I did a few wrangling steps, mostly data-cleaning (removing the “$” symbol from before figure amounts and renaming column headers) and a loop to generate a csv file of individual transactions for each of the departments in the dataset.

<br>

## What I Learned
For this project, I put to work a lot of what I learned in my Foundations of Computer class (see other classwork in this [repo](https://github.com/hgorledeenn/CJS-founds-of-comp)). In addition to skills from class, like creating functional scrapers, using for loops in data manipulation and general Pandas knowledge, I challenged myself and learned several new skills for this project.

(1)&ensp;I learned the `.concat` and `.drop_duplicates` command pair, which enabled me to read in stored data and combine it with newly scraped results

```python
df_orig = pd.read_csv("employee_reimbursements.csv")
df_safe = pd.concat([df_orig, df_new], ignore_index = True)
df_safe = df_safe.drop_duplicates(subset=['voucher_name', 'amount', 'payment_date', 'description'], keep='first')
df_safe.to_csv("employee_reimbursements.csv", index=False)
```
(2)&ensp;I learned how to iterate through dataframe groups, which enabled me to succinctly create individual csv files for the transactions by each department

```python
groups = df.groupby(by="DEPARTMENT")
keys = groups.groups.keys()

for i in keys:
   dept = groups.get_group(i)
   name = i.lower()
   name = name.replace(" ", "-")
   name = name.replace("'", "")
   dept.to_csv(f"docs/dept-csvs/{name}.csv", index=False)
```

(3)&ensp;I learned this structure for defining a function, which enabled me to automatically add the html formatting to hyperlink each department name to its transaction list page

```python
def make_link(dep):
   folder = (
       dep.lower()
       .replace(" ", "-")
       .replace("'", "")
   )
   return f"<a href="https://hgorledeenn.github.io/Chicago-Reimbursements-site/{folder}/index.html">{dep}</a>"

df_dept_sum['DEPARTMENT'] = df_dept_sum['DEPARTMENT'].apply(make_link)

df_dept_sum.to_csv("docs/by_department_summary.csv", index=False
```

<br>

## What I Would Add Next Time
I learned a lot through this project and am happy that I challenged myself to create multiple pages and display the data in this way (the assignment only required us to make one visualization on one page). While the website does achieve the goals I set for myself, it also gives me ideas about more functionality that could be included.

In future projects, I would like to add more ways for a user to interact with the data. Some ideas I have for increased functionality include:

- <ins>A more powerful search tool</ins> to allow for the use of advanced search parameters
- <ins>Filtering options</ins> like the ability to specify date or total amount ranges
- <ins>More/varied visualizations</ins>, for instance showing change over time line charts to demonstrate how employee reimbursement spending has fluctuated historically.
- <ins>Further analysis of spending</ins> using natural language processing (specifically of the   `description` column in the data set). This could lead to interesting insights and allow for more complex visualizations like spending by category or analysis of employee travel locations.
