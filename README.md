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

The data acquisition phase is performed in two different stages, one consequential to the other. Documentation can be found at the following link: https://imdb-api.com/api. First step is to use functionality of the Advanced Search API found at: https://imdbapi.com/api#AdvancedSearch-header. Second step is to use the functionality of the Title API found at: https://imdbapi.com/api#Title-header. To use the API, one had to create an IMDB account from which one 
one obtained a key, called the API Key. It is specified that for each key you can make 
only 100 requests per day, consequently, in the second step one had to create several 
accounts, then several API Keys to be able to download all the data in a reasonable time.

Translated with www.DeepL.com/Translator (free version)

## 2. Data Cleaning



## 3. Data Quality



## 4. Data Integration



## 5. Data Storage 



## 6. Data Exploration



### 

# How to run code
To use the API, one had to create an IMDB account from which one one obtained a key, called the API Key. You can use this link: https://developer.imdb.com/documentation/api-documentation/getting-access/.
Unless otherwise specified in the notebook section all codes can be runned in [Google Colaboratory](https://colab.research.google.com/) platform. All notebooks all already setted to import the necessary packages and also in this way you can easily use a GPU! 
