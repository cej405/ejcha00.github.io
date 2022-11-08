---
layout: single
title: "Webscrawling - investinb.com"
---

## 주식 historical Data 크롤링


investing.com 은 세계 주식 정보를 제공하는 사이트이다.

종목 검색 시, 일자(Data)별 가격(Price), 시가(Open), 고가(High), 저가(Low), 거래량(Vol), 변화율(Change) 정보를 Historical Data라는 테이블 형태로 제공한다.
* 일자(Data)를 지정할 수 있어 원하는 일자 범위에 대한 Historical Data 정보를 얻을 수 있다.
* 일자를 지정하는 것은 웹크롤링 중에서도 동적 웹크롤링에 속하여 아직 구현하지는 못했다.

#### list 저장
```
from bs4 import BeautifulSoup
import urllib.request

import cloudscraper

result = []
company_name_list = ["apple-computer-inc", "amazon-com-inc"]

for name in company_name_list:
    historical_url = 'https://www.investing.com/equities/%s' % f"{name}-historical-data"
    print(historical_url)
    scraper = cloudscraper.create_scraper()  # returns a CloudScraper instance
    ret = scraper.get(historical_url)
    soupHistory = BeautifulSoup(ret.text, 'html.parser')
    title = soupHistory.find("h2", {"class":"text-lg font-semibold content-header_title__qFtZ7"}).string.split()[0]
    tag_tbody = soupHistory.find_all("tbody", {"class":"datatable_body__cs8vJ"})[1]
    for day in tag_tbody.find_all('tr'):
        if len(day) <= 3:
            break
        day_td = day.find_all('td')
        day_Date = day_td[0].string
        day_Price = day_td[1].string
        day_Open = day_td[2].string
        day_High = day_td[3].string
        day_Low = day_td[4].string
        day_Vol = day_td[5].string
        day_Change = day_td[6].string
        result.append([title]+[day_Date]+[day_Price]+[day_Open]+[day_High]+[day_Low]+[day_Vol]+[day_Change])
    print(title)
```

#### DataFrame 변환
```
import pandas as pd

Stock_df = pd.DataFrame(result, columns=('Title','Date', 'Price', 'Open', 'High', 'Low', 'Vol', 'Change'))
print(Stock_df.shape)
Stock_df
```
