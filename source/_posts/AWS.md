---
title: AWS
categories:
  - Cloud
tags:
  - AWS
  - 云服务器
  - 图床
  - 静态资源
  - 安全组
  - CDN
  - 边缘加速
  - cname
cover: https://image.runtimes.cc/202404051525243.png
abbrlink: 310
date: 2022-11-19 18:25:00
updated: 2025-05-12 11:30:18
---

# AWS基本教程

场景是:在把22端口关闭了,无法再连上服务器的情况

解决办法:关闭服务器,点击`操作`->`实例设置`->`编辑用户数据`

```shell
#!/bin/bash
# 允许 SSH 端口
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

再启动服务器

## AWS升级配置

1.先`停止`实例,不是`终止`实例
![](https://image.runtimes.cc/202404051405513.png)

2.修改实例类型
![](https://image.runtimes.cc/202404051406722.png)

{% note danger simple %}
重启公网IP可能会改变
{% endnote %}

## 服务器局域网互通
需要把局域网 想要互通的服务器,加入一个公共的的默认安全组

![](https://image.runtimes.cc/202404051406727.png)
选择`default`安全组
![](https://image.runtimes.cc/202404051406784.png)

## 要看服务器部署在哪里
![](https://image.runtimes.cc/202404051406380.png)

![](https://image.runtimes.cc/202404051407931.png)

## JavaSDK上传图片到S3

```java
@RestController
@Slf4j
@Api(tags = "上传接口")
public class UploadController {
    @Value("${S3.accessKey}")
    String accessKey;

    @Value("${S3.secretKey}")
    String secretKey;

    @Value("${S3.endPoint}")
    String endPoint;

    @Value("${S3.bucketName}")
    String bucketName;

    @PostMapping("/upload")
    @ApiOperation("上传")
    @NotNeedAuth
    public Response<String> upload(@RequestParam("file") MultipartFile file) {
        String originalFilename = file.getOriginalFilename();
        if (!Constant.PHOTO_LIST.contains(originalFilename.substring(originalFilename.lastIndexOf(".") + 1))) {
            return Response.fail(CodeConstant.CODE_095);
        }

        String newImageName = ThreadLocalRandom.current().nextInt(10000) + System.currentTimeMillis() + originalFilename.substring(originalFilename.lastIndexOf(".")).toLowerCase();
        String fileName = "lxdt/dt/" + Constant.DIRECTORY_TIME_FORMAT.format(LocalDateTime.now(Constant.TIME_ZONE)) + newImageName;
        try {
            S3Util.uploadFileByFile(accessKey, secretKey, endPoint, bucketName, fileName, file);
        } catch (Exception e) {
            log.error("上传失败", e);
            return Response.error("");
        }
        return Response.successData(fileName);
    }
}
```

```java
public class S3Util {
    /**
     * 图片上传
     */
    public static void uploadFileByFile(String accessKey, String secretKey, String endPoint, String bucketName, String folderName, MultipartFile file) throws IOException {
        ClientConfiguration clientConfig = new ClientConfiguration();
        clientConfig.setProtocol(Protocol.HTTPS);
        AmazonS3 amazonS3 = AmazonS3Client.builder().withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration(endPoint, Region.getRegion(Regions.AP_SOUTHEAST_1).getName()))
                .withClientConfiguration(clientConfig).withCredentials(new AWSStaticCredentialsProvider(new BasicAWSCredentials(accessKey, secretKey)))
                .disableChunkedEncoding().withPathStyleAccessEnabled(true).withForceGlobalBucketAccessEnabled(true).enablePathStyleAccess().build();
        ObjectMetadata objectMetadata = new ObjectMetadata();
        objectMetadata.setContentType(file.getContentType());
        objectMetadata.setContentLength(file.getSize());
        amazonS3.putObject(new PutObjectRequest(bucketName, folderName, file.getInputStream(), objectMetadata).withCannedAcl(CannedAccessControlList.PublicRead));
    }
}
```

## S3桶策略

公开访问桶策略

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

> 印象中有遇到这种情况:
>
> ​	第一次操作桶的时候,桶里面的文件是能正常共享出来的
>
> ​	很久之后没有操作了,再上传一个新文件,上传的这个文件就不能共享了
>
> 第二次出现这样情况的时候,我文件的url搞错了,是可以正常共享的

# PicGO+S3

### 创建存储桶

红框里不要勾选

![](https://image.runtimes.cc/202404051407616.png)

### 创建文件夹,设置公开
![](https://image.runtimes.cc/202404051407911.png)

### 开启静态托管
![](https://image.runtimes.cc/202404051407713.png)

### 新建IAM用户

访问创建IAM页面
![](https://image.runtimes.cc/202404051407343.png)

创建使用者
![](https://image.runtimes.cc/202404051408604.png)

创建使用者详情
![](https://image.runtimes.cc/202404051408370.png)

创建使用者权限,点击下一步,然后就能看到key和密钥了
![](https://image.runtimes.cc/202404051409638.png)

![](https://image.runtimes.cc/202404051410989.png)

### PicGO配置

PicGO软件许要下再S3插件,然后再配置文件里粘贴这个

```json
"aws-s3": {
    "accessKeyID": "AKIA2Q6ZSIPP7MKZOFHR",
    "secretAccessKey": "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",
    "bucketName": "mmzcg.com",
    "uploadPath": "blog/{year}/{fullName}",
    "acl": "public-read"
}
```

## 参考

[S3教程](https://atsud0.me/2021/02/24/%E4%BD%BF%E7%94%A8Amazon-S3%E5%AE%9E%E7%8E%B0%E5%9B%BE%E5%BA%8A/)

[PicGoS3教程](https://github.com/wayjam/picgo-plugin-s3)

# CloudFront加速Vercel

1.找到vercel自动分配的域名

![image-20221123104312029](https://image.runtimes.cc/202404051410348.png)

2.aws 创建分配

![image-20221123104312029](https://image.runtimes.cc/202404051410581.png)

3.选择源

不要选择源,输入vercel分配的域名

![image-20221123104312029](https://image.runtimes.cc/202404051410351.png)

4.选择全部https,其它可以默认

![image-20221123104312029](https://image.runtimes.cc/202404051410472.png)
5.选择证书

![image-20221123104312029](https://image.runtimes.cc/202404051410436.png)

备用域名:
比如vercel默认分配的是xxx.vercel.app
你想加速的域名是  abc.blog,备用域名就填abc.blog

自定义SSL证书:
用aws自带的证书申请工具

点击保存,回到CloudFront首页

6.cname

* 看上次修改时间这一列,如果显示的时间,就表示刚才的配置已经生效了,如果显示在部署中...就等一会
* CloudFront这一列会分配一个域名,在域名解析那里Cname指向这个域名就可以了

![image-20221123104312029](https://image.runtimes.cc/202404051410552.png)

# AWS S3和Hexo

1.安装插件

```powershell
# 装置
npm install hexo-deployer-aws-s3 --save-dev
```

2.改`_config.yml`

```powershell
deploy:
# - type: 'git'
#   repo: git@github.com:aaaaaaanthony/aaaaaaanthony.github.io.git
#   branch: master

  type: 'aws-s3'            # 写死
  region: ap-east-1        # 区域名
  bucket: mmzcg.com        # 桶名
