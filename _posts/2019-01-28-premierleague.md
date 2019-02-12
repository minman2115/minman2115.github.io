---
layout: post
title: "'scrapy'를 이용한 프리미어리거 개인통산기록 웹크롤링"
tags: [개인프로젝트, 웹크롤링]
comments: true
---

웹크롤링 토이프로젝트 결과
- 'scrapy'를 이용한 프리미어리거 개인통산기록(출전수, 도움 등 일부기록) 웹크롤링 수행


```python
import scrapy
```


```python
import requests
from scrapy.http import TextResponse
```

### Scrapy Project
- 프리미어리그 구단별 선수기록
- https://www.premierleague.com/clubs

- crawler 프로젝트 실행
    - `$ scrapy startproject crawler`


```python
!scrapy startproject premierleague
```


```python
!tree premierleague
```


```python
# 구단별 url링크 엘리먼트 수집
req = requests.get('https://www.premierleague.com/clubs')
response = TextResponse(req.url, body=req.text, encoding='utf-8')
club_links = response.xpath('//*[@id="mainContent"]/div/div/div/div[1]/div/ul/li/a/@href').extract()

len(club_links)
club_links
```




    ['/clubs/1/Arsenal/overview',
     '/clubs/127/Bournemouth/overview',
     '/clubs/131/Brighton-and-Hove-Albion/overview',
     '/clubs/43/Burnley/overview',
     '/clubs/46/Cardiff-City/overview',
     '/clubs/4/Chelsea/overview',
     '/clubs/6/Crystal-Palace/overview',
     '/clubs/7/Everton/overview',
     '/clubs/34/Fulham/overview',
     '/clubs/159/Huddersfield-Town/overview',
     '/clubs/26/Leicester-City/overview',
     '/clubs/10/Liverpool/overview',
     '/clubs/11/Manchester-City/overview',
     '/clubs/12/Manchester-United/overview',
     '/clubs/23/Newcastle-United/overview',
     '/clubs/20/Southampton/overview',
     '/clubs/21/Tottenham-Hotspur/overview',
     '/clubs/33/Watford/overview',
     '/clubs/25/West-Ham-United/overview',
     '/clubs/38/Wolverhampton-Wanderers/overview']




```python
for club_link in club_links:
    club_link = 'https://www.premierleague.com' + club_link[:-8] + 'squad'
    print(club_link)
```

    https://www.premierleague.com/clubs/1/Arsenal/squad
    https://www.premierleague.com/clubs/127/Bournemouth/squad
    https://www.premierleague.com/clubs/131/Brighton-and-Hove-Albion/squad
    https://www.premierleague.com/clubs/43/Burnley/squad
    https://www.premierleague.com/clubs/46/Cardiff-City/squad
    https://www.premierleague.com/clubs/4/Chelsea/squad
    https://www.premierleague.com/clubs/6/Crystal-Palace/squad
    https://www.premierleague.com/clubs/7/Everton/squad
    https://www.premierleague.com/clubs/34/Fulham/squad
    https://www.premierleague.com/clubs/159/Huddersfield-Town/squad
    https://www.premierleague.com/clubs/26/Leicester-City/squad
    https://www.premierleague.com/clubs/10/Liverpool/squad
    https://www.premierleague.com/clubs/11/Manchester-City/squad
    https://www.premierleague.com/clubs/12/Manchester-United/squad
    https://www.premierleague.com/clubs/23/Newcastle-United/squad
    https://www.premierleague.com/clubs/20/Southampton/squad
    https://www.premierleague.com/clubs/21/Tottenham-Hotspur/squad
    https://www.premierleague.com/clubs/33/Watford/squad
    https://www.premierleague.com/clubs/25/West-Ham-United/squad
    https://www.premierleague.com/clubs/38/Wolverhampton-Wanderers/squad
    


```python
# 선수 정보 url 링크 수집

player_links = []

for club_link in club_links:
    club_link = 'https://www.premierleague.com' + club_link[:-8] + 'squad'
    req = requests.get(club_link)
    response = TextResponse(req.url, body=req.text, encoding='utf-8')
    player_link = response.xpath('//*[@id="mainContent"]/div[2]/div/ul/li/a/@href').extract()
    for link in player_link:
        player_links.append(link)

len(player_links)
```




    584




