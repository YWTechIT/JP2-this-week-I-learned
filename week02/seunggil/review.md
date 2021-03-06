# 이번주 리뷰
<br>


## 리액트에서 자주쓰는 if문 작성패턴 5개

1. 컴포넌트 안에서 쓰는 if/else 

```jsx

function Component() {
    if ( true ) {
        return <p> 참이면 보여주는 HTML</p>;
    } else {
        return null;
    }
}

```

<div>내부에서 if문을 사용할수 없으므로

보통 return + JSX 전체를 퉤 뱉는 if문으로 컴포넌트를 짜면된다.

2. JSX안에서 쓰는 삼항연산자  
줄맞추는게 약간 관습적인게 있다는것을 알게된거같다.

```jsx

function Component() {
    return (
        <div>
            {
             1 === 1
             ? <p> 참이면 보여줄 HTML </p>   
             : null   
            }
        </div>
    )
}

```

약간 이런모양이 기본룰인데 자세히보면

조건  
? 참일때  
: 거짓일때  

이런식으로 보여지니까 가독성이 좋은거같다 (마치 if/else문 처럼 보이는거같기도..)

> 3항연산자는 중첩사용도 된다. 소괄호로 묶어서 사용하면된다.


3. && 연산자로 if 역할 대신하기

흔히 아는 AND 게이트 ,OR 게이트 같은느낌인데,

둘다 true여야 전체가 true

or은 둘중하나라도 true이면 true

자주보던 진리표 이런것도 있긴하지만 pass~

`true && '안녕';`  
`false && '안녕';`

위에는 '안녕'이 자리에남고  
아래는 false가 자리에 남는다.

[자세한 내용 MDN 문서 참조](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/%EB%85%BC%EB%A6%AC_%EC%97%B0%EC%82%B0%EC%9E%90(Logical_Operators))


코드의 길이를 단축하는데 도움은 되겠지만.. 당장에는 알필요가 있을까 ?

일단 넘어가자, 내용이 산으로 가는거같으니..

4. switch / case 조건문

js에서도 기본적으로 switch/case 문을 사용할수는 있다.

reducer에서 하나하나 if문으로 만들어줬던 내가 사용하고자하는 함수?

그런거를 사실 좀더 좋은 조건문은 switch 문이지 않나 생각한다

갯수가 많을경우 if문을 써서 만들어져있다면 위에서부터 순차적으로내려가면서 체크하니까 리소스낭비가 심할것이다.

5. 오브젝트 자료형을 응용한 enum 

```jsx

function Component() {
    var 현재상태 = 'info';
    return (
    <div>
        {
            {
                info : <p>상품정보</p>,
                shipping : <p>배송관련</p>,
                refund : <p>환불약관</p>
            }[현재상태]
        }
        
    </div>
    )
}

```

약간 이런식인데 , 현재상태를 변수로 지정하고

딕셔너리 자료형 꺼내듯이 꺼내쓰는느낌인것이다. 첫 대괄호 {} 는 jsx문법이 내부에 쓰여진다는것을 나타내고 , 내부에 두번째 대활호 {} 는 오브젝트자료형이다 라는 의미 이다. 대괄호로 내부값을 꺼내는것이다.

> 예제에서는 편의를 위해 var를 썻지만 state를 써야할것이다.

## 성능잡기1 . lazy loading / React devtools

arrow function등 이름없는 함수들을 많이사용하는데 ( 보통 onClick 이벤트나 등등..)

윗쪽에서 그냥 선언해서 쓰자.

style도 넣을수있는데 이때 object형식으로 넣어야했다.

근데 이것도 그냥 위에서 object형을 선언해서 밑에서 사용하는식이 좋다.

왜? -> 메모리할당때문에? -> 콜백함수, 이름없는 오브젝트등 이런것은

재렌더링되거나 버튼누를때 새로 메모리에 할당된다. 이때 비효율적이기 때문에

먼저 선언하면 성능적인 이점이 있을수 있다.

애니메이션 막주지말고 , 레이아웃에 관한 margin, width, padding 등..브라우저가 렌더링을 힘들어한다. ( transform 이 제일 무난)


app.js 상단에 컴포넌트들 import 할때

```jsx
import Data from './data.js';
import Detail from './detail.js';
... 등등
```

이런식으로 컴포넌트양이 많고하면 app.js 방문할때 미리 다 로드하니까 부담이될수 있다. 그래서? -> lazy loading 이다.

`import Cart from './Cart.js';` 이런것도 결국엔 Cart를 가야 의미있는것인데 미리 로딩하면 부담만 커지는것이다. ( 더많은 컴포넌트들이 있겟지만 )

꼭 필요해진 시점에서 import 시키게 할수있다. -> lazy loading

