# Web

- [Web](#Web)
  - [OAuth2的四种模式是什么](#OAuth2的四种模式是什么)
    - [授权码模式](#授权码模式)
    - [授权码模式](#隐式授权模式)
    - [授权码模式](#密码模式)
    - [授权码模式](#客户端凭证模式)
  - [TLS/SSL握手过程](#tlsssl握手过程)
  - [TLS/SSL会话缓存](#tlsssl会话缓存)
  - [跨站脚本攻击](#跨站脚本攻击)
  - [重放攻击](#重放攻击)

### OAuth2的四种模式是什么
- 授权码模式
- 隐式授权模式
- 密码模式
- 客户端凭证模式

#### 授权码模式
- 访问一个 authorize 的界面，地址带上 `response_type`, `client_id`, `redirect_uri`, `scope`, `state`
- 用户登录后点击授权，然后跳转到 `redirect_uri` 的地址，地址携带授权码（`code`）和 `state`
- 网站得到 code 以后可以在后端请求获取 access token。请求携带: `client_id`, `client_secret`, `grant_type`, `code`, `redirect_uri`; 
- 授权服务器收到 token 请求后验证 `client_id` 和 `client_secret`, `code`, `grant_type` 是固定的 `authorization_code` 表示授权码模式
- 将 token 发送到 `redirect_uri` 的地址，携带 `access_token`, `token_type`, `expires_in`, `refresh_token`

response_type 是 code

#### 隐式授权模式
- 访问一个 authorize 的界面，地址带上 `response_type`, `client_id`, `redirect_uri`, `scope`
- 授权服务器将 access token 以锚点的形式发送给 `redirect_uri`（锚点不会发送到服务器，可以防止中间人攻击）。

response_type 是 token，只能用于一些安全要求不高的场景，会话时间短。

#### 密码模式
- 直接 post 访问授权服务器，`grant_type` 是 `password`, 携带 `client_id`, `username`, `password`

#### 客户端凭证模式
- 直接 post 访问授权服务器，`grant_type` 是 `client_credentials`, 携带 `client_id`, `client_secret`
- 授权服务器验证后直接返回 access token

这个一般是针对第三方应用的，而不是针对用户的，即可能多个用户共享同一个令牌。

#### 更新AccessToken
- 向授权服务器发送请求, `client_id`, `client_secret`, `refresh_token`, `grant_type` 是 refresh_token
- 授权服务器验证后直接返回新的 access token

#### OpenId认证
- 访问 authorize 地址，`response_type` 为 id_token 或 `code id_token`; `client_id`, `redirect_uri`, `state`
- 授权服务器将 `id_token` 返回。

OpenId Connect 也包括显式和隐式授权以上为隐式；id_token 通常包含用户信息，如果不包含，则应该用 accessToken 访问 userinfo 接口获取。

### TLS/SSL握手过程
- 客户端明文发送 协议版本、压缩算法候选列表、加密套件候选列表、随机数 等信息
- 服务端返回 协议版本、加密套件、压缩算法、另一个随机数，通知客户端信息发送结束
- 服务端将自己的证书下发给客户端
- 客户端验证通过后取出证书中的公钥，验证合法性，再生成一个随机数，并用公钥加密生成一个 key
- 客户端将 key 发送给服务端，此时客户端和服务端都使用 3 个随机数加密得到`协商秘钥`
- 客户端采用协商密钥与算法，将之前所有通信参数的 hash 值加密发送给服务器用于数据与握手验证
- 服务端计算之前所有接收信息的 hash 值，然后解密客户端发送的验证信息并验证
- 服务端也和客户端做同样的操作，将通信参数的 hash 值加密传给客服端
- 客户端验证通过后握手结束

### 跨站脚本攻击
通过恶意脚本对客户端网页进行篡改，将一些隐私数据如 cookie 发送给攻击者，或将受害者重定向到一个由攻击者控制的网站进行一些恶意操作。

解决方案是
- 转义用户输入。
- 在 cookie 中使用 HttpOnly 选项，防止对 cookie 的获取

### 重放攻击
指攻击者发送一个目标主机已接收过的包，来达到欺骗系统的目的。解决方案是使用 https 加时间戳参数，另一种方案是请求带上一个随机数，如果随机数已使用过就证明是重放攻击。
