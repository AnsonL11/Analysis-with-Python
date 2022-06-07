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



# Scraping data from website 


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



# Scraping China's data from website


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



# Incorporate 2 set of data and generate dataframe


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
      <td>86637487</td>
      <td>2932133</td>
      <td>54466</td>
      <td>1033830</td>
      <td>82671524</td>
      <td>1.19%</td>
      <td>95.42%</td>
      <td>美国</td>
      <td>北美洲</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asia</td>
      <td>India</td>
      <td>43185049</td>
      <td>26976</td>
      <td>3714</td>
      <td>524708</td>
      <td>42633365</td>
      <td>1.22%</td>
      <td>98.72%</td>
      <td>印度</td>
      <td>亚洲</td>
    </tr>
    <tr>
      <th>2</th>
      <td>South America</td>
      <td>Brazil</td>
      <td>31195118</td>
      <td>426644</td>
      <td>41353</td>
      <td>667106</td>
      <td>30101368</td>
      <td>2.14%</td>
      <td>96.49%</td>
      <td>巴西</td>
      <td>南美洲</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Europe</td>
      <td>France</td>
      <td>29647790</td>
      <td>414481</td>
      <td>6184</td>
      <td>148464</td>
      <td>29084845</td>
      <td>0.50%</td>
      <td>98.10%</td>
      <td>法国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Europe</td>
      <td>Germany</td>
      <td>26543260</td>
      <td>779412</td>
      <td>2408</td>
      <td>139748</td>
      <td>25624100</td>
      <td>0.53%</td>
      <td>96.54%</td>
      <td>德国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Europe</td>
      <td>United Kingdom</td>
      <td>22339004</td>
      <td>169153</td>
      <td>6623</td>
      <td>178866</td>
      <td>21990985</td>
      <td>0.80%</td>
      <td>98.44%</td>
      <td>英国</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Europe</td>
      <td>Russia</td>
      <td>18355200</td>
      <td>207863</td>
      <td>3349</td>
      <td>379584</td>
      <td>17767753</td>
      <td>2.07%</td>
      <td>96.80%</td>
      <td>俄罗斯</td>
      <td>欧洲</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Asia</td>
      <td>Korea</td>
      <td>18168708</td>
      <td>278838</td>
      <td>5022</td>
      <td>24279</td>
      <td>17865591</td>
      <td>0.13%</td>
      <td>98.33%</td>
      <td>韩国</td>
      <td>亚洲</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Europe</td>
      <td>Italy</td>
      <td>17514589</td>
      <td>631348</td>
      <td>8512</td>
      <td>167019</td>
      <td>16716222</td>
      <td>0.95%</td>
      <td>95.44%</td>
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



# To generate numbers base on continents


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
      <td>11455125</td>
      <td>1523117</td>
      <td>1919</td>
      <td>250839</td>
      <td>9681169</td>
      <td>2.19%</td>
      <td>84.51%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asia</td>
      <td>149573250</td>
      <td>5357664</td>
      <td>90235</td>
      <td>1402484</td>
      <td>142813102</td>
      <td>0.94%</td>
      <td>95.48%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Europe</td>
      <td>195966612</td>
      <td>19163083</td>
      <td>35162</td>
      <td>1817230</td>
      <td>174986299</td>
      <td>0.93%</td>
      <td>89.29%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>North America</td>
      <td>101585024</td>
      <td>4056480</td>
      <td>61429</td>
      <td>1469666</td>
      <td>96058878</td>
      <td>1.45%</td>
      <td>94.56%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Oceania</td>
      <td>8782549</td>
      <td>288711</td>
      <td>22523</td>
      <td>11068</td>
      <td>8482770</td>
      <td>0.13%</td>
      <td>96.59%</td>
    </tr>
    <tr>
      <th>5</th>
      <td>South America</td>
      <td>58048868</td>
      <td>5255453</td>
      <td>49492</td>
      <td>1299660</td>
      <td>51493755</td>
      <td>2.24%</td>
      <td>88.71%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>World cases</td>
      <td>525411428</td>
      <td>35644508</td>
      <td>260760</td>
      <td>6250947</td>
      <td>483515973</td>
      <td>1.19%</td>
      <td>92.03%</td>
    </tr>
  </tbody>
</table>
</div>



# To generate numbers divided by conditions


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
      <td>11455125</td>
      <td>149573250</td>
      <td>195966612</td>
      <td>101585024</td>
      <td>8782549</td>
      <td>58048868</td>
      <td>525411428</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Active</td>
      <td>1523117</td>
      <td>5357664</td>
      <td>19163083</td>
      <td>4056480</td>
      <td>288711</td>
      <td>5255453</td>
      <td>35644508</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Newcases</td>
      <td>1919</td>
      <td>90235</td>
      <td>35162</td>
      <td>61429</td>
      <td>22523</td>
      <td>49492</td>
      <td>260760</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Deaths</td>
      <td>250839</td>
      <td>1402484</td>
      <td>1817230</td>
      <td>1469666</td>
      <td>11068</td>
      <td>1299660</td>
      <td>6250947</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Recovered</td>
      <td>9681169</td>
      <td>142813102</td>
      <td>174986299</td>
      <td>96058878</td>
      <td>8482770</td>
      <td>51493755</td>
      <td>483515973</td>
    </tr>
  </tbody>
</table>
</div>




```python
from kaleido.scopes.plotly import PlotlyScope
scope = PlotlyScope()


```

# Data Visualization


```python
continents = continents_df[:6]

fig = px.scatter(continents.sort_values(by='Confirmed', ascending = False), x='Continents', y='Confirmed', color='Confirmed', text='Confirmed',
           color_continuous_scale='rainbow',size='Confirmed', size_max=80, hover_data=['Confirmed', 'Continents'],
                labels={'Confirmed':"Total Confirmed Cases"})

fig.update_traces(textposition='top center', textfont_size=14, textfont_family="Arial Black", texttemplate='%{text:,.2s}')
fig = fig.update_layout(height=600, title="Total confirmed cases in Covid-19 of affected continents")
fig.show()
```