```python
player_stat = []


for player_link in player_links:
    player_link = 'https://www.premierleague.com' + player_link[:-8] +'stats?co=1&se=210'
    player_stat.append(player_link)
    
print(len(player_stat))
player_stat
```

    584
    




    ['https://www.premierleague.com/players/2651/Petr-Cech/stats?co=1&se=210',
     'https://www.premierleague.com/players/4985/Bernd-Leno/stats?co=1&se=210',
     'https://www.premierleague.com/players/4245/Emiliano-Martínez/stats?co=1&se=210',
     'https://www.premierleague.com/players/10542/Dejan-Iliev/stats?co=1&se=210',
     'https://www.premierleague.com/players/4474/Héctor-Bellerín/stats?co=1&se=210',
     'https://www.premierleague.com/players/4030/Laurent-Koscielny/stats?co=1&se=210',
     'https://www.premierleague.com/players/11575/Rob-Holding/stats?co=1&se=210',
     'https://www.premierleague.com/players/4472/Nacho-Monreal/stats?co=1&se=210',
     'https://www.premierleague.com/players/3869/Shkodran-Mustafi/stats?co=1&se=210',
     'https://www.premierleague.com/players/32638/Konstantinos-Mavropanos/stats?co=1&se=210',
     'https://www.premierleague.com/players/5368/Sead-Kolasinac/stats?co=1&se=210',
     'https://www.premierleague.com/players/5520/Stephan-Lichtsteiner/stats?co=1&se=210',
     'https://www.premierleague.com/players/4248/Carl-Jenkinson/stats?co=1&se=210',
     'https://www.premierleague.com/players/5093/Sokratis/stats?co=1&se=210',
     'https://www.premierleague.com/players/21958/Zech-Medley/stats?co=1&se=210',
     'https://www.premierleague.com/players/14433/Julio-Pleguezuelo/stats?co=1&se=210',
     'https://www.premierleague.com/players/14436/Jordi-Osei-Tutu/stats?co=1&se=210',
     'https://www.premierleague.com/players/5239/Mohamed-Elneny/stats?co=1&se=210',
     'https://www.premierleague.com/players/5102/Henrikh-Mkhitaryan/stats?co=1&se=210',
     'https://www.premierleague.com/players/3548/Aaron-Ramsey/stats?co=1&se=210',
     'https://www.premierleague.com/players/4714/Mesut-Özil/stats?co=1&se=210',
     'https://www.premierleague.com/players/4717/Alex-Iwobi/stats?co=1&se=210',
     'https://www.premierleague.com/players/10428/Ainsley-Maitland-Niles/stats?co=1&se=210',
     'https://www.premierleague.com/players/12136/Granit-Xhaka/stats?co=1&se=210',
     'https://www.premierleague.com/players/23033/Lucas-Torreira/stats?co=1&se=210',
     'https://www.premierleague.com/players/50512/Matteo-Guendouzi/stats?co=1&se=210',
     'https://www.premierleague.com/players/14445/Emile-Smith-Rowe/stats?co=1&se=210',
     'https://www.premierleague.com/players/14446/Joe-Willock/stats?co=1&se=210',
     'https://www.premierleague.com/players/14453/Charlie-Gilmour/stats?co=1&se=210',
     'https://www.premierleague.com/players/6899/Alexandre-Lacazette/stats?co=1&se=210',
     'https://www.premierleague.com/players/5110/Pierre-Emerick-Aubameyang/stats?co=1&se=210',
     'https://www.premierleague.com/players/3452/Danny-Welbeck/stats?co=1&se=210',
     'https://www.premierleague.com/players/14451/Eddie-Nketiah/stats?co=1&se=210',
     'https://www.premierleague.com/players/19880/Tyreece-John-Jules/stats?co=1&se=210',
     'https://www.premierleague.com/players/49481/Bukayo-Saka/stats?co=1&se=210',
     'https://www.premierleague.com/players/4601/Artur-Boruc/stats?co=1&se=210',
     'https://www.premierleague.com/players/2537/Asmir-Begovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/13703/Aaron-Ramsdale/stats?co=1&se=210',
     'https://www.premierleague.com/players/50453/Mark-Travers/stats?co=1&se=210',
     'https://www.premierleague.com/players/8044/Simon-Francis/stats?co=1&se=210',
     'https://www.premierleague.com/players/8045/Steve-Cook/stats?co=1&se=210',
     'https://www.premierleague.com/players/4499/Nathan-Aké/stats?co=1&se=210',
     'https://www.premierleague.com/players/3242/Charlie-Daniels/stats?co=1&se=210',
     'https://www.premierleague.com/players/3512/Adam-Smith/stats?co=1&se=210',
     'https://www.premierleague.com/players/8900/Tyrone-Mings/stats?co=1&se=210',
     'https://www.premierleague.com/players/13193/Jack-Simpson/stats?co=1&se=210',
     'https://www.premierleague.com/players/26076/Diego-Rico/stats?co=1&se=210',
     'https://www.premierleague.com/players/3390/Dan-Gosling/stats?co=1&se=210',
     'https://www.premierleague.com/players/2338/Andrew-Surman/stats?co=1&se=210',
     'https://www.premierleague.com/players/8049/Marc-Pugh/stats?co=1&se=210',
     'https://www.premierleague.com/players/10766/Lewis-Cook/stats?co=1&se=210',
     'https://www.premierleague.com/players/3766/Junior-Stanislas/stats?co=1&se=210',
     'https://www.premierleague.com/players/8052/Ryan-Fraser/stats?co=1&se=210',
     'https://www.premierleague.com/players/4527/Jordon-Ibe/stats?co=1&se=210',
     'https://www.premierleague.com/players/13706/David-Brooks/stats?co=1&se=210',
     'https://www.premierleague.com/players/37776/Jefferson-Lerma/stats?co=1&se=210',
     'https://www.premierleague.com/players/24214/Kyle-Taylor/stats?co=1&se=210',
     'https://www.premierleague.com/players/26556/Mihai-Alexandru-Dobre/stats?co=1&se=210',
     'https://www.premierleague.com/players/50463/Nnamdi-Ofoborh/stats?co=1&se=210',
     'https://www.premierleague.com/players/8454/Callum-Wilson/stats?co=1&se=210',
     'https://www.premierleague.com/players/3926/Joshua-King/stats?co=1&se=210',
     'https://www.premierleague.com/players/1526/Jermain-Defoe/stats?co=1&se=210',
     'https://www.premierleague.com/players/19603/Lys-Mousset/stats?co=1&se=210',
     'https://www.premierleague.com/players/12192/Mat-Ryan/stats?co=1&se=210',
     'https://www.premierleague.com/players/3457/Jason-Steele/stats?co=1&se=210',
     'https://www.premierleague.com/players/3505/David-Button/stats?co=1&se=210',
     'https://www.premierleague.com/players/8162/Bruno/stats?co=1&se=210',
     'https://www.premierleague.com/players/5721/Gaëtan-Bong/stats?co=1&se=210',
     'https://www.premierleague.com/players/8163/Lewis-Dunk/stats?co=1&se=210',
     'https://www.premierleague.com/players/16234/Ezequiel-Schelotto/stats?co=1&se=210',
     'https://www.premierleague.com/players/3601/Shane-Duffy/stats?co=1&se=210',
     'https://www.premierleague.com/players/5428/Markus-Suttner/stats?co=1&se=210',
     'https://www.premierleague.com/players/20183/Leon-Balogun/stats?co=1&se=210',
     'https://www.premierleague.com/players/23591/Bernardo/stats?co=1&se=210',
     'https://www.premierleague.com/players/4955/Martín-Montoya/stats?co=1&se=210',
     'https://www.premierleague.com/players/15288/Ben-Barclay/stats?co=1&se=210',
     'https://www.premierleague.com/players/14548/Ben-White/stats?co=1&se=210',
     'https://www.premierleague.com/players/8168/Dale-Stephens/stats?co=1&se=210',
     'https://www.premierleague.com/players/5156/Beram-Kayal/stats?co=1&se=210',
     'https://www.premierleague.com/players/8982/Anthony-Knockaert/stats?co=1&se=210',
     'https://www.premierleague.com/players/22542/Pascal-Groß/stats?co=1&se=210',
     'https://www.premierleague.com/players/8171/Solly-March/stats?co=1&se=210',
     'https://www.premierleague.com/players/16790/Davy-Pröpper/stats?co=1&se=210',
     'https://www.premierleague.com/players/50245/Yves-Bissouma/stats?co=1&se=210',
     'https://www.premierleague.com/players/14549/Will-Collar/stats?co=1&se=210',
     'https://www.premierleague.com/players/4772/Glenn-Murray/stats?co=1&se=210',
     'https://www.premierleague.com/players/12212/José-Izquierdo/stats?co=1&se=210',
     'https://www.premierleague.com/players/7124/Jürgen-Locadia/stats?co=1&se=210',
     'https://www.premierleague.com/players/37587/Florin-Andone/stats?co=1&se=210',
     'https://www.premierleague.com/players/15480/Alireza-Jahanbakhsh/stats?co=1&se=210',
     'https://www.premierleague.com/players/32728/Viktor-Gyokeres/stats?co=1&se=210',
     'https://www.premierleague.com/players/21653/Aaron-Connolly/stats?co=1&se=210',
     'https://www.premierleague.com/players/2933/Tom-Heaton/stats?co=1&se=210',
     'https://www.premierleague.com/players/4154/Anders-Lindegaard/stats?co=1&se=210',
     'https://www.premierleague.com/players/10079/Nick-Pope/stats?co=1&se=210',
     'https://www.premierleague.com/players/2852/Adam-Legzdins/stats?co=1&se=210',
     'https://www.premierleague.com/players/3143/Joe-Hart/stats?co=1&se=210',
     'https://www.premierleague.com/players/4487/Matthew-Lowton/stats?co=1&se=210',
     'https://www.premierleague.com/players/8691/Charlie-Taylor/stats?co=1&se=210',
     'https://www.premierleague.com/players/8143/James-Tarkowski/stats?co=1&se=210',
     'https://www.premierleague.com/players/3909/Ben-Mee/stats?co=1&se=210',
     'https://www.premierleague.com/players/3999/Stephen-Ward/stats?co=1&se=210',
     'https://www.premierleague.com/players/2517/Phil-Bardsley/stats?co=1&se=210',
     'https://www.premierleague.com/players/3837/Kevin-Long/stats?co=1&se=210',
     'https://www.premierleague.com/players/9117/Ben-Gibson/stats?co=1&se=210',
     'https://www.premierleague.com/players/55603/Ali-Koiki/stats?co=1&se=210',
     'https://www.premierleague.com/players/2889/Jack-Cork/stats?co=1&se=210',
     'https://www.premierleague.com/players/4158/Robbie-Brady/stats?co=1&se=210',
     'https://www.premierleague.com/players/8586/Jeff-Hendrick/stats?co=1&se=210',
     'https://www.premierleague.com/players/5345/Steven-Defour/stats?co=1&se=210',
     'https://www.premierleague.com/players/6210/Johann-Gudmundsson/stats?co=1&se=210',
     'https://www.premierleague.com/players/4489/Ashley-Westwood/stats?co=1&se=210',
     'https://www.premierleague.com/players/2270/Aaron-Lennon/stats?co=1&se=210',
     'https://www.premierleague.com/players/4007/Sam-Vokes/stats?co=1&se=210',
     'https://www.premierleague.com/players/8246/Ashley-Barnes/stats?co=1&se=210',
     'https://www.premierleague.com/players/3756/Chris-Wood/stats?co=1&se=210',
     'https://www.premierleague.com/players/10559/Daniel-Agyei/stats?co=1&se=210',
     'https://www.premierleague.com/players/4851/Matej-Vydra/stats?co=1&se=210',
     'https://www.premierleague.com/players/24797/Dwight-McNeil/stats?co=1&se=210',
     'https://www.premierleague.com/players/4120/Neil-Etheridge/stats?co=1&se=210',
     'https://www.premierleague.com/players/8871/Alex-Smithies/stats?co=1&se=210',
     'https://www.premierleague.com/players/1926/Brian-Murphy/stats?co=1&se=210',
     'https://www.premierleague.com/players/3138/Lee-Peltier/stats?co=1&se=210',
     'https://www.premierleague.com/players/3672/Joe-Bennett/stats?co=1&se=210',
     'https://www.premierleague.com/players/4577/Sean-Morrison/stats?co=1&se=210',
     'https://www.premierleague.com/players/10633/Bruno-Ecuele-Manga/stats?co=1&se=210',
     'https://www.premierleague.com/players/4416/Jazz-Richards/stats?co=1&se=210',
     'https://www.premierleague.com/players/7500/Sol-Bamba/stats?co=1&se=210',
     'https://www.premierleague.com/players/2836/Matthew-Connolly/stats?co=1&se=210',
     'https://www.premierleague.com/players/22046/Callum-Paterson/stats?co=1&se=210',
     'https://www.premierleague.com/players/3906/Greg-Cunningham/stats?co=1&se=210',
     'https://www.premierleague.com/players/10074/Joe-Ralls/stats?co=1&se=210',
     'https://www.premierleague.com/players/4856/Kadeem-Harris/stats?co=1&se=210',
     'https://www.premierleague.com/players/23520/Loïc-Damour/stats?co=1&se=210',
     'https://www.premierleague.com/players/4561/Josh-Murphy/stats?co=1&se=210',
     'https://www.premierleague.com/players/8192/Bobby-Reid/stats?co=1&se=210',
     'https://www.premierleague.com/players/4736/Aron-Gunnarsson/stats?co=1&se=210',
     'https://www.premierleague.com/players/22952/Víctor-Camarasa/stats?co=1&se=210',
     'https://www.premierleague.com/players/8050/Harry-Arter/stats?co=1&se=210',
     'https://www.premierleague.com/players/3831/Danny-Ward/stats?co=1&se=210',
     'https://www.premierleague.com/players/6627/Kenneth-Zohore/stats?co=1&se=210',
     'https://www.premierleague.com/players/4006/Nathaniel-Mendez-Laing/stats?co=1&se=210',
     'https://www.premierleague.com/players/3823/David-Junior-Hoilett/stats?co=1&se=210',
     'https://www.premierleague.com/players/8335/Gary-Madine/stats?co=1&se=210',
     'https://www.premierleague.com/players/10466/Willy-Caballero/stats?co=1&se=210',
     'https://www.premierleague.com/players/2758/Robert-Green/stats?co=1&se=210',
     'https://www.premierleague.com/players/23695/Marcin-Bulka/stats?co=1&se=210',
     'https://www.premierleague.com/players/11373/Kepa-Arrizabalaga/stats?co=1&se=210',
     'https://www.premierleague.com/players/14565/Jamie-Cumming/stats?co=1&se=210',
     'https://www.premierleague.com/players/16801/Antonio-Rüdiger/stats?co=1&se=210',
     'https://www.premierleague.com/players/4093/Marcos-Alonso/stats?co=1&se=210',
     'https://www.premierleague.com/players/3983/Victor-Moses/stats?co=1&se=210',
     'https://www.premierleague.com/players/25313/Davide-Zappacosta/stats?co=1&se=210',
     'https://www.premierleague.com/players/2620/Gary-Cahill/stats?co=1&se=210',
     'https://www.premierleague.com/players/4495/Andreas-Christensen/stats?co=1&se=210',
     'https://www.premierleague.com/players/4496/César-Azpilicueta/stats?co=1&se=210',
     'https://www.premierleague.com/players/4100/David-Luiz/stats?co=1&se=210',
     'https://www.premierleague.com/players/16803/Emerson/stats?co=1&se=210',
     'https://www.premierleague.com/players/19731/Ethan-Ampadu/stats?co=1&se=210',
     'https://www.premierleague.com/players/2417/Cesc-Fàbregas/stats?co=1&se=210',
     'https://www.premierleague.com/players/3670/Daniel-Drinkwater/stats?co=1&se=210',
     "https://www.premierleague.com/players/13492/N'Golo-Kanté/stats?co=1&se=210",
     'https://www.premierleague.com/players/4115/Ross-Barkley/stats?co=1&se=210',
     'https://www.premierleague.com/players/5179/Ruben-Loftus-Cheek/stats?co=1&se=210',
     'https://www.premierleague.com/players/5682/Jorginho/stats?co=1&se=210',
     'https://www.premierleague.com/players/12520/Mateo-Kovacic/stats?co=1&se=210',
     'https://www.premierleague.com/players/14587/George-McEachran/stats?co=1&se=210',
     'https://www.premierleague.com/players/5804/Álvaro-Morata/stats?co=1&se=210',
     'https://www.premierleague.com/players/4503/Eden-Hazard/stats?co=1&se=210',
     'https://www.premierleague.com/players/4972/Pedro/stats?co=1&se=210',
     'https://www.premierleague.com/players/4481/Olivier-Giroud/stats?co=1&se=210',
     'https://www.premierleague.com/players/4748/Willian/stats?co=1&se=210',
     'https://www.premierleague.com/players/23702/Callum-Hudson-Odoi/stats?co=1&se=210',
     'https://www.premierleague.com/players/2664/Julian-Speroni/stats?co=1&se=210',
     'https://www.premierleague.com/players/2569/Wayne-Hennessey/stats?co=1&se=210',
     'https://www.premierleague.com/players/7548/Vicente-Guaita/stats?co=1&se=210',
     'https://www.premierleague.com/players/10855/Dion-Henry/stats?co=1&se=210',
     'https://www.premierleague.com/players/3362/Patrick-van-Aanholt/stats?co=1&se=210',
     'https://www.premierleague.com/players/3517/James-Tomkins/stats?co=1&se=210',
     'https://www.premierleague.com/players/3807/Scott-Dann/stats?co=1&se=210',
     'https://www.premierleague.com/players/4796/Mamadou-Sakho/stats?co=1&se=210',
     'https://www.premierleague.com/players/8980/Jeffrey-Schlupp/stats?co=1&se=210',
     'https://www.premierleague.com/players/10444/Pape-Souaré/stats?co=1&se=210',
     'https://www.premierleague.com/players/14164/Aaron-Wan-Bissaka/stats?co=1&se=210',
     'https://www.premierleague.com/players/3644/Martin-Kelly/stats?co=1&se=210',
     'https://www.premierleague.com/players/4878/Jairo-Riedewald/stats?co=1&se=210',
     'https://www.premierleague.com/players/3929/Joel-Ward/stats?co=1&se=210',
     'https://www.premierleague.com/players/14149/Sam-Woods/stats?co=1&se=210',
     'https://www.premierleague.com/players/5859/Luka-Milivojevic/stats?co=1&se=210',
     'https://www.premierleague.com/players/3724/Andros-Townsend/stats?co=1&se=210',
     'https://www.premierleague.com/players/4224/James-McArthur/stats?co=1&se=210',
     'https://www.premierleague.com/players/4084/Jason-Puncheon/stats?co=1&se=210',
     'https://www.premierleague.com/players/10191/Sullay-Kaikai/stats?co=1&se=210',
     'https://www.premierleague.com/players/4767/Jonathan-Williams/stats?co=1&se=210',
     'https://www.premierleague.com/players/5861/Cheikhou-Kouyaté/stats?co=1&se=210',
     'https://www.premierleague.com/players/5377/Max-Meyer/stats?co=1&se=210',
     'https://www.premierleague.com/players/24342/Gio-McGregor/stats?co=1&se=210',
     'https://www.premierleague.com/players/15134/Nya-Kirby/stats?co=1&se=210',
     'https://www.premierleague.com/players/15685/Alexander-Sørloth/stats?co=1&se=210',
     'https://www.premierleague.com/players/4539/Wilfried-Zaha/stats?co=1&se=210',
     'https://www.premierleague.com/players/4492/Christian-Benteke/stats?co=1&se=210',
     'https://www.premierleague.com/players/4396/Connor-Wickham/stats?co=1&se=210',
     'https://www.premierleague.com/players/5595/Jordan-Ayew/stats?co=1&se=210',
     'https://www.premierleague.com/players/4640/Jordan-Pickford/stats?co=1&se=210',
     'https://www.premierleague.com/players/4779/Maarten-Stekelenburg/stats?co=1&se=210',
     'https://www.premierleague.com/players/14619/Mateusz-Hewelt/stats?co=1&se=210',
     'https://www.premierleague.com/players/14428/João-Virgínia/stats?co=1&se=210',
     'https://www.premierleague.com/players/3030/Leighton-Baines/stats?co=1&se=210',
     'https://www.premierleague.com/players/4333/Michael-Keane/stats?co=1&se=210',
     'https://www.premierleague.com/players/3222/Phil-Jagielka/stats?co=1&se=210',
     'https://www.premierleague.com/players/3600/Seamus-Coleman/stats?co=1&se=210',
     'https://www.premierleague.com/players/10564/Mason-Holgate/stats?co=1&se=210',
     'https://www.premierleague.com/players/10449/Jonjoe-Kenny/stats?co=1&se=210',
     'https://www.premierleague.com/players/5758/Lucas-Digne/stats?co=1&se=210',
     'https://www.premierleague.com/players/32923/Yerry-Mina/stats?co=1&se=210',
     'https://www.premierleague.com/players/5175/Kurt-Zouma/stats?co=1&se=210',
     'https://www.premierleague.com/players/4506/Tyias-Browning/stats?co=1&se=210',
     'https://www.premierleague.com/players/4615/Morgan-Schneiderlin/stats?co=1&se=210',
     'https://www.premierleague.com/players/3979/James-McCarthy/stats?co=1&se=210',
     'https://www.premierleague.com/players/12582/Idrissa-Gueye/stats?co=1&se=210',
     'https://www.premierleague.com/players/4421/Gylfi-Sigurdsson/stats?co=1&se=210',
     'https://www.premierleague.com/players/13389/Tom-Davies/stats?co=1&se=210',
     'https://www.premierleague.com/players/14623/Beni-Baningime/stats?co=1&se=210',
     'https://www.premierleague.com/players/10455/Kieran-Dowell/stats?co=1&se=210',
     'https://www.premierleague.com/players/5907/Bernard/stats?co=1&se=210',
     'https://www.premierleague.com/players/5061/André-Gomes/stats?co=1&se=210',
     'https://www.premierleague.com/players/2839/Theo-Walcott/stats?co=1&se=210',
     'https://www.premierleague.com/players/12114/Cenk-Tosun/stats?co=1&se=210',
     'https://www.premierleague.com/players/13390/Oumar-Niasse/stats?co=1&se=210',
     'https://www.premierleague.com/players/9576/Dominic-Calvert-Lewin/stats?co=1&se=210',
     'https://www.premierleague.com/players/13278/Ademola-Lookman/stats?co=1&se=210',
     'https://www.premierleague.com/players/24630/Richarlison/stats?co=1&se=210',
     'https://www.premierleague.com/players/4511/Marcus-Bettinelli/stats?co=1&se=210',
     'https://www.premierleague.com/players/19771/Fabri/stats?co=1&se=210',
     'https://www.premierleague.com/players/7213/Sergio-Rico/stats?co=1&se=210',
     'https://www.premierleague.com/players/23759/Luca-Ashby-Hammond/stats?co=1&se=210',
     'https://www.premierleague.com/players/12536/Denis-Odoi/stats?co=1&se=210',
     'https://www.premierleague.com/players/4276/Tim-Ream/stats?co=1&se=210',
     'https://www.premierleague.com/players/8438/Cyrus-Christie/stats?co=1&se=210',
     'https://www.premierleague.com/players/20503/Maxime-Le-Marchand/stats?co=1&se=210',
     'https://www.premierleague.com/players/9960/Alfie-Mawson/stats?co=1&se=210',
     'https://www.premierleague.com/players/4620/Calum-Chambers/stats?co=1&se=210',
     'https://www.premierleague.com/players/13561/Timothy-Fosu-Mensah/stats?co=1&se=210',
     'https://www.premierleague.com/players/8185/Joe-Bryan/stats?co=1&se=210',
     'https://www.premierleague.com/players/14657/Steven-Sessegnon/stats?co=1&se=210',
     'https://www.premierleague.com/players/14656/Ryan-Sessegnon/stats?co=1&se=210',
     'https://www.premierleague.com/players/3844/Kevin-McDonald/stats?co=1&se=210',
     'https://www.premierleague.com/players/20178/Neeskens-Kebano/stats?co=1&se=210',
     'https://www.premierleague.com/players/5154/Stefan-Johansen/stats?co=1&se=210',
     'https://www.premierleague.com/players/3894/Tom-Cairney/stats?co=1&se=210',
     'https://www.premierleague.com/players/7431/Ibrahima-Cissé/stats?co=1&se=210',
     'https://www.premierleague.com/players/7013/Jean-Michael-Seri/stats?co=1&se=210',
     'https://www.premierleague.com/players/4750/André-Schürrle/stats?co=1&se=210',
     'https://www.premierleague.com/players/32162/André-Frank-Zambo-Anguissa/stats?co=1&se=210',
     'https://www.premierleague.com/players/14676/Luca-De-La-Torre/stats?co=1&se=210',
     "https://www.premierleague.com/players/14671/Matt-O'Riley/stats?co=1&se=210",
     'https://www.premierleague.com/players/33185/Harvey-Elliott/stats?co=1&se=210',
     'https://www.premierleague.com/players/19606/Floyd-Ayité/stats?co=1&se=210',
     'https://www.premierleague.com/players/11267/Aboubakar-Kamara/stats?co=1&se=210',
     'https://www.premierleague.com/players/5879/Aleksandar-Mitrovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/12949/Luciano-Vietto/stats?co=1&se=210',
     'https://www.premierleague.com/players/12444/Jonas-Lössl/stats?co=1&se=210',
     'https://www.premierleague.com/players/3183/Ben-Hamer/stats?co=1&se=210',
     'https://www.premierleague.com/players/24346/Ryan-Schofield/stats?co=1&se=210',
     'https://www.premierleague.com/players/8878/Tommy-Smith/stats?co=1&se=210',
     'https://www.premierleague.com/players/19608/Chris-Löwe/stats?co=1&se=210',
     'https://www.premierleague.com/players/7103/Zanka/stats?co=1&se=210',
     'https://www.premierleague.com/players/19607/Christopher-Schindler/stats?co=1&se=210',
     'https://www.premierleague.com/players/11134/Jon-Gorenc-Stankovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/12359/Terence-Kongolo/stats?co=1&se=210',
     'https://www.premierleague.com/players/12173/Florent-Hadergjonaj/stats?co=1&se=210',
     'https://www.premierleague.com/players/5097/Erik-Durm/stats?co=1&se=210',
     'https://www.premierleague.com/players/3801/Jonathan-Hogg/stats?co=1&se=210',
     'https://www.premierleague.com/players/8882/Philip-Billing/stats?co=1&se=210',
     'https://www.premierleague.com/players/19619/Aaron-Mooy/stats?co=1&se=210',
     'https://www.premierleague.com/players/22591/Abdelhamid-Sabiri/stats?co=1&se=210',
     'https://www.premierleague.com/players/10964/Rajiv-van-La-Parra/stats?co=1&se=210',
     'https://www.premierleague.com/players/9473/Danny-Williams/stats?co=1&se=210',
     'https://www.premierleague.com/players/4433/Alex-Pritchard/stats?co=1&se=210',
     'https://www.premierleague.com/players/19673/Ramadan-Sobhi/stats?co=1&se=210',
     'https://www.premierleague.com/players/15687/Juninho-Bacuna/stats?co=1&se=210',
     'https://www.premierleague.com/players/20646/Isaac-Mbenza/stats?co=1&se=210',
     'https://www.premierleague.com/players/19611/Elias-Kachunga/stats?co=1&se=210',
     'https://www.premierleague.com/players/16747/Laurent-Depoitre/stats?co=1&se=210',
     'https://www.premierleague.com/players/21677/Collin-Quaner/stats?co=1&se=210',
     'https://www.premierleague.com/players/22570/Steve-Mounie/stats?co=1&se=210',
     'https://www.premierleague.com/players/24253/Adama-Diakhaby/stats?co=1&se=210',
     'https://www.premierleague.com/players/2508/Kasper-Schmeichel/stats?co=1&se=210',
     'https://www.premierleague.com/players/4786/Eldin-Jakupovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/4522/Danny-Ward/stats?co=1&se=210',
     'https://www.premierleague.com/players/3157/Danny-Simpson/stats?co=1&se=210',
     'https://www.premierleague.com/players/13491/Ben-Chilwell/stats?co=1&se=210',
     'https://www.premierleague.com/players/8966/Wes-Morgan/stats?co=1&se=210',
     'https://www.premierleague.com/players/9566/Harry-Maguire/stats?co=1&se=210',
     'https://www.premierleague.com/players/5371/Christian-Fuchs/stats?co=1&se=210',
     'https://www.premierleague.com/players/13490/Yohan-Benalouane/stats?co=1&se=210',
     'https://www.premierleague.com/players/5343/Ricardo-Pereira/stats?co=1&se=210',
     'https://www.premierleague.com/players/3156/Jonny-Evans/stats?co=1&se=210',
     'https://www.premierleague.com/players/26216/Caglar-Söyüncü/stats?co=1&se=210',
     'https://www.premierleague.com/players/14693/Josh-Knight/stats?co=1&se=210',
     'https://www.premierleague.com/players/7946/Demarai-Gray/stats?co=1&se=210',
     'https://www.premierleague.com/players/3564/Marc-Albrighton/stats?co=1&se=210',
     'https://www.premierleague.com/players/11357/Adrien-Silva/stats?co=1&se=210',
     'https://www.premierleague.com/players/12306/Daniel-Amartey/stats?co=1&se=210',
     'https://www.premierleague.com/players/7234/Vicente-Iborra/stats?co=1&se=210',
     'https://www.premierleague.com/players/3669/Matty-James/stats?co=1&se=210',
     'https://www.premierleague.com/players/20479/Wilfred-Ndidi/stats?co=1&se=210',
     'https://www.premierleague.com/players/8456/James-Maddison/stats?co=1&se=210',
     'https://www.premierleague.com/players/8976/Andy-King/stats?co=1&se=210',
     'https://www.premierleague.com/players/19617/Nampalys-Mendy/stats?co=1&se=210',
     'https://www.premierleague.com/players/6889/Rachid-Ghezzal/stats?co=1&se=210',
     'https://www.premierleague.com/players/13248/Hamza-Choudhury/stats?co=1&se=210',
     'https://www.premierleague.com/players/23784/Thakgalo-Leshabela/stats?co=1&se=210',
     'https://www.premierleague.com/players/13554/Kelechi-Iheanacho/stats?co=1&se=210',
     'https://www.premierleague.com/players/8979/Jamie-Vardy/stats?co=1&se=210',
     'https://www.premierleague.com/players/13494/Shinji-Okazaki/stats?co=1&se=210',
     'https://www.premierleague.com/players/32936/Fousseni-Diabaté/stats?co=1&se=210',
     'https://www.premierleague.com/players/4179/Simon-Mignolet/stats?co=1&se=210',
     'https://www.premierleague.com/players/20559/Alisson/stats?co=1&se=210',
     'https://www.premierleague.com/players/19909/Kamil-Grabara/stats?co=1&se=210',
     'https://www.premierleague.com/players/4604/Nathaniel-Clyne/stats?co=1&se=210',
     'https://www.premierleague.com/players/5140/Virgil-van-Dijk/stats?co=1&se=210',
     'https://www.premierleague.com/players/4813/Dejan-Lovren/stats?co=1&se=210',
     'https://www.premierleague.com/players/10651/Joseph-Gomez/stats?co=1&se=210',
     'https://www.premierleague.com/players/7219/Alberto-Moreno/stats?co=1&se=210',
     'https://www.premierleague.com/players/10458/Andrew-Robertson/stats?co=1&se=210',
     'https://www.premierleague.com/players/5375/Joel-Matip/stats?co=1&se=210',
     'https://www.premierleague.com/players/14732/Trent-Alexander-Arnold/stats?co=1&se=210',
     'https://www.premierleague.com/players/7114/Georginio-Wijnaldum/stats?co=1&se=210',
     'https://www.premierleague.com/players/2100/James-Milner/stats?co=1&se=210',
     'https://www.premierleague.com/players/3712/Jordan-Henderson/stats?co=1&se=210',
     'https://www.premierleague.com/players/4618/Adam-Lallana/stats?co=1&se=210',
     'https://www.premierleague.com/players/4252/Alex-Oxlade-Chamberlain/stats?co=1&se=210',
     'https://www.premierleague.com/players/11247/Fabinho/stats?co=1&se=210',
     'https://www.premierleague.com/players/12316/Naby-Keita/stats?co=1&se=210',
     'https://www.premierleague.com/players/5078/Lazar-Markovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/23815/Curtis-Jones/stats?co=1&se=210',
     'https://www.premierleague.com/players/23820/Rafael-Camacho/stats?co=1&se=210',
     'https://www.premierleague.com/players/13511/Roberto-Firmino/stats?co=1&se=210',
     'https://www.premierleague.com/players/5178/Mohamed-Salah/stats?co=1&se=210',
     'https://www.premierleague.com/players/6519/Sadio-Mané/stats?co=1&se=210',
     'https://www.premierleague.com/players/10443/Dominic-Solanke/stats?co=1&se=210',
     'https://www.premierleague.com/players/3154/Daniel-Sturridge/stats?co=1&se=210',
     'https://www.premierleague.com/players/10465/Divock-Origi/stats?co=1&se=210',
     'https://www.premierleague.com/players/5267/Xherdan-Shaqiri/stats?co=1&se=210',
     'https://www.premierleague.com/players/14742/Rhian-Brewster/stats?co=1&se=210',
     'https://www.premierleague.com/players/5810/Claudio-Bravo/stats?co=1&se=210',
     'https://www.premierleague.com/players/12707/Ederson/stats?co=1&se=210',
     'https://www.premierleague.com/players/14766/Daniel-Grimshaw/stats?co=1&se=210',
     'https://www.premierleague.com/players/19911/Arijanet-Muric/stats?co=1&se=210',
     'https://www.premierleague.com/players/3955/Kyle-Walker/stats?co=1&se=210',
     'https://www.premierleague.com/players/5328/Danilo/stats?co=1&se=210',
     'https://www.premierleague.com/players/3653/Vincent-Kompany/stats?co=1&se=210',
     'https://www.premierleague.com/players/4505/John-Stones/stats?co=1&se=210',
     'https://www.premierleague.com/players/11044/Aymeric-Laporte/stats?co=1&se=210',
     'https://www.premierleague.com/players/5575/Benjamin-Mendy/stats?co=1&se=210',
     'https://www.premierleague.com/players/6058/Nicolás-Otamendi/stats?co=1&se=210',
     'https://www.premierleague.com/players/5334/Eliaquim-Mangala/stats?co=1&se=210',
     'https://www.premierleague.com/players/33238/Philippe-Sandler/stats?co=1&se=210',
     'https://www.premierleague.com/players/24635/Eric-Garcia/stats?co=1&se=210',
     'https://www.premierleague.com/players/5101/Ilkay-Gündogan/stats?co=1&se=210',
     'https://www.premierleague.com/players/4288/Kevin-De-Bruyne/stats?co=1&se=210',
     'https://www.premierleague.com/players/3799/Fabian-Delph/stats?co=1&se=210',
     'https://www.premierleague.com/players/5067/Bernardo-Silva/stats?co=1&se=210',
     'https://www.premierleague.com/players/4145/David-Silva/stats?co=1&se=210',
     'https://www.premierleague.com/players/4804/Fernandinho/stats?co=1&se=210',
     'https://www.premierleague.com/players/19620/Oleksandr-Zinchenko/stats?co=1&se=210',
     'https://www.premierleague.com/players/14805/Phil-Foden/stats?co=1&se=210',
     'https://www.premierleague.com/players/8983/Riyad-Mahrez/stats?co=1&se=210',
     'https://www.premierleague.com/players/50975/Adrian-Bernabe/stats?co=1&se=210',
     'https://www.premierleague.com/players/50989/Claudio-Gomes/stats?co=1&se=210',
     'https://www.premierleague.com/players/19922/Felix-Nmecha/stats?co=1&se=210',
     'https://www.premierleague.com/players/4316/Raheem-Sterling/stats?co=1&se=210',
     'https://www.premierleague.com/players/4328/Sergio-Agüero/stats?co=1&se=210',
     'https://www.premierleague.com/players/5384/Leroy-Sané/stats?co=1&se=210',
     'https://www.premierleague.com/players/19680/Gabriel-Jesus/stats?co=1&se=210',
     'https://www.premierleague.com/players/14800/Brahim-Diaz/stats?co=1&se=210',
     'https://www.premierleague.com/players/4330/David-De-Gea/stats?co=1&se=210',
     'https://www.premierleague.com/players/13555/Sergio-Romero/stats?co=1&se=210',
     'https://www.premierleague.com/players/1870/Lee-Grant/stats?co=1&se=210',
     'https://www.premierleague.com/players/35733/Matej-Kovar/stats?co=1&se=210',
     'https://www.premierleague.com/players/5066/Victor-Lindelöf/stats?co=1&se=210',
     'https://www.premierleague.com/players/12937/Eric-Bailly/stats?co=1&se=210',
     'https://www.premierleague.com/players/3820/Phil-Jones/stats?co=1&se=210',
     'https://www.premierleague.com/players/10472/Marcos-Rojo/stats?co=1&se=210',
     'https://www.premierleague.com/players/3613/Chris-Smalling/stats?co=1&se=210',
     'https://www.premierleague.com/players/3062/Ashley-Young/stats?co=1&se=210',
     'https://www.premierleague.com/players/4608/Luke-Shaw/stats?co=1&se=210',
     'https://www.premierleague.com/players/3285/Antonio-Valencia/stats?co=1&se=210',
     'https://www.premierleague.com/players/12854/Matteo-Darmian/stats?co=1&se=210',
     'https://www.premierleague.com/players/24252/Diogo-Dalot/stats?co=1&se=210',
     'https://www.premierleague.com/players/3920/Paul-Pogba/stats?co=1&se=210',
     'https://www.premierleague.com/players/4287/Juan-Mata/stats?co=1&se=210',
     'https://www.premierleague.com/players/4337/Jesse-Lingard/stats?co=1&se=210',
     'https://www.premierleague.com/players/10475/Ander-Herrera/stats?co=1&se=210',
     'https://www.premierleague.com/players/3604/Marouane-Fellaini/stats?co=1&se=210',
     'https://www.premierleague.com/players/3861/Nemanja-Matic/stats?co=1&se=210',
     'https://www.premierleague.com/players/14824/Scott-McTominay/stats?co=1&se=210',
     'https://www.premierleague.com/players/5906/Fred/stats?co=1&se=210',
     'https://www.premierleague.com/players/10476/Andreas-Pereira/stats?co=1&se=210',
     'https://www.premierleague.com/players/20858/Tahith-Chong/stats?co=1&se=210',
     'https://www.premierleague.com/players/14830/Angel-Gomes/stats?co=1&se=210',
     'https://www.premierleague.com/players/14835/Ethan-Hamilton/stats?co=1&se=210',
     'https://www.premierleague.com/players/23841/James-Garner/stats?co=1&se=210',
     'https://www.premierleague.com/players/4973/Alexis-Sánchez/stats?co=1&se=210',
     'https://www.premierleague.com/players/4290/Romelu-Lukaku/stats?co=1&se=210',
     'https://www.premierleague.com/players/11272/Anthony-Martial/stats?co=1&se=210',
     'https://www.premierleague.com/players/13565/Marcus-Rashford/stats?co=1&se=210',
     'https://www.premierleague.com/players/21820/Joshua-Bohui/stats?co=1&se=210',
     'https://www.premierleague.com/players/25266/Mason-Greenwood/stats?co=1&se=210',
     'https://www.premierleague.com/players/2214/Robert-Elliot/stats?co=1&se=210',
     'https://www.premierleague.com/players/6451/Martin-Dubravka/stats?co=1&se=210',
     'https://www.premierleague.com/players/9251/Karl-Darlow/stats?co=1&se=210',
     'https://www.premierleague.com/players/10479/Freddy-Woodman/stats?co=1&se=210',
     'https://www.premierleague.com/players/3562/Ciaran-Clark/stats?co=1&se=210',
     'https://www.premierleague.com/players/4340/Paul-Dummett/stats?co=1&se=210',
     'https://www.premierleague.com/players/9257/Jamaal-Lascelles/stats?co=1&se=210',
     'https://www.premierleague.com/players/4918/Javier-Manquillo/stats?co=1&se=210',
     'https://www.premierleague.com/players/16607/Florian-Lejeune/stats?co=1&se=210',
     'https://www.premierleague.com/players/10522/DeAndre-Yedlin/stats?co=1&se=210',
     'https://www.premierleague.com/players/10482/Jamie-Sterry/stats?co=1&se=210',
     'https://www.premierleague.com/players/5220/Fabian-Schär/stats?co=1&se=210',
     'https://www.premierleague.com/players/5672/Federico-Fernández/stats?co=1&se=210',
     'https://www.premierleague.com/players/9662/Jacob-Murphy/stats?co=1&se=210',
     'https://www.premierleague.com/players/4135/Jonjo-Shelvey/stats?co=1&se=210',
     'https://www.premierleague.com/players/3982/Mohamed-Diamé/stats?co=1&se=210',
     'https://www.premierleague.com/players/3934/Matt-Ritchie/stats?co=1&se=210',
     'https://www.premierleague.com/players/4713/Isaac-Hayden/stats?co=1&se=210',
     'https://www.premierleague.com/players/4859/Christian-Atsu/stats?co=1&se=210',
     'https://www.premierleague.com/players/4656/Ki-Sung-yueng/stats?co=1&se=210',
     'https://www.premierleague.com/players/13285/Kenedy/stats?co=1&se=210',
     'https://www.premierleague.com/players/14897/Sean-Longstaff/stats?co=1&se=210',
     'https://www.premierleague.com/players/10486/Callum-Roberts/stats?co=1&se=210',
     'https://www.premierleague.com/players/10487/Ayoze-Pérez/stats?co=1&se=210',
     'https://www.premierleague.com/players/6425/Joselu/stats?co=1&se=210',
     'https://www.premierleague.com/players/20196/Yoshinori-Muto/stats?co=1&se=210',
     'https://www.premierleague.com/players/6030/Salomón-Rondón/stats?co=1&se=210',
     'https://www.premierleague.com/players/4573/Alex-McCarthy/stats?co=1&se=210',
     'https://www.premierleague.com/players/3170/Fraser-Forster/stats?co=1&se=210',
     'https://www.premierleague.com/players/15269/Angus-Gunn/stats?co=1&se=210',
     'https://www.premierleague.com/players/14982/Harry-Lewis/stats?co=1&se=210',
     'https://www.premierleague.com/players/11347/Cédric-Soares/stats?co=1&se=210',
     'https://www.premierleague.com/players/4605/Maya-Yoshida/stats?co=1&se=210',
     'https://www.premierleague.com/players/4612/Jack-Stephens/stats?co=1&se=210',
     'https://www.premierleague.com/players/6194/Wesley-Hoedt/stats?co=1&se=210',
     'https://www.premierleague.com/players/2886/Ryan-Bertrand/stats?co=1&se=210',
     'https://www.premierleague.com/players/19589/Jan-Bednarek/stats?co=1&se=210',
     'https://www.premierleague.com/players/4815/Matt-Targett/stats?co=1&se=210',
     'https://www.premierleague.com/players/15011/Yan-Valery/stats?co=1&se=210',
     'https://www.premierleague.com/players/15007/Alfie-Jones/stats?co=1&se=210',
     'https://www.premierleague.com/players/20109/Jannik-Vestergaard/stats?co=1&se=210',
     'https://www.premierleague.com/players/24001/Kayne-Ramsay/stats?co=1&se=210',
     'https://www.premierleague.com/players/2423/Steven-Davis/stats?co=1&se=210',
     'https://www.premierleague.com/players/4286/Oriol-Romeu/stats?co=1&se=210',
     'https://www.premierleague.com/players/4617/James-Ward-Prowse/stats?co=1&se=210',
     'https://www.premierleague.com/players/5583/Mario-Lemina/stats?co=1&se=210',
     'https://www.premierleague.com/players/3811/Nathan-Redmond/stats?co=1&se=210',
     'https://www.premierleague.com/players/5272/Pierre-Emile-Højbjerg/stats?co=1&se=210',
     'https://www.premierleague.com/players/12186/Stuart-Armstrong/stats?co=1&se=210',
     'https://www.premierleague.com/players/15967/Mohamed-Elyounoussi/stats?co=1&se=210',
     'https://www.premierleague.com/players/15017/Tyreke-Johnson/stats?co=1&se=210',
     'https://www.premierleague.com/players/3206/Shane-Long/stats?co=1&se=210',
     'https://www.premierleague.com/players/9468/Charlie-Austin/stats?co=1&se=210',
     'https://www.premierleague.com/players/12639/Manolo-Gabbiadini/stats?co=1&se=210',
     'https://www.premierleague.com/players/9650/Sam-Gallagher/stats?co=1&se=210',
     'https://www.premierleague.com/players/8245/Danny-Ings/stats?co=1&se=210',
     'https://www.premierleague.com/players/21532/Michael-Obafemi/stats?co=1&se=210',
     'https://www.premierleague.com/players/4664/Hugo-Lloris/stats?co=1&se=210',
     'https://www.premierleague.com/players/4398/Michel-Vorm/stats?co=1&se=210',
     'https://www.premierleague.com/players/4600/Paulo-Gazzaniga/stats?co=1&se=210',
     'https://www.premierleague.com/players/15111/Alfie-Whiteman/stats?co=1&se=210',
     'https://www.premierleague.com/players/3905/Kieran-Trippier/stats?co=1&se=210',
     'https://www.premierleague.com/players/3507/Danny-Rose/stats?co=1&se=210',
     'https://www.premierleague.com/players/4916/Toby-Alderweireld/stats?co=1&se=210',
     'https://www.premierleague.com/players/4666/Jan-Vertonghen/stats?co=1&se=210',
     'https://www.premierleague.com/players/20224/Davinson-Sánchez/stats?co=1&se=210',
     'https://www.premierleague.com/players/25309/Juan-Foyth/stats?co=1&se=210',
     'https://www.premierleague.com/players/11293/Serge-Aurier/stats?co=1&se=210',
     'https://www.premierleague.com/players/4408/Ben-Davies/stats?co=1&se=210',
     'https://www.premierleague.com/players/13815/Kyle-Walker-Peters/stats?co=1&se=210',
     'https://www.premierleague.com/players/15123/Timothy-Eyoma/stats?co=1&se=210',
     'https://www.premierleague.com/players/4842/Erik-Lamela/stats?co=1&se=210',
     'https://www.premierleague.com/players/4816/Victor-Wanyama/stats?co=1&se=210',
     'https://www.premierleague.com/players/4112/Eric-Dier/stats?co=1&se=210',
     'https://www.premierleague.com/players/4549/Moussa-Sissoko/stats?co=1&se=210',
     'https://www.premierleague.com/players/4123/Mousa-Dembélé/stats?co=1&se=210',
     'https://www.premierleague.com/players/9167/Dele-Alli/stats?co=1&se=210',
     'https://www.premierleague.com/players/4845/Christian-Eriksen/stats?co=1&se=210',
     'https://www.premierleague.com/players/5762/Lucas-Moura/stats?co=1&se=210',
     'https://www.premierleague.com/players/7488/Harry-Winks/stats?co=1&se=210',
     'https://www.premierleague.com/players/15938/Georges-Kévin-Nkoudou/stats?co=1&se=210',
     'https://www.premierleague.com/players/15128/Luke-Amos/stats?co=1&se=210',
     'https://www.premierleague.com/players/21413/Oliver-Skipp/stats?co=1&se=210',
     'https://www.premierleague.com/players/15136/George-Marsh/stats?co=1&se=210',
     'https://www.premierleague.com/players/4999/Son-Heung-Min/stats?co=1&se=210',
     'https://www.premierleague.com/players/3960/Harry-Kane/stats?co=1&se=210',
     'https://www.premierleague.com/players/5551/Fernando-Llorente/stats?co=1&se=210',
     'https://www.premierleague.com/players/15481/Vincent-Janssen/stats?co=1&se=210',
     'https://www.premierleague.com/players/3719/Heurelho-Gomes/stats?co=1&se=210',
     'https://www.premierleague.com/players/33158/Pontus-Dahlberg/stats?co=1&se=210',
     'https://www.premierleague.com/players/2932/Ben-Foster/stats?co=1&se=210',
     'https://www.premierleague.com/players/20929/Ben-Wilmot/stats?co=1&se=210',
     'https://www.premierleague.com/players/10480/Daryl-Janmaat/stats?co=1&se=210',
     'https://www.premierleague.com/players/5668/Miguel-Britos/stats?co=1&se=210',
     'https://www.premierleague.com/players/3508/Younes-Kaboul/stats?co=1&se=210',
     'https://www.premierleague.com/players/13822/Sebastian-Prödl/stats?co=1&se=210',
     'https://www.premierleague.com/players/3251/Adrian-Mariappa/stats?co=1&se=210',
     'https://www.premierleague.com/players/3160/Craig-Cathcart/stats?co=1&se=210',
     'https://www.premierleague.com/players/22546/Kiko-Femenía/stats?co=1&se=210',
     'https://www.premierleague.com/players/10123/Marvin-Zeegelaar/stats?co=1&se=210',
     'https://www.premierleague.com/players/5713/José-Holebas/stats?co=1&se=210',
     'https://www.premierleague.com/players/19605/Christian-Kabasele/stats?co=1&se=210',
     'https://www.premierleague.com/players/22651/Marc-Navarro/stats?co=1&se=210',
     'https://www.premierleague.com/players/49992/Adam-Masina/stats?co=1&se=210',
     'https://www.premierleague.com/players/3448/Tom-Cleverley/stats?co=1&se=210',
     'https://www.premierleague.com/players/4105/Nathaniel-Chalobah/stats?co=1&se=210',
     'https://www.premierleague.com/players/13823/Abdoulaye-Doucouré/stats?co=1&se=210',
     'https://www.premierleague.com/players/8589/Will-Hughes/stats?co=1&se=210',
     'https://www.premierleague.com/players/4843/Etienne-Capoue/stats?co=1&se=210',
     'https://www.premierleague.com/players/11213/Roberto-Pereyra/stats?co=1&se=210',
     'https://www.premierleague.com/players/25089/Ken-Sema/stats?co=1&se=210',
     'https://www.premierleague.com/players/19678/Domingos-Quina/stats?co=1&se=210',
     'https://www.premierleague.com/players/9947/Troy-Deeney/stats?co=1&se=210',
     'https://www.premierleague.com/players/9060/Andre-Gray/stats?co=1&se=210',
     'https://www.premierleague.com/players/3889/Stefano-Okaka/stats?co=1&se=210',
     'https://www.premierleague.com/players/4777/Gerard-Deulofeu/stats?co=1&se=210',
     'https://www.premierleague.com/players/19618/Isaac-Success/stats?co=1&se=210',
     'https://www.premierleague.com/players/13825/Adalberto-Peñaranda/stats?co=1&se=210',
     'https://www.premierleague.com/players/4852/Adrián/stats?co=1&se=210',
     'https://www.premierleague.com/players/3309/Lukasz-Fabianski/stats?co=1&se=210',
     'https://www.premierleague.com/players/4209/Winston-Reid/stats?co=1&se=210',
     'https://www.premierleague.com/players/8897/Aaron-Cresswell/stats?co=1&se=210',
     'https://www.premierleague.com/players/3654/Pablo-Zabaleta/stats?co=1&se=210',
     'https://www.premierleague.com/players/5514/Angelo-Ogbonna/stats?co=1&se=210',
     'https://www.premierleague.com/players/11284/Arthur-Masuaku/stats?co=1&se=210',
     'https://www.premierleague.com/players/3958/Ryan-Fredericks/stats?co=1&se=210',
     'https://www.premierleague.com/players/49884/Issa-Diop/stats?co=1&se=210',
     'https://www.premierleague.com/players/10535/Reece-Oxford/stats?co=1&se=210',
     'https://www.premierleague.com/players/32435/Fabián-Balbuena/stats?co=1&se=210',
     'https://www.premierleague.com/players/13872/Manuel-Lanzini/stats?co=1&se=210',
     'https://www.premierleague.com/players/13871/Pedro-Obiang/stats?co=1&se=210',
     'https://www.premierleague.com/players/3014/Mark-Noble/stats?co=1&se=210',
     'https://www.premierleague.com/players/9595/Michail-Antonio/stats?co=1&se=210',
     'https://www.premierleague.com/players/15202/Declan-Rice/stats?co=1&se=210',
     'https://www.premierleague.com/players/4558/Robert-Snodgrass/stats?co=1&se=210',
     'https://www.premierleague.com/players/3547/Jack-Wilshere/stats?co=1&se=210',
     'https://www.premierleague.com/players/6784/Felipe-Anderson/stats?co=1&se=210',
     'https://www.premierleague.com/players/14637/Nathan-Holland/stats?co=1&se=210',
     'https://www.premierleague.com/players/10430/Carlos-Sánchez/stats?co=1&se=210',
     'https://www.premierleague.com/players/15199/Grady-Diangana/stats?co=1&se=210',
     'https://www.premierleague.com/players/15200/Joe-Powell/stats?co=1&se=210',
     'https://www.premierleague.com/players/15211/Conor-Coventry/stats?co=1&se=210',
     'https://www.premierleague.com/players/4823/Marko-Arnautovic/stats?co=1&se=210',
     'https://www.premierleague.com/players/3175/Andy-Carroll/stats?co=1&se=210',
     'https://www.premierleague.com/players/4161/Chicharito/stats?co=1&se=210',
     'https://www.premierleague.com/players/6389/Andriy-Yarmolenko/stats?co=1&se=210',
     'https://www.premierleague.com/players/7081/Lucas-Pérez/stats?co=1&se=210',
     'https://www.premierleague.com/players/24151/Xande-Silva/stats?co=1&se=210',
     'https://www.premierleague.com/players/2896/John-Ruddy/stats?co=1&se=210',
     'https://www.premierleague.com/players/8293/Will-Norris/stats?co=1&se=210',
     'https://www.premierleague.com/players/11341/Rui-Patrício/stats?co=1&se=210',
     'https://www.premierleague.com/players/4229/Matt-Doherty/stats?co=1&se=210',
     'https://www.premierleague.com/players/4351/Ryan-Bennett/stats?co=1&se=210',
     'https://www.premierleague.com/players/4136/Conor-Coady/stats?co=1&se=210',
     'https://www.premierleague.com/players/10000/Kortney-Hause/stats?co=1&se=210',
     'https://www.premierleague.com/players/16204/Willy-Boly/stats?co=1&se=210',
     'https://www.premierleague.com/players/20171/Rúben-Vinagre/stats?co=1&se=210',
     'https://www.premierleague.com/players/20277/Jonny/stats?co=1&se=210',
     'https://www.premierleague.com/players/11180/Rúben-Neves/stats?co=1&se=210',
     'https://www.premierleague.com/players/15259/Morgan-Gibbs-White/stats?co=1&se=210',
     'https://www.premierleague.com/players/20745/Romain-Saïss/stats?co=1&se=210',
     'https://www.premierleague.com/players/19758/Diogo-Jota/stats?co=1&se=210',
     'https://www.premierleague.com/players/11259/João-Moutinho/stats?co=1&se=210',
     'https://www.premierleague.com/players/5866/Leander-Dendoncker/stats?co=1&se=210',
     'https://www.premierleague.com/players/20081/Ryan-John-Giles/stats?co=1&se=210',
     'https://www.premierleague.com/players/21702/Elliot-Watt/stats?co=1&se=210',
     'https://www.premierleague.com/players/24834/Pote/stats?co=1&se=210',
     'https://www.premierleague.com/players/5079/Ivan-Cavaleiro/stats?co=1&se=210',
     'https://www.premierleague.com/players/5064/Hélder-Costa/stats?co=1&se=210',
     'https://www.premierleague.com/players/12299/Léo-Bonatini/stats?co=1&se=210',
     'https://www.premierleague.com/players/11071/Raúl-Jiménez/stats?co=1&se=210',
     'https://www.premierleague.com/players/4979/Adama-Traoré/stats?co=1&se=210',
     'https://www.premierleague.com/players/51941/Ben-Ashley-Seal/stats?co=1&se=210',
     'https://www.premierleague.com/players/14945/Benny-Ashley-Seal/stats?co=1&se=210']



