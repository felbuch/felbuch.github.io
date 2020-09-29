## Introduction

Python and SQL are essential tools for the Data Scientist.  
In this post, we're going to explore how to connect the two. Namely, we're going to perform a task partly in Python and partly in SQL, all from within a Jupyter Notebook. The task will be to transform a python dataframe into a SQL database in the 3rd normal form. Then, we'll run a SQL query in the database and show it works.   

## First look at the data

The data we'll be using is a dataset of [Spotify songs](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-01-21/readme.md). This is how it looks:


```python
import pandas as pd
```


```python
spotify = pd.read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-21/spotify_songs.csv')
spotify.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>track_name</th>
      <th>track_artist</th>
      <th>track_popularity</th>
      <th>track_album_id</th>
      <th>track_album_name</th>
      <th>track_album_release_date</th>
      <th>playlist_name</th>
      <th>playlist_id</th>
      <th>playlist_genre</th>
      <th>...</th>
      <th>key</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>acousticness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
      <th>duration_ms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>I Don't Care (with Justin Bieber) - Loud Luxur...</td>
      <td>Ed Sheeran</td>
      <td>66</td>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>I Don't Care (with Justin Bieber) [Loud Luxury...</td>
      <td>2019-06-14</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>6</td>
      <td>-2.634</td>
      <td>1</td>
      <td>0.0583</td>
      <td>0.1020</td>
      <td>0.000000</td>
      <td>0.0653</td>
      <td>0.518</td>
      <td>122.036</td>
      <td>194754</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Memories - Dillon Francis Remix</td>
      <td>Maroon 5</td>
      <td>67</td>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>Memories (Dillon Francis Remix)</td>
      <td>2019-12-13</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>11</td>
      <td>-4.969</td>
      <td>1</td>
      <td>0.0373</td>
      <td>0.0724</td>
      <td>0.004210</td>
      <td>0.3570</td>
      <td>0.693</td>
      <td>99.972</td>
      <td>162600</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>All the Time - Don Diablo Remix</td>
      <td>Zara Larsson</td>
      <td>70</td>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>All the Time (Don Diablo Remix)</td>
      <td>2019-07-05</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>1</td>
      <td>-3.432</td>
      <td>0</td>
      <td>0.0742</td>
      <td>0.0794</td>
      <td>0.000023</td>
      <td>0.1100</td>
      <td>0.613</td>
      <td>124.008</td>
      <td>176616</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>Call You Mine - Keanu Silva Remix</td>
      <td>The Chainsmokers</td>
      <td>60</td>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>Call You Mine - The Remixes</td>
      <td>2019-07-19</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>7</td>
      <td>-3.778</td>
      <td>1</td>
      <td>0.1020</td>
      <td>0.0287</td>
      <td>0.000009</td>
      <td>0.2040</td>
      <td>0.277</td>
      <td>121.956</td>
      <td>169093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Someone You Loved - Future Humans Remix</td>
      <td>Lewis Capaldi</td>
      <td>69</td>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>Someone You Loved (Future Humans Remix)</td>
      <td>2019-03-05</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>1</td>
      <td>-4.672</td>
      <td>1</td>
      <td>0.0359</td>
      <td>0.0803</td>
      <td>0.000000</td>
      <td>0.0833</td>
      <td>0.725</td>
      <td>123.976</td>
      <td>189052</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 23 columns</p>
</div>



Each row is a different track. But not all columns refer to tracks: `track_album_release_date`, for example, refers to the album, and `playlist_genre` to the playlist. In addition, some information is redundant: if I know the `track_name`, don't I know the `track_id` already? -- These things show our dataset is not on the 3rd normal form.  

## Quick refresher on database normalization

You'll find lots of fancy explanations out there for what database normalization is. In short, it's good practice to avoid bad things from happening. Now, there are different levels of good practice that you can adopt, and these are the so-called **normalziation forms**. 

### First normal form (1NF)

A database is on the first normal form when each column represents only one piece of information. So you don't have the name of the song _and_ the name of the singer on the same column. These are two different pieces of information, and therefore must be in two different columns.

### Second normal form (2NF)

Once a database is on the first normal form, it takes a **primary key** to put it into the second normal form. A primary key is a set of one or more columns that allow you to uniquely identify each row. Given the primary key, all other columns should be determined beyond doubt. So knowing the primary key is all you need to know to be able to look in the database and find everything else you wish to know.

### Third normal form (3NF)

In the second normal form, the key tells you everything about all other columns. But that doesn't mean you can't identify a column from another without needing a key. Think of date of birth and age, for instance. If I know your date of birth, I know your age, without any need to know what your primary key is. If a dataset is on the third normal form, this is not allowed. Only the primary key gives information about the remaining columns. No other column can be informative about any other column.

## Is the Spotify dataset normalized?

In a word, no. But that's not the interesting question. The interesting question is why and, most importantly, what steps must be taken to normalize it.

Look at the dataset's columns:


```python
for col in spotify.columns:
    print(col)
