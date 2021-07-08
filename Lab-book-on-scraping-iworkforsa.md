Lab book on scraping iworkfor.sa.gov.au
================

# Purpose

This is a labbook detailing my efforts at scraping the jobs from [iworkfor.sa.gov.au](https://iworkfor.sa.gov.au/)

# Caveats

Its worth noting that this website only inlcudes *advertisments*. This is different to *jobs*!

It is different in that someone could (and often is) seconded into a position without an advertisment and is only employed in an advertised position after several years of already doing the job. Also, position titles change for all sorts of reasons as well as being added and removed so it would not be possible to infer tenure of job held from advertisments.

# Investigation

The site [iworkfor.sa.gov.au](https://iworkfor.sa.gov.au/) returns results wehn the `Search` button is clicked even if no values are entered in any of the fields. On 20210514 that was 678 jobs. I take that to be all the jobs currently listed.

However, only the first 20 jobs are listed on this page with a `Next` button allowing access to the next 20 jobs. Unfortunatly the URL does not change when the `Next` button is clicked. It would have been easier to scrape each page if each page could be accessed with a discreet URL. The unchanging URL suggests that the databse of jobs is being accessed using JavaScript and jQuery. Hence, to scrape all the jobs, it would be necessary to use `RSelenium` to trigger each page then to use `rvest` to scrape the HTML from each page.

This would return a dataframe such as:

<table class="table" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
JOB TITLE
</th>
<th style="text-align:left;">
REFERENCE NO
</th>
<th style="text-align:left;">
POSTING DATE
</th>
<th style="text-align:left;">
AGENCY
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
SCHOOL AND PRESCHOOL JOBS
</td>
<td style="text-align:left;">
295359
</td>
<td style="text-align:left;">
30/10/2017
</td>
<td style="text-align:left;">
DEPARTMENT FOR EDUCATION
</td>
</tr>
<tr>
<td style="text-align:left;">
ASO2 EMPLOYEE SERVICES OFFICER - TEMPORARY POOL
</td>
<td style="text-align:left;">
394370
</td>
<td style="text-align:left;">
06/08/2020
</td>
<td style="text-align:left;">
DEPARTMENT FOR EDUCATION
</td>
</tr>
<tr>
<td style="text-align:left;">
ASO3 COMMUNICATIONS AND BUSINESS SUPPORT OFFICER - TEMPORARY POOL
</td>
<td style="text-align:left;">
399845
</td>
<td style="text-align:left;">
19/09/2020
</td>
<td style="text-align:left;">
DEPARTMENT FOR EDUCATION
</td>
</tr>
</tbody>
</table>
It is worth noting that:

-   The factor `JOB TITLE` sometimes includes a standardised aspect such as the ASO level but also includes a free text aspect that is not standardised. This will mean that questions related to the `JOB TITLE` will have to be operationalised carefully because they are not neccesarily concistent, countable units.
-   Some rows actually indicate multiple jobs through the use of terms such as "TEMPORARY POOL". It might make more sense to talk about each row as an *advertisment* ralther than a *job*.

Despite these limitations...

If collected as a one off event, this alone would be enough to answer questions such as:

-   Which agencies are hiring the most?
-   What are the differences in job titles between agencies (this could include both the standardised aspects such as ASO level and the free text of the position title)?

If this was collected systematically it would be enough to answer questions such as:

-   When do different agencies recruit different job titles?
-   What are the most frequently recruited for job titles?

But there is more information about each advertisment on the page for the advertisment accessed through the URL associated with the `JOB TITLE`. Reading the page:<https://iworkfor.sa.gov.au/page.php?pageID=842&windowUID=0#report>, it appears that the URL for each advertisment on each page can be collected with some regex in the form of ???.

The advertisment specific pages appear to follow a similar sturcture...but not always (see: <https://jobs.education.sa.gov.au/page.php?pageID=786>). If the page follows the most common structure it will be possible to pull out:

-   Length of employment
-   Salary
-   Some notes similar to a position description
-   Attachments which comtain the full position description

So, here are the stages

1.  Step though the pages of all advertisments collecting

<!-- -->

1.  JOB TITL
2.  REFERENCE NO
3.  POSTING DATE
4.  AGENCY
5.  URL of advertisment page

<!-- -->

1.  Details from the advertisment page including:

<!-- -->

1.  All the source so that useful bits can be extracted from it later
2.  The attachments (if they exist) at this point because they might not be accesible once the advertisment is no longer there.

# Break it down

## Get the details from the
