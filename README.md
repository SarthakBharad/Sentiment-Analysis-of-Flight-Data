# Sentiment-Analysis-of-Flight-Data
This repository contains a sentiment analysis along with a few graphical representation of tweet sentiments which may help the airline companies in improving their customer service to get positive sentiments

**Sentiment Analysis Description**

Sentimental Analysis is a natural  processing language that helps us identify those things
1) Sentimental Analysis must be done on such a large data to identify that what is there customers' opinions on their flights
2) It identifies what is the customer's view, whether it is a positive, negative, or neutral opinion. 
3) Identification of which flight consumer prefers and the level of significance for correction is also calculated to know the correction of the tweet

It is a machine learning model that is able to analyze loads of twitter tweets, and be able to judge the sentiments behind the tweets.
Firstly, we convert the file in pyspark to pandas. The dataset contatins a lot more parameters about the tweet like the location of the tweet, timezone of the user,etc. But for the sake of building a model we are only concerned about 2 parameters: the actual text of the tweet(input of the NLP model) and the airline sentiment(output of the NLP model).

For building a better model we are dropping the tweets that has confidence level less than 50% of correct classification
Of this the most important part is preprocessing the data, so that it is ready for a model to use. That’s what we are doing now.
Since there are a lot of unnecessary parameters in the dataset, lets extract and store the input and the output, which is the text and airline_sentiment confidence column using the following lines:

X=tweets_df['text']

y=tweets_df['airline_sentiment']

The major part of this is data preprocessing which means:
1) Removing stopwords
2) Removing punctuations
3) Stemming

We need to know the words that significes sentiments which this type doesn't significes and thus removing unnecessary words

Now that input is clean and ready, we convert it into numbers using something called as the ‘Bag of Words’ approach. Basically we create a matrix table, where each row represents a sentence and each word will have separate column for itself that represents it’s frequency
Now, as for the input we also have to convert the output into numbers as well. There are many ways to do that, but we are going to replace the sentiments ‘negative, neutral, positive' into ‘0,1,2’ using the following lines:

sentiment_ordering = ['negative', 'neutral', 'positive']

y = y.apply(lambda x: sentiment_ordering.index(x))

Thus finally we are using Multinomial Naive Bayes model to figure out the relationship between the input and the output.

Now to build the model we split the dataset into a training and testing section(testing size=30% of the actual data). To actually fit the model, we call the model.fit function and supply it the training input and output.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Grahical Analysis Description**
We have plotted three primary graphs as follows:
1. Count of Each Sentiment - Bar Graph
2. Distribution of each sentiment Confidence - Histogram
3. Number of Tweets per Airline - Barplot

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

From Graph - 1, we can infer that the total amount of negative sentiments is much much greater than the positive and neutral sentiments.
It is even greater than both the counts combined, this means each airline in the dataset should improve its services to avoid negative reception.

From Graph - 2, we can infer that the confidence levels for the tweets is also pretty high, especially for negative tweets.
THe confidence level for positive and neutral tweets is barely over 50% which means the airlines need to be more careful in increasing customer satisfaction.

From Graph - 3, we can infer that the total amount of tweets referring to some airlines are more and some are less.
This leads to speculation that the data analysis for an airline with less no. of tweets may vary in accuracy compared to other airline with high no. of tweets.
This may lead to diffculties in calculating sentiment analysis due to large difference in no. of referred tweets.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**The following is the process by which we created the 1st graph, i.e. Count of Each Sentiment - Bar Graph**
First, we calculated the count of each sentiment that are present in the given data.
We found out that there are primarily three sentiments namely:
a) Positive
b) Negative
c) Neutral

Now, we calculated the count of each sentiment using the query in spark as below:

- q1=spark.sql('''
- FROM tweet select
- airline_sentiment, count(airline_sentiment)
- GROUP BY airline_sentiment
- ''')
- qp1=q1.toPandas()

We then plotted the graph of sentiment vs count of sentiment using the code below in which we used the seaborn library:

- f, (ax1) = plt.subplots(1, 1, figsize=(9, 9), sharex=True)
- sns.barplot(x=qp1['airline_sentiment'], y=qp1['count(airline_sentiment)'], palette="rocket_r", ax=ax1)
- ax1.axhline(0, color="k", clip_on=False)
- ax1.set_ylabel("Sequential")

The results we got are according to

<img src='https://drive.google.com/file/d/1SgMZ6s3TdJojetL_X_mopzQmKkLmtp0a/view?usp=sharing'>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**The following is the process by which we created the 2nd graph, i.e. Distribution of Sentiment Confidence - Histogram**

First, we segregated the confidence of each sentiment that are present in the given data according to the sentiment values the corresponding cell has.
We performed the above function using the queries below:

- q2=spark.sql('''
- FROM tweet select airline_sentiment, airline_sentiment_confidence
- where airline_sentiment='positive'
- ''')
- q3=spark.sql('''
- FROM tweet select airline_sentiment, airline_sentiment_confidence
- where airline_sentiment='negative'
- ''')
- q4=spark.sql('''
- FROM tweet select airline_sentiment, airline_sentiment_confidence
- where airline_sentiment='neutral'
- ''')
- qp=q2.toPandas()
- qn=q3.toPandas()
- qne=q4.toPandas()

We then plotted the graph of sentiment confidence vs count of sentiment using the code below and got results as per image 2:

- sns.displot(qp['airline_sentiment_confidence'], bins=10,palette="rocket_r")

The results we got are according to

<img src='https://drive.google.com/file/d/1sMmxI0JO8ZS87qnpD4YEo-O7BNQwgTfr/view?usp=sharing'>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

The following is the process by which we created the 3rd graph, i.e. Number of Tweets per Airline - Barplot
First, we calculated the count of tweets that are present in the given data regarding a specific airline.
We used the below query to obtain the results:

- q5=spark.sql('''
- FROM tweet select airline,count(tweet_id)
- GROUP BY airline
- ''')
- q5.show()
- q5p=q5.toPandas()

We then plotted the graph of sentiment vs count of sentiment using the code below and got results as per image 3:

- f, ax = plt.subplots(figsize=(6, 15))
- sns.set_color_codes("pastel")
- sns.barplot(x=q5p['count(tweet_id)'], y=q5p['airline'], data=q5p, label="Total", color="b")

The results we got are according to

<img src='https://drive.google.com/file/d/1SR86jqcY0sRHOwnETTFNjKD6HW0hCfTb/view?usp=sharing'>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
