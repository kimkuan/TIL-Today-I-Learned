## Cookie


### 라이브러리

1. universal-cookies




### 사용법

#### 🍪Request Header에 쿠키 담아서 전송
1. client (Vue.js)
```javascript
// Request Header에 cookie를 담아서 전송 ({withCredentials: true} 설정을 통해 Cookie를 헤더에 담아서 전송 가능.)
store.dispatch('root/requestKakaoLogout', {withCredentials: true})
.then(function(result){
  alert("로그아웃 되었습니다.")
  // cookie 삭제
  cookies.remove('accessToken', { path : '/', sameSite : 'strict' })
  cookies.remove('refreshToken', { path : '/', sameSite : 'strict' })
  cookies.remove('userId', { path : '/', sameSite : 'strict' })
  store.commit('root/setLoginUserInfo', null);
  router.push({name : 'Main'})
})
.catch(function(err){
  alert("로그아웃에 실패하였습니다.")
  router.push({name : 'Main'})
})
```

2. server (Spring Boot)
```java
@GetMapping(value="/logout")
public ResponseEntity<String> logout(@CookieValue(value="accessToken", required = false) Cookie access_Token){
    // @CookieValue 어노테이션을 통해 쿠키를 꺼내올 수 있다. (required 속성이 true이면, 해당 쿠키가 존재하지 않을 때, Exception이 발생한다)
    if(access_Token == null)
        return ResponseEntity.ok("토큰이 유효하지 않습니다.");

    System.out.println("logout accessToken : " + access_Token.getValue());
    kakaoAPI.Logout(access_Token.getValue());
    return ResponseEntity.ok("로그아웃 되었습니다.");
}
```

