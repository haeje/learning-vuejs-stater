# MVVM ( Model-View-ViewModel ) 패턴

데이터를 실제로 가공하는 Model과 데이터를 이용해서 클라이언트에게 화면을 제공하는 View를 분리하고 그 사이에 ViewModel이라는 인터페이스를 둔 설계.

예전에는 View에서 모든 코드 로직(이벤트 핸들링, 초기화, 데이터 모델)을 처리했다. UI와 데이터의 바인딩에 의존성이 생기는 등 협업 작업, 유지보수가 힘들었다. ViewModel이라는 인터페이스로 의존성을 낮추는 구조

![mvvm](https://user-images.githubusercontent.com/36914269/71615756-29b68c80-2bf6-11ea-8d5d-66111f960d84.png)

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

![컴포넌트통신1](https://user-images.githubusercontent.com/36914269/71615769-363ae500-2bf6-11ea-9d1b-99b90d800c39.png)

뷰 통신방식 : 처음엔 번거롭지만, 추적할 수 있다.

![컴포넌트통신2](https://user-images.githubusercontent.com/36914269/71615774-39ce6c00-2bf6-11ea-9c0c-4b8eba72a5b3.png)

### props 전달하기

<app-header v-bind:자식 프롭스 속성 이름="전달할 부모 데이터 이름"></app-header>

- 부모의 데이터가 변경되면 props 속성값도 바뀐다.

### event emit

# 같은 레벨 컴포넌트 간 통신

![같은레벨컴포넌트통신](https://user-images.githubusercontent.com/36914269/71615766-31763100-2bf6-11ea-85fb-08702f839b70.png)

1. 전달할 컴포넌트에서 이벤트 emit을 발생시킨다.
2. 상위 컴포넌트에서 이벤트를 수신한다.
3. props로 전달할 하위 컴포넌트에 연결시킨다.

# this

[https://www.w3schools.com/js/js_this.asp](https://www.w3schools.com/js/js_this.asp)

[https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8](https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8)?


---
# router

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

vue CDN 하위에 router 선언

### VueRouter 객체 연결

vue 객체에 연결시키고 사용할 수 있다.

    const router = new VueRouter();
    const app = new Vue({
    		el : '#app',
    		router : router
    })

### router 사용 예제

HTML 

    <div id="app">
            <router-view></router-view>
    </div>

vue 라우팅은 url에 따라 다른 컴포넌트를 보여주는 방식이다.  'route-view' 태그로 컴포넌트가 위치할 곳을 명시적으로 표현한다. 

JS

    	
      // url에 따른 컴포넌트 생성
      const LoginComponent = {
          template : "<div>login</div>"
      };
      const MainComponent = {
          template : "<div>main</div>"
      };
    
      // url에 따른 라우터 셋팅
      const routes = [
          { path : '/login', component : LoginComponent },
          { path : '/main', component : MainComponent }
      ]
    
    	// routes 정보를 바탕으로 VueRouter 객체 생성
      const router = new VueRouter({
          routes
      });
    
    	// VueRouter와 연결한 Vue 객체 생성
      const app = new Vue({
          el : '#app',
          router : router
      })
    
    

### router-link

각 라우트로 이동할 수 있는 링크를 제공한다. 

    <router-link to="/login">Login</router-link>
    <router-link to="/main">Main</router-link>

다음과 같이 a 태그로 렌더링 된다.

    <a href="/login"></a>

# Axios

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b28c4453-87d8-43b3-abb6-3fa14653b458/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b28c4453-87d8-43b3-abb6-3fa14653b458/Untitled.png)

ajax : 비동기적으로 http 통신

vue-resource : vue의 예전 공식 라이브러리

axios : vue에서 권고하는 비동기 통신 라이브러리

axios github : [https://github.com/axios/axios](https://github.com/axios/axios)

다음과 같은 형식으로 axios를 사용함

1. cdn 선언

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

2. axios 호출

    axiosReq : function(){
          const vm = this;
    
          axios.get('https://jsonplaceholder.typicode.com/users')
              .then(function (response) {
                  // handle success
                  console.log(response);
                  vm.users = response.data;
              })
              .catch(function (error) {
                  // handle error
                  console.log(error);
              })
              .finally(function () {
                  // always executed
              });
      }
    

get 매개변수로 데이터를 요청할 서버 url을 입력한다.

.then 함수 구현에서 데이터를 받아온 이후 동작을 구현한다.

.catch 함수 구현에서 err 발생 핸들링을 구현한다.

.finally 비동기 통신에서 마무리 작업할 일이 있다면 이 함수 구현체에 구현한다.