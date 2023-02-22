# 基于条形图的世界杯数据可视化
## 1 前言
要说时下最热门的活动，非2022卡塔尔世界杯莫属了。正好借此次机会，回顾一下过往的世界杯，就其历史数据做一些数据的呈现和分析工作。根据对世界杯历史数据的简单观察，发现学习过的10种图片中的条形图能更直观地展示数据。同时，本次论文的选题和数据是来自于“阿里云天池平台”的可视化大赛“世界杯数据可视化分析”，该论文也将作为参赛作品同步到“[天池平台](https://tianchi.aliyun.com/notebook/443381)”上。
## 2 导入模块和数据
第一步是导入所需要的包。为此，我们需要：
- pandas和它的DataFrame类来做数据读取和处理；
- numpy在图表中进行一些基于数据的科学运算；
- plotnine来获取数据并执行可视化操作。

除此之外，还使用了 plotnine 的 figure_size 函数来调整图形的大小，使它们显示得更加美观、整洁。


```python
import numpy as np
import pandas as pd
import plotnine
plotnine.options.figure_size=(12, 4.2)
from plotnine import *
from plotnine import data
```

然后导入世界杯历史比赛的信息，文件为“WorldCupMatches.csv”。


```python
# 导入数据并查看
# 注意要加encoding='gbk'，直接读取会显示
# UnicodeDecodeError: 'utf-8' codec can't decode bytes in position 18508-18509: invalid continuation byte
# 这是由于数据中有一些非英语国家的语言，utf-8无法处理的编码导致的
matches = pd.read_csv('WorldCupMatches.csv',encoding='gbk')
matches
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
      <th>Year</th>
      <th>Datetime</th>
      <th>Stage</th>
      <th>Stadium</th>
      <th>City</th>
      <th>Home Team Name</th>
      <th>Home Team Goals</th>
      <th>Away Team Goals</th>
      <th>Away Team Name</th>
      <th>Attendance</th>
      <th>Half-time Home Goals</th>
      <th>Half-time Away Goals</th>
      <th>Referee</th>
      <th>Assistant 1</th>
      <th>Assistant 2</th>
      <th>RoundID</th>
      <th>MatchID</th>
      <th>Home Team Initials</th>
      <th>Away Team Initials</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1930</td>
      <td>13 Jul 1930 - 15:00</td>
      <td>Group 1</td>
      <td>Pocitos</td>
      <td>Montevideo</td>
      <td>France</td>
      <td>4</td>
      <td>1</td>
      <td>Mexico</td>
      <td>4444.0</td>
      <td>3</td>
      <td>0</td>
      <td>LOMBARDI Domingo (URU)</td>
      <td>CRISTOPHE Henry (BEL)</td>
      <td>REGO Gilberto (BRA)</td>
      <td>201</td>
      <td>1096</td>
      <td>FRA</td>
      <td>MEX</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1930</td>
      <td>13 Jul 1930 - 15:00</td>
      <td>Group 4</td>
      <td>Parque Central</td>
      <td>Montevideo</td>
      <td>USA</td>
      <td>3</td>
      <td>0</td>
      <td>Belgium</td>
      <td>18346.0</td>
      <td>2</td>
      <td>0</td>
      <td>MACIAS Jose (ARG)</td>
      <td>MATEUCCI Francisco (URU)</td>
      <td>WARNKEN Alberto (CHI)</td>
      <td>201</td>
      <td>1090</td>
      <td>USA</td>
      <td>BEL</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1930</td>
      <td>14 Jul 1930 - 12:45</td>
      <td>Group 2</td>
      <td>Parque Central</td>
      <td>Montevideo</td>
      <td>Yugoslavia</td>
      <td>2</td>
      <td>1</td>
      <td>Brazil</td>
      <td>24059.0</td>
      <td>2</td>
      <td>0</td>
      <td>TEJADA Anibal (URU)</td>
      <td>VALLARINO Ricardo (URU)</td>
      <td>BALWAY Thomas (FRA)</td>
      <td>201</td>
      <td>1093</td>
      <td>YUG</td>
      <td>BRA</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1930</td>
      <td>14 Jul 1930 - 14:50</td>
      <td>Group 3</td>
      <td>Pocitos</td>
      <td>Montevideo</td>
      <td>Romania</td>
      <td>3</td>
      <td>1</td>
      <td>Peru</td>
      <td>2549.0</td>
      <td>1</td>
      <td>0</td>
      <td>WARNKEN Alberto (CHI)</td>
      <td>LANGENUS Jean (BEL)</td>
      <td>MATEUCCI Francisco (URU)</td>
      <td>201</td>
      <td>1098</td>
      <td>ROU</td>
      <td>PER</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1930</td>
      <td>15 Jul 1930 - 16:00</td>
      <td>Group 1</td>
      <td>Parque Central</td>
      <td>Montevideo</td>
      <td>Argentina</td>
      <td>1</td>
      <td>0</td>
      <td>France</td>
      <td>23409.0</td>
      <td>0</td>
      <td>0</td>
      <td>REGO Gilberto (BRA)</td>
      <td>SAUCEDO Ulises (BOL)</td>
      <td>RADULESCU Constantin (ROU)</td>
      <td>201</td>
      <td>1085</td>
      <td>ARG</td>
      <td>FRA</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>847</th>
      <td>2014</td>
      <td>05 Jul 2014 - 17:00</td>
      <td>Quarter-finals</td>
      <td>Arena Fonte Nova</td>
      <td>Salvador</td>
      <td>Netherlands</td>
      <td>0</td>
      <td>0</td>
      <td>Costa Rica</td>
      <td>51179.0</td>
      <td>0</td>
      <td>0</td>
      <td>Ravshan IRMATOV (UZB)</td>
      <td>RASULOV Abduxamidullo (UZB)</td>
      <td>KOCHKAROV Bakhadyr (KGZ)</td>
      <td>255953</td>
      <td>300186488</td>
      <td>NED</td>
      <td>CRC</td>
    </tr>
    <tr>
      <th>848</th>
      <td>2014</td>
      <td>08 Jul 2014 - 17:00</td>
      <td>Semi-finals</td>
      <td>Estadio Mineirao</td>
      <td>Belo Horizonte</td>
      <td>Brazil</td>
      <td>1</td>
      <td>7</td>
      <td>Germany</td>
      <td>58141.0</td>
      <td>0</td>
      <td>5</td>
      <td>RODRIGUEZ Marco (MEX)</td>
      <td>TORRENTERA Marvin (MEX)</td>
      <td>QUINTERO Marcos (MEX)</td>
      <td>255955</td>
      <td>300186474</td>
      <td>BRA</td>
      <td>GER</td>
    </tr>
    <tr>
      <th>849</th>
      <td>2014</td>
      <td>09 Jul 2014 - 17:00</td>
      <td>Semi-finals</td>
      <td>Arena de Sao Paulo</td>
      <td>Sao Paulo</td>
      <td>Netherlands</td>
      <td>0</td>
      <td>0</td>
      <td>Argentina</td>
      <td>63267.0</td>
      <td>0</td>
      <td>0</td>
      <td>C眉neyt 脟AKIR (TUR)</td>
      <td>DURAN Bahattin (TUR)</td>
      <td>ONGUN Tarik (TUR)</td>
      <td>255955</td>
      <td>300186490</td>
      <td>NED</td>
      <td>ARG</td>
    </tr>
    <tr>
      <th>850</th>
      <td>2014</td>
      <td>12 Jul 2014 - 17:00</td>
      <td>Play-off for third place</td>
      <td>Estadio Nacional</td>
      <td>Brasilia</td>
      <td>Brazil</td>
      <td>0</td>
      <td>3</td>
      <td>Netherlands</td>
      <td>68034.0</td>
      <td>0</td>
      <td>2</td>
      <td>HAIMOUDI Djamel (ALG)</td>
      <td>ACHIK Redouane (MAR)</td>
      <td>ETCHIALI Abdelhak (ALG)</td>
      <td>255957</td>
      <td>300186502</td>
      <td>BRA</td>
      <td>NED</td>
    </tr>
    <tr>
      <th>851</th>
      <td>2014</td>
      <td>13 Jul 2014 - 16:00</td>
      <td>Final</td>
      <td>Estadio do Maracana</td>
      <td>Rio De Janeiro</td>
      <td>Germany</td>
      <td>1</td>
      <td>0</td>
      <td>Argentina</td>
      <td>74738.0</td>
      <td>0</td>
      <td>0</td>
      <td>Nicola RIZZOLI (ITA)</td>
      <td>Renato FAVERANI (ITA)</td>
      <td>Andrea STEFANI (ITA)</td>
      <td>255959</td>
      <td>300186501</td>
      <td>GER</td>
      <td>ARG</td>
    </tr>
  </tbody>
</table>
<p>852 rows × 19 columns</p>
</div>



再查看一下数据描述，用以选取合适的数据x轴和y轴来进行可视化


```python
matches.describe()
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
      <th>Year</th>
      <th>Home Team Goals</th>
      <th>Away Team Goals</th>
      <th>Attendance</th>
      <th>Half-time Home Goals</th>
      <th>Half-time Away Goals</th>
      <th>RoundID</th>
      <th>MatchID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>852.000000</td>
      <td>852.000000</td>
      <td>852.000000</td>
      <td>850.000000</td>
      <td>852.000000</td>
      <td>852.000000</td>
      <td>8.520000e+02</td>
      <td>8.520000e+02</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1985.089202</td>
      <td>1.811033</td>
      <td>1.022300</td>
      <td>45164.800000</td>
      <td>0.708920</td>
      <td>0.428404</td>
      <td>1.066177e+07</td>
      <td>6.134687e+07</td>
    </tr>
    <tr>
      <th>std</th>
      <td>22.448825</td>
      <td>1.610255</td>
      <td>1.087573</td>
      <td>23485.249247</td>
      <td>0.937414</td>
      <td>0.691252</td>
      <td>2.729613e+07</td>
      <td>1.110572e+08</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1930.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2000.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.010000e+02</td>
      <td>2.500000e+01</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1970.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>30000.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.620000e+02</td>
      <td>1.188750e+03</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1990.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>41579.500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.370000e+02</td>
      <td>2.191000e+03</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2002.000000</td>
      <td>3.000000</td>
      <td>2.000000</td>
      <td>61374.500000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>2.497220e+05</td>
      <td>4.395006e+07</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2014.000000</td>
      <td>10.000000</td>
      <td>7.000000</td>
      <td>173850.000000</td>
      <td>6.000000</td>
      <td>5.000000</td>
      <td>9.741060e+07</td>
      <td>3.001865e+08</td>
    </tr>
  </tbody>
</table>
</div>



## 3 具体可视化数据的选取
通过观察表中的数据以及结合比赛实际情况，我发现了评价一届足球比赛的两项重要指标。一个就是当届世界杯的观众人数，这反映了这届比赛的受欢迎程度；另一个则是当届世界杯总的进球数，这反映了这届比赛的激烈程度。这两项指标分别从主观层面和客观层面反映了某届世界杯的精彩程度，是较为全面和有意义的数据分析。

同时，由于数据过多，无法对全部的比赛进行展示，故此次作业仅展示两项指标加权后排名靠前的几届世界杯。

## 4 数据预处理以便可视化
数据预处理的主要涉及到数据清洗，以及针对要分析的内容进行特定字段的转化。具体到该表，主要需要对每届世界杯的数据进行整合，求出当年的观众总数和进球总数，并计算出两项指标的得分，选取前面的比赛进行条形图的绘制。


```python
# 先将空值补为0
matches.fillna(0)
# 使用聚集函数groupby求相同的‘Year’下的‘Attendance’的和
# 并设置as_index=False使‘Year’放弃列索引，让‘Year’和‘Attendance’处于同一列
att_matches=matches.groupby(['Year'],as_index=False)['Attendance'].sum()
# 使用apply函数将每场比赛两队的得分加起来，设置axis=1保证是列操作
matches['Goals']=matches.apply(lambda x: x['Home Team Goals'] + x['Away Team Goals'], axis=1)
score_matches=matches.groupby(['Year'],as_index=False)['Goals'].sum()
```

考虑到ggplot直方图绘制对数据格式有所要求，故需先按对应格式将数据进行转化。简单来说，就是将att_matches和score_matches拼在一起，然后加一列‘Index’表明具体是什么指标，用于fill参数。


```python
# 创建新DataFrame
dic = {"Year": [],
       "y": [],
       "Index": []}
data = pd.DataFrame(dic)
# 做行遍历，将数据填到新的DataFrame中，并用int转换保证年份是整数
for index, row in att_matches.iterrows():
    data.loc[len(data.index)] = [int(row['Year']),
                                 row['Attendance'], 'Attendance']
for index, row in score_matches.iterrows():
    data.loc[len(data.index)] = [int(row['Year']), row['Goals'], 'Goals']
# 查看新数据
data
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
      <th>Year</th>
      <th>y</th>
      <th>Index</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1930</td>
      <td>590549.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1934</td>
      <td>363000.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1938</td>
      <td>375700.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1950</td>
      <td>1045246.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1954</td>
      <td>768607.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1958</td>
      <td>819810.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1962</td>
      <td>893172.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1966</td>
      <td>1563135.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1970</td>
      <td>1603975.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1974</td>
      <td>1865753.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1978</td>
      <td>1545791.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1982</td>
      <td>2109723.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1986</td>
      <td>2394031.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1990</td>
      <td>2516215.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1994</td>
      <td>3587538.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1998</td>
      <td>2785100.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2002</td>
      <td>2705197.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2006</td>
      <td>3359439.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2010</td>
      <td>3178856.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2014</td>
      <td>4319243.0</td>
      <td>Attendance</td>
    </tr>
    <tr>
      <th>20</th>
      <td>1930</td>
      <td>70.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>21</th>
      <td>1934</td>
      <td>70.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1938</td>
      <td>84.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>23</th>
      <td>1950</td>
      <td>88.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1954</td>
      <td>140.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1958</td>
      <td>126.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1962</td>
      <td>89.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1966</td>
      <td>89.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1970</td>
      <td>95.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>29</th>
      <td>1974</td>
      <td>97.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>30</th>
      <td>1978</td>
      <td>102.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>31</th>
      <td>1982</td>
      <td>146.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>32</th>
      <td>1986</td>
      <td>132.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>33</th>
      <td>1990</td>
      <td>115.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>34</th>
      <td>1994</td>
      <td>141.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>35</th>
      <td>1998</td>
      <td>171.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2002</td>
      <td>161.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2006</td>
      <td>147.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2010</td>
      <td>145.0</td>
      <td>Goals</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2014</td>
      <td>206.0</td>
      <td>Goals</td>
    </tr>
  </tbody>
</table>
</div>



我发现数据较多，全部绘制出来可能会不太美观与整洁，故选取其中较为“精彩”的几届比赛。至于评分权重，不妨两个指标五五开。具体的评分是以该届的指标除以该项指标的最大值，求和后取总分大于1的那几届比赛。


```python
# 求指标最值
att_max = att_matches.loc[:, 'Attendance'].max()
score_max = score_matches.loc[:, 'Goals'].max()
# 分类求得分
data['Grade'] = data.apply(lambda x: x['y']/att_max if x['Index']
                           == 'Attendance' else x['y']/score_max, axis=1)

data
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
      <th>Year</th>
      <th>y</th>
      <th>Index</th>
      <th>Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1930</td>
      <td>590549.0</td>
      <td>Attendance</td>
      <td>0.136725</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1934</td>
      <td>363000.0</td>
      <td>Attendance</td>
      <td>0.084043</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1938</td>
      <td>375700.0</td>
      <td>Attendance</td>
      <td>0.086983</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1950</td>
      <td>1045246.0</td>
      <td>Attendance</td>
      <td>0.241997</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1954</td>
      <td>768607.0</td>
      <td>Attendance</td>
      <td>0.177949</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1958</td>
      <td>819810.0</td>
      <td>Attendance</td>
      <td>0.189804</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1962</td>
      <td>893172.0</td>
      <td>Attendance</td>
      <td>0.206789</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1966</td>
      <td>1563135.0</td>
      <td>Attendance</td>
      <td>0.361900</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1970</td>
      <td>1603975.0</td>
      <td>Attendance</td>
      <td>0.371356</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1974</td>
      <td>1865753.0</td>
      <td>Attendance</td>
      <td>0.431963</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1978</td>
      <td>1545791.0</td>
      <td>Attendance</td>
      <td>0.357885</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1982</td>
      <td>2109723.0</td>
      <td>Attendance</td>
      <td>0.488447</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1986</td>
      <td>2394031.0</td>
      <td>Attendance</td>
      <td>0.554271</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1990</td>
      <td>2516215.0</td>
      <td>Attendance</td>
      <td>0.582559</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1994</td>
      <td>3587538.0</td>
      <td>Attendance</td>
      <td>0.830594</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1998</td>
      <td>2785100.0</td>
      <td>Attendance</td>
      <td>0.644812</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2002</td>
      <td>2705197.0</td>
      <td>Attendance</td>
      <td>0.626313</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2006</td>
      <td>3359439.0</td>
      <td>Attendance</td>
      <td>0.777784</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2010</td>
      <td>3178856.0</td>
      <td>Attendance</td>
      <td>0.735975</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2014</td>
      <td>4319243.0</td>
      <td>Attendance</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>20</th>
      <td>1930</td>
      <td>70.0</td>
      <td>Goals</td>
      <td>0.339806</td>
    </tr>
    <tr>
      <th>21</th>
      <td>1934</td>
      <td>70.0</td>
      <td>Goals</td>
      <td>0.339806</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1938</td>
      <td>84.0</td>
      <td>Goals</td>
      <td>0.407767</td>
    </tr>
    <tr>
      <th>23</th>
      <td>1950</td>
      <td>88.0</td>
      <td>Goals</td>
      <td>0.427184</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1954</td>
      <td>140.0</td>
      <td>Goals</td>
      <td>0.679612</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1958</td>
      <td>126.0</td>
      <td>Goals</td>
      <td>0.611650</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1962</td>
      <td>89.0</td>
      <td>Goals</td>
      <td>0.432039</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1966</td>
      <td>89.0</td>
      <td>Goals</td>
      <td>0.432039</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1970</td>
      <td>95.0</td>
      <td>Goals</td>
      <td>0.461165</td>
    </tr>
    <tr>
      <th>29</th>
      <td>1974</td>
      <td>97.0</td>
      <td>Goals</td>
      <td>0.470874</td>
    </tr>
    <tr>
      <th>30</th>
      <td>1978</td>
      <td>102.0</td>
      <td>Goals</td>
      <td>0.495146</td>
    </tr>
    <tr>
      <th>31</th>
      <td>1982</td>
      <td>146.0</td>
      <td>Goals</td>
      <td>0.708738</td>
    </tr>
    <tr>
      <th>32</th>
      <td>1986</td>
      <td>132.0</td>
      <td>Goals</td>
      <td>0.640777</td>
    </tr>
    <tr>
      <th>33</th>
      <td>1990</td>
      <td>115.0</td>
      <td>Goals</td>
      <td>0.558252</td>
    </tr>
    <tr>
      <th>34</th>
      <td>1994</td>
      <td>141.0</td>
      <td>Goals</td>
      <td>0.684466</td>
    </tr>
    <tr>
      <th>35</th>
      <td>1998</td>
      <td>171.0</td>
      <td>Goals</td>
      <td>0.830097</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2002</td>
      <td>161.0</td>
      <td>Goals</td>
      <td>0.781553</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2006</td>
      <td>147.0</td>
      <td>Goals</td>
      <td>0.713592</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2010</td>
      <td>145.0</td>
      <td>Goals</td>
      <td>0.703883</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2014</td>
      <td>206.0</td>
      <td>Goals</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = pd.DataFrame({"Year": [], "y": [], "Index": [], "Grade": []})
# 循环查找各届比赛并计算对应的分数，选出大于等于1的放到df中
i = 1930
while i <= 2014:
    ans = data.query('Year=='+str(i))
    if (len(ans) > 1):
        if (ans.loc[:, 'Grade'].sum() >= 1):
            insertRow = ans[0: 2]
            # 采用concat进行添加，append会有后续不支持的提示
            df = pd.concat([df, ans])
    i += 4
#  对‘Year’和‘y’进行取整操作，以便图中显示
df['Year'] = data['Year'].astype(int)
df['y'] = data['y'].astype(int)
df
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
      <th>Year</th>
      <th>y</th>
      <th>Index</th>
      <th>Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>1982</td>
      <td>2109723</td>
      <td>Attendance</td>
      <td>0.488447</td>
    </tr>
    <tr>
      <th>31</th>
      <td>1982</td>
      <td>146</td>
      <td>Goals</td>
      <td>0.708738</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1986</td>
      <td>2394031</td>
      <td>Attendance</td>
      <td>0.554271</td>
    </tr>
    <tr>
      <th>32</th>
      <td>1986</td>
      <td>132</td>
      <td>Goals</td>
      <td>0.640777</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1990</td>
      <td>2516215</td>
      <td>Attendance</td>
      <td>0.582559</td>
    </tr>
    <tr>
      <th>33</th>
      <td>1990</td>
      <td>115</td>
      <td>Goals</td>
      <td>0.558252</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1994</td>
      <td>3587538</td>
      <td>Attendance</td>
      <td>0.830594</td>
    </tr>
    <tr>
      <th>34</th>
      <td>1994</td>
      <td>141</td>
      <td>Goals</td>
      <td>0.684466</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1998</td>
      <td>2785100</td>
      <td>Attendance</td>
      <td>0.644812</td>
    </tr>
    <tr>
      <th>35</th>
      <td>1998</td>
      <td>171</td>
      <td>Goals</td>
      <td>0.830097</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2002</td>
      <td>2705197</td>
      <td>Attendance</td>
      <td>0.626313</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2002</td>
      <td>161</td>
      <td>Goals</td>
      <td>0.781553</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2006</td>
      <td>3359439</td>
      <td>Attendance</td>
      <td>0.777784</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2006</td>
      <td>147</td>
      <td>Goals</td>
      <td>0.713592</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2010</td>
      <td>3178856</td>
      <td>Attendance</td>
      <td>0.735975</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2010</td>
      <td>145</td>
      <td>Goals</td>
      <td>0.703883</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2014</td>
      <td>4319243</td>
      <td>Attendance</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2014</td>
      <td>206</td>
      <td>Goals</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



## 5 基本视图绘制
根据ggplot的要求，需要有x轴、y轴和分组变量放到aes审美映射中。
并且，为实现直方图的功能，还需要添加geoms的具体原语。如下图所示，为实现直方图的绘制，我使用的是使用geom_col()原语。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col()
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_15_0.png)
​    





    <ggplot: (156870788628)>



