# JWT介绍

> 原文链接：[(16条消息) SpringBoot整合JWT_joybinny的博客-CSDN博客_springboot整合jwt](https://blog.csdn.net/AkiraNicky/article/details/99307713)

> :seedling::seedling:不了解可以看看，如果了解建议跳过。

## JWT简介

### 什么是JWT

​		JWT(JSON Web Token)是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准。

​		它将用户信息[加密](https://so.csdn.net/so/search?q=加密&spm=1001.2101.3001.7020)到token里，服务器不保存任何用户信息。服务器通过使用保存的密钥验证token的正确性，只要正确即通过验证；应用场景如用户登录。

### 为什么使用JWT

​		随着技术的发展，分布式web应用的普及，通过session管理用户登录状态成本越来越高，因此慢慢发展成为token的方式做登录身份校验，然后通过token去取redis中的缓存的用户信息，随着之后jwt的出现，校验方式更加简单便捷化，无需通过redis缓存，而是直接根据token取出保存的用户信息，以及对token可用性校验，单点登录更为简单。

### 传统Cookie+Session与JWT对比

1. 在传统的用户登录认证中，因为http是无状态的，所以都是采用session方式。用户登录成功，服务端会保证一个session，当然会给客户端一个sessionId，客户端会把sessionId保存在cookie中，每次请求都会携带这个sessionId。
2. cookie+session这种模式通常是保存在内存中，而且服务从单服务到多服务会面临的session共享问题，随着用户量的增多，开销就会越大。而JWT不是这样的，只需要服务端生成token，客户端保存这个token，每次请求携带这个token，服务端认证解析就可。

### JWT组成

第一部分为头部（header)，第二部分我们称其为载荷（payload)，第三部分是签证（signature)。【中间用 . 分隔】

## springBoot整合JWT

### 依赖引入

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- jwt -->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.10.3</version>
        </dependency>
        <!--引入Knife4j的官方start包,Swagger2基于Springfox2.10.5项目-->
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <!--使用Swagger2-->
            <artifactId>knife4j-spring-boot-starter</artifactId>
            <version>2.0.9</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
```

> :bug::bug:
>
> 本来想用Knife4j的，后来因为配置了拦截器，把Knife4j拦截了，访问不了。:astonished::astonished:

### 流程图

> 登录

![登录-第 1 页.drawio](D:/notes/3150/%E7%99%BB%E5%BD%95-%E7%AC%AC%201%20%E9%A1%B5.drawio.png)

> 续约

![登录-第 2 页.drawio](D:/notes/3150/%E7%99%BB%E5%BD%95-%E7%AC%AC%202%20%E9%A1%B5.drawio.png)

### 实现

#### JWTUtils

```java
public class JwtUtil {
    // 签名密钥 自定义
    private static final String SECRET = "hello JWT *%$#$&";

    /**
     * 生成token
     * @param payload token携带的信息
     * @return token字符串
     */
    public static String generateToken(Map<String,String> payload){
        //1.头部默认
        // 指定token过期时间
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.SECOND, 10);  //10s
        JWTCreator.Builder builder = JWT.create();
        // 2.构建payload
        payload.forEach(builder::withClaim);
        //3.构建签证
        // 指定签发时间、过期时间 和 签名算法，并返回token
        String token = builder.withIssuedAt(new Date()).withExpiresAt(calendar.getTime()).sign(Algorithm.HMAC256(SECRET));
        return token;
    }


    /**
     * 解析token
     * @param token token字符串
     * @return 解析后的token类
     */
    public static DecodedJWT decodeToken(String token){
        JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256(SECRET)).build();
        DecodedJWT decodedJWT = jwtVerifier.verify(token);

        return decodedJWT;
    }
}
```

#### 拦截器

```java
@Slf4j
public class PermissionIntercept implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取请求头中的token
        String token = request.getHeader("access-token");
        try {
            //解析token
            DecodedJWT dj = JwtUtil.decodeToken(token);
            //获取想要的数据
            String account = dj.getClaim("account").asString();
            String email = dj.getClaim("email").asString();
            //放入请求中
            request.setAttribute("user", account != null ? new User(account, email) : new User());
            // -------------------------------------------------------------------------------------------------------

            // 计算当前时间是否超过过期时间的一半，如果是就帮用户续签 --------------------------
            // 此处并不是永久续签，只是为 大于过期时间的一半 且 小于过期时间 的 token 续签
            Long expTime = dj.getExpiresAt().getTime();
            Long iatTime = dj.getIssuedAt().getTime();
            Long nowTime = new Date().getTime();
            if ((nowTime - iatTime) > (expTime - iatTime) / 2) {
                // 生成新的jwt
                log.info("令牌续约");
                Map<String, String> payload = new HashMap<>();
                payload.put("account", account); // 加入一些非敏感的用户信息
                payload.put("email", email);    // 加入一些非敏感的用户信息
                String newJwt = JwtUtil.generateToken(payload);
                // 加入返回头
                response.addHeader("access-token", newJwt);
            }
        } catch (JWTDecodeException e) {
            log.error("令牌错误");
            return false;

        } catch (TokenExpiredException e) {
            log.error("令牌过期");
            return false;
        }
        return true;
    }
}

```

#### 拦截规则

```java
@Configuration
public class PermissionWebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new PermissionIntercept())
                .addPathPatterns("/**")    // 拦截哪些页面
                .excludePathPatterns("/user/login", "/user/register");   // 放行哪些页面
    }
}
```

> 若想使用Knife4j，修改拦截规则
>
> addPathPatterns("/user/**")  拦截user下的所有请求

#### Controller

```java
@RestController
@RequestMapping("user")
public class UserController {

    /**
     * 登录
     *
     * @param user
     * @return 返回token
     */
    @PostMapping("/login")
    public String userLogin(@RequestBody User user) {
        String account = user.getAccount();
        String email = user.getEmail();
        //测试：account=admin Email= 123
        if ("admin".equals(account) && "123".equals(email)) {
            HashMap<String, String> jwt = new HashMap<>();
            jwt.put("account", account);
            jwt.put("email", email);
            String token = JwtUtil.generateToken(jwt);
            return token;
        }
        return "登录失败";
    }

    /**
     * 获取登录用户信息
     *
     * @param request
     * @return
     */
    @GetMapping("getUser")
    public User gerUser(HttpServletRequest request) {
        User user = (User) request.getAttribute("user");
        return user;
    }
}
```

#### 测试

> 登录

![image-20221204182137386](D:/notes/3150/image-20221204182137386.png)

> 续约：疯狂点:sunglasses:

![image-20221204182221250](D:/notes/3150/image-20221204182221250.png)

![image-20221204182234054](D:/notes/3150/image-20221204182234054.png)