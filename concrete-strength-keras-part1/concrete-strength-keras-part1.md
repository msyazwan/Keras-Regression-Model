
<h2><center> Building a Concrete Compressive Strength Model using Deep Learning Keras Library </center></h2>

<img src = "tf-keras.png" width = 500>

<strong>The dataset is about the compressive strength of different samples of concrete based on the volumes of the different ingredients that were used to make them.</strong>
    
<strong>Ingredients include:</strong>
<ol type="1">
  <li>Cement</li>
  <li>Blast Furnace Slag</li>
  <li>Fly Ash</li>
  <li>Water</li>
  <li>Superplasticizer</li>
  <li>Coarse Aggregate</li>
  <li>Fine Aggregate</li>
  <li>Age</li>
</ol>

<h3><font color="red"> The objective of this project is to build a regression model using Keras library to predict the compressive strength of a concrete based on its ingredients.</font></h3>

### 1. OBTAIN - Obtain Data from its Source.

First lets download the data and stored in pandas dataframe


```python
import pandas as pd
import numpy as np

import matplotlib.pyplot as plt
import seaborn as sns

import time
start = time.time()
```


```python
url = 'https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/DL0101EN/labs/data/concrete_data.csv'
```


```python
df = pd.read_csv(url)
```


```python
df.head()
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
      <th>Cement</th>
      <th>Blast Furnace Slag</th>
      <th>Fly Ash</th>
      <th>Water</th>
      <th>Superplasticizer</th>
      <th>Coarse Aggregate</th>
      <th>Fine Aggregate</th>
      <th>Age</th>
      <th>Strength</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>540.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>162.0</td>
      <td>2.5</td>
      <td>1040.0</td>
      <td>676.0</td>
      <td>28</td>
      <td>79.99</td>
    </tr>
    <tr>
      <th>1</th>
      <td>540.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>162.0</td>
      <td>2.5</td>
      <td>1055.0</td>
      <td>676.0</td>
      <td>28</td>
      <td>61.89</td>
    </tr>
    <tr>
      <th>2</th>
      <td>332.5</td>
      <td>142.5</td>
      <td>0.0</td>
      <td>228.0</td>
      <td>0.0</td>
      <td>932.0</td>
      <td>594.0</td>
      <td>270</td>
      <td>40.27</td>
    </tr>
    <tr>
      <th>3</th>
      <td>332.5</td>
      <td>142.5</td>
      <td>0.0</td>
      <td>228.0</td>
      <td>0.0</td>
      <td>932.0</td>
      <td>594.0</td>
      <td>365</td>
      <td>41.05</td>
    </tr>
    <tr>
      <th>4</th>
      <td>198.6</td>
      <td>132.4</td>
      <td>0.0</td>
      <td>192.0</td>
      <td>0.0</td>
      <td>978.4</td>
      <td>825.5</td>
      <td>360</td>
      <td>44.30</td>
    </tr>
  </tbody>
</table>
</div>



Let's check the shape of the dataframe

### 2. SCRUB - Clean / Preprocess Data to Format that Machine Understand.


```python
df.isnull().sum()
```




    Cement                0
    Blast Furnace Slag    0
    Fly Ash               0
    Water                 0
    Superplasticizer      0
    Coarse Aggregate      0
    Fine Aggregate        0
    Age                   0
    Strength              0
    dtype: int64



The data looks very clean; no missing data and all data is in numerical form. 

Nothing much here, lets move to our next step!

### 3. EXPLORE - Find Significant Patterns and Trends using Statistical Method.


```python
df.shape
```




    (1030, 9)




```python
df.describe()
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
      <th>Cement</th>
      <th>Blast Furnace Slag</th>
      <th>Fly Ash</th>
      <th>Water</th>
      <th>Superplasticizer</th>
      <th>Coarse Aggregate</th>
      <th>Fine Aggregate</th>
      <th>Age</th>
      <th>Strength</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
      <td>1030.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>281.167864</td>
      <td>73.895825</td>
      <td>54.188350</td>
      <td>181.567282</td>
      <td>6.204660</td>
      <td>972.918932</td>
      <td>773.580485</td>
      <td>45.662136</td>
      <td>35.817961</td>
    </tr>
    <tr>
      <th>std</th>
      <td>104.506364</td>
      <td>86.279342</td>
      <td>63.997004</td>
      <td>21.354219</td>
      <td>5.973841</td>
      <td>77.753954</td>
      <td>80.175980</td>
      <td>63.169912</td>
      <td>16.705742</td>
    </tr>
    <tr>
      <th>min</th>
      <td>102.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>121.800000</td>
      <td>0.000000</td>
      <td>801.000000</td>
      <td>594.000000</td>
      <td>1.000000</td>
      <td>2.330000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>192.375000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>164.900000</td>
      <td>0.000000</td>
      <td>932.000000</td>
      <td>730.950000</td>
      <td>7.000000</td>
      <td>23.710000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>272.900000</td>
      <td>22.000000</td>
      <td>0.000000</td>
      <td>185.000000</td>
      <td>6.400000</td>
      <td>968.000000</td>
      <td>779.500000</td>
      <td>28.000000</td>
      <td>34.445000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>350.000000</td>
      <td>142.950000</td>
      <td>118.300000</td>
      <td>192.000000</td>
      <td>10.200000</td>
      <td>1029.400000</td>
      <td>824.000000</td>
      <td>56.000000</td>
      <td>46.135000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>540.000000</td>
      <td>359.400000</td>
      <td>200.100000</td>
      <td>247.000000</td>
      <td>32.200000</td>
      <td>1145.000000</td>
      <td>992.600000</td>
      <td>365.000000</td>
      <td>82.600000</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize=(12, 5))
