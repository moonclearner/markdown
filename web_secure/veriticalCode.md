# Verifical Code

## classification
- png
	ocr 软件可以识别该文字
- Gif Verifical code
- 手机短信方式
	burp 可以使用暴力破解
- 手机语言验证
- 视频验证码

## principle
1. client send request
2. 服务端响应创建一个新的 session 同时生产一个随机验证码
3. 服务端将验证码和 session 返回给客户端
4. 客户端提交验证码连同 session 返回服务端
5. 服务端验证验证码同时销毁当前会话，返回客户端结果

## secure problem
客户端问题
服务端问题
验证码本身问题
验证码流程设计问题

## 客户端问题
- js 验证
	禁用本地 js 即可
- 验证码输出客户端
	将验证码发送到了客户端 (html, cookie responnse headers)，然后在客户端进行匹配
	或者用户输入错误之后，会将验证码会显示出来

## 服务端问题
- 验证码不过期，未及时销毁会话，导致验证码复用
	可以进行暴力破解
- 没有进行非空判断
	去掉 cookie 中的某些值或者请求中参数

## 暴力破解验证码
tool: pkav
字典：top100


## 防御
- 强制输入验证码，js 禁止也不行，或者需要实施 IP 策略，或者密码必须复杂。主要不要被 X-forward-for 绕过，使用转发 ip，及内网代理绕过 IP 限制
- 验证码一定只能用一次，用完就过期
- 验证码一定要复杂，不能被图像识别出来
- 大网站最好使用统一安全验证码，各处使用同一个验证码接口