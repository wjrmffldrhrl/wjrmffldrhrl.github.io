---
title:  "JWT with Spring"
categories:
  - Spring
tags:
  - JWT
  - spring
  - spring-boot
  - java
header:
  teaser: /assets/images/study/jwt/title.png
  image: /assets/images/study/jwt/title.png
---  

JWT와 Spring 환경의 Web Server가 어떻게 동작하는지 알아보자.

# 동작 과정

![protocol]({{ site.baseurl }}/assets/images/study/jwt_with_spring/protocol.png) 

1. 클라이언트가 로그인요청을 보낸다.
    - POST 방식으로 id(email)와 pw를 `/authenticate`에 요청한다.
2. 서버는 id와 pw로 회원 정보를 조회하고 맞다면 JWT를 반환한다.
3. 클라이언트는 JWT를 로컬에 저장한다.
4. 이후 클라이언트는 서버에 요청을 보낼 때 마다 헤더에 Token을 포함시킨다.
5. 서버는 요청을 받을 때 마다 Token이 유요한지 검증한다.
    - Token이 검증되면 따로 id와 pw를 검사하지 않아도 사용자 식별이 가능하다.
6. 검증 결과에 맞게 응답한다.

# 실제 동작 과정

## 1. 클라이언트 로그인 요청

![ex1]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex1.png)

`/authenticate`로 POST요청을 보낸다. 이 때 Request Body에는 JSON형태로 사용자의 email과 password를 보낸다.

## 2. 서버 회원 정보 조회 및 JWT 반환

WebSecurityConfigurerAdapter를 상속받은 WebSecurityConfig에서 `/authenticate`는 인증되지 않은 사용자도 접근 가능하게 한다.

### WebSecurityConfig.java

```java
@Override
protected void configure(HttpSecurity httpSecurity) throws Exception {
        // For CORS error
        httpSecurity.cors().configurationSource(request -> new CorsConfiguration().applyPermitDefaultValues());
        // We don't need CSRF for this example
        httpSecurity.csrf().disable()
            // dont authenticate this particular request
            .authorizeRequests().antMatchers("/authenticate").permitAll().
            // all other requests need to be authenticated
                anyRequest().authenticated().and().
            
            // stateless session exceptionHandling().authenticationEntryPoint(jwtAuthenticationEntryPoint).and().sessionManagement()
                exceptionHandling().authenticationEntryPoint(jwtAuthenticationEntryPoint).and().sessionManagement()
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        // Add a filter to validate the tokens with every request
        httpSecurity.addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class);
    }
```

이제 사용자의 요청이 Controller에 의해 처리된다.

### JwtAuthenticationController.java

```java
// work with jwtUserDetailsService
    private void authenticate(String email, String password) throws Exception {
        try {
            authenticationManager.authenticate(
									new UsernamePasswordAuthenticationToken(email, password));
        } catch (DisabledException e) {
            throw new Exception("USER_DISABLED", e);
        } catch (BadCredentialsException e) {
            throw new Exception("INVALID_CREDENTIALS", e);
        }
    }

    // Front에서 들어오는 회원 인증 요청
    // JwtRequest내부에 email, password가 맵핑되어 들어온다
    @RequestMapping(value = "/authenticate", method = RequestMethod.POST)
    public ResponseEntity<?> createAuthenticationToken(
            @RequestBody JwtRequest authenticationRequest) throws Exception {
           
        // authenticationManager        
        authenticate(authenticationRequest.getEmail(), authenticationRequest.getPassword());

        // 인증된 유저 객체 추출
        final UserDetails userDetails = userDetailsService
            .loadUserByUsername(authenticationRequest.getEmail());

        // 추출된 유저 정보로 토큰 생성
        final String token = jwtTokenUtil.generateToken(userDetails);

        //status 값과 함께 body(token) 전송
        return ResponseEntity.ok(new JwtResponse(token));
    }
```

해당 Controller에서는 authenticationManager에 의해  사용자 email과 인코딩된 비밀번호를 조회한다. authenticationManager는 WebSecurityConfig에서 설정되었으며 해당 사용자가 DB에 존재한다면 UserDetailsService로 인증된 사용자 객체를 사용하여 Token을 생성한 뒤 반환한다.

### UserInfo DB

![ex2]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex2.png)

### UserDetailsService

```java
public class JwtUserDetailsService implements UserDetailsService {

    private final UserInfoRepository userInfoRepository;

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {

        UserInfo userInfo = userInfoRepository.findByEmail(email);

        if(userInfo == null){
            throw new UsernameNotFoundException("User not found with email: " + email);
        } else {
            return new UserInfoDetails(userInfo);
        }
    }
}
```

### JwtTokenUtil.java - generate Token

