# 리소스 종류와 특징

- 앱 개발에서 리소스란 정적인 자원이라고 할 수 있다.
- 리소스는 크게 앱 리소스와 플랫폼 리소스로 구분된다.

## 앱 리소스

- 기본적으로 생성되는 디렉터리
- 아래 리소스 디렉터리명은 고정이며 개발자가 임의로 디렉터리를 생성할 수 없다. 하위 디렉터리도 마찬가지이다.
- 리소스 파일명은 values 디렉터리 제외한 나머지는 자바 이름 작성 규칙을 지켜야 한다.
  - 알파벳 대문자 사용 X
  - 이유는 해당 파일들을 R파일에 식별자로 등록해서 이용하기 때문이다.

```javascript
|-- res
    |-- drawable
    |-- layout
    |-- mipmap
    |-- values
```

- 그 외에도 다양하게 존재

|디렉터리명|설명|
|:-:|:-:|
|animator|속성 애니메이션 XML|
|anim|트윈 애니메이션 XML|
|color|색상 상태 목록 정의 XML|
|drawable|이미지 리소스|
|mipmap|앱 실행 아이콘 리소스|
|layout|레이아웃 XML|
|menu|메뉴 구성 XML|
|raw|원시 형태로 이용되는 리소스 파일|
|values|단순 값으로 이용되는 리소스|
|xml|그 외 XML|
|font|글꼴 리소스|

### layout

- 화면을 구성하는 레이아웃 XML 파일 저장하는 디렉터리

### drawable

- 이미지 리소스를 저장하는 디렉터리
- PNG, JPG, GIF, 9PNG 파일과 XML로 작성된 파일을 저장할 수 있다.

#### XML 이미지 태그

- shape : 도형 타입. ex) rectangle, oval, line, ring
- corners : 둥근 모서리를 그리는 데 사용
- gradient : 그라데이션 그릴 때 사용
- size : 도형 크기
- solid : 도형 색상
- stroke : 도형의 윤곽선

### mipmap

- 앱을 기기에 설치하면 나타나는 실행 아이콘의 이미지 리소스를 저장하는 디렉터리


### values

- 값으로 이용되는 리소스를 저장하는 디렉터리
- 문자열, 색상, 크기, 스타일, 배열 등의 값을 XML로 저장할 수 있다.
- 다른 디렉터리랑 다르게 R인 파일에 식별자로 등록되지 않기 때문에 태그를 지정하여 사용한다.

```html
<resources>
    <string name="app_name">APP NAME</string>
</resources>
```

- 위와 같이 name 속성에 지정한 값은 R 파일에 식별자로 기록되어 아래와 같이 사용된다.

```xml
<TextView
    android:text="@string/app_name"
    ... />
```

```kotlin
getString(R.string.app_name)
```

#### 태그 종류

- string : 값 리소스
- color : 색상 리소스
- dimen : 크기 리소스
- style : 스타일 리소스 (뷰에 설정되는 여러 속성을 한 번에 적용하거나 중복되는 스타일을 재사용할 때 사용)

### font

- 글꼴 리소스를 저장하는 디렉터리
- TTF, OTF 파일을 저장한 후 글꼴을 적용할 뷰에서 사용하면 된다.

## 플랫폼 리소스

- 안드로이드 플랫폼에서 제공하는 많은 리소스
- R 대신 android.R
- @drawable/? 대신 @android:drawable/?

## 리소스 조건 설정

- 어떤 리소스를 특정 환경에서만 적용되도록 설정하는 것을 말한다.
- 보통 앱을 개발할 때 아이콘을 선명하게 출력하기 위해 기기 크기별로 이미지를 5개 준비한다.
- 리소스 조건을 이용할려면 아이콘의 파일명을 똑같이 지정해야 한다.
- R 파일에는 식별자가 하나만 생성이 되므로 코드에서는 리소스를 구분할 필요가 없다.
- 이름이 같은 파일을 같은 디렉터리가 아니라 mipmap-mdpi, mipmap-hdpi 처럼 각 디렉터리 담는다. 이때 - 뒤에 단어가 리소스의 조건이다.
- 리소스 디렉터리명에 명시하면 각 환경에 맞는 리소스를 플랫폼이 알아서 적용한다.- [리소스 조건 문서](https://developer.android.com/guide/topics/resources/providing-resources?hl=ko)