- 상세 페이지 크롤링


```python
# 상세 페이지 접속
req = requests.get('https://www.premierleague.com/players/3390/Dan-Gosling/stats?co=1&se=210')
response = TextResponse(req.url, body=req.text, encoding='utf-8')
```


```python
# 이름
name = response.xpath('//*[@class="playerDetails"]/h1/div/text()').extract()[0]
name
```




    'Dan Gosling'




```python
# 소속팀
team = response.xpath('//*[@id="mainContent"]/div[2]/nav/div/section[1]/div[2]/a/text()').extract()
team[0]
```




    'AFC Bournemouth'




```python
# 포지션
position = response.xpath('//*[@id="mainContent"]/div[2]/nav/div/section[1]/div[4]/text()').extract()
position[0]
```




    'Midfielder'




```python
appearances = response.xpath('//*[@data-stat="appearances"]/text()').extract()[0].strip()
appearances
```




    '147'




```python
team_assists = response.xpath('//*[@id="mainContent"]/div[2]/div/div/div/div/div/ul/li[2]/div/div[2]/span/span/text()').extract()[0].strip()
team_assists
```




    '2'



- crawler/items.py

```
import scrapy

class CrawlerItem(scrapy.Item):
    title = scrapy.Field()
    s_price = scrapy.Field()
    o_price = scrapy.Field()
    discount_rate = scrapy.Field()
```


