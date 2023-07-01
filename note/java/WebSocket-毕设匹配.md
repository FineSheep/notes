# WebSocket

## 简介

​		**WebSocket**是一种在单个[TCP](https://baike.baidu.com/item/TCP?fromModule=lemma_inlink)连接上进行[全双工](https://baike.baidu.com/item/全双工?fromModule=lemma_inlink)通信的协议。WebSocket通信协议于2011年被[IETF](https://baike.baidu.com/item/IETF?fromModule=lemma_inlink)定为标准RFC 6455，并由RFC7936补充规范。WebSocket API也被[W3C](https://baike.baidu.com/item/W3C?fromModule=lemma_inlink)定为标准。

​		WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

> :star:说白了就是服务器向客户端传输信息的手段。一般情况下只能是客户端向服务器发送消息，服务器无法发送消息给客户端，使用WebSocket则可以实现双向通信。:star2:

## 后端Springboot整合

> 前端我暂时还没写，哈哈哈

### 依赖引入

```xml
    <dependencies>
         <!--websocket-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
         <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 启动类

```java
@SpringBootApplication
public class WebSocketApplication {
    public static void main(String[] args) {
        SpringApplication.run(WebSocketApplication.class, args);
    }

    @Bean
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }
}
```

> 当然，你也可以另写一个配置文件
>
> 这个bean的注册,用于扫描带有@ServerEndpoint的注解成为websocket 

### Socket编写

```java
@Component
@Slf4j
@ServerEndpoint(value = "/socket/{userId}")
public class TestSocket {
    @OnOpen
    public void onOpen(@PathParam("userId") String userId) {
        log.info("ChatWebsocket open 有新连接加入 userId: {}", userId);
        log.info("ChatWebsocket open 连接建立完成 userId: {}", userId);
    }
}
```

> 测试网址：
>
> https://www.lddgo.net/network/websocket

![image-20221216204032673](D:/图/3150/image-20221216204032673.png)

![image-20221216204136077](D:/图/3150/image-20221216204136077.png)

### 几个重要注解

- @OnOpen

  连接成功时调用

- @OnClose

  连接断开时调用

- @OnError

  发生错误时调用

- @OnMessage

  收到消息时调用

### 简单实现

```java
@Component
@Slf4j
@ServerEndpoint(value = "/socket/{userId}")
public class TestSocket {
    @OnOpen
    public void onOpen(@PathParam("userId") String userId) {
        log.info("有新连接加入 userId: {}", userId);
        log.info("连接建立完成 userId: {}", userId);
    }

    @OnClose
    public void OnClose() {
        log.info("连接断开");
    }

    @OnError
    public void OnError(Throwable error) {
        log.error("发生了错误  errorMessage: {}", error.getMessage());
    }

    @OnMessage
    public void OnMessage(String message) {
        log.info("收到消息：{}", message);
    }
}
```

![image-20221216205213595](D:/图/3150/image-20221216205213595.png)

![image-20221216205231332](D:/图/3150/image-20221216205231332.png)

### 聊天室实现

```java
@Component
@Slf4j
@ServerEndpoint(value = "/chatRoom/{userId}")
public class ChatRoom {
    /**
     * 存储每一个连接的用户和其对应的ChatRoom
     * 由于webSocket是多例的，采用static把userMap变成一个
     */
    private static Map<String, ChatRoom> userMap = new HashMap<>();
    /**
     * 会话    import javax.websocket.Session;
     */
    private Session session;
    /**
     * userId
     */
    private String userId;


    @OnOpen
    public void onOpen(@PathParam("userId") String userId, Session session) {
        log.info("有新连接加入 userId: {}", userId);
        log.info("连接建立完成 userId: {}", userId);
        this.session = session;
        this.userId = userId;
        userMap.put(userId, this);
    }

    @OnClose
    public void OnClose() {
        log.info("连接断开userId: {}", userId);
    }

    @OnError
    public void OnError(Throwable error) {
        log.error("发生了错误  errorMessage: {}", error.getMessage());
    }

