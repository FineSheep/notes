# Python爬虫入门

Created: March 11, 2023 8:53 AM

# 一、Python基础

## （一）数据类型

1. 常用类型
    1. Number(int)
        1. int(有符号整型)
        2. long(长整型，也可以代表八进制和十六进制)
        3. float(浮点型)
        4. complex(复数)
    2. String(str)
    3. Boolean(bool)
    4. List(列表list)
    5. Set(集合set)
    6. Tuple(元组tuple)
        1. 注意：与列表类似，但无法修改元素
    7. Dictionary(字典dict)
    
    ```python
    # Number
    a = 3
    # String
    a = '3'
    # Boolean
    a = FALSE
    # List
    a = [1, 2, 3, 4]
    # Set
    a = {1, 2, 3, 4}
    # Tuple
    a = (1, 2, 3, 4)
    # Dictionary
    a = {'name': 'zhangsan'}
    ```
    
2. 基本使用
    1. 字符串
        1. len：获取长度——en函数可以获取字符串的长度。
        2. find：查找内容——查找指定内容在字符串中是否存在，如果存在就返回该内容在字符串中第一次出现的开始位置索引值，如果不存在，则返回-1。
        3. startswith,endswith：判断——判断字符串是不是以谁谁谁开头/结尾。
        4. count：计算出现次数——返回 str在start和end之间 在 mystr里面出现的次数。
        5. replace：替换内容——替换字符串中指定的内容，如果指定次数count，则替换不会超过count次。
        6. split：切割字符串——通过参数的内容切割字符串。
        7. upper,lower：修改大小写——将字符串中的大小写互换。
        8. strip：空格处理——去空格。
        9. join：字符串拼接。
        10. format(参数)：将参数按顺序填充到String的占位符{}中，替换占位符。
    2. 列表
        1. append()：添加——在末尾添加元素。
        2. insert(index, object)：插入——在指定index位置插入元素。
        3. extend()：合并——将另一个列表中的元素逐一添加到列表中。
        4. 访问/修改：通过下标访问修改元素。
        5. 判断：in / not in——是否存在。
        6. 删除
            1. del：根据下标进行删除
            2. pop()：删除最后一个元素
            3. remove()：根据元素的值进行删除
    3. 元组
        1. 访问：下标index。
        2. 修改/删除：不允许。
        3. 特殊：定义只有一个元素的元组，需要在唯一的元素后写一个逗号，否则变为其他类型，即a=(1,)。
    4. 切片
        1. 概念：切片是指对操作的对象截取其中一部分的操作。字符串、列表、元组都支持切片操作。
        2. 语法：[起始:结束:步长]，也可以简化使用 [起始:结束]（选取的区间从"起始"位开始，到"结束"位的前一位结束，不包含结束位本身。步长表示选取间隔。索引从0开始。
        
        ```python
        # 索引是通过下标取某一个元素
        # 切片是通过下标去某一段元素
        s = 'Hello World!'
        print(s[4]) # o 字符串里的第4个元素
        print(s[3:7]) # lo W 包含下标 3，不含下标 7
        print(s[1:]) # ello World! 从下标为1开始，取出后面所有的元素（没有结束位）
        print(s[:4]) # Hell 从起始位置开始，取到 下标为4的前一个元素（不包括结束位本身）
        print(s[1:5:2]) # el 从下标为1开始，取到下标为5的前一个元素，步长为2（不包括结束位本身）
        ```
        
    5. 字典
        1. 查看：通过key。
        2. 修改/新增：通过key查找，然后赋值，如果存在则修改，如果不存在则新增。
        3. 删除：① del；② clear()；
           
            ```python
            # 1. del：带key，删除某个元素，不带key，删除整个字典
            info = {'name':'班长', 'id':100}
            del info['name'] # del 可以通过键删除字典里的指定元素
            del info # del 也可以直接删除变量
            # 2. clear()：清空字典元素
            info.clear()
            ```
            
        4. 遍历：key、value、项、键值对
           
            ```python
            # 遍历key
            for key in info.keys():
            	print key
            # 遍历value
            for value in info.values():
            	print value
            # 遍历字典的项（元素）
            for item in info.items():
            	print item
            # 遍历字典的key-value键值对
            for key,value in info.items():
            	print(key)
            	print(value)
            ```
            

## （二）函数

1. 定义
2. 调用
3. 参数
    1. 位置参数：按照顺序对应；
    2. 关键词参数：按照参数名对应；
4. 返回值
5. 变量
    1. 局部变量
    2. 全局变量

```python
def function(a, b):
	print(a, b)

def function(a=a, b=b):
	print(a, b)
```

## （三）文件

1. 打开
    1. 如果用open打开文件时，如果使用的"r"，那么可以省略，即open('test.txt')
    2. 访问模式
    
    | 访问模式 | 说明 |
    | --- | --- |
    | r | 以只读方式打开文件。文件的指针将会放在文件的开头。如果文件不存在，则报错。这是默认模式。 |
    | w | 打开一个文件只用于写入。如果该文件已存在则将其覆盖。如果该文件不存在，创建新文件。 |
    | a | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
    | r+ | 打开一个文件用于读写。文件指针将会放在文件的开头。 |
    | w+ | 打开一个文件用于读写。如果该文件已存在则将其覆盖。如果该文件不存在，创建新文件。 |
    | a+ | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。 |
    | rb | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。 |
    | wb | 以二进制格式打开一个文件只用于写入。如果该文件已存在则将其覆盖。如果该文件不存在，创建新文件。 |
    | ab | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
    | rb+ | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。 |
    | wb+ | 以二进制格式打开一个文件用于读写。如果该文件已存在则将其覆盖。如果该文件不存在，创建新文件。 |
    | ab+ | 以二进制格式打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。 |
2. 关闭
3. 读写
   
    1. 写：如果文件不存在，那么创建；如果存在那么就先清空，然后写入数据
4. 序列化/反序列化
    1. 概念：通过文件操作，我们可以将字符串写入到一个本地文件。但是，如果是一个对象(例如列表、字典、元组等)，就无法直接写入到一个文件里，需要对这个对象进行序列化，然后才能写入到文件里。设计一套协议，按照某种规则，把内存中的数据转换为字节序列，保存到文件，这就是序列化（对象→字节序列）。反之，从文件的字节序列恢复到内存中，就是反序列化（字节序列→对象）。
    2. JSON：JavaScriptObjectNotation, JS对象简谱，是一种轻量级的数据交换标准。JSON的本质是字符串。

```python
# 打开
f = open('test.txt', 'w')

# 关闭
f.close()

# 读
content = f.read(5) # 最多读取5个数据（字节）
content = f.read() # 从上次读取的位置继续读取剩下的所有的数据
content = f.readline() # 只用来读取一行数据。
content = f.readlines() # 按照行的方式把整个文件中的内容进行一次性读取，并且返回的是一个列表，其中每一行为列表的
一个元素。

# 写
f.write('hello world, i am here!\n' * 5)    # 写5次helloworld

# 序列化：对象→字节序列
names = ['zhangsan', 'lisi', 'wangwu', 'jerry', 'henry', 'merry', 'chris']
result = json.dumps(names)  # 把对象转换成为字符串，它本身不具备将数据写入到文件的功能。
file = open('names.txt', 'w')
json.dump(names, file)  # 将对象转换成为字符串的同时，指定一个文件对象，把转换后的字符串写入到这个文件里。

# 反序列化
names = '["zhangsan", "lisi", "wangwu", "jerry", "henry", "merry", "chris"]'
result = json.loads(names)  # 需要一个字符串参数，用来将一个字符串加载成为Python对象。
print(type(result)) # <class 'list'>
result = json.load(file)  # 传入一个文件对象，用来将一个文件对象里的数据加载成为Python对象。
```