```python
%%writefile premierleague/premierleague/items.py
import scrapy

class CrawlerItem(scrapy.Item):
    name = scrapy.Field()
    team = scrapy.Field()
    position = scrapy.Field()
    appearances = scrapy.Field()
    team_assists = scrapy.Field()
```

    Overwriting premierleague/premierleague/items.py
    


```python
%%writefile premierleague/premierleague/spiders/spider.py
import scrapy
import requests
from scrapy.http import TextResponse
from premierleague.items import CrawlerItem

# start_urls -> parse -> parse_page_contents 순으로 호출
class Spider(scrapy.Spider):
    # 스파이더의 이름
    name = "premierleague"
    allow_domain = ["https://www.premierleague.com"]
    # 크롤링의 시작 URL
    start_urls = ["https://www.premierleague.com/clubs"]

    # link 리스트를 가져옴
    def parse(self, response):
    
        player_links = []
        req = requests.get('https://www.premierleague.com/clubs')
        response = TextResponse(req.url, body=req.text, encoding='utf-8')
        club_links = response.xpath('//*[@id="mainContent"]/div/div/div/div[1]/div/ul/li/a/@href').extract()

        for club_link in club_links:
            club_link = 'https://www.premierleague.com' + club_link[:-8] + 'squad'
            req = requests.get(club_link)
            response = TextResponse(req.url, body=req.text, encoding='utf-8')
            player_link = response.xpath('//*[@id="mainContent"]/div[2]/div/ul/li/a/@href').extract()
            for link in player_link:
                player_links.append(link)        
                
        player_stat_links = []

        for player_link in player_links:
            player_link = 'https://www.premierleague.com' + player_link[:-8] +'stats?co=1&se=210'
            player_stat_links.append(player_link)

        for link in player_stat_links[:10]:
            yield scrapy.Request(link, callback=self.parse_page_contents)

    # 각페이지의 link로 접속하여 데이터를 가져옴
    def parse_page_contents(self, response):
        item = CrawlerItem()
        item["name"] = response.xpath('//*[@class="playerDetails"]/h1/div/text()').extract()[0]
        item["team"] = response.xpath('//*[@id="mainContent"]/div[2]/nav/div/section[1]/div[2]/a/text()').extract()[0]
        item["position"] = response.xpath('//*[@id="mainContent"]/div[2]/nav/div/section[1]/div[4]/text()').extract()[0]
        item["appearances"] = response.xpath('//*[@data-stat="appearances"]/text()').extract()[0].strip()
        item["team_assists"] = response.xpath('//*[@id="mainContent"]/div[2]/div/div/div/div/div/ul/li[2]/div/div[2]/span/span/text()').extract()[0].strip()
        
        yield item
```

    Overwriting premierleague/premierleague/spiders/spider.py
    

