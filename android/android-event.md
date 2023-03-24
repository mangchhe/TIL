# 사용자 이벤트 처리하기

## 터치 이벤트

- 터치 이벤트를 처리하고 싶으면 액티비티 클래스에 콜백 함수인 `onTouchEvent()`를 선언한다.
- 매개변수로 MotionEvent 객체는 터치의 종류와 발생 지점이 담긴다.

```kotlin
override fun onTouchEvent(event: MotionEvent?): Boolean {
    return super.onTouchEvent(event)
}
```

> 콜백 함수
>
> 어떤 이벤트가 발생하거나 시점에 도달했을 때 시스템에서 자동으로 호출하는 함수

### 터치 이벤트 종류

- ACTION_DOWN : 화면을 손가락으로 누른 순간의 이벤트
- ACTION_UP : 화면에서 손가락을 떼는 순간의 이벤트
- ACTION_MOVE : 화면을 손가락으로 누른 채로 이동하는 순간의 이벤트

### 터치 이벤트 발생 좌표 얻기

- x, y : 이벤트가 발생한 뷰의 x, y 좌표
- rawX, rawY : 이벤트가 발생한 화면의 X, Y

```kotlin
override fun onTouchEvent(event: MotionEvent?): Boolean {
    when (event?.action) {
        MotionEvent.ACTION_DOWN -> {
            Log.d("Joo", "Touch Down Event")
            Log.d("Joo", "Touch xy - x : ${event.x}, y : ${event.y}")
            Log.d("Joo", "Touch rawXy - x : ${event.rawX}, y : ${event.rawY}")
        }
        MotionEvent.ACTION_UP -> {
            Log.d("Joo", "Touch Up Event")
        }
        MotionEvent.ACTION_MOVE -> {
            Log.d("Joo", "Touch Move Event")
        }
    }
    return super.onTouchEvent(event)
}
```

## 키 이벤트

- onKeyDown : 키를 누른 순간의 이벤트
- onKeyUp : 키를 떼는 순간의 이벤트
- onKeyLongPress : 키를 오래 누르는 순간의 이벤트
- 소프트 키보드는 키 이벤트로 처리할 수 없고 하드웨어 키보드를 이용하면 처리할 수 있다.

> 소프트 키보드
>
> 앱에서 글을 입력할 때 화면 아래에서 올라오는 키보드

```kotlin
override fun onKeyDown(keyCode: Int, event: KeyEvent?): Boolean {
    when (keyCode) {
        KeyEvent.KEYCODE_0 -> Log.d("Joo", "key down 0 키")
        KeyEvent.KEYCODE_1 -> Log.d("Joo", "key down 1 키")
    }
    return super.onKeyDown(keyCode, event)
}

override fun onKeyUp(keyCode: Int, event: KeyEvent?): Boolean {
    when (keyCode) {
        KeyEvent.KEYCODE_8 -> Log.d("Joo", "key up 8 키")
        KeyEvent.KEYCODE_9 -> Log.d("Joo", "key up 9 키")
    }
    return super.onKeyUp(keyCode, event)
}

override fun onKeyLongPress(keyCode: Int, event: KeyEvent?): Boolean {
    when (keyCode) {
        KeyEvent.KEYCODE_A -> Log.d("Joo", "key long press A 키")
        KeyEvent.KEYCODE_B -> Log.d("Joo", "key long press B 키")
    }
    return super.onKeyLongPress(keyCode, event)
}
```

### 하드웨어 키보드

- 전원 버튼, 볼륨 조절 버튼, 내비게이션 바에 있는 뒤로가기, 홈 버튼 등에 대한 이벤트를 처리할 수 있다.

```kotlin
override fun onKeyDown(keyCode: Int, event: KeyEvent?): Boolean {
    when (keyCode) {
        KeyEvent.KEYCODE_BACK -> Log.d("Joo", "Back Button")
        KeyEvent.KEYCODE_VOLUME_UP -> Log.d("Joo", "Volume Up")
        KeyEvent.KEYCODE_VOLUME_DOWN -> Log.d("Joo", "Volume Down")
    }
    return super.onKeyDown(keyCode, event)
}
```

## 뷰 이벤트

- 뷰를 사용자가 터치했을 때 이벤트 처리를 각 뷰에서 별도로 제공한다.
- 뷰 이벤트도 터치 이벤트로 처리하지 않은 이유는 할 수는 있지만 복잡해지기 때문이다.
- 뷰 이벤트는 이벤트 콜백 함수만 선언해서는 처리할 수 없다.
- 뷰 이벤트 처리는 세 가지로 역할을 나눠 연결해 이벤트를 처리한다.
  - 이벤트 소스 : 이벤트가 발생한 객체
  - 이벤트 핸들러 : 이벤트 발생 시 실행할 로직이 구현된 객체
  - 리스너 : 이벤트 소스와 이벤트 핸들러를 연결해주는 함수

### CheckBox 예제

```kotlin
class CheckBoxEventHandler : CompoundButton.OnCheckedChangeListener {
    override fun onCheckedChanged(p0: CompoundButton?, p1: Boolean) {
        Log.d("Joo", "체크박스 클릭")
    }
}

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainBinding.inflate(layoutInflater)

        setContentView(binding.root)
        binding.checkbox.setOnCheckedChangeListener(CheckBoxEventHandler())
    }
}
```

## Button 예제

- setOnClickListener - OnClickListener
- setOnLongClickListener - OnLongClickListener
- SAM 기법 이용

```kotlin
binding.button.setOnClickListener { x -> Log.d("Joo", "버튼 클릭") }
```

> SAM (Single Abstract Method)
>
> 자바 인터페이스를 간단하게 사용하기 위해 제공하는 기법
> 자바 함수형 인터페이스를 람다로 이용해서 사용하는 것과 유사