## （四）异常

1. 异常处理
   
    try...except语句可以对代码运行过程中可能出现的异常进行处理。
    
    ```python
    try:
    	可能会出现异常的代码块
    except 异常的类型:
    	出现异常以后的处理语句
    ```
    

# 二、Urllib请求

## （一）互联网爬虫

1. 简介：如果我们把互联网比作一张大的蜘蛛网，那一台计算机上的数据便是蜘蛛网上的一个猎物，而爬虫程序就是一只小蜘蛛，沿着蜘蛛网抓取自己想要的数据。

2. 爬虫/反爬须知

   1. robots协议：防君子不防小人，在Scrapy配置文件中设为False即可

   2. User‐Agent：User Agent中文名为用户代理，简称 UA，它是一个特殊字符串头，使得服务器能够识别客户使用的操作系统及版本、CPU 类型、浏览器及版本、浏览器渲染引擎、浏览器语言、浏览器插件等。

   3. Cookie等请求头：添加相应的请求头
      1. `user-agent`：浏览器配置的一些信息
      2. `cookie`：用户的登录信息
      3. `referer`：来源页面校验，判断当前路径是不是由指定路径跳转过来的，用于防盗链，比如图片防盗链。

   4. 图片懒加载

      1. 情况一：data-original为真实地址，src为虚假地址，xpath解析data-original地址
      2. 情况二：只有一个地址，但加载前属性是src2，加载后属性是src，解析的时候要使用加载前的属性才能解析到数据。

      > 注意第一张图片的属性可能不一样
      > 

   5. 代理IP：通过代理IP访问，可以隐藏自己的真实IP，防止被封IP等；
       1. 提供商：西次代理、快代理
       2. 代理分类
           1. 透明代理：使用后对方服务器可以知道你使用了代理，并且也知道你的真实IP。
           2. 匿名代理：使用后对方服务器可以知道你使用了代理，但不知道你的真实IP。
           3. 高匿名代理：使用后对方服务器不知道你使用了代理，更不知道你的真实IP。

   6. 验证码访问
   
1. 提供商：打码平台、云打码平台、超级鹰
   
7. 动态加载网页：网站返回的是js数据 并不是网页的真实数据
   
   可以通过selenium驱动真实的浏览器发送请求
   
   8. 数据加密
      
       可以通过分析JS代码
       

## （二）Urllib库的使用

1. 基本使用
    1. 1个类型6个方法
    2. 编解码
    3. 下载
    
    ```python
    # 模拟浏览器向服务器发送请求
    response = urllib.request.urlopen(url)
    
    # 1个类型
    1. response是服务器返回的数据，数据类型是HttpResponse
    
    # 6个方法
    1. 按照一个字节一个字节的去读(read方法默认返回的是字节数据，需要解码成字符串)
    content = response.read().decode('utf-8')
    2. 返回多少个字节
    content = response.read(5).decode('utf-8')
    3. 读取一行/所有行
    content = response.readline().decode('utf-8')
    content = response.readlines().decode('utf-8')
    4. 获取状态码  如果是200，那么就证明我们的逻辑没有错
    code = response.getcode()
    5. 获取url地址
    url = response.geturl()
    6. 获取请求头信息
    headers = response.getheaders()
    
    # 编解码
    decode('utf-8')：解码，字节‐‐>字符串
    encode('utf-8')：编码，字符串‐‐>字节
    
    # 下载文件：网页html文件、图片、视频。
    # url——下载的路径  filename——文件的名字
    urllib.request.urlretrieve(url, filename)
    ```
    
2. 请求对象的定制
    1. 概念：定制封装请求对象Request，携带一些请求头和参数
    2. 语法：`request = urllib.request.Request()`
    
    ```python
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'
    }
    # 因为urlopen方法中不能存储字典 所以headers不能传递进去
    # 请求对象的定制
    request = urllib.request.Request(url=url,headers=headers)
    response = urllib.request.urlopen(request)
    ```
    
3. 编解码
    1. 概念：将请求参数的String形式变成Unicode编码格式才能识别，因为默认识别ASK码
    2. get请求
        1. 单个参数：`urllib.parse.quote('周杰伦')`
        2. 多个参数：`urllib.parse.urlencode(data<Map>)`（对字典请求参数进行&拼接）
    3. post请求
        1. 参数编码：`urllib.parse.urlencode(data<Map>).encode('utf-8')`
    4. 总结
        1. get请求方式的参数必须编码，参数是拼接到url后面，编码之后不需要调用`encode('utf-8')`方法；
        2. post请求方式的参数必须编码，参数是放在请求对象定制的方法中，编码之后需要调用`encode('utf-8')`方法
    
    ```python
    1. get请求：参数通过url拼接
    url = 'https://www.baidu.com/s?wd='
    # 单个参数
    name = urllib.parse.quote('周杰伦')
    # 多个参数
    data = {
        'wd':'周杰伦',
        'sex':'男',
        'location':'中国台湾省'
    }
    new_data = urllib.parse.urlencode(data)
    # url拼接参数
    url = url + name
    url = url + **new_data**
    # 请求对象的定制
    request = urllib.request.Request(url=url, headers=headers)
    
    2. post请求
    url = 'https://fanyi.baidu.com/sug'  # 百度翻译
    data = {
        'kw':'spider'
    }
    # post请求的参数必须要进行编码
    data = urllib.parse.urlencode(data).encode('utf-8')
    # post的请求的参数是不会拼接在url的后面的，而是需要放在请求对象定制的参数中
    request = urllib.request.Request(url=url,**data=data**,headers=headers)
    ```
    
4. post请求应该是字节型数据，所以请求体的数据在发送前需要进行编码
    1. `urllib.parse.quote/urlencode()`：方法返回值是编码之后的字符串
    2. `encode('utf-8')`：编码成字节数据
5. Ajax请求：
6. Ajax请求
    1. 简介：请求头带上`X-Requested-With: XMLHttpRequest`，Ajax的核心对象。ajax 请求是一种特别的 http 请求，对服务器来说，没有任何区别，区别在浏览器端。ajax 是一种不刷新页面的请求方式：请求回来数据后 我们进行 DOM 操作（增删改），不会刷新页面。
        1. Ajax请求：浏览器端发送的请求，只有 XHR 或者 fetch 发出的才是 ajax 请求，其他所有的都是 非 ajax 请求。浏览器不会对界面进行任何更新操作，只是监视其回调函数，并返回相关数据
        2. 普通请求：浏览器一般会直接显示响应体数据，也就是我们通常说的 刷新/跳转 页面，也就是说一般请求需要页面刷新
    2. get
    3. post
    
    ```python
    1. 数据下载到本地
    # open方法默认情况下使用的是gbk的编码  如果我们要想保存汉字 那么需要在open方法中指定编码格式为utf-8
    # encoding = 'utf-8'
    # 方法一
    fp = open('douban.json','w',encoding='utf-8')
    fp.write(content)
    
    # 方法二
    with open('douban1.json','w',encoding='utf-8') as fp:
        fp.write(content)
    ```
    
