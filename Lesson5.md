# Lesson 5

# _"Data are becoming the new raw material of business" - Craig Mundie_

# Streaming data (instead of downloading)

## Introduction

When you go to a soup store and see a new soup, you taste a spoon before you buy the full bowl, no? Same goes with new data. On most occasions you do not have to download the full data to know the sample. You can stream the data and use only a portion of it. This is sometimes useful if you do not want to download the whole data on to your disk particularly when the data is space intensive.

## Downloading different types of data

### HTTP URL pointing to GNU Zipped TSV file

`https://datasets.imdbws.com/name.basics.tsv.gz` - is the URL of the file we are familiar with.

There are two aspects of this URL:

- The protocol is HTTP
- The file appears to be zipped

Let us checkout the first few lines of the file:

```
curl -s "https://datasets.imdbws.com/name.basics.tsv.gz" | zcat | head
```

What are we doing here?

---

**_Commands Used_**

- `curl`: we have used this in the first lesson to download the file, but this time we we do not download on to the disk but pass it to the next command.
- `zcat`: spits out a file after un-compressing (unzipping) it; in this case since we use `|` it takes the output from `curl` as input
- `head`: our good old command to extract the first 10 lines

### FTP URL pointing to GNU Zipped file

`ftp://ftp.fu-berlin.de/pub/misc/movies/database/frozendata/ratings.list.gz` is the URL of a data file.

The two aspects of this URL:

- The protocol is FTP
- The file appears to be zipped

```
curl -s "ftp://ftp.fu-berlin.de/pub/misc/movies/database/frozendata/ratings.list.gz" | gunzip | head -100
```

---

**Commands Used**

- `curl`: we have used this in the first lesson to download the file, but this time we we do not download on to the disk but pass it to the next command.
- `gunzip`: spits out a file after un-compressing (unzipping) it; in this case since we use `|` it takes the output from `curl` as input. Usually `gunzip` and `zcat` can be used interchangeably in such streaming purposes
- `head`: our good old command to extract the first 100 lines

---

### AWS S3 URI pointing to csv file

It is increasingly common to use AWS Simple Storage Service (S3) and provide the URI to the data file. For example:

Data file S3 URI: `s3://ryft-public-sample-data/passengers.txt`

The easiest way to take a quick peak into the S3 is like this:

```
s3cmd get s3://ryft-public-sample-data/passengers.txt - | head
```

---

**Commands Used**

- `s3cmd` - a command line tool to do everything needed with AWS S3
  - we use the sub-command `get` to get the file
  - in normal circumstances the destination is a local file/directory; but in this since we do not want to stream, we provide `-` which means stream into standard output
  - We pipe the standard output to `head` to see the first 10 lines

---

## Review

Remember, stream first, download next.
