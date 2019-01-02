
# 영화 빅데이터 분석

영화진흥위원회의 영화관 입장권 통합 전산망 오픈 API에서 제공하는 박스오피스 결과를 통해 영화의 흥행을 분석

## (1) 영화 데이터 수집

API에서 제공하는 일일박스오피스 정보를 불러와 JSON파일로 저장

```python
import requests
import json
from tqdm import tqdm
# api 사용을 위해 발급받은 key 값을 변수에 저장
key='ad816991534afeaef71f07e7336b0d61'
targetDt=20170301
```

```python
for i in tqdm(range(1,32)):
#일일 박스오피스를 아무 옵션도 주지않고 날짜별로 검색한 뒤 JSON파일 저장
    url1='http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?key='\
        +key+'&targetDt='+str(targetDt)
    
    res1 = requests.get(url1)
    text1 = res1.text
    d1 = json.loads(text1)
    
    with open('data/'+str(targetDt)[0:4]+'/'+str(targetDt)[4:6]+'/daily_box_office/'+str(targetDt)+'.json', 'w', encoding="utf-8") as make_file:
        json.dump(d1, make_file, ensure_ascii=False, indent="\t")

# 한국영화의 일일 박스오피스 자료를 검색하고 JSON파일로 저장
    url2=url1+'&repNationCd=K'
    
    res2 = requests.get(url2)
    text2 = res2.text
    d2 = json.loads(text2)
    
    with open('data/'+str(targetDt)[0:4]+'/'+str(targetDt)[4:6]+'/daily_box_office_k/'+str(targetDt)+'_K.json', 'w', encoding="utf-8") as make_file:
        json.dump(d2, make_file, ensure_ascii=False, indent="\t")
        
# 해외영화의 일일 박스오피스 자료를 검색하고 JSON파일로 저장
    url3=url1+'&repNationCd=F'    
    
    res3 = requests.get(url3)  
    text3 = res3.text    
    d3 = json.loads(text3)
        
    with open('data/'+str(targetDt)[0:4]+'/'+str(targetDt)[4:6]+'/daily_box_office_f/'+str(targetDt)+'_F.json', 'w', encoding="utf-8") as make_file:
        json.dump(d3, make_file, ensure_ascii=False, indent="\t")
    
    targetDt += 1
```

    100%|██████████| 31/31 [01:13<00:00,  2.24s/it]


```python
import json
result=[]
result_name=[]
targetDt=20180101
for i in range(0,10):   
    with open('data/2018/01/daily_box_office/'+str(targetDt)+'.json', 'r', encoding="utf-8") as data_file:
        data = json.load(data_file)
    result.insert(i,data['boxOfficeResult']['dailyBoxOfficeList'][i]['salesAmt'])
    result_name.insert(i,data['boxOfficeResult']['dailyBoxOfficeList'][i]['movieNm'])
result_name
```




    ['신과함께-죄와 벌',
     '1987',
     '강철비',
     '위대한 쇼맨',
     '극장판 포켓몬스터 너로 정했다!',
     '몬스터 패밀리',
     '원더',
     '뽀로로 극장판 공룡섬 대모험',
     '극장판 프리파라 모두의 동경♪ 렛츠고☆프리파리',
     '스타워즈: 라스트 제다이']



```python
import plotly.offline as offline
import plotly.graph_objs as go
labels = result_name
values = result
offline.init_notebook_mode(connected=True) 
trace = go.Pie(labels=labels, values=values) 
offline.iplot([trace], filename='basic_pie_chart')
```


<script type="text/javascript">window.PlotlyConfig = {MathJaxConfig: 'local'};</script><script type="text/javascript">if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}</script><script>requirejs.config({paths: { 'plotly': ['https://cdn.plot.ly/plotly-latest.min']},});if(!window._Plotly) {require(['plotly'],function(plotly) {window._Plotly=plotly;});}</script>



<div id="07bf964d-8919-4a0d-99a7-e524ad271d94" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("07bf964d-8919-4a0d-99a7-e524ad271d94", [{"labels": ["\uc2e0\uacfc\ud568\uaed8-\uc8c4\uc640 \ubc8c", "1987", "\uac15\ucca0\ube44", "\uc704\ub300\ud55c \uc1fc\ub9e8", "\uadf9\uc7a5\ud310 \ud3ec\ucf13\ubaac\uc2a4\ud130 \ub108\ub85c \uc815\ud588\ub2e4!", "\ubaac\uc2a4\ud130 \ud328\ubc00\ub9ac", "\uc6d0\ub354", "\ubf40\ub85c\ub85c \uadf9\uc7a5\ud310 \uacf5\ub8e1\uc12c \ub300\ubaa8\ud5d8", "\uadf9\uc7a5\ud310 \ud504\ub9ac\ud30c\ub77c \ubaa8\ub450\uc758 \ub3d9\uacbd\u266a \ub81b\uce20\uace0\u2606\ud504\ub9ac\ud30c\ub9ac", "\uc2a4\ud0c0\uc6cc\uc988: \ub77c\uc2a4\ud2b8 \uc81c\ub2e4\uc774"], "values": ["7715149100", "4460462100", "944228600", "509947700", "262886500", "205793300", "158549600", "109207600", "47882900", "35090500"], "type": "pie", "uid": "8a120f9a-f8db-4461-b1af-33ac15011c52"}], {}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("07bf964d-8919-4a0d-99a7-e524ad271d94"));});</script>