correlation_matrix = df.corr()
sns.heatmap(correlation_matrix, annot=True)
plt.show()
```


![png](output_16_0.png)



```python
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Scatter(y=df['Strength'], x=df['Cement'], mode = 'markers')],
    layout_title_text="Concrete Cement vs Strength"
)

fig.show()
```


        <script type="text/javascript">
        window.PlotlyConfig = {MathJaxConfig: 'local'};
        if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
        if (typeof require !== 'undefined') {
        require.undef("plotly");
        define('plotly', function(require, exports, module) {
            /**
* plotly.js v1.50.1
* Copyright 2012-2019, Plotly, Inc.
* All rights reserved.
* Licensed under the MIT license
*/
        });
        require(['plotly'], function(Plotly) {
            window._Plotly = Plotly;
        });
        }
        </script>
        



<div>
        
        
            <div id="01a46734-b6ed-4013-9755-c5a2f1012180" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("01a46734-b6ed-4013-9755-c5a2f1012180")) {
                    Plotly.newPlot(
                        '01a46734-b6ed-4013-9755-c5a2f1012180',
                        [{"mode": "markers", "type": "scatter", "x": [540.0, 540.0, 332.5, 332.5, 198.6, 266.0, 380.0, 380.0, 266.0, 475.0, 198.6, 198.6, 427.5, 190.0, 304.0, 380.0, 139.6, 342.0, 380.0, 475.0, 427.5, 139.6, 139.6, 139.6, 380.0, 380.0, 380.0, 342.0, 427.5, 475.0, 304.0, 266.0, 198.6, 475.0, 190.0, 237.5, 237.5, 332.5, 475.0, 237.5, 342.0, 427.5, 237.5, 380.0, 427.5, 427.5, 349.0, 380.0, 237.5, 380.0, 332.5, 190.0, 237.5, 304.0, 139.6, 198.6, 475.0, 198.6, 304.0, 332.5, 304.0, 266.0, 310.0, 190.0, 266.0, 342.0, 139.6, 332.5, 190.0, 485.0, 374.0, 313.3, 425.0, 425.0, 375.0, 475.0, 469.0, 425.0, 388.6, 531.3, 425.0, 318.8, 401.8, 362.6, 323.7, 379.5, 362.6, 286.3, 362.6, 439.0, 389.9, 362.6, 337.9, 374.0, 313.3, 425.0, 425.0, 375.0, 475.0, 469.0, 425.0, 388.6, 531.3, 425.0, 318.8, 401.8, 362.6, 323.7, 379.5, 362.6, 286.3, 362.6, 439.0, 389.9, 362.6, 337.9, 374.0, 313.3, 425.0, 425.0, 375.0, 475.0, 469.0, 425.0, 388.6, 531.3, 425.0, 318.8, 401.8, 362.6, 323.7, 379.5, 362.6, 286.3, 362.6, 439.0, 389.9, 362.6, 337.9, 374.0, 313.3, 425.0, 425.0, 375.0, 475.0, 469.0, 425.0, 388.6, 531.3, 425.0, 318.8, 401.8, 362.6, 323.7, 379.5, 362.6, 286.3, 362.6, 439.0, 389.9, 362.6, 337.9, 374.0, 313.3, 425.0, 425.0, 375.0, 475.0, 469.0, 425.0, 388.6, 531.3, 425.0, 318.8, 401.8, 362.6, 379.5, 362.6, 286.3, 362.6, 439.0, 389.9, 362.6, 337.9, 222.4, 222.4, 222.4, 222.4, 222.4, 233.8, 233.8, 233.8, 233.8, 233.8, 194.7, 194.7, 194.7, 194.7, 194.7, 190.7, 190.7, 190.7, 190.7, 190.7, 212.1, 212.1, 212.1, 212.1, 212.1, 230.0, 230.0, 230.0, 230.0, 230.0, 190.3, 190.3, 190.3, 190.3, 190.3, 166.1, 166.1, 166.1, 166.1, 166.1, 168.0, 168.0, 168.0, 168.0, 168.0, 213.7, 213.7, 213.7, 213.7, 213.7, 213.8, 213.8, 213.8, 213.8, 213.8, 229.7, 229.7, 229.7, 229.7, 229.7, 238.1, 238.1, 238.1, 238.1, 238.1, 250.0, 250.0, 250.0, 250.0, 250.0, 212.5, 212.5, 212.5, 212.5, 212.5, 212.6, 212.6, 212.6, 212.6, 212.6, 212.0, 212.0, 212.0, 212.0, 212.0, 231.8, 231.8, 231.8, 231.8, 231.8, 251.4, 251.4, 251.4, 251.4, 251.4, 251.4, 251.4, 251.4, 251.4, 251.4, 181.4, 181.4, 181.4, 181.4, 181.4, 182.0, 182.0, 182.0, 182.0, 182.0, 168.9, 168.9, 168.9, 168.9, 168.9, 290.4, 290.4, 290.4, 290.4, 290.4, 277.1, 277.1, 277.1, 277.1, 277.1, 295.7, 295.7, 295.7, 295.7, 295.7, 251.8, 251.8, 251.8, 251.8, 251.8, 249.1, 249.1, 249.1, 249.1, 249.1, 252.3, 252.3, 252.3, 252.3, 252.3, 246.8, 246.8, 246.8, 246.8, 246.8, 275.1, 275.1, 275.1, 275.1, 275.1, 297.2, 297.2, 297.2, 297.2, 297.2, 213.7, 213.7, 213.7, 213.7, 213.7, 213.5, 213.5, 213.5, 213.5, 213.5, 277.2, 277.2, 277.2, 277.2, 277.2, 218.2, 218.2, 218.2, 218.2, 218.2, 214.9, 214.9, 214.9, 214.9, 214.9, 218.9, 218.9, 218.9, 218.9, 218.9, 376.0, 376.0, 376.0, 376.0, 376.0, 500.0, 475.0, 315.0, 505.0, 451.0, 516.0, 520.0, 528.0, 520.0, 385.0, 500.1, 450.1, 397.0, 333.0, 334.0, 405.0, 200.0, 516.0, 145.0, 160.0, 234.0, 250.0, 475.0, 285.0, 356.0, 275.0, 500.0, 165.0, 165.0, 178.0, 167.4, 172.4, 173.5, 167.0, 173.8, 190.3, 250.0, 213.5, 194.7, 251.4, 165.0, 165.0, 178.0, 167.4, 172.4, 173.5, 167.0, 173.8, 190.3, 250.0, 213.5, 194.7, 251.4, 165.0, 165.0, 178.0, 167.4, 172.4, 173.5, 167.0, 173.8, 190.3, 250.0, 213.5, 194.7, 251.4, 165.0, 165.0, 178.0, 167.4, 172.4, 173.5, 167.0, 173.8, 190.3, 250.0, 213.5, 194.7, 251.4, 165.0, 165.0, 178.0, 167.4, 172.4, 173.5, 167.0, 173.8, 190.3, 250.0, 213.5, 194.7, 251.4, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 446.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 387.0, 355.0, 355.0, 491.0, 491.0, 491.0, 491.0, 491.0, 491.0, 491.0, 491.0, 424.0, 424.0, 424.0, 424.0, 424.0, 424.0, 424.0, 424.0, 202.0, 202.0, 202.0, 202.0, 284.0, 284.0, 284.0, 284.0, 359.0, 359.0, 359.0, 359.0, 359.0, 359.0, 359.0, 359.0, 436.0, 289.0, 289.0, 393.0, 393.0, 393.0, 480.0, 480.0, 480.0, 480.0, 333.0, 255.0, 255.0, 289.0, 255.0, 333.0, 333.0, 289.0, 333.0, 393.0, 255.0, 158.8, 239.6, 238.2, 181.9, 193.5, 255.5, 272.8, 239.6, 220.8, 397.0, 382.5, 210.7, 158.8, 295.8, 255.5, 203.5, 397.0, 381.4, 295.8, 228.0, 220.8, 316.1, 135.7, 238.1, 339.2, 135.7, 193.5, 203.5, 290.2, 181.9, 170.3, 210.7, 228.0, 290.2, 381.4, 238.2, 186.2, 339.2, 238.1, 252.5, 382.5, 252.5, 316.1, 186.2, 170.3, 272.8, 339.0, 339.0, 339.0, 339.0, 339.0, 339.0, 339.0, 236.0, 236.0, 236.0, 236.0, 236.0, 236.0, 277.0, 277.0, 277.0, 277.0, 277.0, 277.0, 254.0, 254.0, 254.0, 254.0, 307.0, 307.0, 307.0, 307.0, 307.0, 236.0, 200.0, 200.0, 225.0, 225.0, 325.0, 325.0, 275.0, 275.0, 300.0, 300.0, 375.0, 375.0, 400.0, 400.0, 250.0, 250.0, 350.0, 350.0, 203.5, 250.2, 157.0, 141.3, 166.8, 122.6, 183.9, 102.0, 102.0, 122.6, 166.8, 200.0, 108.3, 305.3, 108.3, 116.0, 141.3, 157.0, 133.0, 250.2, 173.0, 192.0, 192.0, 153.0, 288.0, 305.3, 236.0, 173.0, 212.0, 236.0, 183.9, 166.8, 102.0, 288.0, 212.0, 102.0, 173.0, 183.9, 133.0, 192.0, 133.0, 305.3, 236.0, 108.3, 157.0, 288.0, 212.0, 212.0, 153.0, 236.0, 116.0, 183.9, 108.3, 203.5, 203.5, 133.0, 288.0, 200.0, 200.0, 250.2, 122.6, 153.0, 200.0, 116.0, 173.0, 250.2, 305.3, 192.0, 157.0, 153.0, 141.3, 116.0, 141.3, 122.6, 166.8, 203.5, 310.0, 310.0, 310.0, 310.0, 310.0, 331.0, 331.0, 331.0, 331.0, 331.0, 349.0, 349.0, 349.0, 349.0, 349.0, 238.0, 238.0, 296.0, 296.0, 297.0, 480.0, 480.0, 397.0, 281.0, 281.0, 500.0, 500.0, 500.0, 500.0, 500.0, 540.0, 540.0, 540.0, 540.0, 540.0, 540.0, 350.0, 350.0, 350.0, 350.0, 350.0, 350.0, 385.0, 385.0, 385.0, 385.0, 385.0, 331.0, 331.0, 349.0, 331.0, 382.0, 382.0, 382.0, 281.0, 339.0, 339.0, 295.0, 295.0, 238.0, 296.0, 296.0, 296.0, 331.0, 331.0, 331.0, 331.0, 349.0, 349.0, 349.0, 349.0, 349.0, 302.0, 302.0, 525.0, 500.0, 500.0, 500.0, 540.0, 252.0, 252.0, 339.0, 393.0, 393.0, 393.0, 382.0, 382.0, 252.0, 252.0, 310.0, 310.0, 310.0, 310.0, 310.0, 525.0, 525.0, 525.0, 525.0, 525.0, 525.0, 322.0, 322.0, 322.0, 302.0, 397.0, 480.0, 522.0, 522.0, 273.0, 162.0, 154.0, 147.0, 152.0, 310.0, 144.0, 304.0, 374.0, 159.0, 153.0, 310.0, 305.0, 151.0, 142.0, 298.0, 321.0, 366.0, 280.0, 252.0, 165.0, 156.0, 160.0, 298.0, 318.0, 287.0, 326.0, 356.0, 132.0, 322.0, 164.0, 314.0, 321.0, 140.0, 288.0, 298.0, 265.0, 160.0, 166.0, 276.0, 322.0, 149.0, 159.0, 261.0, 237.0, 313.0, 155.0, 146.0, 296.0, 133.0, 313.0, 152.0, 153.0, 140.0, 149.0, 300.0, 153.0, 148.0, 326.0, 153.0, 262.0, 158.0, 151.0, 273.0, 149.0, 143.0, 260.0, 313.0, 284.0, 336.0, 145.0, 150.0, 144.0, 331.0, 155.0, 155.0, 135.0, 266.0, 314.0, 313.0, 146.0, 144.0, 148.0, 277.0, 298.0, 313.0, 155.0, 289.0, 148.0, 145.0, 313.0, 136.0, 155.0, 255.0, 162.0, 136.0, 164.0, 162.0, 157.0, 149.0, 135.0, 159.0, 144.0, 154.0, 167.0, 184.0, 156.0, 236.9, 313.3, 154.8, 145.9, 296.0, 133.1, 313.3, 151.6, 153.1, 139.9, 149.5, 299.8, 153.1, 148.1, 326.5, 152.7, 261.9, 158.4, 150.7, 272.6, 149.0, 143.0, 259.9, 312.9, 284.0, 336.5, 144.8, 150.0, 143.7, 330.5, 154.8, 154.8, 134.7, 266.2, 314.0, 312.7, 145.7, 143.8, 148.1, 277.0, 298.1, 313.3, 155.2, 289.0, 147.8, 145.4, 312.7, 136.4, 154.8, 255.3, 272.8, 162.0, 153.6, 146.5, 151.8, 309.9, 143.6, 303.6, 374.3, 158.6, 152.6, 310.0, 304.8, 150.9, 141.9, 297.8, 321.3, 366.0, 279.8, 252.1, 164.6, 155.6, 160.2, 298.1, 317.9, 287.3, 325.6, 355.9, 132.0, 322.5, 164.2, 313.8, 321.4, 139.7, 288.4, 298.2, 264.5, 159.8, 166.0, 276.4, 322.2, 148.5, 159.1, 260.9], "y": [79.99, 61.89, 40.27, 41.05, 44.3, 47.03, 43.7, 36.45, 45.85, 39.29, 38.07, 28.02, 43.01, 42.33, 47.81, 52.91, 39.36, 56.14, 40.56, 42.62, 41.84, 28.24, 8.06, 44.21, 52.52, 53.3, 41.15, 52.12, 37.43, 38.6, 55.26, 52.91, 41.72, 42.13, 53.69, 38.41, 30.08, 37.72, 42.23, 36.25, 50.46, 43.7, 39.0, 53.1, 41.54, 35.08, 15.05, 40.76, 26.26, 32.82, 39.78, 46.93, 33.12, 49.19, 14.59, 14.64, 41.93, 9.13, 50.95, 33.02, 54.38, 51.73, 9.87, 50.66, 48.7, 55.06, 44.7, 30.28, 40.86, 71.99, 34.4, 28.8, 33.4, 36.3, 29.0, 37.8, 40.2, 33.4, 28.1, 41.3, 33.4, 25.2, 41.1, 35.3, 28.3, 28.6, 35.3, 24.4, 35.3, 39.3, 40.6, 35.3, 24.1, 46.2, 42.8, 49.2, 46.8, 45.7, 55.6, 54.9, 49.2, 34.9, 46.9, 49.2, 33.4, 54.1, 55.9, 49.8, 47.1, 55.9, 38.0, 55.9, 56.1, 59.09, 22.9, 35.1, 61.09, 59.8, 60.29, 61.8, 56.7, 68.3, 66.9, 60.29, 50.7, 56.4, 60.29, 55.5, 68.5, 71.3, 74.7, 52.2, 71.3, 67.7, 71.3, 66.0, 74.5, 71.3, 49.9, 63.4, 64.9, 64.3, 64.9, 60.2, 72.3, 69.3, 64.3, 55.2, 58.8, 64.3, 66.1, 73.7, 77.3, 80.2, 54.9, 77.3, 72.99, 77.3, 71.7, 79.4, 77.3, 59.89, 64.9, 66.6, 65.2, 66.7, 62.5, 74.19, 70.7, 65.2, 57.6, 59.2, 65.2, 68.1, 75.5, 79.3, 56.5, 79.3, 76.8, 79.3, 73.3, 82.6, 79.3, 67.8, 11.58, 24.45, 24.89, 29.45, 40.71, 10.38, 22.14, 22.84, 27.66, 34.56, 12.45, 24.99, 25.72, 33.96, 37.34, 15.04, 21.06, 26.4, 35.34, 40.57, 12.47, 20.92, 24.9, 34.2, 39.61, 10.03, 20.08, 24.48, 31.54, 35.34, 9.45, 22.72, 28.47, 38.56, 40.39, 10.76, 25.48, 21.54, 28.63, 33.54, 7.75, 17.82, 24.24, 32.85, 39.23, 18.0, 30.39, 45.71, 50.77, 53.9, 13.18, 17.84, 40.23, 47.13, 49.97, 13.36, 22.32, 24.54, 31.35, 40.86, 19.93, 25.69, 30.23, 39.59, 44.3, 13.82, 24.92, 29.22, 38.33, 42.35, 13.54, 26.31, 31.64, 42.55, 42.92, 13.33, 25.37, 37.4, 44.4, 47.74, 19.52, 31.35, 38.5, 45.08, 47.82, 15.44, 26.77, 33.73, 42.7, 45.84, 17.22, 29.93, 29.65, 36.97, 43.58, 13.12, 24.43, 32.66, 36.64, 44.21, 13.62, 21.6, 27.77, 35.57, 45.37, 7.32, 21.5, 31.27, 43.5, 48.67, 7.4, 23.51, 31.12, 39.15, 48.15, 22.5, 34.67, 34.74, 45.08, 48.97, 23.14, 41.89, 48.28, 51.04, 55.64, 22.95, 35.23, 39.94, 48.72, 52.04, 21.02, 33.36, 33.94, 44.14, 45.37, 15.36, 28.68, 30.85, 42.03, 51.06, 21.78, 42.29, 50.6, 55.83, 60.95, 23.52, 42.22, 52.5, 60.32, 66.42, 23.8, 38.77, 51.33, 56.85, 58.61, 21.91, 36.99, 47.4, 51.96, 56.74, 17.57, 33.73, 40.15, 46.64, 50.08, 17.37, 33.7, 45.94, 51.43, 59.3, 30.45, 47.71, 63.14, 66.82, 66.95, 27.42, 35.96, 55.51, 61.99, 63.53, 18.02, 38.6, 52.2, 53.96, 56.63, 15.34, 26.05, 30.22, 37.27, 46.23, 16.28, 25.62, 31.97, 36.3, 43.06, 67.57, 57.23, 81.75, 64.02, 78.8, 41.37, 60.28, 56.83, 51.02, 55.55, 44.13, 39.38, 55.65, 47.28, 44.33, 52.3, 49.25, 41.37, 29.16, 39.4, 39.3, 67.87, 58.52, 53.58, 59.0, 76.24, 69.84, 14.4, 19.42, 20.73, 14.94, 21.29, 23.08, 15.52, 15.82, 12.55, 8.49, 15.61, 12.18, 11.98, 16.88, 33.09, 34.24, 31.81, 29.75, 33.01, 32.9, 29.55, 19.42, 24.66, 29.59, 24.28, 20.73, 26.2, 46.39, 39.16, 41.2, 33.69, 38.2, 41.41, 37.81, 24.85, 27.22, 44.64, 37.27, 33.27, 36.56, 53.72, 48.59, 51.72, 35.85, 53.77, 53.46, 48.99, 31.72, 39.64, 51.26, 43.39, 39.27, 37.96, 55.02, 49.99, 53.66, 37.68, 56.06, 56.81, 50.94, 33.56, 41.16, 52.96, 44.28, 40.15, 57.03, 44.42, 51.02, 53.39, 35.36, 25.02, 23.35, 52.01, 38.02, 39.3, 61.07, 56.14, 55.25, 54.77, 50.24, 46.68, 46.68, 22.75, 25.51, 34.77, 36.84, 45.9, 41.67, 56.34, 47.97, 61.46, 44.03, 55.45, 55.55, 57.92, 25.61, 33.49, 59.59, 29.55, 37.92, 61.86, 62.05, 32.01, 72.1, 39.0, 65.7, 32.11, 40.29, 74.36, 21.97, 9.85, 15.07, 23.25, 43.73, 13.4, 24.13, 44.52, 62.94, 59.49, 25.12, 23.64, 35.75, 38.61, 68.75, 66.78, 23.85, 32.07, 11.65, 19.2, 48.85, 39.6, 43.94, 34.57, 54.32, 24.4, 15.62, 21.86, 10.22, 14.6, 18.75, 31.97, 23.4, 25.57, 41.68, 27.74, 8.2, 9.62, 25.42, 15.69, 27.94, 32.63, 17.24, 19.77, 39.44, 25.75, 33.08, 24.07, 21.82, 21.07, 14.84, 32.05, 11.96, 25.45, 22.49, 25.22, 39.7, 13.09, 38.7, 7.51, 17.58, 21.18, 18.2, 17.2, 22.63, 21.86, 12.37, 25.73, 37.81, 21.92, 33.04, 14.54, 26.91, 8.0, 31.9, 10.34, 19.77, 37.44, 11.48, 24.44, 17.6, 10.73, 31.38, 13.22, 20.97, 27.04, 32.04, 35.17, 36.45, 38.89, 6.47, 12.84, 18.42, 21.95, 24.1, 25.08, 21.26, 25.97, 11.36, 31.25, 32.33, 33.7, 9.31, 26.94, 27.63, 29.79, 34.49, 36.15, 12.54, 27.53, 32.92, 9.99, 7.84, 12.25, 11.17, 17.34, 17.54, 30.57, 14.2, 24.5, 15.58, 26.85, 26.06, 38.21, 43.7, 30.14, 12.73, 20.87, 20.28, 34.29, 19.54, 47.71, 43.38, 29.89, 6.9, 33.19, 4.9, 4.57, 25.46, 24.29, 33.95, 11.41, 20.59, 25.89, 29.23, 31.02, 10.39, 33.66, 27.87, 19.35, 11.39, 12.79, 39.32, 4.78, 16.11, 43.38, 20.42, 6.94, 15.03, 13.57, 32.53, 15.75, 7.68, 38.8, 33.0, 17.28, 24.28, 24.05, 36.59, 50.73, 13.66, 14.14, 47.78, 2.33, 16.89, 23.52, 6.81, 39.7, 17.96, 32.88, 22.35, 10.79, 7.72, 41.68, 9.56, 6.88, 50.53, 17.17, 30.44, 9.73, 3.32, 26.32, 43.25, 6.28, 32.1, 36.96, 54.6, 21.48, 9.69, 8.37, 39.66, 10.09, 4.83, 10.35, 43.57, 51.86, 11.85, 17.24, 27.83, 35.76, 38.7, 14.31, 17.44, 31.74, 37.91, 39.38, 15.87, 9.01, 33.61, 40.66, 40.86, 12.05, 17.54, 18.91, 25.18, 30.96, 43.89, 54.28, 36.94, 14.5, 22.44, 12.64, 26.06, 33.21, 36.94, 44.09, 52.61, 59.76, 67.31, 69.66, 71.62, 74.17, 18.13, 22.53, 27.34, 29.98, 31.35, 32.72, 6.27, 14.7, 23.22, 27.92, 31.35, 39.0, 41.24, 14.99, 13.52, 24.0, 37.42, 11.47, 22.44, 21.16, 31.84, 14.8, 25.18, 17.54, 14.2, 21.65, 29.39, 13.52, 16.26, 31.45, 37.23, 18.13, 32.72, 39.49, 41.05, 42.13, 18.13, 26.74, 61.92, 47.22, 51.04, 55.16, 41.64, 13.71, 19.69, 31.65, 19.11, 39.58, 48.79, 24.0, 37.42, 11.47, 19.69, 14.99, 27.92, 34.68, 37.33, 38.11, 33.8, 42.42, 48.4, 55.94, 58.78, 67.11, 20.77, 25.18, 29.59, 21.75, 39.09, 24.39, 50.51, 74.99, 37.17, 33.76, 16.5, 19.99, 36.35, 33.69, 15.42, 33.42, 39.05, 27.68, 26.86, 45.3, 30.12, 15.57, 44.61, 53.52, 57.21, 65.91, 52.82, 33.4, 18.03, 37.36, 32.84, 42.64, 40.06, 41.94, 61.23, 40.87, 33.3, 52.42, 15.09, 38.46, 37.26, 35.23, 42.13, 31.87, 41.54, 39.45, 37.91, 44.28, 31.18, 23.69, 32.76, 32.4, 28.63, 36.8, 18.28, 33.06, 31.42, 31.03, 44.39, 12.18, 25.56, 36.44, 32.96, 23.84, 26.23, 17.95, 40.68, 19.01, 33.72, 8.54, 13.46, 32.24, 23.52, 29.72, 49.77, 52.44, 40.93, 44.86, 13.2, 37.43, 29.87, 56.61, 12.46, 23.79, 13.29, 39.42, 46.23, 44.52, 23.74, 26.14, 15.52, 43.57, 35.86, 41.05, 28.99, 46.24, 26.92, 10.54, 25.1, 29.07, 9.74, 33.8, 39.84, 26.97, 27.23, 30.65, 33.05, 24.58, 21.91, 30.88, 15.34, 24.34, 23.89, 22.93, 29.41, 28.63, 36.8, 18.29, 32.72, 31.42, 28.94, 40.93, 12.18, 25.56, 36.44, 32.96, 23.84, 26.23, 17.96, 38.63, 19.01, 33.72, 8.54, 13.46, 32.25, 23.52, 29.73, 49.77, 52.45, 40.93, 44.87, 13.2, 37.43, 29.87, 56.62, 12.46, 23.79, 13.29, 39.42, 46.23, 44.52, 23.74, 26.15, 15.53, 43.58, 35.87, 41.05, 28.99, 46.25, 26.92, 10.54, 25.1, 29.07, 9.74, 33.8, 37.17, 33.76, 16.5, 19.99, 36.35, 38.22, 15.42, 33.42, 39.06, 27.68, 26.86, 45.3, 30.12, 15.57, 44.61, 53.52, 57.22, 65.91, 52.83, 33.4, 18.03, 37.36, 35.31, 42.64, 40.06, 43.8, 61.24, 40.87, 33.31, 52.43, 15.09, 38.46, 37.27, 35.23, 42.14, 31.88, 41.54, 39.46, 37.92, 44.28, 31.18, 23.7, 32.77, 32.4]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Concrete Cement vs Strength"}},
                        {"responsive": true}
                    ).then(function(){
                            
var gd = document.getElementById('01a46734-b6ed-4013-9755-c5a2f1012180');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


As our objective is mainly to build the model, we will just touch a few in this EDA (exploratory data analysis) section.

### 4. MODEL - Construct Model to Predict and Forecast.

The part where the magic happens

#### Split Data to Predictors and Target


```python
X = df.iloc[:,:-1]
X.head()
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
      <th>Cement</th>
      <th>Blast Furnace Slag</th>
      <th>Fly Ash</th>
      <th>Water</th>
      <th>Superplasticizer</th>
      <th>Coarse Aggregate</th>
      <th>Fine Aggregate</th>
      <th>Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>540.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>162.0</td>
      <td>2.5</td>
      <td>1040.0</td>
      <td>676.0</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>540.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>162.0</td>
      <td>2.5</td>
      <td>1055.0</td>
      <td>676.0</td>
      <td>28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>332.5</td>
      <td>142.5</td>
      <td>0.0</td>
      <td>228.0</td>
      <td>0.0</td>
      <td>932.0</td>
      <td>594.0</td>
      <td>270</td>
    </tr>
    <tr>
      <th>3</th>
      <td>332.5</td>
      <td>142.5</td>
      <td>0.0</td>
      <td>228.0</td>
      <td>0.0</td>
      <td>932.0</td>
      <td>594.0</td>
      <td>365</td>
    </tr>
    <tr>
      <th>4</th>
      <td>198.6</td>
      <td>132.4</td>
      <td>0.0</td>
      <td>192.0</td>
      <td>0.0</td>
      <td>978.4</td>
      <td>825.5</td>
      <td>360</td>
    </tr>
  </tbody>
