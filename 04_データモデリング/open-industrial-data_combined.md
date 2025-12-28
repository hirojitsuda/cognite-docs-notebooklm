# open-industrial-data Documentation
Generated from: open-industrial-data



<!-- SOURCE_START: open-industrial-data/contributions\README.md -->
## File: open-industrial-data/contributions\README.md

# Contributions

Open a pull request to add your analysis here.

<!-- SOURCE_END: open-industrial-data/contributions\README.md -->

================================================================================


<!-- SOURCE_START: open-industrial-data/contributions\smoothing\README.md -->
## File: open-industrial-data/contributions\smoothing\README.md

# Smoothing

[Smoothing](https://en.wikipedia.org/wiki/Smoothing) is an important technique in data analysis:
one is often interested in a general trend and not fine fluctuations, which tend to correspond to measurement noise.

[This notebook](./smoothing.ipynb) introduces the concept by example.


<!-- SOURCE_END: open-industrial-data/contributions\smoothing\README.md -->

================================================================================


<!-- SOURCE_START: open-industrial-data/README.md -->
## File: open-industrial-data/README.md

<a href="https://cognite.com/">
    <img src="https://github.com/cognitedata/cognite-sdk-python/blob/master/img/cognite_logo.png" alt="Cognite logo" title="Cognite" align="right" height="80" />
</a>

# [Open Industrial Data](https://openindustrialdata.com/)

>A live stream of industrial data, continuously available and free of charge. Removing the obstacle of data gathering for students, startups & researchers. 
Take it and learn with it. Show us what you find. 
We’re excited to see what's possible.

# You asked for real data?

![Valhall field](https://www.akerbp.com/wp-content/uploads/2016/09/1455710061745.jpg)

Open industrial data originates from a single compressor on Aker BP’s Valhall oil platform in the North Sea. Aker BP selected the first stage compressor on the Valhall because it is a subsystem with clearly defined boundaries, rich in time series and maintenance data. 

The data set available in the Cognite Data Platform includes time series data, maintenance history, and Process & Instrumentation Diagrams (P&IDs) for Valhall’s first stage compressor and associated process equipment: first stage suction cooler, first stage suction scrubber, first stage compressor and first stage discharge coolers. In addition, data from the compressor’s lubrication system, dry gas seal system and condition monitoring system (temperature and vibration) will be available.

Interested to read more about the project? Read the [white paper](https://cognite.com/media/1145/open-industrial-data-cognite-akerbp.pdf).

# [Cognite Workshop](workshop)
*Events and training sessions to build on the open industrial data ecosystem.*

* Get to know the ins- and outs of the CDP SDK and other ways to access data stored in Cognite data platform.
* Dive into open industrial data, exploring an equipment in the first stage compressor train on the Valhall platform.

# [Contributions](contributions)
A collection of interesting analyses by students, civil data scientists, customers, partners and finally Cogniters.



<!-- SOURCE_END: open-industrial-data/README.md -->

================================================================================


<!-- SOURCE_START: open-industrial-data/workshop\README.md -->
## File: open-industrial-data/workshop\README.md

<a href="https://cognite.com/">
    <img src="https://github.com/cognitedata/cognite-sdk-python/blob/master/img/cognite_logo.png" alt="Cognite logo" title="Cognite" align="right" height="80" />
</a>

# Introduction to Open Industrial Data

Took a course on python programming in first year? Machine learning guru? Evangelical pythonista? Information designer? IoT geek? Welcome, we've got a spot for you all. We need your help to analyze and visualize the industrial world!

Speak up, ask questions and work together. We can't wait to see what you come up with!

![Cogniters](static/Cogniters.jpg)

# Instructions
Although this material references data science topics, we have designed it to cover multiple levels of experience. We start together, but then break out into different specializations, a lot like we do in our teams today at Cognite!

## Part 1: Get set up
- Head over to [openindustrialdata.com](https://openindustrialdata.com) and log in with a google account.
- Read up on the project in this [white paper](https://cognite.com/media/1145/open-industrial-data-cognite-akerbp.pdf). We need your help to analyze the data!
- Once logged in, head to the [Getting Started](https://openindustrialdata.com/get-started/) to generate an API key. Treat this key as a password, store it somewhere secure!
- Watch the [instructional video](https://player.vimeo.com/video/299176372) and log in to [Cognite Operational Intelligence](https://opint.cogniteapp.com/publicdata/)!


## Part 2: Explore the 1st Stage Gas Export Compressor train on the Valhall platform
![System overview](static/SystemOverview.png)
We do infographics slightly differently here at Cognite: with real time data!
- Look through the infographics to find your way to the [System Overview](https://opint.cogniteapp.com/publicdata/infographics/-LOHKEJPLvt0eRIZu8mE)
- Pick a subsystem that you would like to analyze further (labelled on the diagram, e.g. '23-VG-9101') and write down the tag name.

## Part 3: Asset data dive
Let's get stuck into the data for our chosen asset. The Cognite [Python SDK](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/ ) gives us full access to the industrial data.
The notebook "Part 3 - Asset data dive.ipynb" walks us through the basics of how industrial data is represented in CDP.

If you have a notebook environment set up on your laptop, simply clone down this repo and get to work.
Otherwise, we recommend [Google Colab](https://colab.research.google.com/notebooks/welcome.ipynb#recent=true). Simply reference the url of the notebook in github from colab, or open the notebook [directly](https://github.com/cognitedata/open-industrial-data/blob/master/workshop/Part_3_Asset_Data_Dive.ipynb).

- Run the notebook from start to finish, exploring the asset hierarchy and timeseries for the asset you chose in Part 2 (use <shift+enter> to step through the cells and run the code snippets).

## Part 4: Specialization
Time to split into specializations! Either build an engaging visualization in Operational Intelligence, or continue writing code!

### Part 4a: Real time data visualization
In your notebook from part 3, you generated a list of timeseries associated with your asset of choice. Let's head back over to [Operational Intelligence](https://opint.cogniteapp.com/publicdata/infographics) and liberate that data by creating an engaging visualization!
- Research your asset of choice ([ABB Oil & Gas Production Handbook](https://library.e.abb.com/public/34d5b70e18f7d6c8c1257be500438ac3/Oil%20and%20gas%20production%20handbook%20ed3x0_web.pdf))
- Draw a simplified representation or find a diagram online
- Create an infographic in OpInt. Be sure to select "Aker BP" when adding time series (tags).
- Using the timeseries descriptions you loaded in `df_asset_children_timeseries` in your notebook from Part 3, identify and add time series to your infographic in the correct locations.
- Publish your dashboard to the world!

### Part 4b: Data science
A bit excited about that time series data you saw in Part 3? We get it. Here are a couple ideas to get you started:
- Data quality investigation
- Graph analytics
- Supervised sensor prediction
- Unsupervised anomaly detection

We have [a notebook](https://colab.research.google.com/github/cognitedata/open-industrial-data/blob/master/workshop/Part_4b_Data_Science_Example.ipynb) to get you started.

## When you're done
Thanks for participating! Share your work with the world and contribute to the vision of Open Industrial Data!

And don't forget that, like most other companies, we look at Github profiles when we recruit for the Data Science, and strongly encourage you to build up a history of **unique** work. It could be your foot in the door to your dream job :)


<!-- SOURCE_END: open-industrial-data/workshop\README.md -->

================================================================================


<!-- SOURCE_START: open-industrial-data/workshop\What_is_Google_Collab.md -->
## File: open-industrial-data/workshop\What_is_Google_Collab.md

<a href="https://cognite.com/">
    <img src="https://github.com/cognitedata/cognite-sdk-python/blob/master/img/cognite_logo.png" alt="Cognite logo" title="Cognite" align="right" height="80" />
</a>

# Using Google Collab

All of the notebooks created for workshops and hackathons have been developed using Google Collab. Google Collab can be thought of as a Jupyter notebook in the cloud.

Google Collab comes pre-installed with many necessary packages or it allows others such as the `cognite-sdk`
package to be installed on the cloud instance.

It also solves the issue of people having different python environments installed on their local machine, and works on both Mac and Windows.

The notebooks can also be exported to GitHub for convenience. 

<!-- SOURCE_END: open-industrial-data/workshop\What_is_Google_Collab.md -->

================================================================================
