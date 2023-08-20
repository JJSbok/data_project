
------------------------------------------------
# 의류 트랜드 분석 프로젝트 
------------------------------------------------

## 데이터 분석 프로젝트 - 의류 트랜드 및 선호도 분석
- 데이터 분석
- 데이터 시각화
- 웹사이트 크롤링


#### - 의류 트랜드 데이터 시각화 

```
import pandas as pd
import seaborn as sns

df = pd.read_csv('/content/Ktrend.csv', nrows=50)

import plotly.express as px
import matplotlib.pyplot as plt

print(df)

fig=px.bar(df, x='item', y='SALES_CONT', color='SLE_PRC',color_continuous_scale=px.colors.sequential.Oranges) 
fig.update_layout(width=600)
```
<img width="975" alt="스크린샷 2022-12-15 오후 11 33 23" src="https://user-images.githubusercontent.com/90183114/207887559-06d19ba6-e98f-4faa-bec6-fe3b474bd9a7.png">



## MUSINSA 크롤링 CODE 

```
import selenium
from selenium import webdriver as wd
import time
import pandas as pd
from bs4 import BeautifulSoup
import requests

from itertools import repeat
import re
```

#### 간단 정보가져오기
##### 무신사 HTML 정보 가져오기

```
url = 'https://www.musinsa.com/brands/musinsastandard?category3DepthCodes=&category2DepthCodes=&category1DepthCode=&colorCodes=&startPrice=&endPrice=&exclusiveYn=&includeSoldOut=&saleGoods=&timeSale=&includeKeywords=&sortCode=emt_high&tags=&page=1&size=90&listViewType=small&campaignCode=&groupSale=&outletGoods=false&boutiqueGoods='
response = requests.get(url)
soup = BeautifulSoup(response.text, 'lxml')
```

##### 상품 URL & TITLE 가져오기

```
soup.find_all('a', attrs={'class':'img-block'})[0]['href'] # url
soup.find_all('a', attrs={'class':'img-block'})[0]['title'] # 상품제목
```

#### 실시간 순위 크롤링

```
import pandas as pd
import requests
from bs4 import BeautifulSoup as bs
import time
from tqdm import tqdm

url="https://www.musinsa.com/ranking/best?period=now&age=ALL&mainCategory=001&subCategory=&leafCategory=&price=&golf=false&kids=false&newProduct=false&exclusive=false&discount=false&soldOut=false&page=1&viewType=small&priceMin=&priceMax="
```

##### url 호출

```
pd.read_html(url)
response=requests.get(url)
response.status_code
```

##### 브랜드명 가져오기

```
html=bs(response.text, 'lxml')
html
brand_html=html.select('#goodsRankList > li > div.li_inner > div.article_info > p.item_title > a')
brand_html[0].string
brand_list=[]
for i in brand_html:
    brand_list.append(i.string)
brand_list[0:10]
```

##### 이름과 순위 가져오기

```
link_name_html=html.select('#goodsRankList > li > div.li_inner > div.article_info > p.list_info > a')
link_list=[]
name_list=[]
for i in link_name_html:
    link_list.append(i['href'])
    name_list.append(i['title'])
link_list[0:10], name_list[0:10]

rank_html=html.select('#goodsRankList > li > p')
rank_no_list=[]
for i in rank_html:
    rank_no_list.append(i.string.strip())
rank_no_list[0:10]
```

##### CSV 추출

```
start = 1
title_list = []
url_list = []
# price_list = []

while start<37: #1페이지~36페이지
    try:
        url = 'https://www.musinsa.com/brands/musinsastandard?category3DepthCodes=&category2DepthCodes=&category1DepthCode=&colorCodes=&startPrice=&endPrice=&exclusiveYn=&includeSoldOut=&saleGoods=&timeSale=&includeKeywords=&sortCode=emt_high&tags=&page={}&size=90&listViewType=small&campaignCode=&groupSale=&outletGoods=false&boutiqueGoods='.format(start)
        response = requests.get(url)
        soup = BeautifulSoup(response.text, 'lxml')
        
        for soup in soup.find_all('a', attrs={'class':'img-block'}):
            title_list.append(soup['title'])
            url_list.append('https:' + soup['href'])
            
        start += 1
    
    except:
        print(start)
        break
```

```
df = pd.DataFrame({'상품명': title_list,
                  'url': url_list})
df.to_csv('무신사.csv', encoding = 'utf-8-sig')
```
##### csv파일 불러오기

```
import pandas as pd

df = pd.read_csv('/content/무신사.csv')
df.head()
```
<img width="735" alt="스크린샷 2022-12-15 오후 11 40 14" src="https://user-images.githubusercontent.com/90183114/207889108-fbc63cb0-0407-42ec-b704-b068da385664.png">