</table>
</div>




```python
y = df.iloc[:,-1]
y.head()
```




    0    79.99
    1    61.89
    2    40.27
    3    41.05
    4    44.30
    Name: Strength, dtype: float64



Save number of feature columns, <strong><i>n_cols</i></strong> to use later in model development.


```python
n_cols = X.shape[1]
n_cols
```




    8



#### Importing Libraries


```python
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error
from sklearn.metrics import r2_score

import keras

from keras.models import Sequential
from keras.layers import Dense
```

    Using TensorFlow backend.


### Building the Model

<strong><font color="red">A. BASELINE MODEL</font></strong>

<strong>Network Properties:</strong>
<ul>
  <li>Hidden Layer: 1</li>
  <li>Nodes: 10</li>
  <li>Activation Function: ReLU</li>
  <li>Optimizer: Adam</li>
  <li>Loss Function: Mean Squared Error</li>
  <li>Epochs: 50</li>
</ul>


```python
mse_A = []
r2_A = []

for i in range(50):
    
    #Split Data to Train and Test Set
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3)

    #Create model
    model = Sequential()
    model.add(Dense(10, activation='relu', input_shape=(n_cols,)))
    model.add(Dense(1))

    #Compile model
    model.compile(optimizer='adam', loss='mean_squared_error')

    #fit the model
    model.fit(X_train, y_train, epochs=50, verbose=0)

    #predict output on test set
    y_pred = model.predict(X_test)
    
    mse_A.append(mean_squared_error(y_test, y_pred))
    r2_A.append(r2_score(y_test, y_pred))
```


