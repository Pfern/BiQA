# SciQA corpus

This repository contains the code used to generate the SciQA corpus from online forums.
We also include v1 of the corpus.

## Getting started

We first retrieve Q&As from StackExchange and Reddit communities using the 
*src/stackexchange.py* and *src/reddit.py* scripts.
These scripts save the posts in HTML format to be viewed in a browser, and pickle file
to be used to retrieve answer documents.
To convert to the format used by retrieval systems, we use the *src/csv_reader.py* script.
We can then use *src/retrieve_answers.py* to get answer documents for each question,
using either galago or NCBI API.

Before running any script, move *params_default.json* to *params.json* and change the
parameters.

To access the reddit API, we use PRAW. This package requires a *praw.ini* file 
with a section to configure the toolname that is set on the *params.json* file.


## Get posts

### StackExchange

This script will generate pickle, tsv and HTML files and save them to <path>.
It is also possible to use previously retrieved posts by setting the request_query
variable to False, which is the default.
If set to True, it will call the StackExchange API.
This script also requires a file named "se_key" with your StackExchange API key store
in text format.

```bash
python src/stackexchange_questions.py <path>/<sitename>
```

Example:

```bash
python src/stackexchange_questions.py se_biology/biology
```

### Reddit

```bash
python src/reddit.py <path>/<sitename>
```

Example:

```bash
python src/reddit.py reddit_nutrition/nutrition
```


## Filter posts

The previous scripts will retrieve all links from StackExchange and Reddit posts.
However, for our purposes, we only want links that can be mapped to PubMed IDs.
We have a function to do this mapping automatically in some cases (PMC and DOIs),
however it is also possible to manually curate the retrieved posts and links, editing 
the generated CSV files.
These files can be processed again to include only PMIDs using the *src/csv_reader.py*
script.

Usage:

```bash
python src/csv_reader.py <filename> --cache <cache_file> --title_text --body_text
```

For example:

```bash
python src/csv_reader.py se_biology/biology_qdocs.csv --cache biology_questions_cache.json --title_text --body_text
```

Even if no changes are made to the CSV file, this script should be run in order to
generate data to be read by other systems.

## Retrieve documents

After generating a corpus, we want to evaluate document retrieval approaches with it.

```bash
python src/retrieve_answers.py pubmed <path>/<sitename>.aueb.pkl 
```

