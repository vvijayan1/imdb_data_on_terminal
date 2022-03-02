# Lesson 1

# _"Without data, you are just another person with opinion" - W Edwards Deming_

# Obtaining the dataset

## Introduction to the dataset

So, you are told that there is some data to analyse. First two questions you must ask are:

1. Where is the data?
2. Where is the meta-data for the data?

Throught this exercise we are to analyse the data from [IMDB](https://www.imdb.com/) - the popular movie/tv-show review website. So where is the data? IMDB makes a subset of their data available [here](https://datasets.imdbws.com/) - that is the answer to "where is the data". In this case the data is not behind a firewall and is accessible freely over HTTP, in many cases it can be on on a cloud's storage like AWS' S3 or even a _primitive_ (S)FTP server. Whatever it is, you must ask for access to the data.

[This](https://www.imdb.com/interfaces/) is the answer to the next question - on where is the meta-data for the data. In this case IMDB has documented clearly what each file means the contents of the file.

Now you are armed with fundamental details on the dataset.

## Get the data

We are speaking about analysing data on the Terminal. So we need to _touch_ and _feel_ the data. In this case it appears there is only one file per type of dataset, so we have to download the whole of it.

> When the data is _huge_ download a sample set that can be handled on your machine.

IMDB data is [here](https://datasets.imdbws.com/) and let us first try to extract the URLs of the induvidual dataset files.

```
curl https://datasets.imdbws.com/
```

Since the above HTML page is simple and has just the hyperlinks, you can easily see the URLs to the dataset.

```
https://datasets.imdbws.com/name.basics.tsv.gz
https://datasets.imdbws.com/title.akas.tsv.gz
https://datasets.imdbws.com/title.basics.tsv.gz
https://datasets.imdbws.com/title.crew.tsv.gz
https://datasets.imdbws.com/title.episode.tsv.gz
https://datasets.imdbws.com/title.principals.tsv.gz
https://datasets.imdbws.com/title.ratings.tsv.gz
```

Remember, we are not to use the browser using some "Save link as" for this course. Let's get the data using the terminal. That brings us to the first utility `curl` - used to transfer data from URLs.

First try

```
curl https://datasets.imdbws.com/name.basics.tsv.gz
```

Your screen will be splashed with gibberish and a hell lot of it. Let us now create a directory (folder for lesser mortals!).

```
mkdir -p imdb/raw/
```

This will go ahead and create two directories one below the other.

Now let us get the dataset again into a file instead of printing on the screen. This is where you will have to use the redirection '>'.

```
curl https://datasets.imdbws.com/name.basics.tsv.gz > imdb/raw/name.basics.tsv.gz
```

This grabs the contents of the URL, which is essentially the file name.basics.tsv.gz into a file in the directory above. Do this for the other 6 files. It takes a few minutes, so just be patient. When you think you are done, do the following to list the files.

```
ls imdb/raw
```

Do you see 7 files? Then, pat yourself on the back for procuring the data files.
At this stage all the files that were in the servers of IMDB are on your machine.

## What files are these?

An examination of these files is necessarry. The files above come with a proper extension `.tsv.gz` and we can make the assumption that it is a tab-separated-value file which is compressed. But let us not leave it to assumption.

Let us use the `file` command to determine the type of file this is

```
file imdb/raw/name.basics.tsv.gz
```

you will see something like this:

```
imdb/raw/name.basics.tsv.gz: gzip compressed data
```

which tells us that we are dealing with a compressed file.

Let us go ahead and uncompress the file with the command `gunzip`.

```
gunzip imdb/raw/name.basics.tsv.gz
```

The uncompressed file can now be listed

```
ls imdb/raw/name.basics.tsv
```

> Do this operation for all the remaining 6 data files (so, totally 7 files) to uncompress using `gunzip`. Once done you will have 7 tsv files, check if you have

```
ls imdb/raw/*.tsv
```

must result in you seeing

```
name.basics.tsv      title.basics.tsv     title.episode.tsv    title.ratings.tsv
title.akas.tsv       title.crew.tsv       title.principals.tsv
```

Congratulations! You have used only terminal using keyboards to download the data files and bring them to a shape where you an vet them in the next sections.