```

    track_id
    track_name
    track_artist
    track_popularity
    track_album_id
    track_album_name
    track_album_release_date
    playlist_name
    playlist_id
    playlist_genre
    playlist_subgenre
    danceability
    energy
    key
    loudness
    mode
    speechiness
    acousticness
    instrumentalness
    liveness
    valence
    tempo
    duration_ms


`track_id` refers to a track. `album_id` refers to the album the track is in. These are different entities, and should be in different tables. The Spotify table has information about four entities:
* tracks
* albums
* playlists
* genres
We must thus have a table for each one of these.  

In addition, we must also have tables to capture the relationships between these tables:
* which tracks are there in an album
* which (sub)genres are there in a playlist
* which tracks are there in a playlist

Finally, we must break down tables where the primary key is not the only column that can identify other columns. In the track table, for instance, we have both `track_id` and `track_name`. Either one is arguably sufficient to determine the remaining columns, so that violates the requirements for a dataset to be in the third normal form. We'll have to break that up into two tables: one relating `track_name` to `track_id` and another one relating `track_id` to everythign else. Applying this approach to the previous tables will yield us the following extra tables:
* a table relating `track_name` to `track_id`
* a table relating `track_album_id` to `track_album_name`
* a table relating `track_album_id` to `track_album_release_dates`

To do this, we need to verify that a column we believe to be a primary key is indeed a valid primary key. In fact, it doesn't need to be a single column. It can be a set of columns, as long as the can uniquely identify each row in a table. Let's build a function to help us identify if a column (or set of columns) is indeed a primary key:


```python
def is_primary_key(candidate, df):
    
    '''Verifies if a column is a valid primary key for a dataframe.
    A column (or list of columns) is a valid primary key if it uniquely identifies its rows
    This function throws an assertion error if candidate is not a valid primary key.
    
    Parameters
    ----------
    
    candidate: string or list of strings 
               The name of the column we wish to test if its a valid primary key.
               Alternatively, a list containing the names of the columns that compose a multiple primary key
    df:        dataframe
               The table for which we wish to assess if candidate is a valid primary key
               
    Returns
    -------
    None
    
    '''
    
    max_frequency_of_candidate = (df.
                                  loc[:,candidate].
                                  value_counts().
                                  max()
                                 )
    
    #Key only appears once
    assert (max_frequency_of_candidate == 1), 'The is more than one row associated with the same key'
```

We're now ready to get started normalizing the Spotify dataset.

## Normalizing the Spotify dataset

Let's start with information about the tracks. Tracks are an entity of their own: they are a "thing", with characteristics we wish to register. So let's make a table for the tracks:

### TRACKS table

We could be tempted to say that all variables that start with the word `track` are columns of our table. This, however, can be misleading. A column such as `track_album_name` is actually a feature of the album that contains the track, not of the track itself. The same is true for `track_album_id`.  

Conversely, variables such as `danceability` are features of the track, even though they don't start with "track". This is the case for all columns following `danceability`, including `energy`, `key`, and all the way up to `duration_ms`.  

Finally, some columns would violate the conditions for the dataset to be in the third normal form. Take `track_name`, for instance. Knowing `track_name` allows me to identify other characteristics of the track without having to know its `track_id`. That's bad. We'll have to take `track_name` out of the table and store it in a table of its own. 

With this in mind, we can build the `TRACK` table in three steps:
* We get all columns that start with "track";
* We add all columns from `danceability` to `duration_ms`
* We manually exclude the columns that actually refer to something else;



```python
#Auxiliary tables
track_1 = spotify.loc[:, spotify.columns.str.startswith('track')] #all columns that start with 'track'
track_2 = spotify.loc[:, 'danceability':] #columns that refer to the track but don't start with 'track'


