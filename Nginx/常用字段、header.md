``` toc
```
## 字段
#### $remote_addr
- 可能是上游代理或LB的IP，不是真实的客户端IP，真实的客户端IP可能在`X-Forwarded-For`中。
- 如果编译时添加`--with-http_realip_module`模块的话，`$remote_addr` 就会自动变成 `X-Forwarded-For` 中的第一个 IP
>`--with-http_realip_module`：允许请求标头更改客户端的IP地址，默认为关


## Header
#### X-Forwarded-For
X-Forwarded-For: <客户端IP>, <第一层代理IP>, <第二层代理IP>, ...