### Scrapy Excute
- scrapy.cfg 파일이 있는 디렉토리에서 아래의 명령을 실행하면 크롤링이 시작됨
    - `$ scrapy crawl premierleague`
- 결과를 csv 파일로 저장
    - `$ scrapy crawl premierleague -o premierleague.csv`
    - 한꺼번에 페이지를 로드해서 가져옴으로 저장되는 순서가 일정하지 않음
    - 컬럼 데이터도 순서가 뒤죽박죽


```python
df = pd.read_csv("premierleague/premierleague.csv")
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
      <th>appearances</th>
      <th>name</th>
      <th>position</th>
      <th>team</th>
      <th>team_assists</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>443</td>
      <td>Petr Cech</td>
      <td>Goalkeeper</td>
      <td>Arsenal</td>
      <td>202</td>
    </tr>
    <tr>
      <th>1</th>
      <td>239</td>
      <td>Laurent Koscielny</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>Rob Holding</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>172</td>
      <td>Nacho Monreal</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>141</td>
      <td>Héctor Bellerín</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>17</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3</td>
      <td>Konstantinos Mavropanos</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>Dejan Iliev</td>
      <td>Goalkeeper</td>
      <td>Arsenal</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>69</td>
      <td>Shkodran Mustafi</td>
      <td>Defender</td>
      <td>Arsenal</td>
      <td>3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>12</td>
      <td>Bernd Leno</td>
      <td>Goalkeeper</td>
      <td>Arsenal</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>6</td>
      <td>Emiliano Martínez</td>
      <td>Goalkeeper</td>
      <td>Arsenal</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
max(df['appearances'])
```