#Define columns to exclude
redundant_columns = {'track_name', #Each track id only has one name
                     'track_album_id', #Each track id is only on one album
                     'track_album_name', #This is a characteristic of the album, not of the track
                     'track_album_release_date'} #This is a characteristic of the album, not of the track


#Build table
track = (pd.concat([track_1, track_2], axis=1). #join columns that have the word track or refer to the track without explicit mention
         drop(columns = redundant_columns). #Exclude columns that refer to something else
         drop_duplicates()
        )

#Verify legitimacy of primary key
is_primary_key('track_id', track)

#Show head
track.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>track_artist</th>
      <th>track_popularity</th>
      <th>danceability</th>
      <th>energy</th>
      <th>key</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>acousticness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
      <th>duration_ms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>Ed Sheeran</td>
      <td>66</td>
      <td>0.748</td>
      <td>0.916</td>
      <td>6</td>
      <td>-2.634</td>
      <td>1</td>
      <td>0.0583</td>
      <td>0.1020</td>
      <td>0.000000</td>
      <td>0.0653</td>
      <td>0.518</td>
      <td>122.036</td>
      <td>194754</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Maroon 5</td>
      <td>67</td>
      <td>0.726</td>
      <td>0.815</td>
      <td>11</td>
      <td>-4.969</td>
      <td>1</td>
      <td>0.0373</td>
      <td>0.0724</td>
      <td>0.004210</td>
      <td>0.3570</td>
      <td>0.693</td>
      <td>99.972</td>
      <td>162600</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>Zara Larsson</td>
      <td>70</td>
      <td>0.675</td>
      <td>0.931</td>
      <td>1</td>
      <td>-3.432</td>
      <td>0</td>
      <td>0.0742</td>
      <td>0.0794</td>
      <td>0.000023</td>
      <td>0.1100</td>
      <td>0.613</td>
      <td>124.008</td>
      <td>176616</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>The Chainsmokers</td>
      <td>60</td>
      <td>0.718</td>
      <td>0.930</td>
      <td>7</td>
      <td>-3.778</td>
      <td>1</td>
      <td>0.1020</td>
      <td>0.0287</td>
      <td>0.000009</td>
      <td>0.2040</td>
      <td>0.277</td>
      <td>121.956</td>
      <td>169093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Lewis Capaldi</td>
      <td>69</td>
      <td>0.650</td>
      <td>0.833</td>
      <td>1</td>
      <td>-4.672</td>
      <td>1</td>
      <td>0.0359</td>
      <td>0.0803</td>
      <td>0.000000</td>
      <td>0.0833</td>
      <td>0.725</td>
      <td>123.976</td>
      <td>189052</td>
    </tr>
  </tbody>
</table>
</div>



And we'll also have to build another table to store the relationship between `track_id` and `track_name`.:

### TRACK_NAMES table


```python
#Create table
track_names = (spotify.
               loc[:,['track_id','track_name']].
               drop_duplicates()
              )

#Rename columns
track_names.columns = ['track_id','name']

#Verify legitimacy of primary key
is_primary_key('track_id', track_names)

#Show head
track_names.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>I Don't Care (with Justin Bieber) - Loud Luxur...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Memories - Dillon Francis Remix</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>All the Time - Don Diablo Remix</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>Call You Mine - Keanu Silva Remix</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Someone You Loved - Future Humans Remix</td>
    </tr>
  </tbody>
</table>
</div>



### A word on albums

We now move to another entity: the albums.  
Albums have ids, names and release dates. While the id is the most natural primary key, note that if you know the album's name, you may be able to identify its release date without knowing its id. So we'll have to split these columns into different tables. Rather than having one big `ALBUM` table, we'll have:
* an `ALBUM_NAMES` table, that lists album names with their corresponding identifiers;
* an `ALBUM_TRACKS` table, that says which tracks are in each album;
* an `ALBUM_RELEASE_DATES` table, that lists the release dates for each album;

Each of these tables answers a very natural question to ask about albums:
* What albums do we have in our dataset?
* Which tracks are within each album?
* When was each album released?

So let's build those tables and address these questions:

### ALBUM_NAMES table

What albums do we have in our dataset?


```python
#Create dataset
album_names = (spotify.
               loc[:, ['track_album_id','track_album_name']].
               drop_duplicates()
        )

