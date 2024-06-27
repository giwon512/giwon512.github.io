---
layout: single
title: 크롤링한 이미지 파일 저장하기
categories: [Python]
tag: [Python, 'Web Crawling']
toc: true
---


이미지는 크기가 큰 관계로 파일 자체를 저장하는 게 아니라, 크롤링으로 가져온 이미지의 url만 저장해서 띄워주려고 했었다. 하지만 크롤링한 사이트의 원래 게시물이 사라져버린다면 해당 이미지에 접근할 방법도 사라지기 때문에 다른 방법을 방법을 고민해보게 되었다.

## 사전 설정 사항

이미지를 저장하기 위한 바이너리 데이터는 크기가 크기때문에 MEDIUMBLOB 타입으로 지정해서, 테이블을 하나 생성해주었다.

```sql
CREATE TABLE test_image(
	img_id BIGINT AUTO_INCREMENT PRIMARY KEY,
	img_content MEDIUMBLOB,
	news_id BIGINT
);
```

## 방법 1. 백엔드 서버의 파일로 일일이 저장

```python
def save_image():
    #이미지 다운로드
		img_res = requests.get(img_src)
		img_data = img_res.content

    with open("savedImage.jpg", "wb") as f:
        f.write(img_data)
```

이미지 주소로 가서 내용을 가져오면, 바이너리 데이터로 저장이 되어있는데, 이를 그대로 파일 출력을 해주면 사진 파일로 저장이 된다. 하지만 사진 파일이 한두개가 아니기 때문에 이 방식은 이내 포기했다.

## 방법 2. 이미지를 base64로 인코딩해서 db에 저장하기

가져온 이미지의 바이너리 데이터를 인코딩하여 db에 문자열로 저장하는 방식을 택했다.

📄 **Python/TestFiles/FN_image_test.py**

```python
#이미지 다운로드
img_res = requests.get(img_src)
img_data = img_res.content

#base64 인코딩
encoded_image = base64.b64encode(img_data).decode('utf-8')

# db 연결
conn = dbconnect()
insert_image(conn, encoded_image)
```

📄 **Python/database.py**

```java
def insert_image(conn, imageData):
	cur = conn.cursor()
	sql = "insert into test_image(img_content) values(%s)"
	cur.execute(sql, (imageData))
	conn.commit()
```

📄 **Python/test.py**

```python
import TestFiles.FN_image_test as test

def main():
    test.scrape_news()
    # test.save_image()
    
if __name__ == "__main__":
    main()
```

상위 폴더의 파일을 모듈로 가져오기가 번거로워서, database.py와 같은 위치에 [test.py](http://test.py) 파일을 만들어 패션엔의 이미지 파일을 저장하는 FN_image_test.py를 실행해보았다.

### 📘 이미지를 base64로 인코딩하는 이유

 일반적으로 이미지를 http 요청을 통해 가져오는 경우에는, 클라이언트가 서버에게 이미지 파일의 url을 제공하고, 서버가 다시 url로부터 이미지를 가져오는 방식을 취한다고 한다. 

하지만 base64로 인코딩된 이미지는 문자열 그 자체로 태그에 넣어서 사용하면 되는 장점이 있다. 정리하자면 다음과 같다.

> 별도의 http 요청 없이 이미지 직접 전송 가능, 이를 통한 외부 종속성 제거
> 

다만 단점으로, 원래 바이너리 데이터보다 33% 정도 더 많은 공간을 차지하고, 인코딩된 이미지를 디코딩해줘야 하기 때문에 대용량 이미지 처리에 적합하지 않을 수 있다고 gpt 선생님이 주의를 주셨다..

![base64_imageDB.png]({{"../../../../assets/images/Python/ImageCrawling/base64_imageDB.png" | relative_url}})

이런 식으로 db에 값이 저장이 된다.

```
const TestImage = () => {
    const [imgData, setImgData] = useState('');

    useEffect(() => {
        getImage(1);
    }, []);

    const getImage = async (id) => {
        const response = await api.get(`/news/getImage/${id}`);
        const {imgId, imgContent} = response.data;
        setImgData(imgContent);
    }

    return (
        <>
            <h2>in testImage</h2>
            <img src={"data:image/jpg;base64,"+imgData} />
        </>
    );
}
```

이런 식으로 `"data:image/jpg;base64,"` 구문을 base64로 인코딩된 문자열 앞에 붙여 활용 가능하다. `response.data`에는 db에 저장한 대로 `{imgId, imgContent}` 형식으로 값이 저장이 되어 있다.

백엔드에서는 늘 하던 대로 Controller → Service → Mapper 순으로 이동하면서 db에서 값을 갖고 올 수 있도록 했다.

### Controller

```java
private final NewsService2 newsService;
    
@GetMapping("/news/getImage/{id}")
public ResponseEntity<ImageTest> getImageTest(@PathVariable(name="id") Long id){
	ImageTest img = newsService.getImageTest(id);
	return ResponseEntity.ok(img);
}
```

### Service

```java
public ImageTest getImageTest(Long id) {
	ImageTest img = newsMapper.getImageTest(id);
	return img;
}
```

### Mapper

```java
@Select("SELECT * from test_image where img_id = #{id}")
ImageTest getImageTest(@Param("id") Long id);

```

## 방법 3. 이미지를 저장하는 별도의 서버 만들기

이미지를 따로 저장하고, 그 이미지가 저장된 위치의 url을 관리하는 방식이다. 당장은 프리티어를 사용해 연습하는 나로서는 별도의 서버까지 만들 필요성은 느끼지 못했다.