
### 3. 데이터 가공


#### 3.1 데이터 추출 및 정제

> API 요청을 통해 일별 박스오피스 정보를 저장함

> 트위터 크롤링을 통해 해당 영화에 대한 의견을 저장하고 해당 의견이 긍정인지 부정인지 파악함



#### 3.2 데이터 병합

> JSON 파일을 CSV 파일로 병합한 뒤 데이터베이스에 입력 



#### 3.3 데이터 포맷 변환

> 3.2의 과정에서 적절한 형식으로 데이터 변환



#### 3.4 데이터를 DB에 삽입

> CSV 파일을 데이터베이스에 삽입


```python
import pandas as pd
import requests
import datetime
import json
from tqdm import tqdm_notebook
from dateutil.relativedelta import relativedelta

my_date = datetime.date(2017, 1, 1)
```

```python
#일별 데이터  : csv로 저장 함수 정의 

#mykey= '430156241533f1d058c603178cc3ca0e' #kobis에서 key 발급
#mykey='4aa9ba2ba5fd74d34df70439d3b483db'
mykey='aec9e1bc0c849c607bac8a03d6bdb25e' #내 키 일일 조회수 초과해서 예시에 있는 키

def daliy_boxoffice(start_date, end_date, keys =mykey) :
    
    final_list = []
    url = 'http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json'

    for single_date in tqdm_notebook(pd.date_range(start_date, end_date)) :
        for rep in ["","K", "F"] :            
            dic={
            'key' : keys,
            'targetDt' : single_date.strftime('%Y%m%d'),
            'itemPerPage' : '10', 
            'repNationCd' : rep #한국영화 K, 외국영화 F
            }
            
            req = requests.get(url, params = dic)           
            text = req.text
            d = json.loads(text) 
            
            for item in d["boxOfficeResult"]["dailyBoxOfficeList"] :
                value_list = []
                key_list = []
                for key, value in item.items() :
                    key_list.append(key)
                    value_list.append(value)
                    
                value_list.append(single_date)
                key_list.append('CurrentDate')
                
                value_list.append(rep)
                key_list.append('Nation')
            
                final_list.append(value_list)

    return pd.DataFrame(final_list, columns=key_list)     # final_list에 담긴 내용을 df로 반환
```

```python
#일별 박스 오피스를 월별로 추출, 영화코드 리스트 추출 ###ok
movie_code_list_all=pd.DataFrame()
for i in tqdm_notebook(range(1)):
    start_date = my_date + relativedelta(months=i)
    end_date = start_date+ relativedelta(months=1) - datetime.timedelta(days=1)
    
    daliy_boxoffice_data = daliy_boxoffice(start_date, end_date, keys=mykey)
    startdate=start_date.strftime('%Y%m%d')
    
    enddate=end_date.strftime('%Y%m%d')
    
    movie_code_list = pd.DataFrame({'movieCd' :daliy_boxoffice_data.movieCd.drop_duplicates(), 'movieNm':daliy_boxoffice_data.movieNm.drop_duplicates(), 'director':'','Rate' : 0})
    
    
    daliy_boxoffice_data.to_csv("daliy_boxoffice_data_"+startdate+"~"+enddate+".csv", index=False)
    movie_code_list.to_csv("movie_code_list_"+startdate+"~"+enddate+".csv", index=False)
    
    movie_code_list_all = movie_code_list.append(movie_code_list_all)
    movie_code_list_all = movie_code_list_all.drop_duplicates()
    movie_code_list_all.to_csv("movie_code_list_all.csv", index=False)
```


    A Jupyter Widget



    A Jupyter Widget


    


```python
#영화데이터  : txt 로 저장 함수 정의
def movie_data(lists, keys = mykey) :    
    dict_list = dict()
    url = 'http://www.kobis.or.kr/kobisopenapi/webservice/rest/movie/searchMovieInfo.json'
    
    for movie in lists :
        #print(movie)
        dic={ 'key' : keys, 'movieCd' : movie}        
        req = requests.get(url, params = dic)
        
        dict_list[movie] = req.json()['movieInfoResult']['movieInfo'] #dict_list[movie] 가 있어야 append형식으로 됨. 없으면 마지막것만 추출
    return dict_list
```

```python
movie_info=movie_data(movie_code_list_all.movieCd,keys=mykey) 
with open('movie_info.txt', 'w', encoding='utf-8') as outfile:   #딕셔너리를 json으로 저장
    json.dump(movie_info, outfile, ensure_ascii=False)
```

