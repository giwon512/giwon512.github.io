---
layout: single
title: javascript 환경에서 python 실행하기
categories: [Python]
tag: [Python, 'Web Crawling']
toc: true
---
­

react와 springboot를 활용하여 restful api 방식으로 프로젝트를 구현하려는 상황에서, 파이썬을 통해서 크롤링을 하게 된 상황이다. node 서버에서 파이썬 파일을 실행하는 방법을 알아보았다.

메인 아이디어는 자식 프로세스를 생성하여 따로 파이썬 파일을 실행하게 하는 것이다.

## 📖 자식 프로세스 생성

Node.js의 `child_process` 모듈을 통해서 자식 프로세스를 생성할 수 있다. 자식 프로세스를 생성하는 방법에는 4가지가 있다.

1. spawn()
    - 새로운 프로세스를 생성하고 명령어를 실행한다.
        - 어떤 외부 프로그램이든 실행할 수 있다(Node.js 프로세스가 아니어도 상관없음).
    - 세 번째 인수로 옵션 객체를 받아 프로세스의 환경 변수, 작업 디렉터리 등을 설정할 수 있다.
    - 프로세스의 stdin, stdout, stderr 스트림을 별도로 제어할 수 있다.
    - 비동기적으로 동작하며, 프로세스의 출력 데이터는 이벤트 리스너를 통해 받아야 한다.
2. fork()
    - 새로운 Node.js 프로세스를 생성한다.
        - 다른 외부 프로그램은 실행할 수 없다.
    - 부모 프로세스와 자식 프로세스 간의 통신을 위해 IPC 채널을 자동으로 설정한다.
    - 프로세스 간의 메시지 전달 및 파일 기술자 공유가 가능하다.
    - 단일 스레드 Node.js 프로세스에서 멀티 스레드 동작을 모방할 수 있다.
3. exec()
    - 셸 명령어를 실행하고, 완료될 때까지 대기한다.
    - 명령어의 stdout, stderr를 버퍼에 저장하고, 프로세스가 종료되면 결과를 콜백으로 전달한다.
    - 동기적으로 동작하므로, 명령어가 완료될 때까지 Node.js 이벤트 루프가 차단된다.
    - 간단한 명령어를 실행할 때 유용하지만, 대용량 데이터를 처리하거나 스트리밍이 필요한 경우에는 적합하지 않다.
4. execFile()
    - `exec`와 유사하게 동작하지만, 첫 번째 인수로 실행 파일 경로를 받는다.
    - 셸 명령어를 실행하지 않고, 직접 실행 파일을 실행한다.
    - 셸의 추가 기능을 사용할 필요가 없는 경우에 유용하다.
    - 동기적으로 동작하며, 명령어 완료 시까지 Node.js 이벤트 루프가 차단된다.

**fork vs exec**

fork는 새로운 프로세스를 생성하는 방식이라면, exec은 기존의 프로세스에 새로운 프로세스를 덮어씌워 실행하는 방식이다. 따라서 fork는 비동기식, exec은 동기식의 형태를 가지게 되는 것이다.

→ 따라서, 대용량의 웹 스크래핑한 데이터를 가진 파이썬 파일을 실행하기 위해서는 spawn 방식을 선택해야 하는 것을 알 수 있다.

### 활용

```java
const result = spawn('python', ['파이썬파일명.py', 매개변수1, 매개변수2, ... ]);
```

이 코드는 "python 파이썬파일.py 매개변수1, 매개변수2" 명령어로 파이썬 코드를 실행는 것과 동일하게 작동한다.

```java
const spawn = require('child_process').spawn;

const result = spawn('python', ['database.py']);

result.stdout.on('data', function(data) {
    console.log(data.toString());
});

result.stderr.on('data', function(data) {
    console.log('err : ' + data.toString());
});
```

이런 식으로 print.py 파일을 실행하였다.

## 📖 노드 환경에서 python db CRUD 실행하기

### database.py

```python
import pymysql

def dbconnect():
    conn = pymysql.connect(host='127.0.0.1', user='root', password='1111', 
                       db='fashiondb', charset='utf8')
    return conn

def search_data(conn):
    cur = conn.cursor()
    sql = 'SELECT * FROM news'
    cur.execute(sql)
    results = cur.fetchall()
    for i in range(len(results)):
        print(results[i])

def insert_data(conn):
    cur = conn.cursor()
    sql = "insert into news(title, content, author, published_date) \
        values('cccc', 'ccccccccccc', 'kim', SYSDATE())"
    cur.execute(sql)
    conn.commit()
    
def delete_data(conn):
    cur = conn.cursor()
    sql = "delete from news where news_id={}".format(1)
    cur.execute(sql)
    conn.commit()

def main():
    conn = dbconnect()
    print("connected")
    # delete_data(conn)
    insert_data(conn)
    search_data(conn)
    conn.close()
    print("closed")
    
if __name__=="__main__":
    main()
```

news 테이블에 파이썬 코드를 통해서 crud를 진행하는 간단한 코드이다. 해당 코드는 insert후에 select로 테이블의 내용을 출력하는 로직으로 구성되어있다. 이 코드를 node 환경에서 실행하면 다음과 같이 잘 insert되는 것을 볼 수 있다.

![nodePython.png]({{"../../../../assets/images/Python/NodePython/nodePython.png" | relative_url}})

참고

---

[https://curryyou.tistory.com/225](https://curryyou.tistory.com/225)

[https://www.freecodecamp.org/korean/news/node-js-child-processes-everything-you-need-to-know-e69498fe970a/](https://www.freecodecamp.org/korean/news/node-js-child-processes-everything-you-need-to-know-e69498fe970a/)