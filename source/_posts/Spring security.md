---
title: Spring Security
tags:
  - Spring Security
  - 权限控制
  - 安全框架
  - Spring
  - Java
categories:
  - Java
  - Spring
  - Spring Security
cover: https://image.runtimes.cc/202404051525605.jpg
abbrlink: 56958
date: 2022-11-19 18:25:00
updated: 2022-11-19 18:25:00
---

# Spring Security

```bash
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

登录业务

```java
@Service
public class LoginService {

    public static Map<String, RedisUser> map = new HashMap<>();

    @Resource
    AuthenticationManager authenticationManager;

    public String login(SysUser sysUser) {

        // AuthenticationManager 进行用户认证
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(sysUser.getUsername(), sysUser.getPassword());
        Authentication authenticate = authenticationManager.authenticate(usernamePasswordAuthenticationToken);

        // 如果认证不通过,给出对应的提示
        if (Objects.isNull(authenticate)) {
            throw new RuntimeException("登录失败");
        }

        // 如果认证通过,使用username给jwt生成一个
        RedisUser principal = (RedisUser) authenticate.getPrincipal();
        map.put(principal.getUsername(), principal);

        return createJWT("1234567", 9990000,  principal.getUsername());
    }

    public void logout() {

        UsernamePasswordAuthenticationToken authentication = (UsernamePasswordAuthenticationToken) SecurityContextHolder.getContext().getAuthentication();
        RedisUser principal = (RedisUser) authentication.getPrincipal();

        String username = principal.getUsername();

        // 从redis里删除
        map.remove(username);

    }
}
```

```java
@RestController
@RequestMapping("/")
public class LoginController {

    @Resource
    LoginService loginService;

    @PostMapping("/user/login")
    public ResponseEntity<HashMap> login(@RequestParam("username") String username,@RequestParam("password") String password){
        SysUser sysUser = new SysUser();
        sysUser.setUsername(username);
        sysUser.setPassword(password);

        HashMap<String, Object> stringObjectHashMap = new HashMap<>();
        stringObjectHashMap.put("token", loginService.login(sysUser));
        return ResponseEntity.status(200).body(stringObjectHashMap);
    }

    @PostMapping("/user/logout")
    public ResponseEntity<String> logout(){
        loginService.logout();
        return ResponseEntity.ok("success");
    }
}
```

验证账户

```java
import com.example.spirngsecutirylearn.pojo.RedisUser;
import com.example.spirngsecutirylearn.pojo.SysUser;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.ArrayList;

@Service
public class TestService implements UserDetailsService {

    static String user = "anthony";
    static String password = "$2a$10$yyR9WuT9JY/bpe1VPU0yguqlv0lWpgzTD9NEetf2.n8y7NXIa1rfm";

    /**
     * DaoAuthenticationProvier 会调用这个方法查询用户,并且返回UserDetails对象
     * @param username
     * @return
     * @throws UsernameNotFoundException
     */
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        // 从数据库查询用户信息
        SysUser sysUser = new SysUser();
        if (username.equals(user)) {
            sysUser.setUsername(user);
            sysUser.setPassword(password);
        }else {
            // 没有查到用户
            throw new RuntimeException("没有查到用户信息");
        }

        // 查询对应的权限信息
        ArrayList<String> strings = new ArrayList<>();
//        strings.add("test");
        strings.add("admin");

        // 返回封装的信息
        return new RedisUser(sysUser,strings);
    }
}
```

缓存的用户对象

```java
package com.example.spirngsecutirylearn.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

@Data
public class RedisUser implements UserDetails {

    SysUser sysUser;

    List<String> list;

    public RedisUser(SysUser sysUser, List<String> list) {
        this.sysUser = sysUser;
        this.list = list;
    }