```python
#movie_info의  director를 뽑아서 movie_code_list에 넣기
movie_info_keys=list(movie_info.keys())
movie_info_keys
```




    ['20161725',
     '20163183',
     '20162025',
     '20144641',
     '20167904',
     '20161872',
     '20162727',
     '20162183',
     '20152371',
     '20165285',
     '20154661',
     '20161701',
     '20161603',
     '20162545',
     '20159286',
     '20164527',
     '20153401',
     '20161763',
     '20165543',
     '19890291',
     '20150966',
     '20165925',
     '20080893',
     '20160221',
     '20163014',
     '20151181',
     '20154342',
     '20155223',
     '20167286',
     '20161084',
     '20165153',
     '20145486',
     '20165061',
     '20164485',
     '20151228',
     '20165144',
     '20101406',
     '20151229',
     '19880070',
     '19688044',
     '20110014',
     '20157631',
     '20160261',
     '20164421',
     '20168381',
     '20168324',
     '20163845',
     '20168246',
     '20167127',
     '20165443',
     '20160881',
     '20149384',
     '20155700',
     '19600018',
     '20168664',
     '20163186',
     '20010130',
     '20167324',
     '20168104',
     '20168086',
     '20168088',
     '20168087',
     '20166421',
     '20167644',
     '20168154',
     '20179481',
     '20158482',
     '20168688',
     '20167285',
     '20158799',
     '19598036',
     '20168689',
     '20165822',
     '20168611',
     '20168366',
     '19820052',
     '20166707',
     '19730023',
     '20150964']



```python
for i in range(len(movie_code_list_all)):
    movie_code_list_all.iloc[i,1]=movie_info[movie_info_keys[i]]['directors'][0]['peopleNm']
```

