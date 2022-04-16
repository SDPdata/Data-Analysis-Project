# First Dataquest Guided Project
## Profitable App Profiles for the App Store and Google Play Markets

Our goal for this project is to analyze data to help our developers understand what type of apps are likely to attract more users.

First I wanted to create a function that will allow me to easily open
a CSV file and turn it into a list


```python
from csv import reader
def openandlist(CSVFile):
    open_file = open(CSVFile, encoding='utf8')
    read_file = reader(open_file)
    data_set = list(read_file)
    return data_set
```


```python
apple_data = openandlist('AppleStore.csv') 
google_data = openandlist('googleplaystore.csv')
```

Now we created another function that will help to explore the two datasets


```python
def explore_data(dataset, start, end, rows_and_columns=False):
    dataset_slice = dataset[start:end]    
    for row in dataset_slice:
        print(row)
        print('\n') # adds a new (empty) line after each row

    if rows_and_columns:
        print('Number of rows:', len(dataset))
        print('Number of columns:', len(dataset[0]))
```


```python
explore_data(apple_data, 0, 3, True)
print('\n')
explore_data(google_data, 0, 3, True)
```

    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    
    
    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    ['389801252', 'Instagram', '113954816', 'USD', '0.0', '2161558', '1289', '4.5', '4.0', '10.23', '12+', 'Photo & Video', '37', '0', '29', '1']
    
    
    Number of rows: 7198
    Number of columns: 16
    
    
    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']
    
    
    Number of rows: 10842
    Number of columns: 13


### Data cleaning - deleting rows with no data


```python
for row in apple_data[1:]:
    if len(row) != len(apple_data[0]):
        print(row)
        print("\n")
        print("Index postion is:", apple_data.index(row))
        apple_data.remove(row)
```


```python
for row in google_data[1:]:
    if len(row) != len(google_data[0]):
        print(row)
        print("\n")
        print("Index postion is:", google_data.index(row))
        google_data.remove(row)
```

    ['Life Made WI-Fi Touchscreen Photo Frame', '1.9', '19', '3.0M', '1,000+', 'Free', '0', 'Everyone', '', 'February 11, 2018', '1.0.19', '4.0 and up']
    
    
    Index postion is: 10473


### Data Cleaning - Looking for duplicate rows and values


```python
dv_google = []
uv_google = []
for row in google_data[1:]:
    if row[0] in uv_google:
        dv_google.append(row[0])
    else: 
        uv_google.append(row[0])
print(len(dv_google))
        
dv_apple = []
uv_apple = []
for row in apple_data[1:]:
    if row[0] in uv_apple:
        dv_apple.append(row[0])
    else: 
        uv_apple.append(row[0])
print(len(dv_apple))
```

    1181
    0


We are going to remove the duplicates based on update time. We are going to leave only the most udpate row. This will be done based on the amount of review. For every duplicate row, the one with more reviews will be considered as the most recent and up to date. 


```python
reviews_max = {}
for row in google_data[1:]:
    name = row[0]
    n_reviews =float(row[3])
    if name in reviews_max and n_reviews > reviews_max[name]:
        reviews_max[name] = n_reviews
    if name not in reviews_max:
        reviews_max[name] = n_reviews
print(len(reviews_max))
```

    9659



```python
google_clean = []
already_added = []
for row in google_data[1:]:
    name = row[0]
    n_reviews = float(row[3])
    if n_reviews == reviews_max[name] and name not in already_added:
        google_clean.append(row)
        already_added.append(name)
print(len(google_clean))
```

    9659



```python
def is_only_english(string):
    for i in string:
        if ord(i) > 127:
            return False
        
    return True
print(is_only_english('Instagram'))
print(is_only_english('爱奇艺PPS -《欢乐颂2》电视剧热播'))
print(is_only_english('Docs To Go™ Free Office Suite'))
print(is_only_english('Instachat 😜'))
```

    True
    False
    False
    False



```python
def is_english(string):
    non_ascii = 0
    
    for character in string:
        if ord(character) > 127:
            non_ascii += 1
    
    if non_ascii > 3:
        return False
    else:
        return True

print(is_english('Docs To Go™ Free Office Suite'))
print(is_english('Instachat 😜'))
```

    True
    True



