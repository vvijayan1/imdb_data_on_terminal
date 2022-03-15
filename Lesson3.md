# Lesson 3

# _"In God we trust. All others must bring data." - W Edwards Deming_

# The qualitative assessment

## Introduction

At this stage you have an idea about the data files, how big they are and what to expect out of them. This lesson takes you through:

- What to look for in qualitative checks
- How to check for inter-relation when you have more than one data file
- How to check for duplication of data

## The journey of 5 movies

The five movies' data that we will analyse.

<img src="https://upload.wikimedia.org/wikipedia/commons/b/b5/12_Angry_Men_%281957_film_poster%29.jpg" alt="12 Angry Men" width="200"/>
<img src="https://upload.wikimedia.org/wikipedia/en/3/3b/The_Man_from_Earth.png" alt="Man From Earth" width="200"/>
<img src="https://upload.wikimedia.org/wikipedia/en/5/50/Departed234.jpg" alt="The Departed" width="200"/>
<img src="https://upload.wikimedia.org/wikipedia/en/8/81/ShawshankRedemptionMoviePoster.jpg" alt="Shawshank Redemption" width="200"/>
<img src="https://upload.wikimedia.org/wikipedia/en/2/26/ImAmSamSeanMichelle.jpg" alt="I am Sam" width="200"/>

Let us start with the movie **12 Angry Men**