<div>                            <div id="d99ab9bc-552e-447b-9847-d7cfe51596b5" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("d99ab9bc-552e-447b-9847-d7cfe51596b5")) {                    Plotly.newPlot(                        "d99ab9bc-552e-447b-9847-d7cfe51596b5",                        [{"hovertemplate":"Continents=%{x}<br>Total Confirmed Cases=%{marker.color}<extra></extra>","legendgroup":"","marker":{"color":[195966612,149573250,101585024,58048868,11455125,8782549],"coloraxis":"coloraxis","size":[195966612,149573250,101585024,58048868,11455125,8782549],"sizemode":"area","sizeref":30619.783125,"symbol":"circle"},"mode":"markers+text","name":"","orientation":"v","showlegend":false,"text":[195966612.0,149573250.0,101585024.0,58048868.0,11455125.0,8782549.0],"x":["Europe","Asia","North America","South America","Africa","Oceania"],"xaxis":"x","y":[195966612,149573250,101585024,58048868,11455125,8782549],"yaxis":"y","type":"scatter","textfont":{"family":"Arial Black","size":14},"textposition":"top center","texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Continents"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Total Confirmed Cases"}},"coloraxis":{"colorbar":{"title":{"text":"Total Confirmed Cases"}},"colorscale":[[0.0,"rgb(150,0,90)"],[0.125,"rgb(0,0,200)"],[0.25,"rgb(0,25,255)"],[0.375,"rgb(0,152,255)"],[0.5,"rgb(44,255,150)"],[0.625,"rgb(151,255,0)"],[0.75,"rgb(255,234,0)"],[0.875,"rgb(255,111,0)"],[1.0,"rgb(255,0,0)"]]},"legend":{"tracegroupgap":0,"itemsizing":"constant"},"margin":{"t":60},"height":600,"title":{"text":"Total confirmed cases in Covid-19 of affected continents"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('d99ab9bc-552e-447b-9847-d7cfe51596b5');
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


<div>                            <div id="b40b13a6-17d5-43d5-8733-f005b6bf2a95" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("b40b13a6-17d5-43d5-8733-f005b6bf2a95")) {                    Plotly.newPlot(                        "b40b13a6-17d5-43d5-8733-f005b6bf2a95",                        [{"customdata":[["Active"],["Newcases"],["Deaths"],["Recovered"]],"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"hovertemplate":"label=%{label}<br>World cases=%{value}<br>Categories=%{customdata[0]}<extra></extra>","labels":["Active","Newcases","Deaths","Recovered"],"legendgroup":"","marker":{"colors":["darkorange","midnightblue","grey","lightskyblue"]},"name":"","showlegend":true,"values":[35644508,260760,6250947,483515973],"type":"pie","textfont":{"color":"black","family":"Arial Black","size":14},"insidetextorientation":"radial","textinfo":"percent+label","textposition":"auto"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"legend":{"tracegroupgap":0,"title":{"text":"Categories"}},"title":{"text":"The Ratio of Situation of Covid-19"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('b40b13a6-17d5-43d5-8733-f005b6bf2a95');
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


<div>                            <div id="74b77a7f-fa88-4708-b15b-984b859d5886" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("74b77a7f-fa88-4708-b15b-984b859d5886")) {                    Plotly.newPlot(                        "74b77a7f-fa88-4708-b15b-984b859d5886",                        [{"alignmentgroup":"True","customdata":[[195966612,149573250,101585024,58048868,11455125,8782549]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Confirmed","marker":{"color":"maroon","pattern":{"shape":""}},"name":"Confirmed","offsetgroup":"Confirmed","orientation":"h","showlegend":true,"text":[525411428],"textposition":"auto","x":[525411428],"xaxis":"x","y":["Confirmed"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[19163083,5357664,4056480,5255453,1523117,288711]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Active","marker":{"color":"darkorange","pattern":{"shape":""}},"name":"Active","offsetgroup":"Active","orientation":"h","showlegend":true,"text":[35644508],"textposition":"auto","x":[35644508],"xaxis":"x","y":["Active"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[35162,90235,61429,49492,1919,22523]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Newcases","marker":{"color":"midnightblue","pattern":{"shape":""}},"name":"Newcases","offsetgroup":"Newcases","orientation":"h","showlegend":true,"text":[260760],"textposition":"auto","x":[260760],"xaxis":"x","y":["Newcases"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[1817230,1402484,1469666,1299660,250839,11068]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Deaths","marker":{"color":"gray","pattern":{"shape":""}},"name":"Deaths","offsetgroup":"Deaths","orientation":"h","showlegend":true,"text":[6250947],"textposition":"auto","x":[6250947],"xaxis":"x","y":["Deaths"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"},{"alignmentgroup":"True","customdata":[[174986299,142813102,96058878,51493755,9681169,8482770]],"hovertemplate":"Categories=%{y}<br>Total No.of Cases=%{text}<br>Europe=%{customdata[0]}<br>Asia=%{customdata[1]}<br>North America=%{customdata[2]}<br>South America=%{customdata[3]}<br>Africa=%{customdata[4]}<br>Oceania=%{customdata[5]}<extra></extra>","legendgroup":"Recovered","marker":{"color":"lightskyblue","pattern":{"shape":""}},"name":"Recovered","offsetgroup":"Recovered","orientation":"h","showlegend":true,"text":[483515973],"textposition":"auto","x":[483515973],"xaxis":"x","y":["Recovered"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial Black","size":14},"texttemplate":"%{text:,.3s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Total No.of Cases"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Categories"},"categoryorder":"total ascending","categoryarray":["Recovered","Deaths","Newcases","Active","Confirmed"]},"legend":{"title":{"text":"Categories"},"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":600,"title":{"text":"The Global Situation of Covid-19"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('74b77a7f-fa88-4708-b15b-984b859d5886');
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


<div>                            <div id="d792bcae-ae52-457e-a40b-1c2cef2a35f2" class="plotly-graph-div" style="height:600px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("d792bcae-ae52-457e-a40b-1c2cef2a35f2")) {                    Plotly.newPlot(                        "d792bcae-ae52-457e-a40b-1c2cef2a35f2",                        [{"branchvalues":"total","customdata":[[4613,"Africa"],[25547,"Europe"],[1766,"Europe"],[1117,"Asia"],[4260,"Asia"],[75,"Asia"],[46606,"Oceania"],[2644,"Africa"],[72329,"Europe"],[2627,"Asia"],[1014231,"Africa"],[3819,"Europe"],[15833,"South America"],[4430,"North America"],[226,"Africa"],[2209,"Europe"],[53,"Asia"],[398,"Asia"],[7684,"South America"],[1049,"Asia"],[61722,"Africa"],[3130,"Europe"],[26976,"Asia"],[366340,"Asia"],[2845073,"Europe"],[1223,"North America"],[77911,"Asia"],[35252,"Asia"],[631348,"Europe"],[464097,"Europe"],[203743,"Asia"],[236299,"Oceania"],[169153,"Europe"],[2703007,"Asia"],[779412,"Europe"],[3498,"Asia"],[393198,"North America"],[278838,"Asia"],[414481,"Europe"],[207863,"Europe"],[27723,"Africa"],[36126,"Europe"],[65999,"Europe"],[3372498,"South America"],[4044091,"Europe"],[40558,"Europe"],[426644,"South America"],[180063,"South America"],[2459,"Asia"],[1603,"Asia"],[1076,"Africa"],[1716,"Asia"],[146,"Asia"],[1022,"South America"],[1863,"Asia"],[2800,"North America"],[48850,"Africa"],[7070,"Europe"],[849669,"South America"],[14640,"Asia"],[16984,"South America"],[975889,"Europe"],[30790,"South America"],[1100584,"Europe"],[1626,"Europe"],[21630,"Asia"],[29506,"Asia"],[1584,"Africa"],[5656,"Europe"],[1168983,"Asia"],[292,"Europe"],[2932133,"North America"],[26,"Asia"],[206,"Africa"],[12033,"Africa"],[56,"Africa"],[472,"Africa"],[11,"Africa"],[9189,"North America"],[1413,"North America"],[370,"Africa"],[913,"South America"],[0,"Asia"],[14041,"North America"],[188771,"Europe"],[80602,"Africa"],[209327,"Asia"],[2351,"Africa"],[116,"Africa"],[50,"Africa"],[93,"Africa"],[385,"North America"],[12,"Africa"],[138,"Asia"],[137,"Africa"],[8,"Asia"],[26,"Asia"],[32,"Africa"],[497,"Oceania"],[8058,"Africa"],[15,"Africa"],[57440,"Africa"],[327,"North America"],[1283,"Africa"],[3712,"Africa"],[42167,"Africa"],[61,"Africa"],[1223,"North America"],[2940,"Africa"],[72020,"South America"],[7557,"Africa"],[7,"Africa"],[70,"Africa"],[70,"Africa"],[282141,"North America"],[11474,"Africa"],[545,"Oceania"],[29687,"South America"],[9692,"Asia"],[566,"Asia"],[1217,"North America"],[2273,"Asia"],[7492,"Asia"],[1,"Asia"],[83199,"Africa"],[26026,"North America"],[35698,"North America"],[2128,"Africa"],[109,"Asia"],[556809,"Europe"],[251646,"South America"],[18769,"Asia"],[4731,"Oceania"],[36191,"Europe"],[540,"Europe"],[0,"Africa"],[34514,"Africa"],[1046,"Asia"],[2622,"Africa"],[7170,"Europe"],[544,"Asia"],[483,"Europe"],[54929,"Europe"],[48081,"North America"],[302772,"North America"],[183,"North America"],[17169,"Europe"],[45258,"Europe"],[2526,"Asia"],[145,"Africa"],[37,"Africa"],[4906324,"Europe"],[4570,"Europe"],[1431724,"Europe"],[1596,"Asia"],[248,"Africa"],[15027,"Europe"],[3848,"Africa"],[154450,"Asia"],[158,"Asia"],[1227,"Asia"],[42,"Africa"],[1415,"Africa"],[809,"Africa"],[46,"Africa"],[33,"Oceania"],[9692,"Asia"],[292,"Europe"],[80602,"Africa"],[145,"Africa"],[251646,"South America"],[1716,"Asia"],[236299,"Oceania"],[36126,"Europe"],[53,"Asia"],[1223,"North America"],[975889,"Europe"],[65999,"Europe"],[1413,"North America"],[1283,"Africa"],[385,"North America"],[8,"Asia"],[15833,"South America"],[1584,"Africa"],[426644,"South America"],[2273,"Asia"],[72329,"Europe"],[32,"Africa"],[42167,"Africa"],[56,"Africa"],[1,"Asia"],[226,"Africa"],[302772,"North America"],[2351,"Africa"],[180063,"South America"],[2703007,"Asia"],[30790,"South America"],[7,"Africa"],[35698,"North America"],[1626,"Europe"],[183,"North America"],[366340,"Asia"],[540,"Europe"],[7070,"Europe"],[15,"Africa"],[2800,"North America"],[26,"Asia"],[849669,"South America"],[48850,"Africa"],[1217,"North America"],[37,"Africa"],[12,"Africa"],[54929,"Europe"],[11474,"Africa"],[1100584,"Europe"],[414481,"Europe"],[72020,"South America"],[61,"Africa"],[46,"Africa"],[1117,"Asia"],[779412,"Europe"],[370,"Africa"],[45258,"Europe"],[9189,"North America"],[4430,"North America"],[42,"Africa"],[70,"Africa"],[70,"Africa"],[913,"South America"],[327,"North America"],[282141,"North America"],[25547,"Europe"],[188771,"Europe"],[26976,"Asia"],[3498,"Asia"],[35252,"Asia"],[1046,"Asia"],[15027,"Europe"],[18769,"Asia"],[631348,"Europe"],[48081,"North America"],[203743,"Asia"],[1227,"Asia"],[75,"Asia"],[1076,"Africa"],[278838,"Asia"],[1049,"Asia"],[1596,"Asia"],[209327,"Asia"],[2209,"Europe"],[2627,"Asia"],[8058,"Africa"],[1415,"Africa"],[4613,"Africa"],[17169,"Europe"],[7170,"Europe"],[3712,"Africa"],[472,"Africa"],[21630,"Asia"],[116,"Africa"],[137,"Africa"],[393198,"North America"],[3130,"Europe"],[154450,"Asia"],[483,"Europe"],[2644,"Africa"],[248,"Africa"],[1603,"Asia"],[2128,"Africa"],[109,"Asia"],[36191,"Europe"],[497,"Oceania"],[46606,"Oceania"],[14041,"North America"],[93,"Africa"],[2940,"Africa"],[1431724,"Europe"],[544,"Asia"],[4260,"Asia"],[146,"Asia"],[26026,"North America"],[33,"Oceania"],[7684,"South America"],[3372498,"South America"],[2459,"Asia"],[556809,"Europe"],[4044091,"Europe"],[1863,"Asia"],[2845073,"Europe"],[207863,"Europe"],[83199,"Africa"],[3848,"Africa"],[7492,"Asia"],[11,"Africa"],[4570,"Europe"],[7557,"Africa"],[77911,"Asia"],[1766,"Europe"],[3819,"Europe"],[4731,"Oceania"],[12033,"Africa"],[27723,"Africa"],[464097,"Europe"],[398,"Asia"],[57440,"Africa"],[29687,"South America"],[206,"Africa"],[5656,"Europe"],[40558,"Europe"],[138,"Asia"],[0,"Asia"],[34514,"Africa"],[29506,"Asia"],[1223,"North America"],[26,"Asia"],[50,"Africa"],[1014231,"Africa"],[2526,"Asia"],[61722,"Africa"],[4906324,"Europe"],[14640,"Asia"],[169153,"Europe"],[2932133,"North America"],[16984,"South America"],[158,"Asia"],[545,"Oceania"],[1022,"South America"],[1168983,"Asia"],[0,"Africa"],[566,"Asia"],[809,"Africa"],[2622,"Africa"],["(?)","Africa"],["(?)","Asia"],["(?)","Europe"],["(?)","North America"],["(?)","Oceania"],["(?)","South America"]],"domain":{"x":[0.0,0.45],"y":[0.0,1.0]},"hovertemplate":"labels=%{label}<br>Confirmed=%{value}<br>parent=%{parent}<br>id=%{id}<br>Active=%{customdata[0]}<br>Continents=%{customdata[1]}<extra></extra>","ids":["Africa/Libya/502016","Europe/Hungary/1919840","Europe/Slovakia/1790256","Asia/Georgia/1655221","Asia/Pakistan/1530762","Asia/Kazakhstan/1305788","Oceania/New Zealand/1200580","Africa/Morocco/1170427","Europe/Bulgaria/1166148","Asia/Lebanon/1099896","Africa/Tunisia/1042872","Europe/Slovenia/1026368","South America/Bolivia/910228","North America/Guatemala/864768","Africa/Cameroon/119947","Europe/Latvia/829425","Asia/Azerbaijan/792785","Asia/Sri Lanka/663884","South America/Paraguay/651268","Asia/Kuwait/634067","Africa/Uganda/165700","Europe/Moldova/518793","Asia/India/43185049","Asia/Cyprus/491777","Europe/Romania/2910766","North America/Bahamas/35079","Asia/Singapore/1321146","Asia/Iran/7232790","Europe/Italy/17514589","Europe/Spain/12403245","Asia/Japan/8945784","Oceania/Australia/7444436","Europe/United Kingdom/22339004","Asia/China/3015155","Europe/Germany/26543260","Asia/Indonesia/6057142","North America/Mexico/5791282","Asia/Korea/18168708","Europe/France/29647790","Europe/Russia/18355200","Africa/South Africa/3968931","Europe/Austria/4269277","Europe/Belgium/4158754","South America/Peru/3585767","Europe/Portugal/4066674","Europe/Switzerland/3666829","South America/Brazil/31195118","South America/Chile/3756220","Asia/Philippines/3691724","Asia/Myanmar/613387","Africa/Kenya/325634","Asia/Armenia/423006","Asia/Palestine/582495","South America/Venezuela/523937","Asia/Qatar/370235","North America/Dominican Rep./586926","Africa/Egypt/515645","Europe/Denmark/2986308","South America/Ecuador/885318","Asia/United Arab Emirates/911514","South America/Uruguay/925777","Europe/Belarus/982867","South America/Colombia/6109105","Europe/Finland/1105211","Europe/Croatia/1138076","Asia/Malaysia/4516319","Asia/Thailand/4468955","Africa/Botswana/308126","Europe/Sweden/2509366","Asia/Vietnam/10726045","Europe/Albania/276415","North America/United States/86637487","Asia/Timor-Leste/22927","Africa/Swaziland/72703","Africa/Somalia/26565","Africa/C?te d'Ivoire/82309","Africa/Malawi/86022","Africa/Senegal/86137","North America/Greenland/11971","North America/Belize/60063","Africa/Ghana/161795","South America/Guyana/65294","Asia/Tajikistan/17388","North America/Nicaragua/18491","Europe/Iceland/188924","Africa/Algeria/265900","Asia/Lao PDR/210083","Africa/Chad/7418","Africa/Mali/31111","Africa/Togo/37131","Africa/Niger/9031","North America/Bermuda/15085","Africa/Eritrea/9768","Asia/Syria/55898","Africa/Mauritania/59205","Asia/Bhutan/59636","Asia/East Timor/22927","Africa/Burkina Faso/20853","Oceania/New Caledonia/62220","Africa/Lesotho/32910","Africa/Djibouti/15631","Africa/Sudan/62382","North America/Haiti/30892","Africa/Benin/26952","Africa/Madagascar/64478","Africa/Burundi/42205","Africa/Gabon/47677","North America/The Bahamas/35079","Africa/Nigeria/256148","South America/French Guiana/83671","Africa/Sierra Leone/7682","Africa/Comoros/8100","Africa/Guinea Bissau/8283","Africa/Guinea-Bissau/8283","North America/Honduras/425471","Africa/Ethiopia/475321","Oceania/Vanuatu/9438","South America/Suriname/80547","Asia/Afghanistan/180615","Asia/Yemen/11822","North America/El Salvador/163920","Asia/Brunei/150781","Asia/Saudi Arabia/772269","Asia/Cambodia/136262","Africa/Rwanda/130180","North America/Panama/875486","North America/Costa Rica/904934","Africa/Namibia/167565","Asia/Nepal/979206","Europe/Poland/6008664","South America/Argentina/9276618","Asia/Israel/4154566","Oceania/Solomon Is./21237","Europe/Netherlands/8093892","Europe/Czech Rep./3921126","Africa/W. Sahara/10","Africa/Tanzania/35354","Asia/Iraq/2328787","Africa/Zimbabwe/253508","Europe/Luxembourg/244182","Asia/Oman/389473","Europe/Montenegro/237563","Europe/Estonia/576868","North America/Jamaica/139029","North America/Canada/3883584","North America/Cuba/1105477","Europe/Lithuania/1063106","Europe/Greece/3473141","Asia/Turkey/15072747","Africa/Angola/99194","Africa/Eq. Guinea/15924","Europe/Ukraine/5014929","Europe/Serbia/2018890","Europe/Norway/1434896","Asia/Kyrgyzstan/200993","Africa/Mozambique/225984","Europe/Ireland/1565970","Africa/S. Sudan/17630","Asia/Mongolia/469885","Asia/Uzbekistan/239135","Asia/Jordan/1694216","Africa/Guinea/36597","Africa/Liberia/7456","Africa/Zambia/322423","Africa/Gambia/12002","Oceania/Papua New Guinea/44638","Asia/Afghanistan","Europe/Albania","Africa/Algeria","Africa/Angola","South America/Argentina","Asia/Armenia","Oceania/Australia","Europe/Austria","Asia/Azerbaijan","North America/Bahamas","Europe/Belarus","Europe/Belgium","North America/Belize","Africa/Benin","North America/Bermuda","Asia/Bhutan","South America/Bolivia","Africa/Botswana","South America/Brazil","Asia/Brunei","Europe/Bulgaria","Africa/Burkina Faso","Africa/Burundi","Africa/C?te d'Ivoire","Asia/Cambodia","Africa/Cameroon","North America/Canada","Africa/Chad","South America/Chile","Asia/China","South America/Colombia","Africa/Comoros","North America/Costa Rica","Europe/Croatia","North America/Cuba","Asia/Cyprus","Europe/Czech Rep.","Europe/Denmark","Africa/Djibouti","North America/Dominican Rep.","Asia/East Timor","South America/Ecuador","Africa/Egypt","North America/El Salvador","Africa/Eq. Guinea","Africa/Eritrea","Europe/Estonia","Africa/Ethiopia","Europe/Finland","Europe/France","South America/French Guiana","Africa/Gabon","Africa/Gambia","Asia/Georgia","Europe/Germany","Africa/Ghana","Europe/Greece","North America/Greenland","North America/Guatemala","Africa/Guinea","Africa/Guinea Bissau","Africa/Guinea-Bissau","South America/Guyana","North America/Haiti","North America/Honduras","Europe/Hungary","Europe/Iceland","Asia/India","Asia/Indonesia","Asia/Iran","Asia/Iraq","Europe/Ireland","Asia/Israel","Europe/Italy","North America/Jamaica","Asia/Japan","Asia/Jordan","Asia/Kazakhstan","Africa/Kenya","Asia/Korea","Asia/Kuwait","Asia/Kyrgyzstan","Asia/Lao PDR","Europe/Latvia","Asia/Lebanon","Africa/Lesotho","Africa/Liberia","Africa/Libya","Europe/Lithuania","Europe/Luxembourg","Africa/Madagascar","Africa/Malawi","Asia/Malaysia","Africa/Mali","Africa/Mauritania","North America/Mexico","Europe/Moldova","Asia/Mongolia","Europe/Montenegro","Africa/Morocco","Africa/Mozambique","Asia/Myanmar","Africa/Namibia","Asia/Nepal","Europe/Netherlands","Oceania/New Caledonia","Oceania/New Zealand","North America/Nicaragua","Africa/Niger","Africa/Nigeria","Europe/Norway","Asia/Oman","Asia/Pakistan","Asia/Palestine","North America/Panama","Oceania/Papua New Guinea","South America/Paraguay","South America/Peru","Asia/Philippines","Europe/Poland","Europe/Portugal","Asia/Qatar","Europe/Romania","Europe/Russia","Africa/Rwanda","Africa/S. Sudan","Asia/Saudi Arabia","Africa/Senegal","Europe/Serbia","Africa/Sierra Leone","Asia/Singapore","Europe/Slovakia","Europe/Slovenia","Oceania/Solomon Is.","Africa/Somalia","Africa/South Africa","Europe/Spain","Asia/Sri Lanka","Africa/Sudan","South America/Suriname","Africa/Swaziland","Europe/Sweden","Europe/Switzerland","Asia/Syria","Asia/Tajikistan","Africa/Tanzania","Asia/Thailand","North America/The Bahamas","Asia/Timor-Leste","Africa/Togo","Africa/Tunisia","Asia/Turkey","Africa/Uganda","Europe/Ukraine","Asia/United Arab Emirates","Europe/United Kingdom","North America/United States","South America/Uruguay","Asia/Uzbekistan","Oceania/Vanuatu","South America/Venezuela","Asia/Vietnam","Africa/W. Sahara","Asia/Yemen","Africa/Zambia","Africa/Zimbabwe","Africa","Asia","Europe","North America","Oceania","South America"],"labels":["502016","1919840","1790256","1655221","1530762","1305788","1200580","1170427","1166148","1099896","1042872","1026368","910228","864768","119947","829425","792785","663884","651268","634067","165700","518793","43185049","491777","2910766","35079","1321146","7232790","17514589","12403245","8945784","7444436","22339004","3015155","26543260","6057142","5791282","18168708","29647790","18355200","3968931","4269277","4158754","3585767","4066674","3666829","31195118","3756220","3691724","613387","325634","423006","582495","523937","370235","586926","515645","2986308","885318","911514","925777","982867","6109105","1105211","1138076","4516319","4468955","308126","2509366","10726045","276415","86637487","22927","72703","26565","82309","86022","86137","11971","60063","161795","65294","17388","18491","188924","265900","210083","7418","31111","37131","9031","15085","9768","55898","59205","59636","22927","20853","62220","32910","15631","62382","30892","26952","64478","42205","47677","35079","256148","83671","7682","8100","8283","8283","425471","475321","9438","80547","180615","11822","163920","150781","772269","136262","130180","875486","904934","167565","979206","6008664","9276618","4154566","21237","8093892","3921126","10","35354","2328787","253508","244182","389473","237563","576868","139029","3883584","1105477","1063106","3473141","15072747","99194","15924","5014929","2018890","1434896","200993","225984","1565970","17630","469885","239135","1694216","36597","7456","322423","12002","44638","Afghanistan","Albania","Algeria","Angola","Argentina","Armenia","Australia","Austria","Azerbaijan","Bahamas","Belarus","Belgium","Belize","Benin","Bermuda","Bhutan","Bolivia","Botswana","Brazil","Brunei","Bulgaria","Burkina Faso","Burundi","C?te d'Ivoire","Cambodia","Cameroon","Canada","Chad","Chile","China","Colombia","Comoros","Costa Rica","Croatia","Cuba","Cyprus","Czech Rep.","Denmark","Djibouti","Dominican Rep.","East Timor","Ecuador","Egypt","El Salvador","Eq. Guinea","Eritrea","Estonia","Ethiopia","Finland","France","French Guiana","Gabon","Gambia","Georgia","Germany","Ghana","Greece","Greenland","Guatemala","Guinea","Guinea Bissau","Guinea-Bissau","Guyana","Haiti","Honduras","Hungary","Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica","Japan","Jordan","Kazakhstan","Kenya","Korea","Kuwait","Kyrgyzstan","Lao PDR","Latvia","Lebanon","Lesotho","Liberia","Libya","Lithuania","Luxembourg","Madagascar","Malawi","Malaysia","Mali","Mauritania","Mexico","Moldova","Mongolia","Montenegro","Morocco","Mozambique","Myanmar","Namibia","Nepal","Netherlands","New Caledonia","New Zealand","Nicaragua","Niger","Nigeria","Norway","Oman","Pakistan","Palestine","Panama","Papua New Guinea","Paraguay","Peru","Philippines","Poland","Portugal","Qatar","Romania","Russia","Rwanda","S. Sudan","Saudi Arabia","Senegal","Serbia","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Is.","Somalia","South Africa","Spain","Sri Lanka","Sudan","Suriname","Swaziland","Sweden","Switzerland","Syria","Tajikistan","Tanzania","Thailand","The Bahamas","Timor-Leste","Togo","Tunisia","Turkey","Uganda","Ukraine","United Arab Emirates","United Kingdom","United States","Uruguay","Uzbekistan","Vanuatu","Venezuela","Vietnam","W. Sahara","Yemen","Zambia","Zimbabwe","Africa","Asia","Europe","North America","Oceania","South America"],"marker":{"colors":["plum","firebrick","firebrick","midnightblue","midnightblue","midnightblue","darkorange","plum","firebrick","midnightblue","plum","firebrick","seagreen","yellow","plum","firebrick","midnightblue","midnightblue","seagreen","midnightblue","plum","firebrick","midnightblue","midnightblue","firebrick","yellow","midnightblue","midnightblue","firebrick","firebrick","midnightblue","darkorange","firebrick","midnightblue","firebrick","midnightblue","yellow","midnightblue","firebrick","firebrick","plum","firebrick","firebrick","seagreen","firebrick","firebrick","seagreen","seagreen","midnightblue","midnightblue","plum","midnightblue","midnightblue","seagreen","midnightblue","yellow","plum","firebrick","seagreen","midnightblue","seagreen","firebrick","seagreen","firebrick","firebrick","midnightblue","midnightblue","plum","firebrick","midnightblue","firebrick","yellow","midnightblue","plum","plum","plum","plum","plum","yellow","yellow","plum","seagreen","midnightblue","yellow","firebrick","plum","midnightblue","plum","plum","plum","plum","yellow","plum","midnightblue","plum","midnightblue","midnightblue","plum","darkorange","plum","plum","plum","yellow","plum","plum","plum","plum","yellow","plum","seagreen","plum","plum","plum","plum","yellow","plum","darkorange","seagreen","midnightblue","midnightblue","yellow","midnightblue","midnightblue","midnightblue","plum","yellow","yellow","plum","midnightblue","firebrick","seagreen","midnightblue","darkorange","firebrick","firebrick","plum","plum","midnightblue","plum","firebrick","midnightblue","firebrick","firebrick","yellow","yellow","yellow","firebrick","firebrick","midnightblue","plum","plum","firebrick","firebrick","firebrick","midnightblue","plum","firebrick","plum","midnightblue","midnightblue","midnightblue","plum","plum","plum","plum","darkorange","midnightblue","firebrick","plum","plum","seagreen","midnightblue","darkorange","firebrick","midnightblue","yellow","firebrick","firebrick","yellow","plum","yellow","midnightblue","seagreen","plum","seagreen","midnightblue","firebrick","plum","plum","plum","midnightblue","plum","yellow","plum","seagreen","midnightblue","seagreen","plum","yellow","firebrick","yellow","midnightblue","firebrick","firebrick","plum","yellow","midnightblue","seagreen","plum","yellow","plum","plum","firebrick","plum","firebrick","firebrick","seagreen","plum","plum","midnightblue","firebrick","plum","firebrick","yellow","yellow","plum","plum","plum","seagreen","yellow","yellow","firebrick","firebrick","midnightblue","midnightblue","midnightblue","midnightblue","firebrick","midnightblue","firebrick","yellow","midnightblue","midnightblue","midnightblue","plum","midnightblue","midnightblue","midnightblue","midnightblue","firebrick","midnightblue","plum","plum","plum","firebrick","firebrick","plum","plum","midnightblue","plum","plum","yellow","firebrick","midnightblue","firebrick","plum","plum","midnightblue","plum","midnightblue","firebrick","darkorange","darkorange","yellow","plum","plum","firebrick","midnightblue","midnightblue","midnightblue","yellow","darkorange","seagreen","seagreen","midnightblue","firebrick","firebrick","midnightblue","firebrick","firebrick","plum","plum","midnightblue","plum","firebrick","plum","midnightblue","firebrick","firebrick","darkorange","plum","plum","firebrick","midnightblue","plum","seagreen","plum","firebrick","firebrick","midnightblue","midnightblue","plum","midnightblue","yellow","midnightblue","plum","plum","midnightblue","plum","firebrick","midnightblue","firebrick","yellow","seagreen","midnightblue","darkorange","seagreen","midnightblue","plum","midnightblue","plum","plum","plum","midnightblue","firebrick","yellow","darkorange","seagreen"]},"name":"","parents":["Africa/Libya","Europe/Hungary","Europe/Slovakia","Asia/Georgia","Asia/Pakistan","Asia/Kazakhstan","Oceania/New Zealand","Africa/Morocco","Europe/Bulgaria","Asia/Lebanon","Africa/Tunisia","Europe/Slovenia","South America/Bolivia","North America/Guatemala","Africa/Cameroon","Europe/Latvia","Asia/Azerbaijan","Asia/Sri Lanka","South America/Paraguay","Asia/Kuwait","Africa/Uganda","Europe/Moldova","Asia/India","Asia/Cyprus","Europe/Romania","North America/Bahamas","Asia/Singapore","Asia/Iran","Europe/Italy","Europe/Spain","Asia/Japan","Oceania/Australia","Europe/United Kingdom","Asia/China","Europe/Germany","Asia/Indonesia","North America/Mexico","Asia/Korea","Europe/France","Europe/Russia","Africa/South Africa","Europe/Austria","Europe/Belgium","South America/Peru","Europe/Portugal","Europe/Switzerland","South America/Brazil","South America/Chile","Asia/Philippines","Asia/Myanmar","Africa/Kenya","Asia/Armenia","Asia/Palestine","South America/Venezuela","Asia/Qatar","North America/Dominican Rep.","Africa/Egypt","Europe/Denmark","South America/Ecuador","Asia/United Arab Emirates","South America/Uruguay","Europe/Belarus","South America/Colombia","Europe/Finland","Europe/Croatia","Asia/Malaysia","Asia/Thailand","Africa/Botswana","Europe/Sweden","Asia/Vietnam","Europe/Albania","North America/United States","Asia/Timor-Leste","Africa/Swaziland","Africa/Somalia","Africa/C?te d'Ivoire","Africa/Malawi","Africa/Senegal","North America/Greenland","North America/Belize","Africa/Ghana","South America/Guyana","Asia/Tajikistan","North America/Nicaragua","Europe/Iceland","Africa/Algeria","Asia/Lao PDR","Africa/Chad","Africa/Mali","Africa/Togo","Africa/Niger","North America/Bermuda","Africa/Eritrea","Asia/Syria","Africa/Mauritania","Asia/Bhutan","Asia/East Timor","Africa/Burkina Faso","Oceania/New Caledonia","Africa/Lesotho","Africa/Djibouti","Africa/Sudan","North America/Haiti","Africa/Benin","Africa/Madagascar","Africa/Burundi","Africa/Gabon","North America/The Bahamas","Africa/Nigeria","South America/French Guiana","Africa/Sierra Leone","Africa/Comoros","Africa/Guinea Bissau","Africa/Guinea-Bissau","North America/Honduras","Africa/Ethiopia","Oceania/Vanuatu","South America/Suriname","Asia/Afghanistan","Asia/Yemen","North America/El Salvador","Asia/Brunei","Asia/Saudi Arabia","Asia/Cambodia","Africa/Rwanda","North America/Panama","North America/Costa Rica","Africa/Namibia","Asia/Nepal","Europe/Poland","South America/Argentina","Asia/Israel","Oceania/Solomon Is.","Europe/Netherlands","Europe/Czech Rep.","Africa/W. Sahara","Africa/Tanzania","Asia/Iraq","Africa/Zimbabwe","Europe/Luxembourg","Asia/Oman","Europe/Montenegro","Europe/Estonia","North America/Jamaica","North America/Canada","North America/Cuba","Europe/Lithuania","Europe/Greece","Asia/Turkey","Africa/Angola","Africa/Eq. Guinea","Europe/Ukraine","Europe/Serbia","Europe/Norway","Asia/Kyrgyzstan","Africa/Mozambique","Europe/Ireland","Africa/S. Sudan","Asia/Mongolia","Asia/Uzbekistan","Asia/Jordan","Africa/Guinea","Africa/Liberia","Africa/Zambia","Africa/Gambia","Oceania/Papua New Guinea","Asia","Europe","Africa","Africa","South America","Asia","Oceania","Europe","Asia","North America","Europe","Europe","North America","Africa","North America","Asia","South America","Africa","South America","Asia","Europe","Africa","Africa","Africa","Asia","Africa","North America","Africa","South America","Asia","South America","Africa","North America","Europe","North America","Asia","Europe","Europe","Africa","North America","Asia","South America","Africa","North America","Africa","Africa","Europe","Africa","Europe","Europe","South America","Africa","Africa","Asia","Europe","Africa","Europe","North America","North America","Africa","Africa","Africa","South America","North America","North America","Europe","Europe","Asia","Asia","Asia","Asia","Europe","Asia","Europe","North America","Asia","Asia","Asia","Africa","Asia","Asia","Asia","Asia","Europe","Asia","Africa","Africa","Africa","Europe","Europe","Africa","Africa","Asia","Africa","Africa","North America","Europe","Asia","Europe","Africa","Africa","Asia","Africa","Asia","Europe","Oceania","Oceania","North America","Africa","Africa","Europe","Asia","Asia","Asia","North America","Oceania","South America","South America","Asia","Europe","Europe","Asia","Europe","Europe","Africa","Africa","Asia","Africa","Europe","Africa","Asia","Europe","Europe","Oceania","Africa","Africa","Europe","Asia","Africa","South America","Africa","Europe","Europe","Asia","Asia","Africa","Asia","North America","Asia","Africa","Africa","Asia","Africa","Europe","Asia","Europe","North America","South America","Asia","Oceania","South America","Asia","Africa","Asia","Africa","Africa","","","","","",""],"values":[502016,1919840,1790256,1655221,1530762,1305788,1200580,1170427,1166148,1099896,1042872,1026368,910228,864768,119947,829425,792785,663884,651268,634067,165700,518793,43185049,491777,2910766,35079,1321146,7232790,17514589,12403245,8945784,7444436,22339004,3015155,26543260,6057142,5791282,18168708,29647790,18355200,3968931,4269277,4158754,3585767,4066674,3666829,31195118,3756220,3691724,613387,325634,423006,582495,523937,370235,586926,515645,2986308,885318,911514,925777,982867,6109105,1105211,1138076,4516319,4468955,308126,2509366,10726045,276415,86637487,22927,72703,26565,82309,86022,86137,11971,60063,161795,65294,17388,18491,188924,265900,210083,7418,31111,37131,9031,15085,9768,55898,59205,59636,22927,20853,62220,32910,15631,62382,30892,26952,64478,42205,47677,35079,256148,83671,7682,8100,8283,8283,425471,475321,9438,80547,180615,11822,163920,150781,772269,136262,130180,875486,904934,167565,979206,6008664,9276618,4154566,21237,8093892,3921126,10,35354,2328787,253508,244182,389473,237563,576868,139029,3883584,1105477,1063106,3473141,15072747,99194,15924,5014929,2018890,1434896,200993,225984,1565970,17630,469885,239135,1694216,36597,7456,322423,12002,44638,180615,276415,265900,99194,9276618,423006,7444436,4269277,792785,35079,982867,4158754,60063,26952,15085,59636,910228,308126,31195118,150781,1166148,20853,42205,82309,136262,119947,3883584,7418,3756220,3015155,6109105,8100,904934,1138076,1105477,491777,3921126,2986308,15631,586926,22927,885318,515645,163920,15924,9768,576868,475321,1105211,29647790,83671,47677,12002,1655221,26543260,161795,3473141,11971,864768,36597,8283,8283,65294,30892,425471,1919840,188924,43185049,6057142,7232790,2328787,1565970,4154566,17514589,139029,8945784,1694216,1305788,325634,18168708,634067,200993,210083,829425,1099896,32910,7456,502016,1063106,244182,64478,86022,4516319,31111,59205,5791282,518793,469885,237563,1170427,225984,613387,167565,979206,8093892,62220,1200580,18491,9031,256148,1434896,389473,1530762,582495,875486,44638,651268,3585767,3691724,6008664,4066674,370235,2910766,18355200,130180,17630,772269,86137,2018890,7682,1321146,1790256,1026368,21237,26565,3968931,12403245,663884,62382,80547,72703,2509366,3666829,55898,17388,35354,4468955,35079,22927,37131,1042872,15072747,165700,5014929,911514,22339004,86637487,925777,239135,9438,523937,10726045,10,11822,322423,253508,11455125,149573250,195966612,101585024,8782549,58048868],"type":"treemap"},{"branchvalues":"total","customdata":[[209327,"Asia"],[207863,"Europe"],[2845073,"Europe"],[4044091,"Europe"],[2459,"Asia"],[33,"Oceania"],[26026,"North America"],[146,"Asia"],[4260,"Asia"],[93,"Africa"],[46606,"Oceania"],[497,"Oceania"],[248,"Africa"],[3130,"Europe"],[61,"Africa"],[393198,"North America"],[137,"Africa"],[21630,"Asia"],[472,"Africa"],[3712,"Africa"],[4430,"North America"],[2627,"Asia"],[809,"Africa"],[1596,"Asia"],[11,"Africa"],[180063,"South America"],[7684,"South America"],[50,"Africa"],[16984,"South America"],[169153,"Europe"],[61722,"Africa"],[1014231,"Africa"],[1022,"South America"],[7557,"Africa"],[1168983,"Asia"],[1223,"North America"],[34514,"Africa"],[158,"Asia"],[0,"Africa"],[545,"Oceania"],[398,"Asia"],[5656,"Europe"],[206,"Africa"],[1766,"Europe"],[57440,"Africa"],[3819,"Europe"],[566,"Asia"],[12033,"Africa"],[4731,"Oceania"],[7170,"Europe"],[48081,"North America"],[75,"Asia"],[4613,"Africa"],[8058,"Africa"],[203743,"Asia"],[17169,"Europe"],[2209,"Europe"],[4570,"Europe"],[483,"Europe"],[1603,"Asia"],[2128,"Africa"],[36191,"Europe"],[26,"Asia"],[2940,"Africa"],[544,"Asia"],[138,"Asia"],[40558,"Europe"],[18769,"Asia"],[7492,"Asia"],[14640,"Asia"],[15027,"Europe"],[2622,"Africa"],[42167,"Africa"],[37,"Africa"],[56,"Africa"],[54929,"Europe"],[1100584,"Europe"],[414481,"Europe"],[65999,"Europe"],[2800,"North America"],[45258,"Europe"],[1217,"North America"],[15833,"South America"],[426644,"South America"],[70,"Africa"],[1046,"Asia"],[327,"North America"],[292,"Europe"],[302772,"North America"],[35698,"North America"],[36126,"Europe"],[1283,"Africa"],[1223,"North America"],[540,"Europe"],[7070,"Europe"],[15,"Africa"],[32,"Africa"],[2273,"Asia"],[26,"Asia"],[2351,"Africa"],[385,"North America"],[849669,"South America"],[226,"Africa"],[1,"Asia"],[48850,"Africa"],[1626,"Europe"],[366340,"Asia"],[2703007,"Asia"],[35252,"Asia"],[11474,"Africa"],[145,"Africa"],[251646,"South America"],[1716,"Asia"],[236299,"Oceania"],[1076,"Africa"],[1049,"Asia"],[53,"Asia"],[12,"Africa"],[70,"Africa"],[975889,"Europe"],[9189,"North America"],[370,"Africa"],[116,"Africa"],[1117,"Asia"],[46,"Africa"],[154450,"Asia"],[2644,"Africa"],[42,"Africa"],[109,"Asia"],[29506,"Asia"],[4906324,"Europe"],[29687,"South America"],[72329,"Europe"],[2526,"Asia"],[464097,"Europe"],[9692,"Asia"],[30790,"South America"],[3848,"Africa"],[3498,"Asia"],[26976,"Asia"],[1227,"Asia"],[25547,"Europe"],[1415,"Africa"],[779412,"Europe"],[27723,"Africa"],[1431724,"Europe"],[14041,"North America"],[77911,"Asia"],[2932133,"North America"],[72020,"South America"],[8,"Asia"],[0,"Asia"],[83199,"Africa"],[3372498,"South America"],[913,"South America"],[282141,"North America"],[556809,"Europe"],[1584,"Africa"],[278838,"Asia"],[188771,"Europe"],[1863,"Asia"],[7,"Africa"],[80602,"Africa"],[631348,"Europe"],[1413,"North America"],[183,"North America"],["(?)","Africa"],["(?)","Asia"],["(?)","Europe"],["(?)","North America"],["(?)","Oceania"],["(?)","South America"]],"domain":{"x":[0.55,1.0],"y":[0.0,1.0]},"hovertemplate":"labels=%{label}<br>Confirmed=%{value}<br>parent=%{parent}<br>id=%{id}<br>Active=%{customdata[0]}<br>Continents=%{customdata[1]}<extra></extra>","ids":["Asia/Lao PDR","Europe/Russia","Europe/Romania","Europe/Portugal","Asia/Philippines","Oceania/Papua New Guinea","North America/Panama","Asia/Palestine","Asia/Pakistan","Africa/Niger","Oceania/New Zealand","Oceania/New Caledonia","Africa/Mozambique","Europe/Moldova","Africa/Gabon","North America/Mexico","Africa/Mauritania","Asia/Malaysia","Africa/Malawi","Africa/Madagascar","North America/Guatemala","Asia/Lebanon","Africa/Zambia","Asia/Kyrgyzstan","Africa/Senegal","South America/Chile","South America/Paraguay","Africa/Togo","South America/Uruguay","Europe/United Kingdom","Africa/Uganda","Africa/Tunisia","South America/Venezuela","Africa/Sierra Leone","Asia/Vietnam","North America/The Bahamas","Africa/Tanzania","Asia/Uzbekistan","Africa/W. Sahara","Oceania/Vanuatu","Asia/Sri Lanka","Europe/Sweden","Africa/Swaziland","Europe/Slovakia","Africa/Sudan","Europe/Slovenia","Asia/Yemen","Africa/Somalia","Oceania/Solomon Is.","Europe/Luxembourg","North America/Jamaica","Asia/Kazakhstan","Africa/Libya","Africa/Lesotho","Asia/Japan","Europe/Lithuania","Europe/Latvia","Europe/Serbia","Europe/Montenegro","Asia/Myanmar","Africa/Namibia","Europe/Netherlands","Asia/Timor-Leste","Africa/Nigeria","Asia/Oman","Asia/Syria","Europe/Switzerland","Asia/Israel","Asia/Saudi Arabia","Asia/United Arab Emirates","Europe/Ireland","Africa/Zimbabwe","Africa/Burundi","Africa/Eq. Guinea","Africa/C?te d'Ivoire","Europe/Estonia","Europe/Finland","Europe/France","Europe/Belgium","North America/Dominican Rep.","Europe/Greece","North America/El Salvador","South America/Bolivia","South America/Brazil","Africa/Guinea-Bissau","Asia/Iraq","North America/Haiti","Europe/Albania","North America/Canada","North America/Costa Rica","Europe/Austria","Africa/Benin","North America/Bahamas","Europe/Czech Rep.","Europe/Denmark","Africa/Djibouti","Africa/Burkina Faso","Asia/Brunei","Asia/East Timor","Africa/Chad","North America/Bermuda","South America/Ecuador","Africa/Cameroon","Asia/Cambodia","Africa/Egypt","Europe/Croatia","Asia/Cyprus","Asia/China","Asia/Iran","Africa/Ethiopia","Africa/Angola","South America/Argentina","Asia/Armenia","Oceania/Australia","Africa/Kenya","Asia/Kuwait","Asia/Azerbaijan","Africa/Eritrea","Africa/Guinea Bissau","Europe/Belarus","North America/Greenland","Africa/Ghana","Africa/Mali","Asia/Georgia","Africa/Gambia","Asia/Mongolia","Africa/Morocco","Africa/Guinea","Asia/Nepal","Asia/Thailand","Europe/Ukraine","South America/Suriname","Europe/Bulgaria","Asia/Turkey","Europe/Spain","Asia/Afghanistan","South America/Colombia","Africa/S. Sudan","Asia/Indonesia","Asia/India","Asia/Jordan","Europe/Hungary","Africa/Liberia","Europe/Germany","Africa/South Africa","Europe/Norway","North America/Nicaragua","Asia/Singapore","North America/United States","South America/French Guiana","Asia/Bhutan","Asia/Tajikistan","Africa/Rwanda","South America/Peru","South America/Guyana","North America/Honduras","Europe/Poland","Africa/Botswana","Asia/Korea","Europe/Iceland","Asia/Qatar","Africa/Comoros","Africa/Algeria","Europe/Italy","North America/Belize","North America/Cuba","Africa","Asia","Europe","North America","Oceania","South America"],"labels":["Lao PDR","Russia","Romania","Portugal","Philippines","Papua New Guinea","Panama","Palestine","Pakistan","Niger","New Zealand","New Caledonia","Mozambique","Moldova","Gabon","Mexico","Mauritania","Malaysia","Malawi","Madagascar","Guatemala","Lebanon","Zambia","Kyrgyzstan","Senegal","Chile","Paraguay","Togo","Uruguay","United Kingdom","Uganda","Tunisia","Venezuela","Sierra Leone","Vietnam","The Bahamas","Tanzania","Uzbekistan","W. Sahara","Vanuatu","Sri Lanka","Sweden","Swaziland","Slovakia","Sudan","Slovenia","Yemen","Somalia","Solomon Is.","Luxembourg","Jamaica","Kazakhstan","Libya","Lesotho","Japan","Lithuania","Latvia","Serbia","Montenegro","Myanmar","Namibia","Netherlands","Timor-Leste","Nigeria","Oman","Syria","Switzerland","Israel","Saudi Arabia","United Arab Emirates","Ireland","Zimbabwe","Burundi","Eq. Guinea","C?te d'Ivoire","Estonia","Finland","France","Belgium","Dominican Rep.","Greece","El Salvador","Bolivia","Brazil","Guinea-Bissau","Iraq","Haiti","Albania","Canada","Costa Rica","Austria","Benin","Bahamas","Czech Rep.","Denmark","Djibouti","Burkina Faso","Brunei","East Timor","Chad","Bermuda","Ecuador","Cameroon","Cambodia","Egypt","Croatia","Cyprus","China","Iran","Ethiopia","Angola","Argentina","Armenia","Australia","Kenya","Kuwait","Azerbaijan","Eritrea","Guinea Bissau","Belarus","Greenland","Ghana","Mali","Georgia","Gambia","Mongolia","Morocco","Guinea","Nepal","Thailand","Ukraine","Suriname","Bulgaria","Turkey","Spain","Afghanistan","Colombia","S. Sudan","Indonesia","India","Jordan","Hungary","Liberia","Germany","South Africa","Norway","Nicaragua","Singapore","United States","French Guiana","Bhutan","Tajikistan","Rwanda","Peru","Guyana","Honduras","Poland","Botswana","Korea","Iceland","Qatar","Comoros","Algeria","Italy","Belize","Cuba","Africa","Asia","Europe","North America","Oceania","South America"],"marker":{"colors":["midnightblue","firebrick","firebrick","firebrick","midnightblue","darkorange","yellow","midnightblue","midnightblue","plum","darkorange","darkorange","plum","firebrick","plum","yellow","plum","midnightblue","plum","plum","yellow","midnightblue","plum","midnightblue","plum","seagreen","seagreen","plum","seagreen","firebrick","plum","plum","seagreen","plum","midnightblue","yellow","plum","midnightblue","plum","darkorange","midnightblue","firebrick","plum","firebrick","plum","firebrick","midnightblue","plum","darkorange","firebrick","yellow","midnightblue","plum","plum","midnightblue","firebrick","firebrick","firebrick","firebrick","midnightblue","plum","firebrick","midnightblue","plum","midnightblue","midnightblue","firebrick","midnightblue","midnightblue","midnightblue","firebrick","plum","plum","plum","plum","firebrick","firebrick","firebrick","firebrick","yellow","firebrick","yellow","seagreen","seagreen","plum","midnightblue","yellow","firebrick","yellow","yellow","firebrick","plum","yellow","firebrick","firebrick","plum","plum","midnightblue","midnightblue","plum","yellow","seagreen","plum","midnightblue","plum","firebrick","midnightblue","midnightblue","midnightblue","plum","plum","seagreen","midnightblue","darkorange","plum","midnightblue","midnightblue","plum","plum","firebrick","yellow","plum","plum","midnightblue","plum","midnightblue","plum","plum","midnightblue","midnightblue","firebrick","seagreen","firebrick","midnightblue","firebrick","midnightblue","seagreen","plum","midnightblue","midnightblue","midnightblue","firebrick","plum","firebrick","plum","firebrick","yellow","midnightblue","yellow","seagreen","midnightblue","midnightblue","plum","seagreen","seagreen","yellow","firebrick","plum","midnightblue","firebrick","midnightblue","plum","plum","firebrick","yellow","yellow","plum","midnightblue","firebrick","yellow","darkorange","seagreen"]},"name":"","parents":["Asia","Europe","Europe","Europe","Asia","Oceania","North America","Asia","Asia","Africa","Oceania","Oceania","Africa","Europe","Africa","North America","Africa","Asia","Africa","Africa","North America","Asia","Africa","Asia","Africa","South America","South America","Africa","South America","Europe","Africa","Africa","South America","Africa","Asia","North America","Africa","Asia","Africa","Oceania","Asia","Europe","Africa","Europe","Africa","Europe","Asia","Africa","Oceania","Europe","North America","Asia","Africa","Africa","Asia","Europe","Europe","Europe","Europe","Asia","Africa","Europe","Asia","Africa","Asia","Asia","Europe","Asia","Asia","Asia","Europe","Africa","Africa","Africa","Africa","Europe","Europe","Europe","Europe","North America","Europe","North America","South America","South America","Africa","Asia","North America","Europe","North America","North America","Europe","Africa","North America","Europe","Europe","Africa","Africa","Asia","Asia","Africa","North America","South America","Africa","Asia","Africa","Europe","Asia","Asia","Asia","Africa","Africa","South America","Asia","Oceania","Africa","Asia","Asia","Africa","Africa","Europe","North America","Africa","Africa","Asia","Africa","Asia","Africa","Africa","Asia","Asia","Europe","South America","Europe","Asia","Europe","Asia","South America","Africa","Asia","Asia","Asia","Europe","Africa","Europe","Africa","Europe","North America","Asia","North America","South America","Asia","Asia","Africa","South America","South America","North America","Europe","Africa","Asia","Europe","Asia","Africa","Africa","Europe","North America","North America","","","","","",""],"values":[210083,18355200,2910766,4066674,3691724,44638,875486,582495,1530762,9031,1200580,62220,225984,518793,47677,5791282,59205,4516319,86022,64478,864768,1099896,322423,200993,86137,3756220,651268,37131,925777,22339004,165700,1042872,523937,7682,10726045,35079,35354,239135,10,9438,663884,2509366,72703,1790256,62382,1026368,11822,26565,21237,244182,139029,1305788,502016,32910,8945784,1063106,829425,2018890,237563,613387,167565,8093892,22927,256148,389473,55898,3666829,4154566,772269,911514,1565970,253508,42205,15924,82309,576868,1105211,29647790,4158754,586926,3473141,163920,910228,31195118,8283,2328787,30892,276415,3883584,904934,4269277,26952,35079,3921126,2986308,15631,20853,150781,22927,7418,15085,885318,119947,136262,515645,1138076,491777,3015155,7232790,475321,99194,9276618,423006,7444436,325634,634067,792785,9768,8283,982867,11971,161795,31111,1655221,12002,469885,1170427,36597,979206,4468955,5014929,80547,1166148,15072747,12403245,180615,6109105,17630,6057142,43185049,1694216,1919840,7456,26543260,3968931,1434896,18491,1321146,86637487,83671,59636,17388,130180,3585767,65294,425471,6008664,308126,18168708,188924,370235,8100,265900,17514589,60063,1105477,11455125,149573250,195966612,101585024,8782549,58048868],"type":"sunburst"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"height":600,"title":{"text":"The Confirmed cases of Covid-19 Throughout the World"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('d792bcae-ae52-457e-a40b-1c2cef2a35f2');
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


<div>                            <div id="833ce16d-123d-45f0-8473-89c368f19d29" class="plotly-graph-div" style="height:980px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("833ce16d-123d-45f0-8473-89c368f19d29")) {                    Plotly.newPlot(                        "833ce16d-123d-45f0-8473-89c368f19d29",                        [{"marker":{"color":"maroon"},"name":"Confirmed cases","text":[11455125.0,149573250.0,195966612.0,101585024.0,8782549.0,58048868.0,525411428.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[11455125,149573250,195966612,101585024,8782549,58048868,525411428],"type":"bar","xaxis":"x","yaxis":"y","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"darkorange"},"name":"Active cases","text":[1523117.0,5357664.0,19163083.0,4056480.0,288711.0,5255453.0,35644508.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[1523117,5357664,19163083,4056480,288711,5255453,35644508],"type":"bar","xaxis":"x2","yaxis":"y2","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"gray"},"name":"Death cases","text":[250839.0,1402484.0,1817230.0,1469666.0,11068.0,1299660.0,6250947.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[250839,1402484,1817230,1469666,11068,1299660,6250947],"type":"bar","xaxis":"x3","yaxis":"y3","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"},{"marker":{"color":"lightskyblue"},"name":"Recovered cases","text":[9681169.0,142813102.0,174986299.0,96058878.0,8482770.0,51493755.0,483515973.0],"x":["Africa","Asia","Europe","North America","Oceania","South America"],"y":[9681169,142813102,174986299,96058878,8482770,51493755,483515973],"type":"bar","xaxis":"x4","yaxis":"y4","textfont":{"color":"white","family":"Arial Black","size":14},"textposition":"auto","texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,0.45],"title":{"text":"Continents"}},"yaxis":{"anchor":"x","domain":[0.625,1.0],"title":{"text":"Total Cases"}},"xaxis2":{"anchor":"y2","domain":[0.55,1.0],"title":{"text":"Continents"}},"yaxis2":{"anchor":"x2","domain":[0.625,1.0],"title":{"text":"Total Cases"}},"xaxis3":{"anchor":"y3","domain":[0.0,0.45],"title":{"text":"Total Cases"}},"yaxis3":{"anchor":"x3","domain":[0.0,0.375],"title":{"text":"Continents"}},"xaxis4":{"anchor":"y4","domain":[0.55,1.0],"title":{"text":"Total Cases"}},"yaxis4":{"anchor":"x4","domain":[0.0,0.375],"title":{"text":"Continents"}},"annotations":[{"font":{"size":16},"showarrow":false,"text":"Confirmed cases","x":0.225,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Active cases","x":0.775,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Death cases","x":0.225,"xanchor":"center","xref":"paper","y":0.375,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Recovered Cases","x":0.775,"xanchor":"center","xref":"paper","y":0.375,"yanchor":"bottom","yref":"paper"}],"height":980,"showlegend":false,"title":{"text":"The Situation of Covid-19 by Continents"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('833ce16d-123d-45f0-8473-89c368f19d29');
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


<div>                            <div id="a5410bfc-8590-4e1d-9285-7cdfb82454e0" class="plotly-graph-div" style="height:980px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("a5410bfc-8590-4e1d-9285-7cdfb82454e0")) {                    Plotly.newPlot(                        "a5410bfc-8590-4e1d-9285-7cdfb82454e0",                        [{"alignmentgroup":"True","customdata":[["North America"],["Asia"],["South America"],["Europe"],["Europe"],["Europe"],["Europe"],["Asia"],["Europe"],["Asia"]],"hovertemplate":"Confirmed Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#8C001A","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[86637487.0,43185049.0,31195118.0,29647790.0,26543260.0,22339004.0,18355200.0,18168708.0,17514589.0,15072747.0],"textposition":"auto","x":[86637487,43185049,31195118,29647790,26543260,22339004,18355200,18168708,17514589,15072747],"xaxis":"x","y":["United States","India","Brazil","France","Germany","United Kingdom","Russia","Korea","Italy","Turkey"],"yaxis":"y","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["Europe"],["Europe"],["South America"],["North America"],["Europe"],["Asia"],["Europe"],["Asia"],["Europe"],["Africa"]],"hovertemplate":"Active Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#EAC117","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[4906324.0,4044091.0,3372498.0,2932133.0,2845073.0,2703007.0,1431724.0,1168983.0,1100584.0,1014231.0],"textposition":"auto","x":[4906324,4044091,3372498,2932133,2845073,2703007,1431724,1168983,1100584,1014231],"xaxis":"x2","y":["Ukraine","Portugal","Peru","United States","Romania","China","Norway","Vietnam","Finland","Tunisia"],"yaxis":"y2","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["North America"],["South America"],["Asia"],["Europe"],["North America"],["South America"],["Europe"],["Europe"],["Asia"],["Europe"]],"hovertemplate":"Deaths Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#393e46","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[1033830.0,667106.0,524708.0,379584.0,325017.0,213269.0,178866.0,167019.0,156622.0,148464.0],"textposition":"auto","x":[1033830,667106,524708,379584,325017,213269,178866,167019,156622,148464],"xaxis":"x3","y":["United States","Brazil","India","Russia","Mexico","Peru","United Kingdom","Italy","Indonesia","France"],"yaxis":"y3","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"},{"alignmentgroup":"True","customdata":[["North America"],["Asia"],["South America"],["Europe"],["Europe"],["Europe"],["Asia"],["Europe"],["Europe"],["Asia"]],"hovertemplate":"Recovered Cases=%{text}<br>Country=%{y}<br>Continents=%{customdata[0]}<extra></extra>","legendgroup":"","marker":{"color":"#99a9ff","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"text":[82671524.0,42633365.0,30101368.0,29084845.0,25624100.0,21990985.0,17865591.0,17767753.0,16716222.0,14971256.0],"textposition":"auto","x":[82671524,42633365,30101368,29084845,25624100,21990985,17865591,17767753,16716222,14971256],"xaxis":"x4","y":["United States","India","Brazil","France","Germany","United Kingdom","Korea","Russia","Italy","Turkey"],"yaxis":"y4","type":"bar","textfont":{"color":"white","family":"Arial","size":14},"texttemplate":"%{text:,.2s}"}],                        {"template":{"data":{"barpolar":[{"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"bar":[{"error_x":{"color":"#f2f5fa"},"error_y":{"color":"#f2f5fa"},"marker":{"line":{"color":"rgb(17,17,17)","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"carpet":[{"aaxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"baxis":{"endlinecolor":"#A2B1C6","gridcolor":"#506784","linecolor":"#506784","minorgridcolor":"#506784","startlinecolor":"#A2B1C6"},"type":"carpet"}],"choropleth":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"choropleth"}],"contourcarpet":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"contourcarpet"}],"contour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"contour"}],"heatmapgl":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmapgl"}],"heatmap":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"heatmap"}],"histogram2dcontour":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2dcontour"}],"histogram2d":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"histogram2d"}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"mesh3d":[{"colorbar":{"outlinewidth":0,"ticks":""},"type":"mesh3d"}],"parcoords":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"parcoords"}],"pie":[{"automargin":true,"type":"pie"}],"scatter3d":[{"line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatter3d"}],"scattercarpet":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattercarpet"}],"scattergeo":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattergeo"}],"scattergl":[{"marker":{"line":{"color":"#283442"}},"type":"scattergl"}],"scattermapbox":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scattermapbox"}],"scatterpolargl":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolargl"}],"scatterpolar":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterpolar"}],"scatter":[{"marker":{"line":{"color":"#283442"}},"type":"scatter"}],"scatterternary":[{"marker":{"colorbar":{"outlinewidth":0,"ticks":""}},"type":"scatterternary"}],"surface":[{"colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"type":"surface"}],"table":[{"cells":{"fill":{"color":"#506784"},"line":{"color":"rgb(17,17,17)"}},"header":{"fill":{"color":"#2a3f5f"},"line":{"color":"rgb(17,17,17)"}},"type":"table"}]},"layout":{"annotationdefaults":{"arrowcolor":"#f2f5fa","arrowhead":0,"arrowwidth":1},"autotypenumbers":"strict","coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]],"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]},"colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#f2f5fa"},"geo":{"bgcolor":"rgb(17,17,17)","lakecolor":"rgb(17,17,17)","landcolor":"rgb(17,17,17)","showlakes":true,"showland":true,"subunitcolor":"#506784"},"hoverlabel":{"align":"left"},"hovermode":"closest","mapbox":{"style":"dark"},"paper_bgcolor":"rgb(17,17,17)","plot_bgcolor":"rgb(17,17,17)","polar":{"angularaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","radialaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"scene":{"xaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"yaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"},"zaxis":{"backgroundcolor":"rgb(17,17,17)","gridcolor":"#506784","gridwidth":2,"linecolor":"#506784","showbackground":true,"ticks":"","zerolinecolor":"#C8D4E3"}},"shapedefaults":{"line":{"color":"#f2f5fa"}},"sliderdefaults":{"bgcolor":"#C8D4E3","bordercolor":"rgb(17,17,17)","borderwidth":1,"tickwidth":0},"ternary":{"aaxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"baxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""},"bgcolor":"rgb(17,17,17)","caxis":{"gridcolor":"#506784","linecolor":"#506784","ticks":""}},"title":{"x":0.05},"updatemenudefaults":{"bgcolor":"#506784","borderwidth":0},"xaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2},"yaxis":{"automargin":true,"gridcolor":"#283442","linecolor":"#506784","ticks":"","title":{"standoff":15},"zerolinecolor":"#283442","zerolinewidth":2}}},"xaxis":{"anchor":"y","domain":[0.0,0.43]},"yaxis":{"anchor":"x","domain":[0.55,1.0],"autorange":"reversed"},"xaxis2":{"anchor":"y2","domain":[0.5700000000000001,1.0]},"yaxis2":{"anchor":"x2","domain":[0.55,1.0],"autorange":"reversed"},"xaxis3":{"anchor":"y3","domain":[0.0,0.43]},"yaxis3":{"anchor":"x3","domain":[0.0,0.45],"autorange":"reversed"},"xaxis4":{"anchor":"y4","domain":[0.5700000000000001,1.0]},"yaxis4":{"anchor":"x4","domain":[0.0,0.45],"autorange":"reversed"},"annotations":[{"font":{"size":16},"showarrow":false,"text":"Confirmed Cases","x":0.215,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Active Cases","x":0.785,"xanchor":"center","xref":"paper","y":1.0,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Deaths cases","x":0.215,"xanchor":"center","xref":"paper","y":0.45,"yanchor":"bottom","yref":"paper"},{"font":{"size":16},"showarrow":false,"text":"Recovered Cases","x":0.785,"xanchor":"center","xref":"paper","y":0.45,"yanchor":"bottom","yref":"paper"}],"height":980,"width":1000,"title":{"text":"The Situation of Covid-19 by Top 10 Countries"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('a5410bfc-8590-4e1d-9285-7cdfb82454e0');
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


<div>                            <div id="449304cb-9e28-4be9-b33b-9fe03fc11c72" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("449304cb-9e28-4be9-b33b-9fe03fc11c72")) {                    Plotly.newPlot(                        "449304cb-9e28-4be9-b33b-9fe03fc11c72",                        [{"coloraxis":"coloraxis","customdata":[["United States","North America",82671524,1033830,2932133],["India","Asia",42633365,524708,26976],["Brazil","South America",30101368,667106,426644],["France","Europe",29084845,148464,414481],["Germany","Europe",25624100,139748,779412],["United Kingdom","Europe",21990985,178866,169153],["Russia","Europe",17767753,379584,207863],["Korea","Asia",17865591,24279,278838],["Italy","Europe",16716222,167019,631348],["Turkey","Asia",14971256,98965,2526],["Spain","Europe",11832351,106797,464097],["Vietnam","Asia",9513981,43081,1168983],["Argentina","South America",8895999,128973,251646],["Japan","Asia",8711289,30752,203743],["Netherlands","Europe",8035375,22326,36191],["Australia","Oceania",7199382,8755,236299],["Iran","Asia",7056206,141332,35252],["Colombia","South America",5938448,139867,30790],["Indonesia","Asia",5897022,156622,3498],["Poland","Europe",5335506,116349,556809],["Mexico","North America",5073067,325017,393198],["Ukraine","Europe",0,108605,4906324],["Malaysia","Asia",4458999,35690,21630],["Thailand","Asia",4409248,30201,29506],["Austria","Europe",4214477,18674,36126],["Belgium","Europe",4060987,31768,65999],["Israel","Asia",4124933,10864,18769],["Portugal","Europe",0,22583,4044091],["South Africa","Africa",3839858,101350,27723],["Czech Rep.","Europe",3880292,40294,540],["Canada","North America",3539548,41264,302772],["Chile","South America",3518166,57991,180063],["Philippines","Asia",3628809,60456,2459],["Switzerland","Europe",3612304,13967,40558],["Peru","South America",0,213269,3372498],["Greece","Europe",3397956,29927,45258],["Denmark","Europe",2972862,6376,7070],["Romania","Europe",0,65693,2845073],["Sweden","Europe",2484729,18981,5656],["Iraq","Asia",2302520,25221,1046],["Serbia","Europe",1998229,16091,4570],["Hungary","Europe",1847746,46547,25547],["Slovakia","Europe",1768386,20104,1766],["Jordan","Asia",1678941,14048,1227],["Georgia","Asia",1637293,16811,1117],["Ireland","Europe",1543596,7347,15027],["Pakistan","Asia",1496123,30379,4260],["Norway","Europe",0,3172,1431724],["Singapore","Asia",1241842,1393,77911],["Kazakhstan","Asia",1292051,13662,75],["New Zealand","Oceania",1152788,1186,46606],["Morocco","Africa",1151703,16080,2644],["Bulgaria","Europe",1056637,37182,72329],["Croatia","Europe",1120447,16003,1626],["Cuba","North America",1096765,8529,183],["Finland","Europe",0,4627,1100584],["Lebanon","Asia",1086833,10436,2627],["Lithuania","Europe",1036789,9148,17169],["Tunisia","Africa",0,28641,1014231],["Slovenia","Europe",1015907,6642,3819],["Belarus","Europe",0,6978,975889],["Nepal","Asia",967145,11952,109],["Uruguay","South America",901555,7238,16984],["United Arab Emirates","Asia",894569,2305,14640],["Bolivia","South America",872446,21949,15833],["Costa Rica","North America",860711,8525,35698],["Ecuador","South America",0,35649,849669],["Panama","North America",841184,8276,26026],["Guatemala","North America",842114,18224,4430],["Latvia","Europe",821386,5830,2209],["Azerbaijan","Asia",783019,9713,53],["Saudi Arabia","Asia",755619,9158,7492],["Sri Lanka","Asia",646968,16518,398],["Paraguay","South America",624673,18911,7684],["Kuwait","Asia",630463,2555,1049],["Myanmar","Asia",592350,19434,1603],["Dominican Rep.","North America",579749,4377,2800],["Palestine","Asia",576993,5356,146],["Estonia","Europe",519365,2574,54929],["Venezuela","South America",517193,5722,1022],["Moldova","Europe",504142,11521,3130],["Egypt","Africa",442182,24613,48850],["Libya","Africa",490973,6430,4613],["Cyprus","Asia",124370,1067,366340],["Ethiopia","Africa",456332,7515,11474],["Mongolia","Asia",313256,2179,154450],["Honduras","North America",132430,10900,282141],["Armenia","Asia",412661,8629,1716],["Oman","Asia",384669,4260,544],["Qatar","Asia",367695,677,1863],["Kenya","Africa",318907,5651,1076],["Zambia","Africa",317626,3988,809],["Botswana","Africa",303845,2697,1584],["Albania","Europe",272626,3497,292],["Algeria","Africa",178423,6875,80602],["Nigeria","Africa",250065,3143,2940],["Zimbabwe","Africa",245373,5513,2622],["Luxembourg","Europe",235939,1073,7170],["Uzbekistan","Asia",237340,1637,158],["Montenegro","Europe",234360,2720,483],["Mozambique","Africa",223532,2204,248],["Lao PDR","Asia",0,756,209327],["Kyrgyzstan","Asia",196406,2991,1596],["Iceland","Europe",0,153,188771],["Afghanistan","Asia",163215,7708,9692],["Namibia","Africa",161397,4040,2128],["Uganda","Africa",100378,3600,61722],["El Salvador","North America",158570,4133,1217],["Ghana","Africa",159980,1445,370],["Brunei","Asia",148285,223,2273],["Jamaica","North America",87874,3074,48081],["Cambodia","Asia",133205,3056,1],["Rwanda","Africa",45522,1459,83199],["Cameroon","Africa",117791,1930,226],["Angola","Africa",97149,1900,145],["Senegal","Africa",84159,1967,11],["Malawi","Africa",82908,2642,472],["French Guiana","South America",11254,397,72020],["C?te d'Ivoire","Africa",81454,799,56],["Suriname","South America",49510,1350,29687],["Swaziland","Africa",71087,1410,206],["Guyana","South America",63143,1238,913],["Madagascar","Africa",59370,1396,3712],["Sudan","Africa",0,4942,57440],["New Caledonia","Oceania",61410,313,497],["Belize","North America",57972,678,1413],["Bhutan","Asia",59607,21,8],["Mauritania","Africa",58086,982,137],["Syria","Asia",52610,3150,138],["Gabon","Africa",47312,304,61],["Papua New Guinea","Oceania",43954,651,33],["Burundi","Africa",0,38,42167],["Togo","Africa",36808,273,50],["Guinea","Africa",36113,442,42],["Tanzania","Africa",0,840,34514],["Bahamas","North America",33046,810,1223],["The Bahamas","North America",33046,810,1223],["Lesotho","Africa",24155,697,8058],["Mali","Africa",30260,735,116],["Haiti","North America",29730,835,327],["Benin","Africa",25506,163,1283],["Somalia","Africa",13182,1350,12033],["Timor-Leste","Asia",22769,132,26],["East Timor","Asia",22769,132,26],["Solomon Is.","Oceania",16357,149,4731],["Burkina Faso","Africa",20439,382,32],["Nicaragua","North America",4225,225,14041],["S. Sudan","Africa",13644,138,3848],["Tajikistan","Asia",17264,124,0],["Eq. Guinea","Africa",15704,183,37],["Djibouti","Africa",15427,189,15],["Bermuda","North America",14562,138,385],["Gambia","Africa",11591,365,46],["Greenland","North America",2761,21,9189],["Yemen","Asia",9107,2149,566],["Eritrea","Africa",9653,103,12],["Vanuatu","Oceania",8879,14,545],["Niger","Africa",8628,310,93],["Guinea-Bissau","Africa",8042,171,70],["Guinea Bissau","Africa",8042,171,70],["Comoros","Africa",7933,160,7],["Sierra Leone","Africa",0,125,7557],["Liberia","Africa",5747,294,1415],["Chad","Africa",4874,193,2351],["W. Sahara","Africa",9,1,0],["China","Asia",294446,17702,2703007]],"geo":"geo","hovertemplate":"Country=%{customdata[0]}<br>Continents=%{customdata[1]}<br>Recovered=%{customdata[2]}<br>Deaths=%{customdata[3]}<br>Active=%{customdata[4]}<br>Confirmed=%{z}<extra></extra>","locationmode":"country names","locations":["United States","India","Brazil","France","Germany","United Kingdom","Russia","Korea","Italy","Turkey","Spain","Vietnam","Argentina","Japan","Netherlands","Australia","Iran","Colombia","Indonesia","Poland","Mexico","Ukraine","Malaysia","Thailand","Austria","Belgium","Israel","Portugal","South Africa","Czech Rep.","Canada","Chile","Philippines","Switzerland","Peru","Greece","Denmark","Romania","Sweden","Iraq","Serbia","Hungary","Slovakia","Jordan","Georgia","Ireland","Pakistan","Norway","Singapore","Kazakhstan","New Zealand","Morocco","Bulgaria","Croatia","Cuba","Finland","Lebanon","Lithuania","Tunisia","Slovenia","Belarus","Nepal","Uruguay","United Arab Emirates","Bolivia","Costa Rica","Ecuador","Panama","Guatemala","Latvia","Azerbaijan","Saudi Arabia","Sri Lanka","Paraguay","Kuwait","Myanmar","Dominican Rep.","Palestine","Estonia","Venezuela","Moldova","Egypt","Libya","Cyprus","Ethiopia","Mongolia","Honduras","Armenia","Oman","Qatar","Kenya","Zambia","Botswana","Albania","Algeria","Nigeria","Zimbabwe","Luxembourg","Uzbekistan","Montenegro","Mozambique","Lao PDR","Kyrgyzstan","Iceland","Afghanistan","Namibia","Uganda","El Salvador","Ghana","Brunei","Jamaica","Cambodia","Rwanda","Cameroon","Angola","Senegal","Malawi","French Guiana","C?te d'Ivoire","Suriname","Swaziland","Guyana","Madagascar","Sudan","New Caledonia","Belize","Bhutan","Mauritania","Syria","Gabon","Papua New Guinea","Burundi","Togo","Guinea","Tanzania","Bahamas","The Bahamas","Lesotho","Mali","Haiti","Benin","Somalia","Timor-Leste","East Timor","Solomon Is.","Burkina Faso","Nicaragua","S. Sudan","Tajikistan","Eq. Guinea","Djibouti","Bermuda","Gambia","Greenland","Yemen","Eritrea","Vanuatu","Niger","Guinea-Bissau","Guinea Bissau","Comoros","Sierra Leone","Liberia","Chad","W. Sahara","China"],"name":"","z":[86637487,43185049,31195118,29647790,26543260,22339004,18355200,18168708,17514589,15072747,12403245,10726045,9276618,8945784,8093892,7444436,7232790,6109105,6057142,6008664,5791282,5014929,4516319,4468955,4269277,4158754,4154566,4066674,3968931,3921126,3883584,3756220,3691724,3666829,3585767,3473141,2986308,2910766,2509366,2328787,2018890,1919840,1790256,1694216,1655221,1565970,1530762,1434896,1321146,1305788,1200580,1170427,1166148,1138076,1105477,1105211,1099896,1063106,1042872,1026368,982867,979206,925777,911514,910228,904934,885318,875486,864768,829425,792785,772269,663884,651268,634067,613387,586926,582495,576868,523937,518793,515645,502016,491777,475321,469885,425471,423006,389473,370235,325634,322423,308126,276415,265900,256148,253508,244182,239135,237563,225984,210083,200993,188924,180615,167565,165700,163920,161795,150781,139029,136262,130180,119947,99194,86137,86022,83671,82309,80547,72703,65294,64478,62382,62220,60063,59636,59205,55898,47677,44638,42205,37131,36597,35354,35079,35079,32910,31111,30892,26952,26565,22927,22927,21237,20853,18491,17630,17388,15924,15631,15085,12002,11971,11822,9768,9438,9031,8283,8283,8100,7682,7456,7418,10,3015155],"type":"choropleth"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"geo":{"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"center":{}},"coloraxis":{"colorbar":{"title":{"text":"Confirmed"}},"colorscale":[[0.0,"rgb(255,255,204)"],[0.125,"rgb(255,237,160)"],[0.25,"rgb(254,217,118)"],[0.375,"rgb(254,178,76)"],[0.5,"rgb(253,141,60)"],[0.625,"rgb(252,78,42)"],[0.75,"rgb(227,26,28)"],[0.875,"rgb(189,0,38)"],[1.0,"rgb(128,0,38)"]]},"legend":{"tracegroupgap":0},"title":{"text":"Covid-19 Situation In The World"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('449304cb-9e28-4be9-b33b-9fe03fc11c72');
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