```python
movie_code_list_all
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Rate</th>
      <th>director</th>
      <th>movieCd</th>
      <th>movieNm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>조의석</td>
      <td>20161725</td>
      <td>마스터</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>가렛 에드워즈</td>
      <td>20163183</td>
      <td>로그 원: 스타워즈 스토리</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>가스 제닝스</td>
      <td>20162025</td>
      <td>씽</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>박정우</td>
      <td>20144641</td>
      <td>판도라</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>데이미언 셔젤</td>
      <td>20167904</td>
      <td>라라랜드</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>신카이 마코토</td>
      <td>20161872</td>
      <td>너의 이름은.</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>유야마 쿠니히코</td>
      <td>20162727</td>
      <td>루돌프와 많이있어</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>유야마 쿠니히코</td>
      <td>20162183</td>
      <td>포켓몬 더 무비 XY&amp;Z 「볼케니온 : 기계왕국의 비밀」</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0</td>
      <td>홍지영</td>
      <td>20152371</td>
      <td>당신, 거기 있어줄래요</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>야쿠와 신노스케</td>
      <td>20165285</td>
      <td>극장판 도라에몽: 신 진구의 버스 오브 재팬</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0</td>
      <td>권수경</td>
      <td>20154661</td>
      <td>형</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0</td>
      <td>이동하</td>
      <td>20161701</td>
      <td>위켄즈</td>
    </tr>
    <tr>
      <th>15</th>
      <td>0</td>
      <td>김동민</td>
      <td>20161603</td>
      <td>순종</td>
    </tr>
    <tr>
      <th>16</th>
      <td>0</td>
      <td>전인환</td>
      <td>20162545</td>
      <td>무현, 두 도시 이야기</td>
    </tr>
    <tr>
      <th>17</th>
      <td>0</td>
      <td>이현주</td>
      <td>20159286</td>
      <td>연애담</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0</td>
      <td>이영준</td>
      <td>20164527</td>
      <td>부릉! 부릉! 브루미즈: 스피더의 모험 일기</td>
    </tr>
    <tr>
      <th>19</th>
      <td>0</td>
      <td>고현창</td>
      <td>20153401</td>
      <td>파파좀비</td>
    </tr>
    <tr>
      <th>27</th>
      <td>0</td>
      <td>켄 로치</td>
      <td>20161763</td>
      <td>나, 다니엘 블레이크</td>
    </tr>
    <tr>
      <th>28</th>
      <td>0</td>
      <td>미츠나카 스스무</td>
      <td>20165543</td>
      <td>하이큐!! 승자와 패자</td>
    </tr>
    <tr>
      <th>29</th>
      <td>0</td>
      <td>롭 라이너</td>
      <td>19890291</td>
      <td>해리가 샐리를 만났을 때</td>
    </tr>
    <tr>
      <th>43</th>
      <td>0</td>
      <td>주지홍</td>
      <td>20150966</td>
      <td>사랑하기 때문에</td>
    </tr>
    <tr>
      <th>45</th>
      <td>0</td>
      <td>최우성</td>
      <td>20165925</td>
      <td>음란 과외 - 무삭제판</td>
    </tr>
    <tr>
      <th>46</th>
      <td>0</td>
      <td>김기영</td>
      <td>20080893</td>
      <td>렌의 애가</td>
    </tr>
    <tr>
      <th>57</th>
      <td>0</td>
      <td>디에터 버너</td>
      <td>20160221</td>
      <td>에곤 쉴레: 욕망이 그린 그림</td>
    </tr>
    <tr>
      <th>69</th>
      <td>0</td>
      <td>김진혁</td>
      <td>20163014</td>
      <td>7년-그들이 없는 언론</td>
    </tr>
    <tr>
      <th>75</th>
      <td>0</td>
      <td>김태용</td>
      <td>20151181</td>
      <td>여교사</td>
    </tr>
    <tr>
      <th>76</th>
      <td>0</td>
      <td>전일우</td>
      <td>20154342</td>
      <td>뚜르: 내 생애 최고의 49일</td>
    </tr>
    <tr>
      <th>77</th>
      <td>0</td>
      <td>송민규</td>
      <td>20155223</td>
      <td>목숨 건 연애</td>
    </tr>
    <tr>
      <th>79</th>
      <td>0</td>
      <td>모리오카 토시유키</td>
      <td>20167286</td>
      <td>형의 여자</td>
    </tr>
    <tr>
      <th>92</th>
      <td>0</td>
      <td>모튼 틸덤</td>
      <td>20161084</td>
      <td>패신저스</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>329</th>
      <td>0</td>
      <td>론 클레멘츠</td>
      <td>20165443</td>
      <td>모아나</td>
    </tr>
    <tr>
      <th>346</th>
      <td>0</td>
      <td>김소연</td>
      <td>20160881</td>
      <td>문영</td>
    </tr>
    <tr>
      <th>437</th>
      <td>0</td>
      <td>임원식</td>
      <td>20149384</td>
      <td>청일전쟁과 여걸 민비</td>
    </tr>
    <tr>
      <th>469</th>
      <td>0</td>
      <td>한형모</td>
      <td>20155700</td>
      <td>왕자 호동</td>
    </tr>
    <tr>
      <th>497</th>
      <td>0</td>
      <td>김기영</td>
      <td>19600018</td>
      <td>하녀</td>
    </tr>
    <tr>
      <th>518</th>
      <td>0</td>
      <td>피터 잭슨</td>
      <td>20168664</td>
      <td>반지의 제왕 : 두개의 탑 (확장판)</td>
    </tr>
    <tr>
      <th>536</th>
      <td>0</td>
      <td>자비에 돌란</td>
      <td>20163186</td>
      <td>단지 세상의 끝</td>
    </tr>
    <tr>
      <th>539</th>
      <td>0</td>
      <td>스티븐 달드리</td>
      <td>20010130</td>
      <td>빌리 엘리어트</td>
    </tr>
    <tr>
      <th>569</th>
      <td>0</td>
      <td>가스 데이비스</td>
      <td>20167324</td>
      <td>라이언</td>
    </tr>
    <tr>
      <th>618</th>
      <td>0</td>
      <td>배경헌</td>
      <td>20168104</td>
      <td>2017 한국영화아카데미 33기 졸업영화제 - B</td>
    </tr>
    <tr>
      <th>619</th>
      <td>0</td>
      <td>곽승민</td>
      <td>20168086</td>
      <td>2017 한국영화아카데미 33기 졸업영화제 - A</td>
    </tr>
    <tr>
      <th>646</th>
      <td>0</td>
      <td>전희욱</td>
      <td>20168088</td>
      <td>2017 한국영화아카데미 33기 졸업영화제 - D</td>
    </tr>
    <tr>
      <th>647</th>
      <td>0</td>
      <td>한가람</td>
      <td>20168087</td>
      <td>2017 한국영화아카데미 33기 졸업영화제 - C</td>
    </tr>
    <tr>
      <th>675</th>
      <td>0</td>
      <td>김태윤</td>
      <td>20166421</td>
      <td>재심</td>
    </tr>
    <tr>
      <th>688</th>
      <td>0</td>
      <td>에릭 섬머</td>
      <td>20167644</td>
      <td>발레리나</td>
    </tr>
    <tr>
      <th>689</th>
      <td>0</td>
      <td>피터 버그</td>
      <td>20168154</td>
      <td>딥워터 호라이즌</td>
    </tr>
    <tr>
      <th>706</th>
      <td>0</td>
      <td>노진수</td>
      <td>20179481</td>
      <td>사랑받지 못한 여자</td>
    </tr>
    <tr>
      <th>708</th>
      <td>0</td>
      <td>이준익</td>
      <td>20158482</td>
      <td>동주</td>
    </tr>
    <tr>
      <th>722</th>
      <td>0</td>
      <td>폴 W.S. 앤더슨</td>
      <td>20168688</td>
      <td>레지던트 이블: 파멸의 날</td>
    </tr>
    <tr>
      <th>727</th>
      <td>0</td>
      <td>타카하시 와타루</td>
      <td>20167285</td>
      <td>짱구는 못말려 극장판: 폭풍수면! 꿈꾸는 세계 대돌격</td>
    </tr>
    <tr>
      <th>737</th>
      <td>0</td>
      <td>이나정</td>
      <td>20158799</td>
      <td>눈길</td>
    </tr>
    <tr>
      <th>739</th>
      <td>0</td>
      <td>한형모</td>
      <td>19598036</td>
      <td>여사장</td>
    </tr>
    <tr>
      <th>747</th>
      <td>0</td>
      <td>피터 잭슨</td>
      <td>20168689</td>
      <td>반지의 제왕 : 왕의 귀환 (확장판)</td>
    </tr>
    <tr>
      <th>748</th>
      <td>0</td>
      <td>레베카 밀러</td>
      <td>20165822</td>
      <td>매기스 플랜</td>
    </tr>
    <tr>
      <th>749</th>
      <td>0</td>
      <td>데니스 체르노프</td>
      <td>20168611</td>
      <td>키코리키: 황금모자의 비밀</td>
    </tr>
    <tr>
      <th>779</th>
      <td>0</td>
      <td>다라 오코넬</td>
      <td>20168366</td>
      <td>바다 탐험대 옥토넛 시즌4: 바다 괴물 대소동</td>
    </tr>
    <tr>
      <th>826</th>
      <td>0</td>
      <td>박호태</td>
      <td>19820052</td>
      <td>애인</td>
    </tr>
    <tr>
      <th>839</th>
      <td>0</td>
      <td>파블로 라라인</td>
      <td>20166707</td>
      <td>재키</td>
    </tr>
    <tr>
      <th>887</th>
      <td>0</td>
      <td>황풍</td>
      <td>19730023</td>
      <td>흑권</td>
    </tr>
    <tr>
      <th>913</th>
      <td>0</td>
      <td>박광현</td>
      <td>20150964</td>
      <td>조작된 도시</td>
    </tr>
  </tbody>
</table>
<p>79 rows × 4 columns</p>
</div>



```python
############### 네이버 api로 별점 추출, def 이용_1
```

```python
from bs4 import BeautifulSoup
import urllib.request
from urllib.parse import quote
import json
import re
import requests

