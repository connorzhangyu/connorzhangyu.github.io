---
title: Spring Boot 国际化 (i18n) 完整方案
id: 6aae8ae3-fc0b-4657-8965-3c8b98d5eab5
date: 2025-02-05 15:20:37
auther: connor
cover: null
excerpt: Spring Boot 国际化 (i18n) 完整方案 在开发全球化应用时，支持多语言（国际化，i18n）是关键需求之一。本方案基于 Spring Boot，实现一个完整的 国际化解决方案，涵盖以下内容： 国际化资源配置 (messages.properties) 拦截器 (LocaleInterc
---

# Spring Boot 国际化 (i18n) 完整方案

在开发全球化应用时，支持多语言（国际化，i18n）是关键需求之一。本方案基于 **Spring Boot**，实现一个完整的 **国际化解决方案**，涵盖以下内容：

- **国际化资源配置 (`messages.properties`)**
- **拦截器 (`LocaleInterceptor`) 解析 `Accept-Language` 头**
- **基于 `LocaleResolver` 处理请求头的多语言解析**
- **统一异常处理 (`GlobalExceptionHandler`) 让错误消息支持国际化**
- **提供默认语言（英语）支持**

---

## **1. 国际化资源文件配置**

Spring Boot 通过 `messages.properties` 及其翻译版本存储多语言消息。

**默认 `messages.properties`（英语）**
```properties
error.notfound=Resource not found.
error.internal=Internal server error.
```

**中文 `messages_zh.properties`**
```properties
error.notfound=资源未找到。
error.internal=服务器内部错误。
```

**泰语 `messages_th.properties`**
```properties
error.notfound=ไม่พบทรัพยากร
error.internal=ข้อผิดพลาดของเซิร์ฟเวอร์ภายใน
```

---

## **2. `LocaleConfig.java`（Locale 解析 & 语言默认值）**

使用 `AcceptHeaderLocaleResolver` 解析 `Accept-Language` 头部，并默认使用 **英语**。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver;

import java.util.Arrays;
import java.util.List;
import java.util.Locale;

@Configuration
public class LocaleConfig {
    @Bean
    public LocaleResolver localeResolver() {
        AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();

        // 设置默认语言为英语
        localeResolver.setDefaultLocale(Locale.ENGLISH);

        // 指定支持的语言
        List<Locale> supportedLocales = Arrays.asList(Locale.ENGLISH, Locale.CHINESE, new Locale("th"));
        localeResolver.setSupportedLocales(supportedLocales);

        return localeResolver;
    }
}
```

**📌 关键点**
- `setDefaultLocale(Locale.ENGLISH)` 确保默认语言是英语。
- `setSupportedLocales(...)` 指定支持的语言。

---

## **3. `LocaleInterceptor.java`（拦截器解析 `Accept-Language` 头）**

拦截请求并解析 `Accept-Language` 头部：

```java
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.support.RequestContextUtils;

import java.util.Locale;

public class LocaleInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        LocaleResolver localeResolver = RequestContextUtils.getLocaleResolver(request);
        if (localeResolver != null) {
            String acceptLanguage = request.getHeader("Accept-Language");
            Locale locale = (acceptLanguage != null) ? Locale.forLanguageTag(acceptLanguage) : Locale.ENGLISH;
            localeResolver.setLocale(request, response, locale);
        }
        return true;
    }
}
```

**📌 关键点**
- **拦截 `Accept-Language` 头部**，解析成 `Locale`。
- 如果请求未指定语言，**默认使用英语 (`Locale.ENGLISH`)**。

---

## **4. `GlobalExceptionHandler.java`（统一异常处理）**

统一捕获异常，并返回国际化消息。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.context.i18n.LocaleContextHolder;

@ControllerAdvice
public class GlobalExceptionHandler {
    @Autowired
    private MessageSource messageSource;

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleResourceNotFound(ResourceNotFoundException ex) {
        String message = messageSource.getMessage("error.notfound", null, LocaleContextHolder.getLocale());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(message);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGlobalException(Exception ex) {
        String message = messageSource.getMessage("error.internal", null, LocaleContextHolder.getLocale());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(message);
    }
}
```

**📌 关键点**
- `LocaleContextHolder.getLocale()` 获取当前请求的语言环境。
- `messageSource.getMessage("error.notfound", null, locale)` 解析 `messages.properties` 返回翻译内容。

---

## **5. 测试 API**

**请求 1：默认语言（英语）**
```shell
curl -H "Accept-Language: en" http://localhost:8080/api/resource/999
```
✅ **返回**
```json
{
  "message": "Resource not found."
}
```

**请求 2：中文**
```shell
curl -H "Accept-Language: zh" http://localhost:8080/api/resource/999
```
✅ **返回**
```json
{
  "message": "资源未找到。"
}
```

**请求 3：泰语**
```shell
curl -H "Accept-Language: th" http://localhost:8080/api/resource/999
```
✅ **返回**
```json
{
  "message": "ไม่พบทรัพยากร"
}
```

---

## **6. 总结**
✅ **拦截器 (`LocaleInterceptor`)** 解析 `Accept-Language` 头，未指定时默认使用 `Locale.ENGLISH`。
✅ **`LocaleConfig`** 设定默认语言为英语，支持多语言。
✅ **`GlobalExceptionHandler`** 统一异常处理，返回国际化错误消息。

此方案可确保 **Spring Boot API 的国际化（i18n）无侵入式支持**，并且默认使用 **英语** 🚀。
