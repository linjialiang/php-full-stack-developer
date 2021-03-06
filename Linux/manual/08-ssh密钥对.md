# SSH 密钥对

通常我们会使用密码的方式来登录 linux，但是这很容易被暴力破解，对服务器的安全造成隐患，为此先辈们想到了一个更好的办法来保证安全，而且让你可以放心地用 root 账户从远程登录——那就是通过密钥方式登陆！

## 一、密钥对登陆原理

密钥形式登录的原理是：

1. 利用密钥生成器制作一对密钥——一只公钥和一只私钥；
2. 将公钥添加到服务器的某个账户上，然后在客户端利用私钥即可完成认证并登录；
3. 这样一来，没有私钥，任何人都无法通过 SSH 暴力破解你的密码来远程登录到系统；
4. 此外，如果将公钥复制到其他账户甚至主机，利用私钥也可以登录。

## 二、公钥和私钥详解

因为公钥、私钥、加密、认证这些都是较为复杂的问题，其概念不太容易理解，理解不透就容易产生各种似是而非的概念，为了让大家对于密码学有进一步的了解，这里我就详细解说一下公钥和私钥的具体作用和使用方法。

1.  加密和认证

    首先我们需要区分加密和认证这两个基本概念。

    ```text
    - 加密：是将数据资料加密，使得非法用户即使取得加密过的资料，也无法获取正确的资料内容，所以数据加密可以保护数据，防止监听攻击。其重点在于数据的安全性。
    - 身份认证：是用来判断某个身份的真实性，确认身份后，系统才可以依不同的身份给予不同的权限。其重点在于用户的真实性。两者的侧重点是不同的。
    ```

2.  公钥和私钥

    在现代密码体制中加密和解密是采用不同的密钥（公开密钥），也就是非对称密钥密码系统，每个通信方均需要两个密钥，即公钥和私钥，这两把密钥可以互为加解密。

    ```text
    公钥：是公开的，不需要保密；
    私钥：是由个人自己持有，并且必须妥善保管和注意保密。
    ```

3.  公钥私钥的原则

    | 序号 | 原则                                                                         |
    | ---- | ---------------------------------------------------------------------------- |
    | 01   | 一个公钥对应一个私钥                                                         |
    | 02   | 密钥对中，让大家都知道的是公钥，不告诉大家，只有自己知道的，是私钥。         |
    | 03   | 如果用其中一个密钥加密数据，则只有对应的那个密钥才可以解密。                 |
    | 04   | 如果用其中一个密钥可以进行解密数据，则该数据必然是对应的那个密钥进行的加密。 |

    非对称密钥密码的主要应用就是 `公钥加密` 和 `公钥认证` ，而公钥加密的过程和公钥认证的过程是不一样的，下面我就详细讲解一下两者的区别。

4.  基于公开密钥的加密过程

    比如：有两个用户 `emad` 和 `Joyce` emad 想把一段 `通过双钥加密的` 明文信息发送给 Joyce，Joyce 有一对公钥和私钥，那么加密解密的过程如下：

    ```text
    - Joyce 将她的公开密钥传送给 emad；
    - emad 用 Joyce 发给他的公开密钥加密这段明文信息，然后传送给 Joyce；
    - Joyce 用他的私人密钥解密 emad 发过来的这段加密过的明文信息；
    - emad 使用 Joyce 公开的公钥进行加密，Joyce用自己保密的私钥进行解密。
    ```

5.  基于公开密钥的认证过程

    身份认证和加密就不同了，主要用户鉴别用户的真伪。这里我们只要能够鉴别一个用户的私钥是正确的，就可以鉴别这个用户的真伪。

    ```text
    - Joyce 想让 emad 知道自己是真实的 Joyce 而不是被人假冒的；
    - 因此 Joyce 需要使用 "私钥密码学" 对文件签名后，再发送给 emad；
    - emad 使用 Joyce 给的公钥对文件进行解密，如果可以解密成功，则证明 Joyce 的私钥是正确的；
    - 因而就完成了对 Joyce 的身份鉴别。
    ```

    整个身份认证的过程如下：

    ```text
    - Joyce 用她的私人密钥对文件加密，从而对文件签名；
    - Joyce 将签名的文件传送给 emad；
    - emad 用 Joyce 给的公钥解密文件，从而验证签名。
    - Joyce 使用自己的私钥加密，emad 使用 Joyce 给的公钥进行解密。
    ```

6.  授权密钥是公钥还是私钥？

    由上面 4 和 5 两点可以看出，认证和加密都是通过公钥来认证和加密的，这是因为：

    ```text
    - 公钥可以向大家开放，而服务器上的授权密钥是需要向所有客户端公开的，所以必须是公钥；
    - 任何一台电脑（客户端）都可以通过密钥来认证服务器上的公钥，而只有对应的私钥才能认证成功，所以只要私钥不泄露，其它人是无法认证通过的。
    ```

7.  公钥和私钥使用场景

    ```text
    - 公钥使用场景：
        1. 同一服务器上的多个用户可以放置同一个公钥；
        2. 不同服务器上也可以放置同一个公钥。
        3. 一个公钥文件可以放置多个公钥的代码。
    - 私钥使用场景：
        1. 任意一台客户端上，都可以使用私钥认证公钥；
        2. 私钥不能泄漏给他人，否则别人也能通过公钥认证，如果你对服务器没更多的限制，则别人也可以登陆你的服务器。
        3. 一个私钥文件只能放置一个私钥的代码。
    ```

8.  什么是密钥锁码？

    创建密钥对时会提示我们输入密码，而这个密码就是 `迷药锁码`

    ```text
    - 密钥锁码在使用私钥时必须输入，这样就可以增加一层保护；
    - 当然，也可以留空，实现无密码登录，如果没有设置密钥锁码，我们不小心泄漏了私钥，别人可以在任意客户端上都通过公钥认证。
    ```