naver_client_id = "YO4i1JrH8SJibQryNA8j"
naver_client_secret = "gYKhYt564S"

def cleanhtml(raw_html):
    cleanr = re.compile('<.*?>')
    cleantext = re.sub(cleanr, '', raw_html)
    return cleantext
def searchByTitle(title): #네이버 api
    myurl = 'https://openapi.naver.com/v1/search/movie.json?display=100&query=' + quote(title)
    request = urllib.request.Request(myurl)
    request.add_header("X-Naver-Client-Id",naver_client_id)
    request.add_header("X-Naver-Client-Secret",naver_client_secret)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()
    if(rescode==200):
        response_body = response.read()
        d = json.loads(response_body.decode('utf-8'))
        if (len(d['items']) > 0):
            return d['items']
        else:
            return None 
    else:
        print("Error Code:" + rescode)
```

```python
#requests data 불러오기
def findItemByInput(items):
    n=len(items)
    #print('n = ',n)
    for index, item in enumerate(items):
        navertitle = cleanhtml(item['title'])
 #       naversubtitle = cleanhtml(item['subtitle'])
 #       naverpubdate = cleanhtml(item['pubDate'])
 #       naveractor = cleanhtml(item['actor'])
        naverlink = cleanhtml(item['link']) # 있어야함
        naverdirecter=cleanhtml(item['director'])
        naveruserScore = cleanhtml(item['userRating'])
 
        navertitle1 = navertitle.replace(" ","")
        navertitle1 = navertitle1.replace("-", ",")
        navertitle1 = navertitle1.replace(":", ",")
 
        spScore = getSpecialScore(naverlink)  #기자 평론가 평점을 얻어 옵니다
        
        naverid = re.split("code=", naverlink)[1]  #네이버가 다루는 영화 고유 ID를 얻어 옵니다
   #     print('naveruserScore',naveruserScore)
        print(naveruserScore)
      #  print(naverid, navertitle,naveruserScore, spScore)
      #  print(index, navertitle, naversubtitle, naveruserScore, spScore)
