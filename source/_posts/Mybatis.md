---
title: Mybatis
tags:
  - Java
  - Mybatis
  - 笔记
categories:
  - Java
  - Mybatis
cover: https://image.runtimes.cc/202404051530595.jpeg
abbrlink: 13919
date: 2022-11-19 18:25:00
updated: 2023-08-17 20:56:53
---

# 教程

> 参考:
>
> [官方文档](https://mybatis.org/mybatis-3/)
>
> [源码](https://github.com/mybatis)

## Provider的使用

1.一定要注意type的类名.class 和 method方法名,还要注意形参也得是一样的

2.Provider的方法,大概就三个方法sql.SELECT,sql.WHERE,sql.FROM

3.SQL 对象里的方法名跟别的不一样,小写的不行,idea也识别不到,要用大写,比如SLELECT

4.Provider里返回的是String

![](https://image.runtimes.cc/202404051427670.png)

![](https://image.runtimes.cc/202404051427490.png)

## 注解 一对多查询(多个参数)

![](https://image.runtimes.cc/202404051427839.png)

![](https://image.runtimes.cc/202404051428785.png)

![](https://image.runtimes.cc/202404051429441.png)

## 注解 一对多查询(多个参数)

```java
@SelectProvider(method="queryPageFloors",type=BarterGroupProvider.class)
@Results({
        @Result(property = "userId",column="user_id"),
        @Result(property = "floorNum",column="floor_num"),
        @Result(property = "floorSecond",column="id"),
        @Result(property = "say",column="note"),
        @Result(property = "joinOrPublish",column="join_or_publish"),
        @Result(property="categorys",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.queryCategory")),
        @Result(property="productIds",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.queryProducts")),
        @Result(property="beforeResult",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupEvaluationMapper.queryAll")),
        @Result(property="barterGroupUser",javaType = BarterGroupUser.class,column="{user_id=user_id,id = barter_group_id }",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.isHead")),
        @Result(property="futureResultNum",column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.futureResultNum"))
})
List<QueryFloors> queryFloors3(Page<QueryFloors> page);

/**
 * 分类
 * @param id
 * @return
 */
@Select("SELECT * FROM ecloud_marketing.barter_group_category WHERE barter_group_details_id = #{id} limit 0,7 ")
List<BarterGroupCategory> queryCategory(@Param("id") Long id);

/**
 * 产品
 * @param id
 * @return
 */
@Select("SELECT product_id from ecloud_marketing.barter_group_product_category WHERE barter_group_details_id = #{id} limit 0,3 ")
List<Long> queryProducts(@Param("id") Long id);

/**
 * 团员
 * @param userId
 * @param id
 * @return
 */
@Select("SELECT * FROM ecloud_marketing.barter_group_user WHERE user_id =#{user_id} and barter_group_id = #{id} and status = 1")
BarterGroupUser isHead(Map<String,Object> map);

@Select("SELECT count(*) from ecloud_marketing.barter_group_evaluation WHERE barter_group_details_id = #{id}")
Integer futureResultNum(@Param("id") Long id);
```

```java
public class QueryFloors implements Serializable {

    @ApiModelProperty("我有的产品的id")
    List<ProductBarter> list;

    @ApiModelProperty("封装前的评论数据")
    private List<BarterGroupEvaluation> beforeResult;

    @ApiModelProperty("封装后的评论数据有分页给功能")
    private Page<BarterGroupEvaluation> beforeResultPage;

    @ApiModelProperty("封装后的评论数据")
    private List<BarterGroupEvaluationResult> futureResult;

    @ApiModelProperty("剩余评论条数")
    private Integer futureResultNum;

    @ApiModelProperty("分类")
    List<BarterGroupCategory> categorys;

    @ApiModelProperty(value="楼层数")
    private Integer floorNum;

    @ApiModelProperty(value="楼层id")
    private Long floorSecond;

    @ApiModelProperty(value="要说的")
    private String say;

    @ApiModelProperty(value="加入或者是发布")
    private Integer joinOrPublish;

    @ApiModelProperty("会员表")
    private BarterGroupUser barterGroupUser;

    @ApiModelProperty(value="加入时间")
    private Long joinData;
}
```

## 注解 一对多查询(一个参数)

![](https://image.runtimes.cc/202404051430732.png)

![](https://image.runtimes.cc/202404051430867.png)

## 标签

mybatis的xml的常用标签:

**include**和**sql**标签

```xml
	<sql id="query_column">
		id,user_no
	</sql>

	<select id="test">
		select <include refid="query_column"></include>
		from user_info
	</select>
```

**where标签**

```xml
<select id="test">
  select * from user_info
  where
  <if test="userName != null and userName != ''">
    user_name = #{userName}
  </if>
  <if test="password != null and password != ''">
    and password = #{password}
  </if>
</select>
```
如果userName= null，则sql语句就变成
```sql
select * from user_info where and password = #{password}
```

`where`标签可以去除where语句中的第一个and 或 or。

```xml
<select id="test">
  select * from user_info
  <where>
    <if test="userName != null and userName != ''">
      and user_name = #{userName}
    </if>
    <if test="password != null and password != ''">
      and password = #{password}
    </if>
  </where>
</select>
```

**set标签**

```xml
<update id="myupdate">
    update user_info
    <set>
      <if test="userName != null">
        user_name = #{userName},
      </if>
      <if test="password != null">
        password = #{password,jdbcType=VARCHAR},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
```

**trim标签**

`where`标签只能去除第一个and或or，不够灵活，trim标签功能更加强大。
 trim标签的属性如下：

- **prefix**  在trim标签内容前面加上前缀
- **suffix** 在trim标签内容后面加上后缀
- **prefixOverrides** 移除前缀内容。即 属性会忽略通过管道分隔的文本序列，多个忽略序列用 “|” 隔开。它带来的结果就是所有在 prefixOverrides 属性中指定的内容将被移除。
- **suffixOverrides** 移除前缀内容。

```xml
<trim prefix="where" prefixOverrides="and">
    <if test="userId != null">
        user_id=#{userId}
    </if>
   <if test="pwd != null and pwd !=''">
        user_id=#{userId}
    </if>
</trim>
```

**foreach标签**

foreach元素的属性主要有item，index，collection，open，separator，close.

- **collection** 要做foreach的对象，作为入参时，List对象默认用"list"代替作为键，数组对象有"array"代替作为键，Map对象没有默认的键。当然在作为入参时可以使用@Param("keyName")来设置键，设置keyName后，list,array将会失效。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果User有属性List ids。入参是User对象，那么这个collection = "ids".如果User有属性Ids ids;其中Ids是个对象，Ids有个属性List id;入参是User对象，那么collection = "[ids.id](https://links.jianshu.com/go?to=http%3A%2F%2Fids.id)"，必填
- **item** 合中元素迭代时的别名，必选
- **index** 在list和数组中,index是元素的序号，在map中，index是元素的key，可选
- **open** foreach代码的开始符号，一般是(和close=")"合用。常用在in(),values()时。可选
- **separator** 元素之间的分隔符，例如在in()的时候，separator=","会自动在元素中间用“,“隔开，避免手动输入逗号导致sql错误，如in(1,2,)这样，可选。
- **close** foreach代码的关闭符号，一般是)和open="("合用。常用在in(),values()时。可选

**choose，when，otherwise标签**

功能有点类似于Java中的 swicth - case - default

```xml
<select id="getUser" resultType="com.cat.pojo.User">
  SELECT * FROM user 
  <where>
  <choose>
    <when test="id != null and test.trim() != '' ">
      id = #{id}
    </when> 
    <when test="name != null and name.trim() != '' ">
      name = #{name}
    </when> 
    <otherwise>
      age = 17  
    </otherwise>
  </choose>
</where>
</select>
```

**if标签**

```xml
<!-- 判断字符串-->
<if test="item != null and item != ''"></if>

<!-- 如果是Integer类型的需要把and后面去掉或是加上or-->
<if test="item != null"></if>
<if test="item != null and item != '' or item == 0"></if>
```

## 存过/函数

存过

```xml
<select id="pNextSupperUsers" parameterType="map" statementType="CALLABLE" resultType="vo.UserAgentVO">
    {
        call p_next_supper_users(
            #{type,mode=IN,jdbcType=INTEGER},
            #{userId,mode=IN,jdbcType=BIGINT}
        )
        }
</select>
```

```java
List<UserAgentVO> pNextSupperUsers(Map<String, Object> param);

Map<String, Object> param = new HashMap<>();
param.put("type", 1);
param.put("userId", userId);
List<UserAgentVO> list = userInfoMapper.pNextSupperUsers(param);
```

函数

```sql
SELECT
  fn_next_user_count ( 1, u.id ) AS teamCount,
FROM
user_info
```

# 源码

## 参考

> [b站博学谷](https://www.bilibili.com/video/BV1R14y1W7yS?p=1&vd_source=6f5cde8de02ba80d9974b12cabbbdddb)

架构设计

![img](https://image.runtimes.cc/202404051430309.png)

## 启动测试方法

```java
# 第一种调用方法
public static void test(){
    String resource = "com/analyze/mybatis/mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    Map map = sqlSession.selectOne("com.analyze.mybatis.mapper.UserMapper.getUA");
    sqlSession.close();
}


# 第二种调用方法
public static void test2(){
    String resource = "com/analyze/mybatis/mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    Map map = userMapper.getUA();
    sqlSession.close();
}
```

下面的代码,大概意思就是能加载的配置文件的信息,解释

`InputStream inputStream = Resources.getResourceAsStream(resource);`这行代码的作用

## 读取mybatis的配置文件

```java
// org.apache.ibatis.io.Resources#getResourceAsStream(java.lang.String)
public static InputStream getResourceAsStream(String resource) throws IOException {
  return getResourceAsStream(null, resource);
}

// org.apache.ibatis.io.Resources#getResourceAsStream(java.lang.ClassLoader, java.lang.String)
public static InputStream getResourceAsStream(ClassLoader loader, String resource) throws IOException {
  InputStream in = classLoaderWrapper.getResourceAsStream(resource, loader);
  if (in == null) {
    throw new IOException("Could not find resource " + resource);
  }
  return in;
}

// org.apache.ibatis.io.ClassLoaderWrapper#getResourceAsStream(java.lang.String, java.lang.ClassLoader)
public InputStream getResourceAsStream(String resource, ClassLoader classLoader) {
  return getResourceAsStream(resource, getClassLoaders(classLoader));
}

// org.apache.ibatis.io.ClassLoaderWrapper#getClassLoaders
ClassLoader[] getClassLoaders(ClassLoader classLoader) {
    return new ClassLoader[]{
        classLoader,
        defaultClassLoader,
        Thread.currentThread().getContextClassLoader(),
        getClass().getClassLoader(),
        systemClassLoader};
}

// org.apache.ibatis.io.ClassLoaderWrapper#getResourceAsStream(java.lang.String, java.lang.ClassLoader[])
// 找到一个可以用的Classloader
InputStream getResourceAsStream(String resource, ClassLoader[] classLoader) {
  for (ClassLoader cl : classLoader) {
    if (null != cl) {

      // try to find the resource as passed
      InputStream returnValue = cl.getResourceAsStream(resource);

      if (null == returnValue) {
        returnValue = cl.getResourceAsStream("/" + resource);
      }

      if (null != returnValue) {
        return returnValue;
      }
    }
  }
  return null;
}
```

下面的代码,解释`SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);`,用来解析全局配置文件和解析mapper文件

下面是解析全局配置文件

## 解析全局配置文件

```java
// org.apache.ibatis.session.SqlSessionFactoryBuilder#build(java.io.InputStream)
public SqlSessionFactory build(InputStream inputStream) {
  return build(inputStream, null, null);
}

// org.apache.ibatis.session.SqlSessionFactoryBuilder#build(java.io.InputStream, java.lang.String, java.util.Properties)
public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
  try {
    // 1.创建XpathParse解析器对象,根据inputStream解析成Document对象
    // 2.创建全局配置Configuration对象
    // 使用构建者模式,好处降低偶尔,分离复杂对象的创建
    // 构建XMLConfig
    XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
    
    // 根据Xpath解析xml配置文件,将配置文件封装到Configuration对象
    // 返回DefaultSqlSessionFactory对象
    // parse() 就是配置文件解析完成了
    return build(parser.parse());
  } catch (Exception e) {
    throw ExceptionFactory.wrapException("Error building SqlSession.", e);
  } finally {
    ErrorContext.instance().reset();
    try {
      inputStream.close();
    } catch (IOException e) {
      // Intentionally ignore. Prefer previous error.
    }
  }
}

// org.apache.ibatis.session.SqlSessionFactoryBuilder#build(org.apache.ibatis.session.Configuration)
// 最终返回
public SqlSessionFactory build(Configuration config) {
  return new DefaultSqlSessionFactory(config);
}

// org.apache.ibatis.builder.xml.XMLConfigBuilder#XMLConfigBuilder(java.io.InputStream, java.lang.String, java.util.Properties)
public XMLConfigBuilder(InputStream inputStream, String environment, Properties props) {
  // 点this,查看代码
  this(new XPathParser(inputStream, true, props, new XMLMapperEntityResolver()), environment, props);
}

// org.apache.ibatis.builder.xml.XMLConfigBuilder#XMLConfigBuilder(org.apache.ibatis.parsing.XPathParser, java.lang.String, java.util.Properties)
private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
  // 创建Configuration对象，并通过TypeAliasRegistry注册一些Mybatis内部相关类的别名
  super(new Configuration());
  ErrorContext.instance().resource("SQL Mapper Configuration");
  this.configuration.setVariables(props);
  this.parsed = false;
  this.environment = environment;
  this.parser = parser;
}

// org.apache.ibatis.parsing.XPathParser#XPathParser(java.io.InputStream, boolean, java.util.Properties, org.xml.sax.EntityResolver)
public XPathParser(InputStream inputStream, boolean validation, Properties variables, EntityResolver entityResolver) {
  commonConstructor(validation, variables, entityResolver);
  // 创建解析器
  this.document = createDocument(new InputSource(inputStream));
}

// org.apache.ibatis.parsing.XPathParser#createDocument
// 不用太关系,只是创建一个解析器的对象,顺便检查xml文档有没有写错
private Document createDocument(InputSource inputSource) {
    // important: this must only be called AFTER common constructor
    try {
      DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
      factory.setValidating(validation);

      factory.setNamespaceAware(false);
      factory.setIgnoringComments(true);
      factory.setIgnoringElementContentWhitespace(false);
      factory.setCoalescing(false);
      factory.setExpandEntityReferences(true);

      DocumentBuilder builder = factory.newDocumentBuilder();
      builder.setEntityResolver(entityResolver);
      builder.setErrorHandler(new ErrorHandler() {
        @Override
        public void error(SAXParseException exception) throws SAXException {
          throw exception;
        }

        @Override
        public void fatalError(SAXParseException exception) throws SAXException {
          throw exception;
        }

        @Override
        public void warning(SAXParseException exception) throws SAXException {
        }
      });
      // 解析器的源码了,跟mybatis没有关系了
      return builder.parse(inputSource);
    } catch (Exception e) {
      throw new BuilderException("Error creating document instance.  Cause: " + e, e);
    }
  }

// org.apache.ibatis.builder.xml.XMLConfigBuilder#parse
public Configuration parse() {
  if (parsed) {
    // 每一个配置文件,只能解析一次
    throw new BuilderException("Each XMLConfigBuilder can only be used once.");
  }
  parsed = true;
  
  // 从根节点开始解析,最终封装到Configuration对象
  parseConfiguration(parser.evalNode("/configuration"));
  return configuration;
}

// org.apache.ibatis.parsing.XPathParser#evalNode(java.lang.String)
public XNode evalNode(String expression) {
  return evalNode(document, expression);
}

// org.apache.ibatis.parsing.XPathParser#evalNode(java.lang.Object, java.lang.String)
public XNode evalNode(Object root, String expression) {
  Node node = (Node) evaluate(expression, root, XPathConstants.NODE);
  if (node == null) {
    return null;
  }
  return new XNode(this, node, variables);
}

// org.apache.ibatis.builder.xml.XMLConfigBuilder#parseConfiguration
// 可以配置这些信息:https://mybatis.org/mybatis-3/zh/configuration.html
private void parseConfiguration(XNode root) {
  try {
    //issue #117 read properties first
    propertiesElement(root.evalNode("properties"));
    Properties settings = settingsAsProperties(root.evalNode("settings"));
    loadCustomVfs(settings);
    loadCustomLogImpl(settings);
    typeAliasesElement(root.evalNode("typeAliases"));
    // 插件
    pluginElement(root.evalNode("plugins"));
    objectFactoryElement(root.evalNode("objectFactory"));
    objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
    reflectorFactoryElement(root.evalNode("reflectorFactory"));
    settingsElement(settings);
    // read it after objectFactory and objectWrapperFactory issue #631
    // 重点
    environmentsElement(root.evalNode("environments"));
    databaseIdProviderElement(root.evalNode("databaseIdProvider"));
    typeHandlerElement(root.evalNode("typeHandlers"));
    // 重点
    mapperElement(root.evalNode("mappers"));
  } catch (Exception e) {
    throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
  }
}

// org.apache.ibatis.builder.xml.XMLConfigBuilder#mapperElement
private void mapperElement(XNode parent) throws Exception {
  if (parent != null) {
    for (XNode child : parent.getChildren()) {
      if ("package".equals(child.getName())) {
        String mapperPackage = child.getStringAttribute("name");
        configuration.addMappers(mapperPackage);
      } else {
        String resource = child.getStringAttribute("resource");
        String url = child.getStringAttribute("url");
        String mapperClass = child.getStringAttribute("class");
        // 三个值是互斥的
        if (resource != null && url == null && mapperClass == null) {
          ErrorContext.instance().resource(resource);
          InputStream inputStream = Resources.getResourceAsStream(resource);

          XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, resource, configuration.getSqlFragments());
          mapperParser.parse();
        } else if (resource == null && url != null && mapperClass == null) {
          ErrorContext.instance().resource(url);
          // 专门用来解析mapper映射文件
          InputStream inputStream = Resources.getUrlAsStream(url);
          // 重点
          XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, url, configuration.getSqlFragments());
          mapperParser.parse();
        } else if (resource == null && url == null && mapperClass != null) {
          Class<?> mapperInterface = Resources.classForName(mapperClass);
          configuration.addMapper(mapperInterface);
        } else {
          throw new BuilderException("A mapper element may only specify a url, resource or class, but not more than one.");
        }
      }
    }
  }
}
```
解释下environment作用,就是`<environment id="development">`这里的,不同的环境不同的配置

```xml
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://192.168.3.12:3306/orangedb"/>
                <property name="username" value="root"/>
                <property name="password" value="abcd2022"/>
            </dataSource>
        </environment>

        <environment id="test">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://192.168.3.12:3306/orangedb"/>
                <property name="username" value="root"/>
                <property name="password" value="abcd2022"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

用法:如下代码

```java
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream,"development");
```

下面是解析mapper文件,配置的属性,参考:`https://mybatis.org/mybatis-3/zh/sqlmap-xml.html`

## 解析mapper配置文件

```java
// org.apache.ibatis.builder.xml.XMLMapperBuilder#XMLMapperBuilder(java.io.InputStream, org.apache.ibatis.session.Configuration, java.lang.String, java.util.Map<java.lang.String,org.apache.ibatis.parsing.XNode>)
// 这个方法,前面已经用过了
  public XMLMapperBuilder(InputStream inputStream, Configuration configuration, String resource, Map<String, XNode> sqlFragments) {
    this(new XPathParser(inputStream, true, configuration.getVariables(), new XMLMapperEntityResolver()),
        configuration, resource, sqlFragments);
  }

// org.apache.ibatis.builder.xml.XMLMapperBuilder#parse
public void parse() {
  // mapper映射文件是否已经加载过
  if (!configuration.isResourceLoaded(resource)) {
    // 从根节点解析
    configurationElement(parser.evalNode("/mapper"));
    // 标记已经解析
    configuration.addLoadedResource(resource);
    // 为命名空间绑定映射
    bindMapperForNamespace();
  }

  // 
  parsePendingResultMaps();
  parsePendingCacheRefs();
  parsePendingStatements();
}


// org.apache.ibatis.builder.xml.XMLMapperBuilder#configurationElement
private void configurationElement(XNode context) {
  try {
    // 获取命名空间
    String namespace = context.getStringAttribute("namespace");
    // 命名空间不能为空
    if (namespace == null || namespace.equals("")) {
      throw new BuilderException("Mapper's namespace cannot be empty");
    }
    // 设置当前的命名空间的值
    // 构建mappedStatement对象
    builderAssistant.setCurrentNamespace(namespace);
    cacheRefElement(context.evalNode("cache-ref"));
    cacheElement(context.evalNode("cache"));
    parameterMapElement(context.evalNodes("/mapper/parameterMap"));
    resultMapElements(context.evalNodes("/mapper/resultMap"));
    sqlElement(context.evalNodes("/mapper/sql"));
    // 重点
    buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
  } catch (Exception e) {
    throw new BuilderException("Error parsing Mapper XML. The XML location is '" + resource + "'. Cause: " + e, e);
  }
}

// org.apache.ibatis.builder.xml.XMLMapperBuilder#buildStatementFromContext(java.util.List<org.apache.ibatis.parsing.XNode>)
private void buildStatementFromContext(List<XNode> list) {
  if (configuration.getDatabaseId() != null) {
    buildStatementFromContext(list, configuration.getDatabaseId());
  }
  // 没有配置过getDatabaseId,所以走这里
  buildStatementFromContext(list, null);
}

// org.apache.ibatis.builder.xml.XMLMapperBuilder#buildStatementFromContext(java.util.List<org.apache.ibatis.parsing.XNode>, java.lang.String)
private void buildStatementFromContext(List<XNode> list, String requiredDatabaseId) {
  for (XNode context : list) {
    final XMLStatementBuilder statementParser = new XMLStatementBuilder(configuration, builderAssistant, context, requiredDatabaseId);
    try {
      statementParser.parseStatementNode();
    } catch (IncompleteElementException e) {
      configuration.addIncompleteStatement(statementParser);
    }
  }
}

// org.apache.ibatis.builder.xml.XMLStatementBuilder#parseStatementNode
public void parseStatementNode() {
    String id = context.getStringAttribute("id");
    String databaseId = context.getStringAttribute("databaseId");

	  // 没有设置过databaseId
    if (!databaseIdMatchesCurrent(id, databaseId, this.requiredDatabaseId)) {
      return;
    }

    String nodeName = context.getNode().getNodeName();
  
  	// 解析Sql命令类型是什么,确实是 Select,update,insert,delete 类型
    SqlCommandType sqlCommandType = SqlCommandType.valueOf(nodeName.toUpperCase(Locale.ENGLISH));
    boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
    boolean flushCache = context.getBooleanAttribute("flushCache", !isSelect);
    boolean useCache = context.getBooleanAttribute("useCache", isSelect);
    boolean resultOrdered = context.getBooleanAttribute("resultOrdered", false);

    // Include Fragments before parsing
    XMLIncludeTransformer includeParser = new XMLIncludeTransformer(configuration, builderAssistant);
    includeParser.applyIncludes(context.getNode());

    String parameterType = context.getStringAttribute("parameterType");
    Class<?> parameterTypeClass = resolveClass(parameterType);

	  // 配置语言驱动
    String lang = context.getStringAttribute("lang");
    LanguageDriver langDriver = getLanguageDriver(lang);

    // Parse selectKey after includes and remove them.
    processSelectKeyNodes(id, parameterTypeClass, langDriver);

    // Parse the SQL (pre: <selectKey> and <include> were parsed and removed)
    KeyGenerator keyGenerator;
    String keyStatementId = id + SelectKeyGenerator.SELECT_KEY_SUFFIX;
    keyStatementId = builderAssistant.applyCurrentNamespace(keyStatementId, true);
    if (configuration.hasKeyGenerator(keyStatementId)) {
      keyGenerator = configuration.getKeyGenerator(keyStatementId);
    } else {
      keyGenerator = context.getBooleanAttribute("useGeneratedKeys",
          configuration.isUseGeneratedKeys() && SqlCommandType.INSERT.equals(sqlCommandType))
          ? Jdbc3KeyGenerator.INSTANCE : NoKeyGenerator.INSTANCE;
    }

  	// 替换占位符?,保存#{}里面的内容
    SqlSource sqlSource = langDriver.createSqlSource(configuration, context, parameterTypeClass);
    StatementType statementType = StatementType.valueOf(context.getStringAttribute("statementType", StatementType.PREPARED.toString()));
    Integer fetchSize = context.getIntAttribute("fetchSize");
    Integer timeout = context.getIntAttribute("timeout");
    String parameterMap = context.getStringAttribute("parameterMap");
    String resultType = context.getStringAttribute("resultType");
    Class<?> resultTypeClass = resolveClass(resultType);
    String resultMap = context.getStringAttribute("resultMap");
    String resultSetType = context.getStringAttribute("resultSetType");
    ResultSetType resultSetTypeEnum = resolveResultSetType(resultSetType);
    if (resultSetTypeEnum == null) {
      resultSetTypeEnum = configuration.getDefaultResultSetType();
    }
    String keyProperty = context.getStringAttribute("keyProperty");
    String keyColumn = context.getStringAttribute("keyColumn");
    String resultSets = context.getStringAttribute("resultSets");

  	// 通过构建者助手,创建mappedstatement对象
    builderAssistant.addMappedStatement(id, sqlSource, statementType, sqlCommandType,
        fetchSize, timeout, parameterMap, parameterTypeClass, resultMap, resultTypeClass,
        resultSetTypeEnum, flushCache, useCache, resultOrdered,
        keyGenerator, keyProperty, keyColumn, databaseId, langDriver, resultSets);
  }

// org.apache.ibatis.builder.MapperBuilderAssistant#addMappedStatement(java.lang.String, org.apache.ibatis.mapping.SqlSource, org.apache.ibatis.mapping.StatementType, org.apache.ibatis.mapping.SqlCommandType, java.lang.Integer, java.lang.Integer, java.lang.String, java.lang.Class<?>, java.lang.String, java.lang.Class<?>, org.apache.ibatis.mapping.ResultSetType, boolean, boolean, boolean, org.apache.ibatis.executor.keygen.KeyGenerator, java.lang.String, java.lang.String, java.lang.String, org.apache.ibatis.scripting.LanguageDriver, java.lang.String)
public MappedStatement addMappedStatement(
      String id,
      SqlSource sqlSource,
      StatementType statementType,
      SqlCommandType sqlCommandType,
      Integer fetchSize,
      Integer timeout,
      String parameterMap,
      Class<?> parameterType,
      String resultMap,
      Class<?> resultType,
      ResultSetType resultSetType,
      boolean flushCache,
      boolean useCache,
      boolean resultOrdered,
      KeyGenerator keyGenerator,
      String keyProperty,
      String keyColumn,
      String databaseId,
      LanguageDriver lang,
      String resultSets) {

    if (unresolvedCacheRef) {
      throw new IncompleteElementException("Cache-ref not yet resolved");
    }

    id = applyCurrentNamespace(id, false);
    boolean isSelect = sqlCommandType == SqlCommandType.SELECT;

  	// 创建MappedStatement对象
    MappedStatement.Builder statementBuilder = new MappedStatement.Builder(configuration, id, sqlSource, sqlCommandType)
        .resource(resource)
        .fetchSize(fetchSize)
        .timeout(timeout)
        .statementType(statementType)
        .keyGenerator(keyGenerator)
        .keyProperty(keyProperty)
        .keyColumn(keyColumn)
        .databaseId(databaseId)
        .lang(lang)
        .resultOrdered(resultOrdered)
        .resultSets(resultSets)
        .resultMaps(getStatementResultMaps(resultMap, resultType, id))
        .resultSetType(resultSetType)
        .flushCacheRequired(valueOrDefault(flushCache, !isSelect))
        .useCache(valueOrDefault(useCache, isSelect))
        .cache(currentCache);

    ParameterMap statementParameterMap = getStatementParameterMap(parameterMap, parameterType, id);
    if (statementParameterMap != null) {
      statementBuilder.parameterMap(statementParameterMap);
    }

  	// 封装好MappedStatement,并返回
    MappedStatement statement = statementBuilder.build();
    configuration.addMappedStatement(statement);
    return statement;
  }

// org.apache.ibatis.builder.MapperBuilderAssistant#applyCurrentNamespace
public String applyCurrentNamespace(String base, boolean isReference) {
    if (base == null) {
      return null;
    }
    if (isReference) {
      // is it qualified with any namespace yet?
      if (base.contains(".")) {
        return base;
      }
    } else {
      // is it qualified with this namespace yet?
      if (base.startsWith(currentNamespace + ".")) {
        return base;
      }
      if (base.contains(".")) {
        throw new BuilderException("Dots are not allowed in element names, please remove it from " + base);
      }
    }
  	// namespacename+点+方法名
    return currentNamespace + "." + base;
  }
```

到这里,配置文件就解析完成了,下面是创建SqlSessionFactory对象

## SqlSession

```java
 // org.apache.ibatis.session.SqlSessionFactoryBuilder#build(org.apache.ibatis.session.Configuration)
public SqlSessionFactory build(Configuration config) {
  return new DefaultSqlSessionFactory(config);
}
```

下面是mybatis创建sql的流程

```java
// /Users/anthony/.m2/repository/org/mybatis/mybatis/3.5.2/mybatis-3.5.2-sources.jar!/org/apache/ibatis/builder/xml/XMLStatementBuilder.java:96
// 占位符是如果进行替换的?动态sql如果进行的解析
String lang = context.getStringAttribute("lang");
LanguageDriver langDriver = getLanguageDriver(lang);
SqlSource sqlSource = langDriver.createSqlSource(configuration, context, parameterTypeClass);

// org.apache.ibatis.builder.xml.XMLStatementBuilder#getLanguageDriver
private LanguageDriver getLanguageDriver(String lang) {
  Class<? extends LanguageDriver> langClass = null;
  if (lang != null) {
    langClass = resolveClass(lang);
  }
  return configuration.getLanguageDriver(langClass);
}

// org.apache.ibatis.session.Configuration#getLanguageDriver
public LanguageDriver getLanguageDriver(Class<? extends LanguageDriver> langClass) {
  if (langClass == null) {
    return languageRegistry.getDefaultDriver();
  }
  languageRegistry.register(langClass);
  return languageRegistry.getDriver(langClass);
}

// org.apache.ibatis.scripting.LanguageDriverRegistry#getDefaultDriver
// 打断点可以看到是:XMLLanguageDriver
public LanguageDriver getDefaultDriver() {
  return getDriver(getDefaultDriverClass());
}

public Class<? extends LanguageDriver> getDefaultDriverClass() {
  return defaultDriverClass;
}

// org.apache.ibatis.scripting.xmltags.XMLLanguageDriver#createSqlSource(org.apache.ibatis.session.Configuration, org.apache.ibatis.parsing.XNode, java.lang.Class<?>)
public SqlSource createSqlSource(Configuration configuration, XNode script, Class<?> parameterType) {
  XMLScriptBuilder builder = new XMLScriptBuilder(configuration, script, parameterType);
  return builder.parseScriptNode();
}


// org.apache.ibatis.scripting.xmltags.XMLScriptBuilder#XMLScriptBuilder(org.apache.ibatis.session.Configuration, org.apache.ibatis.parsing.XNode, java.lang.Class<?>)
public XMLScriptBuilder(Configuration configuration, XNode context, Class<?> parameterType) {
  super(configuration);
  this.context = context;
  this.parameterType = parameterType;
  // 初始化动态sql的节点处理器结合
  initNodeHandlerMap();
}

// org.apache.ibatis.scripting.xmltags.XMLScriptBuilder#initNodeHandlerMap
  private void initNodeHandlerMap() {
    nodeHandlerMap.put("trim", new TrimHandler());
    nodeHandlerMap.put("where", new WhereHandler());
    nodeHandlerMap.put("set", new SetHandler());
    nodeHandlerMap.put("foreach", new ForEachHandler());
    nodeHandlerMap.put("if", new IfHandler());
    nodeHandlerMap.put("choose", new ChooseHandler());
    nodeHandlerMap.put("when", new IfHandler());
    nodeHandlerMap.put("otherwise", new OtherwiseHandler());
    nodeHandlerMap.put("bind", new BindHandler());
  }

// org.apache.ibatis.scripting.xmltags.XMLScriptBuilder#parseScriptNode
// 解析sql,还有参数类型和结果集类型
public SqlSource parseScriptNode() {
  MixedSqlNode rootSqlNode = parseDynamicTags(context);
  SqlSource sqlSource;
  if (isDynamic) {
    sqlSource = new DynamicSqlSource(configuration, rootSqlNode);
  } else {
    sqlSource = new RawSqlSource(configuration, rootSqlNode, parameterType);
  }
  return sqlSource;
}
```

全部就解析完成了,接下来

![image-20230813213519036](https://image.runtimes.cc/202404051430408.png)

看看`session = sqlSessionFactory.openSession();`

* 创建事务对象
* 创建了执行器对象CasheingExecutor
* 创建DefaultSqlSession对象

```java
// org.apache.ibatis.session.defaults.DefaultSqlSessionFactory#openSession()
@Override
public SqlSession openSession() {
  return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
}

// org.apache.ibatis.session.Configuration#getDefaultExecutorType
// protected ExecutorType defaultExecutorType = ExecutorType.SIMPLE;
public ExecutorType getDefaultExecutorType() {
  return defaultExecutorType;
}

// org.apache.ibatis.session.defaults.DefaultSqlSessionFactory#openSessionFromDataSource
// 参数一:执行器,参数二:隔离级别
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
  Transaction tx = null;
  try {
    // 获取数据源环境信息
    final Environment environment = configuration.getEnvironment();
    // 获取事务工厂
    final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
    // 获取JdbcTransaction或者ManagedTransaction
    // ManagedTransaction 就相当于没有事务
    tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
    // 创建Executor执行器
    final Executor executor = configuration.newExecutor(tx, execType);
    // 创建DefaultSqlSession
    return new DefaultSqlSession(configuration, executor, autoCommit);
  } catch (Exception e) {
    closeTransaction(tx); // may have fetched a connection so lets call close()
    throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
  } finally {
    ErrorContext.instance().reset();
  }
}

// org.apache.ibatis.transaction.jdbc.JdbcTransactionFactory#newTransaction(java.sql.Connection)
@Override
public Transaction newTransaction(DataSource ds, TransactionIsolationLevel level, boolean autoCommit) {
  return new JdbcTransaction(ds, level, autoCommit);
}

// org.apache.ibatis.session.Configuration#newExecutor(org.apache.ibatis.transaction.Transaction, org.apache.ibatis.session.ExecutorType)
  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
    executorType = executorType == null ? defaultExecutorType : executorType;
    executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
    Executor executor;
    if (ExecutorType.BATCH == executorType) {
      executor = new BatchExecutor(this, transaction);
    } else if (ExecutorType.REUSE == executorType) {
      executor = new ReuseExecutor(this, transaction);
    } else {
      executor = new SimpleExecutor(this, transaction);
    }
    // 如果允许缓存,会通过CachingExecutor去代理一层
    if (cacheEnabled) {
      executor = new CachingExecutor(executor);
    }
    // 拦截器插件
    executor = (Executor) interceptorChain.pluginAll(executor);
    return executor;
  }

// org.apache.ibatis.executor.CachingExecutor#CachingExecutor
public CachingExecutor(Executor delegate) {
  this.delegate = delegate;
  delegate.setExecutorWrapper(this);
}
```

## 调用具体的API进行查询

用`启动类`中的方法,对比可以发现两段代码不同之处为:

```java
Map map = sqlSession.selectOne("com.analyze.mybatis.mapper.UserMapper.getUA");

UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
Map map = userMapper.getUA();
```

在查看`DefaultSqlSession`中的`selectOne`方法,会执行以下的调用链

```java
// org.apache.ibatis.session.defaults.DefaultSqlSession#selectOne(java.lang.String, java.lang.Object)
@Override
public <T> T selectOne(String statement, Object parameter) {
  // Popular vote was to return null on 0 results and throw exception on too many.
  List<T> list = this.selectList(statement, parameter);
  if (list.size() == 1) {
    return list.get(0);
  } else if (list.size() > 1) {
    throw new TooManyResultsException("Expected one result (or null) to be returned by selectOne(), but found: " + list.size());
  } else {
    return null;
  }
}

// org.apache.ibatis.session.defaults.DefaultSqlSession#selectList(java.lang.String, java.lang.Object)
// RowBounds 分页对象
public <E> List<E> selectList(String statement, Object parameter) {
    return this.selectList(statement, parameter, RowBounds.DEFAULT);
}

// org.apache.ibatis.session.defaults.DefaultSqlSession#selectList(java.lang.String, java.lang.Object, org.apache.ibatis.session.RowBounds)
@Override
public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
  try {
    // 根据传入的statementId,获取mapperStatement对象
    MappedStatement ms = configuration.getMappedStatement(statement);
    // 调用执行器的方法
    return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
  } catch (Exception e) {
    throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
  } finally {
    ErrorContext.instance().reset();
  }
}

// org.apache.ibatis.executor.CachingExecutor#query(org.apache.ibatis.mapping.MappedStatement, java.lang.Object, org.apache.ibatis.session.RowBounds, org.apache.ibatis.session.ResultHandler)
// 注意这里是CachingExecutor,默认的
@Override
public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
  // 获取带问号的sql语句,比如 select * from user_info where id = ?
  BoundSql boundSql = ms.getBoundSql(parameterObject);
  // 生成缓存key
  CacheKey key = createCacheKey(ms, parameterObject, rowBounds, boundSql);
  return query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
}

// org.apache.ibatis.executor.CachingExecutor#query(org.apache.ibatis.mapping.MappedStatement, java.lang.Object, org.apache.ibatis.session.RowBounds, org.apache.ibatis.session.ResultHandler, org.apache.ibatis.cache.CacheKey, org.apache.ibatis.mapping.BoundSql)
@Override
public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql)
    throws SQLException {
  // 二级缓存
  // usermapper的标签里可以设置<Cache>缓存标签
  Cache cache = ms.getCache();
  if (cache != null) {
    // 刷新二级缓存,在<select> 标签里可以配置flushcache
    flushCacheIfRequired(ms);
    // 在<select> 标签里可以配置usecache
    if (ms.isUseCache() && resultHandler == null) {
      // 判断是不是存过
      ensureNoOutParams(ms, boundSql);
      @SuppressWarnings("unchecked")
      // 从二级缓存中查询数据
      List<E> list = (List<E>) tcm.getObject(cache, key);
      // 如果从二级缓存没有查询到数据
      if (list == null) {
        // 委托给BaseExecutor执行
        list = delegate.query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
        // 将查询结果保存到二级缓存中,这里只是存到map集合中,没有真正存到二级缓存中
        tcm.putObject(cache, key, list); // issue #578 and #116
      }
      return list;
    }
  }
  return delegate.query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
}

// org.apache.ibatis.executor.BaseExecutor#query(org.apache.ibatis.mapping.MappedStatement, java.lang.Object, org.apache.ibatis.session.RowBounds, org.apache.ibatis.session.ResultHandler, org.apache.ibatis.cache.CacheKey, org.apache.ibatis.mapping.BoundSql)
@Override
public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
  ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
  if (closed) {
    throw new ExecutorException("Executor was closed.");
  }
  
  // 如果配置了FlushCacheRequired为true,则会在执行器执行之前就清空本地一级缓存
  if (queryStack == 0 && ms.isFlushCacheRequired()) {
    // 清空缓存
    clearLocalCache();
  }
  List<E> list;
  try {
    queryStack++;
    // 从一级缓存中获取数据
    list = resultHandler == null ? (List<E>) localCache.getObject(key) : null;
    if (list != null) {
      // 如果有数据,则处理本地缓存结果给输出参数
      // 还是处理存过
      handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
    } else {
      // 没有缓存结果,则从数据库查询结果
      list = queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
    }
  } finally {
    queryStack--;
  }
  if (queryStack == 0) {
    for (DeferredLoad deferredLoad : deferredLoads) {
      deferredLoad.load();
    }
    // issue #601
    deferredLoads.clear();
    if (configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
      // issue #482
      clearLocalCache();
    }
  }
  return list;
}


// org.apache.ibatis.executor.BaseExecutor#queryFromDatabase
private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
  List<E> list;
  // 向本地缓存中存入一个ExecutionPlaceholder的枚举类占位
  localCache.putObject(key, EXECUTION_PLACEHOLDER);
  try {
    // 执行查询
    list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
  } finally {
    // 执行玩移除这个key
    localCache.removeObject(key);
  }
  localCache.putObject(key, list);
  if (ms.getStatementType() == StatementType.CALLABLE) {
    localOutputParameterCache.putObject(key, parameter);
  }
  return list;
}

// org.apache.ibatis.executor.SimpleExecutor#doQuery
@Override
public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
  Statement stmt = null;
  try {
    // 获取全局配置文件实例
    Configuration configuration = ms.getConfiguration();
    // new一个statementHandler实例
    StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
    // 准备处理器,主要包括创建statement以及动态参数的设置
    stmt = prepareStatement(handler, ms.getStatementLog());
    // 执行真正的数据库操作调用
    return handler.query(stmt, resultHandler);
  } finally {
    closeStatement(stmt);
  }
}

// org.apache.ibatis.session.Configuration#newStatementHandler
public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
  // 创建路由功能的StatementHanlder,根据MappedStatement中的StetementType
  StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
  // 插件机制:对核心对象进行拦截
  statementHandler = (StatementHandler) interceptorChain.pluginAll(statementHandler);
  return statementHandler;
}

// org.apache.ibatis.executor.statement.RoutingStatementHandler#RoutingStatementHandler
public RoutingStatementHandler(Executor executor, MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {

  switch (ms.getStatementType()) {
    case STATEMENT:
      delegate = new SimpleStatementHandler(executor, ms, parameter, rowBounds, resultHandler, boundSql);
      break;
    case PREPARED:
      delegate = new PreparedStatementHandler(executor, ms, parameter, rowBounds, resultHandler, boundSql);
      break;
    case CALLABLE:
      delegate = new CallableStatementHandler(executor, ms, parameter, rowBounds, resultHandler, boundSql);
      break;
    default:
      throw new ExecutorException("Unknown statement type: " + ms.getStatementType());
  }

}

// org.apache.ibatis.executor.SimpleExecutor#prepareStatement
private Statement prepareStatement(StatementHandler handler, Log statementLog){
  Statement stmt;
  // 获取代理后,增加日志功能的Connection对象
  Connection connection = getConnection(statementLog);
  // 创建Statement对象
  stmt = handler.prepare(connection, transaction.getTimeout());
  // 参数化处理
  handler.parameterize(stmt);
  return stmt;
}

// org.apache.ibatis.executor.statement.PreparedStatementHandler#query
 @Override
public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
  PreparedStatement ps = (PreparedStatement) statement;
  ps.execute();
  return resultSetHandler.handleResultSets(ps);
}

```

![image-20230816183741743](https://image.runtimes.cc/202404051430956.png)

## 解析结果

```java
// org.apache.ibatis.executor.resultset.DefaultResultSetHandler#handleResultSets
@Override
public List<Object> handleResultSets(Statement stmt) throws SQLException {
  ErrorContext.instance().activity("handling results").object(mappedStatement.getId());

  final List<Object> multipleResults = new ArrayList<>();

  int resultSetCount = 0;
  ResultSetWrapper rsw = getFirstResultSet(stmt);

  List<ResultMap> resultMaps = mappedStatement.getResultMaps();
  int resultMapCount = resultMaps.size();
  validateResultMapsCount(rsw, resultMapCount);
  while (rsw != null && resultMapCount > resultSetCount) {
    ResultMap resultMap = resultMaps.get(resultSetCount);
    handleResultSet(rsw, resultMap, multipleResults, null);
    rsw = getNextResultSet(stmt);
    cleanUpAfterHandlingResultSet();
    resultSetCount++;
  }

  String[] resultSets = mappedStatement.getResultSets();
  if (resultSets != null) {
    while (rsw != null && resultSetCount < resultSets.length) {
      ResultMapping parentMapping = nextResultMaps.get(resultSets[resultSetCount]);
      if (parentMapping != null) {
        String nestedResultMapId = parentMapping.getNestedResultMapId();
        ResultMap resultMap = configuration.getResultMap(nestedResultMapId);
        handleResultSet(rsw, resultMap, null, parentMapping);
      }
      rsw = getNextResultSet(stmt);
      cleanUpAfterHandlingResultSet();
      resultSetCount++;
    }
  }

  return collapseSingleResultList(multipleResults);
}
```



## 缓存

```java
// org.apache.ibatis.executor.CachingExecutor#createCacheKey
// CacheKey重新了 hacode和equals方法
@Override
public CacheKey createCacheKey(MappedStatement ms, Object parameterObject, RowBounds rowBounds, BoundSql boundSql) {
  // delegate 是具体类型的执行器的应用
  // 默认是SimpleExecutor
  return delegate.createCacheKey(ms, parameterObject, rowBounds, boundSql);
}

// org.apache.ibatis.executor.BaseExecutor#createCacheKey
@Override
public CacheKey createCacheKey(MappedStatement ms, Object parameterObject, RowBounds rowBounds, BoundSql boundSql) {
  if (closed) {
    throw new ExecutorException("Executor was closed.");
  }
  
  // 通过构造器创建
  CacheKey cacheKey = new CacheKey();
  // id
  cacheKey.update(ms.getId());
  // 分页参数
  cacheKey.update(rowBounds.getOffset());
  cacheKey.update(rowBounds.getLimit());
  // sql
  cacheKey.update(boundSql.getSql());
  List<ParameterMapping> parameterMappings = boundSql.getParameterMappings();
  TypeHandlerRegistry typeHandlerRegistry = ms.getConfiguration().getTypeHandlerRegistry();
  // mimic DefaultParameterHandler logic
  for (ParameterMapping parameterMapping : parameterMappings) {
    if (parameterMapping.getMode() != ParameterMode.OUT) {
      Object value;
      String propertyName = parameterMapping.getProperty();
      if (boundSql.hasAdditionalParameter(propertyName)) {
        value = boundSql.getAdditionalParameter(propertyName);
      } else if (parameterObject == null) {
        value = null;
      } else if (typeHandlerRegistry.hasTypeHandler(parameterObject.getClass())) {
        value = parameterObject;
      } else {
        MetaObject metaObject = configuration.newMetaObject(parameterObject);
        value = metaObject.getValue(propertyName);
      }
      // 参数的值
      cacheKey.update(value);
    }
  }
  if (configuration.getEnvironment() != null) {
    // issue #176
    // 当前环境的值
    cacheKey.update(configuration.getEnvironment().getId());
  }
  return cacheKey;
}
```

# 面试题
