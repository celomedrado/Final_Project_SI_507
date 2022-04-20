# Final_Project_SI_507 - Data Organization

## Creation of Macro Categories
--- 


After making the request to Yelp Fusion's API, I noticed that Yelp organizes its restaurants in a lot of categories. As a result, I had to create macro categories and sub categories to provide a more friendly user experience.

Here are the macro categories I created. 

```python
# Categories
categories = ['Deli', 'Coffee and Tea', 'Deserts', 'American', 'Healthy', 
'International', 'Bars', 'Mediterranean and Seafood']
```

Then, I had to create subcategories inside these macro categories.


```python
# Subcategories
Deli = ['Sandwiches', 'Delis', 'Bakeries','Bagels']

CoffeeAndTea = ['Coffee & Tea', 'Cafes', 'Coffee Roasteries', 'Tea Rooms']

Deserts = ['Desserts', 'Chocolatiers & Shops', 'Ice Cream & Frozen Yogurt', 
'Patisserie/Cake Shop', 'Gelato', 'Macarons', 'Shaved Ice', 'Custom Cakes', 'Cupcakes', 'Candy Stores' ]

American = ['American (Traditional)', 'Middle Eastern', 'American (New)',
'Pizza','Breakfast & Brunch','Diners','Fast Food','Burgers','Hot Dogs',
'Chicken Wings','Donuts','Soul Food','Cheesesteaks','Southern','Cajun/Creole'
,'Steakhouses','Smokehouse','Barbeque','Waffles']

Healthy = ['Juice Bars & Smoothies','Salad','Soup','Vegan','Health Markets',
'Vegetarian','Gluten-Free','Wraps','Fruits & Veggies','Live/Raw Food']

International = ['Africa', 'Asia', 'Latin America', 'Europe', 'Canada']

Bars = ['Distilleries','Whiskey Bars','Cocktail Bars','Wine Bars','Beer Bar',
'Tiki Bars','Bars','Brewpubs','Dive Bars','Breweries','Gastropubs','Pubs','Meaderies',
'Beer, Wine & Spirits','Speakeasies','Beer Gardens','Sports Bars','Irish Pub',
'Hookah Bars','Karaoke','Restaurants','Pool Halls','Eatertainment']

MediterraneanAndSeafood = ['Seafood','Mediterranean','Seafood Markets']
```

Finally, the International subcategory had too many categories inside, so I created a last subcategory for the International subcategory.

```python
Africa = ['African','Moroccan','Senegalese','Ethiopian']

Asia = ['Lebanese','Chinese','Indian','Himalayan/Nepalese','Halal',
'Asian Fusion','Arabic','Ramen','Japanese','Noodles','Bubble Tea','Sushi Bars','Thai',
'Pan Asian','Poke','Syrian','Vietnamese','Bangladeshi','Russian','Izakaya','Armenian',
'Filipino','Korean','Szechuan','Falafel','Dim Sum','Laotian','Pakistani']

LatinAmerica = ['Tapas/Small Plates','Mexican','Tacos','Salvadoran','Peruvian','Venezuelan',
'Argentine','Food Stands','Caribbean','Cuban','Latin American','Honduran','Tapas Bars','Dominican',
'Brazilian','Acai Bowls']

Europe = ['Italian','Polish','Greek','Fish & Chips','Ukrainian','Spanish','Modern European','German',
'Irish','Hungarian','Kebab','British','Belgian','French','Creperies']

Canada = ['Canadian (New)','Poutineries']
```

## Organizing data into a dictionary
---

With all the organization of the categories done, I cleaned the data to get only the variables that mattered to the user and organized them into a list of dictionaries.

>I noticed that some restaurants don't have the variable price, so I had to adjust that.

```python
for i in results:

    try:
        restaurants.append({
            'name': i['name'],
            'categories': i['categories'],
            'rating': i['rating'],
            'price': i['price'],
            'city': i['location']['city'],
            'address': i['location']['display_address'],
            'phone': i['display_phone']
            
        })
    except:
        restaurants.append({
            'name': i['name'],
            'categories': i['categories'],
            'rating': i['rating'],
            'price': 'Price not provided',
            'address': i['location']['display_address'],
            'city': i['location']['city'],
            'address': i['location']['display_address'],
            'phone': i['display_phone']
        })
```

Then, I combined this list of dictionaries with the relevant data from each restaurant with the categories, creating a new dictionary that classified each restaurant in each category.

Here is piece of the dictionary.

```json
{
  "Deli": {
    "Bakeries": [
      {
        "name": "Dom Bakeries",
        "rating": 4.5,
        "price": "$",
        "city": "Ypsilanti",
        "address": "1305 Washtenaw Rd",
        "phone": "(734) 485-3175"
      },
      {
        "name": "24th Cheesecakerie",
        "rating": 5.0,
        "price": "Price not provided",
        "city": "Ypsilanti",
        "address": "14 N Washington St",
        "phone": "(734) 489-4707"
      },
```

## Building the Tree

In order to build the tree, I created two classes: tree and node. 

```python
class Tree():
    def __init__(self,root):
        self.root = root
        self.children = []
        self.Nodes = []
        self.Tree = {}

    def addNode(self,obj):
        self.children.append(obj)

    def getJsonTree(self):
        for i in range(len(self.children)):
            self.Nodes.append([self.children[i].data, []])
            self.children[i].getAllNodes(self.Nodes[i][1])
        self.Nodes.insert(0, self.root)
        write_json('tree.json', self.Nodes)

    def getChildNode(self):
        i = 1
        for child in self.children:
            print(f"{i} - {child.data}")
            i +=1

class Node():
    def __init__(self, data):
        self.data = data
        self.children = []
        self.index = 0

    def addNode(self,obj):
        self.children.append(obj)

    def printChildNodes(self):
         i = 1
         for child in self.children:
             if self.children:
                print(f"{i} - {child.data}")
                i +=1

    def getChildNodes(self):
         return self.children

    def getAllNodes(self, Tree):
        for z in range(len(self.children)):
            if self.children[z].children:
                Tree.append([self.children[z].data, []])
                self.children[z].getAllNodes(Tree[z][1])
            else:
                Tree.append(self.children[z].data)
```

Then, I used the dictionary I had created and instantiated the objects in the dictionary using a recursive function. Below is the function.

```python
def create_nodes(dicts, tree):
    x = tree
    if isinstance(dicts, list) and 'name' in dicts[0]: # Base case
        for i in dicts:
           tree.addNode(Node(i))
        return tree
    else:
        counter = 0
        for z in dicts.keys():
            tree.addNode(Node(z))
            counter += 1
            x = create_nodes(dicts[z], tree.children[counter-1])
            
        return x
```

With my tree created, I was able to walk through the tree in the main() function. Every time that the user answers a question in the command line, the program drills down to find more nodes of the tree until it gets to a leaf node.