    @OnMessage
    public void OnMessage(String message) {
        log.info("收到消息：{}", message);
        sendMessage(message);
    }
	//发送消息
    public void sendMessage(String message) {
        userMap.keySet().forEach((userId) -> {
            if (!userId.equals(this.userId))     //不给自己发
                userMap.get(userId).session.getAsyncRemote().sendText(message);
        });
    }
}
```

- 由于websocket是多例的，即每次连接都会产生一个新的类。所以使用static保证userMap只有一个，存储所有的userId和ChatRoom
- session和userid没有使用static，所以对于每个连接的用户都是唯一的，都是自己的。

> :exclamation:session为websocket包下的​

- 收到消息时向map中的所有user发送消息（除开自己）

![image-20221216212451103](D:/图/3150/image-20221216212451103.png)

> 连接3个用户，id分别为1，2，3

用户1发送消息，除了用户1，用户2，用户3都收到

用户1：

![image-20221216212528418](D:/图/3150/image-20221216212528418.png)

用户2：

![image-20221216212610056](D:/图/3150/image-20221216212610056.png)

用户3：

![image-20221216212620607](D:/图/3150/image-20221216212620607.png)

## 匹配实现

> :rocket::rocket::airplane::airplane:由于毕设需要实现一个在线匹配对战功能，因此有了这个。
>
> 参考：[(20条消息) SpringBoot + WebSocket 实现答题对战匹配机制_低吟不作语的博客-CSDN博客](https://blog.csdn.net/CSDN_handsome/article/details/117366848)

图示：

![](D:/图/3150/match.png)

> 1. 进入匹配大厅，设置为待匹配
> 2. 点击匹配，设置用用户的状态为匹配中，搜索其他状态为匹配中的用户。在这个过程中，用户可以取消匹配，回到匹配大厅，设置状态为待匹配。
> 3. 开始游戏，实时推送对手答题信息。
> 4. 游戏结束，设置状态为游戏结束。

大致是这个样子，目前完成了匹配机制这一部分。还有很多不足。🤣

#### 依赖引入

```xml
  </properties>
    <dependencies>
        <!--引入Knife4j的官方start包,Swagger2基于Springfox2.10.5项目-->
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <!--使用Swagger2-->
            <artifactId>knife4j-spring-boot-starter</artifactId>
            <version>2.0.9</version>
        </dependency>
        <!--使用lombook-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--mysql连接-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--使用mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--json解析-->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
        <!--使用redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!--使用webSocket-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
    </dependencies>
```

#### yml配置

```yaml
spring:
  datasource:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/learningPlat
      username: root
      password: root
  redis:
    port: 6379
    host: 127.0.0.1
    database: 0
    #knife4j配置
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
      #mybatis-plus日志打印
mybatis-plus:
  configuration:
    map-underscore-to-camel-case: false
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      logic-delete-field: isDelete # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

#### 实体类

```java
@TableName(value ="questions")
@Data
public class Questions implements Serializable {
    /**
     * id
     */
    @TableId(type = IdType.AUTO)
    private Long id;

    /**
     * 题目内容
     */
    @ExcelProperty("题目内容")
    private String content;

    /**
     * 选项A
     */
    @ExcelProperty("选项A")
    private String optionA;

    /**
     * 选项B
     */
    @ExcelProperty("选项B")
    private String optionB;

    /**
     * 选项C
     */
    @ExcelProperty("选项C")
    private String optionC;

    /**
     * 选项D
     */
    @ExcelProperty("选项D")
    private String optionD;

    /**
     * 正确选项
     */
    @ExcelProperty("正确选项")
    private String correct;

    /**
     * 解析
     */
    @ExcelProperty("解析")
    private String analyzation;

    /**
     * 创建时间
     */
    private Date createTime;

    /**
     * 更新时间
     */
    private Date updateTime;

    /**
     * 是否删除
     */
    @TableLogic
    private Integer isDelete;
    /**
     * 题型  0-单选 1-多选 2-判断
     */
    private int type;

    @TableField(exist = false)
    private static final long serialVersionUID = 1L;
}
```

