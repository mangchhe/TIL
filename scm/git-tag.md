# Git Tag

## 태그 조회

```shell
git tag
git tag -l
git tag --list
git tag -l <태그명>
```

## 태그 붙이기

- 태그에는 두 종류가 있다.
    - Lightweight : 특정 커밋을 가리키는 역할만 한다.
    - Annotated : 태그를 만든 사람, 이메일, 날짜, 메시지를 저장. GPG 서명도 가능하다.

### Lightweight

```shell
git tag v1.0.0
git tag v1.0.0 <커밋 해시값>
```

### Annotated

```shell
git tag -a v1.0.0
git tag -a v1.0.0 -m<태그 메시지>
git tag -a v1.0.0 -m<태그 메시지> <커밋 해시값>
```

## 태그 확인

- 태그 메시지, 커밋 내용 등을 확인할 수 있다.

```shell
git show <태그명>
```

## 원격 저장소에 태그 올리기

```shell
git push origin <태그명>
git push origin --tags
```

## 원격 저장소에 태그 가져오기

```shell
git fetch --tags
```

## 태그 삭제

```shell
git tag -d v1.0.0
git tag --delete v1.0.0

// 원격 저장소 태그 삭제
git push origin :<태그명>
```

## 특정 태그로 이동하기

```shell
git checkout <태그명>

// 되돌아오기
git switch -
```