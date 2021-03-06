
# 3. KOBIS OPEN API

영화관 입장권 통합 전산망 OPENAPI를 사용하여 일일 박스오피스 자료를 가져오고 JSON파일로 저장

##  1) 일별 박스오피스
kobis에서 일별박스오피스 api를 활용하여 movieCd, movieNm, openDt, salesAmt, salesShare, salesInten, salesChange, salesAcc, audiCnt, audiInten, audiChange, audiAcc, scrnCnt, showCnt, CurrentDate, Nation 을 추출

##  2) 영화 상세정보
kobis에서 영화상세정보 api를 활용하여 movieCd, movieNm, movieNmEn, movieNmOg, showTm, prdtYear, openDt, prdtStatNm, typeNm, nations, genres, directors, actors 를 추출 = movie_info.txt


.

.

# 3.1 일별 박스오피스

### 1) kobis API를 통해 일별 박스오피스 데이터를 받아온다. 
###     파일은' 해당일자'를 이름으로 지정하고
###     JSON 타입으로 저장한다.


```python
import os
import requests
import json
import calendar
from tqdm import tqdm
from tqdm import tqdm_notebook
from datetime import date, timedelta

key='ad816991534afeaef71f07e7336b0d61'
movie_info_path='/home/ubuntu/miniii_su/movie/'
```

```python
def daily_box_office_to_json(start_date,end_date):    
    tmp_date = start_date
#     startDt = int(str(start_date)[0:4]+str(start_date)[5:7]+str(start_date)[8:10])
#     endDt = int(str(end_date)[0:4]+str(end_date)[5:7]+str(end_date)[8:10])
    days = end_date - start_date # 날짜 경과
    for i in tqdm_notebook(range(days.days+1)):
        targetDt = int(str(tmp_date)[0:4]+str(tmp_date)[5:7]+str(tmp_date)[8:10])
        
        if(os.path.isfile(movie_info_path+'raw_data/'+str(targetDt)+'.json')!=1):
            url='http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?key='+key+'&targetDt='+str(targetDt)
            res = requests.get(url)
            text = res.text
            d = json.loads(text)
            with open(movie_info_path+'raw_data/daily'+'/'+str(targetDt)+'.json', 'w', encoding="utf-8") as make_file:
                json.dump(d, make_file, ensure_ascii=False, indent="\t")
        tmp_date += timedelta(1)
```

```python

```

### 3) daliy_box_office_to_json 사용자 함수 실행한다.

```python
# 2017년 01월 ~ 2018년 02월까지 boxoffice 를 받아온다.
start_date = date(2017,1,1)
end_date = date(2018,2,28)
daily_box_office_to_json(start_date,end_date)
```


    HBox(children=(IntProgress(value=0, max=424), HTML(value='')))


```python

```

### 4) 일별 데이터를 분석할 때 용이하도록 월별로 합친다.

```python
import pandas as pd
import requests
from datetime import datetime
from datetime import timedelta
import json
from tqdm import tqdm_notebook
from dateutil.relativedelta import relativedelta

key='ad816991534afeaef71f07e7336b0d61'
movie_info_path='/home/ubuntu/miniii_su/movie/'
```

```python
def daily_boxoffice(start_date, end_date) :
    
    final_list = []

    for single_date in tqdm_notebook(pd.date_range(start_date, end_date)) :
        targetDt = str(single_date)[0:4]+str(single_date)[5:7]+str(single_date)[8:10]
        with open(movie_info_path+'raw_data/daily/'+str(targetDt)+'.json', 'r', encoding="utf-8") as read_file:
            d = json.load(read_file)

        for item in d["boxOfficeResult"]["dailyBoxOfficeList"] :
            value_list = []
            key_list = []
            for key, value in item.items() :
                key_list.append(key)
                value_list.append(value)

            value_list.append(single_date)
            key_list.append('CurrentDate')
            final_list.append(value_list)

    return pd.DataFrame(final_list, columns=key_list)
```

