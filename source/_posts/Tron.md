---
title: Tron
tags:
  - Java
  - 区块链
categories:
  - 区块链
cover: https://image.runtimes.cc/202404051535708.png
abbrlink: 14108
date: 2023-12-20 20:48:19
updated: 2023-12-20 20:48:23
---

> [波场官方trident-java文档](https://cn.developers.tron.network/reference/quickstart)
>
> [m1 mac Android protobuf 遇到的问题](https://blog.csdn.net/u013903024/article/details/121263515)
>
> [tron中文文档](https://tronprotocol.github.io/documentation-zh/api/http/#walletcreateaccount)
>
> [trc20离线签名的demo,有trxdent-java,就不需要这个了,使用http的方式可以看看](https://blog.csdn.net/sail331x/article/details/112972860)
>
> [trc20离线签名的demo,有trxdent-java,就不需要这个了,使用http的方式可以看看,另外一个demo](https://blog.csdn.net/sail331x/article/details/114765892?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-114765892-blog-112972860.235%5Ev39%5Epc_relevant_anti_t3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-114765892-blog-112972860.235%5Ev39%5Epc_relevant_anti_t3&utm_relevant_index=5)
>
> [要继续学习tron可以看看](https://liukay.com/)

打包依赖

[下载源文件trident-java](https://github.com/tronprotocol/trident)

这里选择`main`分支

![在这里插入图片描述](https://image.runtimes.cc/202404051500865.png)

进入trident-java文件夹,可以看到有build.gradle,如果是mac系统,修改一下依赖

```gradle
protoc {
				// 原来的依赖
        artifact = 'com.google.protobuf:protoc:3.12.0'
        // 修改之后的依赖
        artifact = 'com.google.protobuf:protoc:3.12.0:osx-x86_64'
    }
    plugins {
        grpc {
 						// 原来的依赖
            artifact = 'io.grpc:protoc-gen-grpc-java:1.31.0'
						// 修改之后的依赖
            artifact = 'io.grpc:protoc-gen-grpc-java:1.31.0:osx-x86_64'
        }
    }
```

IDEA打包![image-20231220205836156](https://image.runtimes.cc/202404051500207.png)

打包之后,在每个子模块的`build/libs/xxx-jar`

![image-20231220210042662](https://image.runtimes.cc/202404051500763.png)

下面的操作就可以跟着开发文档了,创建项目

pom.xml

```xml
<dependency>
  <groupId>org.tron.trident</groupId>
  <artifactId>abi</artifactId>
  <version>0.8.0</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/src/main/resources/jar/abi-0.8.0.jar</systemPath>
</dependency>
<dependency>
  <groupId>org.tron.trident</groupId>
  <artifactId>utils</artifactId>
  <version>0.8.0</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/src/main/resources/jar/utils-0.8.0.jar</systemPath>
</dependency>
<dependency>
  <groupId>org.tron.trident</groupId>
  <artifactId>core</artifactId>
  <version>0.8.0</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/src/main/resources/jar/core-0.8.0.jar</systemPath>
</dependency>

<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-all</artifactId>
    <version>1.48.1</version>
</dependency>
```

查询余额的代码

```java
public static String privateKey = "私钥";
	public static String apiKey = "apiKey";//波场申请
	@Test
	public void getCount() {
		//生成密钥对
		KeyPair keyPair = new KeyPair(privateKey);
		//地址转换
		System.out.println(keyPair.toHexAddress());
		ApiWrapper wrapper = ApiWrapper.ofMainnet(privateKey,apiKey);
		//账号查询
		Account account = wrapper.getAccount("账户地址");
		//查询余额
		System.out.println(account.getBalance());
		System.out.println(account.getCreateTime());
	}
```

转账示例

```java
public static String transferTrc(String fromAddress, String toAddress, String amount, String contractAddress, String privateKey) {
    ApiWrapper wrapper = ApiWrapper.ofNile(privateKey);
    String ret = "";
    try {
        //根据合约地址获取封装好的合约
        Contract contract = wrapper.getContract(contractAddress);
        //创建一个TRC20合约对象
        Trc20Contract token = new Trc20Contract(contract, fromAddress, wrapper);
        //获取想要转移的数量
        BigInteger sunAmountValue = Convert.toSun(amount, Convert.Unit.TRX).toBigInteger();
        //设置最大手续费
        long maxTrx = Convert.toSun("17", Convert.Unit.TRX).longValue();
        ret = token.transfer(toAddress, sunAmountValue.longValue(), 0, "", maxTrx);
        System.out.println("哈希：" + ret);
    } catch (Exception e) {
        System.out.println("转移失败:" + e.getMessage().toString());
    } finally {
        wrapper.close();
    }
    return ret;
}
```

