### 2. 비즈니스서비스연결

- `MemberVO.java`

```java
@Component("memberVO")
public class MemberVO {
	private String id;
	private String pwd;
	private String name;
	private String email;
	private Date joinDate;
	
	..생략
```



- `MemberDAO.java`

> **무엇을 할것인가? 에 따른 입출력을  sql쿼리를 고려하여 잘생각해봐야 한다!!!!!!!!!**
>
> D(delete)L(insert)U(update)는 테이블에 변경을 가하는 메소드인데 변경된 라인수가 몇 개인지 출력해줘야 하기 때문에 int의 출력값을 가진다.



#### **SqlSession 클래스에서 제공하는 여러가지 메소드**

| **메소드** [출력 메소드(입력)]         | **기능**                                                     |
| -------------------------------------- | ------------------------------------------------------------ |
| **List selectList(query_id)**          | id에 대한 select 문을 실행한 후 여러 레코드를 List로 반환    |
| **List selectList(query_id, 조건)**    | id에 대한 select 문을 실행하면서 사용되는 조건도 전달        |
| **T selectOne(query_id)**              | id에 대한 select 문을 실행한 후 지정된 타입으로 한 개의 레코드 반환 |
| **T selectOne(query_id, 조건)**        | id에 대한 select 문을 실행하면서 사용되는 조건도 전달        |
| **Map<K,V> selectMap(query_id, 조건)** | id에 대한 select 문을 실행하면서 사용되는 조건도 전달. Map 타입으로 레코드 반환 |
| **int insert(query_id, Object obj)**   | id에 대한 insert문을 실행하면서 객체의 값을 테이블에 추가    |
| **int update(query_id, Object obj)**   | obj 객체의 값을 조건문의 수정 값으로 사용해 id데 대한 update문 실행 |
| **int delete(query_id, Object obj)**   | obj 객체의 값을 조건문의 조건 값으로 사용해 id데 대한 delete문 실행 |

1. 데이터 베이스에 있는 모든 것을 가져오기 때문에  input은 필요없고 output을 list로 받아오자.

```java
public List selectAllMemberList() throws DataAccessException;
```

2. table에 있는 값을 memberVO에 담고자 하니까  input은 memberVO를 보내 table값과 매칭하고자 한다.

```java
public int insertMember(MemberVO memberVO) throws DataAccessException ;
```

3. table에 있는 한 행의 값을 지정한 id값으로 삭제하려고 하니 input은 id값이 된다.

```java
public int deleteMember(String id) throws DataAccessException;
```

4. table에 있는 한 행의 값을 지정한 id를 통해 불러오려고 하니 input은 id값이 되고 output은 memberVO가 된다.

```java
public MemberVO searchMember(String id) throws DataAccessException;
```

5. table에 있는 한행의 값을 지정한 한행으로 수정하고자 하니 input은 MemberVO값이 된다.

```java
public int updateMember(MemberVO memberVO) throws DataAccessException;
```



- `MemberDAOImpl.java`

> 데이터베이스 작업을 클레스가 아니고 xml과 연결하여 외부로 다 뺐다.
>
> member.xml로 빼내고 autowired로 연결한다.

```java
@Repository("memberDAO") //sql, connection, rs 등등 다없네
public class MemberDAOImpl implements MemberDAO {
	@Autowired
	private SqlSession sqlSession;
    
	@Override
	public List selectAllMemberList() throws DataAccessException {
		List<MemberVO> membersList = null;
		//아래 호출하면 member.xml과 mybatis.xml(이건 미리 다 세팅이 되어있다)이 알아서 커넥션하고 쿼리 날리고 해서 정보를 가져와 리턴해준다. 호출하면 web.xml 의listener -> action-mybatis.xml의sqlssion -> sqlfactory 자세한건 밑에 코드 
		membersList = sqlSession.selectList("mapper.member.selectAllMemberList"); 
		return membersList;
	}
```



- `MemberServiceImpl.java`

```java
@Service("memberService")
@Transactional(propagation = Propagation.REQUIRED)
public class MemberServiceImpl implements MemberService {
	@Autowired
	private MemberDAO memberDAO;
	
	@Override
	public MemberVO login(MemberVO memberVO) throws DataAccessException{
		MemberVO vo = null;
		vo = memberDAO.loginById(memberVO);
		System.out.println("service in");
		return vo;
	}
}
```

