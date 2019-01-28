---
layout: post
title: "웹크롤링 미니팀프로젝트 결과"
tags: [팀프로젝트, 웹크롤링]
comments: true
---

Whoscored.com 내 특정 리그에 대한 league table data 및 team statistics를 크롤링하여 데이터프레임을 제공하는 서비스 구현

## Mini Project : Club Stats reporting service

> Whoscored.com 내 특정 리그에 대한 league table data 및 team statistics를 크롤링하여 데이터프레임을 제공하는 서비스 구현

                                                                    * team statistics : 특정팀의 경기당 득점수, 평균 볼점유율 등

### 1. 문제정의

- 시즌 현황파악 및 팀별 전력분석을 위한 league table data와 팀별 statistics를 통합한 서비스 제공 미비

### 2. 서비스 구현방안

- 사용자가 원하는 특정 리그를 입력할 시 해당 리그의 league table data와 팀별 statistics를 통합 제공하는 데이터 프레임 구현

#### 예시) England Championship URL 입력 시

![1](https://user-images.githubusercontent.com/41605276/51817517-62772300-230e-11e9-8256-5c6a89726ebb.png)

### 3. 서비스 제공을 위한 'Whoscored' 클래스 구현

#### [클래스 구현중점]

- 코드 오류발생의 최소화, 가능한 모든 조건에서 실행 가능여부 확인
- 코드 오류발생의 최소화를 위해 누락데이터(None)가 다수 존재하는 최악조건의 리그를 기반으로 구현
- 최악의 조건하, 구현한 코드에서 예외 처리가 원활히 이루어지는지 확인하고자 위해 누락데이터(None)가 다수 존재하는 스페인 2부 리그 Segunda-Division을 대상으로 시현

#### [함수별 기능요약]

    1. init

        - 주요기능 : 클래스 내 전역으로 쓰일 변수 설정

        - 실행과정 :

            1) 최초 접속 URL 초기값 인자 설정

            2) headless 옵션 설정 (초기값 = True)

            3) fake_useragent 옵션 설정 (초기값 = True)


    2. get_team_url

         - 주요기능 : 'https://www.whoscored.com/Regions/206/Tournaments/63/Spain-Segunda-Divisi%C3%B3n' URL 링크에서 Segunda Division Tables 내 팁별 URL 링크 크롤링

         - 실행과정 :

             1) css selector로 league table내 팀별 URL link element 수집

             2) 수집한 URL link element를 team_urls 리스트에 저장
             
             3) team_urls 변수를 리턴


    3. get_league_table

        - 주요기능 : 'https://www.whoscored.com/Regions/206/Tournaments/63/Spain-Segunda-Divisi%C3%B3n' URL 링크에서 Segunda Division Tables 내 팀별 순위 정보 크롤링

        - 실행과정 :

            1) css selector로 league table내 팀별 순위정보 수집

            2) 수집한 정보를 league_table 리스트에 저장
            
            3) league_table 변수를 리턴

    4. get_team_information

        - 주요기능 : team_urls 리스트 내 팀별 URL을 순회하며 팀별 URL 내 team statistics 정보 수집

        - 실행과정 : 

            1) team_urls 리스트 내 팀별 URL을 순회

            2) 팀별 URL 내 team statistics 정보 수집 team_info 변수에 저장

            3) 일부 팀 URL 내 team statistics 정보가 없는 경우 예외처리(None값을 대체로 삽입), team_info 변수에 저장
            
            4) team_info 변수를 리턴


    5. making_df

        - 주요기능 : league_table 리스트와 team_info 리스트를 병합하여 판다스 데이터 프레임으로 구현

        - 실행과정 :

            1) league_table 리스트와 team_info 리스트를 병합

            2) 판다스 데이터 프레임으로 구현 및 result 변수에 저장
            
            3) result 변수 리턴
            
    6. crawling

        - 주요기능 : 2 ~ 5. 함수 순차적 실행명령 부여

        - 실행과정 :

            1) get_team_url함수 실행, 결과값을 self.team_urls에 저장

            2) get_league_table함수 실행 , 결과값을 self.league_table에 저장
            
            3) 1) 결과값을 참조하여 get_team_information함수 실행 , 결과값을 self.team_info에 저장
            
            4) 2),3) 결과값을 참조하여 making_df함수 실행, 결과값으로 result에 저장
            
            5) result 변수를 최종 리턴
            
    7. df_columns_to_num

        - 주요기능 : 데이터 프레임 전처리를 위한 데이터타입 변경(문자형 -> 숫자)

        - 실행과정 :
        
            1) 데이터프레임을 인자로 입력하면 데이터 타입을 일괄적으로 numeric 형식으로 변경
            
            2) numeric 변경 불가한 타입은 데이터프레임에서 누락

