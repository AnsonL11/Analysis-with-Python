# Covid-19 Analysis and Visualization


```python
import requests  
import json
import pprint   
import pandas as pd
import time
import matplotlib.pyplot as plt
import plotly as py
import plotly.offline as py
py.init_notebook_mode(connected=True)
import plotly.graph_objs as go
import plotly.express as px
import plotly.io as pio
from plotly.subplots import make_subplots

url = 'https://api.inews.qq.com/newsqa/v1/automation/modules/list?modules=FAutoCountryConfirmAdd,WomWorld,WomAboard&=%d' % int(time.time() *1000)
urlC = 'https://api.inews.qq.com/newsqa/v1/query/inner/publish/modules/list?modules=statisGradeCityDetail,diseaseh5Shelf&=%d' % int(time.time() * 1000)

```


<script type="text/javascript">
window.PlotlyConfig = {MathJaxConfig: 'local'};
if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
if (typeof require !== 'undefined') {
require.undef("plotly");
requirejs.config({
    paths: {
        'plotly': ['https://cdn.plot.ly/plotly-2.11.1.min']
    }
});
require(['plotly'], function(Plotly) {
    window._Plotly = Plotly;
});
}
</script>



# Scrape data from website


```python
response = requests.get(url=url)
web_data = response.json()
world_data = web_data['data']['WomAboard']
    #pprint.pprint(world_data)

world_covid = []

for i in world_data:
    world_dict = {}
    world_dict['continent'] = i['continent']
    world_dict['country'] = i['name']
    world_dict['confirm'] = i['confirm']
    world_dict['active'] = i['nowConfirm']
    world_dict['newcases'] = i['confirmAdd']
    world_dict['dead'] = i['dead']
    world_dict['heal'] = i['heal']
    world_covid.append(world_dict)

```


```python
world = pd.DataFrame(world_covid)
world[world['country']=='中国'] # to search for China's data
world.head()
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
      <th>continent</th>
      <th>country</th>
      <th>confirm</th>
      <th>active</th>
      <th>newcases</th>
      <th>dead</th>
      <th>heal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>北美洲</td>
      <td>美国</td>
      <td>86522561</td>
      <td>2904439</td>
      <td>19504</td>
      <td>1033591</td>
      <td>82584531</td>
    </tr>
    <tr>
      <th>1</th>
      <td>亚洲</td>
      <td>印度</td>
      <td>43181335</td>
      <td>25782</td>
      <td>3255</td>
      <td>524701</td>
      <td>42630852</td>
    </tr>
    <tr>
      <th>2</th>
      <td>南美洲</td>
      <td>巴西</td>
      <td>31153765</td>
      <td>423027</td>
      <td>696</td>
      <td>667056</td>
      <td>30063682</td>
    </tr>
    <tr>
      <th>3</th>
      <td>欧洲</td>
      <td>法国</td>
      <td>29641606</td>
      <td>452024</td>
      <td>20542</td>
      <td>148464</td>
      <td>29041118</td>
    </tr>
    <tr>
      <th>4</th>
      <td>欧洲</td>
      <td>德国</td>
      <td>26540852</td>
      <td>846604</td>
      <td>1010</td>
      <td>139748</td>
      <td>25554500</td>
    </tr>
  </tbody>
</table>
</div>



# Scrape China's data from website


```python
response_china = requests.get(url = urlC).json()
china_status = response_china['data']['diseaseh5Shelf']['areaTree'][0]['children']
#pprint.pprint(china_data)

china_covid = []
for i in china_status:
    china_dict ={}
    china_dict['province'] = i['name']
    china_dict['totalcase'] = i['total']['confirm']
    china_dict['active'] = i['total']['nowConfirm']
    china_dict['newcases'] = i['today']['confirm']
    china_dict['dead'] = i['total']['dead']
    china_dict['heal'] = i['total']['heal']
    china_covid.append(china_dict)
    
```


```python
china = pd.DataFrame(china_covid)
china.head()
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
      <th>province</th>
      <th>totalcase</th>
      <th>active</th>
      <th>newcases</th>
      <th>dead</th>
      <th>heal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>台湾</td>
      <td>2404871</td>
      <td>2388190</td>
      <td>62077</td>
      <td>2939</td>
      <td>13742</td>
    </tr>
    <tr>
      <th>1</th>
      <td>香港</td>
      <td>332721</td>
      <td>260825</td>
      <td>56</td>
      <td>9386</td>
      <td>62510</td>
    </tr>
    <tr>
      <th>2</th>
      <td>北京</td>
      <td>3432</td>
      <td>248</td>
      <td>5</td>
      <td>9</td>
      <td>3175</td>
    </tr>
    <tr>
      <th>3</th>
      <td>天津</td>
      <td>1977</td>
      <td>137</td>
      <td>1</td>
      <td>3</td>
      <td>1837</td>
    </tr>
    <tr>
      <th>4</th>
      <td>吉林</td>
      <td>40293</td>
      <td>131</td>
      <td>0</td>
      <td>5</td>
      <td>40157</td>
    </tr>
  </tbody>
</table>
</div>



# Incorporate global data = World Pandemic


```python
world = pd.DataFrame(world_covid)

china_status = response_china['data']['diseaseh5Shelf']['areaTree'][0]
confirm = china_status['total']['confirm']
active = china_status['total']['nowConfirm']
newcases = china_status['today']['confirm']
dead = china_status['total']['dead']
heal = china_status['total']['heal']

# To incorporate china's data into world's data
worldwide = world.append(
{
    'continent' : "亚洲",
    'country' : "中国",
    'confirm' : confirm,
    'active' : active,
    'newcases' : newcases,
    'dead' : dead,
    'heal' : heal

}, ignore_index = True)

worldwide.loc[worldwide['country'] == "中国"]

#To calculate deaths and recovered ratio
worldwide['Deaths ratio'] = (worldwide['dead'] / worldwide['confirm']*100).map('{:,.2f}%'.format)
worldwide['Recovered ratio'] = (worldwide['heal'] / worldwide['confirm']*100).map('{:,.2f}%'.format)
worldwide.rename(columns={'confirm':"Confirmed",'active':"Active",'newcases':"Newcases",'dead':"Deaths",'heal':"Recovered"},
                 inplace=True)
#worldwide.columns

country_name = pd.read_csv('D:\Program Files\PyCharm Project\Hello Python\world_map.csv', encoding='gbk')
pandemic = pd.merge(worldwide, country_name, left_on="country", right_on="国家", how="inner")
order = ['Continents','Country','Confirmed','Active','Newcases','Deaths','Recovered','Deaths ratio','Recovered ratio','国家','七大洲']
pandemic = pandemic[order]
pandemic.sort_values(by='Confirmed', ascending = False).head(10)

#pandemic[pandemic['Country']=='China']
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
      <th>Continents</th>
      <th>Country</th>
      <th>Confirmed</th>
      <th>Active</th>
      <th>Newcases</th>
      <th>Deaths</th>
      <th>Recovered</th>
      <th>Deaths ratio</th>
      <th>Recovered ratio</th>
      <th>国家</th>
      <th>七大洲</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>North America</td>
      <td>United States</td>
      <td>86522561</td>
      <td>2904439</td>
      <td>19504</td>
      <td>1033591</td>
      <td>82584531</td>
      <td>1.19%</td>
      <td>95.45%</td>
      <td>美国</td>
      <td>北美洲</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asia</td>
      <td>India</td>
      <td>43181335</td>
      <td>25782</td>
      <td>3255</td>
      <td>524701</td>
      <td>42630852</td>
      <td>1.22%</td>
      <td>98.73%</td>
      <td>印度</td>
      <td>亚洲</td>
    </tr>
    <tr>
      <th>2</th>
      <td>South America</td>
      <td>Brazil</td>
      <td>31153765</td>
      <td>423027</td>
      <td>696</td>
      <td>667056</td>
      <td>30063682</td>
      <td>2.14%</td>
      <td>96.50%</td>
      <td>巴西</td>
      <td>南美洲</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Europe</td>
      <td>France</td>
      <td>29641606</td>
      <td>452024</td>
      <td>20542</td>
      <td>148464</td>
      <td>29041118</td>
      <td>0.50%</td>
      <td>97.97%</td>
      <td>法国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Europe</td>
      <td>Germany</td>
      <td>26540852</td>
      <td>846604</td>
      <td>1010</td>
      <td>139748</td>
      <td>25554500</td>
      <td>0.53%</td>
      <td>96.28%</td>
      <td>德国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Europe</td>
      <td>United Kingdom</td>
      <td>22305893</td>
      <td>156448</td>
      <td>0</td>
      <td>178749</td>
      <td>21970696</td>
      <td>0.80%</td>
      <td>98.50%</td>
      <td>英国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Europe</td>
      <td>Russia</td>
      <td>18351851</td>
      <td>208070</td>
      <td>3786</td>
      <td>379520</td>
      <td>17764261</td>
      <td>2.07%</td>
      <td>96.80%</td>
      <td>俄罗斯</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Asia</td>
      <td>Korea</td>
      <td>18163686</td>
      <td>301922</td>
      <td>9835</td>
      <td>24258</td>
      <td>17837506</td>
      <td>0.13%</td>
      <td>98.20%</td>
      <td>韩国</td>
      <td>亚洲</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Europe</td>
      <td>Italy</td>
      <td>17505973</td>
      <td>641427</td>
      <td>15082</td>
      <td>166949</td>
      <td>16697597</td>
      <td>0.95%</td>
      <td>95.38%</td>
      <td>意大利</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Asia</td>
      <td>Turkey</td>
      <td>15072747</td>
      <td>2526</td>
      <td>0</td>
      <td>98965</td>
      <td>14971256</td>
      <td>0.66%</td>
      <td>99.33%</td>
      <td>土耳其</td>
      <td>亚洲</td>
    </tr>
  </tbody>
</table>
</div>




```python
condition_list = ['Confirmed','Active','Newcases','Deaths','Recovered']
#world_pandemic.applymap(lambda x:format(int(x),','))
#apply(lambda x: format(x,'.2%'))
world_pandemic = pandemic.groupby('Continents')[condition_list].sum() #.sort_values(by='confirm', ascending = False)
world_pandemic.loc['World cases'] = world_pandemic.apply(lambda x: x.sum())

world_pandemic['Deaths ratio'] = (world_pandemic['Deaths'] / world_pandemic['Confirmed']).apply(lambda x: format(x,'.2%'))
world_pandemic['Recovered ratio'] = (world_pandemic['Recovered'] / world_pandemic['Confirmed']).apply(lambda x: format(x,'.2%'))
continents_df = world_pandemic.reset_index()
continents_df
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
      <th>Continents</th>
      <th>Confirmed</th>
      <th>Active</th>
      <th>Newcases</th>
      <th>Deaths</th>
      <th>Recovered</th>
      <th>Deaths ratio</th>
      <th>Recovered ratio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Africa</td>
      <td>11451468</td>
      <td>1524387</td>
      <td>2913</td>
      <td>250799</td>
      <td>9676282</td>
      <td>2.19%</td>
      <td>84.50%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asia</td>
      <td>149482939</td>
      <td>5344921</td>
      <td>108824</td>
      <td>1402241</td>
      <td>142735777</td>
      <td>0.94%</td>
      <td>95.49%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Europe</td>
      <td>195890457</td>
      <td>19257737</td>
      <td>46550</td>
      <td>1816853</td>
      <td>174815867</td>
      <td>0.93%</td>
      <td>89.24%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>North America</td>
      <td>101463135</td>
      <td>4029189</td>
      <td>27769</td>
      <td>1469383</td>
      <td>95964563</td>
      <td>1.45%</td>
      <td>94.58%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Oceania</td>
      <td>8756936</td>
      <td>295360</td>
      <td>26380</td>
      <td>11055</td>
      <td>8450521</td>
      <td>0.13%</td>
      <td>96.50%</td>
    </tr>
    <tr>
      <th>5</th>
      <td>South America</td>
      <td>57946209</td>
      <td>5194407</td>
      <td>10956</td>
      <td>1299494</td>
      <td>51452308</td>
      <td>2.24%</td>
      <td>88.79%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>World cases</td>
      <td>524991144</td>
      <td>35646001</td>
      <td>223392</td>
      <td>6249825</td>
      <td>483095318</td>
      <td>1.19%</td>
      <td>92.02%</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = continents_df.transpose().reset_index()
df1 = df.rename(columns=df.iloc[0]).loc[1:]
df_con = df1.rename(columns={'Continents':'Categories'})
df_con[:5]