```jsx

import React, { useState, useContext, lazy, Suspense} from 'react';

```

lazy와 Suspenses는 세트로 같이 묶여서 사용된다.


```jsx

let Detail = lazy(()=>{return import('./Detail.js')});
// ES6 다이나믹 임포트 문법이다. 이렇게하면 import를 다이나믹하게 할수있다. 천천히 로딩을 할수가있다. Detail 컴포넌트가 필요해질때? 그때 사용한다.
//arrow function -> 리턴이 한개이면 중괄호와 리턴 생략가능
// lazy(()=> import('./Detail.js'));

```

그럼 서스펜스는 ?

그러면 <Detail>컴포넌트를 찾아서 <Suspense> 태그로 감싸줘야한다.

```jsx
//로딩전까지 보여주는 사이트
<Suspense fallback={<div>로딩중이에요</div>}> 
    <Detail props들/>
</Suspense>


```

로딩중인거 어떻게보나??

> React Dev Tools 크롬 확장 프로그램

일반적으로 크롬 개발자도구로는 html 태그들만 보이지

컴포넌트단위로는 볼수가없다. 근데 react dev tools 를 쓰게되면

컴포넌트단위에서의 변수등 모두 볼수있고

컴포넌트탭에서 오른쪽상단에 위치한 타이머버튼을 누르면

해당 컴포넌트의 랜더링을 잠깐 정지해주세요 하는 효과가 있다.

profiler ? 이건 뭐지? 렌더링시간? 을 체크해준다.

녹화하고 페이지 이동해본다음에 다시 중지하면

내가 왔다갔다 했을때 로딩되거나 , 재 렌더링된 컴포넌트들을 다 표시해준다.

어떤 컴포넌트가 시간을 많이 잡아먹는가? 특정기능에서 컴포넌트가 너무많이 재 렌더링될때 불필요한 컴포넌트를 빼내야할때도 체크해볼수있다.


# 지난주 리뷰
## 쿠키(cookie) 알아보기

사용자가 네이버에 한번 갔다오면 html을 받아오고, " 너 브라우저에 장바구니 정보를 (상품을) 저장해 "

서버에서 브라우저로 작은 정보파일을 제공한다.

누가 셋팅해주냐? -> 서버가 ( 너그거 저장해! )

client가 GET요청 하면 Server에서 SET Cookie 값으로 userId=toy 이런식으로 키,밸류를 한쌍을 준다.

그 이후에 요청들은 Cookie: userId=toy가 항상 실려서 보내진다.

---

header에 가지고있는 cookie를 모두 실어서 보낸다.

클라이언트에서 변경할수있다.

보안의 취약점이 있다 -> 그래서 유효기간이 있다. (Expires) (팝업창에 오늘만열기 등등..)

flask가됫건 Django가 됬던 Set-Cookie 하는방법이 있다. 그때 찾아보면될것.

### 라이프타임에 따른 쿠키 종류
1. session cookie
    - 현재 세션이 끝날때(브라우저가 종료될때) 삭제되는 쿠기

2. permanent cookie (영구적인? 쿠키)
    - Expires 속성에 명시된 날짜에 삭제되거나, Max-Age속성에 명시된 기간 이후에 삭제되는 쿠기
    `Set-Cookie : id=a3fWa; Expires=Wed,31 Oct 2021 07:28:00 GMT;`

쿠키는 JS에서도 접근이 가능하다.

쿠키 사용예시

- 로그인 안한 상태에서 물건을 장바구니에 담기
- 웹페이지 언어 설정 저장(영어,한글..) , 테마들(라이트,다크)
- 아이디 자동완성
- 공지 메시지 하루 안보기

## HTTP 세션(session) 알아보기

__쿠키를 기반으로__ 작동하고있지만 서버측에서 관리한다.

> session cookie와 HTTP session은 다르다. -> 차이를 이해하자.

특징
- 서버에서 클라이언트 구분을위해서 세션ID를 부여 ( 인증상태를 유지, 재로그인 필요없음 )
    클라이언트에서 서버로 겟요청하면 `SET Cookie: SESSIONID = qwe3121`을 서버에서 내려준다.
    다른 요청을할때 이제 계속 서버로실어서 보낸다. 다만 서버단에서 우리가보내준값이맞는가? 확인한다.

- 보안면에서 쿠키보다 우수, 사용자많아지면 서버메모리 많이차지, 성능저하 요인

- 세션 ID로 클라이언트를 구분해서 클라이언트의 요구에 맞는 서비스를 제공 (로그인 했는지 안했는지)

예) 로그인을했다. ID,PW -> POST method로 보낸다. 서버가 받아서  DB갔다온다. 우리유저가 맞네? sessionID하나 만들어서 발급해줘야지~ 하고 내려준다.

