# score-covid-19-policy
The paper was published in Healthcare Analytics (2021):
SCORECOVID: A Python Package Index for scoring the individual policies against COVID-19

https://doi.org/10.1016/j.health.2021.100005

The paper was published in Journal of Infection and Public Health (2021):
Technological forecasting plays a key role in mitigating the pandemic

https://doi.org/10.1016/j.jiph.2021.09.010

scorecovid has been downloaded by 8698 users worldwide.

You may create a file called countries for scoring individual policies of countries.

Country names must be separated by commas.

VSL_data.csv was created by Mr. Kiuchi using data of the following paper:
Viscusi, W., & Masterman, C. (2017). Income Elasticities and Global Values of a Statistical Life. Journal of Benefit-Cost Analysis, 8(2), 226-250. doi:10.1017/bca.2017.12

Taiwan VSL: https://www.cier.edu.tw/public/Data/2018-5.pdf

Taiwan GNI: https://eng.stat.gov.tw/point.asp?index=1

<pre>
$ cat countries
Iceland,South Korea,India,Brazil,France,New Zealand,Taiwan,Sweden,Japan,United States,Canada,United Kingdom,Israel
</pre>

# How to install and run scorecovid
<pre>
$ pip install scorecovid

$ scorecovid
score is created in result.csv
date is  2022-03-19
                deaths  population   score
country
New Zealand        151        4.82    31.3
Taiwan             853       23.82    35.8
Japan            27056      126.48   213.9
Australia         5725       25.50   224.5
South Korea      12428       51.27   242.4
Iceland             91        0.34   267.6
India           516479     1380.00   374.3
Canada           37204       37.74   985.8
Israel           10419        8.66  1203.1
Germany         126920       83.78  1514.9
Sweden           18053       10.10  1787.4
France          140972       65.27  2159.8
United Kingdom  163658       67.89  2410.6
United States   971087      331.00  2933.8
Brazil          657389      212.56  3092.7

</pre>

# Background
There are ways to mitigate pandemics and prevent infections.
We need to learn good policies from countries with high scores.
Scoring tells us the performance of health policies in individual 
countries that are coping well with pandemics and those that are not.

# How to handle errors
<pre>
For example, "ImportError: lxml not found, please install it"
$ pip install lxml

</pre>

# How scorecovid can score indivisual policies against covid-19?
Scoring of individual policies against covid-19 is calculated by dividing the total number of deaths due to covid-19 by the population (in millions).
The most recent values for total number of deaths and population are scraped from the Web sites.

# Exercises for students
1. Calculate economic loss of a country due to the total number of deaths due to COVID-19 using a value of statistical life (VSI).

2. Add two columns to the result of scorecovid: VSI and economic loss.

# scorecovid.py
$ cat scorecovid.py
<pre>
import requests,re
import pandas as pd

def main():
 url='https://www.worldometers.info/world-population/population-by-country/'
 print('scraping population...')
 page=requests.get(url)
 df = pd.read_html(page.text)[0]
 df.columns.values[1]='Country'
 df.columns.values[2]='Population'
 #df = pd.read_html(page.text,flavor='html5lib')[0]
 df.to_csv('pop.csv')
 print('pop.csv was created')

 print('downloading total_deaths.csv file')
 import subprocess as sp
 sp.call("wget https://github.com/owid/covid-19-data/raw/master/public/data/jhu/total_deaths.csv",shell=True)
 p=pd.read_csv('total_deaths.csv')
 date=p['date'][len(p)-1]

#
#from urllib.request import Request, urlopen
#url='https://www.worldometers.info/coronavirus/#nav-today/'
#print('scraping deaths informationi...')
#req = Request(url, headers={'User-Agent': 'Firefox/76.0.1'})
#page = re.sub(r'<.*?>', lambda g: g.group(0).upper(), urlopen(req).read().decode('utf-8') )
#df = pd.read_html(page)[0]
#df.to_csv('deaths.csv')
#print('deaths.csv was created')
#


 print('countries file was read...')
 d=open('countries').read().strip()
 d=d.split(',')
 print('scoring the following ',len(d),' countries...')
 print(d)

 dd=pd.DataFrame(
  {
   "country": d,
   "deaths": range(len(d)),
   "population": range(len(d)),
   "score": range(len(d)),
  })

 pp=pd.read_csv('pop.csv')
 print('calculating scores of countries\n')
 print('score is created in result.csv')
 print('date is ',date)

 for i in d:
 # print(p[i][len(p)-1])
  dd.loc[dd.country==i,'deaths']=int(p[i][len(p)-1])
 # print(pp.loc[pp.Country==i,'Population'])
  dd.loc[dd.country==i,'population']=int(pp.loc[pp.Country==i,'Population']/1000000)
  dd.loc[dd.country==i,'score']=int(dd.loc[dd.country==i,'deaths']/dd.loc[dd.country==i,'population'])
 dd=dd.sort_values(by=['score'])
 dd.to_csv('result.csv',index=False)
 dd=pd.read_csv('result.csv',index_col=0)
 print(dd)
 sp.call("rm total_deaths.csv pop.csv",shell=True)

if __name__ == "__main__":
 main()

</pre>
