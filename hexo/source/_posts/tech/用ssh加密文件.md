---
title: 用ssh加密文件
date: 2021-12-06 15:40:05
tags: 
- linux
- translate
comments: true
categories: 
- 学习
---
**声明**

翻译自[Andrew Ayer博客](https://www.agwa.name/blog/post/ssh_signatures)
翻译水平不佳，自己学习使用
---

## 现在可以使用SSH密钥给任意数据加密

你知道吗，你可以使用ssh-keygen命令加密和验证任意数据，比如文件和发行软件？尽管这个特性并不是特别新（在2019年随着OpenSSH 8.0发布），依然很少有人知道。
这就有点尴尬，因为这个功能非常有用而且可以作为PGP的有效替换用以加密数据。如果你现在在用PGP加密数据，你可以考虑转到SSH签名上。

以下是我喜欢用SSH签名的原因：
- **不是PGP。** 多年来，安全专家已经[对PGP做出了警告](https://latacora.micro.blog/2019/07/16/the-pgp-problem.html)，包括其最重要的组件GnuPG/GPG。
PGP太复杂了，用户体验非常糟糕，而且充斥着门槛很高的粗糙老旧的密码学原理，
- **SSH无处不在，而且用户早就有SSH密钥了。** 如果你用的是 Debian Bullseye 或者 Ubuntu 20.04以及更新的版本，你已经有了足够的SSH安装版本。如果你有用Github，
或者任何其他的借助SSH命令来验证的服务，你也早就有了一个SSH密钥用于创建签名了。这就是为什么我对SSH签名更热衷，而不是其他PGP的替代品，比如[signify](https://github.com/aperezdc/signify) 或者 
[minisign](https://jedisct1.github.io/minisign/)。后两者很优秀，但是需要你安装新的软件和创建一个新的密钥，这就会阻碍更大范围的兼容性。
- **SSH密钥很容易分配。** SSH公钥只用一行代码就可以轻松到处复制。你不需要网络白名单或者费心去配置密钥的"信任等级"。Github作为一个密钥分配服务早就演示过，比起原先的
PGP密钥服务器更加简易也更加安全。你可以通过访问网页https://github.com/USERNAME.keys去检索任意用户的SSH公钥。（比如，我的公钥在[https://github.com/AGWA.keys](https://github.com/AGWA.keys)）
（这个例子可能不适用于所有的情况，毕竟Github在这里作为一个可信任的第三方，而且你也必须相信他们不会对大众的公钥作假。但是依赖于Github这样的专业安全团队作为可信赖的第三方，应该比几乎不可能用到的PGP的网络白名单更好。
在实践中，如果你曾经想搞清楚如何审核透明度日志，那第三方的公钥的透明度需要考虑进去。）
- **SSH有可选的轻量级证书。** SSH证书不是必需的（而且大部分人也用不到），但是证书可以让你的操作更轻松，SSH有一套比X.509简单很多的轻量级证书系统。这使得SSH签名和S/MIME一样是一个好的选项。

## 你现在可以使用SSH给Git提交和标签加签名
给Git的提交和标签加签名可以帮助你确认你的的仓库在被别人使用的时候，代码未曾被篡改。但是，你必须用PGP或者S/MIME来做到这件事，而我个人从2018年PGP密钥过期后就没在这件事上被困扰过。
马上在Git 2.34这些都会改变了，这个版本支持了SSH签名。（TODO 咋翻译都不对。。）

## 文件签名
像这样直接对一个文件进行签名：
```bash
ssh-keygen -Y sign -f ~/.ssh/id_ed25519 -n file file_to_sign
```
需要更改的参数如下：
- `~/.ssh/id_ed25519`是你私钥的路径。例子里的是你的SSH Ed25519私钥的标准路径。如果你有一个RSA密钥，改为使用id_rsa。

- `file` 参数是"命名空间"，用来描述签名的目的，对于一般文件的签名，SSH将其定义为`file`，邮件的签名定义为`email`，Git则使用`git`作为签名。
如果你的签名用于其他目的，比如一个一般协议，你必须指明你自己的命名空间。这样，你可以避免来自一个单一协议信息的合法签名被移除后，或者附属在不同的协议信息，发起的跨协议攻击。
如果对于协议签名不使用特定的命名空间，那第一个协议的签名就有可能被视作第二个签名的协议，从而被认为是合法的。
命名空间可以是随机字符。为了保证全局的命名空间的唯一性，SSH推荐对于你自己的签名使用email作为命名空间的一部分。比如，我会使用类似于`protocolname-v1@agwa.name`的名字作为命名空间。

- `file_to_sign` 是要被签名的文件的路径。

签名会写入一个叫做`file_to_sign.sig`的新文件中，格式如下：
```bash
-----BEGIN SSH SIGNATURE-----
U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAg2rirQQddpzEzOZwbtM0LUMmlLG
krl2EkDq4CVn/Hw7sAAAAEZmlsZQAAAAAAAAAGc2hhNTEyAAAAUwAAAAtzc2gtZWQyNTUx
OQAAAEDyjWPjmOdG8HJ8gh1CbM8WDDWoGfm+TTd8Qa8eua9Bt5Cc+43S24i/JqVWmk98qV
YXoQmOYL4bY8t/q7cSNeMH
-----END SSH SIGNATURE----- 
```
如果你指明了文件名，那文件签名会从该标准输入并从该标准写出。

## 签名验证
签名验证稍微更复杂点。首先你需要创建一个被允许的签名者文件，该文件将邮件地址映射到公钥，类似于这样：
```bash
alice@example.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINq4q0EHXacxMzmcG7TNC1DJpSxpK5dhJA6uAlZ/x8O7
alice@example.com ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCfHGCK5jjI/Oib4vRBLB9rG30A8y/Br9U75rfAYsitwFPFfl/CaTAvfRlW1lIBqOCshLWxGsN+PFiJCiCWzpW4iILkD5X5KcBBYHTq1ojYXb70BrQXQ+QBDcGxqQjcOp/uTq1D9Z82mYq/usI5wdz6f1KNyqM0J6ZwRXMu6u7NZaAwmY7j1fV4DRiYdmIfUDIyEdqX4a1Gan+EMSanVUYDcNmeBURqmTkkOPYSg8g5xYgcXBMOZ+V0ZUjreV9paKraUD/mVDlZbb/VyWhJGT4FLMNXHU6UHC2FFgqANMUKIlL4vhqc23MoygKbfF3HgNB6BNfv3s+GYlaQ3+66jc5j
bob@example.net ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBgQuuEvhUXerOTIZ2zoOx60M/HHJ/tcHnD84ZvTiX5b
```
一旦你拥有了你的被允许的签名文件，验证的步骤如下：
```bash
ssh-keygen -Y verify -f allowed_signers -I alice@example.com -n file -s file_to_verify.sig < file_to_verify
```
你需要修改如下参数：
- `allowed_signers` 是被允许的签名者文件的路径。
- `alice@example.com` 是对文件加签名的用户邮件地址。该邮件地址会在允许的签名者文件里进行查找到对应的公钥。
- `file` 是"命名空间"，该参数必须匹配前文描述的签名中的命名空间。
- `file_to_verify.sig` 是签名文件的路径。
- `file_to_verify` 是被验证的文件的路径。注意该文件是从标准读入的。在前面的命令中，`<`这个shell操作符用于重定向标准到该文件。

如果签名是有效的，命令行会返回状态0并打印如下信息：
```bash
Good "file" signature for alice@example.com with ED25519 key SHA256:ZGa8RztddW4kE2XKPPsP9ZYC7JnMObs6yZzyxg8xZSk
```
否则，命令行会返回非0状态并打印错误信息。

## SSH密钥的复用安全吗？

简单来说：安全。

对一个不同协议总是要小心的对密钥进行复用。如果不注意的话，有可能导致跨协议攻击的风险。比如，当一个Git签名信息的结构和SSH协议信息的结构相似，攻击者就可以通过SSH副本挪用签名，并伪造一个Git的人工提交。

幸运的是，SSH协议信息的结构和ssh-keygen签名信息的结构并不相似，因此并没有这方面风险的担忧。

为了证明这点，我们可以参考这篇[RFC 4252 section 7](https://datatracker.ietf.org/doc/html/rfc4252#section-7)，阐明了SSH密钥是如何在传统用途上通过SSH验证用户登录服务器。
RFC指出输入的签名语法拥有如下结构：
```bash
string    session identifier
byte      SSH_MSG_USERAUTH_REQUEST
string    user name
string    service name
string    "publickey"
boolean   TRUE
string    public key algorithm name
string    public key to be used for authentication
```
第一个字段是会话标识，是个字符串。在SSH协议中，字符串的前缀由一个[32位字符](https://datatracker.ietf.org/doc/html/rfc4251#section-5)构成。会话标识是一个[hash](https://datatracker.ietf.org/doc/html/rfc4253#section-7.2)值。除非hash值很短，否则一般的上述签名的前三位字符都会是0。

同时，[PROTOCOL.sshsig](https://github.com/openssh/openssh-portable/blob/d575cf44895104e0fcb0629920fb645207218129/PROTOCOL.sshsig)分发OpenSSH仓库，表明了如何通过`ssh-keygen-generated`签名使用SSH密钥。以下是输入的签名语法的结构：
```bash
#define MAGIC_PREAMBLE "SSHSIG"

byte[6]   MAGIC_PREAMBLE
string    namespace
string    reserved
string    hash_algorithm
string    H(message)
```
在这里，头三个字节是SSH，来自magic preamble（直译是魔法序言？）。SSH协议签名输入的头三个字节之后就开始和ssh-keygen签名输入不一样，SSH客户端和ssh-keygen不会生成同样的签名。因此，在这里不会有跨协议攻击的风险，我也有绝对的信心使用现存的SSH密钥，通过ssh-keygen来对信息加密。