> @ExcelProperty这个注解是写文件上传时用的，不用太在意哈😘

相关sql：

```sql
CREATE TABLE `questions` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `content` text NOT NULL COMMENT '题目内容',
  `optionA` text NOT NULL COMMENT '选项A',
  `optionB` text NOT NULL COMMENT '选项B',
  `optionC` text COMMENT '选项C',
  `optionD` text COMMENT '选项D',
  `correct` varchar(512) NOT NULL COMMENT '正确选项',
  `analyzation` text COMMENT '解析',
  `type` tinyint(4) NOT NULL DEFAULT '0' COMMENT '题型  0-单选 1-多选 2-判断',
  `createTime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updateTime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `isDelete` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=20077 DEFAULT CHARSET=utf8mb4 COMMENT='题目';
```

#### 枚举类

用户状态枚举：

```java
@Slf4j
public enum StatusEnum {

    /**
     * 待匹配
     */
    IDLE,
    /**
     * 匹配中
     */
    IN_MATCH,
    /**
     * 游戏中
     */
    IN_GAME,
    /**
     * 游戏结束
     */
    GAME_OVER,
    ;

    public static StatusEnum getStatusEnum(String status) {
        switch (status) {
            case "IDLE":
                return IDLE;
            case "IN_MATCH":
                return IN_MATCH;
            case "IN_GAME":
                return IN_GAME;
            case "GAME_OVER":
                return GAME_OVER;
            default:
               throw new BusinessException(ErrorCode.SYSTEM_ERROR);
        }
    }

    public String getValue() {
        return this.name();
    }
}
```

Redis中key的枚举类：

选择 Redis 保存用户状态，还是创建一个枚举类，Redis 中存储数据都有唯一的 Key 做标识，因此在这里定义 Redis 中的 Key，分别介绍如下：

USER_STATUS：存储用户状态的 Key，存储类型是 Map<String, String>，其中用户 userId 为 key，用户在线状态 为 value
USER_MATCH_INFO：当用户处于游戏中时，我们需要记录用户的信息，比如分数等。这些信息不需要记录到数据库，而且随时会更新，放入缓存方便获取
ROOM：可以理解为匹配的两名用户创建一个房间，具体实现是以键值对方式存储，比如用户 A 和用户 B 匹配，用户 A 的 userId 是 A，用户 B 的 userId 是 B，则在 Redis 中记录为 {A – B}，{B – A}

```java
public enum EnumRedisKey {

    /**
     * userOnline 在线状态
     */
    USER_STATUS,
    /**
     * userOnline 对局信息
     */
    USER_IN_PLAY,
    /**
     * userOnline 匹配信息
     */
    USER_MATCH_INFO,
    /**
     * 房间
     */
    ROOM;

    public String getKey() {
        return this.name();
    }
}
```

消息类型的枚举：

```java
public enum MessageTypeEnum {

    /**
     * 用户加入
     */
    ADD_USER,
    /**
     * 匹配对手
     */
    MATCH_USER,
    /**
     * 取消匹配
     */
    CANCEL_MATCH,
    /**
     * 游戏开始
     */
    PLAY_GAME,
    /**
     * 游戏结束
     */
    GAME_OVER,
}
```

工具类：操作redis

```java
@Component
public class MatchCacheUtil {

    /**
     * 用户 userId 为 key，MatchSocket 为 value
     */
    private static final Map<String, MatchSocket> CLIENTS = new HashMap<>();

    /**
     * key 是标识存储用户在线状态的 EnumRedisKey，value 为 map 类型，其中用户 userId 为 key，用户在线状态 为 value
     */
    @Resource
    private RedisTemplate<String, Map<String, String>> redisTemplate;

    /**
     * 添加客户端
     */
    public void addClient(String userId, MatchSocket websocket) {
        CLIENTS.put(userId, websocket);
    }

    /**
     * 移除客户端
     */
    public void removeClient(String userId) {
        CLIENTS.remove(userId);
    }

