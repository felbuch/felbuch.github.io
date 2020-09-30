A Jedi can use the Force to bring people towards him.
We can do almost the same thing with Python's `requests` package.

## The Requests Package

[Requests](https://en.wikipedia.org/wiki/Requests_(software)) is a Python package to make HTTP requests.  
In simple terms, it fetches things from the internet.  
We can use it, for example, to fetch a character from StarWars from the the [Star Wars API](https://swapi.dev/). 

## A simple example: fetching one item from the web

You'll see its quite easy to **get** info you **request** from the Star Wars API:


```python
import requests

#Get character number 1 from the dataset
character_request = requests.get('https://swapi.dev/api/people/1')

#Show request
character_request
```




    <Response [200]>



You got your information, but its not yet very informative. Lets write this information as a JSON dictionary, so you can see better what we've got:


```python
#Transform into JSON
character_json = character_request.json()

#Show json
character_json
```




    {'name': 'Luke Skywalker',
     'height': '172',
     'mass': '77',
     'hair_color': 'blond',
     'skin_color': 'fair',
     'eye_color': 'blue',
     'birth_year': '19BBY',
     'gender': 'male',
     'homeworld': 'http://swapi.dev/api/planets/1/',
     'films': ['http://swapi.dev/api/films/1/',
      'http://swapi.dev/api/films/2/',
      'http://swapi.dev/api/films/3/',
      'http://swapi.dev/api/films/6/'],
     'species': [],
     'vehicles': ['http://swapi.dev/api/vehicles/14/',
      'http://swapi.dev/api/vehicles/30/'],
     'starships': ['http://swapi.dev/api/starships/12/',
      'http://swapi.dev/api/starships/22/'],
     'created': '2014-12-09T13:50:51.644000Z',
     'edited': '2014-12-20T21:17:56.891000Z',
     'url': 'http://swapi.dev/api/people/1/'}



Much more informative!  

In practice, we won't often be getting a single character. We'll most likely be interested in getting all characters and then working with that in Python. We'll want to have this info in a `pandas` dataframe, rather than as a series of JSON dictionaries. So lets start by transforming a single item into a `pandas` dataframe. There's a function in the `pandas` package to do that called `json_normalize`:  


```python
import pandas as pd

character_df = pd.json_normalize(character_json)
character_df
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
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
  </tbody>
</table>
</div>



You may run into trouble if some elements of the json file are themselves json documents, but let's not worry with that for now. Our concern is that we only got one character. But we're greedy. We follow the Dark Side. We want more: we want all characters from the StarWars universe. How can we use the Force to get that?

## A more realistic example: Getting all characters from the Star Wars universe

Unfortunately, I have no idea how to use the Force to get all StarWars characters.  
That would be AWESOME! But it's beyond my powers.  
What I do know is how to use `requests` to achieve the same end.

This is what we'll do:  
We will loop through all characters and download them one by one.  
We'll start with an empty dataframe and add each character we download to it.

But how do we know how many characters to look for?
If you just request for `people`, in general, without specifying anyone in particular, you get a dictionary. I won't print it, because it's a big dictionary. But it has an element called `count`:


```python
N = requests.get('https://swapi.dev/api/people').json()['count']
print(f'There are {N} Star Wars characters in the dataset')
```

    There are 82 Star Wars characters in the dataset


This is the number of people in our dataset. So we could, in principle, loop our request 82 times. But there's a catch: unfortunately, the dataset "jumps" some characters. Person 17, for example, does not exist:


```python
person17 = requests.get('https://swapi.dev/api/people/17')
person17.json()
```




    {'detail': 'Not found'}



So we need to check wheather each person exists or not. How can we do that?

When `requests` is succesful in finding a character, it returns a `status code` of 200: 


```python
person1 = requests.get('https://swapi.dev/api/people/1')
person1.status_code
```




    200



When `requests` _isn't_ succesful in finding a character, it returns a `status code` of 404:


```python
person17 = requests.get('https://swapi.dev/api/people/17')
person17.status_code
```




    404



So 200 is good.  
404 is bad.

We can therefore loop through requests until we get 82 times _succesful_ requests:


```python
%%time

import pandas

#Define empty dataframe to store characters
starwars = pd.DataFrame()

#Start from the first character in the dataset
i = 1

#while there are less than N people in the dataset...
while starwars.shape[0] < N: 
    
    #Make request
    character_request = requests.get('https://swapi.dev/api/people/' + str(i))
    
    #Check if a character was found:
    status_code = character_request.status_code
    if status_code == 200:
        
        #If so...
        
        #Transform to JSON
        character_json = character_request.json()
    
        #Transform to pandas dataframe
        character_df = pd.json_normalize(character_json)
    
        #Concatenate to existing dataframe
        starwars = pd.concat([starwars, character_df])
        
    else:
        #If no character was found, do nothing.
        pass
    
    #In either case, move on to next person
    i += 1
    
```

    CPU times: user 1.71 s, sys: 84 ms, total: 1.79 s
    Wall time: 55.3 s


Let's take a look at the data we've got:


```python
#Check number of rows and columns
starwars.shape
```




    (82, 16)




```python
#Show head of dataframe    
starwars.head()
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
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>0</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
    </tr>
    <tr>
      <th>0</th>
      <td>R2-D2</td>
      <td>96</td>
      <td>32</td>
      <td>n/a</td>
      <td>white, blue</td>
      <td>red</td>
      <td>33BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/8/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:11:50.376000Z</td>
      <td>2014-12-20T21:17:50.311000Z</td>
      <td>http://swapi.dev/api/people/3/</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Darth Vader</td>
      <td>202</td>
      <td>136</td>
      <td>none</td>
      <td>white</td>
      <td>yellow</td>
      <td>41.9BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/starships/13/]</td>
      <td>2014-12-10T15:18:20.704000Z</td>
      <td>2014-12-20T21:17:50.313000Z</td>
      <td>http://swapi.dev/api/people/4/</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Leia Organa</td>
      <td>150</td>
      <td>49</td>
      <td>brown</td>
      <td>light</td>
      <td>brown</td>
      <td>19BBY</td>
      <td>female</td>
      <td>http://swapi.dev/api/planets/2/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/30/]</td>
      <td>[]</td>
      <td>2014-12-10T15:20:09.791000Z</td>
      <td>2014-12-20T21:17:50.315000Z</td>
      <td>http://swapi.dev/api/people/5/</td>
    </tr>
  </tbody>
</table>
</div>



## Who's oldest? Who's youngest?

Who is the oldest character in the StarWars series? Who's the youngest?  

One of the columns in our dataset is `birth_year`. Unfortunately, we don't have year of death, so all we can answer is who would be oldest if it were alive today. Still, that's an interesting question to ask.  

Note that birth years are not referrenced as B.C. or A.C., but rather as BBY. That stands for **B**efore the **B**attle of **Y**evin. If you've never head of the battle of Yevin, go watch the movies! Or just click [here](https://en.wikipedia.org/wiki/Yavin).  

To calculate how old each character would be today, we must know which year we're in *now* relative to the Battle of Yevin. Surprisingly, if you search the web, you'll find a huge debate on weather we're currently before or after the Battle of Yevin.  

## Small digression: What year does StarWars take place?

One side of the debate says we're after the Battle of Yevin. After all, StarWars happened *long, long ago, in a galaxy far, far away*. So the battle of Yevin must have been in the past. Some people say, however, that the *long, long ago* [does not refer to our own, human standards](https://www.quora.com/Does-Star-Wars-take-place-in-the-past-or-the-future). StarWars is not about us, humans. So even when it says *long, long ago*, it's from the viewpoint of an alien civilization, not ours.  

Frankly, the arguments can get quite crazy, with some people even saying Star Wars happened in [1627](https://www.denofgeek.com/games/what-year-does-star-wars-the-force-awakens-take-place-in/#:~:text=1607%20AD%20is%2014%20years,in%20the%20year%201627%20AD.). That's neither *long, long ago*, nor in the future! In fact, in 1627, Rembrandt was alive, the 30 years war was sweeping accross Europe, and the last of the aurochs were being shot dead in Poland. 

Ultimately, we're forced to the inevitable conclusion: StarWars *never* happened. Not in the past, not in the future. It's fiction. Setting it *a long, long time ago* was a [fictional decision](https://scifi.stackexchange.com/questions/224126/why-did-george-lucas-set-star-wars-in-the-past-instead-of-the-future). That's all.  

But I still want to calculate the ages of StarWars characters, so I must accept some theory of which year is now, relative to the StarWars chronology. I'll follow [in-universe calendar system adopted by the Galactic Senate following the Treaty of Coruscant](https://torcommunity.com/guides/game-basics/timeline). According to this calendar, **2020 AD corresponds to the year 3630 BBY**

According to this calendar, the Battle of Yevin has not yet happened, and Star Wars happens in the future. This means _age_ is not really _age_, but rather _time until birth_

Interestingly all characters are born before the battle of Yevin:


```python
starwars.birth_year.str.endswith('BBY').count() == N
```




    True



This means we can simply stripe away the BBY suffix to transform birth date into a number we can make calculation with. In addition, note that the most ancient birth year in the dataset is 896 BBY:


```python
starwars.birth_year.str.replace('BBY','').replace('unknown',np.nan).astype('float').max()
```




    896.0



Recall that our current year is 3630 BBY. This means all characters are yet to be born, and indeed they won't be born untill many, many, many years into the future.



## Back to calculating characters' ages

You may not be reading this post in 2020 A.D. But if 2020 A.D. corresponds to 3630 BBY, then we can easily translate your current year to BBY:


```python
import pendulum

current_year = (pendulum.now().year - 2020) + 3630

print(f'The current year in StarWars chronology is {current_year} BBY')
```

    The current year in StarWars chronology is 3630 BBY


Likewise, the age of any character will be given by subtracting the current year from his year of birth. To do that, we'll need to do some string cleaning, but that's nothing a Padawan can't handle:


```python
starwars['age'] = (starwars.birth_year.str.
                   replace('BBY','').
                   replace('unknown',np.nan).
                   astype('float').
                   pipe(lambda x: current_year - x)
                   )
```

Let's take a peek at what we've got:


```python
starwars.loc[:,['name','birth_year','age']].dropna().head()
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
      <th>birth_year</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>19BBY</td>
      <td>3611.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>C-3PO</td>
      <td>112BBY</td>
      <td>3518.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>R2-D2</td>
      <td>33BBY</td>
      <td>3597.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Darth Vader</td>
      <td>41.9BBY</td>
      <td>3588.1</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Leia Organa</td>
      <td>19BBY</td>
      <td>3611.0</td>
    </tr>
  </tbody>
</table>
</div>



Its now easy to find the youngest and oldest character in the StarWars expanded universe:


```python
starwars_age = starwars.loc[:,['name','birth_year','age']].dropna().sort_values('age')
```


```python
#Oldest (first caracter to be born)
starwars_age.head(1)
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
      <th>birth_year</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Yoda</td>
      <td>896BBY</td>
      <td>2734.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Youngest (last caracter to be born)
starwars_age.tail(1)
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
      <th>birth_year</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Wicket Systri Warrick</td>
      <td>8BBY</td>
      <td>3622.0</td>
    </tr>
  </tbody>
</table>
</div>



Remember: since Star Wars actually happens in the future, "age" is actually "time until birth". This means [Yoda](https://en.wikipedia.org/wiki/Yoda) is the first character to be born. He will be born in 2,734 years. [Wicket Systri Warrick](https://en.wikipedia.org/wiki/Wicket_W._Warrick) will be born almost 900 years later, 3,633 years from now.  

To be fair, we don't have birth year for all characters:


```python
starwars.loc[starwars.birth_year == 'unknown',['name','birth_year']].head()
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
      <th>birth_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>R5-D4</td>
      <td>unknown</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Jek Tono Porkins</td>
      <td>unknown</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Arvel Crynyd</td>
      <td>unknown</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Nien Nunb</td>
      <td>unknown</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Nute Gunray</td>
      <td>unknown</td>
    </tr>
  </tbody>
</table>
</div>



This means there can be characters that are still to be born before Yoda, or after Wicket Systri Warrick. But with missing data, there's not much we can do, other than trust the Force.