相比于堆叠的直方图，此处我们采用双直方图的效果可能会更好，故需要在geom_col()中加入position="dodge"的代码。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge")
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_17_0.png)
​    





    <ggplot: (156873574352)>



## 6 添加数据标签
完成了基本底图的绘制，接下来需要在图上加一些数据，让图中信息变得更丰富。首先，在各条形图上贴上数值标签，具体值取‘y’即可，分别表示观众总数和进球总数。以上可通过geom-geom_text()--并在其aes()参数中添加 label="y"来实现。为方便数据的显示，我们可对直方图的宽度进行调整，通过设置width属性来实现。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(label="y"))
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_19_0.png)
​    





    <ggplot: (156875705741)>



尽管数据标签已经添加上去了，但还不够美观，这主要体现在标签的大小、位置和内容上。首先标签的位置应该居于各条形图的中间（宽度和高度两方面）；然后是‘Attendance’的数值过大，不太好显示，可约算为多少W的形式；最后调整完以上内容，可适当对文字的大小做一些改动。


```python
# y的坐标比较好确定，取‘Grade’的一半即可
df["y_pos"] = df["Grade"]/2
# x的坐标则要麻烦一点，需要根据它的Index而定，是‘Attendance’则在‘Year’基础上前移，‘Goals’则后移
df['x_pos'] = df.apply(lambda x: x['Year']-0.7 if x['Index']
                       == 'Attendance' else x['Year']+0.7, axis=1)
# 用类似的方式再加一列，转换标签为‘Attendance’的数据，不过此处转换并没有进行四舍五入，直接通过‘//’的除法来实现
df['y_show'] = df.apply(lambda x: str(x['y']//10000)+'w' if x['Index']
                        == 'Attendance' else x['y'], axis=1)
# 然后添加geom_text()原语通过aes()映射将求得的坐标赋上去
# 文字大小的调整则可设置size属性，此处设置为7
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_21_0.png)
​    





    <ggplot: (156875769532)>



## 7 调整轴的刻度
观察已画出的图，我发现‘Year’没有显示出具体的年份，就无法知道具体是那届世界杯，故需要改变X轴的刻度线，可使用scale_x_continuous选项。这里将X轴改为每四年，因为世界杯是每四年一届。正好筛选出来的年份是1982年到2014年所有的世界杯，没有中间某届不在范围内的情况出现，故不需要再对x轴做进一步处理。可使用的参数如下：break选项来设置断点，它需要一个数值的列表作为参数。 可以使用numpy的range函数来生成这样一个列表，可设置开始、结束和步长。注意，由于 Python 的索引，在使用 np.range 的范围是[,)。同样地，如果想改变Y轴，可以使用scale_y_continuous选项。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_23_0.png)
​    





    <ggplot: (156875797514)>



## 8 设置轴标签和标题
为了改变轴的标签，可以使用xlab和ylab方法添加所需的名称，分别对应的是x轴和y轴。添加标题，则要设置ggtitle选项，并将要设置的内容作为一个字符串参数传入。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_25_0.png)
​    





    <ggplot: (156875691678)>



## 9 调整调色板
plotnine支持matplotlib中的调色板，通过scale_fill_manual选项可以设置，里面需要的参数是关于颜色的列表。先尝试把条形区域设为青绿色和橘黄色。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_manual(["mediumturquoise", "orange"])
)
p

```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_27_0.png)
​    





    <ggplot: (156875907379)>