```python
print('mse_Mean: {:.2f}'.format(np.mean(mse_A)))
print('mse_StdDev: {:.2f}'.format(np.std(mse_A)))
```

    mse_Mean: 214.49
    mse_StdDev: 250.50



```python
print('R^2_Mean: {:.2f}'.format(np.mean(r2_A)))
print('R^2_StdDev: {:.2f}'.format(np.std(r2_A)))
```

    R^2_Mean: 0.24
    R^2_StdDev: 0.87


<strong><font color="red">B. MODEL WITH NORMALIZED DATA</font></strong>

<strong>Network Properties:</strong>
<ul>
  <li>Hidden Layer: 1</li>
  <li>Nodes: 10</li>
  <li>Activation Function: ReLU</li>
  <li>Optimizer: Adam</li>
  <li>Loss Function: Mean Squared Error</li>
  <li>Epochs: 50</li>
</ul>

Model is retrain with normalized data.


```python
X_norm = (X - X.mean()) / X.std()
X_norm.head()
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
      <th>Cement</th>
      <th>Blast Furnace Slag</th>
      <th>Fly Ash</th>
      <th>Water</th>
      <th>Superplasticizer</th>
      <th>Coarse Aggregate</th>
      <th>Fine Aggregate</th>
      <th>Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.476712</td>
      <td>-0.856472</td>
      <td>-0.846733</td>
      <td>-0.916319</td>
      <td>-0.620147</td>
      <td>0.862735</td>
      <td>-1.217079</td>
      <td>-0.279597</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2.476712</td>
      <td>-0.856472</td>
      <td>-0.846733</td>
      <td>-0.916319</td>
      <td>-0.620147</td>
      <td>1.055651</td>
      <td>-1.217079</td>
      <td>-0.279597</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.491187</td>
      <td>0.795140</td>
      <td>-0.846733</td>
      <td>2.174405</td>
      <td>-1.038638</td>
      <td>-0.526262</td>
      <td>-2.239829</td>
      <td>3.551340</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.491187</td>
      <td>0.795140</td>
      <td>-0.846733</td>
      <td>2.174405</td>
      <td>-1.038638</td>
      <td>-0.526262</td>
      <td>-2.239829</td>
      <td>5.055221</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.790075</td>
      <td>0.678079</td>
      <td>-0.846733</td>
      <td>0.488555</td>
      <td>-1.038638</td>
      <td>0.070492</td>
      <td>0.647569</td>
      <td>4.976069</td>
    </tr>
  </tbody>
</table>
</div>




```python
mse_B = []
r2_B = []

