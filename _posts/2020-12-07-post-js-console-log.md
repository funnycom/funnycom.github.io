---
title: "자바스크립트 - console.log"
last_modified_at: 2020-12-07T16:50
categories: 
  - Javascript
tags:
  - webdev
  - javascript
  - beginner
  - console

---

# console.log 활용하기

자바스크립트를 처음 공부할 때 콘솔 창에서 결괏값을 확인하기 위해 소스 코드에 많이 사용하는 명령이 console.log()일 것입니다. 
console 객체의 log() 메서드라는 복잡한 관계를 몰라도, 그냥 console.log() 라는 명령만으로 뭔가 결과를 볼 수 있다는 뿌듯함에 이것저것 괄호 안에 넣어보기도 하죠.

이제 console.log() 명령을 다양하게 사용하는 방법을 알아보겠습니다. 


## console.log()에서 스타일 적용하기 

흔히 콘솔 창에는 텍스트만 표시하곤 하는데, %c와 함께 표시할 내용을 지정하고, css 속성과 속성값을 따로 지정하면 css 스타일을 적용할 수 있습니다.

```
console.log("%c 테스트용 텍스트", "color:blue; background-color:lime; font-size:1.5em;")
```

![assets/images/12-07-1.png]


## console.log() 에서 문자열 대체하기

console.log()에서 대체 문자열을 대체할 수도 있습니다. 문자열이라면 %s를, 숫자라면 %d(실수일 경우 %f)를, 객체라면 %o를 사용합니다. 

```
for (let i = 0; i <= 5; i++) {
    console.log("Hey, %s. This %d times. ", "YOU", i+1);
}
```

![assets/images/12-07-2.png]

 ***참고*** *대체 문자열 대신 직접 연결할 수도 있습니다*

```
var you = "YOU";
for (let i = 0; i <= 5; i++) {
    console.log("Hey, ", you, "This ", i+1, " times. " );
}
```

![assets/images/12-07-3.png]