```python
my_date = date(2017, 1, 1)
movie_code_list_all=pd.DataFrame()
for i in tqdm_notebook(range(14)): # 14개월 추출이라서 range(14)
    start_date = my_date + relativedelta(months=i)
    end_date = start_date + relativedelta(months=1) - timedelta(days=1)
    
    daily_boxoffice_data = daily_boxoffice(start_date, end_date)
    startdate=start_date.strftime('%Y%m%d')
    enddate=end_date.strftime('%Y%m%d')
    
    movie_code_list = pd.DataFrame({'movieCd' :daily_boxoffice_data.movieCd.drop_duplicates(), 'movieNm':daily_boxoffice_data.movieNm.drop_duplicates(), 'director':'','Rate' : 0})
    
    
    daily_boxoffice_data.to_csv(movie_info_path+"data/"+"daily_"+startdate+"~"+enddate+".csv", index=False)
    
    
    movie_code_list_all = movie_code_list.append(movie_code_list_all)
    movie_code_list_all = movie_code_list_all.drop_duplicates()
    movie_code_list_all.to_csv(movie_info_path+"data/"+"movie_code_list_all.csv", index=False)
```


    HBox(children=(IntProgress(value=0, max=14), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=28), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=30), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=30), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=30), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=30), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=31), HTML(value='')))



    HBox(children=(IntProgress(value=0, max=28), HTML(value='')))


```python
movie_code_list_all.head()
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
      <th>movieCd</th>
      <th>movieNm</th>
      <th>director</th>
      <th>Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20168323</td>
      <td>염력</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20176104</td>
      <td>그것만이 내 세상</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20178126</td>
      <td>인시디어스4: 라스트 키</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20179985</td>
      <td>코코</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20150976</td>
      <td>신과함께-죄와 벌</td>
      <td></td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



```python

```

```python

```

### 5) 일일 데이터를 하나로 합치기


```python
# start_date = date(2017,1,1)
# end_date = date(2018,2,28)
# a = daily_boxoffice(start_date, end_date)
# a
```

```python

```

```python

```

# 3.2 영화 상세정보 

### 1) kobis API를 통해 영화 정보 데이터를 받아온다.
### 파일 이름은 movie_info 이고
### csv 파일 형식으로 저장한다.

```python
#영화데이터  : txt 로 저장 함수 정의
def movie_data(lists) :    
    dict_list = dict()
    url = 'http://www.kobis.or.kr/kobisopenapi/webservice/rest/movie/searchMovieInfo.json'
    
    for movie in lists :
        #print(movie)
        dic={ 'key' : key, 'movieCd' : movie}        
        req = requests.get(url, params = dic)
        
        dict_list[movie] = req.json()['movieInfoResult']['movieInfo'] #dict_list[movie] 가 있어야 append형식으로 됨. 없으면 마지막것만 추출
    return dict_list
```

```python
movie_info=movie_data(movie_code_list_all.movieCd) 
with open(movie_info_path+'raw_data/movie_info/movie_info.txt', 'w', encoding='utf-8') as outfile:   #딕셔너리를 json으로 저장
    json.dump(movie_info, outfile, ensure_ascii=False)
```

```python

