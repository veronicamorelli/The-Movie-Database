# The-Movie-Database

What can be said about the success of a film before it is produced? Are there specific formulas for achieving it? Is it possible to predict whether a film will have a high rating, considering some of its characteristics? These are the questions we asked ourselves and prompted the creation of the dataset. As their production cost, in most cases, exceeds well over $100 million dollars, knowing how to answer these questions can bring very useful and important for film productions, but it also represents a source of great interest for fans.

## Goal

The goal of this project is to create a dataset containing information of the most popular films from 2010 to the present time. Thus, in addition to the titles of the films, we want to add characteristics regarding the actors, with the corresponding role played, but also the awards won, the ratings and box office obtained, the film studios that produced them and the directors involved, the countries of production, the genres to which they belong, the keywords and reviews associated. Also, the languages available, their duration, and a list of similar films. The end user for whom this database is intended is the one who is interested in analyzing and viewing films, so that he/she can make further insights and comparisons, identifying relevant or non-relevant features. The presence of reviews, moreover, can be used as a starting point for future explorations such as sentiment analysis and topic extraction.

The project is developed in the following steps:
1. Data Acquisition: via API and Scraping
2. Data Cleaning
3. Data Quality
4. Data Integration
5. Data Storage 
6. Data Exploration

# Steps

## 1. Data Acquisition

### 1.1 API (Application Programming Interface)

The data acquisition phase is performed in two different stages, one consequential to the other. Documentation can be found at the following link: https://imdb-api.com/api. First step is to use functionality of the Advanced Search API found at: https://imdbapi.com/api#AdvancedSearch-header. Second step is to use the functionality of the Title API found at: https://imdbapi.com/api#Title-header. To use the API, one had to create an IMDB account from which one one obtained a key, called the API Key. It is specified that for each key you can make only 100 requests per day, consequently, in the second step one had to create several accounts, then several API Keys to be able to download all the data in a reasonable time. At the end of this first phase of downloading data, a documentary database containing 14365 documents is obtained, each of them related to a film.
The attributes for each movie can be found in "Appendix A - IMDB Movie Attribute Descriptions before the Data Cleaning step".

**Data Storage**

The output of phase 1 consists of a list of ids, each related to a film. The list is stored locally in a text file (extension .txt). The output of Phase 2 consists of a single document database in json format containing 14365 documents, each related to one film. To obtain this complete dataset we take advantage of the capabilities of the library python `pymongo` as follows. From python we connect to MongoDB by creating a database and a collection in which to store the data. After this, one performs the requests for data with the Title API. Each time a request is executed you store the result on MongoDB using the `collect.one()` function. This procedure makes it possible to store information in a document database that is continuously updated by new data requested via API. Considering that MongoDB limits the database to a size of 17MB, two different databases are created in which to store data. 
At the end of the storage phase, a document dataset in json format is obtained.

### 1.2 Web Scraping

The second data source, is extracted through Web Scraping. More specifically, this technique is applied to the web page https://www.rogerebert.com, where reviews are given official reviews of the U.S. film critic Rogert Ebert. This is a web page with "infinite scrolling"; content, in fact, is published within the same page  without being forced to generate new ones for different films. Exploration of the page by the user is facilitated, while the scraping operation is more complex. Through  detailed inspection of the source page, we were able to extract all reviews. The library used is `BeautifulSoup`, aimed at extracting data from HTML and XML files. 
In addition, the requests library was also installed so that we could perform requests HTTP through Python code. Downloading of data is performed in two iterations: the firse one is done on the number of pages, the second one is on the links obtained in the previous step.

**Data Storage**

A relational database was created in csv format containing 6525 movie titles with the corresponding review. The database was stored locally on the computer personal.

## 2. Data Cleaning

After storing the data on MongoDB, we perform the Data Cleaning phase to fix the dataset and keep only the variables of interest. This phase is necessary to make the dataset useful for later analysis. The first data source consists of a document database where each document contains 46 attributes. Before proceed to the data cleaning phase on the attributes, it is noted that some documents are null and have *ErrorMessage* attribute value other than *None*; this means that during data acquisition there there were problems related to these documents. Considering that there are 600 documents with error, it is decided to delete them and proceed to the next steps. Regarding the attributes, it is specified that many of them were not retained because they were considered irrelevant to the description of the characteristics of a film. For example, the image attribute containing the link to the image cover of the movie, the id attribute relating to the identifier of the movie, the imDbRatingsVotes attribute related to the number of voters of the movie, etc. In general, the retained attributes relate to the title of the film, the cast, the genre, the languages spoken, the country or countries in which the film is produced, the company(ies) of production, release date, length, scores, earnings, plot, a list of keywords describing the film, a list of similar films, and any awards won. In total, 21 attributes compared to the initial 46.For many attributes that you decide to keep, changes need to be made. For example, the values of the imDbRating, runtimeMins, year, and metacriticRating attributes are transformed from string to numeric values, in order to do quantitative exploratory analysis later. The values of the attributes genres, directors, writers, languages, companies, and countries are transformed from string to list. The values of the attributes actorList and similars are dictionary lists: for both of these, we remove irrelevant keys  relevant, such as image and id. The value of the boxOffice attribute is a dictionary consisting of 4 keys related to the budget and different earnings obtained by the movie; these values should be numbers integers, instead they are strings also containing alphanumeric values such as the '$' indicating the currency. They use some regular expressions to eliminate alphanumeric characters, thus transforming the values from strings to integers.