for i in range(50):
    
    #Split Data to Train and Test Set
    X_train, X_test, y_train, y_test = train_test_split(X_norm, y, test_size = 0.3)

    #Create model
    model = Sequential()
    model.add(Dense(10, activation='relu', input_shape=(n_cols,)))
    model.add(Dense(1))

    #Compile model
    model.compile(optimizer='adam', loss='mean_squared_error')

    #fit the model
    model.fit(X_train, y_train, epochs=50, verbose=0)

    #predict output on test set
    y_pred = model.predict(X_test)
    
    mse_B.append(mean_squared_error(y_test, y_pred))
    r2_B.append(r2_score(y_test, y_pred))
```


```python
print('mse_Mean: {:.2f}'.format(np.mean(mse_B)))
print('mse_StdDev: {:.2f}'.format(np.std(mse_B)))
```

    mse_Mean: 366.34
    mse_StdDev: 97.34



```python
print('R^2_Mean: {:.2f}'.format(np.mean(r2_B)))
print('R^2_StdDev: {:.2f}'.format(np.std(r2_B)))
```

    R^2_Mean: -0.31
    R^2_StdDev: 0.33


<strong><font color="red">C. MODEL WITH 100 EPOCHS</font></strong>

<strong>Network Properties:</strong>
<ul>
  <li>Hidden Layer: 1</li>
  <li>Nodes: 10</li>
  <li>Activation Function: ReLU</li>
  <li>Optimizer: Adam</li>
  <li>Loss Function: Mean Squared Error</li>
  <li>Epochs: 100</li>
</ul>

Model is retrained with 100 epochs.


```python
mse_C = []
r2_C = []

