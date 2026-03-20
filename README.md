# feapder

`feapder` 用来处理 feapder 1.9.2 项目的编写、修改和调试，重点覆盖 `AirSpider`、`Spider`、`TaskSpider`、`BatchSpider`、请求解析、入库流程、配置管理和框架原生调试方式。

## 适用任务

- 新建或修改 feapder 爬虫、项目脚手架、`main.py`、`setting.py`、`items/` 结构
- 在 `AirSpider`、`Spider`、`TaskSpider`、`BatchSpider` 之间做正确选型
- 编写请求流、回调链、任务状态更新、`Item` / `UpdateItem` 或 pipeline 持久化逻辑
- 处理 `render=True`、自定义下载器、配置覆盖、命令行调试和上游行为核对

## 默认工作方式

1. 先判断任务属于单文件演示、现有项目修改，还是带持久化或任务编排的完整项目。
2. 先选对蜘蛛类型，再去读对应的参考文件，而不是一上来扫完整个 vendored 文档树。
3. 优先沿用 feapder 原生模式：`yield feapder.Request(...)`、标准回调签名、`setting.py` 或 `__custom_setting__` 配置覆盖。
4. 只有在文档、测试和模板不足以确认行为时，才打开最小范围的 vendored 源码锚点。
5. 完成实现后，按蜘蛛类型和项目形态做针对性验证，不默认扩写成框架无关抽象。

## 安装到 Codex

如果你是从 GitHub 仓库下载这个 skill，下载完成后，把仓库根目录下的 `feapder` 文件夹复制到你本机的 Codex 技能目录中即可。仓库根目录的 `README.md` 仅用于仓库说明，不需要复制到技能目录。

1. 下载或克隆仓库。
2. 打开仓库根目录下的 `feapder/`。
3. 复制整个 `feapder` 文件夹。
4. 粘贴到本机 Codex 技能目录：
   - Windows 示例：`C:\Users\你的用户名\.codex\skills\`
   - macOS / Linux 示例：`~/.codex/skills/`
5. 重新打开 Codex，或开始一个新会话，让技能被重新加载。

安装完成后，技能目录应类似这样：

```text
~/.codex/skills/
└── feapder/
    ├── SKILL.md
    ├── agents/
    └── references/
```

## 目录结构

```text
feapder-skills/
├── README.md
└── feapder/
    ├── SKILL.md
    ├── agents/
    └── references/
        ├── spider-types-and-scaffolding.md
        ├── code-patterns.md
        ├── settings-debugging-and-sources.md
        └── vendor/
```

## 关键资源

| 资源 | 作用 |
|------|------|
| `SKILL.md` | 规定蜘蛛选型、输出边界、代码约定和收尾检查项 |
| `references/spider-types-and-scaffolding.md` | 先做蜘蛛类型判断，并确定项目结构和 CLI 脚手架方式 |
| `references/code-patterns.md` | 提供请求流、回调、持久化、渲染和日志风格的核心模式 |
| `references/settings-debugging-and-sources.md` | 说明配置放置位置、调试路径和上游源码锚点 |
| `references/vendor/` | feapder 1.9.2 的 vendored 文档、模板、测试和源码快照，按需最小范围查阅 |

## 使用要点

- 纯抓取或解析演示，默认优先使用单文件 `AirSpider` 风格。
- 涉及 `Item`、`UpdateItem`、Redis、MySQL、任务表或批次调度时，不要退回成简单演示脚本。
- 修改现有项目时，优先保留其当前蜘蛛类型、入口方式、模块布局和日志语言。
- 日志优先使用 `from feapder.utils.log import log`，除非用户明确要求保留 `print(...)`。

## 验证建议

- `AirSpider`：验证最小请求流和 `parse()` 逻辑是否可运行。
- `Spider`：确认 `redis_key`、持久化路径和回调签名匹配项目现状。
- `TaskSpider` / `BatchSpider`：分别检查 master 与 worker 入口、任务状态更新和调试入口是否完整。
- 配置或调试改动：优先使用 `feapder shell`、`to_DebugSpider(...)`、`to_DebugBatchSpider(...)` 做框架原生验证。
