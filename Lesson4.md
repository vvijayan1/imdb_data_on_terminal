# Lesson 4

# _“Most people use statistics like a drunk man uses a lamppost; more for support than illumination” - Andrew Lang_

# The basic quantitative assessment

## Introduction

At this stage you have a good idea about what each file contains and how they are inter-related. This is the stage at which you must dive into quantitative analysis where you look at the data as a whole in terms of numbers. It is always tough to figure out what should our quantitative analysis look for, but usually there is some intuitions to match to.

## A look at aggregations

### How many distinct title-types are present?

```
cat title.basics.tsv | cut -f2 | sort | uniq
```

tells you that these many types of titles exist; this is a lot more that what one usually thinks.

```
movie
short
tvEpisode
tvMiniSeries
tvMovie
tvPilot
tvSeries
tvShort
tvSpecial
video
videoGame
```

---

**Commands Used**

- `cut`: extracts based on the delimiter - similar to `csvcut`
- `sort`: sorts what is extracted - but does not eliminate duplicates
- `uniq`: eliminates duplicates in a sorted input

---

### what is the ratio of Adult to non-Adult movies?

The 5th field field `isAdult` in `title.basics.tsv` tells what we need. Let's find out how many are Adult and non-Adult.

```
cat title.basics.tsv | cut -f5  | sort | uniq -c
```

spews this:

```
8305403 0
264408 1
```

Here the `uniq` has the `-c` option which says to print the number of occurrences of each value. Phew, so non-Adult movies far outweighs the ones that are Adult.

**Somethings you can try**:

- How many regions are present in the file `title.akas.tsv`
- What is the ratio of original versus remakes of titles - see `title.akas.tsv` again
- How many unique primary professions are there in the field and how many of each? - see `name.basics.tsv` - you will have to pile through two `cut`s

### Basic statistics

In the analysis of data that we have it would be interesting to know:

- Highest rated title
- Oldest and youngest artist
- Average and median rating of titles

One analysis will lead to another and then it leads to a discovery. But we have to start with some hypothesis and begin analysis of the data.

#### What is the maximum votes a title has ever got?

```
csvstat -t -c numVotes title.ratings.tsv --max
```

`csvstat` can do most of the basic things you want to get out of the data, like the one above to get the largest number of votes polled. If you do not know what metrics might be useful, just run

```
csvstat -t -c numVotes title.ratings.tsv
```

which spewed out:

```

        Type of data:          Number
        Contains null values:  False
        Unique values:         19772
        Smallest value:        5
        Largest value:         2,516,262
        Sum:                   1,194,676,557
        Mean:                  994.737
        Median:                24
        StDev:                 16,574.573
        Most common values:    7 (56184x)
                               6 (54651x)
                               8 (53157x)
                               9 (47101x)
                               10 (42706x)
```

What seems to be a discovery for me is that the median (the number that is in the middle when all values are arranged in sorted order) is just 24, which means more than half of the titles have no more than 24 votes to decide the rating; this as a Data person should make you conscious in your analysis when you see that rating of a movie is say 9.9, because all 24 (or less) votes would be from the family member of the crew.

A few more to analyse:

**Who is the oldest artist?**

```
csvstat -t -c birthYear name.basics.tsv --max
```

will answer how old is the oldest artist, but not who it is. Again this throws very surprising results, some 'crew' members have a birth date as early as in the 1st century and the data does not seem to be incorrect.

## Review

What did we learn in this lesson?

- Aggregating data
- Extracting basic statistics from the data