for i in range(50):
    
    #Split Data to Train and Test Set
    X_train, X_test, y_train, y_test = train_test_split(X_norm, y, test_size = 0.3)

    #Create model
    model = Sequential()
    model.add(Dense(10, activation='relu', input_shape=(n_cols,)))
    model.add(Dense(1))

    #Compile model
    model.compile(optimizer='adam', loss='mean_squared_error')

    #fit the model
    model.fit(X_train, y_train, epochs=100, verbose=0)

    #predict output on test set
    y_pred = model.predict(X_test)
    
    mse_C.append(mean_squared_error(y_test, y_pred))
    r2_C.append(r2_score(y_test, y_pred))
```


```python
print('mse_Mean: {:.2f}'.format(np.mean(mse_C)))
print('mse_StdDev: {:.2f}'.format(np.std(mse_C)))
```

    mse_Mean: 163.47
    mse_StdDev: 19.81



```python
print('R^2_Mean: {:.2f}'.format(np.mean(r2_C)))
print('R^2_StdDev: {:.2f}'.format(np.std(r2_C)))
```

    R^2_Mean: 0.41
    R^2_StdDev: 0.07


<strong><font color="red">D. MODEL WITH 3 HIDDEN LAYERS</font></strong>

<strong>Network Properties:</strong>
<ul>
  <li>Hidden Layers: 3</li>
  <li>Nodes: 10</li>
  <li>Activation Function: ReLU</li>
  <li>Optimizer: Adam</li>
  <li>Loss Function: Mean Squared Error</li>
  <li>Epochs: 100</li>
</ul>

Model is retrained with 3 hidden layers.


```python
mse_D = []
r2_D = []