除此之外，还可以使用HEX代码来表示颜色，如以上区域的颜色可改为#F0FF00、#58CFFB。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_manual(["#F0FF00", "#58CFFB"])
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_29_0.png)
​    





    <ggplot: (156875963152)>



除了手动的调颜色，还可以使用现成的配色方案，plotnine提供了scale_colour_brewer的选项，需要的参数主要是type和palette。type指数据的类型，有['seq', 'div', 'qual']三个选择，分别是连续、离散、定性，其实不一定要严格按照这个来，只要配色适合就可以。palette可以是str和int类型，如果是前者，则按该命名选取；如果是后者，因每个值都有对应的str，索引即可，默认为1。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_31_0.png)
​    





    <ggplot: (156876241752)>



## 10 调整图例
目前的图例处于默认位置，即整体的右侧，较为割裂，这可以添加一个theme选项来做调整。具体来说，可设置legend_position选项指定其位于底部，并设置legend_direction="horizontal"让图例水平排布，当然这是底部的默认选项，可不写出。同时，‘Index’作为图例的标题有点冗余，可以用legend_title=element_blank()将它隐藏。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
    + theme(
        legend_position="bottom",
        legend_title=element_blank(),
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_33_0.png)
​    





    <ggplot: (156875917736)>



但有点问题的就是，图例把X轴的标题以及部分数据遮住了。这时需要在theme中使用 legend_box_spacing 选项来解决这个问题，它可以将图例的位置向下移动。具体的值要由具体情况而定，可以反复调整取最佳的位置。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
    + theme(
        legend_position="bottom",
        legend_title=element_blank(),
        legend_box_spacing=0.4,
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_35_0.png)
​    





    <ggplot: (156875735251)>



