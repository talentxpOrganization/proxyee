### HTTP代理服务器
    支持HTTP、HTTPS、WebSocket,HTTPS采用动态签发SSL证书,可以拦截http、https的报文并进行处理。
    例如：http(s)协议抓包,http(s)动态替换请求内容或响应内容等等。
#### HTTPS支持
    需要导入项目中的CA证书(src/resources/ca.crt)至受信任的根证书颁发机构。
#### 二级代理
    可设置二级代理服务器,支持http,socks4,socks5。
#### 启动
```
//new HttpProxyServer().start(9999);  //不做任何拦截处理

  new HttpProxyServer() //拦截修改请求头和响应头和使用二级代理
      .proxyConfig(new ProxyConfig(ProxyType.SOCKS5, "127.0.0.1", 1085))  //使用socks5二级代理(例如：使用shadowsocks翻墙)
      .proxyInterceptFactory(() -> new HttpProxyIntercept() { //拦截http请求和响应
        @Override
        public boolean beforeRequest(Channel clientChannel, HttpRequest httpRequest) {
          //替换UA，伪装成手机浏览器
          httpRequest.headers().set(HttpHeaderNames.USER_AGENT,"Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1");
          return true;
        }

        @Override
        public boolean afterResponse(Channel clientChannel, Channel proxyChannel,
            HttpResponse httpResponse) {
          //拦截响应，添加一个响应头
          httpResponse.headers().add("intercept", "test");
          return true;
        }

      }).start(9999); //启动http代理服务器，监听9999端口
```

#### 流程
SSL握手

![SSL握手](https://sfault-image.b0.upaiyun.com/751/727/751727588-59ccbe3293bef_articlex)

HTTP通讯

![HTTP通讯](https://sfault-image.b0.upaiyun.com/114/487/1144878844-59ccbe42037b6_articlex)