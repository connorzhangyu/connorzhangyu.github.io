---
title: Cloudflare
tags:
  - 域名
categories:
  - Cloud
cover: https://image.runtimes.cc/202404051526915.webp
abbrlink: 24517
date: 2022-11-19 18:25:00
updated: 2022-11-21 18:57:00
---

# 关闭IPV6

![](https://image.runtimes.cc/202404051517919.png)

```bash
curl -X PATCH "https://api.cloudflare.com/client/v4/zones/cfa068f2a1e282a2731ba29a0a24b052/settings/ipv6" \
     -H "X-Auth-Email: y950727@gmail.com" \
     -H "X-Auth-Key: 830419c40277b1cff929397d7c08b2012c2a" \
     -H "Content-Type: application/json" \
     --data '{"value":"off"}'
```

URL上的地址:
![](https://image.runtimes.cc/202404051411183.png)

X-Auth-Key:
![](https://image.runtimes.cc/202404051411721.png)

