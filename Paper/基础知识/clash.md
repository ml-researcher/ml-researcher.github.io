https://hellodk.cn/post/870

```
dns:
  enable: true # 启用自定义DNS
  ipv6: false # default is false
  listen: 0.0.0.0:1053
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16 # if you don't know what it is, don't change it
  default-nameserver:
    - 180.76.76.76
    - 223.5.5.5
    - 119.29.29.29
  nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query

  fallback:
    - tls://1.1.1.1:853
    - tls://1.0.0.1:853
    - 101.6.6.6:5353
```