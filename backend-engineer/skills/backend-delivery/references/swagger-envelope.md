# swagger 信封契约(走 api-client 联通方式时的硬契约)

> 项目走「swagger 生成 api-client」联通方式(P2/会审定)时,前端的生成 client **默认强制按信封解包**(customWrappedFetch)——你的响应不满足信封,前端拿到的就是 undefined/错形数据,**联调必卡且改前端无用**。本契约=前后端共同真相源,落在 `.plan/api.md`「信封契约」节,实现与修订都以它为准。

## 响应包装(默认所有 JSON 端点)

每个 JSON 响应体必须是:

```json
{ "success": true, "message": "ok", "data": <真正的载荷> }
```

- 前端工具层只读三个字段:**`success` / `message` / `data`**。`status` 可选(缺省回落 HTTP 码);**多余字段无害**(实战先例 JsonResult=`{code, data, errorCode, logRequestId, logTraceId, message, success}` 完全兼容)——想加 code/traceId 随意,三个必读字段不能少;
- **业务失败**二选一:HTTP 200 + `"success": false` + `message` 说明(前端把它当错误抛);或 HTTP 4xx/5xx + body 含 `message`(前端抛 ApiError,HTTP 层拦截);
- **校验失败**(如非法登记)=HTTP 4xx + `message` 带校验错误描述——这是「校验错误前端可见」的承载位,别只回裸状态码。

## 分页端点(按需,swagger 操作级扩展声明)

列表接口要分页时,该操作的 swagger **声明扩展字段 `x-sdk-page-wrapper: "x-sdk-page-wrapper"`**(注意:扩展的值=键名字符串本身)。约束:

- query 收 `pageNum` / `pageSize`(可选 `orderBy`)——**前端生成器会自动给该操作补这三个参数声明**,你按收到的参数实现即可;
- `data` = `{ "total": <总数>, "pageNum": n, "pageSize": n, "list": [<条目>…] }`(前端读 `data.total` 与 `data.list`,其余分页元字段可选但不缺更好)。

不分页的列表=普通端点,`data` 直接给数组。

## 不包装的例外(仅文件流等裸响应)

文件下载等端点:扩展 `x-sdk-no-wrapper: "x-sdk-no-wrapper"` → 该函数前端不解包。常规 JSON 端点不需要也不该用。

## swagger 文档侧四要求(生成的类型与函数名全由此决定)

1. **可达的 api-docs JSON 地址**:springdoc=`/v3/api-docs`、springfox=`/v2/api-docs`——写进 `.plan/api.md` 与启动说明;**生成时前端会 fetch 它,你的服务必须在线**;
2. **响应 schema 要声明包装类型**:每个响应 schema=包装形态(如 `JsonResultXxxVO`,内含 `data` 字段装业务字段)——前端生成的 TS 类型由此而来,只声明裸业务类型=前端类型全错;
3. **operationId/tag 规整命名**:生成函数名=tag(去 `-controller` 尾)驼峰拼接 operationId(自动剥 `UsingGET` 类后缀)。controller 注解随手命名=前端拿到一堆不可读函数名,会被退回;
4. **扩展字段写进 swagger**(分页/裸响应两处,见上)。

## 自测要点(完成报告的 curl 记录要能证明)

- 每类端点 curl 一发,**响应体里可见 `{success, message, data}` 包装**;
- 非法数据一发,4xx+`message` 可见;
- 分页端点带 `pageNum/pageSize` curl 一发,`data.list` 形状正确;
- swagger 地址 curl 可达且返回 JSON。