7. Urllib异常
    1. 简介：URLError、HTTPError，HTTPError类是URLError类的子类。
    2. 导包：导入的包urllib.error.HTTPError，urllib.error.URLError。
    3. http错误：http错误是针对浏览器无法连接到服务器而增加出来的错误提示。引导并告诉浏览者该页是哪里出了问题。
    4. 处理：通过urllib发送请求的时候，有可能会发送失败，这个时候如果想让你的代码更加的健壮，可以通过try…except进行捕获异常，异常有两类——URLError和HTTPError。
8. Handler处理器
    1. 作用：定制更高级的请求头（随着业务逻辑的复杂，请求对象的定制已经满足不了我们的需求，动态cookie和代理不能使用请求对象的定制）
    2. 基本使用
       
        ```python
        # 请求对象定制
        request = urllib.request.Request(url = url,headers = headers)
        
        # 关键词：handler  build_opener  open
        （1）获取hanlder对象
        handler = urllib.request.HTTPHandler()
        （2）获取opener对象
        opener = urllib.request.build_opener(handler)
        （3）调用open方法
        response = opener.open(request)
        ```
    
9. 代理服务器
    1. 功能：
        1. 突破自身IP访问限制，访问国外站点。
        2. 访问一些单位或团体内部资源：某大学FTP(前提是该代理地址在该资源的允许访问范围之内)，使用教育网内地址段免费代理服务器，就可以用于对教育网开放的各类FTP下载上传，以及各类资料查询共享等服务。
        3. 提高访问速度：通常代理服务器都设置一个较大的硬盘缓冲区，当有外界的信息通过时，同时也将其保存到缓冲区中，当其他用户再访问相同的信息时， 则直接由缓冲区中取出信息，传给用户，以提高访问速度。
        4. 隐藏真实IP：上网者可以通过这种方法隐藏自己的IP，免受攻击。
    2. 使用
       
        ```python
        # 请求对象定制
        request = urllib.request.Request(url = url,headers = headers)
        # 创建代理
        proxies = {
            'http':'118.24.219.151:16817'
        }
        # handler  build_opener  open
        handler = urllib.request.**ProxyHandler**(proxies = proxies)
        opener = urllib.request.build_opener(handler)
        response = opener.open(request)
        ```
        
    3. 代理池
       
        ```python
        # 创建代理池
        proxies_pool = [
            {'http':'118.24.219.151:16817'},
            {'http':'118.24.219.151:16817'},
        ]
        # 随机选择代理
        proxies = random.choice(proxies_pool)
        ```
        

# 三、解析

## （一）Xpath

1. 安装
    1. 浏览器插件：xpath
    2. lxml库：`pip install lxml ‐i https://pypi.douban.com/simple`
2. 基本使用
   
    ```python
    1. 导入lxml.etree
    from lxml import etree
    
    2. etree.parse() 解析
    # 解析本地文件
    html_tree = etree.parse('XX.html')
    # 解析服务器响应文件
    html_tree = etree.HTML(response.read().decode('utf‐8'))
    
    3. 获取想要的数据
    html_tree.xpath(xpath路径)
    ```
    
    > 返回数据类型是列表类型
    > 
3. xpath语法
    1. 路径查询
        1. //：查找所有子孙节点，不考虑层级关系
        2. / ：找直接子节点
    2. 谓词查询
        1. //div[@id]：查找所有有id属性的div标签
        2. //div[@id="maincontent"]：查找id属性为maincontent的div标签
    3. 属性查询
       
        1. //@class：获取class的属性值
    4. 模糊查询
        1. //div[contains(@id, "he")]：id属性值包含he的div标签
        2. //div[starts‐with(@id, "he")]：id属性值以he开头的div标签
    5. 内容查询
       
        1. //div/h1/text()：获取`<h1>内容</h1>`中的内容
    6. 逻辑运算
        1. //div[@id="head" and @class="s_down"]
        2. //title | //price
        
        > 注意：
        ① and无法跨标签，只能在同一个标签；
        ② | 运算前面的路径必须重新写一遍；
        > 
    
    ```python
    # 查找ul下面的li
    li_list = tree.xpath('//body/ul/li')
    
    # 查找所有有id的属性的li标签
    # text()获取标签中的内容
    li_list = tree.xpath('//ul/li[@id]/text()')
    
    # 找到id为l1的li标签，注意引号的问题，获取标签值
    li_list = tree.xpath('//ul/li[@id="l1"]/text()')
    
    # 查找到id为l1的li标签的class的属性值
    li = tree.xpath('//ul/li[@id="l1"]/@class')
    
    # 查询id中包含l的li标签，获取标签值
    li_list = tree.xpath('//ul/li[contains(@id,"l")]/text()')
    
    # 查询id的值以l开头的li标签的值
    li_list = tree.xpath('//ul/li[starts-with(@id,"c")]/text()')
    
    #查询id为l1和class为c1的li标签的值
    li_list = tree.xpath('//ul/li[@id="l1" and @class="c1"]/text()')
    
    # 查询id为l1或id为l2的li标签的值
    li_list = tree.xpath('//ul/li[@id="l1"]/text() | //ul/li[@id="l2"]/text()')
    ```
    

## （二）BeautifulSoup

1. 简介：BeautifulSoup简称bs4，和lxml一样，是一个html的解析器，主要功能也是解析和提取数据。效率没有lxml的效率高，但接口设计人性化，使用方便（语法使用了CSS选择器）。
2. 安装：`pip install bs4`
3. 基本使用
   
    ```python
    1.导入
    from bs4 import BeautifulSoup
    
    2.创建对象
    # 本地文件生成对象
    # 注意：默认打开文件的编码格式gbk所以需要指定打开编码格式
    soup = BeautifulSoup(open('1.html', encoding='utf-8'), 'lxml')
    # 服务器响应的文件生成对象
    soup = BeautifulSoup(response.read().decode(), 'lxml')
    ```
    