    /**
     * 获取客户端
     */
    public MatchSocket getClient(String userId) {
        return CLIENTS.get(userId);
    }

    /**
     * 移除用户在线状态
     */
    public void removeUserOnlineStatus(String userId) {
        redisTemplate.opsForHash().delete(EnumRedisKey.USER_STATUS.getKey(), userId);
    }

    /**
     * 获取用户在线状态
     */
    public StatusEnum getUserOnlineStatus(String userId) {
        Object status = redisTemplate.opsForHash().get(EnumRedisKey.USER_STATUS.getKey(), userId);
        if (status == null) {
            return null;
        }
        return StatusEnum.getStatusEnum(status.toString());
    }

    /**
     * 设置用户为 IDLE 状态
     */
    public void setUserIDLE(String userId) {
        removeUserOnlineStatus(userId);
        redisTemplate.opsForHash().put(EnumRedisKey.USER_STATUS.getKey(), userId, StatusEnum.IDLE.getValue());
    }

    /**
     * 设置用户为 IN_MATCH 状态
     */
    public void setUserInMatch(String userId) {
        removeUserOnlineStatus(userId);
        redisTemplate.opsForHash().put(EnumRedisKey.USER_STATUS.getKey(), userId, StatusEnum.IN_MATCH.getValue());
    }

    /**
     * 随机获取处于匹配状态的用户（除了指定用户外）
     */
    public String getUserInMatchRandom(String userId) {
        Optional<Map.Entry<Object, Object>> any = redisTemplate.opsForHash().entries(EnumRedisKey.USER_STATUS.getKey())
                .entrySet().stream().filter(entry -> entry.getValue().equals(StatusEnum.IN_MATCH.getValue()) && !entry.getKey().equals(userId))
                .findAny();

        return any.map(entry -> entry.getKey().toString()).orElse(null);
    }

    /**
     * 设置用户为 IN_GAME 状态
     */
    public void setUserInGame(String userId) {
        removeUserOnlineStatus(userId);
        redisTemplate.opsForHash().put(EnumRedisKey.USER_STATUS.getKey(), userId, StatusEnum.IN_GAME.getValue());
    }

    /**
     * 设置处于游戏中的用户在同一房间
     */
    public void setUserInRoom(String userId1, String userId2) {
        redisTemplate.opsForHash().put(EnumRedisKey.ROOM.getKey(), userId1, userId2);
        redisTemplate.opsForHash().put(EnumRedisKey.ROOM.getKey(), userId2, userId1);
    }

    /**
     * 从房间中移除用户
     */
    public void removeUserFromRoom(String userId) {
        redisTemplate.opsForHash().delete(EnumRedisKey.ROOM.getKey(), userId);
    }

    /**
     * 从房间中获取用户
     */
    public String getUserFromRoom(String userId) {
        return redisTemplate.opsForHash().get(EnumRedisKey.ROOM.getKey(), userId).toString();
    }

    /**
     * 设置处于游戏中的用户的对战信息
     */
    public void setUserMatchInfo(String userId, String userMatchInfo) {
        redisTemplate.opsForHash().put(EnumRedisKey.USER_MATCH_INFO.getKey(), userId, userMatchInfo);
    }

    /**
     * 移除处于游戏中的用户的对战信息
     */
    public void removeUserMatchInfo(String userId) {
        redisTemplate.opsForHash().delete(EnumRedisKey.USER_MATCH_INFO.getKey(), userId);
    }

    /**
     * 获取处于游戏中的用户的对战信息
     */
    public String getUserMatchInfo(String userId) {
        return redisTemplate.opsForHash().get(EnumRedisKey.USER_MATCH_INFO.getKey(), userId).toString();
    }

