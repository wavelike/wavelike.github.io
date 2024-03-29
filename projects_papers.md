---
layout: page
title: Projects&Papers
permalink: /projects_papers/
---

This is a list of selected personal projects I was working on and papers or books I read:


## Personal Projects

#### Automatic Machine Learning System
- Generalisation of Machine Learning Pipeline tasks (from reading raw data to model optimisation)
- Setup of ML-pipelines fast and easy via configs and without writing code
- Utilisation of a self designed feature store
- Automatic model selection, hyperparameter optimisation, cross validation, metric calculation, ensemble building
- Deployment on AWS

#### Automated Stock Trading via Interactive Brokers
- Data Streaming via Interactive Brokers and RabbitMQ
- Data Storage via Postgres and the time series database TimeScaleDb
- Containerisation of Interactive Brokers Software
- Container orchestration for 5 workers:
    1. retrieving live stock data from Interactive Brokers API
    2. cleaning and storing stock data
    3. calculating time series features and stock indicators
    4. execution of trading strategy
    5. live trading of stocks via Interactive Brokers API

#### Stock trading strategy based on Machine Learning models
- Various approaches to building a trading strategy that beats the market
- Leveraging of Deep Learning using the Keras package and Long Short-Term Memory Networks (LSTMs)
- Development of a full backtesting suite using real stock time series data

#### Prediction of Food Intolerances
- Data model design for various meals and ingredients
- Prediction of digestive conditions based on previously eaten meals

#### KnowNet Document Search Engine
- Development of a personal knowledge graph and graph based search engine that connects personal information (e.g. local or cloud files, emails and notes)
in a network of nodes and allows contextual full text search
- Leveraging of Natural Language Processing (NLP), Topic Modelling
- WebApp development using React, Node.js and hosting on Heroku
- Business Model development
- Application for an Exist Business Start-up Grant

## Work-related Projects

- Churn and conversion prediction
- Financial Budget prediction
- User activity prediction
- Model insights with SHAP
- Mailing optimisation with multi-armed bandits
- Customer lifetime revenue (CLR)
- Automatic risk report generation based on Big Data

Some technologies: 
- Gradient Boosted Trees (Catboost) incl. feature engineering and hyperparameter optimisation
- Multi-Armed Bandits
- SHAP values for model insights
- Bayesian AB tests 
- Impact Analysis of events on Time Series data


- AWS Services: S3, EC2, ECR, AWS Batch, AWS Lambda
- Databases: Snowflake, Postgres, Elastic Search
- Automation: Airflow
- Containerisation: Docker
- Big Data: Spark and Kafka



## Selected papers and books

#### TabNet: Deep Learning Models for small tabular datasets
- ***TabNet: Attentive Interpretable Tabular Learning***
- Keywords: Tabular data, interpretable neural networks, attention models
- <https://arxiv.org/abs/1908.07442>

#### Sleeping, recovering bandit algorithm
- ***A Sleeping, Recovering Bandit Algorithm for Optimizing Recurring Notifications***
- <https://research.duolingo.com/papers/yancey.kdd20.pdf>

#### Multi-armed Bandits: A smart alternative to classical A/B testing
- ***Multiworld Testing Decision Service:
A System for Experimentation, Learning, And Decision-Making***
- <https://raw.githubusercontent.com/Microsoft/mwt-ds/master/images/MWT-WhitePaper.pdf>

#### Causal Impact: Impact of an event on time series data
- ***Inferring Causal Impact Using Bayesian Structural Time-Series Models***
- Keywords: Causal inference, counterfactual, synthetic control, observational, difference in differences, econometrics, advertising, market research.
- <https://research.google/pubs/pub41854/>

#### Book: Time Series Forecasting
- ***Forecasting: Principles and Practice***
- <https://otexts.com/fpp2/>

#### Interpretable Models / Shap Values
- ***Consistent Individualized Feature Attribution for Tree
Ensembles***
- <https://arxiv.org/abs/1802.03888>

#### Ngboost
- ***NGBoost: Natural Gradient Boosting for Probabilistic Prediction***
- <https://arxiv.org/abs/1910.03225>

#### Xgboost
- ***XGBoost: A Scalable Tree Boosting System***
- <https://arxiv.org/abs/1603.02754>

#### Catboost
- ***CatBoost: unbiased boosting with categorical features***
- <https://arxiv.org/abs/1706.09516>

#### Topic Modelling
- ***Latent Dirichlet Allocation***
- <https://ai.stanford.edu/~ang/papers/nips01-lda.pdf>

#### PageRank
- ***The PageRank Citation Ranking: Bringing Order to the Web***
- <http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf>

#### **Monte Carlo Tree Search (MCTS)**

