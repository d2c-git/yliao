 
 ## P2P平台授权接口规范
 
 #### 对接准备：管理员分配 appId appSecret
 #### 接口地址：https://yliao.com/api/oauth
 #### 接口方式：POST（application/x-www-form-urlencoded）
 #### 跳转地址：http://yliao.com/oAuth?token=接口返回的token值
 -----------------------------------------------
 #### 测试地址：http://183.129.242.178:8009/api/oauth
 #### 测试跳转地址：http://183.129.242.178:8010/oAuth?token=接口返回的token值
-------------------------------------------------
### 流程

贵司平台请求有料网接口，有料网返回token值，贵司平台取到token值，将token值拼接我们提供的跳转地址，然后跳转到该地址即可授权成功

### 注意
请贵司平台自己做好用户信息的正确性验证，有料网无法对资料的真实性进行核对，默认接收贵司平台传递的任何参数，并根据这些参数生成会员相关数据

------------------------------------------------
| 参数 | 类型 |描述 |
| ---- | ---- | ---- |
| appId | String | 平台ID（平台ID） |
| mobile | String |  授权账号（手机号码） |
| name | String |  真实姓名（务必真实）  |
| identity | String |  身份证号（务必真实）  |
| amount | BigDecimal |  授权金额（格式:1.23 / 单位:元） |
| sign | String |  请求签名（签名方式:MD5） |

 #### 签名方式：以上字段除去sign，按照参数名的字典顺序排列，以如下形式拼接成字符串，然后加上appSecret字符串，将整个字符串MD5得到sign。
  ```
 [appId=1111166059554922498]
 [appSecret=1234567890]
 
 1.字典排列
 amount=30000&appId=1111166059554922498&identity=330823********2115&mobile=137****9882&name=王**
 
 2.加上appSecret
 amount=30000&appId=1111166059554922498&identity=330823********2115&mobile=137****9882&name=王**1234567890
 
 3.最后MD5
 sign=9aa9c188d2e10c73a3638dc1df495002
 ```
 
 #### JAVA签名示例代码：
 ```
    public String getSignContent(Map<String, String> params) {
        if (params == null) {
            return null;
        } else {
            params.remove("sign");
            StringBuffer content = new StringBuffer();
            List<String> keys = new ArrayList(params.keySet());
            Collections.sort(keys);
            for (int i = 0; i < keys.size(); ++i) {
                String key = String.valueOf(keys.get(i));
                String value = String.valueOf(params.get(key));
                content.append((i == 0 ? "" : "&") + key + "=" + value);
            }
            return content.toString();
        }
    }
    
    String sign = DigestUtil.md5Hex(this.getSignContent(PARAMS_MAP) + APP_SECRET);
 ```
 #### 返回成功示例：
 ```
 {
   "code": 1,
   "msg": "操作成功",
   "data": "D2C-$2a$10$iWwxYtpyqtpp8KlFMh9tnOSYfwnvvA9cWMl6J0LO5ULHHygwJRWbK"
 }
 ```
 #### 返回失败示例：
 ```
 {
   "code": -1,
   "msg": "appId不正确，请仔细检查" / "签名不正确，请仔细检查"
 }
 ```