맨처음 인증은 아이디 비밀번호로 유저를확인하고 그뒤로 사용하는사람은 세션이 있으므로 그때부터는 같은사람이구나~ 하고 서비스를 이용할수 있겠구나.

> 쿠키에 HTTP Only 라는 옵션이 있다. 이러면 JS에서 접근이 불가능하다. (범위 외의 내용) 이것으로 회피 가능 session cookie의 탈취를 막는 두가지 : HTTP Only, Secure 옵션

key,value 저장하기에 적합한 DB가 있다. = redis ( 왜? 자주 접근해야하는데 너무 빈번하니까 그것만 처리하는 DB가 더 효율적이니까.)

## JWT


Self-contained (자가 수용적) -> 엄청 많은 데이터를 담고있어서 이렇게 얘기한다.

- 데이터를 임의변경할경우 JWT를 처음 내려서 쓸때 주어진 secret에 의해서 signature가 인정되지 않는다.

> 서버에 JWT가 보내어지면 우리가 예전에 주었던 JWT가 맞는지 시크릿으로 먼저 합당한 변경되지않은 JWT인지 확인하고

> 그이후에 payload에 올려진 데이터들의 값을 확인하는 절차가 이루어진다.

Payload

- 토큰에 담을 정보가 들어있다.
- 여기에 담는 정보의 한'조각'을 클레임(claim)이라고 부르며, 이는 name/value의 한 쌍으로 이뤄져 있다.
- 클레임은 크게 3가지 / 등록된 클레임(Registered claims) / 공개된 클레임 (Public claims) / 비공개 클레임 (Private claims)

Signature

- 서명은 헤더의 인코딩값과 정보(payload)의 인코딩값을 합친후 주어진 secret으로 해쉬를 하여 생성
- 서명은 메시지가 중간에 변경되지 않았다는것을 검증하며, JWT를 보낸 사람이 신뢰할 수 있는 대상임을 확인 할 수 있음