#df_con.columns

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
      <th>Categories</th>
      <th>Africa</th>
      <th>Asia</th>
      <th>Europe</th>
      <th>North America</th>
      <th>Oceania</th>
      <th>South America</th>
      <th>World cases</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Confirmed</td>
      <td>11451468</td>
      <td>149482939</td>
      <td>195890457</td>
      <td>101463135</td>
      <td>8756936</td>
      <td>57946209</td>
      <td>524991144</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Active</td>
      <td>1524387</td>
      <td>5344921</td>
      <td>19257737</td>
      <td>4029189</td>
      <td>295360</td>
      <td>5194407</td>
      <td>35646001</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Newcases</td>
      <td>2913</td>
      <td>108824</td>
      <td>46550</td>
      <td>27769</td>
      <td>26380</td>
      <td>10956</td>
      <td>223392</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Deaths</td>
      <td>250799</td>
      <td>1402241</td>
      <td>1816853</td>
      <td>1469383</td>
      <td>11055</td>
      <td>1299494</td>
      <td>6249825</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Recovered</td>
      <td>9676282</td>
      <td>142735777</td>
      <td>174815867</td>
      <td>95964563</td>
      <td>8450521</td>
      <td>51452308</td>
      <td>483095318</td>
    </tr>
  </tbody>
</table>
</div>




```python
continents = continents_df[:6]

fig = px.scatter(continents.sort_values(by='Confirmed', ascending = False), x='Continents', y='Confirmed', color='Confirmed', text='Confirmed',
           color_continuous_scale='rainbow',size='Confirmed', size_max=80, hover_data=['Confirmed', 'Continents'],
                labels={'Confirmed':"Total Confirmed Cases"})

fig.update_traces(textposition='top center', textfont_size=14, textfont_family="Arial Black", texttemplate='%{text:,.2s}')
fig = fig.update_layout(height=600, title="Total confirmed cases in Covid-19 of affected continents")
fig.show()

```


<div>                            <div id="7e07dabc-aa40-40af-b189-39efe6ed9b33" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("7e07dabc-aa40-40af-b189-39efe6ed9b33")) {                    Plotly.newPlot(                        "7e07dabc-aa40-40af-b189-39efe6ed9b33",                        [{"hovertemplate":"Continents=%{x}<br>Total Confirmed Cases=%{marker.color}<extra></extra>","legendgroup":"","marker":{"color":[195890457,149482939,101463135,57946209,11451468,8756936],"coloraxis":"coloraxis","size":[195890457,149482939,101463135,57946209,11451468,8756936],"sizemode":"area","sizeref":30607.88390625,"symbol":"circle"},"mode":"markers+text","name":"","orientation":"v","showlegend":false,"text":[195890457.0,149482939.0,101463135.0,57946209.0,11451468.0,8756936.0],"x":["Europe","Asia","North America","South America","Africa","Oceania"],"xaxis":"x","y":[195890457,149482939,101463135,57946209,11451468,8756936],"yaxis":"y","type":"scatter","textfont":{"family":"Arial Black","size":14},"textposition":"top center","texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Continents"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Total Confirmed Cases"}},"coloraxis":{"colorbar":{"title":{"text":"Total Confirmed Cases"}},"colorscale":[[0.0,"rgb(150,0,90)"],[0.125,"rgb(0,0,200)"],[0.25,"rgb(0,25,255)"],[0.375,"rgb(0,152,255)"],[0.5,"rgb(44,255,150)"],[0.625,"rgb(151,255,0)"],[0.75,"rgb(255,234,0)"],[0.875,"rgb(255,111,0)"],[1.0,"rgb(255,0,0)"]]},"legend":{"tracegroupgap":0,"itemsizing":"constant"},"margin":{"t":60},"height":600,"title":{"text":"Total confirmed cases in Covid-19 of affected continents"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('7e07dabc-aa40-40af-b189-39efe6ed9b33');
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

                        })                };                });            </script>        </div>


# The scatter above illustrates the numbers of confirmed cases of Covid-19 among 7 continents in the world. The scatter highlights that Europe is the worst affected continent compare to others as it reach to total 200 million confirmed cases. Asia is the second worst of affected continents it has 150 million confirmed cases; North Ameirica rank to the thrid and the fourth goes to South America followed by Africa and Oceania.
# As of May 2022, Over 520 million confirmed cases have been reported globally. This is not a bright sign as it can tell the pandemic has impacted almost every corner of life.This may probably due to an opportunity to stop the spread of the virus during its early stages was missed and it caused serious consequences for many countries such as causing economies to stall, stretching healthcare systems to the limit and governments have been forced to implement harsh restrictions on human activity to control the spread of the virus.



```python
colors = {'Recovered':'lightskyblue','Active':'darkorange', 'Deaths':'grey', 'Newcases':'midnightblue'}
labels = df_con[1:5]['Categories'].values

fig = px.pie(df_con[1:5], values='World cases', names=labels, color='Categories', 
             title='The Ratio of Situation of Covid-19', color_discrete_map= colors)

fig.update_traces(textposition='auto', textfont_color="black", textinfo='percent+label', insidetextorientation='radial',
                 textfont_size=14, textfont_family="Arial Black")

fig.update_layout(legend_title="Categories")

fig.show()

```


<div>                            <div id="ed6ba666-2a1c-4e26-b39a-8f15a32695a0" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("ed6ba666-2a1c-4e26-b39a-8f15a32695a0")) {                    Plotly.newPlot(                        "ed6ba666-2a1c-4e26-b39a-8f15a32695a0",                        [{"customdata":[["Active"],["Newcases"],["Deaths"],["Recovered"]],"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"hovertemplate":"label=%{label}<br>World cases=%{value}<br>Categories=%{customdata[0]}<extra></extra>","labels":["Active","Newcases","Deaths","Recovered"],"legendgroup":"","marker":{"colors":["darkorange","midnightblue","grey","lightskyblue"]},"name":"","showlegend":true,"values":[35646001,223392,6249825,483095318],"type":"pie","textfont":{"color":"black","family":"Arial Black","size":14},"insidetextorientation":"radial","textinfo":"percent+label","textposition":"auto"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"legend":{"tracegroupgap":0,"title":{"text":"Categories"}},"title":{"text":"The Ratio of Situation of Covid-19"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('ed6ba666-2a1c-4e26-b39a-8f15a32695a0');
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

                        })                };                });            </script>        </div>



```python
colors = {'Confirmed': 'maroon','Recovered':'lightskyblue', 'Active':'darkorange', 'Deaths':'gray', 'Newcases':'midnightblue'}
Continents = ['Europe', 'Asia', 'North America', 'South America', 'Africa', 'Oceania']
label = {'World cases': "Total No.of Cases"}

fig = px.bar(df_con[:5], x="World cases", y="Categories", orientation='h', labels=label, hover_data=Continents,
             color='Categories', text='World cases', color_discrete_map= colors)

fig.update_traces(textfont_family="Arial Black", textfont_size=14, textfont_color="white", textposition='auto',
                 texttemplate='%{text:,.3s}')

fig = fig.update_layout(template='plotly_dark', height=600, title='The Global Situation of Covid-19',
                       yaxis_categoryorder = 'total ascending')
fig.show()
```


