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



