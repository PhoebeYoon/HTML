##### 🍑  Front_End 과정 1단계 

## @property

자바스크립트를 실행할 필요없이 스타일 시트에서 직접 사용자 지정 속성 등록을 나타낸다. 유효한 @property 규칙은 자바스크립트의 registerProperty()가 동등한 매개변수로 호출된 것처럼 사용자 정의 속성을 등록한다.

@property는 CSS 변수의 초기값, 유형를 선언할 수 있다.  
```html  
   <style>
@property --primary {
 syntax: '<color>';
 initial-value: orange;
 inherits: true;
}
.text-1 { color:var(--primary)}
.text-2 { 
      /* --primary:green;  */ --primary:#ff;
     color:var(--primary)}

    </style>
<h3>1. CSS 변수 사용하기</h3>
<span class="text-1">CSS Variables</span><br/><br/>

<h3>2. 변수에 타입에 맞지 않는 값을 할당한 경우, 초기값을 사용</h3>
<span class="text-2">CSS Variables</span>

```
위의 예제에서  .text-2에 --primary를 다시 green으로 정의하며 재 정의된 색상으로 나오지만 타입에 맞지 않게 (#ff) 으로 정의되면 기존에 정의된 내용을 실행한다.  

만약에 위의 내용을 자바스크립트로 사용하고자 한다면 registerProperty() 를 사용하면 된다.  
```
window.CSS.registerProperty({
   name: '--primary',       // 변수명
  syntax: '<color>',       // 변수값 유형
  initialValue: 'orange',  // 초기값
  inherits: true,         
})
```
위의 예제를 다시 적으면, 
```html
    <style>
.text-1 { color:var(--primary)}
.text-2 {  --primary:green;  color:var(--primary)}
    </style>
<h3>1. CSS 변수 사용하기</h3>
<span class="text-1">CSS Variables</span><br/><br/>

<h3>2. 변수에 타입에 맞지 않는 값을 할당한 경우, 초기값을 사용</h3>
<span class="text-2">CSS Variables</span>
<script>
    window.CSS.registerProperty({
    name:'--primary',
    syntax :'<color>',
    initialValue:'blue',
    inherits: true
    })
</script>
```
또한,
자바스크립트에서 정의한 값을  재정의해서 사용하고자 한다면 스타일에서 값을 바꾸어야 한다.   
``` document.documentElement.style.setProperty('--primary','gold')```    
주의할 것은 CSS.registerProperty()가 아니라 .style.setProperty() 이다.  
또한 위에서 바꾼 내용은 document.documentElement에서 스타일을 바꾼것이기 때문에 초기값이 바뀐것이다.  
만약 특정한 엘리먼트에서 값을 바꿀 수도 있다. 
```
document.documentElement.style.setProperty('--primary','magenta');
document.querySelector('.text-2').style.setProperty('--primary','gold');
```  
이렇게하면 초기값은 마젠타로, .text-2 클래스는 골드색으로 바뀐다.   

### getComputedStyle(), getPropertyValue()   
자바스크립트에서 따로 설정한 값을 가져오는 것을 시도해보자. 우선 접근은 getComputedStyle()로 하고 특정값을 가져올때는  getPropertyValue()이다. 

위의 코드에 
```js
  window.CSS.registerProperty({
    name:'--primary',
    syntax :'<color>',
    initialValue:'blue',
    inherits: true
    })
document.documentElement.style.setProperty('--primary','magenta');
document.querySelector('.text-2').style.setProperty('--primary','gold');

const result1= getComputedStyle(document.querySelector('.text-1'))
console.log(result1)  // 적용된 스타일전체를 보여준다 

console.log(result1.getPropertyValue('--primary')) // --primary값만 출력하여 rgb(255, 0, 255)

const result2= getComputedStyle(document.querySelector('.text-2'))
console.log(result2.getPropertyValue('--primary')) // .text-2의 색상인 rgb(255, 215, 0) 출력
```

### Syntax 에는  
```
syntax: "<color>" ,"<number>","<percentage>","<length>",
 "<image>", "<url>","<integer>","<angle>", "<time>"
 "<resolution>", "<transform-function>","<custom-ident>"
"<transform-list>" 
```

> "transform-function"으로는 , matrix(),perspective(),rotate(),scale(),translate() 등등

> "custom-ident" 으로는, CSS data type은 식별자로 사용되는 임의의 사용자 정의 문자열을 의미하며 대소문자를 구분한다 


[다른 예]
```html
    <style>
 @property --colorPrimary {
  syntax: "<color>";
  initial-value: magenta;
  inherits: false;
}
@property --stop {
  syntax: "<percentage>";
  initial-value: 50%;
  inherits: false;
}
body { background-color: var(--colorPrimary);
    display: grid;  
    grid-template-columns: 50% 50%;  margin: 0;
    }
div{ height: 100vh; 
background-image: 
   linear-gradient(to bottom, black, var(--stop), var(--colorPrimary));}
div:hover { --colorPrimary : red; --stop:10%}
    </style>
</head>
<div></div>
<div></div>
```

###   inherits: false / true 만
```html
<style>
@property --item-size {
  syntax: "<percentage>";
  inherits: true;
  initial-value: 40%;
}
.container {
  display: flex;
  height: 200px;
  border: 1px dashed black;

  /* set custom property values on parent */
  --item-size: 20%;
  --item-color: orange;
}

/* use custom properties to set item size and background color */
.item {
  width: var(--item-size);
  height: var(--item-size);
  background-color: var(--item-color);
}

/* set custom property values on element itself */
.two {
  --item-size: initial;
  --item-color: inherit;
}

.three {
  /* invalid values */
  --item-size: 1000px;
  --item-color: inherit;
}

    </style>
<div class="container">
    <div class="item one">Item one</div>
    <div class="item two">Item two</div>
    <div class="item three">Item three</div>
  </div>
<script>
window.CSS.registerProperty({
  name: "--item-color",
  syntax: "<color>",
  inherits: false,
  initialValue: "aqua",
});
</script>
```







