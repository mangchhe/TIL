# 레이아웃 종류

## LinearLayout

- 뷰를 가로, 세로 방향으로 나열하는 레이아웃 클래스
- android:orientation : 방향을 정함
  - horizontal
  - vertical
- android:layout_weight : 여백을 채울 수 있다. 같은 level의 뷰에 설정하면 서로 가중치를 설정할 수 있다.
- android:gravity, layout_gravity : 뷰 정렬
  - default는 left/top
  - gravity는 정렬 대상이 콘텐츠, layout_gravity는 뷰 자체를 정렬하는 속성이다.

## RelativeLayout

- 상대 뷰의 위치를 기준으로 정렬하는 레이아웃 클래스
- android:layout_above : 기존 뷰의 위쪽에 배치
- android:layout_below : 기존 뷰의 아래쪽에 배치
- android:layout_toLeftOf : 기존 뷰의 왼쪽에 배치
- android:layout_toRightOf : 기존 뷰의 오른쪽에 배치
- [그 외 속성](https://developer.android.com/reference/android/widget/RelativeLayout.LayoutParams)

## GridLayout

- 행과 열로 구성된 테이블 화면을 만드는 레이아웃 클래스
- android:orientation : 방향 설정
- android:rowCount : 세로로 나열할 뷰 개수
- android:columnCount : 가로로 나열할 뷰 개수
- android:layout_row : 뷰가 위치하는 세로 방향 인덱스
- android:layout_column : 뷰가 위치하는 가로 방향 인덱스
- android:layout_gravity : 뷰 크기 확장
- android:layout_columnSpan : 가로 열 병합
- android:layout_rowSpan : 세로 행 병합


## ConstraintLayout

- 뷰를 상대 위치로 배치하는 RelativeLayout과 비슷하지만, 더 많은 속성을 제공한다.
- 안드로이드 플랫폼이 아니라 androidx에서 제공하는 라이브러리이다.