```

```python
def getInfoFromNaver(searchTitle):
    items = searchByTitle(searchTitle)
 
    if (items != None):
        findItemByInput(items)
    else:
        print("No result") 
```

```python
def get_soup(url):
    source_code = requests.get(url)
    plain_text = source_code.text
    soup = BeautifulSoup(plain_text, 'lxml')
    return soup
 
#기자 평론가 평점을 얻어 옵니다
def getSpecialScore(URL):
    soup = get_soup(URL)
    scorearea = soup.find_all('div', "spc_score_area")
    newsoup = BeautifulSoup(str(scorearea), 'lxml')
    score = newsoup.find_all('em')
    if (score and len(score) > 5):
        scoreis = score[1].text + score[2].text + score[3].text + score[4].text
        return float(scoreis)
    else:
        return 0.0
```

```python
for r in tqdm_notebook(range(10)):
    print('r = ',r)
    
    name=movie_code_list_all.iloc[r,3]
    print('name = ',name)  
    
    getInfoFromNaver(name)    #이 값을 변수에 넣을 수가 없음 ㅠㅠ 대입이 안됨
    

movie_code_list_all
```

```python
############### 네이버 api로 별점 추출, def 이용_2
import os
import sys
import urllib.request

client_id = "YO4i1JrH8SJibQryNA8j"
client_secret = "gYKhYt564S"

with open('movie_info.txt', 'w', encoding='utf-8') as outfile:   
    json.dump(movie_info, outfile, ensure_ascii=False)

from IPython.display import clear_output

def naver_starscore():
    for i in tqdm_notebook(range(len(movie_code_list_all))) : 
        url = 'https://openapi.naver.com/v1/search/movie.json'
        dic = {
            'query' : movie_code_list_all.iloc[i, 3],
            'display' : 10
        }

        headers = {
            'X-Naver-Client-Id' : 'API Client ID',
            'X-Naver-Client-Secret' : 'API Client Secret'
        }
        
        request = requests.get(url, headers=headers, dic=payload)        
        response = urllib.request.urlopen(request)
        rescode = response.getcode()
        
        if(rescode==200):
            response_body = response.read()
            js_naver = json.loads(response_body)
            print(l, 'th = request success')
            print('js_naver = \n', js_naver)

            if js_naver['total']==1: #영화명 1개, 별점 추출                
                if movie_code_list_all.iloc[l,3].replace(' ', '') == js_naver['items'][0]['title'][3:-4].replace(' ', ''):
                    movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
                    print(' 영화명1개, 일치,  okokokokkok')
                else:                
                    print(' 영화명1개, 불일치')

            else:  #영화명 여러개 
             #   print('js_naver[items].count(title) = ',js_naver['items'].count('title'))
                for j in range(1,js_naver['items'].count('title')):
                 #   print('j = ', j)
                    for k in range(80):
                 #       print('k = ',k)
              #          print('js_naver director =',js_naver['items'][j]['director'][:3],'movie_code_list_all.iloc[k,1] = ',movie_code_list_all.iloc[k,1])
                        if js_naver['items'][j]['director'][:3] == movie_code_list_all.iloc[k,1]:                        
                            movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
                #            print('영화여러개, 감독 일치, okokokok')
                        else:
                            movie_code_list_all.iloc[l,0] = 0
                   #         print('영화여러개, 감독불일치, 별점 0')
        else:
            print("Error Code:" + rescode)
```

```python
movie_code_list_all
```

```python
############### 네이버 api로 별점 추출, display = 100 으로 수정중
import os
import sys
import urllib.request

