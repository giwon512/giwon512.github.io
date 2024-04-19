---
layout: single
title: JdbcTemplate으로 스프링 DB 연동하기
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링]
toc: true
---


# 📘 JdbcTemplate

org.springframework.jdbc.core.JdbcTemplate 클래스를 통해서 Connection, Statement, ResultSet을 한 번에 묶어 사용할 수 있다.

- update() 메서드
    
    insert, update, delete를 처리한다.
    
- query() 메서드
    
    select를 처리한다.
    

두 메서드 모두 오버로딩된 메서드가 정말 많기 때문에 적절한 걸로 잘 골라서 사용해주면 된다.

JdbcTemplate을 사용할 때는 코드가 없기 때문에 어노테이션으로 빈 객체를 생성할 수 없어서, xml 파일을 활용해주어야 한다.

### config.xml

```xml
<bean class="org.springframework.jdbc.datasource.DriverManagerDataSource" id="dataSource">
	<property name="driverClassName" value="oracle.jdbc.driver.OracleDriver" />
	<property name="url" value="jdbc:oracle:thin:@localhost:1521:xe" />
	<property name="username" value="user1" />
	<property name="password" value="1111" />
</bean>
  
<bean class="org.springframework.jdbc.core.JdbcTemplate">
	<property name="dataSource" ref="dataSource" />
</bean>
```

jdbcTemplate 객체에 값들을 모두 정해줄 수 있고, dao 클래스에서 @Autowired 어노테이션을 통해 객체를 생성해 불러올 것이므로 id 값을 따로 지정해주지 않아도 된다.

## 📖 **[update](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html#update-org.springframework.jdbc.core.PreparedStatementCreator-)**(**[PreparedStatementCreator](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/PreparedStatementCreator.html)** psc)

훨씬 더 간단한 방법도 있지만, 공부할 겸 이 방법도 알아두려고 한다. PreparedStatementCreator 클래스 이름을 보면 알 수 있듯이, PreparedStatement 객체를 생성해주는 클래스이다.

### PreparedStatementCreatorImpl.class

```java
class PreparedStatementCreatorImpl implements PreparedStatementCreator {
	@Override
	public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
		PreparedStatement stmt;
		return null;
	}
}
```

update 메서드에 인자를 넘겨주기 위해 위와 같이 오버라이딩해주는 클래스를 만들어도 되지만, 한 번만 호출될 함수라면 위의 클래스를 작성해줄 필요 없이 다음과 같이 익명 함수로 처리해줄 수 있다.

```java
public void insert(Member member) {
		jdbcTemplate.update(new PreparedStatementCreator() {
			
		@Override
		public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
			String sql = "insert into member values(seq_id.nextVal, ?, ?, ?, ?)";
			PreparedStatement stmt = con.prepareStatement(sql);
			stmt.setString(1, member.getEmail());
			stmt.setString(2, member.getName());
			stmt.setString(3, member.getPassword());
			//stmt.setDate(4, member.getRegisterDate()); java.sql.Date인 경우
			stmt.setTimestamp(4, new Timestamp(member.getRegisterDate().getTime()));
			
			return stmt;
		}
	});
}
```

이걸 더 깔끔하게 람다 함수로 처리해주면 다음과 같이 코드가 줄여진다.

```java
public void insert(Member member) {
		jdbcTemplate.update((Connection con) -> {
		String sql = "insert into member values(seq_id.nextVal, ?, ?, ?, ?)";
		PreparedStatement stmt = con.prepareStatement(sql);
		stmt.setString(1, member.getEmail());
		stmt.setString(2, member.getName());
		stmt.setString(3, member.getPassword());
		//stmt.setDate(4, member.getRegisterDate()); java.sql.Date인 경우
		stmt.setTimestamp(4, new Timestamp(member.getRegisterDate().getTime()));
		
		return stmt;
	});
}
```

## 📖 **[update](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html#update-java.lang.String-java.lang.Object...-)**(**[String](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html?is-external=true)** sql, **[Object](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html?is-external=true)**... args)

이렇게 오버로딩된 메서드를 사용하면 훨씬 간단하게 쿼리를 처리할 수 있다. 여기서 … 은 가변 인자라는 의미로, 원하는 만큼 객체를 넣어도 된다는 의미이다.

패스워드를 수정하는 메서드는 간단하게 구현해보았다.

```java
//패스워드 수정
public void update(Member member) {
	String sql = "update member set password=? where email=?";
	jdbcTemplate.update(sql, member.getPassword(), member.getEmail());
}
```

## 📖 **[query](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html#query-java.lang.String-org.springframework.jdbc.core.RowMapper-)**(**[String](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html?is-external=true)** sql, **[RowMapper](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/RowMapper.html)**<T> rowMapper)

```java
//전체 회원정보 조회
public Collection<Member> selectAll() {
	String sql = "select * from member";
	List<Member> result = jdbcTemplate.query(sql, (ResultSet rs, int rowNum) -> {
			Member memDto = new Member(rs.getString("name"), rs.getString("email"),
					rs.getString("password"), rs.getTimestamp("registerDate"));
			memDto.setId(rs.getLong("id"));
			return memDto;
		}
	);
	return result;
}
```

sql을 반복을 돌면서 실행해 DB에서 가져온 결과 값을 ResultSet에 자동으로 넘겨주고, 우리는 그 값을 잘 다루어서 처리해주면 된다.

RowMapper 클래스의 mapRow함수를 오버라이딩하여 람다 함수의 형태로 인자를 넘겨준다. RowMapper의 경우 자동으로 반복을 돌아서 결과 값을 List 컨테이너로 리턴해준다. 

## 📖**[query](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html#query-java.lang.String-org.springframework.jdbc.core.PreparedStatementSetter-org.springframework.jdbc.core.ResultSetExtractor-)**(**[String](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html?is-external=true)** sql,  **[PreparedStatementSetter](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/PreparedStatementSetter.html)** pss,  **[ResultSetExtractor](https://docs.spring.io/spring-framework/docs/5.3.30/javadoc-api/org/springframework/jdbc/core/ResultSetExtractor.html)**<T> rse)

```java
//DB를 통해 같은 이메일 주소가 있는지 확인한다.
public Member selectByEmail(String email) {
	String sql = "select * from member where email=?";
	Member mem = jdbcTemplate.query(sql, new PreparedStatementSetter() {
		@Override
		public void setValues(PreparedStatement ps) throws SQLException {
			ps.setString(1, email);
		}
	}, 
	new ResultSetExtractor<Member>(){
		@Override
		public Member extractData(ResultSet rs) throws SQLException, DataAccessException {
			Member memDto = null;
			if(rs.next()) {
				memDto = new Member(rs.getString("name"), rs.getString("email"), 
						rs.getString("password"), rs.getTimestamp("registerDate"));
				memDto.setId(rs.getLong("id"));
			}
			return memDto;
		}	
	});
	return mem;
}
```

구조 이해를 돕기 위해 람다 함수는 굳이 사용하지 않았다. sql 문에 인자 값이 들어가서 preparedStatement가 필요한 경우엔 이렇게 오버로딩된 메서드를 사용한다.

RowMapper 클래스는 결과가 여러 개일 때 사용되고, ResultSetExtractor는 결과가 하나일 때 사용된다는 차이점이 있다. ResultSetExtractor 클래스를 사용할 때는 rs.next()를 직접 호출해주어야 한다.