4. 语法
    1. 节点对象定位
       
        获取的是一个对象列表
        
        ```python
        # 1. 根据标签名查找节点
        soup.a 【注】找到的是第一个符合条件的数据a
        soup.a.name
        soup.a.attrs  # 获取标签的属性和属性值
        
        # 2. 函数
        (1).find(返回一个对象)
        find('a')  # 返回的是第一个符合条件的a标签
        find('a', title='名字')  # 根据title的值来找到对应的标签对象
        find('a', class_='名字') # 根据class的值来找到对应的标签对象，注意的是class需要添加下划线
        
        (2).find_all(返回一个列表)
        find_all('a') 查找到所有的a标签列表
        find_all(['a', 'span']) 返回所有的a和span
        find_all('a', limit=2) 只找前两个a标签
        
        (3).select选择器(根据选择器得到节点对象)【推荐】
        # select方法返回的是一个列表  并且会返回多个数据
        # 3.1 标签选择器
        （1）单个标签：element
        soup.select('a')
        （2）组合标签：element,element
        soup.select('a,span')  # 找到所有a标签和span标签的的对象
        
        # 3.2 类选择器：.class，可以通过.代表class
        soup.select('.a1')
        
        # 3.3 id选择器：#id
        soup.select('#l1')
        
        # 3.4 属性选择器：通过属性来寻找对应的标签
        [attribute]
        li = soup.select('li[class]')  # 找到带有class属性的li标签
        [attribute=value]
        li = soup.select('li[class="hengheng1"]')  # 找到class属性值为hengheng1的li标签
        
        # 3.5 层级选择器
        （1）后代选择器：element element，某标签下面的子标签，不一定是第一级
        soup.select('div li')  # 找到的是div下面的li
        （2）子代选择器：element>element，某标签的第一级子标签
        soup.select('div > ul > li')
        ```
        
    2. 节点信息
       
        通过索引获取到某个对象，接着获取对象的各个属性
        
        ```python
        1. 获取节点内容
        obj = soup.select('#d1')[0]  # 获取某个节点对象
        # 只适用于标签对象中只有内容的场景
        obj.string
        # 适用于标签对象中只有内容和嵌套标签的场景
        obj.get_text()  # **【推荐】**
        
        2. 节点的属性
        # 获取标签名，如：p表示p标签名
        obj.name
        # 将属性值作为一个字典返回，属性名为key，值为value
        obj.attrs
        
        3. 获取节点属性的值
        obj.attrs.get('title')【常用】  # 获取title属性的值（本质和字典中获取值一致）
        obj.get('title')
        obj['title']
        ```
    