for i in range(50):
    
    #Split Data to Train and Test Set
    X_train, X_test, y_train, y_test = train_test_split(X_norm, y, test_size = 0.3)

    #Create model
    model = Sequential()
    model.add(Dense(10, activation='relu', input_shape=(n_cols,)))
    model.add(Dense(10, activation='relu'))
    model.add(Dense(10, activation='relu'))
    model.add(Dense(1))

    #Compile model
    model.compile(optimizer='adam', loss='mean_squared_error')

    #fit the model
    model.fit(X_train, y_train, epochs=100, verbose=0)

    #predict output on test set
    y_pred = model.predict(X_test)
    
    mse_D.append(mean_squared_error(y_test, y_pred))
    r2_D.append(r2_score(y_test, y_pred))
```


```python
print('mse_Mean: {:.2f}'.format(np.mean(mse_D)))
print('mse_StdDev: {:.2f}'.format(np.std(mse_D)))
```

    mse_Mean: 91.02
    mse_StdDev: 22.57



```python
print('R^2_Mean: {:.2f}'.format(np.mean(r2_D)))
print('R^2_StdDev: {:.2f}'.format(np.std(r2_D)))
```

    R^2_Mean: 0.67
    R^2_StdDev: 0.08


### 5. iNTERPRET - Analyze and Interpret Model

#### Comparing all evaluation metrics


```python
from IPython.display import HTML, display
import tabulate