<div>                            <div id="1c47d42a-ed67-4369-986d-2aa2fd410ad4" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("1c47d42a-ed67-4369-986d-2aa2fd410ad4")) {                    Plotly.newPlot(                        "1c47d42a-ed67-4369-986d-2aa2fd410ad4",                        [{"alignmentgroup":"True","customdata":[[195890457,149482939,101463135,57946209,11451468,8756936]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Confirmed","marker":{"color":"maroon","pattern":{"shape":""}},"name":"Confirmed","offsetgroup":"Confirmed","orientation":"h","showlegend":true,"text":[524991144],"textposition":"auto","x":[524991144],"xaxis":"x","y":["Confirmed"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[19257737,5344921,4029189,5194407,1524387,295360]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Active","marker":{"color":"darkorange","pattern":{"shape":""}},"name":"Active","offsetgroup":"Active","orientation":"h","showlegend":true,"text":[35646001],"textposition":"auto","x":[35646001],"xaxis":"x","y":["Active"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[46550,108824,27769,10956,2913,26380]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Newcases","marker":{"color":"midnightblue","pattern":{"shape":""}},"name":"Newcases","offsetgroup":"Newcases","orientation":"h","showlegend":true,"text":[223392],"textposition":"auto","x":[223392],"xaxis":"x","y":["Newcases"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[1816853,1402241,1469383,1299494,250799,11055]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Deaths","marker":{"color":"gray","pattern":{"shape":""}},"name":"Deaths","offsetgroup":"Deaths","orientation":"h","showlegend":true,"text":[6249825],"textposition":"auto","x":[6249825],"xaxis":"x","y":["Deaths"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[174815867,142735777,95964563,51452308,9676282,8450521]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Recovered","marker":{"color":"lightskyblue","pattern":{"shape":""}},"name":"Recovered","offsetgroup":"Recovered","orientation":"h","showlegend":true,"text":[483095318],"textposition":"auto","x":[483095318],"xaxis":"x","y":["Recovered"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Total No.of Cases"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Categories"},"categoryorder":"total ascending","categoryarray":["Recovered","Deaths","Newcases","Active","Confirmed"]},"legend":{"title":{"text":"Categories"},"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":600,"title":{"text":"The Global Situation of Covid-19"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('1c47d42a-ed67-4369-986d-2aa2fd410ad4');
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

                        })                };                });            </script>        </div>


# Two chart above Pie and Bar indicate the situation of Covid-19 throughout the world. The Pie chart is about the ratio of Covid-19 situation whereas the Bar chart is showing the total number of cases. Both charts are divide into 5 categories; confirmed, active, newcases, deaths and recovered cases.
# According to the charts, out of over 525 mil confirmed cases reported globaly, over 480 million recovered cases have been reported which is 92% cases have been recovered from the pandemic.
# 6.8% which is 36 million cases are still active and over 6 million cases which 1.2% people have been reported deaths. It can be seen from information that active and deaths cases reported are less than half numbers compare to recovered cases.
# It is clear that the world are getting recovered from pandemic of Covid-19. This may due to goverments around the world have been made a great efforts to emphasize on pandamic prevention and medical attention.
# Also, many countries and societies are dedicating to furthering the vaccination ever since numerous vaccines begin their roll out in countries across the world.


```python
colors = {'Europe': 'firebrick','Asia':'midnightblue', 'North America':'yellow', 'South America':'seagreen', 'Africa':'plum','Oceania':'darkorange'}
fig = make_subplots(rows=1, cols=2, shared_xaxes=False, shared_yaxes=False, specs=[[{"type": "domain"}, {"type": "domain"}]],)

fig_m = px.treemap(pandemic, path=['Continents','Country','Confirmed'], values='Confirmed', 
                   hover_data=['Active'], color='Continents', color_discrete_map= colors)

fig_s = px.sunburst(pandemic, path=['Continents','Country'], values='Confirmed',
                  color='Continents', hover_data=['Active'], color_discrete_map= colors)

fig.add_trace(fig_m['data'][0], row=1, col=1)
fig.add_trace(fig_s['data'][0], row=1, col=2)

fig = fig.update_layout(height=600, title='The Confirmed cases of Covid-19 Throughout the World')
                       

fig.show()
```


<div>                            <div id="cd4b63d5-66da-4b03-a214-9621029a26e9" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("cd4b63d5-66da-4b03-a214-9621029a26e9")) {                    Plotly.newPlot(                        "cd4b63d5-66da-4b03-a214-9621029a26e9",                        [{"branchvalues":"total","customdata":[[4613,"Africa"],[25547,"Europe"],[1776,"Europe"],[1117,"Asia"],[4201,"Asia"],[79,"Asia"],[47965,"Oceania"],[2525,"Africa"],[73148,"Europe"],[2477,"Asia"],[1014231,"Africa"],[3743,"Europe"],[16128,"South America"],[4491,"North America"],[226,"Africa"],[2272,"Europe"],[53,"Asia"],[385,"Asia"],[7684,"South America"],[1049,"Asia"],[60268,"Africa"],[3130,"Europe"],[25782,"Asia"],[366340,"Asia"],[2844861,"Europe"],[1266,"North America"],[79182,"Asia"],[35907,"Asia"],[641427,"Europe"],[464097,"Europe"],[211991,"Asia"],[244690,"Oceania"],[156448,"Europe"],[7070,"Europe"],[846604,"Europe"],[3433,"Asia"],[4902895,"Europe"],[301922,"Asia"],[452024,"Europe"],[208070,"Europe"],[30551,"Africa"],[36185,"Europe"],[70418,"Europe"],[3372122,"South America"],[4044091,"Europe"],[33202,"Europe"],[423027,"South America"],[175784,"South America"],[2478,"Asia"],[1602,"Asia"],[1116,"Africa"],[1732,"Asia"],[146,"Asia"],[1031,"South America"],[1775,"Asia"],[2800,"North America"],[48850,"Africa"],[2650195,"Asia"],[842551,"South America"],[14537,"Asia"],[16984,"South America"],[975889,"Europe"],[30790,"South America"],[1100584,"Europe"],[1891,"Europe"],[22183,"Asia"],[32250,"Asia"],[1584,"Africa"],[6256,"Europe"],[1177204,"Asia"],[299,"Europe"],[2904439,"North America"],[30,"Asia"],[228,"Africa"],[12033,"Africa"],[64,"Africa"],[484,"Africa"],[11,"Africa"],[9189,"North America"],[1353,"North America"],[370,"Africa"],[914,"South America"],[0,"Asia"],[1671,"Oceania"],[188771,"Europe"],[80601,"Africa"],[209325,"Asia"],[2350,"Africa"],[116,"Africa"],[50,"Africa"],[93,"Africa"],[385,"North America"],[11,"Africa"],[120,"Asia"],[152,"Africa"],[11,"Asia"],[30,"Asia"],[14041,"North America"],[470,"Oceania"],[8058,"Africa"],[15,"Africa"],[57432,"Africa"],[327,"North America"],[1283,"Africa"],[3612,"Africa"],[42091,"Africa"],[61,"Africa"],[1266,"North America"],[2940,"Africa"],[72020,"South America"],[7557,"Africa"],[7,"Africa"],[70,"Africa"],[70,"Africa"],[282141,"North America"],[11316,"Africa"],[545,"Oceania"],[29687,"South America"],[9692,"Asia"],[566,"Asia"],[1057,"North America"],[2110,"Asia"],[7190,"Asia"],[1,"Asia"],[83199,"Africa"],[27709,"North America"],[35698,"North America"],[2284,"Africa"],[110,"Asia"],[393576,"North America"],[205685,"South America"],[16189,"Asia"],[32,"Africa"],[33840,"Europe"],[708,"Europe"],[0,"Africa"],[34514,"Africa"],[1027,"Asia"],[2685,"Africa"],[7170,"Europe"],[544,"Asia"],[498,"Europe"],[54931,"Europe"],[47630,"North America"],[301625,"North America"],[196,"North America"],[17282,"Europe"],[42771,"Europe"],[2526,"Asia"],[145,"Africa"],[556764,"Europe"],[37,"Africa"],[4625,"Europe"],[1431627,"Europe"],[1596,"Asia"],[266,"Africa"],[16823,"Europe"],[3844,"Africa"],[154450,"Asia"],[157,"Asia"],[1227,"Asia"],[42,"Africa"],[1415,"Africa"],[839,"Africa"],[46,"Africa"],[19,"Oceania"],[9692,"Asia"],[299,"Europe"],[80601,"Africa"],[145,"Africa"],[205685,"South America"],[1732,"Asia"],[244690,"Oceania"],[36185,"Europe"],[53,"Asia"],[1266,"North America"],[975889,"Europe"],[70418,"Europe"],[1353,"North America"],[1283,"Africa"],[385,"North America"],[11,"Asia"],[16128,"South America"],[1584,"Africa"],[423027,"South America"],[2110,"Asia"],[73148,"Europe"],[32,"Africa"],[42091,"Africa"],[64,"Africa"],[1,"Asia"],[226,"Africa"],[301625,"North America"],[2350,"Africa"],[175784,"South America"],[2650195,"Asia"],[30790,"South America"],[7,"Africa"],[35698,"North America"],[1891,"Europe"],[196,"North America"],[366340,"Asia"],[708,"Europe"],[7070,"Europe"],[15,"Africa"],[2800,"North America"],[30,"Asia"],[842551,"South America"],[48850,"Africa"],[1057,"North America"],[37,"Africa"],[11,"Africa"],[54931,"Europe"],[11316,"Africa"],[1100584,"Europe"],[452024,"Europe"],[72020,"South America"],[61,"Africa"],[46,"Africa"],[1117,"Asia"],[846604,"Europe"],[370,"Africa"],[42771,"Europe"],[9189,"North America"],[4491,"North America"],[42,"Africa"],[70,"Africa"],[70,"Africa"],[914,"South America"],[327,"North America"],[282141,"North America"],[25547,"Europe"],[188771,"Europe"],[25782,"Asia"],[3433,"Asia"],[35907,"Asia"],[1027,"Asia"],[16823,"Europe"],[16189,"Asia"],[641427,"Europe"],[47630,"North America"],[211991,"Asia"],[1227,"Asia"],[79,"Asia"],[1116,"Africa"],[301922,"Asia"],[1049,"Asia"],[1596,"Asia"],[209325,"Asia"],[2272,"Europe"],[2477,"Asia"],[8058,"Africa"],[1415,"Africa"],[4613,"Africa"],[17282,"Europe"],[7170,"Europe"],[3612,"Africa"],[484,"Africa"],[22183,"Asia"],[116,"Africa"],[152,"Africa"],[393576,"North America"],[3130,"Europe"],[154450,"Asia"],[498,"Europe"],[2525,"Africa"],[266,"Africa"],[1602,"Asia"],[2284,"Africa"],[110,"Asia"],[33840,"Europe"],[470,"Oceania"],[47965,"Oceania"],[14041,"North America"],[93,"Africa"],[2940,"Africa"],[1431627,"Europe"],[544,"Asia"],[4201,"Asia"],[146,"Asia"],[27709,"North America"],[19,"Oceania"],[7684,"South America"],[3372122,"South America"],[2478,"Asia"],[556764,"Europe"],[4044091,"Europe"],[1775,"Asia"],[2844861,"Europe"],[208070,"Europe"],[83199,"Africa"],[3844,"Africa"],[7190,"Asia"],[11,"Africa"],[4625,"Europe"],[7557,"Africa"],[79182,"Asia"],[1776,"Europe"],[3743,"Europe"],[1671,"Oceania"],[12033,"Africa"],[30551,"Africa"],[464097,"Europe"],[385,"Asia"],[57432,"Africa"],[29687,"South America"],[228,"Africa"],[6256,"Europe"],[33202,"Europe"],[120,"Asia"],[0,"Asia"],[34514,"Africa"],[32250,"Asia"],[1266,"North America"],[30,"Asia"],[50,"Africa"],[1014231,"Africa"],[2526,"Asia"],[60268,"Africa"],[4902895,"Europe"],[14537,"Asia"],[156448,"Europe"],[2904439,"North America"],[16984,"South America"],[157,"Asia"],[545,"Oceania"],[1031,"South America"],[1177204,"Asia"],[0,"Africa"],[566,"Asia"],[839,"Africa"],[2685,"Africa"],["(?)","Africa"],["(?)","Asia"],["(?)","Europe"],["(?)","North America"],["(?)","Oceania"],["(?)","South America"]],"domain":{"x":[0.0,0.45],"y":[0.0,1.0]},"hovertemplate":"labels=%{label}<br>Confirmed=%{value}<br>parent=%{parent}<br>id=%{id}<br>Active=%{customdata[0]}<br>Continents=%{customdata[1]}<extra></extra>","ids":["Africa/Libya/502016","Europe/Hungary/1919840","Europe/Slovakia/1790228","Asia/Georgia/1655221","Asia/Pakistan/1530703","Asia/Kazakhstan/1305774","Oceania/New Zealand/1196033","Africa/Morocco/1170194","Europe/Bulgaria/1165856","Asia/Lebanon/1099745","Africa/Tunisia/1042872","Europe/Slovenia/1026292","South America/Bolivia/910134","North America/Guatemala/864662","Africa/Cameroon/119947","Europe/Latvia/829143","Asia/Azerbaijan/792785","Asia/Sri Lanka/663869","South America/Paraguay/651268","Asia/Kuwait/634067","Africa/Uganda/164069","Europe/Moldova/518793","Asia/India/43181335","Asia/Cyprus/491777","Europe/Romania/2910553","North America/Bahamas/35070","Asia/Singapore/1318984","Asia/Iran/7232731","Europe/Italy/17505973","Europe/Spain/12403245","Asia/Japan/8929654","Oceania/Australia/7426673","Europe/United Kingdom/22305893","Europe/Denmark/2986308","Europe/Germany/26540852","Asia/Indonesia/6056800","Europe/Ukraine/5011433","Asia/Korea/18163686","Europe/France/29641606","Europe/Russia/18351851","Africa/South Africa/3968205","Europe/Austria/4267154","Europe/Belgium/4158754","South America/Peru/3585381","Europe/Portugal/4066674","Europe/Switzerland/3657546","South America/Brazil/31153765","South America/Chile/3748619","Asia/Philippines/3691545","Asia/Myanmar/613382","Africa/Kenya/325554","Asia/Armenia/422963","Asia/Palestine/582495","South America/Venezuela/523901","Asia/Qatar/369929","North America/Dominican Rep./586926","Africa/Egypt/515645","Asia/China/2962016","South America/Ecuador/878196","Asia/United Arab Emirates/910935","South America/Uruguay/925777","Europe/Belarus/982867","South America/Colombia/6109105","Europe/Finland/1105211","Europe/Croatia/1138046","Asia/Malaysia/4514989","Asia/Thailand/4466793","Africa/Botswana/308126","Europe/Sweden/2509366","Asia/Vietnam/10725239","Europe/Albania/276401","North America/United States/86522561","Asia/Timor-Leste/22925","Africa/Swaziland/72688","Africa/Somalia/26565","Africa/C?te d'Ivoire/82305","Africa/Malawi/86011","Africa/Senegal/86135","North America/Greenland/11971","North America/Belize/59788","Africa/Ghana/161795","South America/Guyana/65272","Asia/Tajikistan/17388","Oceania/Solomon Is./18174","Europe/Iceland/188924","Africa/Algeria/265897","Asia/Lao PDR/210081","Africa/Chad/7417","Africa/Mali/31110","Africa/Togo/37131","Africa/Niger/9031","North America/Bermuda/15085","Africa/Eritrea/9767","Asia/Syria/55880","Africa/Mauritania/59199","Asia/Bhutan/59628","Asia/East Timor/22925","North America/Nicaragua/18491","Oceania/New Caledonia/62193","Africa/Lesotho/32910","Africa/Djibouti/15631","Africa/Sudan/62374","North America/Haiti/30892","Africa/Benin/26952","Africa/Madagascar/64377","Africa/Burundi/42129","Africa/Gabon/47677","North America/The Bahamas/35070","Africa/Nigeria/256148","South America/French Guiana/83671","Africa/Sierra Leone/7682","Africa/Comoros/8100","Africa/Guinea Bissau/8283","Africa/Guinea-Bissau/8283","North America/Honduras/425471","Africa/Ethiopia/475012","Oceania/Vanuatu/9438","South America/Suriname/80547","Asia/Afghanistan/180615","Asia/Yemen/11822","North America/El Salvador/163735","Asia/Brunei/150402","Asia/Saudi Arabia/771302","Asia/Cambodia/136262","Africa/Rwanda/130180","North America/Panama/873794","North America/Costa Rica/904934","Africa/Namibia/167498","Asia/Nepal/979199","North America/Mexico/5789401","South America/Argentina/9230573","Asia/Israel/4151986","Africa/Burkina Faso/20853","Europe/Netherlands/8090237","Europe/Czech Rep./3921096","Africa/W. Sahara/10","Africa/Tanzania/35354","Asia/Iraq/2328670","Africa/Zimbabwe/253397","Europe/Luxembourg/244182","Asia/Oman/389473","Europe/Montenegro/237534","Europe/Estonia/576870","North America/Jamaica/138110","North America/Canada/3881713","North America/Cuba/1105461","Europe/Lithuania/1063099","Europe/Greece/3470640","Asia/Turkey/15072747","Africa/Angola/99194","Europe/Poland/6008612","Africa/Eq. Guinea/15924","Europe/Serbia/2018609","Europe/Norway/1434799","Asia/Kyrgyzstan/200993","Africa/Mozambique/225933","Europe/Ireland/1565970","Africa/S. Sudan/17626","Asia/Mongolia/469885","Asia/Uzbekistan/239123","Asia/Jordan/1694216","Africa/Guinea/36597","Africa/Liberia/7456","Africa/Zambia/322207","Africa/Gambia/12002","Oceania/Papua New Guinea/44425","Asia/Afghanistan","Europe/Albania","Africa/Algeria","Africa/Angola","South America/Argentina","Asia/Armenia","Oceania/Australia","Europe/Austria","Asia/Azerbaijan","North America/Bahamas","Europe/Belarus","Europe/Belgium","North America/Belize","Africa/Benin","North America/Bermuda","Asia/Bhutan","South America/Bolivia","Africa/Botswana","South America/Brazil","Asia/Brunei","Europe/Bulgaria","Africa/Burkina Faso","Africa/Burundi","Africa/C?te d'Ivoire","Asia/Cambodia","Africa/Cameroon","North America/Canada","Africa/Chad","South America/Chile","Asia/China","South America/Colombia","Africa/Comoros","North America/Costa Rica","Europe/Croatia","North America/Cuba","Asia/Cyprus","Europe/Czech Rep.","Europe/Denmark","Africa/Djibouti","North America/Dominican Rep.","Asia/East Timor","South America/Ecuador","Africa/Egypt","North America/El Salvador","Africa/Eq. Guinea","Africa/Eritrea","Europe/Estonia","Africa/Ethiopia","Europe/Finland","Europe/France","South America/French Guiana","Africa/Gabon","Africa/Gambia","Asia/Georgia","Europe/Germany","Africa/Ghana","Europe/Greece","North America/Greenland","North America/Guatemala","Africa/Guinea","Africa/Guinea Bissau","Africa/Guinea-Bissau","South America/Guyana","North America/Haiti","North America/Honduras","Europe/Hungary","Europe/Iceland","Asia/India","Asia/Indonesia","Asia/Iran","Asia/Iraq","Europe/Ireland","Asia/Israel","Europe/Italy","North America/Jamaica","Asia/Japan","Asia/Jordan","Asia/Kazakhstan","Africa/Kenya","Asia/Korea","Asia/Kuwait","Asia/Kyrgyzstan","Asia/Lao PDR","Europe/Latvia","Asia/Lebanon","Africa/Lesotho","Africa/Liberia","Africa/Libya","Europe/Lithuania","Europe/Luxembourg","Africa/Madagascar","Africa/Malawi","Asia/Malaysia","Africa/Mali","Africa/Mauritania","North America/Mexico","Europe/Moldova","Asia/Mongolia","Europe/Montenegro","Africa/Morocco","Africa/Mozambique","Asia/Myanmar","Africa/Namibia","Asia/Nepal","Europe/Netherlands","Oceania/New Caledonia","Oceania/New Zealand","North America/Nicaragua","Africa/Niger","Africa/Nigeria","Europe/Norway","Asia/Oman","Asia/Pakistan","Asia/Palestine","North America/Panama","Oceania/Papua New Guinea","South America/Paraguay","South America/Peru","Asia/Philippines","Europe/Poland","Europe/Portugal","Asia/Qatar","Europe/Romania","Europe/Russia","Africa/Rwanda","Africa/S. Sudan","Asia/Saudi Arabia","Africa/Senegal","Europe/Serbia","Africa/Sierra Leone","Asia/Singapore","Europe/Slovakia","Europe/Slovenia","Oceania/Solomon Is.","Africa/Somalia","Africa/South Africa","Europe/Spain","Asia/Sri Lanka","Africa/Sudan","South America/Suriname","Africa/Swaziland","Europe/Sweden","Europe/Switzerland","Asia/Syria","Asia/Tajikistan","Africa/Tanzania","Asia/Thailand","North America/The Bahamas","Asia/Timor-Leste","Africa/Togo","Africa/Tunisia","Asia/Turkey","Africa/Uganda","Europe/Ukraine","Asia/United Arab Emirates","Europe/United Kingdom","North America/United States","South America/Uruguay","Asia/Uzbekistan","Oceania/Vanuatu","South America/Venezuela","Asia/Vietnam","Africa/W. Sahara","Asia/Yemen","Africa/Zambia","Africa/Zimbabwe","Africa","Asia","Europe","North America","Oceania","South America"],"labels":["502016","1919840","1790228","1655221","1530703","1305774","1196033","1170194","1165856","1099745","1042872","1026292","910134","864662","119947","829143","792785","663869","651268","634067","164069","518793","43181335","491777","2910553","35070","1318984","7232731","17505973","12403245","8929654","7426673","22305893","2986308","26540852","6056800","5011433","18163686","29641606","18351851","3968205","4267154","4158754","3585381","4066674","3657546","31153765","3748619","3691545","613382","325554","422963","582495","523901","369929","586926","515645","2962016","878196","910935","925777","982867","6109105","1105211","1138046","4514989","4466793","308126","2509366","10725239","276401","86522561","22925","72688","26565","82305","86011","86135","11971","59788","161795","65272","17388","18174","188924","265897","210081","7417","31110","37131","9031","15085","9767","55880","59199","59628","22925","18491","62193","32910","15631","62374","30892","26952","64377","42129","47677","35070","256148","83671","7682","8100","8283","8283","425471","475012","9438","80547","180615","11822","163735","150402","771302","136262","130180","873794","904934","167498","979199","5789401","9230573","4151986","20853","8090237","3921096","10","35354","2328670","253397","244182","389473","237534","576870","138110","3881713","1105461","1063099","3470640","15072747","99194","6008612","15924","2018609","1434799","200993","225933","1565970","17626","469885","239123","1694216","36597","7456","322207","12002","44425","Afghanistan","Albania","Algeria","Angola","Argentina","Armenia","Australia","Austria","Azerbaijan","Bahamas","Belarus","Belgium","Belize","Benin","Bermuda","Bhutan","Bolivia","Botswana","Brazil","Brunei","Bulgaria","Burkina Faso","Burundi","C?te d'Ivoire","Cambodia","Cameroon","Canada","Chad","Chile","China","Colombia","Comoros","Costa Rica","Croatia","Cuba","Cyprus","Czech Rep.","Denmark","Djibouti","Dominican Rep.","East Timor","Ecuador","Egypt","El Salvador","Eq. Guinea","Eritrea","Estonia","Ethiopia","Finland","France","French Guiana","Gabon","Gambia","Georgia","Germany","Ghana","Greece","Greenland","Guatemala","Guinea","Guinea Bissau","Guinea-Bissau","Guyana","Haiti","Honduras","Hungary","Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica","Japan","Jordan","Kazakhstan","Kenya","Korea","Kuwait","Kyrgyzstan","Lao PDR","Latvia","Lebanon","Lesotho","Liberia","Libya","Lithuania","Luxembourg","Madagascar","Malawi","Malaysia","Mali","Mauritania","Mexico","Moldova","Mongolia","Montenegro","Morocco","Mozambique","Myanmar","Namibia","Nepal","Netherlands","New Caledonia","New Zealand","Nicaragua","Niger","Nigeria","Norway","Oman","Pakistan","Palestine","Panama","Papua New Guinea","Paraguay","Peru","Philippines","Poland","Portugal","Qatar","Romania","Russia","Rwanda","S. Sudan","Saudi Arabia","Senegal","Serbia","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Is.","Somalia","South Africa","Spain","Sri Lanka","Sudan","Suriname","Swaziland","Sweden","Switzerland","Syria","Tajikistan","Tanzania","Thailand","The Bahamas","Timor-Leste","Togo","Tunisia","Turkey","Uganda","Ukraine","United Arab Emirates","United Kingdom","United States","Uruguay","Uzbekistan","Vanuatu","Venezuela","Vietnam","W. Sahara","Yemen","Zambia","Zimbabwe","Africa","Asia","Europe","North America","Oceania","South America"],"marker":{"colors":["plum","firebrick","firebrick","midnightblue","midnightblue","midnightblue","darkorange","plum","firebrick","midnightblue","plum","firebrick","seagreen","yellow","plum","firebrick","midnightblue","midnightblue","seagreen","midnightblue","plum","firebrick","midnightblue","midnightblue","firebrick","yellow","midnightblue","midnightblue","firebrick","firebrick","midnightblue","darkorange","firebrick","firebrick","firebrick","midnightblue","firebrick","midnightblue","firebrick","firebrick","plum","firebrick","firebrick","seagreen","firebrick","firebrick","seagreen","seagreen","midnightblue","midnightblue","plum","midnightblue","midnightblue","seagreen","midnightblue","yellow","plum","midnightblue","seagreen","midnightblue","seagreen","firebrick","seagreen","firebrick","firebrick","midnightblue","midnightblue","plum","firebrick","midnightblue","firebrick","yellow","midnightblue","plum","plum","plum","plum","plum","yellow","yellow","plum","seagreen","midnightblue","darkorange","firebrick","plum","midnightblue","plum","plum","plum","plum","yellow","plum","midnightblue","plum","midnightblue","midnightblue","yellow","darkorange","plum","plum","plum","yellow","plum","plum","plum","plum","yellow","plum","seagreen","plum","plum","plum","plum","yellow","plum","darkorange","seagreen","midnightblue","midnightblue","yellow","midnightblue","midnightblue","midnightblue","plum","yellow","yellow","plum","midnightblue","yellow","seagreen","midnightblue","plum","firebrick","firebrick","plum","plum","midnightblue","plum","firebrick","midnightblue","firebrick","firebrick","yellow","yellow","yellow","firebrick","firebrick","midnightblue","plum","firebrick","plum","firebrick","firebrick","midnightblue","plum","firebrick","plum","midnightblue","midnightblue","midnightblue","plum","plum","plum","plum","darkorange","midnightblue","firebrick","plum","plum","seagreen","midnightblue","darkorange","firebrick","midnightblue","yellow","firebrick","firebrick","yellow","plum","yellow","midnightblue","seagreen","plum","seagreen","midnightblue","firebrick","plum","plum","plum","midnightblue","plum","yellow","plum","seagreen","midnightblue","seagreen","plum","yellow","firebrick","yellow","midnightblue","firebrick","firebrick","plum","yellow","midnightblue","seagreen","plum","yellow","plum","plum","firebrick","plum","firebrick","firebrick","seagreen","plum","plum","midnightblue","firebrick","plum","firebrick","yellow","yellow","plum","plum","plum","seagreen","yellow","yellow","firebrick","firebrick","midnightblue","midnightblue","midnightblue","midnightblue","firebrick","midnightblue","firebrick","yellow","midnightblue","midnightblue","midnightblue","plum","midnightblue","midnightblue","midnightblue","midnightblue","firebrick","midnightblue","plum","plum","plum","firebrick","firebrick","plum","plum","midnightblue","plum","plum","yellow","firebrick","midnightblue","firebrick","plum","plum","midnightblue","plum","midnightblue","firebrick","darkorange","darkorange","yellow","plum","plum","firebrick","midnightblue","midnightblue","midnightblue","yellow","darkorange","seagreen","seagreen","midnightblue","firebrick","firebrick","midnightblue","firebrick","firebrick","plum","plum","midnightblue","plum","firebrick","plum","midnightblue","firebrick","firebrick","darkorange","plum","plum","firebrick","midnightblue","plum","seagreen","plum","firebrick","firebrick","midnightblue","midnightblue","plum","midnightblue","yellow","midnightblue","plum","plum","midnightblue","plum","firebrick","midnightblue","firebrick","yellow","seagreen","midnightblue","darkorange","seagreen","midnightblue","plum","midnightblue","plum","plum","plum","midnightblue","firebrick","yellow","darkorange","seagreen"]},"name":"","parents":["Africa/Libya","Europe/Hungary","Europe/Slovakia","Asia/Georgia","Asia/Pakistan","Asia/Kazakhstan","Oceania/New Zealand","Africa/Morocco","Europe/Bulgaria","Asia/Lebanon","Africa/Tunisia","Europe/Slovenia","South America/Bolivia","North America/Guatemala","Africa/Cameroon","Europe/Latvia","Asia/Azerbaijan","Asia/Sri Lanka","South America/Paraguay","Asia/Kuwait","Africa/Uganda","Europe/Moldova","Asia/India","Asia/Cyprus","Europe/Romania","North America/Bahamas","Asia/Singapore","Asia/Iran","Europe/Italy","Europe/Spain","Asia/Japan","Oceania/Australia","Europe/United Kingdom","Europe/Denmark","Europe/Germany","Asia/Indonesia","Europe/Ukraine","Asia/Korea","Europe/France","Europe/Russia","Africa/South Africa","Europe/Austria","Europe/Belgium","South America/Peru","Europe/Portugal","Europe/Switzerland","South America/Brazil","South America/Chile","Asia/Philippines","Asia/Myanmar","Africa/Kenya","Asia/Armenia","Asia/Palestine","South America/Venezuela","Asia/Qatar","North America/Dominican Rep.","Africa/Egypt","Asia/China","South America/Ecuador","Asia/United Arab Emirates","South America/Uruguay","Europe/Belarus","South America/Colombia","Europe/Finland","Europe/Croatia","Asia/Malaysia","Asia/Thailand","Africa/Botswana","Europe/Sweden","Asia/Vietnam","Europe/Albania","North America/United States","Asia/Timor-Leste","Africa/Swaziland","Africa/Somalia","Africa/C?te d'Ivoire","Africa/Malawi","Africa/Senegal","North America/Greenland","North America/Belize","Africa/Ghana","South America/Guyana","Asia/Tajikistan","Oceania/Solomon Is.","Europe/Iceland","Africa/Algeria","Asia/Lao PDR","Africa/Chad","Africa/Mali","Africa/Togo","Africa/Niger","North America/Bermuda","Africa/Eritrea","Asia/Syria","Africa/Mauritania","Asia/Bhutan","Asia/East Timor","North America/Nicaragua","Oceania/New Caledonia","Africa/Lesotho","Africa/Djibouti","Africa/Sudan","North America/Haiti","Africa/Benin","Africa/Madagascar","Africa/Burundi","Africa/Gabon","North America/The Bahamas","Africa/Nigeria","South America/French Guiana","Africa/Sierra Leone","Africa/Comoros","Africa/Guinea Bissau","Africa/Guinea-Bissau","North America/Honduras","Africa/Ethiopia","Oceania/Vanuatu","South America/Suriname","Asia/Afghanistan","Asia/Yemen","North America/El Salvador","Asia/Brunei","Asia/Saudi Arabia","Asia/Cambodia","Africa/Rwanda","North America/Panama","North America/Costa Rica","Africa/Namibia","Asia/Nepal","North America/Mexico","South America/Argentina","Asia/Israel","Africa/Burkina Faso","Europe/Netherlands","Europe/Czech Rep.","Africa/W. Sahara","Africa/Tanzania","Asia/Iraq","Africa/Zimbabwe","Europe/Luxembourg","Asia/Oman","Europe/Montenegro","Europe/Estonia","North America/Jamaica","North America/Canada","North America/Cuba","Europe/Lithuania","Europe/Greece","Asia/Turkey","Africa/Angola","Europe/Poland","Africa/Eq. Guinea","Europe/Serbia","Europe/Norway","Asia/Kyrgyzstan","Africa/Mozambique","Europe/Ireland","Africa/S. Sudan","Asia/Mongolia","Asia/Uzbekistan","Asia/Jordan","Africa/Guinea","Africa/Liberia","Africa/Zambia","Africa/Gambia","Oceania/Papua New Guinea","Asia","Europe","Africa","Africa","South America","Asia","Oceania","Europe","Asia","North America","Europe","Europe","North America","Africa","North America","Asia","South America","Africa","South America","Asia","Europe","Africa","Africa","Africa","Asia","Africa","North America","Africa","South America","Asia","South America","Africa","North America","Europe","North America","Asia","Europe","Europe","Africa","North America","Asia","South America","Africa","North America","Africa","Africa","Europe","Africa","Europe","Europe","South America","Africa","Africa","Asia","Europe","Africa","Europe","North America","North America","Africa","Africa","Africa","South America","North America","North America","Europe","Europe","Asia","Asia","Asia","Asia","Europe","Asia","Europe","North America","Asia","Asia","Asia","Africa","Asia","Asia","Asia","Asia","Europe","Asia","Africa","Africa","Africa","Europe","Europe","Africa","Africa","Asia","Africa","Africa","North America","Europe","Asia","Europe","Africa","Africa","Asia","Africa","Asia","Europe","Oceania","Oceania","North America","Africa","Africa","Europe","Asia","Asia","Asia","North America","Oceania","South America","South America","Asia","Europe","Europe","Asia","Europe","Europe","Africa","Africa","Asia","Africa","Europe","Africa","Asia","Europe","Europe","Oceania","Africa","Africa","Europe","Asia","Africa","South America","Africa","Europe","Europe","Asia","Asia","Africa","Asia","North America","Asia","Africa","Africa","Asia","Africa","Europe","Asia","Europe","North America","South America","Asia","Oceania","South America","Asia","Africa","Asia","Africa","Africa","","","","","",""],"values":[502016,1919840,1790228,1655221,1530703,1305774,1196033,1170194,1165856,1099745,1042872,1026292,910134,864662,119947,829143,792785,663869,651268,634067,164069,518793,43181335,491777,2910553,35070,1318984,7232731,17505973,12403245,8929654,7426673,22305893,2986308,26540852,6056800,5011433,18163686,29641606,18351851,3968205,4267154,4158754,3585381,4066674,3657546,31153765,3748619,3691545,613382,325554,422963,582495,523901,369929,586926,515645,2962016,878196,910935,925777,982867,6109105,1105211,1138046,4514989,4466793,308126,2509366,10725239,276401,86522561,22925,72688,26565,82305,86011,86135,11971,59788,161795,65272,17388,18174,188924,265897,210081,7417,31110,37131,9031,15085,9767,55880,59199,59628,22925,18491,62193,32910,15631,62374,30892,26952,64377,42129,47677,35070,256148,83671,7682,8100,8283,8283,425471,475012,9438,80547,180615,11822,163735,150402,771302,136262,130180,873794,904934,167498,979199,5789401,9230573,4151986,20853,8090237,3921096,10,35354,2328670,253397,244182,389473,237534,576870,138110,3881713,1105461,1063099,3470640,15072747,99194,6008612,15924,2018609,1434799,200993,225933,1565970,17626,469885,239123,1694216,36597,7456,322207,12002,44425,180615,276401,265897,99194,9230573,422963,7426673,4267154,792785,35070,982867,4158754,59788,26952,15085,59628,910134,308126,31153765,150402,1165856,20853,42129,82305,136262,119947,3881713,7417,3748619,2962016,6109105,8100,904934,1138046,1105461,491777,3921096,2986308,15631,586926,22925,878196,515645,163735,15924,9767,576870,475012,1105211,29641606,83671,47677,12002,1655221,26540852,161795,3470640,11971,864662,36597,8283,8283,65272,30892,425471,1919840,188924,43181335,6056800,7232731,2328670,1565970,4151986,17505973,138110,8929654,1694216,1305774,325554,18163686,634067,200993,210081,829143,1099745,32910,7456,502016,1063099,244182,64377,86011,4514989,31110,59199,5789401,518793,469885,237534,1170194,225933,613382,167498,979199,8090237,62193,1196033,18491,9031,256148,1434799,389473,1530703,582495,873794,44425,651268,3585381,3691545,6008612,4066674,369929,2910553,18351851,130180,17626,771302,86135,2018609,7682,1318984,1790228,1026292,18174,26565,3968205,12403245,663869,62374,80547,72688,2509366,3657546,55880,17388,35354,4466793,35070,22925,37131,1042872,15072747,164069,5011433,910935,22305893,86522561,925777,239123,9438,523901,10725239,10,11822,322207,253397,11451468,149482939,195890457,101463135,8756936,57946209],"type":"treemap"},{"branchvalues":"total","customdata":[[209325,"Asia"],[208070,"Europe"],[2844861,"Europe"],[4044091,"Europe"],[2478,"Asia"],[19,"Oceania"],[27709,"North America"],[146,"Asia"],[4201,"Asia"],[93,"Africa"],[47965,"Oceania"],[470,"Oceania"],[266,"Africa"],[3130,"Europe"],[61,"Africa"],[393576,"North America"],[152,"Africa"],[22183,"Asia"],[484,"Africa"],[3612,"Africa"],[4491,"North America"],[2477,"Asia"],[839,"Africa"],[1596,"Asia"],[11,"Africa"],[175784,"South America"],[7684,"South America"],[50,"Africa"],[16984,"South America"],[156448,"Europe"],[60268,"Africa"],[1014231,"Africa"],[1031,"South America"],[7557,"Africa"],[1177204,"Asia"],[1266,"North America"],[0,"Asia"],[157,"Asia"],[0,"Africa"],[545,"Oceania"],[385,"Asia"],[6256,"Europe"],[228,"Africa"],[1776,"Europe"],[57432,"Africa"],[3743,"Europe"],[566,"Asia"],[12033,"Africa"],[1671,"Oceania"],[7170,"Europe"],[47630,"North America"],[79,"Asia"],[4613,"Africa"],[8058,"Africa"],[211991,"Asia"],[17282,"Europe"],[2272,"Europe"],[4625,"Europe"],[498,"Europe"],[1602,"Asia"],[2284,"Africa"],[33840,"Europe"],[30,"Asia"],[2940,"Africa"],[544,"Asia"],[120,"Asia"],[33202,"Europe"],[16189,"Asia"],[7190,"Asia"],[14537,"Asia"],[16823,"Europe"],[2685,"Africa"],[42091,"Africa"],[37,"Africa"],[64,"Africa"],[54931,"Europe"],[1100584,"Europe"],[452024,"Europe"],[70418,"Europe"],[2800,"North America"],[42771,"Europe"],[1057,"North America"],[16128,"South America"],[423027,"South America"],[70,"Africa"],[1027,"Asia"],[327,"North America"],[299,"Europe"],[301625,"North America"],[35698,"North America"],[36185,"Europe"],[1283,"Africa"],[1266,"North America"],[708,"Europe"],[7070,"Europe"],[15,"Africa"],[32,"Africa"],[2110,"Asia"],[30,"Asia"],[2350,"Africa"],[385,"North America"],[842551,"South America"],[226,"Africa"],[1,"Asia"],[48850,"Africa"],[1891,"Europe"],[366340,"Asia"],[2650195,"Asia"],[35907,"Asia"],[11316,"Africa"],[145,"Africa"],[205685,"South America"],[1732,"Asia"],[244690,"Oceania"],[1116,"Africa"],[1049,"Asia"],[53,"Asia"],[11,"Africa"],[70,"Africa"],[975889,"Europe"],[9189,"North America"],[370,"Africa"],[116,"Africa"],[1117,"Asia"],[46,"Africa"],[154450,"Asia"],[2525,"Africa"],[42,"Africa"],[110,"Asia"],[34514,"Africa"],[4902895,"Europe"],[29687,"South America"],[73148,"Europe"],[2526,"Asia"],[464097,"Europe"],[9692,"Asia"],[30790,"South America"],[3844,"Africa"],[3433,"Asia"],[25782,"Asia"],[1227,"Asia"],[25547,"Europe"],[1415,"Africa"],[846604,"Europe"],[30551,"Africa"],[1431627,"Europe"],[14041,"North America"],[79182,"Asia"],[2904439,"North America"],[72020,"South America"],[32250,"Asia"],[11,"Asia"],[83199,"Africa"],[3372122,"South America"],[914,"South America"],[282141,"North America"],[556764,"Europe"],[1584,"Africa"],[301922,"Asia"],[188771,"Europe"],[1775,"Asia"],[7,"Africa"],[80601,"Africa"],[641427,"Europe"],[1353,"North America"],[196,"North America"],["(?)","Africa"],["(?)","Asia"],["(?)","Europe"],["(?)","North America"],["(?)","Oceania"],["(?)","South America"]],"domain":{"x":[0.55,1.0],"y":[0.0,1.0]},"hovertemplate":"labels=%{label}<br>Confirmed=%{value}<br>parent=%{parent}<br>id=%{id}<br>Active=%{customdata[0]}<br>Continents=%{customdata[1]}<extra></extra>","ids":["Asia/Lao PDR","Europe/Russia","Europe/Romania","Europe/Portugal","Asia/Philippines","Oceania/Papua New Guinea","North America/Panama","Asia/Palestine","Asia/Pakistan","Africa/Niger","Oceania/New Zealand","Oceania/New Caledonia","Africa/Mozambique","Europe/Moldova","Africa/Gabon","North America/Mexico","Africa/Mauritania","Asia/Malaysia","Africa/Malawi","Africa/Madagascar","North America/Guatemala","Asia/Lebanon","Africa/Zambia","Asia/Kyrgyzstan","Africa/Senegal","South America/Chile","South America/Paraguay","Africa/Togo","South America/Uruguay","Europe/United Kingdom","Africa/Uganda","Africa/Tunisia","South America/Venezuela","Africa/Sierra Leone","Asia/Vietnam","North America/The Bahamas","Asia/Tajikistan","Asia/Uzbekistan","Africa/W. Sahara","Oceania/Vanuatu","Asia/Sri Lanka","Europe/Sweden","Africa/Swaziland","Europe/Slovakia","Africa/Sudan","Europe/Slovenia","Asia/Yemen","Africa/Somalia","Oceania/Solomon Is.","Europe/Luxembourg","North America/Jamaica","Asia/Kazakhstan","Africa/Libya","Africa/Lesotho","Asia/Japan","Europe/Lithuania","Europe/Latvia","Europe/Serbia","Europe/Montenegro","Asia/Myanmar","Africa/Namibia","Europe/Netherlands","Asia/Timor-Leste","Africa/Nigeria","Asia/Oman","Asia/Syria","Europe/Switzerland","Asia/Israel","Asia/Saudi Arabia","Asia/United Arab Emirates","Europe/Ireland","Africa/Zimbabwe","Africa/Burundi","Africa/Eq. Guinea","Africa/C?te d'Ivoire","Europe/Estonia","Europe/Finland","Europe/France","Europe/Belgium","North America/Dominican Rep.","Europe/Greece","North America/El Salvador","South America/Bolivia","South America/Brazil","Africa/Guinea-Bissau","Asia/Iraq","North America/Haiti","Europe/Albania","North America/Canada","North America/Costa Rica","Europe/Austria","Africa/Benin","North America/Bahamas","Europe/Czech Rep.","Europe/Denmark","Africa/Djibouti","Africa/Burkina Faso","Asia/Brunei","Asia/East Timor","Africa/Chad","North America/Bermuda","South America/Ecuador","Africa/Cameroon","Asia/Cambodia","Africa/Egypt","Europe/Croatia","Asia/Cyprus","Asia/China","Asia/Iran","Africa/Ethiopia","Africa/Angola","South America/Argentina","Asia/Armenia","Oceania/Australia","Africa/Kenya","Asia/Kuwait","Asia/Azerbaijan","Africa/Eritrea","Africa/Guinea Bissau","Europe/Belarus","North America/Greenland","Africa/Ghana","Africa/Mali","Asia/Georgia","Africa/Gambia","Asia/Mongolia","Africa/Morocco","Africa/Guinea","Asia/Nepal","Africa/Tanzania","Europe/Ukraine","South America/Suriname","Europe/Bulgaria","Asia/Turkey","Europe/Spain","Asia/Afghanistan","South America/Colombia","Africa/S. Sudan","Asia/Indonesia","Asia/India","Asia/Jordan","Europe/Hungary","Africa/Liberia","Europe/Germany","Africa/South Africa","Europe/Norway","North America/Nicaragua","Asia/Singapore","North America/United States","South America/French Guiana","Asia/Thailand","Asia/Bhutan","Africa/Rwanda","South America/Peru","South America/Guyana","North America/Honduras","Europe/Poland","Africa/Botswana","Asia/Korea","Europe/Iceland","Asia/Qatar","Africa/Comoros","Africa/Algeria","Europe/Italy","North America/Belize","North America/Cuba","Africa","Asia","Europe","North America","Oceania","South America"],"labels":["Lao PDR","Russia","Romania","Portugal","Philippines","Papua New Guinea","Panama","Palestine","Pakistan","Niger","New Zealand","New Caledonia","Mozambique","Moldova","Gabon","Mexico","Mauritania","Malaysia","Malawi","Madagascar","Guatemala","Lebanon","Zambia","Kyrgyzstan","Senegal","Chile","Paraguay","Togo","Uruguay","United Kingdom","Uganda","Tunisia","Venezuela","Sierra Leone","Vietnam","The Bahamas","Tajikistan","Uzbekistan","W. Sahara","Vanuatu","Sri Lanka","Sweden","Swaziland","Slovakia","Sudan","Slovenia","Yemen","Somalia","Solomon Is.","Luxembourg","Jamaica","Kazakhstan","Libya","Lesotho","Japan","Lithuania","Latvia","Serbia","Montenegro","Myanmar","Namibia","Netherlands","Timor-Leste","Nigeria","Oman","Syria","Switzerland","Israel","Saudi Arabia","United Arab Emirates","Ireland","Zimbabwe","Burundi","Eq. Guinea","C?te d'Ivoire","Estonia","Finland","France","Belgium","Dominican Rep.","Greece","El Salvador","Bolivia","Brazil","Guinea-Bissau","Iraq","Haiti","Albania","Canada","Costa Rica","Austria","Benin","Bahamas","Czech Rep.","Denmark","Djibouti","Burkina Faso","Brunei","East Timor","Chad","Bermuda","Ecuador","Cameroon","Cambodia","Egypt","Croatia","Cyprus","China","Iran","Ethiopia","Angola","Argentina","Armenia","Australia","Kenya","Kuwait","Azerbaijan","Eritrea","Guinea Bissau","Belarus","Greenland","Ghana","Mali","Georgia","Gambia","Mongolia","Morocco","Guinea","Nepal","Tanzania","Ukraine","Suriname","Bulgaria","Turkey","Spain","Afghanistan","Colombia","S. Sudan","Indonesia","India","Jordan","Hungary","Liberia","Germany","South Africa","Norway","Nicaragua","Singapore","United States","French Guiana","Thailand","Bhutan","Rwanda","Peru","Guyana","Honduras","Poland","Botswana","Korea","Iceland","Qatar","Comoros","Algeria","Italy","Belize","Cuba","Africa","Asia","Europe","North America","Oceania","South America"],"marker":{"colors":["midnightblue","firebrick","firebrick","firebrick","midnightblue","darkorange","yellow","midnightblue","midnightblue","plum","darkorange","darkorange","plum","firebrick","plum","yellow","plum","midnightblue","plum","plum","yellow","midnightblue","plum","midnightblue","plum","seagreen","seagreen","plum","seagreen","firebrick","plum","plum","seagreen","plum","midnightblue","yellow","midnightblue","midnightblue","plum","darkorange","midnightblue","firebrick","plum","firebrick","plum","firebrick","midnightblue","plum","darkorange","firebrick","yellow","midnightblue","plum","plum","midnightblue","firebrick","firebrick","firebrick","firebrick","midnightblue","plum","firebrick","midnightblue","plum","midnightblue","midnightblue","firebrick","midnightblue","midnightblue","midnightblue","firebrick","plum","plum","plum","plum","firebrick","firebrick","firebrick","firebrick","yellow","firebrick","yellow","seagreen","seagreen","plum","midnightblue","yellow","firebrick","yellow","yellow","firebrick","plum","yellow","firebrick","firebrick","plum","plum","midnightblue","midnightblue","plum","yellow","seagreen","plum","midnightblue","plum","firebrick","midnightblue","midnightblue","midnightblue","plum","plum","seagreen","midnightblue","darkorange","plum","midnightblue","midnightblue","plum","plum","firebrick","yellow","plum","plum","midnightblue","plum","midnightblue","plum","plum","midnightblue","plum","firebrick","seagreen","firebrick","midnightblue","firebrick","midnightblue","seagreen","plum","midnightblue","midnightblue","midnightblue","firebrick","plum","firebrick","plum","firebrick","yellow","midnightblue","yellow","seagreen","midnightblue","midnightblue","plum","seagreen","seagreen","yellow","firebrick","plum","midnightblue","firebrick","midnightblue","plum","plum","firebrick","yellow","yellow","plum","midnightblue","firebrick","yellow","darkorange","seagreen"]},"name":"","parents":["Asia","Europe","Europe","Europe","Asia","Oceania","North America","Asia","Asia","Africa","Oceania","Oceania","Africa","Europe","Africa","North America","Africa","Asia","Africa","Africa","North America","Asia","Africa","Asia","Africa","South America","South America","Africa","South America","Europe","Africa","Africa","South America","Africa","Asia","North America","Asia","Asia","Africa","Oceania","Asia","Europe","Africa","Europe","Africa","Europe","Asia","Africa","Oceania","Europe","North America","Asia","Africa","Africa","Asia","Europe","Europe","Europe","Europe","Asia","Africa","Europe","Asia","Africa","Asia","Asia","Europe","Asia","Asia","Asia","Europe","Africa","Africa","Africa","Africa","Europe","Europe","Europe","Europe","North America","Europe","North America","South America","South America","Africa","Asia","North America","Europe","North America","North America","Europe","Africa","North America","Europe","Europe","Africa","Africa","Asia","Asia","Africa","North America","South America","Africa","Asia","Africa","Europe","Asia","Asia","Asia","Africa","Africa","South America","Asia","Oceania","Africa","Asia","Asia","Africa","Africa","Europe","North America","Africa","Africa","Asia","Africa","Asia","Africa","Africa","Asia","Africa","Europe","South America","Europe","Asia","Europe","Asia","South America","Africa","Asia","Asia","Asia","Europe","Africa","Europe","Africa","Europe","North America","Asia","North America","South America","Asia","Asia","Africa","South America","South America","North America","Europe","Africa","Asia","Europe","Asia","Africa","Africa","Europe","North America","North America","","","","","",""],"values":[210081,18351851,2910553,4066674,3691545,44425,873794,582495,1530703,9031,1196033,62193,225933,518793,47677,5789401,59199,4514989,86011,64377,864662,1099745,322207,200993,86135,3748619,651268,37131,925777,22305893,164069,1042872,523901,7682,10725239,35070,17388,239123,10,9438,663869,2509366,72688,1790228,62374,1026292,11822,26565,18174,244182,138110,1305774,502016,32910,8929654,1063099,829143,2018609,237534,613382,167498,8090237,22925,256148,389473,55880,3657546,4151986,771302,910935,1565970,253397,42129,15924,82305,576870,1105211,29641606,4158754,586926,3470640,163735,910134,31153765,8283,2328670,30892,276401,3881713,904934,4267154,26952,35070,3921096,2986308,15631,20853,150402,22925,7417,15085,878196,119947,136262,515645,1138046,491777,2962016,7232731,475012,99194,9230573,422963,7426673,325554,634067,792785,9767,8283,982867,11971,161795,31110,1655221,12002,469885,1170194,36597,979199,35354,5011433,80547,1165856,15072747,12403245,180615,6109105,17626,6056800,43181335,1694216,1919840,7456,26540852,3968205,1434799,18491,1318984,86522561,83671,4466793,59628,130180,3585381,65272,425471,6008612,308126,18163686,188924,369929,8100,265897,17505973,59788,1105461,11451468,149482939,195890457,101463135,8756936,57946209],"type":"sunburst"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"height":600,"title":{"text":"The Confirmed cases of Covid-19 Throughout the World"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('cd4b63d5-66da-4b03-a214-9621029a26e9');
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

                        })                };                });            </script>        </div>


# The diagram above describe the confirmed cases of Covid-19 of affected continents and countries around the world.
# It clearly shows that Europe is the worst affected continents and France is the highest confirmed cases of Covid-19 in Europe which has 29 million follow by Germany and UK both countries have 26 million and 22 million total confrimed cases respectively. 
# Asia is the second worst affected continent and India is the top higher confirmed cases which is 43 million cases and Korea is the second higher which is 17 million confirmed cases.
# The thrid goes to North America follow by South America and United States is the top affected country it has total 85 million confirmed cases, and Brazil has 31 million confirmed cases the highest affected country in South America.


```python
continents = continents_df[:6]['Continents']

fig = make_subplots(rows=2, cols=2, subplot_titles=('Confirmed cases','Active cases','Death cases','Recovered Cases',))

fig.add_trace(go.Bar(name='Confirmed cases', x=continents, y=continents_df['Confirmed'], 
                     marker=dict(color="maroon"), text=continents_df['Confirmed']), 1, 1)

fig.add_trace(go.Bar(name='Active cases', x=continents, y=continents_df['Active'], 
                     marker=dict(color="darkorange"), text=continents_df['Active']), 1, 2)


fig.add_trace(go.Bar(name='Death cases', x=continents, y=continents_df['Deaths'], 
                     marker=dict(color="gray"), text=continents_df['Deaths']), 2, 1)

fig.add_trace(go.Bar(name='Recovered cases', x=continents, y=continents_df['Recovered'],
                     marker=dict(color="lightskyblue"),text=continents_df['Recovered']), 2, 2)

fig.update_traces(textposition='auto', textfont_size=14, textfont_family="Arial Black", textfont_color="white", 
                  texttemplate='%{text:,.2s}')

fig.update_layout(template='plotly_dark', height=980, showlegend=False, title='The Situation of Covid-19 by Continents')


fig.update_xaxes(row=1, col=1, title='Continents')
fig.update_xaxes(row=1, col=2, title='Continents')
fig.update_xaxes(row=2, col=1, title='Total Cases')
fig.update_xaxes(row=2, col=2, title='Total Cases')

fig.update_yaxes(row=1, col=1, title='Total Cases')
fig.update_yaxes(row=1, col=2, title='Total Cases')
fig.update_yaxes(row=2, col=1, title='Continents')
fig.update_yaxes(row=2, col=2, title='Continents')

fig.show()

```


<div>                            <div id="57637176-d53a-4a6a-a9ba-e219bf4ea850" class="plotly-graph-div" style="height:980px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("57637176-d53a-4a6a-a9ba-e219bf4ea850")) {                    Plotly.newPlot(                        "57637176-d53a-4a6a-a9ba-e219bf4ea850",                        [{"marker":{"color":"maroon"},"name":"Confirmed cases","text":[11451468.0,149482939.0,195890457.0,101463135.0,8756936.0,57946209.0,524991144.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[11451468,149482939,195890457,101463135,8756936,57946209,524991144],"type":"bar","xaxis":"x","yaxis":"y","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"darkorange"},"name":"Active cases","text":[1524387.0,5344921.0,19257737.0,4029189.0,295360.0,5194407.0,35646001.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[1524387,5344921,19257737,4029189,295360,5194407,35646001],"type":"bar","xaxis":"x2","yaxis":"y2","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"gray"},"name":"Death cases","text":[250799.0,1402241.0,1816853.0,1469383.0,11055.0,1299494.0,6249825.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[250799,1402241,1816853,1469383,11055,1299494,6249825],"type":"bar","xaxis":"x3","yaxis":"y3","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"lightskyblue"},"name":"Recovered cases","text":[9676282.0,142735777.0,174815867.0,95964563.0,8450521.0,51452308.0,483095318.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[9676282,142735777,174815867,95964563,8450521,51452308,483095318],"type":"bar","xaxis":"x4","yaxis":"y4","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,0.45],"title":{"text":"Continents"}},"yaxis":{"anchor":"x","domain":[0.625,1.0],"title":{"text":"Total Cases"}},"xaxis2":{"anchor":"y2","domain":[0.55,1.0],"title":{"text":"Continents"}},"yaxis2":{"anchor":"x2","domain":[0.625,1.0],"title":{"text":"Total Cases"}},"xaxis3":{"anchor":"y3","domain":[0.0,0.45],"title":{"text":"Total Cases"}},"yaxis3":{"anchor":"x3","domain":[0.0,0.375],"title":{"text":"Continents"}},"xaxis4":{"anchor":"y4","domain":[0.55,1.0],"title":{"text":"Total Cases"}},"yaxis4":{"anchor":"x4","domain":[0.0,0.375],"title":{"text":"Continents"}},"annotations":[{"font":{"size":16},"showarrow":false,"text":"Confirmed cases","x":0.225,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Active cases","x":0.775,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Death cases","x":0.225,"xanchor":"center","xref":"paper","y":0.375,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Recovered Cases","x":0.775,"xanchor":"center","xref":"paper","y":0.375,"yanchor":"bottom","yref":"paper"}],"height":980,"showlegend":false,"title":{"text":"The Situation of Covid-19 by Continents"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('57637176-d53a-4a6a-a9ba-e219bf4ea850');
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

                        })                };                });            </script>        </div>