```python
#-*- coding: utf-8 -*-
import json
import csv

dates = {'01':'32','02':'29','03':'32','04':'31','05':'32','06':'31','07':'32','08':'32','09':'31','10':'32','11':'31','12':'32'}
lists = ['rnum','rank','rankInten','rankOldAndNew','movieCd','rnum','movieNm','openDt','salesAmt','salesShare','salesInten',\
    'salesChange','salesAcc','audiCnt','audiInten','audiChange','audiAcc','scrnCnt','showCnt']
lists2 = ['date','rnum','rank','rankInten','rankOldAndNew','movieCd','rnum','movieNm','openDt','salesAmt','salesShare','salesInten',\
    'salesChange','salesAcc','audiCnt','audiInten','audiChange','audiAcc','scrnCnt','showCnt']

for date in dates:
    targetDt='2017'+date+'01'
    j = open('2017'+date+'.csv', 'w', encoding='utf-8')
    csv_file = csv.writer(j)
    csv_file.writerow(lists2)

    for i in range(1,int(dates[date])):
        with open('data/2017/'+date+'/daily_box_office/'+targetDt+'.json', 'r', encoding="utf-8") as data_file:
            data = json.load(data_file)
        #data = json.loads(f)
        tmp = []
        tmp2=[]
        for i in range(0,10):
            tmp.append(int(targetDt))
            for list in lists:                
                tmp.append(data['boxOfficeResult']['dailyBoxOfficeList'][i][list])
            tmp2.insert(i,tmp)
            tmp=[]
        for i in range(0,10):
            csv_file.writerow(tmp2[i])
        tmp_dt = int(targetDt) + 1
        targetDt = str(tmp_dt)

j.close()
```

```python
import pymysql
 
# MySQL Connection 연결
conn = pymysql.connect(host='localhost', user='test', password='1234',db='movie', charset='utf8', local_infile=True)
 
# Connection 으로부터 Cursor 생성
curs = conn.cursor()
 
# SQL문 실행
sql = """create table test2(
    date int,
    rnum int, 
    rank int,
    rankInten int,
    rankOldAndNew int,
    movieCd int,    
    movieNm varchar(50),
    openDt int,
    salesAmt int,
    salesShare int,
    salesInten int,
    salesChange int,
    salesAcc int,
    audiCnt int,
    audiInten int,
    audiChange int,
    audiAcc int,
    scrnCnt int,
    showCnt int);"""
curs.execute(sql)
conn.commit()
# rows = curs.fetchall()

sql3="select * from test2;"
# curs.execute(sql3)
# rows=curs.fetchall()
# print(rows)
# rows[1]

```

```python
sql2 = """LOAD DATA LOCAL INFILE "/root/movie/.jupyter/project/csv/201702.csv" INTO TABLE movie.test2 FIELDS TERMINATED BY ',';"""
curs.execute(sql2)
conn.commit()
conn.close()
```

```python
trace = go.Bar(x=date, y=list2) 
data=[trace]
offline.iplot(data)
```


<div id="d12d4fc9-d429-491e-bcf6-b3442873a0a1" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("d12d4fc9-d429-491e-bcf6-b3442873a0a1", [{"x": ["2017-01-01", "2017-01-02", "2017-01-03", "2017-01-04", "2017-01-05", "2017-01-06", "2017-01-07", "2017-01-08", "2017-01-09", "2017-01-10", "2017-01-11", "2017-01-12", "2017-01-13", "2017-01-14", "2017-01-15", "2017-01-16", "2017-01-17", "2017-01-18", "2017-01-19", "2017-01-20", "2017-01-21", "2017-01-22", "2017-01-23", "2017-01-24", "2017-01-25", "2017-01-26", "2017-01-27", "2017-01-28", "2017-01-29", "2017-01-30", "2017-01-31"], "y": [1079312, 429079, 354356, 524319, 472716, 529040, 1050318, 984339, 361874, 366937, 387775, 390926, 438353, 958710, 931352, 291145, 297278, 626383, 541489, 575217, 1252975, 1243345, 413530, 418223, 590581, 651238, 1088636, 1365896, 1662892, 1604649, 609035], "type": "bar", "uid": "a2f6d113-a6f8-43ae-9f81-b2fda2b6a882"}], {}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("d12d4fc9-d429-491e-bcf6-b3442873a0a1"));});</script>

