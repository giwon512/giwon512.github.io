---
layout: single
title: 패션 포털 웹서비스 Survey API 호출 과정
categories: Project
tag: [Frontend, Backend, Java, 스프링부트, 서버, 데이터베이스]
toc: true
---



# 메인 서비스 로직을 위한 API

우리 프로젝트의 핵심 중 하나는, 사용자에게 받은 설문 조사 내용을 바탕으로 관심사 키워드가 포함된 컨텐츠를 먼저 노출되게 하는 것이다.

관심사를 분류하기 위해 스타일과 브랜드에 대한 분류 기준을 테이블에 다음과 같이 정해두었다. 

![table_style.png]({{"../../../../assets/images/Project/surveyAPI/table_style.png" | relative_url}})

![table_brand.png]({{"../../../../assets/images/Project/surveyAPI/table_brand.png" | relative_url}})

[ MYPAGE ] → [ 설문 관리 ]로 들어가면, 설문 조사를 하거나, 이전의 설문 조사 내용을 확인하고 수정할 수 있다.

# 📖 설문 조사한 내용 가져오기

### 📚 GET /surveys/user

현재 사용자의 설문 조사 결과를 가져오는 API이다.

```java
List<UserSurveyResponse> responses = userSurveys.stream()
    .map(survey -> new UserSurveyResponse(survey,
            userSurveyService.findStylesBySurveyId(survey.getSurveyId()),
            userSurveyService.findBrandsBySurveyId(survey.getSurveyId())))
    .collect(Collectors.toList());
```

자바의 Collections 객체에서는 map() 메서드를 지원해주지 않기 때문에 stream API를 활용하여 리스트로 반환해주었다. UserServey 객체에는 유저의 설문 조사 결과가 들어있는데, 결과에 있는 관심 스타일의 아이디 값들과 브랜드 아이디 값들을  `findStylesBySurveyId`와 `findBrandsBySurveyId`를 통해 실제 스타일명, 브랜드명으로 바꾸어 보내주게 된다.

```java
 @Select("SELECT s.style_id, s.name FROM Style s"
	 + "JOIN UserSurveyStyle uss ON s.style_id = uss.style_id WHERE uss.survey_id = #{surveyId}")
    List<Style> findStylesBySurveyId(Long surveyId);
```

위에서 미리 정해놓은 스타일 테이블과 설문 조사의 스타일 테이블을 조인하여 실제 스타일 명을 가져오도록 구현했다.

# 📖 설문 조사하기

### 📚 POST /surveys

설문 조사한 내용을 DB에 저장한다.

```java
@Insert({
        "<script>",
        "INSERT INTO UserSurveyStyle (survey_id, style_id) VALUES ",
        "<foreach collection='styleIds' item='styleId' separator=','>",
        "(#{surveyId}, #{styleId})",
        "</foreach>",
        "</script>"
})
void insertUserSurveyStyles(@Param("surveyId") Long surveyId, @Param("styleIds") List<Long> styleIds);
```

단순히 insert를 하는 과정으로, MyBatis에서 지원하는 foreach 태그 문법으로 다중 값을 가지는 컬럼들을 한 번에 처리했다.

# 📖 설문 삭제하기

### 📚 DELETE /surveys/{surveyId}

설문 내용을 삭제하는 API이다.

해당 설문의 다중 값인 스타일과 브랜드 값을 저장하는 `UserSurveyStyle` 테이블과 `UserSurveyBrand` 테이블에서 삭제하고자 하는 surveyId의 값들을 다 삭제해주는 과정을 거친다.

# 📖 설문 수정하기

### 📚 PUT /surveys/{surveyId}

비슷한 내용이라 생략한다.

# 📖 관리자 페이지에서 통계 확인하기

### 📚 GET /surveys

`GET /surveys/user` API와 똑같이 동작한다. 

```jsx
import { Bar } from "react-chartjs-2";
import "chart.js/auto";
```

가져온 데이터를 바탕으로 성별, 나이대, 선호 스타일/브랜드 수를 세어 차트로 나타낸다. 모듈을 활용해주었다.

```jsx
const genderCounts = surveys.reduce(
    (acc, survey) => {
        const gender = survey.userSurvey.gender;
        acc[gender] = (acc[gender] || 0) + 1;
        return acc;
    },
    {}
);
```

reduce 함수를 사용하여 성별의 수를 구해오는 코드이다. 예를 들어, genderCounts는 `{ ”남” : 2, “여” : 3 }` 과 같은 값을 가진다.

```
📖 **Reduce 함수의 파라미터 값**

1. callback function
    반환값을 축적할 accumulator, 배열의 현재요소, 
    현재요소의 인덱스(선택), 호출한 배열(선택) 순으로 
    4가지의 인자를 가지는 콜백 함수를 첫 번째 인자로 갖는다.
    
2. accumulator 초기값(선택)
    초기값을 제공하지 않을 경우 배열의 첫 번째 값이 들어간다.
```

```jsx
<div className="chart">
    <h3>Gender Distribution</h3>
    <Bar data={genderData} />
</div>
```

다음과 같이 객체 값을 넘겨주면, 그에 맞는 차트를 그려준다.

**결과 화면**

![surveyChart.png]({{"../../../../assets/images/Project/surveyAPI/surveyChart.png" | relative_url}})

# 📖 관심도 높은 게시글 먼저 출력하기

### 📚 GET /top3/prefer

일단, 메인 페이지에서는 카테고리 뉴스 3개씩 출력을 조정해주면 된다.

로그아웃 시 : top3 API

로그인 상태 : top3/prefer API

1. 카테고리 별로 해당 유저의 설문 결과에 있는 스타일, 브랜드 명이 포함된 뉴스를 가져온다.
    
    → 검색 기능과 동일하게 구현할 수도 있지만 값이 실시간으로 바뀌지 않으므로 백에서 처리하자.
    
2. 가져온 개수가 3개 미만이라면, 모자란 수만큼 카테고리 뉴스를 최신 순으로 채운다.

```java
public List<ProcessedNews> getTop3NewsByCategoriesAndPreference(String category, User currentUser) {
	Long userId = currentUser.getUserId();
	//해당 카테고리의 뉴스 1000개 가져옴
	List<ProcessedNews> newsList = newsMapper.findProcessedNewsByCategory(category, 0, 1000);
	
	//해당 유저의 설문조사 결과 가져오기
	List<UserSurvey> userSurveys = userSurveyService.getUserSurveysByUserId(userId);
	List<Style> styleList = userSurveyService.findStylesBySurveyId(userSurveys.get(0).getSurveyId());
	List<Brand> brandList = userSurveyService.findBrandsBySurveyId(userSurveys.get(0).getSurveyId());
	
	//관심있는 스타일, 브랜드의 키워드를 가지고 있는 뉴스 필터링
	List<ProcessedNews> filteredList = newsList.stream().filter(news -> {
		for(Style style : styleList) {
			if(news.getTitle().contains(style.getName()) || news.getContent().contains(style.getName())) {
				return true;
			}
		}
		for(Brand brand : brandList) {
			if(news.getTitle().contains(brand.getName()) || news.getContent().contains(brand.getName())) {
				return true;
			}
		}
		return false;
	}).collect(Collectors.toList());
	
	//리턴할 리스트의 크기 3개로 맞춰서 리턴해주기
	if(filteredList.size() >= 3) {
		return filteredList.subList(0, 3);
	}
	else {
		int add = 3 - filteredList.size();
		for(int i = 0; i < add; i++) {
			filteredList.add(newsList.get(i));
		}
		return filteredList;
	}
}
```

이렇게 간단하게 메인 서비스 로직을 구현했다.