# The 4 bar charts above tell the situation of Covid-19 among continents with divided into 4 conditions, they are total number of confirmed cases, active cases, death cases and recovered cases.
# Although Europe is the worst affected continents, but it also the higher recovered continent of all as it can be seen in the chart of recovered cases.
# Asian countries considered more successful in curbing the virus spread, it can tells from the charts above that although Asia is the second higher of confirmed cases, however Asia also the second higher recovered continent and the numbers of active and deaths cases are less than Europe and North America not to mention that Asia is the largest population in the world. 
# The pandemic prevention measures in North America and South America are relatively unfavorable compare to Asia, this may due to many countires are easing their restriction precaution to curb the spread of virus.


```python
from plotly.subplots import make_subplots

grey, burgundy, peach, blue = '#393e46', '#8C001A', '#EAC117', '#99a9ff'

fig_c = px.bar(pandemic.sort_values(by='Confirmed', ascending = False).head(10), x='Confirmed', y='Country',
             labels={'Confirmed': "Confirmed Cases"}, text='Confirmed', hover_data=['Country','Continents'],
               color_discrete_sequence=[burgundy])

fig_a = px.bar(pandemic.sort_values(by='Active', ascending = False).head(10), x='Active', y='Country',
             labels={'Active': "Active Cases"}, text='Active', hover_data=['Country','Continents'],color_discrete_sequence=[peach])

fig_d = px.bar(pandemic.sort_values(by='Deaths', ascending = False).head(10), x='Deaths', y='Country',
             labels={'Deaths': "Deaths Cases"}, text='Deaths', hover_data=['Country','Continents'], color_discrete_sequence=[grey])

fig_r = px.bar(pandemic.sort_values(by='Recovered', ascending = False).head(10), x='Recovered', y='Country',
             labels={'Recovered': "Recovered Cases"}, text='Recovered', hover_data=['Country','Continents'], color_discrete_sequence=[blue])

fig = make_subplots(rows=2, cols=2, shared_xaxes=False, horizontal_spacing=0.14, vertical_spacing=0.1, 
                    subplot_titles=('Confirmed Cases','Active Cases','Deaths cases','Recovered Cases'))

fig.add_trace(fig_c['data'][0], row=1, col=1)
fig.add_trace(fig_a['data'][0], row=1, col=2)
fig.add_trace(fig_d['data'][0], row=2, col=1)
fig.add_trace(fig_r['data'][0], row=2, col=2)



fig.update_traces(textposition='auto', textfont_size=14, textfont_family="Arial", textfont_color="white", 
                  texttemplate='%{text:,.2s}')

fig.update_yaxes(row=1, col=1, autorange='reversed')
fig.update_yaxes(row=1, col=2, autorange='reversed')
fig.update_yaxes(row=2, col=1, autorange='reversed')
fig.update_yaxes(row=2, col=2, autorange='reversed')

fig.update_layout(template='plotly_dark', height=980, width=1000, title='The Situation of Covid-19 by Top 10 Countries') 
# yaxis_categoryorder = 'total ascending'

fig.show()
```


