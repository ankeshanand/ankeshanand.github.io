---
layout: post
title: "GSoC: Midterm Status-Quo"
description: A Midterm Report of my Google Summer of Code Project with BRL-CAD
modified: 2014-06-26 02:47:38 +0530
category: posts
tags: [gsoc, brlcad]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: 
---

##Overview

Ahoy, this happens to be my first blog post on the new blog. I am working with [BRL-CAD](http://brlcad.org/) this summer as part of
[Google Summer of Code](http://www.google-melange.com/gsoc/homepage/google/gsoc2014)
to develop a web service that provides analysis of a system's performance based upon metrics from BRL-CAD's Benchmark Suite.
The end-goal is to deploy a database and visualization framework that provides multiple mechanisms to add new benchmark
logs into the database, displays performance results cum comparisons and produces various forms of visualizations for the aggregate data.
The how's and why's are elaborated in the project proposal [here](http://brlcad.org/wiki/User:Ankeshanand/GSoC14/proposal).

Quick summary of the progress so far:

* **File Uploads:** A web interface to upload the Benchmark logs, files are transferred to an archive folder. File validations and drag n drop are supported.
* **Visualizations on Aggregate Data:** Flot has been used to plot the aggregate data from the database. An initial roster of the plots that
comparing serveral parameters has been implemented.
* **Processing Uploaded Files:** I have leveraged the script from the previous GSoC project, which needs to run as a cron job on the server.
* **Performance Results and Comparison:** Lately, I have been working on serving immediate performance results on log submission,
the UI mockups are ready and the next phase of the project would be to complete this module.

## The Technical Stuff

### Architecture/Code Structure
The repository for the project resides [here](https://github.com/BRL-CAD/benchmark) on Github. It's built primarily with Django and leverages Python scripts from the previous project for parsing
and several other utilities.

{% highlight text %}
benchmark/
├── doc/                         # Includes documentation from the earlier project
├── fileupload/                  # Django app to handle file uploads
|    ├── static/                    # Images, CSS and Javascript files for the app. A typical assets dir.
|    ├── templates/                 # Contains a Django Template for the front-end page of the app
|    ├── admin.py                   # Registers the app with Django Admin
|    ├── models.py                  # A model is basically an uploaded file here, includes save and delete functions
|    ├── response.py                # Dumps the HTTP response into JSON
|    ├── serialize.py               # Serializes a file instance into a dictionary object.
|    ├── tests.py                   # Unit tests for the app, just contains a stub test at the moment.
|    ├── urls.py                    # URL Patterns for the app
|    └── views.py                   # Responsible for putting everything together and then displaying the output via a Django template.
├── libs/                        # Database handlers for the parser, Parser scripts and IMAP extractor
├── plots/                       # Django app for visualizations of aggregate data
|    ├── static/                    # Stylesheets and JS files
|    ├── templates/                 # Index and Chart front-ends
|    |   ├── flot-chart.html            # Renders the chart using Flot.
|    |   └── index.html                 # Index page of the plots module.
|    ├── admin.py                   # Registers the app with Django Admin
|    ├── data.py                    # Data extractor methods
|    ├── models.py                  # Models that mimic the database schema
|    ├── tests.py                   # Unit tests for the project, currently a stub
|    ├── urls.py                    # URL patterns for the app
|    └── views.py                   # Pass relevant information to templates for displaying the charts and the index page.
├── results/                     # Django app for showcasing and comparing performance results
├── scripts/                     # Utility scripts
|    ├── parse.py                   # A wrapper around the parser library in libs/
|    └── process_from_queue.py      # A scripts to process uploaded files from the queue folder.
├── sql/                         # Legacy MySQL script for implementing the database schema
├── config                       # Project configuration settings
├── manage.py                    # The default manage.py script from Django
├── requirements.txt             # Dependencies
├── settings.py                  # Django Project settings
└── urls.py                      # URL Patterns for the project.
{% endhighlight %}

### File Uploads

All the files are uploaded to a queue folder from where they are further processed. A web interface to support file uploads
has been implemented now, and this would be extended to an HTTP API that hooks with the benchmark script for automatic uploads.
The Django app supports file validations and has Drag n Drop support. There are admin controls to view a list of files as well.
<figure>
    <a href="/images/upload-shot.png"><img src="/images/upload-shot.png"></a>
    <figcaption>The upload interface. Click for an enlarged version.</figcaption>
</figure>


### Visualization on Aggregate Data

The plots app is responsible for aggregating data from the database and then displaying the visualizations. Visualizations
have been built using [Flot](http://www.flotcharts.org/), this is a deviation from the original plan and this
[document](https://github.com/BRL-CAD/benchmark/wiki/Selection-of-an-appropriate-Javascript-Charting-Library) describes why we chose to go with Flot.

A data-extractor method has been implemented for each of the plots. The data-extractor methods use Django ORM methods to interact and
fetch the data from the database. The Django template sends a AJAX request for the data, and the views call the appropriate data-extractor
methods based on the chart name.  The data-extractor methods then build the appropriate representation of the data in the form of a dictionary.
This dictionary is then dumped into a JSON object and sent to the data URL.The data is used to assign the options in Flot, which renders the chart
finally using these options.
<figure>
    <a href="/images/plots-shot.png"><img src="/images/plots-shot.png"></a>
    <figcaption>The plots interface. Click for an enlarged version.</figcaption>
</figure>


### Numbers
Git commands show a total of 46820 insertions and 1562 deletions but a bulk of them are new CSS and JS files required for the
project. I tried to get a more reasonable figure from some bash one-liners.

* **Total Commits:** 89
* **Files Changed:** 106 (Includes files deleted from the previous project and new static CSS, JS files)
* **Lines of code added/refactored:** 1800 approx. (includes only the .py and .html files)

### Next Steps

* **Performance Results and Comparisons:** The idea is to show the results of a submitted log immidiately and allow comparisons
against the database against several metrics. The result of each submitted log would be displayed on a unique URL which can be
added to a user's account. This should be carried out for logs submitted via all the mediums.
* **Extracting E-Mail logs**
* **Testing and Deployment**
* **Project Documentation**


