# Suggest Video Service - Deep Suggestion Engine

## Overview architecture

![Overview archiecture](https://i.imgur.com/JyuSs5P.png)

The overall architecture of the Suggest Video Service - Deep Suggestion Engine consists of the following components:

* **Deep Learning Service:** Collects, normalizes, builds, and serves machine learning models for video recommendations.
* **Data Collecting Service:** Collects the events necessary for video recommendations and stores them in a database.
* **Video Indexing Service:** Collects new or changed video information, analyzes video content, and indexes it into Elasticsearch.
* **Suggestion Service:** Implements video recommendation logic for a variety of contexts with pre-configured algorithms.
* **Report Module:** Collects video recommendation data and reports to BI.

## Components

### Deep Learning Service

The Deep Learning Service is the main component of the system, responsible for collecting, normalizing, building, and serving machine learning models for video recommendations.

* **Data collector workers:** Collect the events necessary for model training, including information about user video viewing history, interactions, and actions during video viewing.
* **Data standardlization:** Contains jobs normalizing and cleaning the raw data collected from Data collector workers to structured & semantic data
* **Build dataset:** Scoring & labeling standardlized data and build the training dataset for the model. Split the training / test datasets.
* **Build model:** Trains a deep learning model from the dataset.
* **Serving:** Once the model is built, it will be deployed and configured manually to the server via an internal API. The model will be preloaded and indexed into FAISS to support nearest neighbor vector queries in n-dimensional space. The model then will be used to:
  - Get a list of videos that are relevant to the user
  - Get a list of videos that are similar to a seed video
  - Get a list of users who are similar to a user

### Data Collecting Service

The Data Collecting Service collects the events necessary for video recommendations and stores them in a database. The current events include:
  - How users view videos: CTR, view duration, read comments, like, share...
  - Users implicit interests: sync from other services, analyzing user behaviors and interests
  - Video stats / trending scores:Statistics about videos, including number of views, likes, shares, etc., as well as trending score.


### Video Indexing Service

The Video Indexing Service collects new or changed video information, analyzes video content, and indexes it into Elasticsearch. The indexed video information might includes:
  - Category, labels
  - Title, description, links, tags,...
  - Viewers meta: ages, genders, locations...

### Suggestion Service

The Suggestion Service implements video recommendation logic for a variety of contexts with pre-configured algorithms. Also apply A/B testing for each of them on PROD.

- Generate a list of candidates:
- Choose an algorithm suitable for the user and according to the A/B test weight to generate suggestions, currently there are:
  + Suggest according to user's interests (explicit & implicit interests)
  + Random suggestions (Diversity)
  + Suggest by deep learning model (video suitable for user or similar to seed video)
  + Viral suggestions (New videos, trending, or recently viewed by users similar to the original user)
- Get relevant information to create a list of candidates (videos)
- Create a list of candidates according to the selected strategy
- Ranking candidates, current criteria:
  + distance: score from ES search or deep learning service
  + popularity: video stats (reacts, comms, shares) score from event bucketing service
  + correlation: videos from same page with seed videos, from followed pages or having similar category with seed videos
  + diversity: variance + random score
  + freshness: videos uploaded recently, coeff different between categories
  + interested: matched with user implicit / explicit interests
  + penalties: video that has been watched / suggested recently will receive penalty score
- Returns the top n videos with the highest score
  
### Report Module:
- All collected events will be synced to GG BigQuery
- The data of all suggestions, strategies and user view context will be stored in a MongoDB and synced to GG BigQuery
- Reports is designed by an analytics team and updated automatically