```

### 2)  movie_code_list_all 에 dirctors 삽입

```python
movie_code_list_all.reset_index(drop=True, inplace=True)
movie_code_list_all
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
      <th>movieCd</th>
      <th>movieNm</th>
      <th>director</th>
      <th>Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20168323</td>
      <td>염력</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20176104</td>
      <td>그것만이 내 세상</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20178126</td>
      <td>인시디어스4: 라스트 키</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20179985</td>
      <td>코코</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20150976</td>
      <td>신과함께-죄와 벌</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>20175054</td>
      <td>메이즈 러너: 데스 큐어</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>20178395</td>
      <td>12 솔져스</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>20170590</td>
      <td>1987</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>20179228</td>
      <td>마야2</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>20179205</td>
      <td>조선명탐정: 흡혈괴마의 비밀</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>20178469</td>
      <td>올 더 머니</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>19980074</td>
      <td>타이타닉</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>20189901</td>
      <td>더 포리너</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>20178672</td>
      <td>패딩턴 2</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>20189761</td>
      <td>터닝메카드W: 반다인의 비밀 특별판</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>20170841</td>
      <td>리틀 포레스트</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>20170561</td>
      <td>블랙 팬서</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>20168250</td>
      <td>골든슬럼버</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>20176121</td>
      <td>흥부: 글로 세상을 바꾼 자</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>20078561</td>
      <td>명탐정 코난:감벽의 관</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20179462</td>
      <td>위대한 쇼맨</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>20164844</td>
      <td>불한당: 나쁜 놈들의 세상</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>20177946</td>
      <td>월요일이 사라졌다</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>20180542</td>
      <td>50가지 그림자: 해방</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>20173436</td>
      <td>셰이프 오브 워터: 사랑의 모양</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>20189882</td>
      <td>반딧불이 딘딘</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>20150969</td>
      <td>궁합</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>20179731</td>
      <td>나미야 잡화점의 기적</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>20180341</td>
      <td>레드 스패로</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>20189861</td>
      <td>더 포스트</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>294</th>
      <td>20161141</td>
      <td>50가지 그림자: 심연</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>295</th>
      <td>20163580</td>
      <td>그레이트 월</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>296</th>
      <td>20167362</td>
      <td>더 큐어</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>297</th>
      <td>20161366</td>
      <td>그래, 가족</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>298</th>
      <td>20167906</td>
      <td>마이펫 오지</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>299</th>
      <td>20161723</td>
      <td>싱글라이더</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>300</th>
      <td>20156562</td>
      <td>루시드 드림</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>301</th>
      <td>20167922</td>
      <td>핵소 고지</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>302</th>
      <td>20161725</td>
      <td>마스터</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>303</th>
      <td>20163183</td>
      <td>로그 원: 스타워즈 스토리</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>304</th>
      <td>20162025</td>
      <td>씽</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>305</th>
      <td>20144641</td>
      <td>판도라</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>306</th>
      <td>20162727</td>
      <td>루돌프와 많이있어</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>307</th>
      <td>20162183</td>
      <td>포켓몬 더 무비 XY&amp;Z 「볼케니온 : 기계왕국의 비밀」</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>308</th>
      <td>20152371</td>
      <td>당신, 거기 있어줄래요</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>309</th>
      <td>20165285</td>
      <td>극장판 도라에몽: 신 진구의 버스 오브 재팬</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>310</th>
      <td>19890291</td>
      <td>해리가 샐리를 만났을 때</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>311</th>
      <td>20161763</td>
      <td>나, 다니엘 블레이크</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>312</th>
      <td>20163014</td>
      <td>7년-그들이 없는 언론</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>313</th>
      <td>20161084</td>
      <td>패신저스</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>314</th>
      <td>20150966</td>
      <td>사랑하기 때문에</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>315</th>
      <td>20165153</td>
      <td>눈의 여왕 3: 눈과 불의 마법대결</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>316</th>
      <td>20151181</td>
      <td>여교사</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>317</th>
      <td>20164421</td>
      <td>어쌔신 크리드</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>318</th>
      <td>20168381</td>
      <td>얼라이드</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>319</th>
      <td>20168324</td>
      <td>반지의 제왕 : 반지원정대 (확장판)</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>320</th>
      <td>20168664</td>
      <td>반지의 제왕 : 두개의 탑 (확장판)</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>321</th>
      <td>20163186</td>
      <td>단지 세상의 끝</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>322</th>
      <td>20168689</td>
      <td>반지의 제왕 : 왕의 귀환 (확장판)</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <th>323</th>
      <td>20168366</td>
      <td>바다 탐험대 옥토넛 시즌4: 바다 괴물 대소동</td>
      <td></td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>324 rows × 4 columns</p>
</div>



```python
#movie_info의  director를 뽑아서 movie_code_list에 넣기
movie_info_keys=list(movie_info.keys())
#movie_info_keys
```