```python
df['team'].value_counts()
```


```python
df[(df['appearances'] > 100) & (df['team'] == 'Manchester United')]
```

### Pipelines
- 결과를 깔끔하게 정리해서 저장
- spider의 items 데이터가 pipelines에 정의된 대로 저장

- pipelines.py 

```
%%writefile premierleague/premierleague/pipelines.py
import csv

class CrawlerPipeline(object):

    def __init__(self):
        self.csvwriter = csv.writer(open("premierleague.csv", "w"))
        self.csvwriter.writerow(["name","team","position","appearances",'team_assists'])

    def process_item(self, item, spider):
        row = []
        row.append(item["name"])
        row.append(item["team"])
        row.append(item["position"])
        row.append(item["appearances"])
        row.append(item["team_assists"])
        self.csvwriter.writerow(row)
        return item
```


```python
%%writefile premierleague/premierleague/pipelines.py
import csv

class CrawlerPipeline(object):

    def __init__(self):
        self.csvwriter = csv.writer(open("premierleague.csv", "w"))
        self.csvwriter.writerow(["name","team","position","appearances",'team_assists'])

    def process_item(self, item, spider):
        row = []
        row.append(item["name"])
        row.append(item["team"])
        row.append(item["position"])
        row.append(item["appearances"])
        row.append(item["team_assists"])
        self.csvwriter.writerow(row)
        return item
```


```python
!cat crawler/crawler/pipelines.py
```
