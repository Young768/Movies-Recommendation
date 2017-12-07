Movies Recommendation Project Report
====
1. Introduction
------- 
In this project, I will process a large corpus of movie ratings to provide recommendations. The program will help us decide what to watch on Netflix tonight. For each pair of movies in the dataset, I will compute their statistical correlation and cosine similarity [1]. Based on this criteria, we should provide good recommendation.

2. Dataset
------- 
The dataset are from MovieLens [2]. GroupLens Research has collected and made available
rating data sets on the MovieLens web site. The data sets were collected over various periods of
time, depending on the size of the set. This project will use 11 million computed tag-movie
relevance scores from a pool of 1,100 tags applied to 10,000 movies. Released on 3/2014.

For both data sets, there are two input files:
movies.dat contains identification and genre information for each movie.
Lines are of the form:  
`MovieID`,`Movie Title`,`Genre`  
`0068646`,`The Godfather (1972)`,`Crime|Drama`  
`0181875`,`Almost Famous (2000)`,`Drama|Music`  
ratings.dat contains a series of individual user movie ratings.
Lines are of the form:  
`UserID`,`MovieID`,`Rating`,`Timestamp`  
`120`,`0068646`,`10`,`1365448727`  
`374`,`0181875`,`9`,`1374863640`  

3. Definition
------- 
Cosine similarity is a measure of similarity between two non-zero vectors of an inner product space that measures the cosine of the angle between them [4].  
![](https://github.com/Young768/Movies-Recommendation/blob/master/cos.png)  
Correlations are useful because they can indicate a predictive relationship. 
This project will use Pearson correlation coefficient [3].  
![](https://github.com/Young768/Movies-Recommendation/blob/master/corre.png)
This program will combine them into a  single similarity value. I will blend the values  together, using 50% of  each.  That  is, the final  value  for  a   pair  of movies  is    0.5  *   statistical  correlation  +   0.5  * the  cosine  correlation  for  the  pair. Based on this criterion, we can recommend what the user should watch. 

4. Steps
-----
This program will implement a sequence of Map/Reduce tasks. Here is the sequence of setps:
1. Join the input files.
For the input files (ratings.dat  and  movies.dat). I’ll get most of the important information from ratings.dat. But it only has movie IDs rather than movies names. I will implement the Reducer’s output to be  
key:`user_id`, value:`(movie_name,rating)`.

2. Pair the movie ratings
This step should organize the movies into pairs. For example, for each user, the pairs will be two movies. This output vector will be used to compute the similarity later.
Here is a concrete example:  
`A` rated `Almost Famous` a `10`, `The Godfather` a `9`, and `Anchorman` a `4`. 
`B` rated  `Almost Famous` a `7` and `Anchorman` a `10`. 
`C` rated `The Godfather` a `10` and `Anchorman` an `8`. 

Here is the output (key, value) store:  
Key: `(Almost Famous, The Godfather)` Values: `(10, 9)`   
Key: `(Almost Famous, Anchorman)` Values: `(10, 4)`  
Key: `(Almost Famous, Anchorman)` Values: `(7, 10)`   
Key: `(Anchorman, The Godfather)` Values: `(4, 9)`  
Key: `(Anchorman, The Godfather)` Values: `(8, 10)`   
The keys are in alphabetical order.

5. Compute the similarity
------- 
Before computing the movies similarity, I will organize the values for each key.  
For example, Movies with key “`Anchorman`” and “`The Godfather`” has 2 value:`(4, 9)` and `(8, 10)`.  
The input for the calculation should be vector `[4,8]` and vector `[9, 10]`. And put these two vector into Cosine similarity correlation calculation.





 
6. Output
------- 

I will sort  the  output  by  similarity  metric  and output top K=100 movies that users likely to watch in decreasing order.
Here is the output for movies Barenaked in America:
![](https://github.com/Young768/Movies-Recommendation/blob/master/Screenshot%20from%202017-12-04%2017-47-11.png)
The output for each line is:  
`The name of the movie for which we want`;  
`The name of a recommended movie`;  
`The similarity they share`;  
`The statistical correlation that these two movies share`;  
`The cosine correlation that these two movies share`;  
`The number of ratings this pair of movies had in common`.


7. Reference:
------- 
[1]Movie Recommendations and More via MapReduce andScalding http://blog.echen.me/2012/02/09/movie-recommendations-and-more-via-mapreduce-and-scalding/  
[2]MovieLens: https://grouplens.org/datasets/movielens/  
[3] Correlation and dependence https://en.wikipedia.org/wiki/Correlation_and_dependence  
[4] Cosine similarity https://en.wikipedia.org/wiki/Cosine_similarity  
