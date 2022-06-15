# SerialVersionUID 지정하는 이유

- SerialVersionUID는 Serializable을 상속하는 Class의 경우 Class의 Versioning 용도로 사용한다.
- 이때 SerialVersionUID 값을 명시적으로 지정하지 않으면 Compiler가 자동으로 계산된 값으로 지정하게 된다.
- 만약 클래스에 멤버가 추가된다던가 변경이 일어나게 되면 SerialVersionUID가 변경되게 된다.
- 그렇게 된다면 직렬화/역직렬화할 때 SerialVersionUID 값이 달라져 InvalidClassException을 발생시켜 변환할 수 없게 된다.

## 발생하는 문제

- 만약 SerialVersionUID를 설정하지 않고 클래스에 변동사항이 생겼을 경우

### 준비

- Serializable을 구현한 User 클래스 생성

```java
@Data
public class User implements Serializable {

    private final String username;
    private final String password;
}
```

- 객체를 직렬화/역직렬화하는 메소드 생성

```java
public class UserUtil {

    private static final String path = "./user.txt";

    public static void writeFile(User user) throws Exception {
        FileOutputStream fo = new FileOutputStream(path);
        ObjectOutputStream out = new ObjectOutputStream(fo);
        out.writeObject(user);
        out.close();
    }

    public static void readFile() throws Exception {
        FileInputStream fi = new FileInputStream(path);
        ObjectInputStream in = new ObjectInputStream(fi);
        User result = (User) in.readObject();
    }
}
```

- 테스트 생성

```java
class UserTest {

    @Test
    void writeTest() throws Exception {
        User user = new User("username", "password");
        UserUtil.writeFile(user);
    }

    @Test
    void readTest() throws Exception {
        UserUtil.readFile();
    }
}
```

### User 객체 변경

- 변경하는 방법은 여러 가지가 있을 수 있다.

```java
// Case 1
private final String username;
private final String password;
private final String birth;

// Case 2
private String username;
private final String password;

// Case 3
private final String username;
private final Integer password;
```

- 이미 이전 User로 생성했던 파일을 클래스를 변경하고 읽어 들이면 Exception이 발생한다.

<img width="1358" alt="serialVersionUIDFail" src="https://user-images.githubusercontent.com/50051656/173797843-2ec494c7-7c4d-4c47-b614-04456433d349.png">

## 해결 방법

- SerialVersionUID를 명시적으로 지정
- 똑같이 클래스 내용 변경해도 테스트 성공하는 것을 볼 수 있다.

```java
@Data
public class User implements Serializable {

    private static final long serialVersionUID = -348313511482571668L;

    ...
}
```

- 만약에 SerialVersionUID를 변경하게 되면 그 외에 클래스 내용은 변경하지 않아도 UID가 달라서 테스트 실패하게 된다.

<img width="1356" alt="serialVersionUIDFail2" src="https://user-images.githubusercontent.com/50051656/173797853-ed9f10ea-065a-45d6-9bc9-792cbbb25755.png">

## Reference

- [https://m.blog.naver.com/writer0713/220922099055](https://m.blog.naver.com/writer0713/220922099055)