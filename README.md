# EX-03 Implementing Recommendation Systems
### Aim:
To Implement Recommendation Systems using the suitable data sets.
### Algorithm
STEP 1: Load the necessary Datasets and include the python libraries.<br>
STEP 2: Use Fuzzy library for handling text data and Perform Data Preprocessing steps.<br>
STEP 3: Standardize column names for merging and Apply fuzzy matching to find similar text data between datasets.<br>
STEP 4: Perform Data transformation between datasets and Define a recommendation score using the features of the datasets.<br>
STEP 5: Sort the data by recommendation score and Export the results to a CSV file.
### Program and Output:
###### Importing necessary libraries
```Python
import pandas as pd 
import numpy as np
from fuzzywuzzy import process
from sklearn.preprocessing import MinMaxScaler
```
###### Reading the datasets
<table>
<tr>
<td width=50%>
  
```Python
da=pd.read_csv('emobile.csv')          
da.head()
```
![image](https://github.com/user-attachments/assets/b01c0a08-a1ce-4b95-bdc2-9a1e55ac2754)
</td> 
<td>
  
```Python
db=pd.read_csv('maxmobile.csv')          
db.head()
```
![image](https://github.com/user-attachments/assets/2f5465ef-7e47-4ae9-9a3a-70cd745edc2c)
</td>
</tr> 
</table>

###### Data Cleaning and Column Renaming
```Python
da.drop_duplicates(inplace=True)
db.drop_duplicates(inplace=True)
da.columns=['Product_ID','Product_Name','Rating','Review_Count']
db.columns=['Product_ID','Product_Name','Rating','Review_Count']
```
###### Fuzzy Matching Function Definition
```Python
def matching(name,choice,l=1):
    results=process.extract(name,choice,limit=l)
    return results[0][0] if results else None
```
###### Applying Fuzzy Matching to Identify Corresponding Products
```Python
db['Matched_Product'] = db['Product_Name'].apply(lambda x: matching(x, da['Product_Name'].tolist()))
db.head()
```
![image](https://github.com/user-attachments/assets/77492da3-93cc-4140-8f6f-4c9cf25d1d18)



###### Merging Datasets Based on Fuzzy Matches
```Python
merged=pd.merge(da,db,left_on='Product_Name',right_on='Matched_Product',
                how='inner',suffixes=('_ds1','_ds2'))
merged.head()
```
![image](https://github.com/user-attachments/assets/7880a3ba-b653-458c-b97b-344d85a96793)



###### Calculating Combined Ratings and Review Counts
```Python
merged.columns
x=merged['Review_Count_ds1']+merged['Review_Count_ds2']
merged['Combined_Rating']=(merged['Rating_ds1']*merged['Review_Count_ds1']+
                           merged['Rating_ds2']*merged['Review_Count_ds2'])/x
merged.head()
```
![image](https://github.com/user-attachments/assets/56379282-1cbc-44ef-815e-ae293c815923)

###### Calculating Recommendation Score
```Python
merged['Recommendation_Score']=0.7*merged['Combined_Rating']+0.3*merged['Rating_ds1']
merged.head()
```
![image](https://github.com/user-attachments/assets/a7f7d7b0-e00d-4f01-a432-1d4aab74d043)

###### Identifying Top Products by Recommendation Score
```Python
best=merged.sort_values (by='Recommendation_Score', ascending=False)
print(best[['Matched_Product', 'Combined_Rating', 'Recommendation_Score']].head(5))
```
![image](https://github.com/user-attachments/assets/dac8c335-a560-41a0-9ffc-e89eb533f4b1)

###### Saving the Best Recommendations
```Python
best[['Matched_Product','Combined_Rating',
      'Recommendation_Score']].to_csv('BestRecommended.csv',index=False)
new=pd.read_csv('BestRecommended.csv')
new.head()
```
![image](https://github.com/user-attachments/assets/1bae1f7a-b9e7-4f07-914b-85f8729e92f6)

### Result:
Thus, the Implementation of the Recommendation System for the given dataset is completed successfully.<br>
