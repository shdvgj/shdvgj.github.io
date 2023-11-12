---
title: 缩写短语概念简单介绍
date: 2023-11-07 15:53:20
tags:
---

#### RBAC(Role-based access control)

<!-- more -->
#### ACID(Atomicity, Consistency, Isolation, Durability)

#### CAP(Consistency, Availability, Partition tolerance)

#### DSL(Domain Specific Language)

#### CSFR(Cross-site request forgery)

#### LDAP(Lightweight Directory Access Protocol)

#### JDBC(Java Database Connectivity)

#### EJB(Enterprise Java Beans)

#### SLA(Service-Level Agreement)

#### CDN(Content Delivery Network)

CDN（Content Delivery Network）是一种分布式网络架构，旨在提供高效的内容传输和交付服务。它通过将内容存储在分布在全球各地的服务器节点上，并根据用户的地理位置和网络条件选择最近的节点来提供内容，从而加快内容的传输速度和用户访问体验。

- 工作原理
  - 内容缓存：CDN将内容缓存到分布在全球各地的边缘服务器节点上。这些节点位于网络的边缘，靠近用户，以减少内容传输的延迟和网络拥塞。
  - 就近访问：当用户请求访问某个内容时，CDN会根据用户的地理位置和网络状况，选择最近的服务器节点提供内容。这样可以减少内容传输的距离，提高访问速度。
  - 负载均衡：CDN使用负载均衡算法来分配用户请求到合适的服务器节点。通过合理分配请求负载，CDN可以提高系统的吞吐量和可扩展性。
  - 动态内容加速：除了静态内容，CDN还可以加速动态内容的传输。它可以缓存动态内容的一部分或整个页面，减轻源服务器的负载，并提供更快的响应时间。

#### DNS(Domain Name System)

DNS（Domain Name System）是互联网中用于将域名转换为对应 IP 地址的系统。它充当了互联网的“电话簿”，将易记的域名映射到计算机理解的 IP 地址，方便用户通过域名访问网站、发送电子邮件等。

- 域名解析请求：当用户在浏览器中输入一个域名时，浏览器会向本地 DNS 解析器发起域名解析请求。
- 本地 DNS 解析器：本地 DNS 解析器是用户设备或网络中的一部分，它负责处理域名解析请求。如果本地解析器已经缓存了该域名的 IP 地址，则直接返回缓存的结果。否则，它会向根域名服务器发起请求。
- 根域名服务器：根域名服务器是 DNS 解析的起点，它存储了顶级域名服务器的信息。当本地解析器向根域名服务器发起请求时，根域名服务器会返回对应顶级域名服务器的 IP 地址。
- 顶级域名服务器：顶级域名服务器负责管理顶级域名（如.com、.org、.net等）的域名解析。当本地解析器向顶级域名服务器发起请求时，顶级域名服务器会返回对应权威域名服务器的 IP 地址。
- 权威域名服务器：权威域名服务器是负责管理特定域名的服务器。当本地解析器向权威域名服务器发起请求时，权威域名服务器会返回该域名对应的 IP 地址。
- 返回 IP 地址：本地解析器收到权威域名服务器返回的 IP 地址后，将其缓存，并将解析结果返回给浏览器。
- 建立连接：浏览器收到 IP 地址后，将使用该地址与目标服务器建立连接，并发送相应的请求。