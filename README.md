# 后端工程师(backend-engineer)

TF Agent Desktop 多会话协作流水线的 **C 席位/后端执行者**:接口先行(修订→请审→信守契约实现)、curl 自测、启动说明。

## 市场条目(catalog)

| 字段 | 值 |
|---|---|
| id | `backend-engineer` |
| type | `preset` |
| name | 后端工程师 |
| location | `https://github.com/yanyim/backend-engineer.git` |
| path | `backend-engineer` |
| version | 1.0.0 |

## 仓库结构

```
backend-engineer/          # 仓库根
└── backend-engineer/      # 上架物(preset 本体)
    ├── agent.cordis.yml   # dsh 框架消费的 agent 定义
    ├── preset.yml         # dsh preset 元数据(name/description/order)
    ├── desktop.yml        # 旁车文件(TF Agent Desktop 产品层:开场白/建议项)
    └── skills/
        └── backend-delivery/   # 私有技能:交付流程+swagger 信封契约硬约束
```

> **为何是 `<id>/` 子目录而非仓库根**:市场安装纪律要求 preset 仓库内子目录名 === 清单 id
> (`assertShape` 的 basename 对齐校验)。与 `dyson-framework-assistant` 上架形态一致。

## 安装 / 卸载

TF Agent Desktop → 市场 → 找到「后端工程师」→ 安装(内部 `git clone --depth 1` 本仓库,
物化 `backend-engineer/` 子目录到 `$DSH_HOME/.agent-presets/backend-engineer/`);卸载即删该目录。

## 维护

本仓库是**唯一权威源**(自 tf-ai-destop `presets/` 迁出,#141)。修改 persona/技能后:
commit → push → 在应用市场卸载重装即可生效。

## 出处

- 迁自 tf-ai-destop `presets/backend-engineer/`(M9 T2;#140 补信封契约硬约束;#141 迁出上架)
- 角色定义:tf-ai-destop `docs/PLAN3.md` §三角色 persona
