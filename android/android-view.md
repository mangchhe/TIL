# 화면 구성 방법

- 화면을 출력하는 컴포넌트는 액티비티만 존재

> 화면 열 개를 만들려면 액티비티도 열 개를 만들어야 하나?
>
> 화면이 10개라도 액티비티 한 개로 가능하다.
> Fragment를 이용 → 추후 다룸

- 액티비티는 화면을 출력하는 컴포넌트일 뿐 그 자체 화면은 아니다.
- 화면을 구성하기 위해서는 View 클래스를 이용해야 한다.
  - TextView
  - ImageView

## 액티비티 코드 화면 구성

- 뷰로 화면을 구성하는 방법은 두 가지가 존재한다.
  - 액티비티 코드 작성
  - 레이아웃 XML 파일 작성

> 화면 구현 방법 중 어떤 것이 더 좋은가?
>
> 개발자가 선택하기 나름이지만 효율성을 고려한다면 XML이 더 좋다.
> 액티비티는 네트워킹, 데이터 핸들링, 사용자 이벤트 처리 등만 작성하고
> 화면 구현은 XML로 구현한다.

## View 클래스

- View : 모든 뷰 클래스의 최상위 클래스
    - ViewGroup : View 하위 클래스지만 자체 UI는 없다. 다른 뷰 여러 개를 묶어서 제어할 목적으로 사용한다.
        - LinearLayout
        - RelativeLayout
    - TextView
    - ImageView 

### ViewGroup

- 화면 자체가 아닌 다른 뷰(객체) 여러 개를 담아서 한꺼번에 제어할 목적
- 컨테이너 기능 담당

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
</LinearLayout>
```

#### 레이아웃 중첩

- 이처럼 객체를 계층 구조로 만들어 이용하는 패턴을 **컴포지트 패턴**, **문서 객체 모델**이라고 한다.

```javascript
|-- LinearLayout
    |-- Button
    |-- Button
    |-- LinearLayout
        |-- Button
        |-- Button
```


## 레이아웃 XML의 뷰를 코드에서 사용하기

- XML에 선언한 객체에 id 속성을 넣는다.
- 속성은 앱에서 유일해야 한다.

```xml
<TextView
    android:id="@+id/textView"
</>
```

- `findViewById()` 함수를 통해서 객체를 얻을 수 있다.

```kotlin
findViewById(R.id.testView)
```

## 뷰 크기 지정

- 뷰가 화면에 나올 때 어떤 크기로 보여야 하는지 필수 정보
- 수치 : 크기를 정할 수 있고 단위는 px, dp
- match_parent : 부모 크기의 전체
- wrap_content : 자신의 콘텐츠를 출력할 수 있느 적절한 크기

```xml
android:layout_width="match_parent"
android:layout_height="match_parent"
```

> 구체적인 수치가 아니라 match_parent, wrap_content 사용 이유
>
> 기기마다 크기가 다양해서 호환성을 생각하기 위해서는 절대적인 수치보다 상대적인 수치가 좋을 수 있다.

## 뷰 간격 설정

- margin : 뷰와 뷰 사이 간격
  - layout_margin
- padding : 뷰의 콘텐츠와 테두리 사이의 거리
  - paddingLeft
  - paddingRight
  - ...

## 뷰의 표시 여부 설정

- visibility
  - visible : 화면에 출력. default
  - invisible : 미출력되지만, 화면은 차지한다.
  - gone : 미출력되고 화면 자리를 차지하지 않는다.

## TextView

### Attribute

- android:text : 출력할 문자열
- android:textColor : 문자열 색상
- android:textSize : 문자열 크기
- android:textStyle : 문자열 스타일 (bold, italic, normal)
- android:autoLink : 특정 형태의 문자열에 자동 링크 추가
  - web, phone, email 등이 있으며 | 기호를 통해 여러 개를 함께 설정할 수 있다.
- android:maxLines : 긴 문자열의 경우 자동 줄바꿈을 해주는데 특정 행을 고정할 수 있다.
- android:ellipsize : 출력되지 않은 문자열이 있다는 걸 표시하기 위해 ... 를 넣는다.
  - start, middle, end

## ImageView

### Attribute

- android:src : 출력할 이미지
- android:maxWidth, maxHeight : 최대 가로, 세로 크기
- android:adjustViewBounds : 가로, 세로 길이의 비율을 맞춰준다.

## Button, CheckBox, RadioButton

## EditText

- android:lines : 처음부터 설정한 라인의 수만큼 입력 크기가 출력된다.
- android:maxLines : 처음에는 한 줄이었다가 설정한 라인의 수만큼 늘어난다.
- android:inputType : text, number, phone ...

## 뷰 바인딩

- XML 파일에 선언한 뷰 객체를 코드에서 쉽게 이용하는 방법
- 이전에는 `findViewById()` 함수를 이용해서 가져왔지만 하나의 화면에 여러 개의 뷰가 존재하고 해당 작업은 번거롭다.

```gradle
android {
    viewBinding {
        enabled = true
    }
}
```

- 위와 같이 gradle 파일에 설정해주게 되면 XML 파일에 등록된 뷰 객체를 포함하는 클래스가 자동으로 생성된다.
- **a**ctivity_**m**ain.xml → **A**ctivity**M**ain**Binding**

```kotlin
val binding = ActivityMainBinding.inflate(layoutInflater)
binding.textView
```

- 레이아웃 XML마다 하나의 바인딩 클래스가 생성되는데 필요 없는 XML도 있을 수 있다.
- XML 파일 루트 태그에 `tools:viewBindingIgnore=true`라는 속성을 추가하면 해당 XML 파일을 바인딩 클래스를 만들지 않는다.