client_id = "YO4i1JrH8SJibQryNA8j"
client_secret = "gYKhYt564S"

with open('movie_info.txt', 'w', encoding='utf-8') as outfile:   
    json.dump(movie_info, outfile, ensure_ascii=False)

for i in tqdm_notebook(range(len(movie_code_list_all))) :  
    url = 'https://openapi.naver.com/v1/search/movie.json'
    dic = {
        'query' : movie_code_list_all.iloc[i, 3],
         'display' : 100
    }
    headers = {
         'X-Naver-Client-Id' : 'API Client ID',
          'X-Naver-Client-Secret' : 'API Client Secret'
       }    
    req = requests.get(url, headers=headers, params=dic)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()        
    
    if(rescode==200):
        response_body = response.read()
        js_naver = json.loads(response_body)
        print(l, 'th = request success')
        print('js_naver = \n', js_naver)
        
        if js_naver['total']==1: #영화명 1개, 별점 추출                
            if movie_code_list_all.iloc[l,3].replace(' ', '') == js_naver['items'][0]['title'][3:-4].replace(' ', ''):
                movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
                print(' 영화명1개, 일치,  okokokokkok')
            else:                
                print(' 영화명1개, 불일치')
                
        else:  #영화명 여러개 
            print('js_naver[items].count(title) = ',js_naver['items'].count('title'))
            for j in range(1,js_naver['items'].count('title')):
                print('j = ', j)
                for k in range(80):
                    print('k = ',k)
                    print('js_naver director =',js_naver['items'][j]['director'][:3],'movie_code_list_all.iloc[k,1] = ',movie_code_list_all.iloc[k,1])
                    if js_naver['items'][j]['director'][:3] == movie_code_list_all.iloc[k,1]:                        
                        movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
            #            print('영화여러개, 감독 일치, okokokok')
                    else:
                        movie_code_list_all.iloc[l,0] = 0
               #         print('영화여러개, 감독불일치, 별점 0')
    else:
        print("Error Code:" + rescode)
```

```python
movie_code_list_all
```

```python
############### 네이버 api로 별점 추출, display=10
import os
import sys
import urllib.request

client_id = "YO4i1JrH8SJibQryNA8j"
client_secret = "gYKhYt564S"

with open('movie_info.txt', 'w', encoding='utf-8') as outfile:   
    json.dump(movie_info, outfile, ensure_ascii=False)

for l in tqdm_notebook(range(len(movie_code_list_all))):
    m=movie_code_list_all.iloc[l,3] #영화명이 iloc[l,3]
    encText = urllib.parse.quote(m)
    url = "https://openapi.naver.com/v1/search/movie.json?query=" + encText # json 결과
    request = urllib.request.Request(url)
    request.add_header("X-Naver-Client-Id",client_id)
    request.add_header("X-Naver-Client-Secret",client_secret)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()
    
    if(rescode==200):
        response_body = response.read()
        js_naver = json.loads(response_body)
        print(l, 'th = request success')
        print('js_naver = \n', js_naver)
        
        if js_naver['total']==1: #영화명 1개, 별점 추출                
            if movie_code_list_all.iloc[l,3].replace(' ', '') == js_naver['items'][0]['title'][3:-4].replace(' ', ''):
                movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
                print(' 영화명1개, 일치,  okokokokkok')
            else:                
                print(' 영화명1개, 불일치')
                
        else:  #영화명 여러개 
            print('js_naver[items].count(title) = ',js_naver['items'].count('title'))
            for j in range(1,js_naver['items'].count('title')):
                print('j = ', j)
                for k in range(80):
                    print('k = ',k)
                    print('js_naver director =',js_naver['items'][j]['director'][:3],'movie_code_list_all.iloc[k,1] = ',movie_code_list_all.iloc[k,1])
                    if js_naver['items'][j]['director'][:3] == movie_code_list_all.iloc[k,1]:                        
                        movie_code_list_all.iloc[l,0] = js_naver['items'][0]['userRating']
            #            print('영화여러개, 감독 일치, okokokok')
                    else:
                        movie_code_list_all.iloc[l,0] = 0
               #         print('영화여러개, 감독불일치, 별점 0')
    else:
        print("Error Code:" + rescode)
```

```python
movie_code_list_all
```

```python
movie_code_list_all.to_csv("movie_code_list_all.csv", index=False)
```
