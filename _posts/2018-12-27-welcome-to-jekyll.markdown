---
layout: post
title:  "Cookie介绍"
date:   2018-12-27 18:14:06 +0800
categories: Maco
---
#### cookie的起源

早期Web开发面临的最大问题之一是如何管理状态。简言之，服务器端没有办法知道两个请求是否来自于同一个浏览器。那时的办法是在请求的页面中插入一个token，并且在下一次请求中将这个token返回（至服务器）。这就需要在form中插入一个包含token的隐藏表单域，或着在URL的qurey字符串中传递该token。这两种办法都强调手工操作并且极易出错。

Lou Montulli(卢·蒙特利)，那时是网景通讯的一个雇员，被认为在1994年将“magic cookies”的概念应用到了web通讯中。他意图解决的是web中的购物车，现在所有购物网站都依赖购物车。他的最早的说明文档提供了一些cookies工作原理的基本信息该文档在RFC2109中被规范化（这是所有浏览器实现cookies的参考依据），并且最终逐步形成了REF2965.Montulli最终也被授予了关于cookies的美国专利。网景浏览器在它的第一个版本中就开始支持cookies，并且当前所有web浏览器都支持cookies。

 [RFC2109](https://tools.ietf.org/html/rfc2109)

 [RFC2965](https://tools.ietf.org/html/rfc2965) 

 [RFC6265](https://tools.ietf.org/html/rfc6265)

#### cookie是什么

Cookie是服务器保存在浏览器的一小段文本信息，每个 Cookie 的大小一般不能超过4KB。浏览器每次向服务器发出请求，就会自动附上这段信息。

#### cookie的用途

1. 会话管理<br>
   1.1 记录用户的登录状态是cookie最常用的用途。通常web服务器会在用户登录成功后下发一个签名来标记session的有效性，这样免去了用户多次认证和登录网站。<br>
   1.2 记录用户的访问状态，例如导航啊，用户的注册流程啊。
2. 个性化信息<br>
   2.1 Cookie也经常用来记忆用户相关的信息，以方便用户在使用和自己相关的站点服务。例如：ptlogin会记忆上一次登录的用户的QQ号码，这样在下次登录的时候会默认填写好这个QQ号码。<br>
   2.2 Cookie也被用来记忆用户自定义的一些功能。用户在设置自定义特征的时候，仅仅是保存在用户的浏览器中，在下一次访问的时候服务器会根据用户本地的cookie来表现用户的设置。例如google将搜索设置（使用语言、每页的条数，以及打开搜索结果的方式等等）保存在一个COOKIE里。<br>

3. 记录用户的行为<br>
   最典型的是公司的TCSS系统。它使用Cookie来记录用户的点击流和某个产品或商业行为的操作率和流失率。当然功能可以通过IP或http header中的referrer实现，但是Cookie更精准一些。



#### 创建cookie

服务器如果希望在浏览器保存 Cookie，就要在 HTTP 回应的头信息里面，放置一个`Set-Cookie`字段。Set-Cookie消息的格式如下面的字符串（中括号中的部分都是可选的）。

```
Set-Cookie:name=value [ ;expires=date][ ;max-age=time][ ;domain=domain][ ;path=path][ ;secure][ ;httponly]
```



上面可选的字段是Cookie的属性，一个`Set-Cookie`字段里面，可以同时包括多个属性，没有次序的要求。如下一个例子：

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```



如果服务器想改变一个早先设置的 Cookie，必须同时满足四个条件：Cookie 的`key`、`domain`、`path`和`secure`都匹配。只要有一个属性不同，就会生成一个全新的 Cookie，而不是替换掉原来那个 Cookie。举例来说，如果原始的 Cookie 是用如下的`Set-Cookie`设置的。

```
Set-Cookie: key1=value1; domain=example.com; path=/blog
```



改变上面这个 Cookie 的值，就必须使用同样的`Set-Cookie`。

```
Set-Cookie: key1=value2; domain=example.com; path=/blog
```



HTTP 回应可以包含多个`Set-Cookie`字段，即在浏览器生成多个 Cookie，如下。

```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```

#### cookie的属性

##### Expires，Max-Age

`Expires`属性指定一个具体的到期时间，到了指定时间以后，浏览器就不再保留这个 Cookie。它的值是 UTC 格式。可以通过设置它的`expires`属性为一个过去的日期来删除这个cookie。

`Max-Age`属性指定从现在开始 Cookie 存在的秒数，比如`60 * 60 * 24 * 365`（即一年）。过了这个时间以后，浏览器就不再保留这个 Cookie。

如果同时指定了`Expires`和`Max-Age`，那么`Max-Age`的值将优先生效。

如果`Set-Cookie`字段没有指定`Expires`或`Max-Age`属性，那么这个 Cookie 就是 Session Cookie，即它只在本次对话存在，一旦用户关闭浏览器，浏览器就不会再保留这个 Cookie。

##### Domain

`Domain`属性指定浏览器发出 HTTP 请求时，哪些域名要附带这个 Cookie。如果没有指定该属性，浏览器会默认将其设为当前 URL 的一级域名，比如`www.example.com`会设为`.example.com`，而且以后如果访问`.example.com`的任何子域名，HTTP 请求也会带上这个 Cookie。如果服务器在`Set-Cookie`字段指定的域名，不属于当前域名，浏览器会拒绝这个 Cookie。

 RFC2109规定domain必须满足以.开头。

##### Path

`Path`属性指定浏览器发出 HTTP 请求时，哪些路径要附带这个 Cookie。只要浏览器发现，`Path`属性是 HTTP 请求路径的开头一部分，就会在头信息里面带上这个 Cookie。比如，`PATH`属性是`/`，那么请求`/docs`路径也会包含该 Cookie。当然，前提是域名必须一致。path属性的默认值是发送Set-Cookie消息头所对应的URL中的path部分。

##### Secure

`Secure`属性指定浏览器只有在加密协议 HTTPS 下，才能将这个 Cookie 发送到服务器，以确保cookie在从客户端传递到Server的过程中始终加密的。该属性只是一个开关，不需要指定值。如果通信是 HTTPS 协议，该开关自动打开。

##### HttpOnly

`HttpOnly`属性指定该 Cookie 无法通过 JavaScript 脚本拿到，主要是`Document.cookie`属性、`XMLHttpRequest`对象和 Request API 都拿不到该属性。这样就防止了该 Cookie 被脚本读到，只有浏览器发出 HTTP 请求时，才会带上该 Cookie。

#### 发送cookie

当一个cookie存在，并且可选条件允许的话，该cookie的值会在接下来的每个请求中被发送至服务器。cookie的值被存储在名为Cookie的HTTP消息头中，例如：

```
Cookie : name=value
```



如果在指定的请求中有多个cookies，那么它们会被分号和空格分开，例如：

```
Cookie:name1=value1 ; name2=value2 ; name3=value3
```



#### Cookie缺陷

1. Cookie会被附加在每个HTTP请求中，所以无形中增加了流量。

2. 由于在HTTP请求中的Cookie是明文传递的，所以安全性成问题，除非用HTTPS。
3. Cookie的大小限制在4KB左右，对于复杂的存储需求来说是不够用的。
4. 状态不一致，后退导致cookie不会重置。

### Cookie在Android中的使用

**Cookie持久化**

持久化保存cookie有很多方式，可以用数据库，可以用文件，SharedPreferences，还可以保存到系统Webview的CookieManager里（其实也是个数据库）。

如果我们自己本地保存cookie，要做好本地Cookie和Webview的cookie同步，所以最好的办法是保存到系统Webview的CookieManager里，取的时候从Webview的CookieManager里取。

例子：

[https://github.com/franmontiel/PersistentCookieJar](https://github.com/franmontiel/PersistentCookieJar) 

  A persistent CookieJar implementation for OkHttp 3 based on SharedPreferences.

#### OkHttp3 中实现 Cookie 持久化管理

3.0之后OKHttp是加了CookieJar和Cookie两个类的，通过实现CookieJar即可管理cookie。

```java
private class CookiesManager implements CookieJar {

    private final PersistentCookieStore cookieStore = new PersistentCookieStore(getApplicationContext());

    @Override
    public void saveFromResponse(HttpUrl url, List<Cookie> cookies) {
        if (cookies != null && cookies.size() > 0) {
            for (Cookie item : cookies) {
                cookieStore.add(url, item);
            }
        }
    }

    @Override
    public List<Cookie> loadForRequest(HttpUrl url) {
        List<Cookie> cookies = cookieStore.get(url);
        return cookies;
    }
}

OkHttpClient.Builder builder = new OkHttpClient.Builder();
builder.cookieJar(new CookiesManager());
```

#### WebView中的Cookie机制

WebView是基于webkit内核的UI控件，相当于一个浏览器客户端。它会在本地维护每次会话的cookie(保存在data/data/package_name/app_WebView/Cookies)。

![cookie目录](../images/image-20180914150451187.png)



当WebView加载URL的时候,WebView会从本地读取该URL对应的cookie，并携带该cookie与服务器进行通信。WebView通过android.webkit.CookieManager类来维护cookie。CookieManager是 WebView的cookie管理类。

之前同步cookie需要用到CookieSyncManager类，现在这个类已经被deprecated。如今WebView已经可以在需要的时候自动同步cookie了。

**CookieSyncManager**

在SDK21以下，使用CookieSyncManager在内存和存储器之间同步浏览器的cookie。另外CookieSyncManager同步策略是在一个独立的线程里定时进行同步。

1. cookie开始同步：注意每次同步的时间间隔是5分钟

   ```java
   CookieSyncManager.createInstance(context); 
   CookieSyncManager.getInstance().startSync();
   ```

2. cookie停止同步：

   ```java
   CookieSyncManager.getInstance().stopSync()
   ```

3. cookie立即同步：调用了该方法会立即进行cookie的同步，代码如下： 

   ```
   //一般是在webview中的onPageFinished(WebView, String)方法进行强制同步
   CookieSyncManager.getInstance().sync()
   ```

4. 删除cookie操作： 

   ```
   CookieSyncManager.createInstance(this); 
   CookieManager.getInstance().removeAllCookie(); CookieManager.getInstance().removeSessionCookie(); CookieSyncManager.getInstance().sync(); 
   ```

**CookieManager**

从sdk21之后，webview已经内置了cookie的同步操作了。

删除所有Cookie

```java
CookieManager.getInstance().removeAllCookies(null); 
CookieManager.getInstance().flush();
```

保存Cookie

```
CookieManager.getInstance().setCookie(String url, String value)
```

获取Cookie

```
CookieManager.getInstance().getCookie(url)
```



综合两个Manager, 最后写法：

同步Cookie

```java
CookieSyncManager.createInstance(this); 
if (Build.VERSION.SDK_INT < 21) {
	CookieSyncManager.getInstance().sync();
} else {
	CookieManager.getInstance().flush();
}
```

删除所有Cookie

```java
CookieSyncManager.createInstance(this); 
CookieManager.getInstance().removeAllCookie(); CookieManager.getInstance().removeSessionCookie(); 
if (Build.VERSION.SDK_INT < 21) {
	CookieSyncManager.getInstance().sync();
} else {
	CookieManager.getInstance().flush();
}

```



### Cookie在iOS中的使用

##### Cookie获取和存储

NSHTTPCookieStorage会自动存储NSHttpRequest的所有请求产生的cookie，需要删除的时候我们会手动移除cookie。

##### Cookie的内容

cookie 中包含了一个由 名字 = 值 （name=value） 这样的信息构成的任意列表

```
CM_S=v1_2_b91cf9ddaaf767e7a986cd509d66806c; expires=Fri, 12-Oct-2018 19:00:00 GMT; path=/; domain=charminginsurance.cn; httponly
```



##### Cookie的手动管理

cookie的手动主要有添加和删除两中，从服务器获取到cookie后，会将cookie存在本地，给请求头添加cookie。本地cookie不能使用或者用户退出登录，会将存在本地cookie清除，同时清空缓存。

````objective-c
//添加Cookie
[NetTools.requestSerializer setValue:netCookie forHTTPHeaderField:@"cookie"];
NSUserDefaults *userDefault = [NSUserDefaults standardUserDefaults];
    [userDefault setObject:cookie forKey:COOKIE_KEY];
    [userDefault synchronize];
````



```objective-c
//清除本地和afn的cookie 和缓存
    NSUserDefaults *userDefault = [NSUserDefaults standardUserDefaults];
    [userDefault removeObjectForKey:COOKIE_KEY];
    NSArray *arrOfCookie = [[NSHTTPCookieStorage sharedHTTPCookieStorage] cookiesForURL:[NSURL URLWithString:BaseURL]];
    LCLog(@"%lu",(unsigned long)arrOfCookie.count);
    for (NSHTTPCookie *cookie in arrOfCookie)
    {
        [[NSHTTPCookieStorage sharedHTTPCookieStorage] deleteCookie:cookie];
    }
    [userDefault synchronize];
    NetTools.netCookie = nil;
```

#### UIWeview和WKWebview中的Cookie使用

UIWebView会将NSHttpRequest的所有请求产生的cookie自动保存，并且，在同一个app内多个UIWebView之间共享，不需要我们做任何操作。

WKWebView不会和NSHttpRequest共享cookie，因此，如果登录接口用AFN，那么WKWebView是读取不到登录之后的cookie的。需要手动添加cookie。

WKWebView中，只有使用了同一个WKProcessPool的WKWebView，才会共享cookie在单例中，创建一个WKProcessPool单例对象，讲WKwebview的processPool属性设置为同一个就可以了。



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/