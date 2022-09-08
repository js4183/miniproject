# portfolio
# 제목
## 제목
### 제목

### 소스코드넣기
```
public class 첫자바 {
	public static void main(String[] args) {
		
	}
}
```

### 링크넣기
[참고사이트](https://blog.naver.com/jang0_0yw)

### 목록넣기
* 깃허브활용
  * push하기
    * push1
    * push2
  * pull하기
  
1. 첫번째
2. 두번째
 
### 테이블만들기
일정|상세내용
---|---|
9/1~9/3|프로젝트기획
9/4~9/5|DB설계


## 기술
* jdbc

## 구현기능
* DB연결메소드

<details>
    <summary>DB연결 코드보기</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
public void connect() { //DB연결 메소드
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");

			String url = "jdbc:oracle:thin:@project-db-stu.ddns.net:1524:xe";
			String db_id = "campus_k_0830_1";
			String db_pw = "smhrd1";

			conn = DriverManager.getConnection(url, db_id, db_pw);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

public void close() { //DB닫는 메소드
		try {
			if (rs != null) {
				rs.close();
			}
			if (psmt != null) {
				psmt.close();
			}
			if (conn != null) {
				conn.close();
			}
		} catch (SQLException e) {// Exception
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
```

</details>

* member

<details>
    <summary>회원가입시 DB에 저장하는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//회원가입시 DB에 저장
	public boolean userInsert(String id, String pw, String nick) { 
		try {
			connect();
			
		
			String sql = "insert into member values(?, ?, ?)";// 저장될 테이블 이름수정하기

			psmt = conn.prepareStatement(sql);

			psmt.setString(1, id);
			psmt.setString(2, pw);
			psmt.setString(3, nick);

			int result = psmt.executeUpdate();

			if (result > 0) {
				check = true;
			} else {
				check = false;
			}
		} catch (Exception e) {
			System.out.println("중복된 아이디이거나 잘못된 형식입니다.");
		} finally {
			close();
		}
		return check;
	}
```
</details>

<details>
    <summary>로그인정보 DB확인하는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//로그인정보 DB확인
public String login(String id, String pw) {
		String userNick = null;

		try {
			connect();

			String sql = "select nick from member where id=? and pw=?";
			psmt = conn.prepareStatement(sql);
			
			psmt.setString(1,id);
			psmt.setString(2,pw);
			
			rs = psmt.executeQuery();
			
			if(rs.next()) {
				userNick = rs.getString(1);
			}
			
		} catch (Exception e) {
		} finally {
			close();
		}
		return userNick;
	}
```
<details>

<details>
    <summary>로그인시 스코어가져오는  </summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->
```
//로그인정보 스코어 가져오기
		public int Scoreget(String nick) {
			int score = 0;

			try {
				connect();

				String sql = "select score from ranking where nick=?";
				psmt = conn.prepareStatement(sql);
				
				psmt.setString(1,nick);
				
				rs = psmt.executeQuery();
				
				if(rs.next()) {
					score = rs.getInt("score");
				}
				
			} catch (Exception e) {
			} finally {
				close();
			}
			return score;
		}
```
<details>

<details>
    <summary>첫 점수생성하는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//첫 점수 생성
	public boolean insertScore(String nick, int score) {
		try {
			connect();
			
		
			String sql = "insert into ranking values(?, ?)";// 저장될 테이블 이름수정하기

			psmt = conn.prepareStatement(sql);

			psmt.setString(1, nick);
			psmt.setInt(2, score);

			int result = psmt.executeUpdate();

			if (result > 0) {
				check = true;
			} else {
				check = false;
			}
		} catch (Exception e) {
		} finally {
			close();
		}
		return check;
	}
```
<details>

<details>
    <summary>점수 보유시 가져오는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//점수 가져오는 메소드
	public int getuserScore(String nick) {
		try {
			connect();
			
			String sql = "select score from ranking where nick=?";
			
			psmt = conn.prepareStatement(sql);
			
			psmt.setString(1, nick);
			
			rs = psmt.executeQuery();
			
			if(rs.next()) {
				score = rs.getInt("score");
			}
			
			
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			close();
		}
		return score;
	}
```
<details>

<details>
    <summary>회원가입시 DB에 저장하는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//점수 업데이트 메소드
	public void updateScore(int score, String nick) {
		try {
			connect();
			
			String sql = "update ranking set score = ? where nick = ?";
			psmt = conn.prepareStatement(sql);
			
			
			psmt.setInt(1, score);
			psmt.setString(2, nick);
			
			psmt.executeUpdate();
			
			
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			close();
		}
	}
```
<details>

<details>
    <summary>최종점수 랭킹 DB에 저장하는 코드</summary>

<!-- summary 아래 한칸 공백 두고 내용 삽입 -->

```
//랭킹 DB에 저장
	public ArrayList<MemberDTO> rank() {
		int n = 1;
		ArrayList<MemberDTO> al = new ArrayList<MemberDTO>();
		try {
			connect();

			String sql = "select * from ranking order by score desc";

			psmt = conn.prepareStatement(sql);
			rs = psmt.executeQuery();
			
			System.out.println("  닉네임\t점수");
			while (rs.next()) {
				String nick = rs.getString("nick");
				int score = rs.getInt("score");
				System.out.println(n+". "+nick + "\t" + score);
				n++;
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			close();
		}
		return al;
	}
```
<details>

* movielist
