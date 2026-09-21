---
name: backend-delivery
description: 后端工程师的干活规范。接到后端任务信(修订接口文档/实现服务/写完成报告)、要请前端审查接口修订、或要写启动说明时使用。收到流水线协作邮件先按 tf-flow 技能行事(唤醒四步循环)。
---

# 后端交付(backend-delivery)

## 接口先行(接到后端任务的第一动作)

1. 读信内指针的 `.plan/api.md` 初版(绝对路径,跨区只读,在前端工作区);
2. 在**自己工作区**落 `.plan/api.md` 真相源——从此接口文档归你写(前端区初版由其自行退役,你不动它);
3. 修订:按实现可行性与健壮性补齐(错误码/校验规则/边界行为);**走 api-client 联通方式时,初版缺「信封契约」节=先补齐它**(以本技能 `references/swagger-envelope.md` 为准)再请审;**每处变更记入文档内 changelog 节**(前端按此节审查);
4. 修订完成 → 发「请审查」信给前端(指针=你区 `.plan/api.md` 绝对路径;要求回复二选一);**无异议确认前不开始实现**。

## 实现

- **信守契约**:以文档为准;实现中发现文档有错 → 先改文档(记 changelog)再请审,不悄悄偏离;
- 硬约束:**CORS `Access-Control-Allow-Origin: *`**(静态页跨 origin 联通的生死线);
- **信封契约(#140,走 api-client 联通方式时的硬契约)**:前端生成的 client **默认强制解包**每个响应——你的所有 JSON 响应必须包装 `{success, message, data}`(业务失败=200+`success:false` 或 4xx+`message`;分页=`data` 给 `{total,…,list}` 且 swagger 该操作声明扩展 `x-sdk-page-wrapper`;裸响应例外 `x-sdk-no-wrapper`)。**不满足=前端拿到 undefined,联调必卡且改前端无用**。细则=本技能 `references/swagger-envelope.md`;契约落在 `.plan/api.md`「信封契约」节;
- **swagger 地址**(#135):项目走 swagger→api-client 联通方式时(P2/会审定),你的服务**必须暴露 swagger 文档地址**(springdoc `/v3/api-docs`/springfox `/v2/api-docs`)并写进 `.plan/api.md` 与启动说明——前端生成 api-client 全靠它,缺了联调必卡;**且 swagger 里响应 schema 要声明包装类型、operationId/tag 规整命名**(前端生成的 TS 类型与函数名全由此决定,详见 references);
- 端口与存储方式自定(记入自主决策清单),定后写进 `.plan/api.md`。

## 完成报告(三要素+启动说明)

1. **验收对照**:逐条「条目 → 结果 → 证据指针」;
2. **自测记录**:curl 实跑贴输出——至少覆盖:提交一条**合法**数据成功、提交一条**非法**数据被拒(4xx+校验错误,证明校验是真校验)、查询列表返回;**走 api-client 时再加**:每类端点响应体可见**信封包装**、分页端点 `data.list` 形状一发、swagger 地址 curl 可达且返回 JSON;不许只写「已测试」;
3. **自主决策清单**:栈/端口/存储选择等;
4. **启动说明**:启动命令+端口+你区服务绝对路径+**swagger 地址**(项目走 api-client 时)——前端联调全靠它,缺了必被退回。
