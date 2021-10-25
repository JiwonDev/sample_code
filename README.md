# sample_code
```python
#%% 문제 1 ###
import pandas as pd
import numpy as np
from matplotlib import pyplot as plt
from matplotlib import rc
import seaborn as sns

excel_url = '다운받은/파일경로/입력해주세요/전공_파이썬데이터분석과제_이름_ID.xlsx'
df = pd.read_excel(excel_url, sheet_name=2)
df['datetime'] = df['확진일'].apply(lambda x: pd.to_datetime(x, format='%Y-%m-%d', errors='raise'))
df.set_index(df['datetime'], inplace=True)

df['count'] = 1
temp = df.groupby(["지역", pd.Grouper(key="datetime", freq="w")])
sorted_series = temp["count"].sum()
sorted_df = pd.DataFrame(sorted_series, columns=["count"])

rc('font', family="Malgun Gothic") # window - 맑은고딕 설정
# rc('font', family='AppleGothic') # mac - 애플고딕 설정
rc('axes', unicode_minus=False)
table = sorted_df.pivot_table(values='count', index='datetime', columns='지역', fill_value=0)

# x축 생성
x_data = pd.date_range(min(df['datetime']), max(df['datetime']), freq='W')
ax = table.plot(figsize=(10, 10))
ax.set_xticks(x_data)
```
![image](https://user-images.githubusercontent.com/26001202/138667261-d7e226c7-ca36-4ef2-8019-4e7a28cdbca9.png)

참고로 import는 문제1에서 불러왔던 걸 재사용합니다. 
```python
#%% 문제 2-1 ###
df2 = pd.read_excel(excel_url, sheet_name=4)
condition = (df2['접종일'] >= "2021.01.01") & (df2['접종일'] <= "2021.09.31")
df2 = df2[condition]

df3 = pd.read_excel(excel_url, sheet_name=3)
condition = (df3['사용월'] >= 202101) & (df3['사용월'] <= 202109)
df3 = df3[condition]

import calendar

x_data = ["21.01", "21.02", "21.03", "21.04", "21.05", "21.06", "21.07", "21.08", "21.09"]
y_data = []
for i in range(1, 10):
    day = calendar.monthrange(2021, i)[1]
    condition = (df2['접종일'] == "2021." + "0" + str(i) + "." + str(day))
    if len(df2[condition]) == 0:
        y_data.append(0)
    else:
        y_data.append(df2[condition]['2차접종률(%)'])
x_data2 = [202101, 202102, 202103, 202104, 202105, 202106, 202107, 202108, 202109]

plt.ylim([0, 100])
plt.bar(x_data2, y_data)
```
```python
#%% 문제 2-2 ###
df_temp = df3.drop(['작업일자', '사용월'], axis=1)
df3['총이용인원'] = df_temp.sum(axis=1)

sorted_series3 = df3.groupby(['호선명', '사용월'])['총이용인원'].sum()
sorted_df3 = pd.DataFrame(sorted_series3, columns=['총이용인원'])
pd.DataFrame(sorted_df3.loc['1호선', '총이용인원'])

table = sorted_df3.pivot_table(values='총이용인원', index='사용월', columns='호선명', fill_value=0)

# 꺾은선 그래프 x축 생성
x_data2 = ["", "20210101", "20210102", "20210103", "20210104", "20210105", "20210106", "20210107", "20210108",
           "20210109"]

ax = table.plot(figsize=(10, 10))
ax.set_xticklabels(x_data2)

plt.rcParams['figure.figsize'] = (36, 24)
fig, ax1 = plt.subplots()

ax1.plot(table)
x_data2 = [202101, 202102, 202103, 202104, 202105, 202106, 202107, 202108, 202109]

ax1.set_xticklabels(x_data2)
ax1.tick_params(axis='both', direction='in')

ax2 = ax1.twinx()
ax2.bar(x_data2, y_data)
ax2.tick_params(axis='y', direction='in')
```
![image](https://user-images.githubusercontent.com/26001202/138667393-67348359-bb1c-4a06-aa6e-8680435ecfbf.png)