可以看到，图例在下方单独拿一部分来显示会感觉有点冗余，希望其放在与‘Year’平行靠右的位置。这里可以使用legend_position的另一种参数传入形式，即坐标形式(x,y)，范围在(0,0)到(1,1)对应左下角到右上角。需反复调整，得到好的位置，此处坐标选的是(0.7,0)。然后发现还需对‘Year’，即x轴标题做下移操作，可使用theme中的axis_title_x选项，参数格式需用element_text()的格式，在里面使用margin这个参数。值得一提的是，这个参数的赋值比较怪，类似于{'t': 15}的格式，前面可选项为['t','r','b','l']，表示top、right、bottom、left；后面为移动的数值。为对称考虑，也对y轴标题做相同移动。至于标题，留到下一节处理。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
    + theme(
        axis_title_x = element_text(margin={'t': 15}),
        axis_title_y = element_text(margin={'r': 15}),
        legend_position=(0.7,0),
        legend_direction="horizontal",
        legend_title=element_blank(),
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_37_0.png)
​    





    <ggplot: (156876171643)>



## 11 调整标题
类似于x轴标题的调整，theme中的plot_title选项可对标题样式做更改，同样也是在element_text()中进行参数赋值。具体用法可见下方注释。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
    + theme(
        plot_title=element_text(face="bold.italic",  # 字体
                                color="#223C5F",  # 颜色
                                size=24,  # 大小
                                vjust=0),  # 垂直位置，相对于图，0为远离，1为靠近。
        axis_title_x=element_text(margin={'t': 15}),
        axis_title_y=element_text(margin={'r': 15}),
        legend_position=(0.7, 0),
        legend_direction="horizontal",
        legend_title=element_blank(),
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_39_0.png)
​    





    <ggplot: (156875716521)>