[변조방지에 대해 자세히 알고 싶다면?](http://bcho.tistory.com/807)

Token : 유저가 ID/PW를 옳게 입력하면 서버에서 내려주는 하나의 증거물 / 너는 인증된 사람이다. 를 증명하는 하나의 민증같은느낌?
- 중복되지 않는 하나의 String

---

### 토큰 인증 방식

> 설명에넌 JWT를 쓰지않겠다. 일반 토큰 !! = 어떤 유니크한 값의 스트링

1. Session 기반 Token 인증
    1. API클라이언트(브라우저)가 id/pw들고 서버로 토큰발급을 요청
    2. 서버에서 계정확인후 토큰생성 (임의의값)하고, 토큰에 대한 정보를 토큰저장소에 저장합니다. 
    3. 생성된 토큰을 API로 브라우저에 내려줍니다. (주로 cookie , json응답에 실어서 보냄)
    4. 발급받을 Access토큰을 함께 API호출합니다 (authorization Header 혹은 cookie 사용)
    5. 토큰저장소의 값과 확인한 후에 유저에게 권한들을 열어준다. (구매,결제 등등..)
    6. 정상적읜 고객임을 확인하고 API에서 응답을 내려줍니다. (결제완료등)
2. JWT 기반 Token 인증
    1. api클라이언트가 id/pw넣고 서버로 jwt 토큰 을 요청
    2. 토큰에 연관되는 정보,권한등 토큰 자체에 넣어서 토큰을 생성하고 API클라이언트(브라우저)에 내려준다 (cookie,json)
    3. api클라이언트에서는 그것을 어딘가에 저장한다 ( 로컬스토리지나 등등..) 
    4. 이제 어떤 요청을 할때 토큰을 들고 서버에 요청을하는데 이때 서버에서는 secret을 가지고 변조되었는지 디코딩을 해서 확인한다. -> 변조된게 아니구나, 유효하구나 우리가만들어준거구나. 그 내용,정보들이 유효하다. -> 결제 정보 내려주자
3. 두가지를 다 사용한 Token 인증


토큰은 언제언제 검사하는가?
- middleware로써 서버로치면 route될때 내부요소로 authentication(인증) 을줘서 주기적으로 토큰을 확인하게끔 할수있다.

session 기반 token에서 누군가 access_token을 탈취당한거같다?
- db의 토큰을 지워버리면 된다.

JWT 항상 좋은가?
- 만료기간을 잘못조정하고 그러면 한번털려서 계속 정보를 털릴수 있다.
- 만료기간을 짧게주면 되잖아?
    - 그러면 사용자가 로그인을 계속 자주 해야해.. 불편
        - 요즘 유저들한테 로그인하라고 자주 권한다? 좋은서비스가 아니다.
            - 해결방법은 : session과 JWT 를 적절하게 이용하면 refresh 토큰 이라는것을 저장해놓고 ..  현존 보안방식중에 왠만한거 다막는 최종 끝판왕~
            - [refresh토큰 더 알아보기](https://blog.ull.im/engineering/2019/02/07/jwt-strategy.html)

- 강제 로그아웃이 안돼~

session기반의 인증은 서버를 여러개 쓰면, 토큰저장소는 확장성이 약하다. 부하를 받게된다. (server의 scale up은 ok, token db는 안됀다.)

jwt는 상대적으로 부하가 작다.

session도 CORS의 문제가있다..음 /

인x파크 개인정보 유출건 => admin의 session이 털렸다.

## OAuth

간단하게 -> third party (페북,구글,카카오)를 통해서 인증

OAuth는 제3자가 생기면서 3명이서 인증절차를 나누는것이다.

그사람이 인증되있어 (Authentication) 그래서 인가(Authorization) 권한을 내려줬어

요즘은 인가가 거의없다 (옛날에 facebook에서 포스팅권한을 인가 해준적이 있엇다.)

다른 큰 서비스에서 인증된정보는 신뢰할수있다고 생각하고 받아서 쓰는느낌 ?

요즘 트렌드 -> Passwordless -> medium에서 시행하고있는 서비스 (책임을 third party애들을 믿고 전가하는느낌) -> 해외 추세

email인증 -> 이메일로 보내줌 -> 열면 그안에 버튼같은 UI가 있다면 거기서 암호화된 token을 GET요청일까? 아마도 redirect시켜서 보내주는것, 얘는 이 메일의 주인이 맞나봐~ -> 브라우저가 그걸 session을 저장을한다.

### Access Token 발급받기

브라우저가 로그인요청 -> 페북서버로 -> 맞네? 액세스토큰내려줌 -> 그러면 facebook으로부터 data를 받을수있다.

브라우저가 가지고있는 AccessToken을 우리서버로 보내서 -> 우리서버에서 facebook 서버로부터 data를 받으면되는거아닌가~? (email,이름 등등..~)

- facebook에서 제공하는 팝업은 내서버에서는 모른다~~ 그래서 브라우저가 가진 AccessToken을 내서버로 가져와서 해결하자~



1. Access Token 서버로 건내주기


- 그러면 AccessToken을 어떻게 어떤방식으로 받을래?


    직접 입력하는방식 ex) 휴대폰인증
    1. 우리서비스에서 인증을해야해 예를들어 pass라는서비스에 본인인증을 해줘~
    2. 그래 그럼 핸드폰으로 access키(인증번호)를 보내줬어
    3. 그것을 브라우저 html= 클라이언트가 적으면 그것을 서버로 보내줘
    4. 서버는 그 액세스키가 맞는지 pass에 물어봐
    5. 맞네~

        문제점 : access토큰이 클라이언트에서 서버로 들어가는것이다보니까 조작될수있고, 인증절차가 번거로운(?)편이다.

    Redirect방식 ex)Oauth  / html에서 링크태그로 다른사이트로 보낼수있듯이, 서버에서도 다른사이트로 보낼수있다.그것을 리다이렉트라고한다.
    1. 브라우저가 페이스북 로그인페이지로 이동(아이디/패스워드입력) -> 액세스토큰을 브라우저가 받는게~~~아니라
    2. url에 심어서 (accessToken을) redirect시킨다 우리사이트로~ -> 그뒤에 액세스 토큰달려있고
    3. 토큰뜯어서 우리서버에 저장하고 로그인완료 페이지 보여줘야지~!

        -> 페이스북은 우리서버로 redirect시킬때 주소를 어떻게알까?
        - 초기에 페이스북에 등록절차가 있다. 그때 redirect_url을 미리 약속한다.
        - 약속되지않은(변조된) url의경우 에러를 발생시킨다.


2. 우리서버의 AccessToken을 내려주자

    이제 우리서버에 third party의 AccessToken이 있어서 facebook같은곳에서 정보를 요청해서 받아올수있다.

    그러면 ? 우리 서버에서 처리할수 있다~!!

    신규가입이면 DB하나 생성해서 유저정보 담고 우리서비스의 token을 생성해서 내려준다.
    로그인의경우 기존에 있는 DB에서부터의 token을 생성해서 내려준다.



참고) AuthO 라고 이런 OAuth를 처리해주는 서비스가 있다~~!






# 튜터에게 궁금한 점
궁금한점 작성 공간!

1. if문의 길이를 단축시키는 &&과 같은것을 잘 알고 잘 쓸줄알아야할까요?

2. 토큰은 파일인가요? 브라우저 저장소에 저장이되는건가요?

3. JWT 말고 일반 토큰이라 칭해지는것도 header와 signature를 가진형태인지, 다른형태이더라도 암호화가 되어있는것인지?
