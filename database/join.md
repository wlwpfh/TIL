## 8월 3일 Join

### left join & right join

<br/>

1. left join
```  
A left join B on (a.id = b.id)
```  
left join 시 B에 값이 없더라고 A의 모든 값은 출력된다.

<br/>

2. right join
```
   A right join B on (a.id = b.id)
```
right join 시 A에 값이 없더라도 B의 모든 값은 출력된다.  

그렇기에 어떤 값을 보여줄 지 결정하여 선택해야 한다.  

<br/>

### 나의 경우엔 어땠을까?

`ORACLE`에서 `MYSQL`로 문법을 바꾸면서 여러 곳 중에 한 곳을 가져오는 쿼리를 수정했다.  

```SQL
    SELECT
        A.NAME
        , B.ID
        , A.MAP_X
        . A.MAP_Y
        , B.EMAIL
    FROM
        A
        RIGHT JOIN B
        ON A.PLACE_ID = B.PLACE__ID
```
대부분이 잘 작동하였지만 몇 곳에서 해당 쿼리의 결과값을 .jsp 파일로 가져왔을 때 `NullPointerException`이 발생했다. (?)  
문제는 `RIGHT JOIN`에서 `LEFT JOIN`으로 수정 후에 해결되었다.    

해당 `RIGHT JOIN`으로 하면 A테이블에 존재하는 값이 아닌 B테이블에 있는 값을 가져와 해당 부분을 클릭했을 때 관련 값이 없어 에러가 났던 것이다.  
```SQL
    SELECT COUNT(*)
	FROM EB
	LEFT JOIN EA 
	ON EB.ID  = EA.ID 
	WHERE
		1=1
		AND EB.DISPLAY_YN = 'Y'
		AND EB.DELETE_YN = 'N';
-- 결과값 225		

```

```SQL
SELECT COUNT(*)
	FROM EB
	RIGHT JOIN EA 
	ON EB.ID  = EA.ID 
	WHERE
		1=1
		AND EB.DISPLAY_YN = 'Y'
		AND EB.DELETE_YN = 'N';
-- 결과값 173
```