## 3. Data Quality

### 3.1 API

In the first case, the quality dimension of **completeness** related to missing data is assessed. Knowing that in document databases the schema is free, there is no requirement to maintain missing data. In this case, one goes and eliminates for each document the values that are missing. This procedure reduces the dimensionality of the database thus making future processing and analysis faster. <br>
For the attributes awards, genres, directors, writers, languages, companies and countries, title, plot the data missing occurs when the value associated with the key is an empty string. For the attributes contentRating, imDbRating, runtimeMins, year, metacriticRating, releaseDate the missing data is had if the value is None. For the attributes actorList, keywordList, and similars the missing data is had with an empty list. For the attribute boxOffice the missing data is with an empty dictionary. If the attribute has a missing value you delete.

As a second dimension of quality, it is decided to assess consistency. 
The consistency checks that have been performed are listed:
- The releaseDate attribute is a string containing the release date of the movie, its format is YYYY-MM-DD. It is checked that the year is between 2010 and 2021, as this is the time frame time frame chosen to download the data. In addition, it is verified that the month is between 1 and 12 and the day between 1 and 31. Since these values are in string format, they are transformed to integer in order to be able to evaluate the consistency constraints. The output shows that only two movies do not meet the constraints since the year in which they were released is 2022.
- The imDbRating attribute must be between 0 and 10, since it is on this range of values that the film's rating is expressed. No films have values outside this  range. A similar operation was performed for the imDbRating attribute included in the Similars attribute. Again, the constraint is respected.
- The runtimeMins attribute expresses the duration of the movie in minutes. "The Academy of Motion Picture Arts and Sciences, the American Film Institute, and the British Film Institute define a feature film (feature film) as a minimum of 40 minutes." Since the films were downloaded under the criterion of being feature films (feature films), a condition is imposed that they have a duration greater than or equal to 40 minutes. The output shows that there are 49 movies that are less than 40 minutes in length. It is therefore useful to investigate why such films were downloaded despite the condition imposed in the API. 
- The budget($) attribute containing in the boxOffice attribute expresses the budget set for the production of a movie. One wants to see how many and which movies have a very low budget and less than $10000. Such a low budget might raise some suspicions about the film and its nature. The output shows that 2868 films have a budget less than $10000.
- The boxOffice attribute is a dictionary containing a maximum of 4 keys, 3 of them pertaining to the following attributes: openingWeekendUSA related to dollarearnings in the first weekend after the film's release, grossUSA related to the total dollar earnings in the United States (US), cumulativeWorldwideGross related to the total dollar earnings worldwide. It is desired to verify that the values associated with these attributes are consistent with their meaning. This means that these inequalities must be respected: openingWeekendUSA < grossUSA < cumulativeWorldwideGross. Since not all movies have non-missing value for the attributespreviously mentioned, consistency is also checked for pairs of attributes. From the output weit is observed that no film exhibits a nonmissing

### 3.2 Web Scraping

The database obtained from the second data source is a relational database in csv format, consisting of by the variables Title and Review. The database does not have any missing data. The only adjustment needed concerns the text in the reviews. From the scraping operation characters appear in the text to be deleted such as \n and \xa0. Through the `replace()` function we delete such characters.

## 4. Data Integration

We integrates the two databases into a new document database in json format that will be stored on MongoDB. In the new database for documents that have a review there will be a new attribute named review, related to the review. The documents no longer contain only the main features of the movie, but also a 
review. The review provides the user with a complete and detailed description of the film in all its its facets. The dataset is ready to do the first Data Exploration analyses.

## 5. Data Exploration

Data exploration is implemented through the `pandas_profiling` library and the functions of MongoDB. All the analyses done are commented in the notebook related to Data Exploration. Sentiment analysis and topic extraction are performed on review attribute. 


# How to run code
To use the API, one had to create an IMDB account from which one one obtained a key, called the API Key. You can use this link: https://developer.imdb.com/documentation/api-documentation/getting-access/.
Unless otherwise specified in the notebook section all codes can be runned in [Google Colaboratory](https://colab.research.google.com/) platform. All notebooks all already setted to import the necessary packages and also in this way you can easily use a GPU! 
