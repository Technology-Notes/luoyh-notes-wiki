



NAME|DESCRIPTION|NULLABLE|DEFAULT
:-:|:-:|:-:|:-:
timeType|1:month,2:quarter,3:half-year,4:year|not null|-
timeType|1:month,2:quarter,3:half-year,4:year|not null|-
timeType|1:month,2:quarter,3:half-year,4:year|not null|-


我们制定的 API 规范，使用了微服务架构所以做了一些改进，我们更偏向使用 http code 标识，不然需要自己处理成功或失败的逻辑，在 200 内再包一层显得啰嗦；并且微服务系列都不支持，Feign，监控等都需要自己改造。

当逻辑错误时，返回 http code 400，body 体内是具体的错误原因，也可以加上自定义的状态码，解决了 http code 不能满足业务状态的需求

### 接口命名规范

**前端对接时如果发现后端提供的接口不符合规范有权拒接(包括后端对接爬虫)，如接入了不符合规范的接口，需要及时整改**

为了便于管理接口,我们将接口分为三类:

1. `public`: 不用登录即可访问的资源,举例:登录接口 `https://api.buffdj.com/public/quick_login`
2. `api`: 需要用户登录才能进行访问的接口,举例:获取用户详细信息接口: `https://api.buffdj.com/api/user/info?id=666`
3. `rpc`: 微服务之间相互调用才能用的,外部不允许访问.举例: 调用pay 微服务的 pay 接口:`https://api.buffdj.com/rpc/service-pay/pay?param=666`


### Restful 风格的 API

1. `get`:用于获取资源
2. `post`:用于提交新增一个资源
3. `put`:用于修改一个资源
4. `delete`:用于删除一个资源

**所有 API 返回值都必须是 application/json 形式，不允许字符串格式的返回值**

详细可参考 :[如何向其他人解释Restful](http://www.jianshu.com/p/e77d2f60aa5d)

我们的接口规范如下:

1. 获取书籍列表: `GET /api/book?size=10&page=1` size 和 page为分页参数
2. 获取书籍详情: `GET /api/book/info?id=666` id 为要查询的资源`主键`
3. 修改书籍信息: `PUT /api/book  `     body里边放更新依据`主键 id`,以及其他参数
4. 新增数据资源: `POST /api/book   `    body里放主键之外的参数
5. 删除书籍资源: `DELETE /api/book?id=666`  id 为要删除的资源`主键`

不使用 PathVariable 的原因是由于 Spring Could 系列的监控、追踪等软件还不兼容，会认为 GET /api/book/1, GET /api/book/2 是不同的 URL，所以我们不使用它

ps: 

1. 不要在路径里使用动词如: getXXX,queryXXX,findXXX,只需资源的名字即可,比如:获取书籍封面`GET /api/book/cover`,因为请求方法`GET` 就已经表明当前是获取资源而不是增加或者删除
2. 路径统一使用小写字母,多个单词使用`_`分割


###  Response Body 状态码规范

便于交流和协作,我们统一使用 HTTP 标准的状态码

* 200:本次请求成功,服务器已返回正确的数据在 body 里
* 4XX:出现业务逻辑异常或错误,包括:路径错误,参数错误,具体信息会在response body 中展示
* 5XX:这个是服务器出现了一些意料之外的错误

**找不到数据,查询数据结果为空的时候,需要返回404**

状态码4xx,具体的Response Body 数据结构

```
{
  "timestamp": 1512611487539,
  "status": 400,
  "error": "Bad Request",
  "exception": "org.springframework.web.bind.MethodArgumentNotValidException",
  "message": "Validation failed for object='person'. Error count: 1"
}
```

### 关于控制前端弹出信息相关规范

控制前端弹出信息统一放在 Response Header 中

```
X-dialog-message: \u6d88\u606f\u63d0\u793a
X-dialog-type: warning
```

`X-dialog-message` 这个message 是后端根据该用户的语言返回对应的提示信息的 **Unicode 编码** ,因为 header 中不能存放中文

`X-dialog-type` 目前只有warning 和 error 类型，纯展示,后续根据产品需要可以扩充更多类型

### 网关与其他微服务之间一些约定


#### Header 中存放了一些用户的基本信息

`X-user-id` 根据前端传递的 token 解析出来的 userId 放在 header中的这个字段里传递给其他微服务,其他微服务通过 HttpServletRequest.header 来获取

`X-user-nickname` 获取用户昵称 

`X-user-phone` 获取用户手机号码,如果有的话

`X-user-photo` 获取用户头像 

`X-user-account` 获取用户账号,手机号码或者邮箱 

ps: Java 可直接通过 duiker 提供的 **HttpUtils** 中提供的方法获取用户基本信息


```
asdkashgka
sdhgsg
sgkhskg
```