```java
//while creating the token -
    //1. Define  claims of the token, like Issuer, Expiration, Subject, and the ID
    //2. Sign the JWT using the HS512 algorithm and secret key.
    //3. According to JWS Compact Serialization(https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41#section-3.1)
    //   compaction of the JWT to a URL-safe string
    private String doGenerateToken(Map<String, Object> claims, String subject) {
        return Jwts.builder().setClaims(claims).setSubject(subject).setIssuedAt(new Date(System.currentTimeMillis()))
            .setExpiration(new Date(System.currentTimeMillis() + JWT_TOKEN_VALIDITY * 1000))
            
            .signWith(SignatureAlgorithm.HS512, secret).compact();
    }
```

Token을 생성할 때 Claim에 subject로 사용자를 식별하는 정보(ex: email)가 들어간다.

이렇게 생성된 JWT는 반환되어 사용자가 받을 수 있다.

![ex3]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex3.png)

## 클라이언트 요청 (with JWT)

클라이언트는 토큰을 수신한 뒤 이후에 보내는 모든 요청의 헤더에 토큰을 넣는다.

 

![ex4]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex4.png)

헤더의 key값은 `Authorization`을 value에는 `Bearer`를 앞에 명시해준 뒤 수신받은 토큰값을 넣어준다. 

ex) `Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ0ZXN0QG5hdmVyLmNvbSIsImV4cCI6MTU5NzQwMzUxNiwiaWF0IjoxNTk
3Mzg1NTE2fQ.eQBXhSVgVebfruLLQbJbi3rm6siyEAStGz349IGcmpvwuWujz5O6OEcgJkIrBBy2aFHHDSI8AKNtmaN4gB6WqQ`

서버에서는 모든 요청을 검사하기 위해 Filter를 추가해준다. Filter의 추가는 WebSecurityConfig에서 진행되고 Filter의 구조는 다음과 같다.

### JwtRequestFilter.java

```java
@Component
public class JwtRequestFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUserDetailsService jwtUserDetailsService;

    @Autowired
    private JwtTokenUtil jwtTokenUtil;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
        throws ServletException, IOException {

        final String requestTokenHeader = request.getHeader("Authorization");
        
        String email = null;
        String jwtToken = null;

        // JWT Token is in the form "Bearer token". Remove Bearer word and get
        // only the Token
        // 헤더에서 username 에 있는 email을 가져와 비교를 진행한다
        if (requestTokenHeader != null && requestTokenHeader.startsWith("Bearer ")) {
            jwtToken = requestTokenHeader.substring(7);
            
            try {
                email = jwtTokenUtil.getUserEmailFromToken(jwtToken);
                System.out.println("token user name : " + email);
            } catch (IllegalArgumentException e) {
                System.out.println("Unable to get JWT Token");
            } catch (ExpiredJwtException e) {
                System.out.println("JWT Token has expired");
            }
        } else {
            logger.warn("JWT Token does not begin with Bearer String");
        }

        // Once we get the token validate it.
        if (email != null && SecurityContextHolder.getContext().getAuthentication() == null) {

            UserDetails userDetails = this.jwtUserDetailsService.loadUserByUsername(email);

            // if token is valid configure Spring Security to manually set
            // authentication
            if (jwtTokenUtil.validateToken(jwtToken, userDetails)) {

                UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(
                    userDetails, null, userDetails.getAuthorities());
                usernamePasswordAuthenticationToken
                    .setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

                // After setting the Authentication in the context, we specify
                // that the current user is authenticated. So it passes the
                // Spring Security Configurations successfully.
                SecurityContextHolder.getContext().setAuthentication(usernamePasswordAuthenticationToken);
            }
        }
        chain.doFilter(request, response);
    }
}
```

필터는 다음과 같이 동작한다.

1. 요청 헤더에서 `Authorization`값을 추출한다.
2. 추출된 값이 `Bearer`로 시작하는지 확인한다.
3. JWT 값을 추출하고 추출된 값에서 사용자 email을 획득한다.
4. email값으로 인증된 사용자 객체를 생성하여 반환한다.

필터를 거쳐 검증된 JWT인것을 확인 받으면 원래 가려던 목적지로 이동한다. 해당 예시에서는 `/hello` 로 요청했으며 내용은 간단하게 구성했다.

```java
public class HelloWorldController {
    @RequestMapping({ "/hello" })
    public String firstPage() {
        return "Hello. you have valid JWT (JSon Web Token)!";
    }
}
```

JWT가 유효하다면 아래와 같은 결과를 얻을 것이다.

![ex5]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex5.png)
만약 헤더에 JWT가 존재하지 않거나, 유효하지 않은 JWT가 존재한다면 401 응답을 받을 것이다.

![ex6]({{ site.baseurl }}/assets/images/study/jwt_with_spring/ex6.png)

# Reference

[🔓Simple Login App (hard-coded ver.)](https://velog.io/@dsunni/Spring-Boot-React-JWT%EB%A1%9C-%EA%B0%84%EB%8B%A8%ED%95%9C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