## 12 选用主题
以上都是对一些细节进行改动，如果相对整体的样式进行改动，可在在ggplot()后面添加对应的主题函数。主要有以下几种：
- theme_grey()：默认主题，灰色背景，不带x、y轴线，带白色网格线等
- theme_bw()：变色背景，带全方位x、y轴线（上下左右），带灰色网格线
- theme_classic()：无网格线，有轴线，类似excel风格
- theme_linedraw()：白色背景黑色线条，带黑框
- theme_light()：和theme_linedraw()很像，区别是线条为灰色
- theme_dark()：黑色背景的theme_light()，可以用来画薄彩色线条
- theme_minimal()：简约主题，无框
- theme_void()：空白主题
- theme_xkcd()：XKCD风格的主题
- theme_538()：538主题，灰色背景，无框，带线
还有一些以其他可视化工具为格式的主题，如theme_matplotlib()、theme_seaborn。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"), size=7)
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_brewer(type='qual', palette=2)
    + theme_classic()
    + theme(
        plot_title=element_text(face="bold.italic",  # 字体
                                color="#223C5F",  # 颜色
                                size=24,  # 大小
                                vjust=0),  # 垂直位置，相对于图，0为远离，1为靠近。
        axis_title_x=element_text(margin={'t': 15}),
        axis_title_y=element_text(margin={'r': 15}),
        legend_position=(0.7, 0),
        legend_direction="horizontal",
        legend_title=element_blank(),
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_41_0.png)
​    





    <ggplot: (156876533097)>