<div>                            <div id="08c12db8-db9f-4282-b421-0cdaf7d49a83" class="plotly-graph-div" style="height:980px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("08c12db8-db9f-4282-b421-0cdaf7d49a83")) {                    Plotly.newPlot(                        "08c12db8-db9f-4282-b421-0cdaf7d49a83",                        [{"alignmentgroup":"True","customdata":[["North America"],["Asia"],["South America"],["Europe"],["Europe"],["Europe"],["Europe"],["Asia"],["Europe"],["Asia"]],"hovertemplate":"Confirmed Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#8C001A","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[86522561.0,43181335.0,31153765.0,29641606.0,26540852.0,22305893.0,18351851.0,18163686.0,17505973.0,15072747.0],"textposition":"auto","x":[86522561,43181335,31153765,29641606,26540852,22305893,18351851,18163686,17505973,15072747],"xaxis":"x","y":["United States","India","Brazil","France","Germany","United Kingdom","Russia","Korea","Italy","Turkey"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["Europe"],["Europe"],["South America"],["North America"],["Europe"],["Asia"],["Europe"],["Asia"],["Europe"],["Africa"]],"hovertemplate":"Active Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#EAC117","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[4902895.0,4044091.0,3372122.0,2904439.0,2844861.0,2650195.0,1431627.0,1177204.0,1100584.0,1014231.0],"textposition":"auto","x":[4902895,4044091,3372122,2904439,2844861,2650195,1431627,1177204,1100584,1014231],"xaxis":"x2","y":["Ukraine","Portugal","Peru","United States","Romania","China","Norway","Vietnam","Finland","Tunisia"],"yaxis":"y2","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["North America"],["South America"],["Asia"],["Europe"],["North America"],["South America"],["Europe"],["Europe"],["Asia"],["Europe"]],"hovertemplate":"Deaths Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#393e46","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[1033591.0,667056.0,524701.0,379520.0,325000.0,213259.0,178749.0,166949.0,156615.0,148464.0],"textposition":"auto","x":[1033591,667056,524701,379520,325000,213259,178749,166949,156615,148464],"xaxis":"x3","y":["United States","Brazil","India","Russia","Mexico","Peru","United Kingdom","Italy","Indonesia","France"],"yaxis":"y3","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["North America"],["Asia"],["South America"],["Europe"],["Europe"],["Europe"],["Asia"],["Europe"],["Europe"],["Asia"]],"hovertemplate":"Recovered Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#99a9ff","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[82584531.0,42630852.0,30063682.0,29041118.0,25554500.0,21970696.0,17837506.0,17764261.0,16697597.0,14971256.0],"textposition":"auto","x":[82584531,42630852,30063682,29041118,25554500,21970696,17837506,17764261,16697597,14971256],"xaxis":"x4","y":["United States","India","Brazil","France","Germany","United Kingdom","Korea","Russia","Italy","Turkey"],"yaxis":"y4","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,0.43]},"yaxis":{"anchor":"x","domain":[0.55,1.0],"autorange":"reversed"},"xaxis2":{"anchor":"y2","domain":[0.5700000000000001,1.0]},"yaxis2":{"anchor":"x2","domain":[0.55,1.0],"autorange":"reversed"},"xaxis3":{"anchor":"y3","domain":[0.0,0.43]},"yaxis3":{"anchor":"x3","domain":[0.0,0.45],"autorange":"reversed"},"xaxis4":{"anchor":"y4","domain":[0.5700000000000001,1.0]},"yaxis4":{"anchor":"x4","domain":[0.0,0.45],"autorange":"reversed"},"annotations":[{"font":{"size":16},"showarrow":false,"text":"Confirmed Cases","x":0.215,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Active Cases","x":0.785,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Deaths cases","x":0.215,"xanchor":"center","xref":"paper","y":0.45,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Recovered Cases","x":0.785,"xanchor":"center","xref":"paper","y":0.45,"yanchor":"bottom","yref":"paper"}],"height":980,"width":1000,"title":{"text":"The Situation of Covid-19 by Top 10 Countries"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('08c12db8-db9f-4282-b421-0cdaf7d49a83');
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

                        })                };                });            </script>        </div>