    /**
     * 设置用户为游戏结束状态
     */
    public synchronized void setUserGameOver(String userId) {
        removeUserOnlineStatus(userId);
        redisTemplate.opsForHash().put(EnumRedisKey.USER_STATUS.getKey(), userId, StatusEnum.GAME_OVER.getValue());
    }
}
```

#### redis配置

> 来源：黑马🐴

```java
@Configuration
public class RedisTemplateConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        // 创建RedisTemplate对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂
        template.setConnectionFactory(connectionFactory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer =
                new GenericJackson2JsonRedisSerializer();
        // 设置Key的序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置Value的序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
        // 返回
        return template;
    }
}
```

#### 匹配实现

​		创建一个线程专门用来匹配用户，如果匹配成功就向客户端推送消息

​		用户匹配对手时遵循这么一个原则：用户 A 找到用户 B，由用户 A 负责一切工作，既由用户 A 完成创建匹配数据并保存到缓存的全部操作。值得注意的一点是，在匹配时要注意保证状态的变化：

- 当前用户在匹配对手的同时，被其他用户匹配，那么当前用户应当停止匹配操作
- 当前用户匹配到对手，但对手被其他用户匹配了，那么当前用户应该重新寻找新的对手

```java
@Component
@Slf4j
@ServerEndpoint(value = "/game/match/{userId}")
@Data
public class MatchSocket {

    private Session session;

    private String userId;

    private static MatchCacheUtil matchCacheUtil;
    private static MatchImpl matchImpl;

    @Autowired
    public void setMatchImpl(MatchImpl matchImpl) {
        MatchSocket.matchImpl = matchImpl;
    }

    @Autowired
    public void setMatchCacheUtil(MatchCacheUtil matchCacheUtil) {
        MatchSocket.matchCacheUtil = matchCacheUtil;
    }


    @OnOpen
    public void onOpen(@PathParam("userId") String userId, Session session) {
        log.info(" open 有新连接加入 userId: {}", userId);
        this.userId = userId;
        this.session = session;
        matchCacheUtil.addClient(userId, this);
        System.out.println(matchImpl);
        log.info(" open 连接建立完成 userId: {}", userId);
    }


    @OnClose
    public void onClose() {
        log.info(" onClose 连接断开 userId: {}", userId);

        matchCacheUtil.removeClient(userId);
        matchCacheUtil.removeUserOnlineStatus(userId);
        matchCacheUtil.removeUserFromRoom(userId);
        matchCacheUtil.removeUserMatchInfo(userId);

        log.info("onClose 连接断开完成 userId: {}", userId);
    }

    @OnMessage
    public void onMessage(String message, Session session) {
        log.info("onMessage userId: {}, 来自客户端的消息 message: {}", userId, message);
        Gson gson = new Gson();
        Type gsonType = new TypeToken<Map<String, String>>() {
        }.getType();
        Map<String, String> map = gson.fromJson(message, gsonType);
        MessageTypeEnum type = gson.fromJson(map.get("type"), MessageTypeEnum.class);
        log.info("onMessage userId: {}, 来自客户端的消息类型 type: {}", userId, type);
        if (type == MessageTypeEnum.ADD_USER) {
//            addUser();
        } else if (type == MessageTypeEnum.MATCH_USER) {
            matchImpl.match(userId);
        } else if (type == MessageTypeEnum.CANCEL_MATCH) {
//            cancelMatch();
        } else if (type == MessageTypeEnum.PLAY_GAME) {
//            toPlay();
        } else if (type == MessageTypeEnum.GAME_OVER) {
//            gameover();
        } else {
            throw new BusinessException(ErrorCode.SYSTEM_ERROR);
        }

        log.info("onMessage userId: {} 消息接收结束", userId);
    }

}
```

> :sob:前文提到，由于webSocket是多例模式的，而spring默认是单例的，所以，这了我就踩了个坑。
>
> :bug::bug:按照以往的编程习惯，我通过@Autowired自动注入MatchImpl与MatchCacheUtil的值，结果报空指针。结合球友解释以及百度了解原因以及解决方式。上文是第一种，还有一种未实验。🤪

实现：

```java
@Service
@Slf4j
public class MatchImpl implements MatchService {

    @Autowired
    private QuestionsService questionsService;
    @Autowired
    private MatchCacheUtil matchCacheUtil;
    private Lock lock = new ReentrantLock();