## 13 创建自定义主题
首先，自定义一个主题就要选定使用的字体，由于内置的字体有限，可从外部导入下载好的字体。再通过matplotlib.font_manager中的FontProperties选项以创建对象的形式把该字体加载到Python以供使用,并可以对该对象进行格式的设置。
具体到本次作业，我力图创建一个科技感的主题，使用的字体为赛博朋克风格的“Qualy_Bold.ttf”。


```python
# 导入模块
import matplotlib.font_manager as fm
# 读取字体
fpath = "Qualy_Bold.ttf"
# 创建字体对象
title_text = fm .FontProperties(fname=fpath)
body_text = fm .FontProperties(fname=fpath)
# 设置字体大小和样式，标题要设得大和粗一点
title_text .set_size(24)
title_text .set_weight("bold")
body_text.set_size(12)
```

为了让图表看起来更科技感，作如下改动：
- 改变各项文字的颜色，具体包括geom_text(colour='black')；plot_title、axis_title_x、axis_title_y、axis_text_x、axis_text_y中的element_text(color="#EAF7F7")
- 通过改变 axis_line_x 和 axis_line_y 的参数可以改变轴线的厚度和颜色，传值需用element_line()映射。
- 摆脱网格线，需要设置四个参数为空（element_blank()）：panel_grid_major, panel_grid_minor, panel_border 和 panel_background。
- 使用刚刚导入的科技感字体，需改变plot_title和text的值，传值也要进行映射：element_text(fontproperties=body_text)。
- 去除图例的背景色，设置legend_background为空。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"),
                size=9, colour='black', fontstyle='oblique')
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_manual(["#07a793", "#84ff94"])
    + theme(
        plot_background=element_rect(fill='#05171b', colour='#05171b'),
        plot_title=element_text(color="#EAF7F7",  # 颜色
                                size=24,  # 大小
                                vjust=0,
                                fontproperties=title_text
                                ),  # 垂直位置，相对于图，0为远离，1为靠近。
        axis_title_x=element_text(margin={'t': 15}, colour="#EAF7F7"),
        axis_title_y=element_text(margin={'r': 15}, colour="#EAF7F7"),
        legend_position=(0.7, 0),
        legend_direction="horizontal",
        legend_title=element_blank(),
        legend_text=element_text(colour="#EAF7F7"),
        axis_line_x=element_line(size=2, colour="#EAF7F7"),
        axis_line_y=element_line(size=2, colour="#EAF7F7"),
        legend_background=element_blank(),
        panel_grid_major=element_blank(),
        panel_grid_minor=element_blank(),
        panel_border=element_blank(),
        panel_background=element_blank(),
        text=element_text(fontproperties=body_text),
        axis_text_x=element_text(colour="#EAF7F7"),
        axis_text_y=element_text(colour="#EAF7F7"),
    )
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_45_0.png)
​    





    <ggplot: (156876500176)>



