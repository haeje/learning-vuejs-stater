# MVVM ( Model-View-ViewModel ) 패턴

데이터를 실제로 가공하는 Model과 데이터를 이용해서 클라이언트에게 화면을 제공하는 View를 분리하고 그 사이에 ViewModel이라는 인터페이스를 둔 설계.

예전에는 View에서 모든 코드 로직(이벤트 핸들링, 초기화, 데이터 모델)을 처리했다. UI와 데이터의 바인딩에 의존성이 생기는 등 협업 작업, 유지보수가 힘들었다. ViewModel이라는 인터페이스로 의존성을 낮추는 구조

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8f0d531c-d823-40d4-805b-08111f70f62b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8f0d531c-d823-40d4-805b-08111f70f62b/Untitled.png)

DOM에서 이벤트가 발생하면, DOM Listeners가 듣고 데이터가 변경에 따라 모델의 값을 바꾼다. 변경된 데이터 모델을 Data Bindings를 이용하여 view를 변경함.

# 기존 방식

화면에 데이터 변경을 코딩할 때, 문자를 수정하고 직접 dom의 속성으로 지정해야함.

    <body>
        <div id="app"></div>
    
        <script>
            var app = document.querySelector('#app');
            var str = "hello";
            app.innerHTML = str;
            str = "hello!!!";
    
    				// 다음과 같이 변경된 데이터와 UI를 동기화시켜야함
            app.innerHTML = str; 
        </script>
    </body>

# Vue방식

    <script>
            var app = document.querySelector('#app');
            var vueModel = {};
    
            Object.defineProperty(vueModel, 'str', {
                get : function(){
                    console.log('str 접근');
                },
                set : function(newValue){
                    console.log('할당', newValue);
                    app.innerHTML = newValue;
                }
                });
    
    				//데이터를 변경시킬 일이 있으면, 데이터만 변경시키면 됨. => UI 의존성이 낮아짐
            vueModel.str = "hello"
        </script>

Object.defineProperty : 특정 객체의 속성을 재정의 할 수 있음. 예제에서는 'vueModel' 객체의 get, set 메서드를 재정의 함. set 메서드가 호출되면 변경된 값을 UI에 바인딩되도록 재정의 하였음.

## 라이브러리화

클로저 

[https://hyunseob.github.io/2016/08/30/javascript-closure/](https://hyunseob.github.io/2016/08/30/javascript-closure/)

settimeout 등 클로저의 활용에 대해 공부할 필요가 있음

즉시실행함수

즉시실행함수에서 실행되는 코드들은 또 다른 스코프에서 실행되고 변수의 유효범위를 분리할 수 있다. 많은 오픈소스 라이브러리에서 사용되고 있다.

일회성으로 잠깐 사용할 변수를 전역에 선언해놓는 등 전역스코프의 오염을 막을 수 있다. 

[https://velog.io/@doondoony/javascript-iife](https://velog.io/@doondoony/javascript-iife)

[https://developer.mozilla.org/ko/docs/Glossary/IIFE](https://developer.mozilla.org/ko/docs/Glossary/IIFE)

    (function(){
                function init(){
                        Object.defineProperty(vueModel, 'str', {
                        get : function(){
                            console.log('str 접근');
                        },
                        set : function(newValue){
                            console.log('할당', newValue);
                            render(newValue);
                        }
                        });
                    }
    
                function render(newValue){
                    app.innerHTML = newValue;
                }
    
                init();
            })();

# 뷰 인스턴스

사람들이 기능, 속성을 재사용할 수 있도록 Vue 객체를 만들었음.

    function Vue(){
    	this.logText(){
    		console.log('hello');
    	}
    }

    var vm = new Vue();
    vm.logText();
    //hello

Vue 객체에는 이렇게 많은 기능들이 정의되어 있음.

Vue 객체를 생성하면서 설정할 수 있는 옵션은 다음과 같다.

    new Vue({
      el: ,
      template: ,
      data: ,
      methods: ,
      created: ,
      watch: ,
    });

# 뷰 컴포넌트

화면의 영역을 구분해서 개발하는 방법, 재사용성

### 전역 컴포넌트

다른 뷰 인스턴스에서도 사용가능, 하지만 대부분 하나의 인스턴스를 사용하기 때문에 대부분 지역컴포넌트 사용.

라이브러리 등 공통적으로 사용되는 컴포넌트만 

### 지역 컴포넌트

거의 대부분의 경우는 지역, 하위 컴포넌트를 devtool로 확인하며 개발 가능.

# 컴포넌트 통신

하위 컴포넌트로는 props

상위 컴포넌트로는 event

기존 : 데이터가 변경되고 다른 컴포넌트에 영향을 줄 때, 추적하기가 어렵다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ce7c14ba-fa24-4d9f-ab73-d55a89d15db5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ce7c14ba-fa24-4d9f-ab73-d55a89d15db5/Untitled.png)

뷰 통신방식 : 처음엔 번거롭지만, 추적할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/81cddf59-0814-45fd-8421-ae99a4a834e9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/81cddf59-0814-45fd-8421-ae99a4a834e9/Untitled.png)

### props 전달하기

<app-header v-bind:자식 프롭스 속성 이름="전달할 부모 데이터 이름"></app-header>

- 부모의 데이터가 변경되면 props 속성값도 바뀐다.

### event emit

# 같은 레벨 컴포넌트 간 통신

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06547f43-b6b9-4aa5-a6e5-bedabf627b21/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06547f43-b6b9-4aa5-a6e5-bedabf627b21/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9a1379f0-12df-494a-a0c0-6dbda466d76a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9a1379f0-12df-494a-a0c0-6dbda466d76a/Untitled.png)

1. 전달할 컴포넌트에서 이벤트 emit을 발생시킨다.
2. 상위 컴포넌트에서 이벤트를 수신한다.
3. props로 전달할 하위 컴포넌트에 연결시킨다.

# this

[https://www.w3schools.com/js/js_this.asp](https://www.w3schools.com/js/js_this.asp)

[https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8](https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8)?