#### [구현 상세]


```python
import pandas as pd
import time
from selenium import webdriver
from fake_useragent import UserAgent
```


```python
class Whoscored:
    
    def __init__(self, url='https://www.whoscored.com/Regions/206/Tournaments/63/Spain-Segunda-Divisi%C3%B3n', headless=True):
        
        self.url = url
        self.team_urls = None
        self.league_table = None
        self.team_info = None
        self.options = webdriver.ChromeOptions() 
        self.options.add_argument("user-agent={}".format(UserAgent().chrome))
        if headless:
            self.options.add_argument('headless')
        self.driver = webdriver.Chrome(options=self.options)
        
        
    def get_team_url(self):

        self.team_urls = []
        
        self.driver.get(self.url)
        
        links = self.driver.find_elements_by_css_selector("#standings-16547-content tr") 

        for link in links:
            url_link = link.find_element_by_css_selector("td:nth-child(2) > a").get_attribute("href")
            self.team_urls.append(url_link)

        return self.team_urls

        
    def get_league_table(self):

        self.league_table = []
        
        self.driver.get(self.url)

        links = self.driver.find_elements_by_css_selector("#standings-16547-content tr") 

        for link in links:
            rank = link.find_element_by_css_selector(".o").text
            team = link.find_element_by_css_selector(".team").text
            played = link.find_element_by_css_selector(".p").text
            win = link.find_element_by_css_selector(".w").text
            draw = link.find_element_by_css_selector(".d").text
            loss = link.find_element_by_css_selector(".l").text
            goal_for = link.find_element_by_css_selector(".gf").text
            goal_against = link.find_element_by_css_selector(".ga").text
            goal_difference = link.find_element_by_css_selector(".gd").text
            points = link.find_element_by_css_selector(".pts").text

            table_data = {
                "rank" : rank,
                "team" : team,
                "played" : played,
                "win" : win,
                "draw" : draw,
                "loss" : loss,
                "goal_for" : goal_for,
                "goal_against" : goal_against,
                "goal_difference" : goal_difference,
                "points" : points,
            }
            
            self.league_table.append(table_data)
        
        return self.league_table 
    

    
    def get_team_information(self):

        self.team_info = []

        for team_url in self.team_urls:
            
#           Fake-useragent를 계속 바꾸고 싶은 경우 추가하는 코드:
#             self.options.add_argument("user-agent={}".format(UserAgent().chrome)) # check: add UserAgent 반복문에 넣어줘야 함..
#             self.driver = webdriver.Chrome(options=self.options) # driver가 꺼졌다가 실행되어야 useragent가 갱신됩니다!
#             agent = self.driver.execute_script("return navigator.userAgent") 
#             print(agent) # useragent가 바뀌었는지 check
#           하지만, 결과적으로 Useragent는 request block을 막는데 도움은 안됐습니다.
            
            self.driver.get(team_url)
    
            try:
                self.driver.find_element_by_css_selector(".team-profile-side-box div.team-name > a").text
                
            except:
                print("{} data not available".format(self.driver.find_element_by_css_selector("h2.team-header > span.team-header-name").text))
                data = {
                    "goals_per_game" : None,
                    "avg_possession" : None,
                    "pass_accuracy" : None,
                    "shots_per_game" : None,
                    "tackles_per_game" : None,
                    "dribbles_per_game" : None,
                    "yellow_card" : None,
                    "red_card" : None,
                } # Team profile이 없는 경우에는 None을 채웁니다.

                self.team_info.append(data)
                
            else:
                data = {
                    "goals_per_game" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(6)").text,
                    "avg_possession" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(8)").text,
                    "pass_accuracy" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(10)").text,
                    "shots_per_game" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(12)").text,
                    "tackles_per_game" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(14)").text,
                    "dribbles_per_game" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(16)").text,
                    "yellow_card" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(18) > .yellow-card-box").text,
                    "red_card" : self.driver.find_element_by_css_selector("dl.stats > dd:nth-child(18) > .red-card-box").text,
                }

                self.team_info.append(data)
                            
            time.sleep(7) # check: Prevent Access Denied
                          # 수십번을 실험해본 결과 제 맥북 기준으로 7초는 넣어줘야 안전하게 Denied가 안걸리네요..
        
        self.driver.quit()
        return self.team_info    
    
    def making_df(self):

        result = []

        for count in range(len(self.team_urls)):

            data = dict(self.league_table[count], **self.team_info[count])
            result.append(data)

        df = pd.DataFrame(result)

        df = df[[
            "team",
            "rank",
            "points",
            "played",
            "win",
            "draw",
            "loss",
            "goal_for",
            "goal_against",
            "goal_difference",
            "goals_per_game",
            "avg_possession",
            "pass_accuracy",
            "shots_per_game",
            "tackles_per_game",
            "dribbles_per_game",
            "yellow_card",
            "red_card",
        ]]

        return df

    
    def crawling(self):
        self.team_urls = self.get_team_url()
        self.league_table = self.get_league_table()
        self.team_info = self.get_team_information()
        result = self.making_df()
        
        return result
    
    
    def df_columns_to_num(self, df, column_list):
        df[column_list] = df[column_list].apply(pd.to_numeric, errors='coerce')
        return df.dropna(axis=1, how='all')
```


