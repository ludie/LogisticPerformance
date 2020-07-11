# This Python 3 environment comes with many helpful analytics libraries installed
# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python
# For example, here's several helpful packages to load

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
import datetime
# Input data files are available in the read-only "../input/" directory
# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory

import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))

# You can write up to 5GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using "Save & Run All" 
# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session


raw = pd.read_csv(r'../input/open-shopee-code-league-logistic/delivery_orders_march.csv')
print(raw.head(10))


new_raw = raw[['orderid','buyeraddress','selleraddress']].copy()
print(new_raw.head())


new_raw.loc[new_raw['buyeraddress'].str.contains('Metro Manila', case=False), 'buyeraddress'] = 'Metro Manila'
new_raw.loc[new_raw['selleraddress'].str.contains('Metro Manila', case=False), 'selleraddress'] = 'Metro Manila'
new_raw.loc[new_raw['buyeraddress'].str.contains('Luzon', case=False), 'buyeraddress'] = 'Luzon'
new_raw.loc[new_raw['selleraddress'].str.contains('Luzon', case=False), 'selleraddress'] = 'Luzon'
new_raw.loc[new_raw['buyeraddress'].str.contains('Visayas', case=False), 'buyeraddress'] = 'Visayas'
new_raw.loc[new_raw['selleraddress'].str.contains('Visayas', case=False), 'selleraddress'] = 'Visayas'
new_raw.loc[new_raw['buyeraddress'].str.contains('Mindanao', case=False), 'buyeraddress'] = 'Mindanao'
new_raw.loc[new_raw['selleraddress'].str.contains('Mindanao', case=False), 'selleraddress'] = 'Mindanao'
print(len(new_raw))
print(new_raw.head())


conditions = [
    (new_raw['buyeraddress'] == 'Metro Manila') & (new_raw['selleraddress'] == 'Metro Manila'),
    (new_raw['buyeraddress'] == 'Metro Manila') & (new_raw['selleraddress'] == 'Luzon'),
    (new_raw['buyeraddress'] == 'Luzon') & (new_raw['selleraddress'] == 'Metro Manila'),
    (new_raw['buyeraddress'] == 'Luzon') & (new_raw['selleraddress'] == 'Luzon')
]
choices = [3, 5, 5, 5]
new_raw['needed_day'] = np.select(conditions, choices, default=7)
print(new_raw.head(20))


date1 = raw[['pick','1st_deliver_attempt','2nd_deliver_attempt']].copy()
print(date1.head(10))


date1['pick'] =  pd.to_datetime(date1['pick'], unit = 's').dt.date
date1['1st_deliver_attempt'] =  pd.to_datetime(date1['1st_deliver_attempt'], unit = 's').dt.date
date1.loc[date1['2nd_deliver_attempt'] != 'NaN', '2nd_deliver_attempt'] = pd.to_datetime(date1['2nd_deliver_attempt'], unit = 's').dt.date
print(date1.head(10))


final_raw1 = pd.concat([new_raw, date1], axis = 1)
print(final_raw1.head())


final_raw2 = final_raw1[['orderid','pick','1st_deliver_attempt','2nd_deliver_attempt','needed_day']].copy()
print(final_raw2.head(10))


holi = []
holi.append(datetime.date(2020,3,25))
holi.append(datetime.date(2020,3,30))
holi.append(datetime.date(2020,3,31))


is_late = []
holiday = ['2020-03-25']
for i, j, k, l in zip(final_raw2['pick'].to_numpy(),final_raw2['1st_deliver_attempt'].to_numpy(),final_raw2['2nd_deliver_attempt'].to_numpy(), final_raw2['needed_day'].to_numpy()):
    if np.busday_count(i, j, weekmask ='1111110', holidays =holi) > l:
        is_late.append(1)
    elif pd.notna(k) == True:
        if np.busday_count(j, k, weekmask ='1111110', holidays =holi) > 3:
            is_late.append(1)
        else:
            is_late.append(0)
    else:
        is_late.append(0)
        

final_raw2['is_late'] = is_late
ans = final_raw2[['orderid','is_late']]
print(ans.shape)
ans.to_csv('sub3.csv', index = False)