## 14 添加图片
最后，为在标题中展现足球的元素，可以通过watermark（水印）来导入本地图片。具体设置为filename为图片路径, xo、yo为图片的位置，可随具体情况调整。


```python
p = (
    ggplot(df, aes(x='Year', y='Grade', fill='Index'))
    + geom_col(position="dodge", width=3)
    + geom_text(df, aes(x="x_pos", y="y_pos", label="y_show"),
                size=9, colour='black', fontstyle='oblique')
    + scale_x_continuous(breaks=np .arange(1982, 2015, 4))
    + ggtitle("Top exciting World Cup matches")
    + xlab("Year")
    + ylab("Measure of excitement")
    + scale_fill_manual(["#07a793", "#84ff94"])
    + theme(
        plot_background=element_rect(fill='#05171b', colour='#05171b'),
        plot_title=element_text(color="#EAF7F7",  # 颜色
                                size=24,  # 大小
                                vjust=0,
                                fontproperties=title_text
                                ),  # 垂直位置，相对于图，0为远离，1为靠近。
        axis_title_x=element_text(margin={'t': 15}, colour="#EAF7F7"),
        axis_title_y=element_text(margin={'r': 15}, colour="#EAF7F7"),
        legend_position=(0.7, 0),
        legend_direction="horizontal",
        legend_title=element_blank(),
        legend_text=element_text(colour="#EAF7F7"),
        axis_line_x=element_line(size=2, colour="#EAF7F7"),
        axis_line_y=element_line(size=2, colour="#EAF7F7"),
        legend_background=element_blank(),
        panel_grid_major=element_blank(),
        panel_grid_minor=element_blank(),
        panel_border=element_blank(),
        panel_background=element_blank(),
        text=element_text(fontproperties=body_text),
        axis_text_x=element_text(colour="#EAF7F7"),
        axis_text_y=element_text(colour="#EAF7F7"),
    )
    + watermark(filename='soccer.png', xo=190, yo=417)
)
p
```


​    
![png](WorldCupVisualization_files/WorldCupVisualization_47_0.png)
​    





    <ggplot: (156875740413)>



## 总结
本文根据课上所学内容，对时下热点内容进行了可视化。先对数据按ggplot的要求读入格式进行了预处理，再依次进行了基本视图绘制，并在此基础上做了一系列的优化，包括数据标签、轴的刻度与标签、标题、配色、图例、主题和图片的添加与调整。最后，得到了一张美观、简洁且具有科技感的世界杯精彩程度双直方图。