    /**
     * 权限
     * @return
     */
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return list.stream().map(SimpleGrantedAuthority::new).collect(Collectors.toList());
    }

    @Override
    public String getPassword() {
        return sysUser.getPassword();
    }

    @Override
    public String getUsername() {
        return sysUser.getUsername();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

认证失败的回调

```java
package com.example.spirngsecutirylearn.handler;

import cn.hutool.json.JSONUtil;
import org.springframework.http.ResponseEntity;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class AuthException implements AuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {

        ResponseEntity<String> responseEntity = ResponseEntity.ok().body("认证失败,请重新登录");
        String str = JSONUtil.toJsonStr(responseEntity);
        /// 处理异常
        extracted(response,str);

    }

    private static void extracted(HttpServletResponse response,String string) throws IOException {
        response.setStatus(200);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().println(string);
    }
}
```

授权失败的回调

```java
package com.example.spirngsecutirylearn.handler;

import cn.hutool.json.JSONUtil;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.stereotype.Component;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class AccessException implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        ResponseEntity<String> responseEntity = ResponseEntity.ok().body("权限不租");
        String str = JSONUtil.toJsonStr(responseEntity);
        /// 处理异常
        extracted(response,str);
    }

    private static void extracted(HttpServletResponse response,String string) throws IOException {
        response.setStatus(200);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().println(string);
    }
}
```

开启注解权限控制和自定义注解

```java
@RestController
@RequestMapping("/test")
public class TestController {

    @GetMapping("/hello")
    @PreAuthorize("hasAnyAuthority('test')")
    public ResponseEntity<String> hello(){
        return ResponseEntity.ok("hello server");
    }

    /**
     * 自定义校验权限
     * @return
     */
    @GetMapping("/hello2")
    @PreAuthorize("@anthony.hasAnyAuthority('test')")
    public ResponseEntity<String> hello2(){
        return ResponseEntity.ok("hello server2");
    }
}
```

配置

```java
package com.example.spirngsecutirylearn.cofig;

import com.example.spirngsecutirylearn.filter.JwtFilter;
import com.example.spirngsecutirylearn.handler.AccessException;
import com.example.spirngsecutirylearn.handler.AuthException;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.EnableGlobalAuthentication;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.web.authentication.logout.LogoutFilter;

import javax.annotation.Resource;

@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Resource
    JwtFilter jwtFilter;

    @Resource
    AuthException authException;

    @Resource
    AccessException accessException;

    @Override
    @Bean
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {

        httpSecurity
                // CSRF禁用，因为不使用session
                .csrf().disable()
                // 基于token，所以不需要session
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                // 过滤请求
                .authorizeRequests()
                // 对于登录login 注册register 验证码captchaImage 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 静态资源，permitAll 有没有登录都访问
//                .antMatchers(HttpMethod.GET, "/", "/*.html", "/**/*.html", "/**/*.css", "/**/*.js", "/profile/**").permitAll()
//                .antMatchers("/swagger-ui.html", "/swagger-resources/**", "/webjars/**", "/*/api-docs", "/druid/**").permitAll()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();

//                .and()
//                .headers().frameOptions().disable();
        // 添加Logout filter
//        httpSecurity.logout().logoutUrl("/logout").logoutSuccessHandler(logoutSuccessHandler);
//        // 添加JWT filter
        httpSecurity.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
//        // 添加CORS filter
//        httpSecurity.addFilterBefore(corsFilter, JwtAuthenticationTokenFilter.class);
//        httpSecurity.addFilterBefore(corsFilter, LogoutFilter.class);

        // 添加自定义的认证和授权的自定义失败处理
        httpSecurity.exceptionHandling().authenticationEntryPoint(authException).accessDeniedHandler(accessException);
        httpSecurity.cors();

    }

    /**
     * 密码加密的规则
     * @return
     */
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    /**
     * BCryptPasswordEncoder 的测试方法
     * @param args
     */
    public static void main(String[] args) {
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();

        // 加密
        String encode = bCryptPasswordEncoder.encode("123456");
        System.out.println(encode);

        // 解密
        boolean matches = bCryptPasswordEncoder.matches("123456", encode);
        System.out.println(matches);
    }
}
```

自定义注解

```java
@Component("anthony")
public class TestExpress {

    public boolean hasAnyAuthority(String authority){

        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        RedisUser principal = (RedisUser) authentication.getPrincipal();
//        List<String> list = (List<String>)principal.getAuthorities();
        List<SimpleGrantedAuthority> authorities = (List<SimpleGrantedAuthority>) principal.getAuthorities();
        for (SimpleGrantedAuthority simpleGrantedAuthority : authorities) {
            if (simpleGrantedAuthority.getAuthority().equals(authority)) {
                return true;
            }
        }
        return false;

    }
}
```

找Java包的路径
```shell
# mac
/usr/libexec/java_home -V
```