# The diagram above give information of the top 10 affected countries in divided into 4 conditions of confirmed, active, death and recoverd cases in Covid-19 pandemic.
# According to charts above, United State is the top 1 country of confirmed cases in the world  which has 87 million cases; 1 million of death cases and 83 million of recovered cases whereas 2.9 million active cases in top 5. 
# Ukraine has climb up to 4.9 million active cases the highest of all country recently, this may due to people living in Ukraine are suffering from depression, food and financial insecurity since Russia started its war with Ukraine at the end of February. People who are concerned about residence and food security or household finances may not be able to take the same pandemic precautions as those with stable condition and good financial security.These concerns can obstruct efforts to stop the spread of  Covid-19. 



```python
fig = px.choropleth(pandemic, locations="Country", locationmode= "country names",
                    color="Confirmed", title="Covid-19 Situation In The World",
                    hover_data=["Country","Continents","Recovered","Deaths","Active"], 
                    color_continuous_scale=px.colors.sequential.YlOrRd)
fig.show()
```


<div>                            <div id="c9999f49-70ce-4594-a402-e9cdc6e77cc2" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("c9999f49-70ce-4594-a402-e9cdc6e77cc2")) {                    Plotly.newPlot(                        "c9999f49-70ce-4594-a402-e9cdc6e77cc2",                        [{"coloraxis":"coloraxis","customdata":[["United States","North America",82584531,1033591,2904439],["India","Asia",42630852,524701,25782],["Brazil","South America",30063682,667056,423027],["France","Europe",29041118,148464,452024],["Germany","Europe",25554500,139748,846604],["United Kingdom","Europe",21970696,178749,156448],["Russia","Europe",17764261,379520,208070],["Korea","Asia",17837506,24258,301922],["Italy","Europe",16697597,166949,641427],["Turkey","Asia",14971256,98965,2526],["Spain","Europe",11832351,106797,464097],["Vietnam","Asia",9504955,43080,1177204],["Argentina","South America",8895999,128889,205685],["Japan","Asia",8686928,30735,211991],["Netherlands","Europe",8034072,22325,33840],["Australia","Oceania",7173231,8752,244690],["Iran","Asia",7055493,141331,35907],["Colombia","South America",5938448,139867,30790],["Indonesia","Asia",5896752,156615,3433],["Poland","Europe",5335499,116349,556764],["Mexico","North America",5070825,325000,393576],["Ukraine","Europe",0,108538,4902895],["Malaysia","Asia",4457118,35688,22183],["Thailand","Asia",4404369,30174,32250],["Austria","Europe",4212299,18670,36185],["Belgium","Europe",4056568,31768,70418],["Israel","Asia",4124933,10864,16189],["Portugal","Europe",0,22583,4044091],["South Africa","Africa",3836337,101317,30551],["Czech Rep.","Europe",3880095,40293,708],["Canada","North America",3538840,41248,301625],["Chile","South America",3514862,57973,175784],["Philippines","Asia",3628611,60456,2478],["Switzerland","Europe",3610386,13958,33202],["Peru","South America",0,213259,3372122],["Greece","Europe",3397956,29913,42771],["Denmark","Europe",2972862,6376,7070],["Romania","Europe",0,65692,2844861],["Sweden","Europe",2484129,18981,6256],["Iraq","Asia",2302422,25221,1027],["Serbia","Europe",1997896,16088,4625],["Hungary","Europe",1847746,46547,25547],["Slovakia","Europe",1768348,20104,1776],["Jordan","Asia",1678941,14048,1227],["Georgia","Asia",1637293,16811,1117],["Ireland","Europe",1541800,7347,16823],["Pakistan","Asia",1496123,30379,4201],["Norway","Europe",0,3172,1431627],["Singapore","Asia",1238409,1393,79182],["Kazakhstan","Asia",1292033,13662,79],["New Zealand","Oceania",1146889,1179,47965],["Morocco","Africa",1151589,16080,2525],["Bulgaria","Europe",1055545,37163,73148],["Croatia","Europe",1120157,15998,1891],["Cuba","North America",1096736,8529,196],["Finland","Europe",0,4627,1100584],["Lebanon","Asia",1086833,10435,2477],["Lithuania","Europe",1036669,9148,17282],["Tunisia","Africa",0,28641,1014231],["Slovenia","Europe",1015907,6642,3743],["Belarus","Europe",0,6978,975889],["Nepal","Asia",967137,11952,110],["Uruguay","South America",901555,7238,16984],["United Arab Emirates","Asia",894093,2305,14537],["Bolivia","South America",872057,21949,16128],["Costa Rica","North America",860711,8525,35698],["Ecuador","South America",0,35645,842551],["Panama","North America",837810,8275,27709],["Guatemala","North America",841952,18219,4491],["Latvia","Europe",821043,5828,2272],["Azerbaijan","Asia",783019,9713,53],["Saudi Arabia","Asia",754956,9156,7190],["Sri Lanka","Asia",646966,16518,385],["Paraguay","South America",624673,18911,7684],["Kuwait","Asia",630463,2555,1049],["Myanmar","Asia",592346,19434,1602],["Dominican Rep.","North America",579749,4377,2800],["Palestine","Asia",576993,5356,146],["Estonia","Europe",519365,2574,54931],["Venezuela","South America",517148,5722,1031],["Moldova","Europe",504142,11521,3130],["Egypt","Africa",442182,24613,48850],["Libya","Africa",490973,6430,4613],["Cyprus","Asia",124370,1067,366340],["Ethiopia","Africa",456181,7515,11316],["Mongolia","Asia",313256,2179,154450],["Honduras","North America",132430,10900,282141],["Armenia","Asia",412606,8625,1732],["Oman","Asia",384669,4260,544],["Qatar","Asia",367477,677,1775],["Kenya","Africa",318787,5651,1116],["Zambia","Africa",317380,3988,839],["Botswana","Africa",303845,2697,1584],["Albania","Europe",272605,3497,299],["Algeria","Africa",178421,6875,80601],["Nigeria","Africa",250065,3143,2940],["Zimbabwe","Africa",245200,5512,2685],["Luxembourg","Europe",235939,1073,7170],["Uzbekistan","Asia",237329,1637,157],["Montenegro","Europe",234316,2720,498],["Mozambique","Africa",223464,2203,266],["Lao PDR","Asia",0,756,209325],["Kyrgyzstan","Asia",196406,2991,1596],["Iceland","Europe",0,153,188771],["Afghanistan","Asia",163215,7708,9692],["Namibia","Africa",161174,4040,2284],["Uganda","Africa",100205,3596,60268],["El Salvador","North America",158545,4133,1057],["Ghana","Africa",159980,1445,370],["Brunei","Asia",148069,223,2110],["Jamaica","North America",87411,3069,47630],["Cambodia","Asia",133205,3056,1],["Rwanda","Africa",45522,1459,83199],["Cameroon","Africa",117791,1930,226],["Angola","Africa",97149,1900,145],["Senegal","Africa",84157,1967,11],["Malawi","Africa",82885,2642,484],["French Guiana","South America",11254,397,72020],["C?te d'Ivoire","Africa",81442,799,64],["Suriname","South America",49510,1350,29687],["Swaziland","Africa",71050,1410,228],["Guyana","South America",63120,1238,914],["Madagascar","Africa",59370,1395,3612],["Sudan","Africa",0,4942,57432],["New Caledonia","Oceania",61410,313,470],["Belize","North America",57757,678,1353],["Bhutan","Asia",59596,21,11],["Mauritania","Africa",58065,982,152],["Syria","Asia",52610,3150,120],["Gabon","Africa",47312,304,61],["Papua New Guinea","Oceania",43755,651,19],["Burundi","Africa",0,38,42091],["Togo","Africa",36808,273,50],["Guinea","Africa",36113,442,42],["Tanzania","Africa",0,840,34514],["Bahamas","North America",32994,810,1266],["The Bahamas","North America",32994,810,1266],["Lesotho","Africa",24155,697,8058],["Mali","Africa",30259,735,116],["Haiti","North America",29730,835,327],["Benin","Africa",25506,163,1283],["Somalia","Africa",13182,1350,12033],["Timor-Leste","Asia",22764,131,30],["East Timor","Asia",22764,131,30],["Burkina Faso","Africa",20439,382,32],["Nicaragua","North America",4225,225,14041],["Solomon Is.","Oceania",16357,146,1671],["S. Sudan","Africa",13644,138,3844],["Tajikistan","Asia",17264,124,0],["Eq. Guinea","Africa",15704,183,37],["Djibouti","Africa",15427,189,15],["Bermuda","North America",14562,138,385],["Gambia","Africa",11591,365,46],["Greenland","North America",2761,21,9189],["Yemen","Asia",9107,2149,566],["Eritrea","Africa",9653,103,11],["Vanuatu","Oceania",8879,14,545],["Niger","Africa",8628,310,93],["Guinea-Bissau","Africa",8042,171,70],["Guinea Bissau","Africa",8042,171,70],["Comoros","Africa",7933,160,7],["Sierra Leone","Africa",0,125,7557],["Liberia","Africa",5747,294,1415],["Chad","Africa",4874,193,2350],["W. Sahara","Africa",9,1,0],["China","Asia",294270,17551,2650195]],"geo":"geo","hovertemplate":"Country=%{customdata[0]}<br>Continents=%{customdata[1]}<br>Recovered=%{customdata[2]}<br>Deaths=%{customdata[3]}<br>Active=%{customdata[4]}<br>Confirmed=%{z}<extra></extra>","locationmode":"country names","locations":["United States","India","Brazil","France","Germany","United Kingdom","Russia","Korea","Italy","Turkey","Spain","Vietnam","Argentina","Japan","Netherlands","Australia","Iran","Colombia","Indonesia","Poland","Mexico","Ukraine","Malaysia","Thailand","Austria","Belgium","Israel","Portugal","South Africa","Czech Rep.","Canada","Chile","Philippines","Switzerland","Peru","Greece","Denmark","Romania","Sweden","Iraq","Serbia","Hungary","Slovakia","Jordan","Georgia","Ireland","Pakistan","Norway","Singapore","Kazakhstan","New Zealand","Morocco","Bulgaria","Croatia","Cuba","Finland","Lebanon","Lithuania","Tunisia","Slovenia","Belarus","Nepal","Uruguay","United Arab Emirates","Bolivia","Costa Rica","Ecuador","Panama","Guatemala","Latvia","Azerbaijan","Saudi Arabia","Sri Lanka","Paraguay","Kuwait","Myanmar","Dominican Rep.","Palestine","Estonia","Venezuela","Moldova","Egypt","Libya","Cyprus","Ethiopia","Mongolia","Honduras","Armenia","Oman","Qatar","Kenya","Zambia","Botswana","Albania","Algeria","Nigeria","Zimbabwe","Luxembourg","Uzbekistan","Montenegro","Mozambique","Lao PDR","Kyrgyzstan","Iceland","Afghanistan","Namibia","Uganda","El Salvador","Ghana","Brunei","Jamaica","Cambodia","Rwanda","Cameroon","Angola","Senegal","Malawi","French Guiana","C?te d'Ivoire","Suriname","Swaziland","Guyana","Madagascar","Sudan","New Caledonia","Belize","Bhutan","Mauritania","Syria","Gabon","Papua New Guinea","Burundi","Togo","Guinea","Tanzania","Bahamas","The Bahamas","Lesotho","Mali","Haiti","Benin","Somalia","Timor-Leste","East Timor","Burkina Faso","Nicaragua","Solomon Is.","S. Sudan","Tajikistan","Eq. Guinea","Djibouti","Bermuda","Gambia","Greenland","Yemen","Eritrea","Vanuatu","Niger","Guinea-Bissau","Guinea Bissau","Comoros","Sierra Leone","Liberia","Chad","W. Sahara","China"],"name":"","z":[86522561,43181335,31153765,29641606,26540852,22305893,18351851,18163686,17505973,15072747,12403245,10725239,9230573,8929654,8090237,7426673,7232731,6109105,6056800,6008612,5789401,5011433,4514989,4466793,4267154,4158754,4151986,4066674,3968205,3921096,3881713,3748619,3691545,3657546,3585381,3470640,2986308,2910553,2509366,2328670,2018609,1919840,1790228,1694216,1655221,1565970,1530703,1434799,1318984,1305774,1196033,1170194,1165856,1138046,1105461,1105211,1099745,1063099,1042872,1026292,982867,979199,925777,910935,910134,904934,878196,873794,864662,829143,792785,771302,663869,651268,634067,613382,586926,582495,576870,523901,518793,515645,502016,491777,475012,469885,425471,422963,389473,369929,325554,322207,308126,276401,265897,256148,253397,244182,239123,237534,225933,210081,200993,188924,180615,167498,164069,163735,161795,150402,138110,136262,130180,119947,99194,86135,86011,83671,82305,80547,72688,65272,64377,62374,62193,59788,59628,59199,55880,47677,44425,42129,37131,36597,35354,35070,35070,32910,31110,30892,26952,26565,22925,22925,20853,18491,18174,17626,17388,15924,15631,15085,12002,11971,11822,9767,9438,9031,8283,8283,8100,7682,7456,7417,10,2962016],"type":"choropleth"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"geo":{"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"center":{}},"coloraxis":{"colorbar":{"title":{"text":"Confirmed"}},"colorscale":[[0.0,"rgb(255,255,204)"],[0.125,"rgb(255,237,160)"],[0.25,"rgb(254,217,118)"],[0.375,"rgb(254,178,76)"],[0.5,"rgb(253,141,60)"],[0.625,"rgb(252,78,42)"],[0.75,"rgb(227,26,28)"],[0.875,"rgb(189,0,38)"],[1.0,"rgb(128,0,38)"]]},"legend":{"tracegroupgap":0},"title":{"text":"Covid-19 Situation In The World"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('c9999f49-70ce-4594-a402-e9cdc6e77cc2');
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

                        })                };                });            </script>        </div>