#Rename columns
album_names.columns = ['album_id','name']

#Verify validity of primary key
is_primary_key('album_id', album_names)

album_names.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album_id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>I Don't Care (with Justin Bieber) [Loud Luxury...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>Memories (Dillon Francis Remix)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>All the Time (Don Diablo Remix)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>Call You Mine - The Remixes</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>Someone You Loved (Future Humans Remix)</td>
    </tr>
  </tbody>
</table>
</div>



### ALBUM_TRACKS table

Which tracks are within each album?


```python
#Create table
album_tracks = (spotify.
                loc[:,['track_id','track_album_id']].
                drop_duplicates()
              )

#Rename columns
album_tracks.columns = ['track_id','album_id']

#Verify validity of primary key
is_primary_key('track_id', album_tracks)

#Show head
album_tracks.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>album_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>63rPSO264uRjW1X5E6cWv6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
    </tr>
  </tbody>
</table>
</div>



Note that here, it is the `track_id`, not the `album_id` that is the primary key. That's only natural: it's an album that has many tracks, not the other way round.

### ALBUM_RELEASE_DATES table

When was each album released?


```python
#Create table
album_release_dates = (spotify.
                       loc[:,['track_album_id','track_album_release_date']].
                       drop_duplicates()
                      )

#Rename columns
album_release_dates.columns = ['album_id','release_date']

#Verify validity of primary key
is_primary_key('album_id', album_release_dates)

#Show head
album_release_dates.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album_id</th>
      <th>release_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>2019-06-14</td>
    </tr>
    <tr>
      <th>1</th>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>2019-12-13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>2019-07-05</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>2019-07-19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>2019-03-05</td>
    </tr>
  </tbody>
</table>
</div>



### A word on playslits

playlist allows us to group songs based on genre and sub-genre. We've all been there. Sometimes we feel like shaking up baby, [twist and shout](https://www.youtube.com/watch?v=b-VAxGJdJeQ)! And then, [rainy days and mondays always bring be down](https://www.youtube.com/watch?v=PjFoQxjgbrs). In addition, there may be certain genres we simply don't enjoy. So it makes sense to group songs based on genres and subgenres.  

As the name suggests, a subgenre is a niche within a genre. Each subgenre belongs to a single genre:


