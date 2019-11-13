# Text generation using Open AI's [GPT-2](https://github.com/openai/gpt-2) and hosting it in Google Cloud Run.

This project outlines tuning [OpenAI](https://github.com/openai/gpt-2) GPT-2 using Reddit comments and hosting the model app via Docker in Google Cloud.

## What is GPT-2? 

The OpenAI GPT-2 language model is a direct successor to GPT. GPT-2 has 1.5B parameters, 10x more than the original GPT, and it achieves SOTA results on 7 out of 8 tested language modeling datasets in a zero-shot transfer setting without any task-specific fine-tuning. The pre-training dataset contains 8 million Web pages collected by crawling qualified outbound links from Reddit.

I am using Max Woolf's [gpt-2-simple](https://github.com/minimaxir/gpt-2-simple) package that wraps existing model fine-tuning and generation scripts for OpenAI's GPT-2 text generation model (specifically the **"small"** 124M and **"medium"** 355M hyperparameter versions). The model finetuning is done in TensorFlow. 

## Where to get text data? 

First of all we need text data to finetuning GPT-2 for our application. I decided to use Reddit comments hosted in BigQuery's reddit comments [database](https://console.cloud.google.com/bigquery?utm_source=bqui&utm_medium=link&utm_campaign=classic&project=charismatic-sum-134503). For fun, I wanted to generate YTA comments in [AITA](https://www.reddit.com/r/AmItheAsshole/) subreddit. You may have to setup your Google cloud credentials before you can begin querying. 

This is an example script to get comments from the subreddit. Feel free to change this to your needs. 

```
#standardSQL
SELECT
  REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(body, '&amp;', '&'), '&lt;', '<'), '&gt;', '>'), '�', '') AS body
FROM
  `fh-bigquery.reddit_comments.2019_0*`
WHERE
  subreddit = 'AmItheAsshole'
  AND REGEXP_CONTAINS(body, r'(?i)YTA')
  AND author not in ('AutoModerator')
LIMIT
  100
```

You can save the data set locally or to your google drive.

![alt text](https://github.com/addadda023/GPT-2-text-generation/blob/master/images/gcp_save_data.PNG)

## GPT-2 fine-tuning

Use the accompanying colab [notebook](https://github.com/addadda023/GPT-2-text-generation/blob/master/Train_a_GPT_2_Text_Generating_Model.ipynb) to understand more. In summary:

* Use gpt-2-simple to load gpt-2.
* Load the input text file.
* Specify tuning parameters.
* Train and save the model.
* Generate texts.
* Save the TensorFlow checkpoint to Google drive and download locally. This is needed to deploy the model.

Notice the average loss of the model when training. It is easy to overfit. You can stop the training once you start to see it's decreasing slowly. 

## Making GPT-2 App