```python
from pyecharts.charts import Map,Geo
from pyecharts import options as opts
from pyecharts.globals import GeoType,RenderType

global_list = list(zip(pandemic['Country'],pandemic['Confirmed']))
map = Map(opts.InitOpts(width='1300px',height='800px')).add(series_name = "World Pandemic Situation",
               data_pair = global_list,
               maptype = "world",
               #name_map=english_map, 
               is_map_symbol_show=False)

map.set_series_opts(label_opts=opts.LabelOpts(is_show=False))
map.set_global_opts(title_opts = opts.TitleOpts(title="Covid-19 Confirmed Cases Thourghout The World"),
                   visualmap_opts=opts.VisualMapOpts(is_piecewise=True,
                                                      pieces=[{
                                                          "min": 10000000,
                                                          "label": '>10000000',
                                                          "color": "#940808"}, {
                                                          "min": 1000000,
                                                         "max": 9999999,
                                                         "label": '1000000-9999999',
                                                         "color": "#d20404"},{
                                                          "min": 100000,
                                                          "max": 999999,
                                                          "label": '100000-999999',
                                                          "color": "#ff6700"},{
                                                          "min": 10000,
                                                          "max": 99999,
                                                          "label": '10000-99999',
                                                          "color": "#fdff00"}, {
                                                          "min": 1000,
                                                          "max": 9999,
                                                          "label": '1000-9999',
                                                          "color": "#b0ff00"}, {
                                                          "min": 0,
                                                          "max": 999,
                                                          "label": '0-999',
                                                          "color": "#b1d6ec"}
                                                            ]))
map.render('world map pandemic.html') #'worldpandemic.html'
```




    'C:\\Users\\User\\Documents\\Udemy\\Pandemic\\world map pandemic.html'




```python

```
