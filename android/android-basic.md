# 안드로이드 앱 개발 특징

- 안드로이드 앱 개발의 핵심은 **컴포넌트**이다.

## 컴포넌트는 애플리케이션의 구성 요소

- **애플리케이션의 구성 요소**
- 애플리케이션을 구성하는 단위, 즉 하나의 애플리케이션의 여러 컴포넌트의 구성이 된다.
- 안드로이드에서는 **클래스로 컴포넌트를 개발**한다.

## 안드로이드 앱을 구성하는 클래스는 모두 컴포넌트?

- 앱은 여러 개의 클래스로 구성되는데 크게 컴포넌트 클래스와 일반 클래스로 구성
- 이 둘의 차이점은 런타임 때 생명주기를 누가 관리하느냐에 따라 달렸다.
- 객체 생성부터 소멸까지 개발자 코드에서 관리하면 일반 클래스
- 생명주기를 안드로이드 시스템에서 관리하면 컴포넌트 클래스

## 안드로이드 컴포넌트 종류

- 액티비티, 서비스, 콘텐츠 프로바이더, 브로드캐스트 리시버 네 가지로 구분된다.
- 액티비티 : 화면을 구성하는 컴포넌트
- 서비스 : 백그라운드 작업을 하는 컴포넌트
- 콘텐츠 프로바이더 : 앱의 데이터를 공유하는 컴포넌트. 앱 내에서도 다양한 컴포넌트들이 존재
- 브로드캐스트 리시버 : 시스템 이벤트가 발생할 때 실행되게 하는 컴포넌트

## 컴포넌트 구분 방법

- 개발자가 컴포넌트 클래스를 구현하기 위해서는 **지정된 클래스를 상속받아야 한다.**
- 액티비티 : Activity Class
- 서비스 : Service Class
- 콘텐츠 프로바이더 : ContentProvider Class
- 브로드캐스트 리시버 : BroadcastReceiver Class

## 컴포넌트 구성 방법

- 설계에 따라 달라지며 정해진 규칙은 없다.

## 컴포넌트는 앱 안에서 독립된 실행 단위

- 독립된 실행 단위란 컴포넌트끼리 서로 종속되지 않아서 코드 결합이 발생하지 않는다.

## 앱 실행 시점이 다양하다.

- 안드로이드 앱에는 메인 함수는 없다고 말할 수 있다.
- 메인 함수란 앱의 단일 시작점을 의미하는데 안드로이드 앱은 실행 시점이 다양해서 메인 함수 개념이 없다고 표현한다.

## 애플리케이션 라이브러리를 사용할 수 있다.

- 다른 애플리케이션을 라이브러리처럼 이용할 수 있다.

## 리소스를 활용한 개발

- 리소스란 코드에서 정적인 값을 분리한 것을 의미
- 앱에서 발생하는 데이터가 동적인 값이 아니라면 굳이 코드에 담지 않고 분리해서 개발할 수 있다.