```python
google_new = []
for app in google_clean:
    if is_english(app[0]):
        google_new.append(app)
print(len(google_new))

apple_new = []
for app in apple_data:
    if is_english(app[0]):
        apple_new.append(app)
print(len(apple_new))
```

    9614
    7198


### We have removed any non-english appes from the data set
viva la Anglofonía!

### Data Cleaning next part - keeping only the free apps 


```python
google_final = []
apple_final = []

for app in google_new:
    price = app[7]
    if price == '0':
        google_final.append(app)
        
for app in apple_new:
    price = app[4]
    if price == '0.0':
        apple_final.append(app)
        
print(len(google_final))
print(len(apple_final))
```

    8864
    4056


our end goal is to add the app on both Google Play and the App Store, we need to find app profiles that are successful in both markets.
to help up identify the right genre we are going now to build a frequency table for both data sets.


```python
def freq_table(dataset, index):
    table = {}
    total = 0
    
    for row in dataset:
        total += 1
        value = row[index]
        if value in table:
            table[value] += 1
        else:
            table[value] = 1
    
    table_percentages = {}
    for key in table:
        percentage = (table[key] / total) * 100
        table_percentages[key] = percentage 
    
    return table_percentages
    

def display_table(dataset, index):
    table = freq_table(dataset, index)
    table_display = []
    for key in table:
        key_val_as_tuple = (table[key], key)
        table_display.append(key_val_as_tuple)

    table_sorted = sorted(table_display, reverse = True)
    for entry in table_sorted:
        print(entry[1], ':', entry[0])
        
freq_apple = display_table(apple_final, 11)

```

    Games : 55.64595660749507
    Entertainment : 8.234714003944774
    Photo & Video : 4.117357001972387
    Social Networking : 3.5256410256410255
    Education : 3.2544378698224854
    Shopping : 2.983234714003945
    Utilities : 2.687376725838264
    Lifestyle : 2.3175542406311638
    Finance : 2.0710059171597637
    Sports : 1.947731755424063
    Health & Fitness : 1.8737672583826428
    Music : 1.6518737672583828
    Book : 1.6272189349112427
    Productivity : 1.5285996055226825
    News : 1.4299802761341223
    Travel : 1.3806706114398422
    Food & Drink : 1.0601577909270217
    Weather : 0.7642998027613412
    Reference : 0.4930966469428008
    Navigation : 0.4930966469428008
    Business : 0.4930966469428008
    Catalogs : 0.22189349112426035
    Medical : 0.19723865877712032



