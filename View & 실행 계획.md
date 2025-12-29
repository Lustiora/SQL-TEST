```sheet
|  ▷▶▷▶ Query 실행 순서 ▷▶▷▶  | < | < | < | < | < | < | < | < | < |
| :----: | :------: | :---------------: | :------: | :------------: | :------: | :---------------: | :------: | :------------: | :------------: |
|  [With](1.%20With)  |   [From](2.%20From%20&%20Join)   |       [Where](3.%20Where%20&%20Group%20By%20&%20Having#^where)       | [Group by](3.%20Where%20&%20Group%20By%20&%20Having#^groupby) |     [Having](3.%20Where%20&%20Group%20By%20&%20Having#^having)     | [Select](4.%20Select) | [Distinct](연산자#^distinct) | [Union / Union all](연산자#^union) | [Order by](5.%20Order%20By%20&%20Offset%20&%20Limit#^orderby) | [Offset](5.%20Order%20By%20&%20Offset%20&%20Limit#^offset) / [Limit](5.%20Order%20By%20&%20Offset%20&%20Limit#^limit) |
```
- **실행계획**
	- 성능 최적화, 인덱스 전략을 위해 사용됨
		- Incremental Sort : 데이터의 정렬 상태를 깨뜨리지 않고 효율적으로 최적화된 상태 (Cost ▼)
		- Full Sort : 모든 데이터를 재정렬하여 출력하는 상태 (Cost ▲)
	- 응답 속도 : Index > Table
		- Table 당 Index(PK)는 1개만 지정 가능
			- Index를 추가할 수 있지만 메모리 사용량이 증가
				- 사용량을 계산하여 최대 10 ~ 15% 미만으로 설계
				- Index 생성 : `create index [index name] on [table](column)`
					- 생성된 index는 별도의 물리적 공간에 저장
				- Index 삭제 : `drop index [index name]`
			- Select 대상이 index or PK  : 조건을 지정해도 DB는 Index Scan
				- index full scan > index 전체를 검색
					`select empno from emp where To_char(empno) = 7369;`
				- index full scan
					`select empno from emp where empno - 1 = 7368;`
				- index unique scan > 해당하는 건만 검색
					`select empno from emp where empno  = 7369;`
				- index fast full scan > 전체를 호출하고 조건 해당부분만 제외하여 Fast
					`select empno from emp where empno <> 7369;`
				- index fast full scan
					`select empno from emp;`
				- index full scan
					`select empno from emp where empno like '73%';`
				- table access full > index에 포함되지 않은 column을 조회하여 table scan
					`SELECT empno FROM emp WHERE empno = 7369 OR ename = 'SMITH';`
		- B-Tree (Balanced Tree) 구조로 관리됨
				![[Pasted image 20251224121028.png]]
			- [https://suyeonme.tistory.com/102](https://suyeonme.tistory.com/102)
	- [https://green-bin.tistory.com/236](https://green-bin.tistory.com/236)
	- [https://velog.io/@chullll/DB-옵티마이저-실행계획-INDEX](https://velog.io/@chullll/DB-옵티마이저-실행계획-INDEX)

<p></p>

- **View**
	- 제작한 쿼리를 간편하게 호출하기 위해 사용
	- Table과 달리 물리적인 저장공간을 갖지 않고 Data Dictionary에 저장
		- 정규화 규칙을 따르지 않아도 되고 목적에 따라 자유롭게 사용 가능
	- Table과 거의 동등하게 취급되는 논리적인 집합
	- Index , Clustering (클러스터링) 지정은 불가
		- 가상의 Table이라 실제 Data를 저장하지 않고 원본 Table Data를 참조
	- View 생성시 제작한 쿼리와 동일한 column 개수를 사용
		- Column 명은 동일할 필요 없음
			- `create or replace view emp_view (e_no, e_name) as`
```sql
select empno, ename
from emp
where deptno = 10;

select * from emp_view;
```
![[Pasted image 20251224123829.png]]