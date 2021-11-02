# 微信开放平台-网页登录

#### 准备工作

网站应用微信登录是基于OAuth2.0协议标准构建的微信OAuth2.0授权登录系统。 在进行微信OAuth2.0授权登录接入之前，在微信开放平台注册开发者帐号，并拥有一个已审核通过的网站应用，并获得相应的AppID和AppSecret，申请微信登录且通过审核后，可开始接入流程。

#### 授权流程说明

微信OAuth2.0授权登录让微信用户使用微信身份安全登录第三方应用或网站，在微信用户授权登录已接入微信OAuth2.0的第三方应用后，第三方可以获取到用户的接口调用凭证（access_token），通过access_token可以进行微信开放平台授权关系接口调用，从而可实现获取微信用户基本开放信息和帮助用户实现基础开放功能等。 微信OAuth2.0授权登录目前支持authorization_code模式，适用于拥有server端的应用授权。该模式整体流程为：

```text
1. 第三方发起微信授权登录请求，微信用户允许授权第三方应用后，微信会拉起应用或重定向到第三方网站，并且带上授权临时票据code参数；
2. 通过code参数加上AppID和AppSecret等，通过API换取access_token；
3. 通过access_token进行接口调用，获取用户基本数据资源或帮助用户实现基本操作。
```

获取access_token时序图：

![img](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/D0wkkHSbtC6VUSHX4WsjP5ssg5mdnEmXO8NGVGF34dxS9N1WCcq6wvquR4K_Hcut)

**第一步：请求CODE**

第三方使用网站应用授权登录前请注意已获取相应网页授权作用域（scope=snsapi_login），则可以通过在PC端打开以下链接： https://open.weixin.qq.com/connect/qrconnect?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect 若提示“该链接无法访问”，请检查参数是否填写错误，如redirect_uri的域名与审核时填写的授权域名不一致或scope不为snsapi_login。





**第二步：通过code获取access_token**

**第三步：通过access_token调用接口**

获取access_token后，进行接口调用，有以下前提：

```text
1. access_token有效且未超时；
2. 微信用户已授权给第三方应用帐号相应接口作用域（scope）。
```

对于接口作用域（scope），能调用的接口有以下：

| 授权作用域（scope） | 接口                      | 接口说明                                             |
| :------------------ | :------------------------ | :--------------------------------------------------- |
| snsapi_base         | /sns/oauth2/access_token  | 通过code换取access_token、refresh_token和已授权scope |
| snsapi_base         | /sns/oauth2/refresh_token | 刷新或续期access_token使用                           |
| snsapi_base         | /sns/auth                 | 检查access_token有效性                               |
| snsapi_userinfo     | /sns/userinfo             | 获取用户个人信息                                     |

