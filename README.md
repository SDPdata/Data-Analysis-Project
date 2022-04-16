# First Dataquest Guided Project
## Profitable App Profiles for the App Store and Google Play Markets

Our goal for this project is to analyze data to help our developers understand what type of apps are likely to attract more users.


```python
# first I wanted to create a function that will allow me to easily open
# a CSV file and turn it into a list
from csv import reader
def openandlist(CSVFile):
    open_file = open(CSVFile)
    read_file = reader(open_file)
    data_set = list(read_file)
    return data_set
```


```python
apple_data = ('AppleStore.csv') 
google_data = openandlist('googleplaystore.csv')
```


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
explore_data(google_data, 1, 4, True)
explore_data(apple_data, 1, 4, True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    Number of rows: 10842
    Number of columns: 13
    p
    
    
    p
    
    
    l
    
    
    Number of rows: 14
    Number of columns: 1



```python