Introducing [CSV Kit](https://csvkit.readthedocs.io/en/latest/) - a set of command line tools that you must know as a Data Engineer/Scientist/Analyst.

### Step 1 - search for the movie "12 Angry Men" in the column with header "primaryTitle" in the file title.basics.tsv

```
csvgrep -t -c "primaryTitle" -m "12 Angry Men" title.basics.tsv
```

---

**Commands used**
`csvgrep` - is grep on steroids! Works for a variety of data files csv, tsv or any such data files with delimiters

- `-t` - by default, `csvgrep` command accepts csv (comma separated value) files as input, `-t` tells that the input file is tab-separated (tsv)
- `-c` - the column which must be searched, in this case it is `primaryTitle`
- `-m` - what must be searched

---

```
tconst,titleType,primaryTitle,originalTitle,isAdult,startYear,endYear,runtimeMinutes,genres
tt0050083,movie,12 Angry Men,12 Angry Men,0,1957,\N,96,"Crime,Drama"
tt0118528,tvMovie,12 Angry Men,12 Angry Men,0,1997,\N,117,"Crime,Drama"
tt1201562,video,Beyond a Reasonable Doubt: Making '12 Angry Men',Beyond a Reasonable Doubt: Making '12 Angry Men',0,2008,\N,23,"Documentary,Short"
tt12126370,tvEpisode,12 Angry Men,12 Angry Men,0,2020,\N,\N,Talk-Show
tt12389600,tvEpisode,12 Angry Men (1957),12 Angry Men (1957),0,2018,\N,19,Comedy
tt12714994,tvEpisode,Conversations on Essential Cinema: 12 Angry Men,Conversations on Essential Cinema: 12 Angry Men,0,2020,\N,\N,Documentary
tt1957833,short,12 Angry Men: Teams That Don't Quit,12 Angry Men: Teams That Don't Quit,0,1999,\N,\N,"Documentary,Short"
tt3143018,tvEpisode,12 Angry Men,12 Angry Men,0,2013,\N,\N,Talk-Show
tt4030604,tvEpisode,12 Angry Men Inside Amy Schumer,12 Angry Men Inside Amy Schumer,0,2015,\N,22,Comedy
tt5723438,video,12 Angry Men: The Television Version,12 Angry Men: The Television Version,0,2011,\N,14,"Documentary,Short"
tt5723504,video,12 Angry Men: From TV to the Big Screen,12 Angry Men: From TV to the Big Screen,0,2011,\N,26,"Documentary,Short"
tt7414600,tvEpisode,12 Angry Men: How the Camera Pulls Us In,12 Angry Men: How the Camera Pulls Us In,0,2016,\N,\N,Short
tt7879140,tvEpisode,12 Angry Men,12 Angry Men,0,2018,\N,12,"Comedy,Talk-Show"
tt8254058,movie,Obwohl Der Himmel Fällt/12 Angry Men,Obwohl Der Himmel Fällt/12 Angry Men,0,\N,\N,\N,Crime
```

The above output is correct, but has two deficiencies:

- We need information about only the movie, but there seems to be many entries with the name '12 Angry Names'
- The output is not all that readable

We should now pass the output of the above command to filter only the movies and then pass the resulting output to `csvlook` to make the output a lot more readable.

```
csvgrep -t -c "primaryTitle" -m "12 Angry Men" title.basics.tsv | csvgrep  -c "titleType" -m "movie" | csvlook
```

---

**Commands used**

- `csvgrep` is used twice to search different columns
- `csvlook` is used to make the output readable

---

You should get an output like this:

```
| tconst    | titleType | primaryTitle                         | originalTitle                        | isAdult | startYear | endYear | runtimeMinutes | genres      |
| --------- | --------- | ------------------------------------ | ------------------------------------ | ------- | --------- | ------- | -------------- | ----------- |
| tt0050083 | movie     | 12 Angry Men                         | 12 Angry Men                         |   False | 1957      | \N      | 96             | Crime,Drama |
| tt8254058 | movie     | Obwohl Der Himmel Fällt/12 Angry Men | Obwohl Der Himmel Fällt/12 Angry Men |   False | \N        | \N      | \N             | Crime       |
```

Let us take the movie with the identifier `tt0050083` and proceed with our data investigation as some search on IMDB says that the other one (Obwohl Der Himmel Fällt) is not even a movie that is released.

The above data extract says that the movie released in 1957, is not an adult movie, has a run-time of 90 minutes and is of the genre 'Crime' and 'Drama'. Let us dig in further.

### Step-2: Search for the variations of the movie

A look into `title.akas.tsv` with the ID `tt0050083` - says that this movie has been dubbed in several languages.

**Note:** The field `tconst` in `title.basics.tsv` is linked to the field `titleId` in `title.akas.tsv`.

```
csvgrep -t -m "tt0050083" -c titleId  title.akas.tsv | wc -l
```

The above command tells us the number fo variations - a total of 71 variations. How many different languages and regions does it have? This again is present in the file `title.akas.tsv` with the column headers `region` and `language`.

---

**Making things faster**
Did you notice that the above command took a bit long to execute? You can measure the time a command took by using the command `time` like this in the front of the command pipeline.

```
time csvgrep -t -m "tt0050083" -c titleId  title.akas.tsv | wc -l
```

It took around 65 seconds for me; depends on the memory and CPU of the machine that you use. This file as we have seen in lesson 1 is 'big' with 30 MM lines and hence searching through huge files will take time. So, if we want to further analyse the entries for the id "tt0050083", what we can do is to segregate the contents of the title with id "tt0050083" into another temporary file like this.

```
csvgrep -t -m "tt0050083" -c titleId  title.akas.tsv > /tmp/tt0050083
```

---

Now let us figure out the top 5 languages this movie has been translated (dubbed?)

```
csvcut -c "language"  /tmp/tt0050083  | sort | uniq -c | sort -nr | head -5
```

produces:

```
  49 \N
   5 en
   1 tr
   1 sv
   1 sr
```

which means 49 entries have no data on the field `language` and five entries says the language is `en` (English).

---

**Commands used**

- `csvcut` - extracts (cuts) a specific field in a delimited file - in this case we need only one column/field
  - `-c` option says which field - can be name of the header or the position
- `sort` - sorts what is spewed out from csvcut
- `uniq` - eliminates any duplicates
  - `-c` - gives the count of the duplicates, like `5 en` means `en` was present 5 times
- `sort -nr` - sorts the output from `uniq`
  - `-n` using numerical sort
  - `-r` using reverse lexicographical way to sort (in this case descending order)
- `head -5` - display first 5 lines - in this case it is the top five language variations

I know this is daunting. So, what you must do is to execute the above pipeline one command after the other to learn, like:

First, execute

`csvcut -c "language" /tmp/tt0050083`

and then

`csvcut -c "language" /tmp/tt0050083 | sort`

and gradually the full pipeline so that you understand each step.

---

### Step-3: Find out people behind the movie

From the [description](https://www.imdb.com/interfaces/) of the data files, the data file `title.principals.tsv` appears to have the details regarding main people involved in the movie. Let us launch a search!

```
csvgrep -m "tt0050083" -t  title.principals.tsv -c tconst
```

gives us this:

```
| tconst    | ordering | nconst    | category        | job                     | characters  |
| --------- | -------- | --------- | --------------- | ----------------------- | ----------- |
| tt0050083 |        1 | nm0000020 | actor           | \N                      | ["Juror 8"] |
| tt0050083 |        2 | nm0002011 | actor           | \N                      | ["Juror 3"] |
| tt0050083 |        3 | nm0000842 | actor           | \N                      | ["Juror 1"] |
| tt0050083 |        4 | nm0275835 | actor           | \N                      | ["Juror 2"] |
| tt0050083 |        5 | nm0001486 | director        | \N                      | \N          |
| tt0050083 |        6 | nm0741627 | writer          | story                   | \N          |
| tt0050083 |        7 | nm0006132 | composer        | \N                      | \N          |
| tt0050083 |        8 | nm0442100 | cinematographer | director of photography | \N          |
| tt0050083 |        9 | nm0503594 | editor          | \N                      | \N          |
```

We still do not know the names, all we know are some unique identifiers. For example `nm0000020` is the identifier of an actor in the movie.
Let us try to get all the names of the crew involved

```
csvgrep -t -r "nm0000020|nm0002011|nm0000842|nm0275835|nm0001486|nm0741627|nm0006132|nm0442100|nm0503594" -c nconst name.basics.tsv | csvlook
```

which spews:

```
| nconst    | primaryName    | birthYear | deathYear | primaryProfession                          | knownForTitles                          |
| --------- | -------------- | --------- | --------- | ------------------------------------------ | --------------------------------------- |
| nm0000020 | Henry Fonda    |     1,905 |     1,982 | actor,producer,soundtrack                  | tt0051207,tt0032551,tt0050083,tt0082846 |
| nm0000842 | Martin Balsam  |     1,919 |     1,996 | actor                                      | tt0050083,tt0054215,tt0059798,tt0074119 |
| nm0001486 | Sidney Lumet   |     1,924 |     2,011 | director,producer,writer                   | tt0050083,tt0070666,tt0072890,tt0292963 |
| nm0002011 | Lee J. Cobb    |     1,911 |     1,976 | actor,director,soundtrack                  | tt0050083,tt0070047,tt0053804,tt0047296 |
| nm0006132 | Kenyon Hopkins |     1,912 |     1,983 | music_department,composer,soundtrack       | tt0060009,tt0050083,tt0056753,tt0054997 |
| nm0275835 | John Fiedler   |     1,925 |     2,005 | actor,soundtrack                           | tt0050083,tt0065126,tt0063374,tt0076363 |
| nm0442100 | Boris Kaufman  |     1,906 |     1,980 | cinematographer,camera_department,director | tt0050083,tt0047296,tt0048973,tt0024844 |
| nm0503594 | Carl Lerner    |     1,912 |     1,973 | editor,editorial_department,director       | tt0057889,tt1147447,tt0050083,tt0067309 |
| nm0741627 | Reginald Rose  |     1,920 |     2,002 | writer,producer                            | tt0054531,tt0040051,tt0078294,tt0050083 |
```

What is new in the way we used `csvgrep` above? The usage of the `-r` option where you can provide [regular expressions](https://en.wikipedia.org/wiki/Regular_expression) and in this case we have:

```
-r "nm0000020|nm0002011|nm0000842|nm0275835|nm0001486|nm0741627|nm0006132|nm0442100|nm0503594"
```

the `|` means "or" - that means if one of them match. Regular expressions are a world by themselves and this tutorial does not cover that; anyone serious on command line, must know regular expressions well to be called power-user.

#### An observation

Were all crew of the movie _The 12 Angry Men_ are known primarily for that movie? Let's find out the main crew of the movie who were not 'known for' that movie with a slight variation in the command.

```
csvgrep -t -r "nm0000020|nm0002011|nm0000842|nm0275835|nm0001486|nm0741627|nm0006132|nm0442100|nm0503594" -c nconst name.basics.tsv | grep -v tt0050083
```

---

**Invert Match in grep**
`grep`'s `-v` option spews out lines that do not match the pattern. Looks like all the crew members are known for this movie as the output returns no (non)match.

---

### Step 4: The ratings, finally

As a final step in the qualitative assessment is to check the movie ratings.

```
csvgrep -m tt0050083 -c tconst title.ratings.tsv -t | csvlook
```

which spews

```
| tconst    | averageRating | numVotes |
| --------- | ------------- | -------- |
| tt0050083 |             9 |  743,120 |
```

Lets now find out where the movie stands in terms of number of votes; there are several movies that have a 10.0 rating with just 10 votes. So, just the rating does not speak much about a movie.

This command pipeline gives us what we want:

```
csvsort -t -c3 -r title.ratings.tsv| tail -n +2 | cat -n | grep tt0050083
```

which spews out:

```
115  tt0050083,9.0,743120
```

So, the movie stands 115th in position in number of votes.

---

**Commands used**

- `csvsort` - we use the `-r` option to do a reverse sort
- `tail` - we use this to eliminate the first line which is the column header
  - `-n +2` asks the command to output from the second line
- `cat` - is well known, but the option `-n` puts the line number in front of each line, since we wanted the ranking, it works right for us
- `grep` - our faithful guy to extract just what we need

---

## Review

What did we learn to do in this lesson?

- Learn to use the `csvtool` library for data processing
- Qualitative analysis of the data and corroborating with our intuition

Now that qualitative assessment is covered, move on to quantitative assessment in [Lesson 4](Lesson4.md).
