---
title: SpringBoot配置Gmail
id: 4488e83a-e86e-458e-80b4-1b4e0a5804de
date: 2025-01-04 03:56:02
auther: connor
cover: null
excerpt: title SpringBoot配置Gmail tags Gmail 邮箱 SpringBoot 邮箱 categories 记录 cover https//image.runtimes.cc/202404051524969.jpg abbrlink 21215 date 2024-0
---

---
title: SpringBoot配置Gmail
tags:
  - Gmail
  - 邮箱
  - SpringBoot
  - 邮箱
categories:
  - 记录
cover: https://image.runtimes.cc/202404051524969.jpg
abbrlink: 21215
date: 2024-03-02 18:52:43
updated: 2024-03-02 18:52:48
---

![image-20240302190257438](https://image.runtimes.cc/202404051400542.png)

申请应用程序验证码

![image-20240302184916544](https://image.runtimes.cc/202404051400643.png)

配置二步验证

![image-20240302185000050](https://image.runtimes.cc/202404051400904.png)

要配置二步验证,才能看到应用程序的选项

![image-20240302185040456](https://image.runtimes.cc/202404051402469.png)

最后就能获取到应用程序验证码

![image-20240302185121257](https://image.runtimes.cc/202404051402369.png)

引入代码

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

application.properties文件配置

```properties
spring.mail.host=smtp.gmail.com
# 邮件服务器端口号
spring.mail.port=587
# 邮件发送方的电子邮件地址
spring.mail.username=你的Gmail邮箱账号
# 邮件发送方的密码或应用程序专用密码一定要开启了两步验证
spring.mail.password=应用程序专用密码
# 启用TLS加密
spring.mail.properties.mail.smtp.starttls.enable=true
# 验证邮件服务器的身份
spring.mail.properties.mail.smtp.auth=true
# 邮件传输协议
spring.mail.properties.mail.transport.protocol=smtp
```

配置的代码

```java
@Service
public class EmailUtil implements EmailService
{
    private final Logger logger = LoggerFactory.getLogger(this.getClass());

    //Spring Boot 提供了一个发送邮件的简单抽象，使用的是下面这个接口，这里直接注入即可使用
    @Autowired
    private JavaMailSender mailSender;

    // 配置文件中我的谷歌邮箱
    @Value("${spring.mail.username}")
    private String from;

    /**
     * 简单文本邮件
     * @param to 收件人
     * @param subject 主题
     * @param content 内容
     */
    @Override
    public void sendSimpleMail(String to, String subject, String content) {
        //创建SimpleMailMessage对象
        SimpleMailMessage message = new SimpleMailMessage();
        //邮件发送人
        message.setFrom(from);
        //邮件接收人
        message.setTo(to);
        //邮件主题
        message.setSubject(subject);
        //邮件内容
        message.setText(content);
        //发送邮件
        mailSender.send(message);
    }

    /**
     * html邮件
     * @param to 收件人,多个时参数形式 ："xxx@xxx.com,xxx@xxx.com,xxx@xxx.com"
     * @param subject 主题
     * @param content 内容
     */
    @Override
    public void sendHtmlMail(String to, String subject, String content) {
        //获取MimeMessage对象
        MimeMessage message = mailSender.createMimeMessage();
        MimeMessageHelper messageHelper;
        try {
            messageHelper = new MimeMessageHelper(message, true);
            //邮件发送人
            messageHelper.setFrom(from);
            //邮件接收人,设置多个收件人地址
            InternetAddress[] internetAddressTo = InternetAddress.parse(to);
            messageHelper.setTo(internetAddressTo);
            //messageHelper.setTo(to);
            //邮件主题
            message.setSubject(subject);
            //邮件内容，html格式
            messageHelper.setText(content, true);
            //发送
            mailSender.send(message);
            //日志信息
            logger.info("邮件已经发送。");
        } catch (Exception e) {
            logger.error("发送邮件时发生异常！", e);
        }
    }

    /**
     * 带附件的邮件
     * @param to 收件人
     * @param subject 主题
     * @param content 内容
     * @param filePath 附件
     */
    @Override
    public void sendAttachmentsMail(String to, String subject, String content, String filePath) {
        MimeMessage message = mailSender.createMimeMessage();
        try {
            MimeMessageHelper helper = new MimeMessageHelper(message, true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);

            FileSystemResource file = new FileSystemResource(new File(filePath));
            String fileName = filePath.substring(filePath.lastIndexOf(File.separator));
            helper.addAttachment(fileName, file);
            mailSender.send(message);
            //日志信息
            logger.info("邮件已经发送。");
        } catch (Exception e) {
            logger.error("发送邮件时发生异常！", e);
        }
    }

    /**
     * 验证邮箱格式
     * @param email
     * @return
     */
    public  boolean isEmail(String email) {
        if (email == null || email.length() < 1 || email.length() > 256) {
            return false;
        }
        Pattern pattern = Pattern.compile("^\\w+([-+.]\\w+)*@\\w+([-.]\\w+)*\\.\\w+([-.]\\w+)*$");
        return pattern.matcher(email).matches();
    }
}
```

```java
public interface EmailService {
    /**
     * 发送文本邮件
     *
     * @param to      收件人
     * @param subject 主题
     * @param content 内容
     */
    void sendSimpleMail(String to, String subject, String content);

    /**
     * 发送HTML邮件
     *
     * @param to      收件人
     * @param subject 主题
     * @param content 内容
     */
    public void sendHtmlMail(String to, String subject, String content);

    /**
     * 发送带附件的邮件
     *
     * @param to       收件人
     * @param subject  主题
     * @param content  内容
     * @param filePath 附件
     */
    public void sendAttachmentsMail(String to, String subject, String content, String filePath);
}
```

```java
@Service
public class CodeUtil {

    @Autowired
    private StringRedisTemplate redisTemplate;

    public String generateVerificationCode() {
        // 生成6位随机数字验证码
        return String.valueOf((int) ((Math.random() * 9 + 1) * 100000));
    }

    // 验证验证码是否正确
    public boolean verifyCode(String userId, String code,String key) {
        key = key + userId;
        String storedCode = redisTemplate.opsForValue().get(key);
        return code.equals(storedCode);
    }

    //删除redis中验证码
    public void deleteCode(String userId,String key) {
         key = key + userId;
        redisTemplate.delete(key);
    }
}
```

```java
//写在需要发送验证码的地方
//存储时间
  private static final int EXPIRATION_TIME_IN_MINUTES = 3;
  //键名
  private static final String KEY_PREFIX =="xxx";
  key=KEY_PREFIX+"123456"
redisTemplate.opsForValue().set(key, 随机数字验证码, EXPIRATION_TIME_IN_MINUTES, TimeUnit.MINUTES);
```