```python
(spotify.
 loc[:,['playlist_genre','playlist_subgenre']].
 drop_duplicates().
 groupby('playlist_subgenre').
 count()
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_genre</th>
    </tr>
    <tr>
      <th>playlist_subgenre</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>album rock</th>
      <td>1</td>
    </tr>
    <tr>
      <th>big room</th>
      <td>1</td>
    </tr>
    <tr>
      <th>classic rock</th>
      <td>1</td>
    </tr>
    <tr>
      <th>dance pop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>electro house</th>
      <td>1</td>
    </tr>
    <tr>
      <th>electropop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>gangster rap</th>
      <td>1</td>
    </tr>
    <tr>
      <th>hard rock</th>
      <td>1</td>
    </tr>
    <tr>
      <th>hip hop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>hip pop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>indie poptimism</th>
      <td>1</td>
    </tr>
    <tr>
      <th>latin hip hop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>latin pop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>neo soul</th>
      <td>1</td>
    </tr>
    <tr>
      <th>new jack swing</th>
      <td>1</td>
    </tr>
    <tr>
      <th>permanent wave</th>
      <td>1</td>
    </tr>
    <tr>
      <th>pop edm</th>
      <td>1</td>
    </tr>
    <tr>
      <th>post-teen pop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>progressive electro house</th>
      <td>1</td>
    </tr>
    <tr>
      <th>reggaeton</th>
      <td>1</td>
    </tr>
    <tr>
      <th>southern hip hop</th>
      <td>1</td>
    </tr>
    <tr>
      <th>trap</th>
      <td>1</td>
    </tr>
    <tr>
      <th>tropical</th>
      <td>1</td>
    </tr>
    <tr>
      <th>urban contemporary</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Hence, if we know a playlist's subgenre, we automatically know its genre.  

But the opposite isn't true. Each genre has multiple subgenres within itself:


```python
(spotify.
 loc[:,['playlist_genre','playlist_subgenre']].
 drop_duplicates().
 groupby('playlist_genre').
 count()
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_subgenre</th>
    </tr>
    <tr>
      <th>playlist_genre</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>edm</th>
      <td>4</td>
    </tr>
    <tr>
      <th>latin</th>
      <td>4</td>
    </tr>
    <tr>
      <th>pop</th>
      <td>4</td>
    </tr>
    <tr>
      <th>r&amp;b</th>
      <td>4</td>
    </tr>
    <tr>
      <th>rap</th>
      <td>4</td>
    </tr>
    <tr>
      <th>rock</th>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



Thus, we just need to keep track of the subgenre of each playlist. The genre can be kept on another table, and indeed must. This is a very clear example of what third form normalization means. If we had `playlist_id`, `playlist_genre` and `playlist_subgenre` on a single table, then we wuold be able to guess `playlist_genre` just by knowing `playlist_subgenre`, without needing to know the primay key (`playlist_id`). This shouldn't be possible in a 3NF dataset. _Only the key must be informative_. For this reason, we'll make three distinct tables:

* A table that relates which tracks are within each playlist
* A table that relates each playlist to its name
* A table that relates each playlist to its subenre
* A table that relates each subgenre to its genre

Let's to it:

### PLAYLIST_TRACKS table

Which tracks are in each playlist?


```python
playlist_tracks = (spotify.
                   loc[:,['playlist_id','track_id']].
                   drop_duplicates()
                  )
```

Interestingly, in this table, neither column alone is a primary key. Both columns are needed to identify each row.


```python
is_primary_key(['playlist_id','track_id'], playlist_tracks)
```

### PLAYLIST_NAMES table

Now we're interested in what playlists we have. 


```python
playlist = (spotify.loc[:,['playlist_id','playlist_name']].
            drop_duplicates()
           )

is_primary_key('playlist_id',playlist)

playlist.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_id</th>
      <th>playlist_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>Pop Remix</td>
    </tr>
    <tr>
      <th>70</th>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>Dance Pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>Dance Room</td>
    </tr>
    <tr>
      <th>223</th>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>Cardio</td>
    </tr>
    <tr>
      <th>272</th>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>Dance Pop Hits</td>
    </tr>
  </tbody>
</table>
</div>



### PLAYLIST_SUBGENRES table

A playlist may be associated to more than a single subgenre. Therefore, this is another interesting example where we actually need our primary key to be composed of multiple columns (in this case, as before, all columns in the table). This is because this table does not capture an entity, but rather an [many-to-many relationship](https://en.wikipedia.org/wiki/Many-to-many_(data_model)): a playlist can have multiple subgenres and each subgenre can be used in multiple playlists. We call such tables _associative tables_.  

One of the misconceptions I had when I first started learning about relational databases was that tables refered to "things", such as a track, or an album. Truth is much more beautiful: tables can also be used to map relationships, such as we see with associative tables. 

This is one such table:


```python
playlist_subgenres = (spotify.loc[:,['playlist_id','playlist_subgenre']].
                      drop_duplicates()
                     )

playlist_subgenres.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_id</th>
      <th>playlist_subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>70</th>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>223</th>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>272</th>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>dance pop</td>
    </tr>
  </tbody>
</table>
</div>



No single column acts as a primary key in this case:


```python
is_primary_key(['playlist_id','playlist_subgenre'], playlist_subgenres)
```

### GENRES table

We need one more table, and that's the table of genres. It lets us figure out the genre of a playlist given its subgenre. Technically speaking, genres are an entity of its own. It's not a playlist, not a track, and not an album. It's something a thing on its own.

The properties of a genre are the many subgenres associated to it. Let's build this table:


```python
#Build table
genres = (spotify.
         loc[:, ['playlist_genre','playlist_subgenre']].
         drop_duplicates()
        )

#rename columns
genres.columns = ['genre', 'subgenre']

#show head
genres.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>genre</th>
      <th>subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>1298</th>
      <td>pop</td>
      <td>post-teen pop</td>
    </tr>
    <tr>
      <th>2427</th>
      <td>pop</td>
      <td>electropop</td>
    </tr>
    <tr>
      <th>3835</th>
      <td>pop</td>
      <td>indie poptimism</td>
    </tr>
    <tr>
      <th>5507</th>
      <td>rap</td>
      <td>hip hop</td>
    </tr>
  </tbody>
</table>
</div>



_Et voilà!_ With this, our dataset is (almost) complete.

We have tracks, albums, playlists and genres.
We have a table to connect tracks with albums and tracks to playlists, as well as a table to connect playlists to genres.  

Before we declare our dataset complete, let's save make it a SQL database.  
A database must have some self-respect, and cannot be content with being a Pandas dataframe forever. 

## Saving our dataset to SQL

Pandas aren't jealous, so they have no problem converting our dataset to SQL. In fact, they even provide us with a method do do that. It's called `.to_sql` (cute as a panda, right?)  

First things first. SQL comes in many flavours. We'll be using [SQLite](https://www.sqlite.org/index.html), and the [sqlite3](https://docs.python.org/3/library/sqlite3.html#module-sqlite3) python package.  

To sabe our pandas dataframes as a SQL dataset, we first need to create a connection. Then, we'll save our dataframes using the this connection we just established.


```python
#Import package to use SQLite from Python
import sqlite3

#Create connection
conn = sqlite3.connect('spotify.db')
```

We now have a connection to a database called `spotify.db`:


```python
conn
```




    <sqlite3.Connection at 0x7f59c2ca1c70>



We'll use this connection to save our datasets using the `.to_sql` method. The command is the same for all datasets, but I'll do the `TRACK` table separately, to the synthax clear. See if you can understand it. It's quite easy:


```python
#First, the TRACK dataset
track.to_sql('track', 
             conn, 
             if_exists='replace', 
             index = False)

#Now, repeat the same for all other datasets
track_names.to_sql('track_names', conn, if_exists='replace', index = False) 
album_tracks.to_sql('album_tracks', conn, if_exists='replace', index = False)
album_names.to_sql('album_names', conn, if_exists='replace', index = False)
album_release_dates.to_sql('album_release_dates', conn, if_exists='replace', index = False)
playlist.to_sql('playlist', conn, if_exists='replace', index = False)
genres.to_sql('genres', conn, if_exists='replace', index = False)
```

You might have guesses what's going on. We're getting a dataset (say, track) and saving it as a SQL table (also called 'track') in the database we're connected to. If such a table already exists, replace it by whatever we have now. And please, no indexing at this time!

## Does it work?

Don't take my word that the data's up and running. Let's run a query to verify that indeed it is. This is what we'll do:

> Use an SQL query to find the names of all playlists that contain instrumentals

A query contains instrumentals when its instrumentalness (one of the column in the TRACKS table) is positive. To keep the output visually neat, we'll limit our output to 10 tracks only.

To make an SQL query, we begin by writting the query as a multi-line string in Python:


```python
query = '''
SELECT track_names.name, track.track_artist, track.instrumentalness
FROM track
LEFT JOIN
track_names
ON track.track_id = track_names.track_id
WHERE track.instrumentalness > 0 -- a query contains instrumentals when its instrumentalness is greater than zero
ORDER BY track.instrumentalness DESC
LIMIT 10 -- we'll limit our output to 10
'''
```

Then, we use Pandas' `read_sql` function (and the connection to the database) to read the query:


```python
pd.read_sql(query, conn)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>track_artist</th>
      <th>instrumentalness</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Chill Waves &amp; Wind in Leaves</td>
      <td>Pinetree Way</td>
      <td>0.994</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Summer Rain</td>
      <td>Rain Sounds FX</td>
      <td>0.994</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sandstorm - Radio Edit</td>
      <td>Darude</td>
      <td>0.987</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Genesis</td>
      <td>Rob Stepwart</td>
      <td>0.983</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sandstorm - Radio Edit</td>
      <td>Darude</td>
      <td>0.982</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Flowers</td>
      <td>The Deli</td>
      <td>0.981</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Tropical Forest</td>
      <td>The Sleep Specialist</td>
      <td>0.979</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Spanish Galleon</td>
      <td>Wun Two</td>
      <td>0.974</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Rain Forest and Tropical Beach Sound</td>
      <td>Nature Sounds Nature Music</td>
      <td>0.974</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Alignment</td>
      <td>Guustavv</td>
      <td>0.972</td>
    </tr>
  </tbody>
</table>
</div>



It works -- and that is music to my ears!
