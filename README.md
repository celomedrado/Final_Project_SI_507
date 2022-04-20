# Final_Project_SI_507

## ✨Project Description

The program will ask the user questions in the command prompt such as "'What type of food would you like to eat?" and will provide a list of categories. The user will choose a category and more questions will be asked in order to walk through the tree. Finally, the program will show a list of recommendations of restaurants in the Ann Arbor region, their price level, ratings, address, and phone number.

The data source used is from Yelp Fusion API. 

### *Example*

This is the first question that the program will ask to the user. Then, it will drill down in more categories to learn more about the user's choices.

```console
What type of food would you like to eat? 

1 - Deli
2 - Coffee and Tea
3 - Deserts
4 - American
5 - Healthy
6 - International
7 - Bars
8 - Mediterranean and Seafood
Enter a number for the category of your choice, or "exit" to quit:
```

After asking some questions to the user, the program will provide a list of restaurantes based on the user's choices.

```console
Great! Here is the list of Indian restaurants in Michigan that I recommend based on your answers: 

Restaurant: Hutkay Fusion
----------------------------
-> Rating: 5.0
-> Price: Price not provided
-> City: Ann Arbor
-> Address: 3022 Packard St
-> Phone: (734) 786-8312

#######################################

Restaurant: Everest Sherpa Restaurant
----------------------------
-> Rating: 4.5
-> Price: $$
-> City: Ann Arbor
-> Address: 2803 Oak Valley Dr
-> Phone: (734) 997-5490

#######################################
```

## :wrench: Packages

- Requests
- Json

## :key: How to supply API keys

In order to access Yelp Fusion's API, I signed up on their website and got their API KEY. The keys provided by Yelp Fusion's API are stored in a file called `secrets.py`.

The API requires that you pass the key as the header of your request by using the following sintax.

```python
headers = {'Authorization': yelp_key}
```

### Endpoint

The endpoint that I used to get the data from the API was the following.

```python
endpoint_url = 'https://api.yelp.com/v3/businesses/search'
```

### Params

In order to get the restaurants from Ann Arbor region at Yelp Fusion's API, I used the following params. 

This request returned 1000 records from the API. 

```python
for offset in range(0, 1000, 50):
    params = {
        'location': 'Ann Arbor',
        'limit': '50',
        'sort_by':'rating',
        'offset': offset
    }
    results.extend(make_request_with_cache(endpoint_url, params=params, headers=headers)['businesses'])
```