    private Condition matchCond = lock.newCondition();

    /**
     * 向成员发送题目
     * @param ids 房间内成员id
     * @param data 题目数据
     * @param <T>
     */
    public <T> void sendMessage(Set<String> ids, T data) {
        ids.forEach(userId -> {
            try {
                MatchSocket client = matchCacheUtil.getClient(userId);
                Gson gson = new Gson();
                String json = gson.toJson(ResultUtils.success(data));
                client.getSession().getBasicRemote().sendText(json);
            } catch (IOException e) {
                throw new BusinessException(ErrorCode.SYSTEM_ERROR);
            }
        });


    }

    /**
     * 获取题目，随机从数据库抽取20道题目，发送给房间内的成员
     * @param ids 房间内的成员id
     */
    public void sendQues(Set<String> ids) {
        Map<Integer, List<Questions>> ques = questionsService.getQuesRandom(20);
        Gson gson = new Gson();
        sendMessage(ids, gson.toJson(ques));
    }

    @Override
    /**
     * 匹配
     */
    public void match(String userId) {
        log.info("matchUser 用户随机匹配对手开始 , userId: {}", userId);
        //保证原子性
        lock.lock();
        try {
            // 设置用户状态为匹配中
            matchCacheUtil.setUserInMatch(userId);
            //唤醒等待线程
            matchCond.signal();
        } finally {
            lock.unlock();
        }
        new Thread(() -> {
            boolean flag = true;
            String receiver = null;
            while (flag) {
                // 获取除自己以外的其他待匹配用户
                // 当前用户不处于待匹配状态
                lock.lock();
                try {
                    //当前用户不在匹配状态
                    if (matchCacheUtil.getUserOnlineStatus(userId).compareTo(StatusEnum.IN_GAME) == 0
                            || matchCacheUtil.getUserOnlineStatus(userId).compareTo(StatusEnum.GAME_OVER) == 0) {
                        log.info("matchUser 当前用户 {} 已退出匹配", userId);
                        return;
                    }
                    //当前用户取消匹配
                    if (matchCacheUtil.getUserOnlineStatus(userId).compareTo(StatusEnum.IDLE) == 0) {
                        log.info("matchUser 当前用户 {} 已退出匹配", userId);
                        HashSet<String> idSet = new HashSet<>();
                        idSet.add(userId);
                        sendMessage(idSet, Constant.CANCEL);
                    }
                    receiver = matchCacheUtil.getUserInMatchRandom(userId);
                    if (receiver != null) {
                        //对手取消匹配
                        if (matchCacheUtil.getUserOnlineStatus(receiver).compareTo(StatusEnum.IN_MATCH) != 0) {
                            log.info("matchUser 当前用户 {}, 匹配对手 {} 已退出匹配状态", userId, receiver);
                            HashSet<String> idSet = new HashSet<>();
                            idSet.add(userId);
                            sendMessage(idSet, Constant.CANCEL);
                        } else {
                            //加入对战房间，设置状态
                            matchCacheUtil.setUserInGame(userId);
                            matchCacheUtil.setUserInGame(receiver);
                            matchCacheUtil.setUserInRoom(userId, receiver);
                            //结束循环
                            flag = false;
                            log.info("匹配成功{}---->{}", userId, receiver);
                            HashSet<String> ids = new HashSet<>();
                            ids.add(userId);
                            ids.add(receiver);
                            //抽取题目，发送
                            sendQues(ids);
                        }
                    } else {
                        try {
                            log.info("matchUser 当前用户 {} 无对手可匹配", userId);
                            //等待
                            matchCond.await();
                        } catch (InterruptedException e) {
                            log.error("matchUser 匹配线程 {} 发生异常: {}",
                                    Thread.currentThread().getName(), e.getMessage());
                        }
                    }
                } finally {
                    lock.unlock();
                }
            }

        }).start();
    }
}
```

> 大致就这样啦，可能还有bug未发现。。。。。