5. JsonPath
    1. 作用：解析JSON文件，但只能解析本地文件，无法解析服务器响应文件，xpath和bs4两种都可以解析。
    2. 安装：`pip install jsonpath`
    3. 使用
       
        [JSONPath教程](https://blog.csdn.net/luxideyao/article/details/77802389)
        
        ```python
        obj = json.load(open('json文件', 'r', encoding='utf‐8'))
        ret = jsonpath.jsonpath(obj, 'jsonpath语法')
        ```
        

# 四、模拟浏览器

## （一）Selenium

1. 简介
    1. 什么是Selenium
        1. Selenium是一个用于Web应用程序测试的工具。
        2. Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。
        3. 支持通过各种driver（FirfoxDriver，IternetExplorerDriver，OperaDriver，ChromeDriver）驱动真实浏览器完成测试。
        4. selenium也是支持无界面浏览器操作的。
    2. 为什么使用selenium
       
        模拟浏览器功能，自动执行网页中的js代码，实现动态加载
        
    3. 如何安装selenium
        1. [操作谷歌浏览器驱动下载地址](http://chromedriver.storage.googleapis.com/index.html)
        2. [谷歌驱动和谷歌浏览器版本之间的映射表](http://blog.csdn.net/huilan_same/article/details/51896672)
        3. 查看谷歌浏览器版本：谷歌浏览器右上角‐‐>帮助‐‐>关于
        4. `pip install selenium`
2. 基本使用
   
    ```python
    # 导入
    	from selenium import webdriver
    
    # 创建谷歌浏览器操作对象
    	path = 谷歌浏览器驱动文件路径
    	browser = webdriver.Chrome(path)
    
    # 访问网址
    	url = 要访问的网址
    	browser.get(url)
    ```
    
1. selenium的元素定位
    1. 概念：自动化要做的就是模拟鼠标和键盘来操作来操作这些元素，点击、输入等等。操作这些元素前首先要找到它们，WebDriver提供很多定位元素的方法。
    2. 方法
       
        ```python
        # 1.find_element_by_id  根据id来找到对象(标签)
        	button = browser.find_element_by_id('su')
        
        # 2.find_elements_by_name  根据标签属性的属性值来获取对象
        	name = browser.find_element_by_name('wd')
        
        # 3.find_elements_by_xpath  根据xpath语句来获取对象
        	xpath1 = browser.find_elements_by_xpath('//input[@id="su"]')
        
        # 4.find_elements_by_tag_name  根据标签的名字来获取对象
        	names = browser.find_elements_by_tag_name('input')
        
        # 5.find_elements_by_css_selector  使用的bs4的语法来获取对象
        	my_input = browser.find_elements_by_css_selector('#kw')[0]
        
        # 6.find_elements_by_link_text  根据超链接名称来获取对象
        	browser.find_element_by_link_text("新闻")
        ```
    
2. 访问元素信息
   
    ```python
    # 前置操作
    	from selenium import webdriver
    	path = 'chromedriver.exe'
    	browser = webdriver.Chrome(path)
    	url = 'http://www.baidu.com'
    	browser.get(url)
    	input = browser.find_element_by_id('su')
    
    # 1. 获取标签的属性
    	print(input.get_attribute('class'))
    
    # 2. 获取标签的名字
    	print(input.tag_name)
    
    # 3. 获取元素文本
    	a = browser.find_element_by_link_text('新闻')
    	print(a.text)
    ```
    
3. 交互
    1. 点击：`click()`
    2. 输入：`send_keys()`
    3. 后退操作：`browser.back()`
    4. 前进操作：`browser.forword()`
    5. 模拟JS滚动
    `js='document.documentElement.scrollTop=100000'`
    `browser.execute_script(js)` 执行js代码
    6. 获取网页代码：`page_source`
    7. 退出：`browser.quit()`
    
    ```python
    from selenium import webdriver
    import time
    
    # 获取浏览器对象
    path = 'chromedriver.exe'
    browser = webdriver.Chrome(path)
    # url
    url = 'https://www.baidu.com'
    browser.get(url)
    time.sleep(2)
    
    # 获取文本框的对象
    input = browser.find_element_by_id('kw')
    
    # 在文本框中输入周杰伦
    input.send_keys('周杰伦')
    
    # 获取百度一下的按钮
    button = browser.find_element_by_id('su')
    
    # 点击按钮
    button.click()
    
    # 滑到底部
    js_bottom = 'document.documentElement.scrollTop=100000'
    browser.execute_script(js_bottom)
    
    # 获取下一页的按钮
    next = browser.find_element_by_xpath('//a[@class="n"]')
    
    # 点击下一页
    next.click()
    
    # 回到上一页
    browser.back()
    
    # 回去
    browser.forward()
    
    # 退出
    browser.quit()
    ```
    

## （二）Phantomjs

1. 简介
    1. 什么是Phantomjs
        1. 是一个无界面的浏览器
        2. 支持页面元素查找，JS的执行等
        3. 由于不进行CSS和GUI渲染，运行效率要比真实的浏览器要快很多
2. 使用
   
    ```python
    from selenium import webdriver
    import time
    
    # 1. 获取PhantomJS.exe文件路径path
    path = 'phantomjs.exe'
    
    # 2. 获取浏览器对象
    browser = webdriver.PhantomJS(path)
    
    url = 'https://www.baidu.com'
    browser.get(url)
    
    # 3. 保存屏幕快照
    browser.save_screenshot('baidu.png')
    
    time.sleep(2)
    
    # 交互操作和Selenium一致
    input = browser.find_element_by_id('kw')
    input.send_keys('昆凌')
    ```
    

## （三）Chrome handless

1. 简介：Chrome-headless 模式，Google 针对 Chrome 浏览器59版本新增加的一种模式，可以让你不打开UI界面的情况下使用Chrome浏览器，所以运行效果与Chrome保持完美一致。
2. 系统要求
    1. Chrome
    Unix\Linux 系统需要 chrome >= 59
    Windows 系统需要 chrome >= 60
    2. Python3.6
    3. Selenium==3.4.*
    4. ChromeDriver==2.31
3. 配置：获取浏览器对象
   
    ```python
    from selenium import webdriver
    from selenium.webdriver.chrome.options import Options
    
    # 初始化
    chrome_options = Options()
    chrome_options.add_argument('--headless')
    chrome_options.add_argument('--disable-gpu')
    
    # path是你chrome浏览器的文件路径
    path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
    chrome_options.binary_location = path
    browser = webdriver.Chrome(chrome_options=chrome_options)
    
    # 使用
    url = 'https://www.baidu.com'
    browser.get(url)
    browser.save_screenshot('baidu.png')
    ```
    

# 五、Requests

## （一）基本使用

1. 安装：`pip install requests -i https://pypi.douban.com/simple`
2. Response的1个类型6个属性
    1. 类型：models.Response
    2. 获取网站字符串源码：response.text
    3. 设置响应的编码格式：response.encoding
    4. 获取请求的url：response.url
    5. 响应的字节数据(二进制)：response.content
    6. 响应的状态码：response.status_code
    7. 响应的头信息：response.headers

## （二）Get请求

```python
import requests

url = 'https://www.baidu.com/s'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'
}
data = {'wd':'北京'}

# url  请求资源路径
# params 参数
# kwargs 字典
response = requests.get(url=url,params=data,headers=headers)
content = response.text
print(content)

# 总结：
# （1）参数使用params传递
# （2）参数无需urlencode编码
# （3）不需要请求对象的定制
# （4）请求资源路径中的？可以加也可以不加
```

## （二）Post请求

```python
import requests
import json

url = 'https://fanyi.baidu.com/sug'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'
}
data = {'kw': 'eye'}

# url 请求地址
# data 请求参数
# kwargs 字典
response = requests.post(url=url,data=data,headers=headers)
content = response.text
# 转为JSON数据，解决中文乱码
obj = json.loads(content,encoding='utf-8')
print(obj)

# 总结：
# （1）post请求是不需要编解码
# （2）post请求的参数是data
# （3）不需要请求对象的定制
```

## （三）代理

```python
import requests

url = 'http://www.baidu.com/s?'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36',
}
data = {'wd':'ip'}
proxy = {
    'http':'212.129.251.55:16816'
}
# proxy定制：在请求中设置proxies参数，参数类型是一个字典类型
response = requests.get(url = url,params=data,headers = headers,proxies = proxy)
content = response.text
with open('daili.html','w',encoding='utf-8')as fp:
    fp.write(content)
```

## （四）Cookie定制

```python
# 通过登陆  然后进入到主页面
# 通过找登陆接口我们发现 登陆的时候需要的参数很多
# _VIEWSTATE: /m1O5dxmOo7f1qlmvtnyNyhhaUrWNVTs3TMKIsm1lvpIgs0WWWUCQHl5iMrvLlwnsqLUN6Wh1aNpitc4WnOt0So3k6UYdFyqCPI6jWSvC8yBA1Q39I7uuR4NjGo=
# __VIEWSTATEGENERATOR: C93BE1AE
# from: http://so.gushiwen.cn/user/collect.aspx
# email: 595165358@qq.com
# pwd: action
# code: PId7
# denglu: 登录

# 我们观察到_VIEWSTATE   __VIEWSTATEGENERATOR  code是一个可以变化的量

# 难点:(1)_VIEWSTATE   __VIEWSTATEGENERATOR  一般情况看不到的数据 都是在页面的源码中
#     我们观察到这两个数据在页面的源码中 所以我们需要获取页面的源码 然后进行解析就可以获取了
#     (2)验证码

import requests

# 这是登陆页面的url地址
url = 'https://so.gushiwen.cn/user/login.aspx?from=http://so.gushiwen.cn/user/collect.aspx'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36'
}

# 获取页面的源码
response = requests.get(url = url,headers = headers)
content = response.text

# 解析页面源码  然后获取_VIEWSTATE   __VIEWSTATEGENERATOR
from bs4 import BeautifulSoup
soup = BeautifulSoup(content,'lxml')

# 获取_VIEWSTATE
viewstate = soup.select('#__VIEWSTATE')[0].attrs.get('value')

# 获取__VIEWSTATEGENERATOR
viewstategenerator = soup.select('#__VIEWSTATEGENERATOR')[0].attrs.get('value')

# 获取验证码图片
code = soup.select('#imgCode')[0].attrs.get('src')
code_url = 'https://so.gushiwen.cn' + code

# 有坑
# import urllib.request
# urllib.request.urlretrieve(url=code_url,filename='code.jpg')
# requests里面有一个方法 session（）  通过session的返回值 就能使用请求变成一个对象

session = requests.session()

# 验证码的url的内容
response_code = session.get(code_url)

# 注意此时要使用二进制数据  因为我们要使用的是图片的下载
content_code = response_code.content

# wb的模式就是将二进制数据写入到文件
with open('code.jpg','wb')as fp:
    fp.write(content_code)

# 获取了验证码的图片之后 下载到本地 然后观察验证码  观察之后 然后在控制台输入这个验证码 就可以将这个值给
# code的参数 就可以登陆
code_name = input('请输入你的验证码')

# 点击登陆
url_post = 'https://so.gushiwen.cn/user/login.aspx?from=http%3a%2f%2fso.gushiwen.cn%2fuser%2fcollect.aspx'
data_post = {
    '__VIEWSTATE': viewstate,
    '__VIEWSTATEGENERATOR': viewstategenerator,
    'from': 'http://so.gushiwen.cn/user/collect.aspx',
    'email': '595165358@qq.com',
    'pwd': 'action',
    'code': code_name,
    'denglu': '登录',
}
response_post = session.post(url = url, headers = headers, data = data_post)
content_post = response_post.text
with open('gushiwen.html','w',encoding= ' utf-8')as fp:
    fp.write(content_post)

# 难点
# （1） 隐藏域
# （2） 验证码
```

## （五）超级鹰打码平台使用

```python
import requests
from hashlib import md5

class Chaojiying_Client(object):

  def __init__(self, username, password, soft_id):
      self.username = username
      password =  password.encode('utf8')
      self.password = md5(password).hexdigest()
      self.soft_id = soft_id
      self.base_params = {
          'user': self.username,
          'pass2': self.password,
          'softid': self.soft_id,
      }
      self.headers = {
          'Connection': 'Keep-Alive',
          'User-Agent': 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0)',
      }

  def PostPic(self, im, codetype):
      """
      im: 图片字节
      codetype: 题目类型 参考 http://www.chaojiying.com/price.html
      """
      params = {
          'codetype': codetype,
      }
      params.update(self.base_params)
      files = {'userfile': ('ccc.jpg', im)}
      r = requests.post('http://upload.chaojiying.net/Upload/Processing.php', data=params, files=files, headers=self.headers)
      return r.json()

  def ReportError(self, im_id):
      """
      im_id:报错题目的图片ID
      """
      params = {
          'id': im_id,
      }
      params.update(self.base_params)
      r = requests.post('http://upload.chaojiying.net/Upload/ReportError.php', data=params, headers=self.headers)
      return r.json()

if __name__ == '__main__':
	# 1. 获取到验证码图片

	# 2. 超级鹰客户端，用户中心>>软件ID 生成一个替换 96001
	chaojiying = Chaojiying_Client('超级鹰用户名', '超级鹰用户名的密码', '96001')	

	# 3. 读取图片，本地图片文件路径 来替换 a.jpg 有时WIN系统须要//
	im = open('a.jpg', 'rb').read()

	# 4. 1902 验证码类型  官方网站>>价格体系 3.4+版 print 后要加()
	print(chaojiying.PostPic(im, 1902))
```

# 六、Scrapy

## （一）基本介绍

1. 简介：Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。

2. 安装
    1. 命令：`pip install scrapy`
    2. 报错
        1. Microsoft Visual C++未安装
           
            > 解决方案：[下载Microsoft Visual C++](https://blog.csdn.net/qq_33218097/article/details/129453985)，一般情况下安装下Microsoft Visual C++就好了，应该不会有其他问题。
            > 
        2. twisted未安装
           
            > 解决方案：[下载twisted对应版本](http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted)的whl文件（如我的Twisted‐17.5.0‐cp36‐cp36m‐win_amd64.whl），cp后面是
            python版本，amd64代表64位，运行命令：
            `pip install C:\Users\...\Twisted‐17.5.0‐cp36‐cp36m‐win_amd64.whl`
            > 
    
3. 项目的创建和运行
    1. 创建项目：`scrapy startproject 项目名称`
    2. 创建爬虫文件
        1. 跳转到spiders文件夹 `cd 目录名字/目录名字/spiders`
        2. `scrapy genspider 爬虫名字 网页的域名`
    3. 运行项目：`scrapy crawl 爬虫名称`
       
        > 注意：应在spiders文件夹内执行
        > 
    
4. 项目组成
    1. spiders
        1. \__init\__.py
        2. 自定义的爬虫文件.py：由我们自己创建，是实现爬虫核心功能的文件
    2. \__init\__.py
    3. items.py：定义数据结构(属性/变量)的地方，是一个继承自scrapy.Item的类
    4. middlewares.py：中间件，如代理
    5. pipelines.py：管道文件，每个类就是一个管道，用于对自定义爬虫文件传过来的数据进行后续处理。需要在settings文件中配置，默认是300优先级，值越小优先级越高（1‐1000）
    6. settings.py：配置文件，比如：是否遵守robots协议，User‐Agent定义，管道配置等
    
5. 爬虫文件
    1. 创建
        1. 跳转到spiders文件夹 `cd 目录名字/目录名字/spiders`
        2. `scrapy genspider 爬虫名字 网页的域名`
    2. 基本组成
       
        ```python
        # 继承scrapy.Spider类
        class MvSpider(scrapy.Spider):
            # 运行爬虫文件时使用的名字
        	name = "mv"
            # 爬虫允许的域名，在爬取的时候，如果不是此域名之下的url，会被过滤掉，一般不带路径
            allowed_domains = ["www.ygdy8.net"]
            # 声明了爬虫的起始地址，可以写多个url，一般是一个
            start_urls = ["https://www.ygdy8.net/html/gndy/china/index.html"]
            # 自定义全局变量
            page_num = 1
            # 解析数据的回调函数
            def parse(self, response):
                # 响应的是字符串，可能会有编码问题，可以使用json指定编码
                response.text
                # 响应的是二进制文件
                response.body
                # xpath方法的返回值类型是selector列表，具体值存储在data属性中，如图
                response.xpath()
                # 提取selector对象中的data属性值，如图
                extract()
                # 提取的是selector列表中的第一个数据
                extract_first()
        ```
        
    
6. 架构组成
    1. 引擎：自动运行，无需关注，会自动组织所有的请求对象，分发给下载器
    2. 下载器：从引擎处获取到请求对象后，请求数据
    3. Spiders：Spider类定义了如何爬取某个(或某些)网站。包括了爬取的动作(例如:是否跟进链接)以及如何从网页的内容中提取结构化数据(爬取item)。 换句话说，Spider就是您定义爬取的动作及分析某个网页(或者是有些网页)的地方。
    4. 调度器：有自己的调度规则，无需关注
    5. 管道（Item Pipeline）：最终处理数据的管道，会预留接口供我们处理数据。当Item在Spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对Item的处理。每个Item Pipeline组件(有时称之为“Item Pipeline”)是实现了简单方法的Python类。他们接收到Item并通过它执行一些行为，同时也决定此Item是否继续通过pipeline，或是被丢弃而不再进行处理。
       
        > Item Pipeline的一些典型应用：
        > 
        > 1. 清理HTML数据
        > 2. 验证爬取的数据(检查item包含某些字段)
        > 3. 查重(并丢弃)
        > 4. 将爬取结果保存到数据库中
    
7. 工作原理
   
    参见贴子附图
    
    ![FoGlxFInGgVEYKfbdjF8OR1DnxOv](D:\NOTES\typora\Python爬虫入门 \FoGlxFInGgVEYKfbdjF8OR1DnxOv.png)

![FvPvakftQO-UCKkrrHf2vcX_fawg](D:\notes\typora\Python爬虫入门 \FvPvakftQO-UCKkrrHf2vcX_fawg.png)

## （二）基本使用

1. Scrapy Shell
    1. 简介：Scrapy终端，是一个交互终端，供您在未启动spider的情况下尝试及调试您的爬取代码。 其本意是用来测试提取数据的代码，不过您可以将其作为正常的Python终端，在上面测试任何的Python代码。该终端是用来测试XPath或CSS表达式，查看他们的工作方式及从爬取的网页中提取的数据。 在编写您的spider时，该终端提供了交互性测试您的表达式代码的功能，免去了每次修改后运行spider的麻烦。一旦熟悉了Scrapy终端后，您会发现其在开发和调试spider时发挥的巨大作用。
    2. 安装Ipython：`pip install ipython`
       
        > 如果安装了 IPython ，Scrapy终端将使用 IPython替代标准Python终端。IPython 终端与其他相比更为强大，提供智能的自动补全，高亮输出，及其他特性。scrapy shell + ipython，可以进行交互式调试脚本。
        > 
    3. 应用
        - scrapy shell [www.baidu.com](http://www.baidu.com/)
        - scrapy shell [http://www.baidu.com](http://www.baidu.com/)
        - scrapy shell "[http://www.baidu.com](http://www.baidu.com/)"
        - scrapy shell "[www.baidu.com](http://www.baidu.com/)"
    4. 语法
        1. response对象
            - response.body
            - response.text
            - response.url
            - response.status
        2. response的解析
            - response.xpath()（常用）：使用xpath路径查询特定元素，返回一个selector列表对象
            - response.css()：使用css_selector查询元素，返回一个selector列表对象
                - 获取内容 ：response.css('#su::text').extract_first()
                - 获取属性 ：response.css('#su::attr(“value”)').extract_first()
        3. selector对象：通过xpath方法调用返回的是seletor列表，可以通过下标获取单个对象
            - extract()：提取selector对象的值，如果提取不到值，那么会报错。使用xpath请求到的对象是一个selector对象，需要进一步使用extract()方法拆包，转换为unicode字符串。
            - extract_first()：提取seletor列表中的第一个值。如果提取不到值，会返回一个空值。返回第一个解析到的值，如果列表为空，此种方法也不会报错，会返回一个空值。
            - xpath()
            - css()
            
            > 注意：每一个selector对象可以再次的去使用xpath()或者css()方法进行再次解析。
            > 
2. yield
    1. 简介：带有 yield 的函数不再是一个普通函数，而是一个生成器generator，可用于迭代。
    2. 理解：yield类似Java中的return关键字，迭代一次就返回yield后面的值，交给pipeline处理。并且记住这个返回的位置，下次迭代就从这个位置后(下一行)的代码开始执行
       
        > yield并不是像return关键字一样执行完就结束了，而是会继续执行下一阶段，只是将当前阶段的值先交给pipeline进行处理，也可以继续调用其他函数。
        > 
3. GET/POST请求
    1. 重写start_requests方法：`def start_requests(self)`
    2. start_requests的返回值调用解析函数
    3. 对比GET请求：`yield scrapy.**Request**(url=url, callback=self.parse_image, **meta**={'name': self.name})`
    
    > POST请求和GET请求不一样，初始化爬虫文件的起始URL和解析函数没有用，因为起始URL并不能直接访问，需要带参访问才行。GET和POST请求不同之处主要如下：
    ① 请求开始的方法不同；
    ② 发起请求的函数不同——`scrapy.**Request**`和`scrapy.**FormRequest**`；
    ③ 函数参数名称不同——`**meta**`和`formdata`；
    > 
    
    ```python
    import scrapy
    import json
    
    class TestpostSpider(scrapy.Spider):
        name = "testpost"
        allowed_domains = ["fanyi.baidu.com"]
        # start_urls = ["http://fanyi.baidu.com/"]
        #
        # def parse(self, response):
        #     pass
    
        def start_requests(self):
            url = 'https://fanyi.baidu.com/sug'
            data = {'kw': 'final'}
            yield scrapy.**FormRequest**(url=url, **formdata**=data, callback=self.parse_second)
    
        def parse_second(self, response):
            content = response.text
    		# Python3的loads函数好像不能指定encoding='utf-8'，报错TypeError: JSONDecoder.__init__() got an unexpected keyword argument 'encoding'
            # 默认utf-8编码，如果其他编码，不知道怎么指定了
    		obj = json.loads(content)
            print(obj)
    ```
    
4. 实战应用
    1. yield：返回数据或调用函数
        1. 返回数据给管道；
        2. 调用函数
           1. 调用本身；
           2. 调用其他函数；
        
        
        ```python
        # 1. 调用本身：实现多页数据下载，不同页面解析逻辑相同，当当网实例
        import scrapy
        from scrapy_dangdang_095.items import ScrapyDangdang095Item
        
        class DangSpider(scrapy.Spider):
        	name = "dang"
        	allowed_domains = ["category.dangdang.com"]
        	# 起始url
        	start_urls = ["http://category.dangdang.com/cp01.01.02.00.00.00.html"]
        	base_url = 'http://category.dangdang.com/pg'
        	page_num = 1
        	# http://category.dangdang.com/pg2-cp01.01.02.00.00.00.html
        
        	def parse(self, response):
        		protocol = 'https:'
        		# 元素定位
        		li_list = response.xpath('//ul[@id="component_59"]/li')
        		for li in li_list:
        			# 图片懒加载，获取data-original的属性值
        			src = li.xpath('.//img/@data-original').extract_first()
        			if src == None:
        				# 第一本书没有data-original懒加载属性，获取src属性值
        				src = li.xpath('.//img/@src').extract_first()
        			src = protocol + src
        			name = li.xpath('.//img/@alt').extract_first()
        			price = li.xpath('.//p[@class="price"]/span/text()').extract_first()
        			book = ScrapyDangdang095Item(src=src, name=name, price=price)
        			# 将数据传给pipeline处理
        			yield book
        		# 如果页码小于100，继续生成下一页地址，循环调用
        		if self.page_num < 100:
        			self.page_num += 1
        			url = self.base_url + str(self.page_num) + '-cp01.01.02.00.00.00.html'
        			yield scrapy.Request(url=url, callback=self.parse)
        
        # 2. 调用其他函数：一个item包含多级页面的数据，不同页面解析逻辑不同，层层嵌套：电影天堂实例
        # scrapy.Request就是scrpay的get请求
        # url就是下一个请求的请求地址
        # callback是你要执行的那个函数  注意不需要加（）
        # meta就是要传给下一个函数的数据，type是字典
        yield scrapy.Request(url=url, callback=self.parse_image, meta={'name': self.name})
        
        import scrapy
        from scrapy_dytt_099.items import ScrapyDytt099Item
        
        class MvSpider(scrapy.Spider):
            name = "mv"
            allowed_domains = ["www.ygdy8.net"]
            start_urls = ["https://www.ygdy8.net/html/gndy/china/index.html"]
        
            # 请求首页获取到电影名称和图片地址
            def parse(self, response):
                name_list = response.xpath('//div[@class="co_content8"]//table//a[2]/text()')
                src_list = response.xpath('//div[@class="co_content8"]//table//a[2]/@href')
                for i in range(len(name_list)):
                    self.name = name_list[i].extract()
                    href = src_list[i].extract()
                    url = 'https://www.ygdy8.net' + href
                    # 调用请求图片函数，传入下一个页面URL，电影名称（为了下一步封装movie实体）
                    yield scrapy.Request(url=url, callback=self.parse_image, meta={'name': self.name})
        	
            # 请求图片地址，获取图片
            def parse_image(self, response):
                # 从页面解析出图片地址
                src = response.xpath('//div[@id="Zoom"]//img/@src').extract_first()
                name = response.meta['name']
                # 封装movie实体，传给管道
                movie = ScrapyDytt099Item(src=src, name = name)
                yield movie
        
        ```
        
    2. pipeline：下载数据，保存数据
        1. 需要提前在settings文件中开启管道ITEM_PIPELINES，优先级1-1000，值越小，优先级越高
        2. item参数就是自定义脚本中yield传过来的值，且item是一个对象，非字符串；
        3. 保存数据的时候，open方法‘w’模式会每一个对象都打开一次文件，覆盖之前的内容，如果追加，需要用’a’模式，即append
        4. 多条管道：① 定义管道类；② 在settings中开启管道；
        
        ```python
        # 当当网实例
        # 管道1：默认管道，将数据写入json文件，包括书名、价格、图片地址
        class ScrapyDangdang095Pipeline:
            # 在爬虫文件开始的之前就执行的一个方法
            def open_spider(self,spider):
                self.fp = open('book.json','w',encoding='utf-8')
            # 爬虫文件的主流程
            def process_item(self, item, spider):
                self.fp.write(str(item))
                return item
            # 在爬虫文件执行完之后  执行的方法
            def close_spider(self,spider):
                self.fp.close()
        
        # 管道2：自定义管道，下载书籍图片数据
        class ScrapyDownloadPipeline:
            def process_item(self, item, spider):
                url = item.get('src')
                filename = './book_images/' + item.get('name') + '.jpg'
                # print(url, filename)
                urllib.request.urlretrieve(url= url, filename= filename)
                return item
        ```
        
    3. item：相当于一个实体类，定义属性和数据结构，即要下载的数据有哪些，定义下变量
        1. 导入item实体类：from 项目名.items import 类名(items.py中的类名)
           
            > 导入资源的时候，通过from可以省去方法或对象前面的类路径。
            > 
        
        ```python
        # 当当网实例：
        import scrapy
        class ScrapyDangdang095Item(scrapy.Item):
            # 定义属性
            # 图片地址
            src = scrapy.Field()
            # 书名
            name = scrapy.Field()
            # 价格
            price = scrapy.Field()
        
        # 在自定义爬虫文件中调用，封装各个属性为item中的实体类
        book = ScrapyDangdang095Item(src=src, name=name, price=price)
        ```
        
    4. 层层嵌套封装item：parse调用方法1，传入下一个url，方法1调用方法2，传入下一个url……

## （三）高级应用

1. CrawlSpider
    1. 简介：链接提取器，继承自scrapy.Spider。可以定义链接提取规则，在解析html内容的时候，根据链接规则提取出指定的链接，然后再向这些链接发送请求。所以，如果有需要跟进链接的需求，意思就是爬取了网页之后，需要提取链接再次爬取，使用CrawlSpider是非常合适的。
    2. 语法
       
        ```python
        # 创建链接提取器，设置提取规则。一般不用自己写，创建CrawlSpider项目的时候已经自动设置好了
        scrapy.linkextractors.LinkExtractor(
        		allow = (), # 正则表达式 提取符合正则的链接（常用）
        		deny = (), # 正则表达式 不提取符合正则的链接（基本不用）
        		allow_domains = (), # 允许的域名（基本不用）
        		deny_domains = (), # 不允许的域名（基本不用）
        		restrict_xpaths = (), # xpath，提取符合xpath规则的链接
        		restrict_css = () # 提取符合选择器规则的链接
        )
        
        # 模拟使用
        from scrapy.linkextractors import LinkExtractor
        links1 = LinkExtractor(allow=r'list_23_\d+\.html')  # 正则用法
        links2 = LinkExtractor(restrict_xpaths=r'//div[@class="x"]')  # xpath用法
        links3 = LinkExtractor(restrict_css='.x')  # css用法
        
        # 提取连接
        links1.extract_links(response)
        ```
        
    3. 实际应用：读书网
        1. allow：链接提取规则
           
            > 注意：起始URL也要符合提取规则，否则不会进行链接提取
            > 
        2. callback：执行的提取逻辑，只能写函数名字符串。
           
            > 注意：在基本的spider中，如果重新发送请求，需要使用yield调用函数并传入新的url，那里的callback写的是 callback=self.parse_item，函数名而非字符串
            > 
        3. follow：是否跟进提取，即当页数不确定时，是否越过当前页面看到的页数向下提取。如：读书网第一页只能看到前13页的索引，页面源码中也只能提取出前13页的链接，如果想要再提取13页之后的链接，需要设置为True表示跟进提取。
        
        ```python
        import scrapy
        from scrapy.linkextractors import LinkExtractor
        from scrapy.spiders import CrawlSpider, Rule
        from scrapy_readbook_101.items import ScrapyReadbook101Item
        
        class ReadSpider(CrawlSpider):
            name = "read"
            allowed_domains = ["www.dushu.com"]
            start_urls = ["http://www.dushu.com//book/1158_1.html"]
        
            rules = (Rule(LinkExtractor(allow=r"/book/1158_\d+\.html"),
                                        callback="parse_item",
                                        follow=True),)
        
            def parse_item(self, response):
                book_list = response.xpath('//div[@class="bookslist"]//img')
                for book in book_list:
                    name = book.xpath('./@alt').extract_first()
                    src = book.xpath('./@src').extract_first()
                    book = ScrapyReadbook101Item(name=name, src=src)
                    yield book
        ```
    
2. 数据入库
    1. 简介：通过Pipeline处理提取到的数据，将数据通过pymysql库保存到数据库；
    2. 安装：`pip install pymysql -i https://pypi.douban.com/simple`
    3. 实际应用：读书网
       
        ```python
        1. settings.py文件配置
        # 配置数据库连接参数：主机、端口、用户名、密码、数据库名、字符集
        DB_HOST = "192.168.15.134"
        DB_PORT = 3306  # 端口号需要是整数
        DB_USER = 'root'
        DB_PASSWORD = 'root'
        DB_NAME = 'spider'
        DB_CHARSET = 'utf8'  # 字符集不能带“-”
        
        2. pipelines.py文件
        from itemadapter import ItemAdapter
        from scrapy.utils.project import get_project_settings
        import pymysql
        
        # 默认管道：正常保存数据到本地JSON文件
        class ScrapyReadbook101Pipeline:
            def open_spider(self, spider):
                self.fp = open('book.json', 'w', encoding='utf-8')
            def process_item(self, item, spider):
                self.fp.write(str(item))
                return item
            def close_spider(self, spider):
                self.fp.close()
        
        # 自定义管道：数据入库
        class MysqlPipeline:
            # 获取settings配置文件中的配置信息
            def open_spider(self, spider):
                settings = get_project_settings()
                self.host = settings['DB_HOST']
                self.port = settings['DB_PORT']
                self.user = settings['DB_USER']
                self.password = settings['DB_PASSWORD']
                self.name = settings['DB_NAME']
                self.charset = settings['DB_CHARSET']
                self.connect()
        		# 建立数据库连接
            def connect(self):
                # 获取数据库连接
                self.conn = pymysql.connect(host=self.host, port=self.port, user=self.user, password=self.password, db=self.name,
                                          charset=self.charset)
                # 获取执行数据库操作的对象
                self.cursor = self.conn.cursor()
        
            def process_item(self, item, spider):
                # 构建sql语句
                sql = 'insert into spider01(name, src) values ("{}", "{}")'.format(item['name'], item['src'])
                # 执行sql
                self.cursor.execute(sql)
                # 提交
                self.conn.commit()
                return item
        
            def close_spider(self, spider):
                # 关闭连接
                self.conn.close()
                self.cursor.close()
        ```
        

## （四）其他

1. 日志
    1. 日志级别
        - CRITICAL：严重错误
        - ERROR： 一般错误
        - WARNING： 警告
        - INFO: 一般信息
        - DEBUG： 调试信息
        
        > 默认的日志等级是DEBUG，只要出现了DEBUG或者DEBUG以上等级的日志，那么这些日志将会打印。
        > 
    2. settings.py文件设置
        - LOG_FILE : 将屏幕显示的信息全部记录到文件中，屏幕不再显示，注意文件后缀一定是.log。如：`LOG_FILE=’logdemo.log’`
        - LOG_LEVEL : 设置日志显示的最低等级。如：`LOG_LEVEL=’WARNING’`
2. 代理
    1. 在settings.py中，打开一个选项
       
        ```python
        DOWNLOADER_MIDDLEWARES = {
            'postproject.middlewares.Proxy': 543,
        }
        ```
        
    2. 在middlewares.py中写代码
       
        ```python
        def process_request(self, request, spider):
            request.meta['proxy'] = '[https://113.68.202.10:9999](https://113.68.202.10:9999/)'
            return None
        ```