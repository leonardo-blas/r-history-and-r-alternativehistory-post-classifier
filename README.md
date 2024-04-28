# Classifying r/History and r/AlternativeHistory posts

## Introduction

These days, we not only have lots of textual information but also lots of alternative versions of that information. This applies to history, science, the news, etc. The problem is that distorted messages can be hard to identify and could be interpreted as factual information. One potential solution to this problem is using natural language processing techniques to help us distinguish factual from alternative information. To test this approach, we scraped ~2000 r/History and r/AlternativeHistory posts and tried to classify them by Subreddit [1] [2].

## Summary

We begun our approach by taking into account a post's title, description, author, and url domain (if it contains a link), lemmatizing it, and TF-IDF vectorizing this text. We chose this vectorizer because we wanted to give each token a weight, as it is reasonable to think that some words may be more frequently used in r/History or r/AlternativeHistory.

Besides considering text data, our models take into account numeric and categorical data found in the scraped `Submission` objects. We thought it was a good idea to consider any other relevant features that may increase our models' accuracy. One particular example of how this feature selection may have helped our model is the `crossposts` column. This feature measures how many times a post was reposted on a different community, and r/History posts tended to achieve higher `crossposts` metrics. This could be because r/History is one of the largest and most reputable Reddit communities, whereas r/AlternativeHistory is smaller and, from the ~2000 scraped posts, some where removed for "legal" reasons. Features like `crossposts` may have been influenced by external factors like outreach, reputation, and community size--inherent to the Subreddit a post belongs to. By leveraging a post's text and features like `crossposts`, our models achieved high accuracy levels. Note, however, that different Subreddit `Submission` objects may have different numbers of features. We faced this situation and had to conduct a thorough feature-cleaning process before we could further transform our non-textual data--scaling, normalizing, etc.

One detail worth noticing is that we used the `created` column, which provides the UNIX time for when the post was created, to feature engineer a 0-23 time value. Using this feature alone was better than using a baseline model (50/50), achieving ~0.57 accuracy in a default random forest classifier and a default logistic regressor.

Finally, note that while we didn't perform gridsearch on our models--we have limited compute resources--we learned that a TFIDF vectorizer using English stopwords and taking into account unigrams and bigrams, coupled with a default random forest classifier, performs well. Our model achieved ~0.95 accuracy on the testing data and a 1.0 on the training data.

## Data dictionary

Note that Reddit is a private organization and that PRAW--our scraping software--is an open source project. The description for each feature in a `Submission` object may not be publicly available and Reddit may add or remove features in the future. Hence, on the table below, if we put a description in-between quotes, it means we got it from the PRAW documentation [3]. Else, we deduced a description from the feature's name or further researched it.

| Feature             | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| subreddit           | "Provides an instance of Subreddit."                         |
| selftext            | "The submissions’ selftext - an empty string if a link post." |
| gilded              | Assuming: The post's author received 1 month of Reddit gold. |
| title               | "The title of the submission."                               |
| thumbnail_height    | Assuming: The height of the post's thumbail.                 |
| upvote_ratio        | "The percentage of upvotes from all votes on the submission." |
| score               | "The number of upvotes for the submission."                  |
| edited              | "Whether or not the submission has been edited."             |
| is_self             | "Whether or not the submission is a selfpost (text-only)."   |
| created             | Assuming: The local UNIX time in which the post was created. |
| domain              | Assuming: If the post has an URL, the URL's domain.          |
| allow_live_comments | Assuming: Whether post allows live comments.                 |
| no_follow           | Assuming: Wether Redditors can follow the post.              |
| locked              | "Whether or not the submission has been locked."             |
| author              | "Provides an instance of [`Redditor`](https://praw.readthedocs.io/en/latest/code_overview/models/redditor.html#praw.models.Redditor)." |
| num_comments        | "The number of comments on the submission."                  |
| send_replies        | Assuming: If true, the post's author will receive a message when the post receives a comment. |
| num_crossposts      | Assuming: The number of times the post appeared in other unique Subreddits. |

## References

[1] https://www.reddit.com/r/history/

[2] https://www.reddit.com/r/AlternativeHistory/

[3] https://praw.readthedocs.io/