```python
for i in range(len(movie_code_list_all)):
    with open(movie_info_path+'raw_data/movie_info/movie_info.txt', 'r', encoding="utf-8") as make_file:
        d = json.load(make_file)
    if(d[movie_info_keys[i]]['directors'] != []):
        movie_code_list_all['director'][i]=d[movie_info_keys[i]]['directors'][0]['peopleNm']
        
    else:
        movie_code_list_all['director'][i] = '---'
```

    /home/ubuntu/.local/lib/python3.6/site-packages/ipykernel_launcher.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """
    /home/ubuntu/.local/lib/python3.6/site-packages/ipykernel_launcher.py:8: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      


```python
movie_code_list_all
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
      <th>movieCd</th>
      <th>movieNm</th>
      <th>director</th>
      <th>Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20168323</td>
      <td>염력</td>
      <td>연상호</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20176104</td>
      <td>그것만이 내 세상</td>
      <td>최성현</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20178126</td>
      <td>인시디어스4: 라스트 키</td>
      <td>애덤 로비텔</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20179985</td>
      <td>코코</td>
      <td>리 언크리치</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20150976</td>
      <td>신과함께-죄와 벌</td>
      <td>김용화</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>20175054</td>
      <td>메이즈 러너: 데스 큐어</td>
      <td>웨스 볼</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>20178395</td>
      <td>12 솔져스</td>
      <td>니콜라이 퓰시</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>20170590</td>
      <td>1987</td>
      <td>장준환</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>20179228</td>
      <td>마야2</td>
      <td>노엘 클리어리</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>20179205</td>
      <td>조선명탐정: 흡혈괴마의 비밀</td>
      <td>김석윤</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>20178469</td>
      <td>올 더 머니</td>
      <td>리들리 스콧</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>19980074</td>
      <td>타이타닉</td>
      <td>제임스 카메론</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>20189901</td>
      <td>더 포리너</td>
      <td>마틴 캠벨</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>20178672</td>
      <td>패딩턴 2</td>
      <td>폴 킹</td>
      <td>0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>20189761</td>
      <td>터닝메카드W: 반다인의 비밀 특별판</td>
      <td>홍헌표</td>
      <td>0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>20170841</td>
      <td>리틀 포레스트</td>
      <td>임순례</td>
      <td>0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>20170561</td>
      <td>블랙 팬서</td>
      <td>라이언 쿠글러</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>20168250</td>
      <td>골든슬럼버</td>
      <td>노동석</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>20176121</td>
      <td>흥부: 글로 세상을 바꾼 자</td>
      <td>조근현</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>20078561</td>
      <td>명탐정 코난:감벽의 관</td>
      <td>야마모토 야스이치로</td>
      <td>0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20179462</td>
      <td>위대한 쇼맨</td>
      <td>마이클 그레이시</td>
      <td>0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>20164844</td>
      <td>불한당: 나쁜 놈들의 세상</td>
      <td>변성현</td>
      <td>0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>20177946</td>
      <td>월요일이 사라졌다</td>
      <td>토미 위르콜라</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>20180542</td>
      <td>50가지 그림자: 해방</td>
      <td>제임스 폴리</td>
      <td>0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>20173436</td>
      <td>셰이프 오브 워터: 사랑의 모양</td>
      <td>기예르모 델 토로</td>
      <td>0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>20189882</td>
      <td>반딧불이 딘딘</td>
      <td>등위봉</td>
      <td>0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>20150969</td>
      <td>궁합</td>
      <td>홍창표</td>
      <td>0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>20179731</td>
      <td>나미야 잡화점의 기적</td>
      <td>히로키 류이치</td>
      <td>0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>20180341</td>
      <td>레드 스패로</td>
      <td>프란시스 로렌스</td>
      <td>0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>20189861</td>
      <td>더 포스트</td>
      <td>스티븐 스필버그</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>294</th>
      <td>20161141</td>
      <td>50가지 그림자: 심연</td>
      <td>제임스 폴리</td>
      <td>0</td>
    </tr>
    <tr>
      <th>295</th>
      <td>20163580</td>
      <td>그레이트 월</td>
      <td>장예모</td>
      <td>0</td>
    </tr>
    <tr>
      <th>296</th>
      <td>20167362</td>
      <td>더 큐어</td>
      <td>고어 버빈스키</td>
      <td>0</td>
    </tr>
    <tr>
      <th>297</th>
      <td>20161366</td>
      <td>그래, 가족</td>
      <td>마대윤</td>
      <td>0</td>
    </tr>
    <tr>
      <th>298</th>
      <td>20167906</td>
      <td>마이펫 오지</td>
      <td>알베르토 로드리게즈</td>
      <td>0</td>
    </tr>
    <tr>
      <th>299</th>
      <td>20161723</td>
      <td>싱글라이더</td>
      <td>이주영</td>
      <td>0</td>
    </tr>
    <tr>
      <th>300</th>
      <td>20156562</td>
      <td>루시드 드림</td>
      <td>김준성</td>
      <td>0</td>
    </tr>
    <tr>
      <th>301</th>
      <td>20167922</td>
      <td>핵소 고지</td>
      <td>멜 깁슨</td>
      <td>0</td>
    </tr>
    <tr>
      <th>302</th>
      <td>20161725</td>
      <td>마스터</td>
      <td>조의석</td>
      <td>0</td>
    </tr>
    <tr>
      <th>303</th>
      <td>20163183</td>
      <td>로그 원: 스타워즈 스토리</td>
      <td>가렛 에드워즈</td>
      <td>0</td>
    </tr>
    <tr>
      <th>304</th>
      <td>20162025</td>
      <td>씽</td>
      <td>가스 제닝스</td>
      <td>0</td>
    </tr>
    <tr>
      <th>305</th>
      <td>20144641</td>
      <td>판도라</td>
      <td>박정우</td>
      <td>0</td>
    </tr>
    <tr>
      <th>306</th>
      <td>20162727</td>
      <td>루돌프와 많이있어</td>
      <td>유야마 쿠니히코</td>
      <td>0</td>
    </tr>
    <tr>
      <th>307</th>
      <td>20162183</td>
      <td>포켓몬 더 무비 XY&amp;Z 「볼케니온 : 기계왕국의 비밀」</td>
      <td>유야마 쿠니히코</td>
      <td>0</td>
    </tr>
    <tr>
      <th>308</th>
      <td>20152371</td>
      <td>당신, 거기 있어줄래요</td>
      <td>홍지영</td>
      <td>0</td>
    </tr>
    <tr>
      <th>309</th>
      <td>20165285</td>
      <td>극장판 도라에몽: 신 진구의 버스 오브 재팬</td>
      <td>야쿠와 신노스케</td>
      <td>0</td>
    </tr>
    <tr>
      <th>310</th>
      <td>19890291</td>
      <td>해리가 샐리를 만났을 때</td>
      <td>롭 라이너</td>
      <td>0</td>
    </tr>
    <tr>
      <th>311</th>
      <td>20161763</td>
      <td>나, 다니엘 블레이크</td>
      <td>켄 로치</td>
      <td>0</td>
    </tr>
    <tr>
      <th>312</th>
      <td>20163014</td>
      <td>7년-그들이 없는 언론</td>
      <td>김진혁</td>
      <td>0</td>
    </tr>
    <tr>
      <th>313</th>
      <td>20161084</td>
      <td>패신저스</td>
      <td>모튼 틸덤</td>
      <td>0</td>
    </tr>
    <tr>
      <th>314</th>
      <td>20150966</td>
      <td>사랑하기 때문에</td>
      <td>주지홍</td>
      <td>0</td>
    </tr>
    <tr>
      <th>315</th>
      <td>20165153</td>
      <td>눈의 여왕 3: 눈과 불의 마법대결</td>
      <td>알렉세이 트시칠린</td>
      <td>0</td>
    </tr>
    <tr>
      <th>316</th>
      <td>20151181</td>
      <td>여교사</td>
      <td>김태용</td>
      <td>0</td>
    </tr>
    <tr>
      <th>317</th>
      <td>20164421</td>
      <td>어쌔신 크리드</td>
      <td>저스틴 커젤</td>
      <td>0</td>
    </tr>
    <tr>
      <th>318</th>
      <td>20168381</td>
      <td>얼라이드</td>
      <td>로버트 저메키스</td>
      <td>0</td>
    </tr>
    <tr>
      <th>319</th>
      <td>20168324</td>
      <td>반지의 제왕 : 반지원정대 (확장판)</td>
      <td>피터 잭슨</td>
      <td>0</td>
    </tr>
    <tr>
      <th>320</th>
      <td>20168664</td>
      <td>반지의 제왕 : 두개의 탑 (확장판)</td>
      <td>피터 잭슨</td>
      <td>0</td>
    </tr>
    <tr>
      <th>321</th>
      <td>20163186</td>
      <td>단지 세상의 끝</td>
      <td>자비에 돌란</td>
      <td>0</td>
    </tr>
    <tr>
      <th>322</th>
      <td>20168689</td>
      <td>반지의 제왕 : 왕의 귀환 (확장판)</td>
      <td>피터 잭슨</td>
      <td>0</td>
    </tr>
    <tr>
      <th>323</th>
      <td>20168366</td>
      <td>바다 탐험대 옥토넛 시즌4: 바다 괴물 대소동</td>
      <td>다라 오코넬</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>324 rows × 4 columns</p>
</div>



```python
with open('movie_info.txt', 'w', encoding='utf-8') as outfile:   #딕셔너리를 json으로 저장
    json.dump(movie_info, outfile, ensure_ascii=False)
```

```python

```

```python

```

```python

```

```python

```

```python

```