```

如果要部署多个地方,每个仓库的type前面要加一个`-` 

如果只有一个,就只用type开头就行

3.执行环境变量

```bash
export AWS_ACCESS_KEY_ID=AKIA2Q6ZSIPP7MKZOFHR
export AWS_SECRET_ACCESS_KEY=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
source /etc/profile
```

4.部署

```bash

hexo clean
hexo algoli
hexo d -g
```

发现报错，`The bucket does not allow ACLs`，还需要在存储中开启 `ACLs`

![](https://image.runtimes.cc/202404051411607.png)

接下来要关闭「阻止共有访问操作」

![](https://image.runtimes.cc/202404051411528.png)

部署成功后，开启静态网站托管功能

![](https://image.runtimes.cc/202404051411449.png)
# RDS
## 创建RDS

选择mysql社区版本

![1](https://image.runtimes.cc/202406151610262.png)

![image-20240615161249827](https://image.runtimes.cc/202406151612706.png)

![image-20240615161843363](https://image.runtimes.cc/202406151618731.png)

![image-20240615162034149](https://image.runtimes.cc/202406151620676.png)

[AWS的RDS开启慢查询日志到cloudwatch中](https://blog.csdn.net/zhouyan8603/article/details/107947011)

## 快照导出到S3

> [参考文档](https://docs.aws.amazon.com/zh_cn/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html#USER_ExportSnapshot.Exporting)

![image-20240813103851409](https://image.runtimes.cc/202408131038847.png)

接下来的页面是需要的设置

* 导出标识符,就是导出任务的名字
* 导出的数据,这里测试就简单点,导出全部表,要是选部分表,文档里有导出规则
* S3 目标,  选一个存储桶
* 接下来的两个是难点,IAM角色,和加密

![image-20240813104211323](https://image.runtimes.cc/202408131042173.png)

先看加密,先创建ARN,找到配置的选项卡

![image-20240813104403258](https://image.runtimes.cc/202408131044763.png)

点击下面的链接

![image-20240813104444558](https://image.runtimes.cc/202408131044182.png)

在客户管理的秘钥,创建一个秘钥,  客户管理的密钥和AWS托管的密钥是有区别的,要选对

![image-20240813104544460](https://image.runtimes.cc/202408131045111.png)

创建完之后,再回到 密钥列表的页面,点密钥ID,进入到详情页

![image-20240813104726725](https://image.runtimes.cc/202408131047379.png)

复制ARN刚刚才导出的页面,角色权限什么的,各种尝试下就可以了

# front清空缓存API
```python
import boto3
import datetime

def create_invalidation(distribution_id, paths):
    # 创建一个CloudFront客户端并提供凭证
    client = boto3.client('cloudfront',aws_access_key_id='AKIA2UC26S7767GSGKJQ',aws_secret_access_key='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')

    # 创建一个唯一的无效请求ID
    caller_reference = str(datetime.datetime.now())

    # 格式化路径
    items = paths if isinstance(paths, list) else [paths]

    # 构建无效请求
    invalidation_batch = {
        'Paths': {
            'Quantity': len(items),
            'Items': items
        },
        'CallerReference': caller_reference
    }

    # 创建无效请求
    response = client.create_invalidation(
        DistributionId=distribution_id,
        InvalidationBatch=invalidation_batch
    )

    return response


# distribution_id 列表，包含ID、说明和类型
distribution_info = [
    ["EX123AFBG", "生产环境", "生产", "baidu.com"],
]

# 要使失效的路径
paths_to_invalidate = ['/*']

# 循环遍历每个distribution_id
for info in distribution_info:
    distribution_id = info[0]
    print(f"Creating invalidation for Distribution ID: {distribution_id}, Description: {info[1]}, Type: {info[2]}")
    response = create_invalidation(distribution_id, paths_to_invalidate)
    print(response)
    print("================")

print("刷新完成")

```