```python
whoscored = Whoscored()
df = whoscored.crawling()
```

    Alcorcon data not available
    Albacete data not available
    Real Oviedo data not available
    Numancia data not available
    Cadiz data not available
    Lugo data not available
    Reus data not available
    Rayo Majadahonda data not available
    Extremadura data not available
    Gimnastic data not available
    


```python
df
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
      <th>team</th>
      <th>rank</th>
      <th>points</th>
      <th>played</th>
      <th>win</th>
      <th>draw</th>
      <th>loss</th>
      <th>goal_for</th>
      <th>goal_against</th>
      <th>goal_difference</th>
      <th>goals_per_game</th>
      <th>avg_possession</th>
      <th>pass_accuracy</th>
      <th>shots_per_game</th>
      <th>tackles_per_game</th>
      <th>dribbles_per_game</th>
      <th>yellow_card</th>
      <th>red_card</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Granada</td>
      <td>1</td>
      <td>26</td>
      <td>12</td>
      <td>8</td>
      <td>2</td>
      <td>2</td>
      <td>20</td>
      <td>8</td>
      <td>+12</td>
      <td>0.8</td>
      <td>46.1%</td>
      <td>76.6%</td>
      <td>10.1</td>
      <td>18</td>
      <td>10.8</td>
      <td>109</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Malaga</td>
      <td>2</td>
      <td>25</td>
      <td>12</td>
      <td>8</td>
      <td>1</td>
      <td>3</td>
      <td>15</td>
      <td>8</td>
      <td>+7</td>
      <td>0.6</td>
      <td>45.3%</td>
      <td>75.1%</td>
      <td>10.6</td>
      <td>19.6</td>
      <td>8.1</td>
      <td>92</td>
      <td>8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Alcorcon</td>
      <td>3</td>
      <td>24</td>
      <td>12</td>
      <td>7</td>
      <td>3</td>
      <td>2</td>
      <td>15</td>
      <td>6</td>
      <td>+9</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Deportivo La Coruna</td>
      <td>4</td>
      <td>23</td>
      <td>12</td>
      <td>6</td>
      <td>5</td>
      <td>1</td>
      <td>19</td>
      <td>9</td>
      <td>+10</td>
      <td>1</td>
      <td>47.4%</td>
      <td>77.8%</td>
      <td>12.3</td>
      <td>19.2</td>
      <td>9.9</td>
      <td>98</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Albacete</td>
      <td>5</td>
      <td>20</td>
      <td>12</td>
      <td>5</td>
      <td>5</td>
      <td>2</td>
      <td>19</td>
      <td>12</td>
      <td>+7</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Las Palmas</td>
      <td>6</td>
      <td>20</td>
      <td>12</td>
      <td>5</td>
      <td>5</td>
      <td>2</td>
      <td>17</td>
      <td>10</td>
      <td>+7</td>
      <td>0.6</td>
      <td>53.8%</td>
      <td>81.9%</td>
      <td>10.9</td>
      <td>17.2</td>
      <td>11.8</td>
      <td>87</td>
      <td>3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Almeria</td>
      <td>7</td>
      <td>17</td>
      <td>12</td>
      <td>5</td>
      <td>2</td>
      <td>5</td>
      <td>14</td>
      <td>11</td>
      <td>+3</td>
      <td>0.9</td>
      <td>46%</td>
      <td>70.6%</td>
      <td>10.4</td>
      <td>18.2</td>
      <td>7.9</td>
      <td>107</td>
      <td>11</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Mallorca</td>
      <td>8</td>
      <td>17</td>
      <td>12</td>
      <td>4</td>
      <td>5</td>
      <td>3</td>
      <td>14</td>
      <td>12</td>
      <td>+2</td>
      <td>1.1</td>
      <td>44.1%</td>
      <td>71.8%</td>
      <td>12.8</td>
      <td>20.1</td>
      <td>5.4</td>
      <td>98</td>
      <td>7</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Osasuna</td>
      <td>9</td>
      <td>16</td>
      <td>12</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>13</td>
      <td>12</td>
      <td>+1</td>
      <td>1.1</td>
      <td>41.8%</td>
      <td>69.2%</td>
      <td>10.3</td>
      <td>19.5</td>
      <td>8.5</td>
      <td>98</td>
      <td>3</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Real Oviedo</td>
      <td>10</td>
      <td>16</td>
      <td>12</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>14</td>
      <td>16</td>
      <td>-2</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Numancia</td>
      <td>11</td>
      <td>16</td>
      <td>12</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>12</td>
      <td>15</td>
      <td>-3</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Cadiz</td>
      <td>12</td>
      <td>14</td>
      <td>12</td>
      <td>3</td>
      <td>5</td>
      <td>4</td>
      <td>13</td>
      <td>12</td>
      <td>+1</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Sporting Gijon</td>
      <td>13</td>
      <td>14</td>
      <td>12</td>
      <td>3</td>
      <td>5</td>
      <td>4</td>
      <td>10</td>
      <td>9</td>
      <td>+1</td>
      <td>1.1</td>
      <td>45.6%</td>
      <td>72.6%</td>
      <td>9.9</td>
      <td>20.4</td>
      <td>7.3</td>
      <td>114</td>
      <td>4</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Elche</td>
      <td>14</td>
      <td>14</td>
      <td>12</td>
      <td>3</td>
      <td>5</td>
      <td>4</td>
      <td>12</td>
      <td>18</td>
      <td>-6</td>
      <td>0.9</td>
      <td>47.9%</td>
      <td>75.1%</td>
      <td>10.7</td>
      <td>19.1</td>
      <td>6.5</td>
      <td>103</td>
      <td>6</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Lugo</td>
      <td>15</td>
      <td>13</td>
      <td>12</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>11</td>
      <td>13</td>
      <td>-2</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Reus</td>
      <td>16</td>
      <td>13</td>
      <td>12</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>9</td>
      <td>13</td>
      <td>-4</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Rayo Majadahonda</td>
      <td>17</td>
      <td>13</td>
      <td>12</td>
      <td>4</td>
      <td>1</td>
      <td>7</td>
      <td>9</td>
      <td>17</td>
      <td>-8</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Tenerife</td>
      <td>18</td>
      <td>12</td>
      <td>12</td>
      <td>2</td>
      <td>6</td>
      <td>4</td>
      <td>11</td>
      <td>16</td>
      <td>-5</td>
      <td>1.1</td>
      <td>50.8%</td>
      <td>72.1%</td>
      <td>14.2</td>
      <td>21.1</td>
      <td>6.5</td>
      <td>89</td>
      <td>4</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Real Zaragoza</td>
      <td>19</td>
      <td>11</td>
      <td>12</td>
      <td>2</td>
      <td>5</td>
      <td>5</td>
      <td>12</td>
      <td>15</td>
      <td>-3</td>
      <td>1</td>
      <td>46.9%</td>
      <td>72.7%</td>
      <td>12.1</td>
      <td>20.5</td>
      <td>5.9</td>
      <td>120</td>
      <td>10</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Cordoba</td>
      <td>20</td>
      <td>11</td>
      <td>12</td>
      <td>2</td>
      <td>5</td>
      <td>5</td>
      <td>15</td>
      <td>24</td>
      <td>-9</td>
      <td>0.6</td>
      <td>47.4%</td>
      <td>75.6%</td>
      <td>10.2</td>
      <td>20.1</td>
      <td>8.9</td>
      <td>95</td>
      <td>9</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Extremadura</td>
      <td>21</td>
      <td>9</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>7</td>
      <td>15</td>
      <td>21</td>
      <td>-6</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Gimnastic</td>
      <td>22</td>
      <td>9</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>7</td>
      <td>8</td>
      <td>20</td>
      <td>-12</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>



### 4. 향후 개선 및 보완사항

1) 다른 리그의 데이터를 크롤링하기 위해서는 함수인자로 URL을 바꿔야하는데, 리그 별 URL의 규칙에 따른 기능 추가

    
2) 리그 별로 css 실렉트 시 아이디값(또는 클래스네임)이 상이한 문제점 보완필요

    - 예시) driver.find_elements_by_css_selector("#standings-16547-content tr") 
            #standings-'*****'-content에서 '*****'이 리그별로 상이함
    
3) 프로그램 안정성 보장필요
   - Access Deined 다수발생, 근본적인 해결필요
   - Fake-useragent로 방지 불가
   - 서버접근시간을 늘려주는 방식으로 임시적으로 해결 (time.sleep()함수 이용)
   
   
4) 수집한 데이터를 가공해 통계적으로 분석하여 제공가능