tabletest = [['STEPS','MSE: Mean','MSE: StdDev','R^2: Mean','R^2: StdDev'],
         ['A', round(np.mean(mse_A),2), round(np.std(mse_A),2), round(np.mean(r2_A),2), round(np.std(r2_A),2)],
         ['B', round(np.mean(mse_B),2), round(np.std(mse_B),2), round(np.mean(r2_B),2), round(np.std(r2_B),2)],
         ['C', round(np.mean(mse_C),2), round(np.std(mse_C),2), round(np.mean(r2_D),2), round(np.std(r2_C),2)],
         ['D', round(np.mean(mse_D),2), round(np.std(mse_D),2), round(np.mean(r2_D),2), round(np.std(r2_D),2)]]

display(HTML(tabulate.tabulate(tabletest, tablefmt='html')))
```


<table>
<tbody>
<tr><td>STEPS</td><td>MSE: Mean</td><td>MSE: StdDev</td><td>R^2: Mean</td><td>R^2: StdDev</td></tr>
<tr><td>A    </td><td>214.49   </td><td>250.5      </td><td>0.24     </td><td>0.87       </td></tr>
<tr><td>B    </td><td>366.34   </td><td>97.34      </td><td>-0.31    </td><td>0.33       </td></tr>
<tr><td>C    </td><td>163.47   </td><td>19.81      </td><td>0.67     </td><td>0.07       </td></tr>
<tr><td>D    </td><td>91.02    </td><td>22.57      </td><td>0.67     </td><td>0.08       </td></tr>
</tbody>
</table>



```python
end = time.time()
print('Time taken to complete iteration: {:.2f} minutes'.format((end-start)/60))
```

    Time taken to complete iteration: 12.07 minutes


<strong>From the results above, we can clearly see that by applying:</strong>
<ul>
  <li>Data Normalization,</li>
  <li>Increasing Epochs,</li>
  <li>and Increasing Hidden Layers</li>
</ul>

<strong>the mean of MSE has gone down, while the mean of R^2 has gone up indicating that the model accuracy is getting better.</strong>