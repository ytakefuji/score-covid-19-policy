# score-covid-19-policy
<pre>
import requests,re
import pandas as pd

url='https://www.worldometers.info/world-population/population-by-country/'

print('scraping population...')
page=requests.get(url)
df = pd.read_html(page.text)[0]
df.columns.values[1]='Country'
df.columns.values[2]='Population'
#df = pd.read_html(page.text,flavor='html5lib')[0]
df.to_csv('pop.csv')
print('pop.csv was created')
#
#
# deaths.csv or total_deaths.csv
'''
from urllib.request import Request, urlopen
url='https://www.worldometers.info/coronavirus/#nav-today/'
print('scraping deaths informationi...')
req = Request(url, headers={'User-Agent': 'Firefox/76.0.1'})
page = re.sub(r'<.*?>', lambda g: g.group(0).upper(), urlopen(req).read().decode('utf-8') )
df = pd.read_html(page)[0]
df.to_csv('deaths.csv')
print('deaths.csv was created')
'''
print('countries file was read...')
d=open('countries').read().strip()
print('scoring the following countries...')
d=d.split(',')
print(d)

dd=pd.DataFrame(
 { "country": d,
  "deaths": range(len(d)),
  "population": range(len(d)),
  "score": range(len(d)),
 })

# deaths information can be downloaded from the following site:
# https://github.com/owid/covid-19-data/raw/master/public/data/jhu/total_deaths.csv
print('downloading total_deaths.csv file')
import subprocess as sp
sp.call("wget https://github.com/owid/covid-19-data/raw/master/public/data/jhu/total_deaths.csv",shell=True)

p=pd.read_csv('total_deaths.csv')
pp=pd.read_csv('pop.csv')

print('calculating scores of countries')
print('\n')
print('result will be result.csv')

for i in d:
# print(p[i][len(p)-1])
 dd.loc[dd.country==i,'deaths']=int(p[i][len(p)-1])
# print(pp.loc[pp.Country==i,'Population'])
 dd.loc[dd.country==i,'population']=int(pp.loc[pp.Country==i,'Population']/1000000)
 dd.loc[dd.country==i,'score']=int(dd.loc[dd.country==i,'deaths']/dd.loc[dd.country==i,'population'])
dd=dd.sort_values(by=['score'])
print(dd)
dd.to_csv('result.csv')
</pre>
