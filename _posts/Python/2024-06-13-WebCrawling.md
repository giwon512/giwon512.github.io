---
layout: single
title: 웹 크롤링을 위한 기본 개념
categories: [Python]
tag: [Python, 'Web Crawling']
toc: true
---


# 📘 웹 크롤링 vs 웹 스크래핑

**웹 크롤링**

자동화된 프로그램이나 봇이 웹 사이트를 탐색하고 링크를 따라가면서 필요한 정보한 정보를 포함하는 페이지를 찾는데 초점이 맞춰져 있다. 마구잡이로 데이터를 가져옴.

**웹 스크래핑**

웹 페이지에서 특정 데이터를 추출하고 수집하는 과정을 의미한다. 즉, HTML 구조를 파싱하여 원하는 데이터를 선택적으로 가져오는 과정이다. 크롤링과 다르게 필요한 부분만을 가져온다는 뜻이다.

→ 실제로는 두 단어가 혼용되어 사용되기도 하며, 크롤러가 웹 사이트를 탐색하고, 스크래퍼가 필요한 정보를 추출하는 식으로 두 가지가 함께 사용되는 경우가 많다.

# 📖 웹 크롤링 과정

1. 웹 사이트를 접속하는 행위 발생(Request 보냄)
2. Response로 받아온 데이터를 parsing 하여 가져옴

→ 실시간으로 변동하는 데이터들을 크롤러를 여러 번 호출함으로써 손쉽게 업데이트가 가능해졌다.

# 📔 웹 스크래핑을 위한 기초 개념

## 1. XPath

> 원하는 태그를 찾아가기 위한 경로 설정, 비슷한 태그나 요소가 존재한다면 어떠한 태그 혹은 요소를 지칭하는지 명확하게 하기 위해 xpath를 사용한다.
> 

`html/body/div/span/a…` 와 같은 방식으로 태그를 찾아간다. 고유한 속성 값이 있다면 다음과 같이 줄일 수도 있다. 

`//*[@속성=”값”]` 슬래시가 두 개인 경우 현재 위치에서 모든 하위 계층의 노드들을 찾겠다는 의미이다.

## 2. Requests

```bash
pip install requests
```

requests 모듈을 사용해서 페이지에 있는 값들을 가져올 수 있다.

```python
import requests

res = requests.get("http://google.com")
print("응답코드 :", res.status_code) #정상이면 200

res.raise_for_status() #오류 발생 시 뒷 코드 실행 안 됨
print("웹 스크래핑을 진행합니다.")

print(len(res.text))

with open("mygoogle.html", "w", encoding="utf8") as f:
    f.write(res.text)
```

가져온 값을 mygoogle.html 파일로 만들어 열어보면, css가 적용되지 않은 구글 홈페이지가 그대로 가져와 지는 것을 볼 수 있다.

## 3. 정규식(Regular Expression)

### 기본 정규식 문법

. (ca.e) : 하나의 문자를 의미 > care, cafe, ...
^ (^de) : 문자열의 시작 > desk, deny, ...
$ (se$) : 문자열의 끝 > case, base, ...

```python
import re

p = re.compile("ca.e")

def print_match(m):
    if m:
        print(m.group()) #일치하는 문자열 반환
        print(m.string) #입력받은 문자열 반환
        print(m.start()) #일치하는 문자열의 시작 인덱스
        print(m.end()) #일치하는 문자열의 끝 인덱스
        print(m.span()) #일치하는 문자열의 시작과 끝 인덱스
    else:
        print("매칭되지 않음")

# m = p.match("case")
# n = p.match("caffe")
# mm = p.match("careless") #match : 주어진 문자열의 처음부터 일치하는지 확인
# print_match(m)
# print_match(n)
# print_match(mm) #care

# m = p.search("good care") #care
# print_match(m) #search : 주어진 문자열 중에 일치하는게 있는지 확인

lst = p.findall("careless good care cafe") #일치하는 모든 문자열 리스트로 반환
print(lst)
```

1. p = re.complie("원하는 형태")로 컴파일을 받아옴
2. m = p.match/p.search/p.findall로 값 받아와서 사용

## 4. User Agent

requests를 통해 무분별하게 프로그램이 크롤링을 해가게 되면 서버에 부하가 발생하거나 정보가 빼앗기는 문제 등이 발생할 수 있는데, 이를 방지하고자 사람이 아닌 프로그램이라 생각하면 403 등의 오류를 띄워버릴 수 있다. 이 오류를 피하기 위해 User Agent를 활용한다.

## 5. BeautifulSoup

