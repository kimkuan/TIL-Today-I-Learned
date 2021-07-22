# CSS - Layout



### Display 속성

: 요소의 렌더링 박스 유형을 결정하는 속성입니다.



[ 속성 값 ]

- none - 요소가 렌더링 되지 않음.
- inline - inline level 요소처럼 렌더링
- block - block level 요소처럼 렌더링
- inline-block - inline level 요소처럼 렌더링되지만 block level의 성질을 갖는다. (width, height 설정 가능)



```
inline 
    - 하나의 라인안에서 자신의 내용만큼 박스를 만드는 요소. 
    - 요소 앞 뒤로 줄 바꿈이 되지 않아 다른 인라인 요소들이 자리할 수 있다.
    - 자손으로 블록레벨을 가질 수 없다!
    - width, height 값을 지정할 수 없다.
    - ex) span, i, img, em, strong, a ... 

block
	- 부모요소의 가로 영역에 맞게 채워져 표현되는 요소.
	- 양옆으로 다른 요소가 배치되지 않게 박스를 생성하므로 박스 위아래로 줄바꿈이 생긴다.
	- width, height 값을 지정할 수 있다.
	- ex) div, h1~h6, p, ul, li, table ...
```



### Float 속성

: 레이아웃 흐름을 벗어나 요소를 띄우는 속성



[ 속성 값 ]

- none - float 시키지 않음. (default)
- left - 좌측으로 float 시킴
- right - 우측으로 float 시킴



[ 설명 ]

- 요소를 보통의 흐름에서 벗어나 띄어지게 한다.
- 주변 텍스트나 인라인 요소가 주위를 감싸는 특징이 있다.
- 대부분 요소의 display 값을 block으로 변경한다. (단, display 값 변경예외 : inline-table, flex 등)

<img src="https://raw.githubusercontent.com/kimkuan/TIL-Today-I-Learned/main/img/image-20210723000944460.png" alt="image-20210723000944460" width="60%" />



### Clear 속성

: 요소를 floating된 요소의 영향에서 벗어나게 하는 속성 



[ 속성 값 ]

* none - 양쪽으로 floating 요소를 허용. (default)
* left - 왼쪽으로 floating 요소를 허용하지 않음. 
* right - 오른쪽으로 floating 요소를 허용하지 않음. 
* both - 양쪽으로 floating 요소를 허용하지 않음. 



[ 설명 ]

* 항상 float 속성과 함께 따라다니는 속성!
* clear 속성은 **block level 요소에만** 적용이 가능하다.

<img src="https://raw.githubusercontent.com/kimkuan/TIL-Today-I-Learned/main/img/image-20210723000814116.png" alt="image-20210723000814116" width="60%"/>

```html
<div class="container">
    <div style="float: left">Box1</div>
    <div style="float: left">Box2</div>
</div>
<h2>주변 텍스트나 인라인요소가 주위를 감싸는 특징이 있음.</h2>
```

- 다음과 같이 float 속성은 주변 요소들에게도 영향을 미친다. 

- 따라서 float은 부모가 감싸는 영역에서만 적용될 수 있도록 clear 속성을 꼭 사용해주어야 한다.



<img src="https://raw.githubusercontent.com/kimkuan/TIL-Today-I-Learned/main/img/image-20210723000855713.png" alt="image-20210723000855713" width="60%" />

```html
<div class="container">
    <div style="float: left">Box1</div>
    <div style="float: left">Box2</div>
    <span style="display:block;clear=both;"></span> <!-- display는 block이어야함. 주의! -->
</div>
<h2>주변 텍스트나 인라인요소가 주위를 감싸는 특징이 있음.</h2>
```