```python
freq_google = display_table(google_final, 9)
```

    Tools : 8.449909747292418
    Entertainment : 6.069494584837545
    Education : 5.347472924187725
    Business : 4.591606498194946
    Productivity : 3.892148014440433
    Lifestyle : 3.892148014440433
    Finance : 3.7003610108303246
    Medical : 3.531137184115524
    Sports : 3.463447653429603
    Personalization : 3.3167870036101084
    Communication : 3.2378158844765346
    Action : 3.1024368231046933
    Health & Fitness : 3.0798736462093865
    Photography : 2.944494584837545
    News & Magazines : 2.7978339350180503
    Social : 2.6624548736462095
    Travel & Local : 2.3240072202166067
    Shopping : 2.2450361010830324
    Books & Reference : 2.1435018050541514
    Simulation : 2.0419675090252705
    Dating : 1.861462093862816
    Arcade : 1.8501805054151623
    Video Players & Editors : 1.7712093862815883
    Casual : 1.7599277978339352
    Maps & Navigation : 1.3989169675090252
    Food & Drink : 1.2409747292418771
    Puzzle : 1.128158844765343
    Racing : 0.9927797833935018
    Role Playing : 0.9363718411552346
    Libraries & Demo : 0.9363718411552346
    Auto & Vehicles : 0.9250902527075812
    Strategy : 0.9138086642599278
    House & Home : 0.8235559566787004
    Weather : 0.8009927797833934
    Events : 0.7107400722021661
    Adventure : 0.6768953068592057
    Comics : 0.6092057761732852
    Beauty : 0.5979241877256317
    Art & Design : 0.5979241877256317
    Parenting : 0.4963898916967509
    Card : 0.45126353790613716
    Casino : 0.42870036101083037
    Trivia : 0.41741877256317694
    Educational;Education : 0.39485559566787
    Board : 0.3835740072202166
    Educational : 0.3722924187725632
    Education;Education : 0.33844765342960287
    Word : 0.2594765342960289
    Casual;Pretend Play : 0.236913357400722
    Music : 0.2030685920577617
    Racing;Action & Adventure : 0.16922382671480143
    Puzzle;Brain Games : 0.16922382671480143
    Entertainment;Music & Video : 0.16922382671480143
    Casual;Brain Games : 0.13537906137184114
    Casual;Action & Adventure : 0.13537906137184114
    Arcade;Action & Adventure : 0.12409747292418773
    Action;Action & Adventure : 0.10153429602888085
    Educational;Pretend Play : 0.09025270758122744
    Simulation;Action & Adventure : 0.078971119133574
    Parenting;Education : 0.078971119133574
    Entertainment;Brain Games : 0.078971119133574
    Board;Brain Games : 0.078971119133574
    Parenting;Music & Video : 0.06768953068592057
    Educational;Brain Games : 0.06768953068592057
    Casual;Creativity : 0.06768953068592057
    Art & Design;Creativity : 0.06768953068592057
    Education;Pretend Play : 0.056407942238267145
    Role Playing;Pretend Play : 0.04512635379061372
    Education;Creativity : 0.04512635379061372
    Role Playing;Action & Adventure : 0.033844765342960284
    Puzzle;Action & Adventure : 0.033844765342960284
    Entertainment;Creativity : 0.033844765342960284
    Entertainment;Action & Adventure : 0.033844765342960284
    Educational;Creativity : 0.033844765342960284
    Educational;Action & Adventure : 0.033844765342960284
    Education;Music & Video : 0.033844765342960284
    Education;Brain Games : 0.033844765342960284
    Education;Action & Adventure : 0.033844765342960284
    Adventure;Action & Adventure : 0.033844765342960284
    Video Players & Editors;Music & Video : 0.02256317689530686
    Sports;Action & Adventure : 0.02256317689530686
    Simulation;Pretend Play : 0.02256317689530686
    Puzzle;Creativity : 0.02256317689530686
    Music;Music & Video : 0.02256317689530686
    Entertainment;Pretend Play : 0.02256317689530686
    Casual;Education : 0.02256317689530686
    Board;Action & Adventure : 0.02256317689530686
    Video Players & Editors;Creativity : 0.01128158844765343
    Trivia;Education : 0.01128158844765343
    Travel & Local;Action & Adventure : 0.01128158844765343
    Tools;Education : 0.01128158844765343
    Strategy;Education : 0.01128158844765343
    Strategy;Creativity : 0.01128158844765343
    Strategy;Action & Adventure : 0.01128158844765343
    Simulation;Education : 0.01128158844765343
    Role Playing;Brain Games : 0.01128158844765343
    Racing;Pretend Play : 0.01128158844765343
    Puzzle;Education : 0.01128158844765343
    Parenting;Brain Games : 0.01128158844765343
    Music & Audio;Music & Video : 0.01128158844765343
    Lifestyle;Pretend Play : 0.01128158844765343
    Lifestyle;Education : 0.01128158844765343
    Health & Fitness;Education : 0.01128158844765343
    Health & Fitness;Action & Adventure : 0.01128158844765343
    Entertainment;Education : 0.01128158844765343
    Communication;Creativity : 0.01128158844765343
    Comics;Creativity : 0.01128158844765343
    Casual;Music & Video : 0.01128158844765343
    Card;Action & Adventure : 0.01128158844765343
    Books & Reference;Education : 0.01128158844765343
    Art & Design;Pretend Play : 0.01128158844765343
    Art & Design;Action & Adventure : 0.01128158844765343
    Arcade;Pretend Play : 0.01128158844765343
    Adventure;Education : 0.01128158844765343



```python

```
