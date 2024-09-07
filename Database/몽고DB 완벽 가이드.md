
# PART I 몽고DB 시작
## CHAPTER 1 몽고DB 소개
- 도큐먼트 지향 데이터베이스다
- 관계형 모델이 아니어서 스케일 아웃이 쉽다
- 도큐먼트의 키와 값을 미리 정의하지 않는다. 고정된 스키마가 없다
	- 모델을 수시로 바꿀 수 있어 개발 속도가 향상된다
	- 모델들을 실험할 수 있다
- 고성능을 유지하도록 설계됐다
## CHAPTER 2 몽고DB 기본  
- 도큐먼트
	- RDB의 행에 대응된다
	- 대이터형과 대소문자를 구분한다
	- 도큐먼트의 키
		- 문자열이다.
		- 키는 `\0` (null문자)를 포함하면 안된다
		- .과 $는 특정 상황에서만 사용하고, 예약어로 취급해야 한다
	- 도큐먼트의 값
		- 데이터형이어야 한다
- 컬렉션
	- RDB의 테이블에 대응된다
	- 하나의 컬렉션 내 도큐먼트들이 모두 다른 구조를 가질 수 있다 (동적 스키마)
	- 하지만 관련된 유형의 도큐먼트를 컬렉션으로 묶는게 성능상, 유지보수상 좋다
	- 안되는 컬렉션 이름
		- 빈 문자열
		- `\0` (null문자) 또는 $가 포함된 문자열
		- `system.` 으로 시작하는 문자열
- 데이터베이스
	- 데이터베이스에 컬렉션을 그룹 지어 놓는다
- 데이터형
	- null
	- 불리언
	- 숫자 (정수, float)
		- 
		  ```json
		  {"x": 3.14}
		  {"x": 3}
		  {"x": NumberInt("3")} // 4바이트 signed integer
		  {"x": NumberLong("3")} // 8바이트 signed integer
			```
	- 문자열
	- 날짜
		- 
		  ```json
		  {"x": new Date()} // new 를 반드시 써야한다
			```
	- 정규표현식
	- 배열
		- 배열 내 값들의 데이터형이 모두 같지 않아도 되고, 중첩배열도 된다
	- 내장 도큐먼트
		- 도큐먼트 내 도큐먼트를 구성할 수 있지만 데이터 반복이 생길 수 있다
	- 객체 id
		- 
		   ```json
		  {"x": ObjectId()}
			```
		- 모든 도큐먼트는 `_id` 키를 가진다. 값은 아무 데이터형도 가능하지만 ObjectId가 기본이다
		- ObjectId
			- 12바이트 (16진수 24자리 문자열)
			- 분산환경에서 유일하도록 설계됨
			- 처음 4바이트 : 타임스탬프 (1970.1.1부터 ms 단위로 저장)
			- 중간 5바이트 : 랜덤값
			- 마지막 3바이트 : 단순 증분
	- 이진 데이터
	- 코드
## CHAPTER 3 도큐먼트 생성, 갱신, 삭제
- 도큐먼트 삽입
	- 
	  ```
	  // 단건 삽입. _id 키도 추가됨
	  > db.movies.insertOne({"title": "movie1"})
	  
	  // 다건 삽입. 데이터 제한이 있다.
	  > db.movies.insertMany([{"title": "movie1"},
							  {"title": "movie2"},
							  {"title": "movie3"}])
	  
	  // 정렬된 삽입. 중간에 실패하면 실패하기 전까지 저장하고 그 이후는 저장X. 기본값인 true
	  > db.movies.insertMany([{"title": "movie1"},
							  {"title": "movie2"},
							  {"title": "movie3"}],
							  {"ordered": true})
							  
	  // 정렬되지 않은 삽입. 실패하는것만 skip한다
	  > db.movies.insertMany([{"title": "movie1"},
							  {"title": "movie2"},
							  {"title": "movie3"}],
							  {"ordered": false})					  
		```
- 도큐먼트 삭제
	- 
	  ```
	  // 단건 삭제. 필터에 일치하는 하나 삭제
	  > db.movies.deleteOne({"_id": 4})
	  
	  // 다건 삭제
	  > db.movies.deleteMany({"year": 1984})
		```

## CHAPTER 4 쿼리  


# PART II 몽고DB 개발  
## CHAPTER 5 인덱싱  
## CHAPTER 6 특수 인덱스와 컬렉션 유형  
## CHAPTER 7 집계 프레임워크  
## CHAPTER 8 트랜잭션  
## CHAPTER 9 애플리케이션 설계  


# PART III 복제  
## CHAPTER 10 복제 셋 설정  
## CHAPTER 11 복제 셋 구성 요소  
## CHAPTER 12 애플리케이션에서 복제 셋 연결  
## CHAPTER 13 관리  


# PART IV 샤딩  
## CHAPTER 14 샤딩 소개  
## CHAPTER 15 샤딩 구성  
## CHAPTER 16 샤드 키 선정  
## CHAPTER 17 샤딩 관리  


# PART V 애플리케이션 관리  
## CHAPTER 18 애플리케이션 작업 확인  
## CHAPTER 19 몽고DB 보안 소개  
## CHAPTER 20 영속성  


# PART VI 서버 관리  
## CHAPTER 21 몽고DB 시작과 중지  
## CHAPTER 22 몽고DB 모니터링  
## CHAPTER 23 백업  
## CHAPTER 24 몽고DB 배포