## 三、密钥文件路径

1. 默认操作：

   默认会在当前用户家目录下创建 `.ssh` 目录，并在 `.ssh` 目录下生成两个密钥文件：

   | 密钥文件名 | 密钥类型       |
   | ---------- | -------------- |
   | id_rsa     | 存放私钥的文件 |
   | id_rsa.pub | 存放公钥的文件 |

2. 指令文件名的情况下：

   指定密钥文件名为 `rsa` 后，会在指定的路径（未指定路径就在当前路径）下生成两个密钥文件：

   | 密钥文件名 | 密钥类型       |
   | ---------- | -------------- |
   | rsa        | 存放私钥的文件 |
   | rsa.pub    | 存放公钥的文件 |

## 四、密钥对实例

通过一个实例让我们更加直观、全面的认识 ssh 密钥对的使用。

### 4.1 制作密钥对

在服务器上通过密钥生成器指令 `ssh-keygen` 制作一个密钥对。

1. 首先用密码登录到你打算使用密钥登录的账户。

2. 然后执行以下命令：

   ```text
   $ su tmproot
   $ cd ~
   $ ssh-keygen    <- 密钥生成器
   Generating public/private rsa key pair. <- 生成公钥/私钥对
   Enter file in which to save the key (/home/tmproot/.ssh/id_rsa): rsa   <- 输入保存密钥的文件名(包含路径)
   Created directory '/home/tmproot/.ssh'. <- 创建了一个目录
   Enter passphrase (empty for no passphrase): <-输入密钥锁码（空为不设置密码）
   Enter same passphrase again:    <- 再次输入密钥锁码
   Your identification has been saved in rsa.  <- 私钥文件
   Your public key has been saved in rsa.pub.  <- 公钥文件
   The key fingerprint is: <- 关键指纹
   SHA256:WxJFl5ZZLHEeBXzwNkGVcBvS5sQ1LLjY6iLhoSkjang tmproot@主机名
   The key's randomart image is:   <- 密钥随机图形
   +---[RSA 2048]----+
   |         .o +@%XB|
   |         . o*+=X*|
   |        . o...*+o|
   |         o o   o.|
   |        S o      |
   |     o   =       |
   |.   + o o        |
   |o+Eo o . .       |
   |=.o   . .        |
   +----[SHA256]-----+
   ```

3. 因为本次指定了密钥文件名，所以会直接在当前目录下生成两个密钥文件：

   | 密钥文件名 | 密钥类型 |
   | ---------- | -------- |
   | rsa        | 私钥     |
   | rsa.pub    | 公钥     |

4. 对于公钥密令的一些简单说明：

   | 序号 | 公钥密令说明                                                             |
   | ---- | ------------------------------------------------------------------------ |
   | 01   | 关键指纹中的 `tmproot@主机名` 字符串是可以任意修改，甚至留空都可以；     |
   | 02   | 公钥允许放置在任意服务器的任何可登陆用户中，客户端通过私钥都能认证成功！ |

### 4.2 在服务器上安装授权密钥

ssh 默认的授权密钥文件路径为： `~/.ssh/autherized_keys` ，可以通过修改 `AuthorizedKeysFile` 这个选项的参数来重新指定。

1. 推荐使用 cat 追加的方式安装授权密钥，因为 1 个公钥文件允许放置多个公钥代码：

   ```sh
   $ mkdir ~/.ssh
   $ cat rsa.pub >> ~/.ssh/authorized_keys
   ```

   > 提示： `cat file1 > file2` 是替换，而 `cat file1 >> file2` 是追加！

2. 追加公钥密令注意事项

   | 序号 | 注意事项                                                                     |
   | ---- | ---------------------------------------------------------------------------- |
   | 01   | cat 是直接在结尾处添加字符串的，但是每个公钥之间至少需要一个空格或换行隔开； |
   | 02   | 因此在使用 cat 追加指令前，推荐先为 authorized_keys 文件另起一行。           |

3. 修改密钥文件的权限

   ```sh
   $ chown tmproot -R ~/.ssh
   $ chmod 600 authorized_keys
   $ chmod 700 ~/.ssh
   ```

   > 提示：保证仅有当前登陆用户可以读写，这样才能保证安全！

### 4.3 设置 SSH，打开密钥登录功能

最新版的 SSH 默认开启密钥登陆功能，如果是旧版本请编辑 /etc/ssh/sshd_config 文件，进行如下设置：

```text
RSAAuthentication yes
PubkeyAuthentication yes
```

1. 以 root 用户登陆

   如果想要让 root 用户直接认证登陆，请添加如下设置：

   ```text
   PermitRootLogin yes
   ```

2. 禁用密码登录

   当你完成全部设置，并以密钥方式登录成功后，再禁用密码登录：

   ```text
   PasswordAuthentication no
   ```

3. 重新载入 ssh 服务进程的配置

   ```sh
   service ssh reload
   ```

## 附录一：支持密钥登录的 SSH 服务器端配置文件参考：

```text
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem	sftp	/usr/lib/openssh/sftp-server
UseDNS no
AddressFamily inet
SyslogFacility AUTHPRIV
PermitRootLogin yes
PasswordAuthentication no
```

## 附录二：加强密钥对登陆的安全性

1. 删除私钥文件

   服务器上不要保留私钥文件，自己保存好私钥文件后，应该将服务器上的私钥文件删除掉。

2. 限制 ip 登陆

   通过防火墙等措施限制 22 端口的访问 ip

关于服务器安全的更多信息，请查考[Linux 服务器安全指南](./09-Linux服务器安全指南.md)
