# Seminar
**Sentiment analysis of Elon Musk's tweets and its impact on Tesla's stock price Using VADER**

The Elon Musk tweets dataset was extracted from seekingalpha.com and the Tesla stock price dataset was extracted from Yahoo Finance.

The implementation code is available here: https://colab.research.google.com/drive/1BPfe5HtIa99GaDkxxYBZUiLrZ-ZRjuQM?usp=sharing

**Algorithm implementation steps:**
After import the data,

1. Clean data:

“Title” is already clean, But "Date" is not. 
Date data is in text (i.e. string) format. Changing it to a datetime format.
Before I can modify the date using code, need to briefly look through the dataset to have a sense of the format of the data.
Eventually:
```python
df1['Date'][0] = 'Dec. 9'
df1.head() # display the first 5 rows

import re # import Regular expression library

print(df1['Date'][1]) # display original

match = re.search(r'\w{3}\.\s\d{1,2}', df1['Date'][1])

modifiedDate = match[0] + ", 2019"

print(modifiedDate) # display modified

import re # import Regular expression library

print(df1['Date'][1000]) # display original

match = re.search(r'\w{3}\.\s\d{1,2}\,\s\d{4}', df1['Date'][1000])

print(match[0]) # display modified

pd.set_option('display.max_rows', df1.shape[0]+1)
df1

import re # import Regular expression library

print(df1['Date'][200]) # display original (without year)
match = re.search(r'\w{3}\s\d{1,2}', df1['Date'][200])
modifiedDate = match[0] + ", 2019"
print(modifiedDate) # display modified

print(df1['Date'][850]) # display original (with year)
match = re.search(r'\w{3}\s\d{1,2}\,\s\d{4}', df1['Date'][850])
print(match[0]) # display modified

from datetime import datetime

newDate1 = datetime.strptime('Dec. 6, 2019', '%b. %d, %Y').date()
print(newDate1)

newDate2 = datetime.strptime('May 17, 2018', '%b %d, %Y').date()
print(newDate2)

from datetime import datetime
import re

newDateList = [] # create a list to store the cleaned dates

for dateOfArticles in df1['Date']: # loop every row in the "Date" column
    match = re.search(r'\w{3}\.\s\d{1,2}\,\s\d{4}|May\s\d{1,2}\,\s\d{4}|\w{3}\.\s\d{1,2}|May\s\d{1,2}', 
                      dateOfArticles)

    if re.search(r'\w{3}\.\s\d{1,2}\,\s\d{4}|\w{3}\s\d{1,2}\,\s\d{4}',match[0]):
        fulldate = match[0] # don't append year to string
    else:
        fulldate = match[0] + ", 2019" # append year to string
    
    for fmt in ('%b. %d, %Y', '%b %d, %Y'):
        try:
            newDate = datetime.strptime(fulldate, fmt).date()
            break # if format is correct, don't test any other formats
        except ValueError:
            pass
        
    newDateList.append(newDate) # add new date to the list

if(len(newDateList) != df1.shape[0]):
    print("Error: Rows don't match")
else:
    df1['New Date'] = newDateList # add the list to our original dataframe

df1



for dateOfArticles in df1['Date']: # loop every row in the "Date" column
    match = re.search(r'\w{3}\.\s\d{1,2}\,\s\d{4}|May\s\d{1,2}\,\s\d{4}|\w{3}\.\s\d{1,2}|May\s\d{1,2}', dateOfArticles)
    
    
    
    if re.search(r'\w{3}\.\s\d{1,2}\,\s\d{4}|\w{3}\s\d{1,2}\,\s\d{4}', match[0]):
        fulldate = match[0] # don't append year to string
else:
        fulldate = match[0] + ", 2019" # append year to string
        
        for fmt in ('%b. %d, %Y', '%b %d, %Y'):
        try:
            newDate = datetime.strptime(fulldate, fmt).date()
            break # if format is correct, don't test any other formats
        except ValueError:
            pass
        
newDateList.append(newDate) # add new date to the list


if(len(newDateList) != df1.shape[0]):
    print("Error: Rows don't match")
else:
    df1['New Date'] = newDateList # add the list to our original dataframe
    
    
```


