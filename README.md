# QMusicCookieRefresh
企鹅音乐的 Cookie 续签示例方案
<p>&nbsp;<p>

> [!IMPORTANT]
> ## 免责声明
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;·&nbsp;此模块诞生仅为学习，以及 在自定义页面播放背景音乐。请勿用于其他用途！播放会员音乐需要付费开通会员。使用本模块产生的任何问题作者均不承担任何责任，请自行承担。<p>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;·&nbsp;<b>严禁用于商业用途！</b><p>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;·&nbsp;尊重正版人人有责！<p>

<p>&nbsp;<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于企鹅音乐的网页端 Cookie 有效期非常短，普遍为 24h 。如果期间有频繁调用，会适当延长至 3day 左右。每次过期后手动换 Cookie 太麻烦，且总是造成服务中断，所以诞生了这个模块用于在到期前续签 Cookie 。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个模块原是为 Meting-Api 写的，且只对使用企鹅授权登录有效。如果你使用其它方法登录，小而美或手机号，这个可能帮不到你。如果使用其它程序实现，仍可以参考方案自行实现。<p><p>

### 下面是大概的实现步骤：<p>
- 首先，进入 [企鹅音乐官网](https://y.qq.com/) ，正常使用企鹅授权登录，通过浏览器的开发者工具获得完整 Cookie 。<b>注意，应尽量在 api 调用设备上获得 Cookie ，后续仅在此设备上使用。避免 IP 及位置信息更换。</b>完整 Cookie 应该像这样：

```bash
pgv_pvid=4***4; fqm_pvqid=3***f-d**b-4**e-9**c-0***e; ts_refer=cn.bing.com/; ts_uid=6***8; RK=3***a; ptcz=c***8; fqm_sessionid=e***4-b***c-4***d-a***d-c***1; pgv_info=ssid=s***8; ts_last=y.qq.com/; _qpsvr_localtk=0.1***7; login_type=1; wxopenid=; psrf_musickey_createtime=1***8; euin=o***A; uin=2***2; wxunionid=; music_ignore_pskey=2***j; wxrefresh_token=; qqmusic_key=Q_H_L_6***A; qm_keyst=Q_H_L_6***A; psrf_qqrefresh_token=D***C; psrf_qqunionid=A***6; tmeLoginType=2; psrf_qqaccess_token=F***6; psrf_access_token_expiresAt=1***8; psrf_qqopenid=7***3
```

- Cookie 内包含 psrf_musickey_createtime ，这就是这个 Cookie 生成时间的时间戳。可以通过比对时间戳判断 Cookie 是否临近过期并进行续签。注意续签时旧 Cookie 必须仍然有效！推荐达到 20h 时进行续签。<p>

- 然后调用 [续签](https://u6.y.qq.com/cgi-bin/musicu.fcg?format=json&inCharset=utf8&outCharset=utf8) 接口进行续签。至于为什么不使用签名接口？...<p>

```bash
- 1、我懒（x）
- 2、我不会。
- 3、企鹅音乐目前自己都没有对登录使用签名接口！
```
#### 续签调用方法：<p>
- · 从<b>还未过期</b>的 Cookie 内获得对应参数，使用 POST 方法调用 [续签](https://u6.y.qq.com/cgi-bin/musicu.fcg?format=json&inCharset=utf8&outCharset=utf8) 接口，附带旧 Cookie 作为请求 Cookie ，并携带以下 body 。

```json
{
    "code": 0,
    "req1": {
        "code": 0,
        "module": "QQConnectLogin.LoginServer",
        "method": "QQLogin",
        "param": {
            "onlyNeedAccessToken": 0,
            "forceRefreshToken": 0,
            "psrf_qqopenid": "psrf_qqopenid",
            "refresh_token": "psrf_qqrefresh_token",
            "access_token": "psrf_qqaccess_token",
            "expired_at": "psrf_access_token_expiresAt",
            "musicid": uin,
            "musickey": "qqmusic_key",
            "musickeyCreateTime": psrf_musickey_createtime,
            "unionid": "psrf_qqunionid",
            "str_musicid": "uin",
            "encryptUin": "euin",
        }
    }
}
```
- · 如果没有问题，code 为 0 ，则接口会返回续签后的 Cookie 。以下是接口的返回示例：

```json
{
    "code": 0,
    "ts": 1***6,
    "start_ts": 1***0,
    "traceid": "4***6",
    "req1": {
        "code": 0,
        "data": {
            "openid": "",
            "refresh_token": "7***D", // psrf_qqrefresh_token
            "access_token": "1***E", // psrf_qqaccess_token
            "expired_at": 0,
            "musicid": 3***4, // uin（正常不应变动）
            "musickey": "Q_H_L_6***g", // qqmusic_key & qm_keyst
            "musickeyCreateTime": 1***5, // psrf_musickey_createtime
            "first_login": 0,
            "errMsg": "",
            "sessionKey": "",
            "unionid": "2***5", // psrf_qqunionid
            "str_musicid": "3***4", // uin（正常不应变动）
            "errtip": "",
            "nick": "",
            "logo": "",
            "feedbackURL": "",
            "encryptUin": "o***n**", // euin（正常不应变动）
            "userip": "1***3",
            "lastLoginTime": 0,
            "keyExpiresIn": 2***0,
            "refresh_key": "",
            "loginType": 2, // login_type
            "prompt2bind": 0,
            "logoffStatus": 0,
            "otherAccounts": [],
            "otherPhoneNo": "",
            "token": "",
            "isPrized": -1,
            "isShowDevManage": 0,
            "errTip2": "",
            "tip3": "",
            "encryptedPhoneNo": "",
            "phoneNo": "",
            "bindAccountType": 2,
            "needRefreshKeyIn": 0
        }
    }
}
```
- 将接口内获得的数据与旧 Cookie 内的部分内容进行替换，组合成新的 Cookie 。即可继续使用。