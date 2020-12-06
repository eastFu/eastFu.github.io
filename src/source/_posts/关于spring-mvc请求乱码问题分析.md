---
title: 关于spring mvc请求乱码问题分析
toc: true
tags: 编程
categories:
  - 编程
  - Java
abbrlink: 31802
date: 2015-04-26 19:46:07
---
## spring乱码
spring mvc 在请求参数乱码的时候可以使用 CharacterEncodingFilter来做处理，配置如下：
```
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
## 原理
很简单，就是拦截所有/*的请求，并将request和response的编码设置为UTF-8，关键的源代码如下：
```
@Override
protected void doFilterInternal(
        HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
        throws ServletException, IOException {

    if (this.encoding != null && (this.forceEncoding || request.getCharacterEncoding() == null)) {
        request.setCharacterEncoding(this.encoding);
        if (this.forceEncoding) {
            response.setCharacterEncoding(this.encoding);
        }
    }
    filterChain.doFilter(request, response);
}
```
## get请求
但是这只能解决post的请求，对于get请求传递的参数，这种方法是不起作用的。最简单的办法就是修改tomcat的配置文件，新增配置项： useBodyEncodingForURI=”true”
```
<Connector connectionTimeout="20000" port="80" protocol="HTTP/1.1" redirectPort="8443" useBodyEncodingForURI="true"/>
```
## 原因
```
tomcat 默认按ISO-8859-1进行URL解码，如果请求方式为get，参数包含中文字符，则会出现乱码的问题，按ISO-8859-1进行URL解码，useBodyEncodingForURI的作用是根据响应该请求的页面的request.setCharacterEncoding参数对数据进行的重新编码，不同的页面可以有不同的重新编码的编码，默认：false 不启用，设置为 true，那么我们上面配置的filter就生效了。
```
## 其它方式
tomcat还有一个URIEncoding的配置项，它的作用是对所有GET方式的请求的数据进行统一的重新编码，简单暴力，如果尝试过多次还未解决乱码问题，可以添加这样一个配置项：
```
<Connector connectionTimeout="20000" port="80" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="utf-8"/>
```
