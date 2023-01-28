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
**The next part is churn out a score.**


**2. Using VADER to analyse Tesla Headlines**
2.1. **Install the NLTK library:**
NLTK stands for Natural Language ToolKit. It is a library that helps manage and analyse languages.
Need this as the VADER analyser is part of the NLTK library.
Need to download the VADER Lexicon.

```python
import nltk
nltk.download('vader_lexicon')
```
**2.2 Run the sentiment analysis**

```python
from nltk.sentiment.vader import SentimentIntensityAnalyzer as SIA

results = []

for headline in df1['Title']:
    pol_score = SIA().polarity_scores(headline) # run analysis
    pol_score['headline'] = headline # add headlines for viewing
    results.append(pol_score)

results
```
Use a loop to pass every headline into our analyser. A sentiment score is assigned to each headline.

```python
df1['Score'] = pd.DataFrame(results)['compound']
```

**3.Correlate lagged score index against prices**
compare the relationship between the TSLA stock returns and our sentiment score. If there is a significant relationship, then sentiment scores might have some predictive value.
the next steps:
- Aggregate daily sentiment scores
- Import TSLA prices and calculate returns
- Check relationship between lagged score against returns (daily)


```python
df2 = df1.groupby(['New Date']).sum() # creates a daily score by summing the scores of the individual articles in each day


dfEodPrice['Date'] = dfEodPrice['Date'].astype('datetime64[ns]') 
type(dfEodPrice['Date'][1])


dfEodPrice2 = dfEodPrice.drop(['Open', 'High','Low','Close','Volume'], axis=1) # drop unwanted rows
dfEodPrice2.set_index('Date', inplace=True) # set Date coloumn as index
dfEodPrice2


dfEodPrice2['Returns'] = dfEodPrice2['Adj Close']/dfEodPrice2['Adj Close'].shift(1) - 1 # calculate daily returns
dfEodPrice2


df2['Score(1)'] = df2.shift(1)
df2

dfEodPrice3 = pd.merge(dfEodPrice2[['Returns']], df2[['Score(1)']], left_index=True, right_index=True, how='left')
dfEodPrice3

dfReturnsScore.fillna(0, inplace=True) 
# replace NaN with 0 permanently

dfReturnsScore2 = dfReturnsScore[(dfReturnsScore['Score(1)'] > 0.5) | (dfReturnsScore['Score(1)'] < -0.5)]

```
![image](https://user-images.githubusercontent.com/30371881/215287120-fe7b6b22-539d-47ab-9572-0e2ed8feecd5.png)
![image](https://user-images.githubusercontent.com/30371881/215287129-2056088d-dadf-4cf9-a453-a26b52c45449.png)





correlation coefficient is 0.044.(0)

This means headlines alone do not have any predictive value for stock returns.


- The accuracy of the VADER sentiment analyser is nowhere near perfect.


As mentioned earlier, already know that these sentiment output have huge variance and we rely on large numbers to squeeze out a slightly useful mean output value.

if want to improve on this, the solution will be to build your own sentiment analyser by training it on the type of data you are testing on.