[https://www.crummy.com/software/BeautifulSoup/bs4/doc.ko/](https://www.crummy.com/software/BeautifulSoup/bs4/doc.ko/)

자세한 내용은 beautifulSoup 공식 문서에 정리되어 있다.

```python
import requests
from bs4 import BeautifulSoup

url = "https://news.naver.com/"
res = requests.get(url)
res.raise_for_status()

#우리가 가져온 텍스트를 lxml파서를 통해서 beautifulsoup 객체를 만듦
soup = BeautifulSoup(res.text, "lxml")
print(soup.title)
print(soup.title.get_text())
```

bs4 모듈의 BeautifulSoup 객체를 가져와 import해주고, 해당 객체의 태그 값을 다음과 같이 검색하면, 가장 먼저 발견되는 태그를 반환해준다.

```python
elem = soup.find("div", attrs={"class" : "Ngnb_left"})
print(elem.next_sibling.next_sibling)
```

이렇게 find 함수를 이용해 속성 값을 검색하여 원하는 요소를 검색하는 것이 가능하다. 형제 노드 사이에 개행 정보와 같은 것들이 들어있을 수 있기 때문에, next_sibling 한 번으로 검색이 제대로 되지 않는 경우 두 번 붙여 써주면 잘 검색이 될 것이다.

```python
result = soup.select_one("#ct > div > section.main_content > div.main_brick > div > div:nth-child(1) > div.main_brick_item._channel_news_preview_wrapper > div:nth-child(1) > div > div:nth-child(2) > a")
print(result)
```

개발자 도구에서 원하는 태그의 selector를 복사할 수 있는데, 위와 같이 검색하는 것도 가능하다.

**활용 가능한 함수들**

```python
soup.find()
soup.select_one()
soup.find_all()
soup.get_text()
...
```

**활용 가능한 속성들**

```python
elem = soup.find(...)
elem.next_sibling
elem.previous_sibling
elem.find_next_sibling(condition)
elem.find_next_siblings(condition)
elem.parent
...
```

## 6. Selenium

beautifulSoup과는 다르게 웹페이지에서 값을 넣고, 클릭을 하는 등의 상호작용을 한 이후의 페이지를 가져와 스크래핑을 하는게 가능해진다.

### Chrome Web Driver 설치하기

[https://github.com/GoogleChromeLabs/chrome-for-testing/blob/main/data/latest-versions-per-milestone-with-downloads.json](https://github.com/GoogleChromeLabs/chrome-for-testing/blob/main/data/latest-versions-per-milestone-with-downloads.json)

구글 검색창에 `chrome://version` 이라고 검색하거나, 설정의 도움말 탭을 통해서 현재 내 크롬 브라우의 버전을 알 수가 있는데, 위 링크에서 버전에 맞는 크롬 드라이버를 설치하면 된다.

**예제 코드**

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
import time
from selenium.webdriver.chrome.options import Options

# 브라우저 꺼짐 방지 옵션
chrome_options = Options()
chrome_options.add_experimental_option("detach", True)
# 브라우저 실행
browser = webdriver.Chrome(options=chrome_options)
# browser.maximize_window()
# 네이버 항공권 예약 사이트로 이동
browser.get("https://flight.naver.com/")
try:
    # 가는 날 항목 클릭
    elem = WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.CLASS_NAME, "tabContent_option___mYJO.select_Date__Potbp")))
    elem.click()
    # 이번 달 27일, 다음 달 28일 선택
    elem = WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.XPATH, "//*[@id='__next']/div/main/div[10]/div[2]/div[1]/div[2]/div/div[2]/table/tbody/tr[5]/td[5]/button/b")))
    elem.click()
    elem = WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.XPATH, "//*[@id='__next']/div/main/div[10]/div[2]/div[1]/div[2]/div/div[3]/table/tbody/tr[5]/td[1]/button")))
    elem.click()
    # 도착 항목 클릭
    elem = WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.CLASS_NAME, "tabContent_route__EXyDz.select_City__mKbzk.end")))
    elem.click()
    # 일본 항목 클릭
    elem = WebDriverWait(browser, 10).until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, "div.autocomplete_content__N2sKC > section > section > button")))
    # destination = [e.text for e in elem]
    # print(destination)
    elem[1].click()
    # 일본 도시 항목 클릭
    elem = WebDriverWait(browser, 10).until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, "div.autocomplete_list__4GCEJ > button")))
    elem[0].click()
    # 항공권 검색 항목 클릭
    elem = WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.CLASS_NAME, "searchBox_txt__jQZGF")))
    elem.click()
    elem = WebDriverWait(browser, 10).until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, "div.concurrent_ConcurrentItemContainer__NDJda")))
    print(elem[0].text)
    elem[0].click()
finally:
    # browser.quit()
    print("in finally")
```

네이버 항공권 예약 사이트에서 자동으로 가는 날과 오는 날을 입력하고, 도착지를 자동으로 클릭하여 프로그램을 실행시키기만 해도 인천에서 오사카로 가는 최저가 항공편 정보가 터미널에 출력이 되도록 예제를 짜보았다.

주의해야할 점이 있다면, 로딩 등의 문제로 요소들이 렌더링 되기 전에 검색을 하는 코드가 실행이 된다면 `noSuchElementException`이 발생하기 때문에, `WebDriverWait()` 함수를 통해 페이지가 다 로딩된 것을 보장하는 코드를 짜주는 것이 좋은 것 같다.

참고

---

[https://www.youtube.com/watch?v=yQ20jZwDjTE](https://www.youtube.com/watch?v=yQ20jZwDjTE)

[https://www.youtube.com/watch?v=aYwg1H5BK04](https://www.youtube.com/watch?v=aYwg1H5BK04)