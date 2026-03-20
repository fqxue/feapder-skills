---
name: feapder
description: Build, modify, and debug feapder 1.9.2 spiders and projects with the framework's native patterns. Use when working on feapder codebases or when requests mention feapder, AirSpider, Spider, TaskSpider, BatchSpider, Request/Response parsing, Item or UpdateItem, pipeline integration, render settings, project scaffolding, or feapder CLI workflows. Also trigger on common Chinese requests such as 编写feapder爬虫, 改feapder项目, 新建AirSpider, 分布式Spider, TaskSpider任务表, BatchSpider批次爬虫, feapder入库, feapder渲染, feapder调试, feapder shell, 生成item, 修改setting.py, or 分析feapder示例代码.
---

# Feapder

## Overview

Implement feapder 1.9.2 code with the framework's native patterns, grounded in the vendored docs, templates, tests, and source anchors. Choose the right spider base class first, then follow the matching startup, request, parse, persistence, and debugging patterns.

## Workflow Decision Tree

1. Classify the request before writing code:
   - Use `AirSpider` for small, local, non-distributed jobs.
   - Use `Spider` for Redis-backed distributed crawling, resumable queues, and automatic item persistence.
   - Use `TaskSpider` when seeds come from MySQL or Redis task tables and the framework should manage seed loading.
   - Use `BatchSpider` for periodic batches with batch records and explicit task-state transitions.
2. Read only the reference file that matches the task:
   - Spider selection, CLI scaffolding, and project layout: [references/spider-types-and-scaffolding.md](references/spider-types-and-scaffolding.md)
   - Code patterns for request flow, items, task updates, and render usage: [references/code-patterns.md](references/code-patterns.md)
   - Settings, pipelines, shell debugging, and upstream source anchors: [references/settings-debugging-and-sources.md](references/settings-debugging-and-sources.md)
   - Open vendored docs, tests, or source files only for the specific behavior being implemented or verified. Do not bulk-read `references/vendor/`.
3. Reuse feapder conventions instead of inventing abstractions:
   - Import `feapder` directly and subclass the correct base spider.
   - Emit work with `yield feapder.Request(...)`.
   - Keep parsing in `parse` or explicit callback methods passed through `callback=...`.
   - Prefer `__custom_setting__` for spider-local overrides and `setting.py` for project-wide config.
   - Prefer `from feapder.utils.log import log` and `log.info(...)` for runtime logs instead of `print(...)` unless the user explicitly asks to mirror upstream demo output.
   - Match the surrounding project's language for logs and comments. If creating a new standalone demo for a Chinese request, concise Chinese logs and comments are acceptable.
4. If the user asks to create new feapder code, prefer the framework's generated structure and naming conventions over bespoke layouts.
5. If the user asks for a simple crawler demo and does not mention persistence, `Item`, `UpdateItem`, pipeline wiring, Redis, MySQL, task tables, batch scheduling, distributed workers, or multi-file output, default to the single-file `AirSpider` style shown in [references/vendor/feapder-1.9.2/tests/air-spider/test_air_spider.py](references/vendor/feapder-1.9.2/tests/air-spider/test_air_spider.py): one file, one spider class, minimal startup block, and only the code required to demonstrate the requested behavior.

## Working Rules

- Keep output aligned with feapder 1.9.2, not newer community variants.
- Prefer minimal, working spider code over framework-agnostic architecture.
- When the request is underspecified, do not scaffold a full feapder project by default. Write the smallest single-file `AirSpider` demo only for pure crawling or parsing requests. If the request mentions `Item`, `UpdateItem`, 入库, `ITEM_PIPELINES`, Redis, MySQL, task tables, or batch/task state, choose the corresponding spider and project shape instead.
- When modifying an existing feapder project, preserve its current spider type, `main.py` dispatch style, item modules, and `setting.py` shape unless the user asks for a migration.
- When persistence is needed, decide explicitly between:
  - Manual DB calls with `MysqlDB` or `RedisDB`
  - Automatic batch persistence via `Item` or `UpdateItem`
  - Custom `ITEM_PIPELINES`
- When render is required, set `render=True` on the request and then align downloader configuration with the project's `setting.py` or `__custom_setting__`.
- When handling `BatchSpider` or `TaskSpider`, treat master and worker entrypoints separately. Do not collapse them into a single ambiguous startup path.
- When writing new spider code, prefer `log.info(...)`, `log.error(...)`, and similar logger methods for runtime output. Avoid `print(...)` unless the user explicitly asks for raw console prints or exact upstream reproduction.
- Keep log and comment language consistent with the existing project. For net-new standalone demos requested in Chinese, concise Chinese logs such as `log.info(f"第{request.page}页原始结果 | {response.json}")` are acceptable.
- Only keep comments that clarify non-obvious logic, and avoid introducing mixed-language diffs unless the user asks for a language change.

## Implementation Checklist

Before finishing a feapder task, verify the code against this checklist:

- The spider inherits the correct feapder base class.
- If the task is a pure crawling or parsing demo and does not require persistence or task orchestration, the new spider defaults to the single-file `AirSpider` pattern anchored by `references/vendor/feapder-1.9.2/tests/air-spider/test_air_spider.py`.
- Startup code passes the required constructor args such as `redis_key`, `task_table`, `task_keys`, or batch metadata when needed.
- Request callbacks, carried parameters, and middleware hooks use feapder's expected method signatures.
- Distributed spiders update task state where required instead of relying on implicit completion.
- Item, pipeline, and setting references match the project's existing module layout.
- Debugging guidance uses feapder-native tools such as `feapder shell`, `to_DebugSpider`, or `to_DebugBatchSpider` when appropriate.
- New example code keeps log and comment language aligned with the surrounding project, or with the user's requested language for standalone demos.

## Source Anchors

Use the vendored feapder 1.9.2 snapshot under `references/vendor/` as the source of truth. This keeps the skill portable when copied to another machine. Prefer the smallest relevant doc or test first, then open source files only when you need to confirm method signatures or edge-case behavior:

- Core overview: [references/vendor/feapder-1.9.2/README.md](references/vendor/feapder-1.9.2/README.md)
- Usage docs: [references/vendor/feapder-1.9.2/docs/usage/AirSpider.md](references/vendor/feapder-1.9.2/docs/usage/AirSpider.md), [references/vendor/feapder-1.9.2/docs/usage/Spider.md](references/vendor/feapder-1.9.2/docs/usage/Spider.md), [references/vendor/feapder-1.9.2/docs/usage/TaskSpider.md](references/vendor/feapder-1.9.2/docs/usage/TaskSpider.md), [references/vendor/feapder-1.9.2/docs/usage/BatchSpider.md](references/vendor/feapder-1.9.2/docs/usage/BatchSpider.md)
- CLI and project generation: [references/vendor/feapder-1.9.2/docs/command/cmdline.md](references/vendor/feapder-1.9.2/docs/command/cmdline.md)
- Minimal runnable examples: [references/vendor/feapder-1.9.2/tests/air-spider/test_air_spider.py](references/vendor/feapder-1.9.2/tests/air-spider/test_air_spider.py), [references/vendor/feapder-1.9.2/tests/spider/spiders/test_spider.py](references/vendor/feapder-1.9.2/tests/spider/spiders/test_spider.py), [references/vendor/feapder-1.9.2/tests/batch-spider/spiders/test_spider.py](references/vendor/feapder-1.9.2/tests/batch-spider/spiders/test_spider.py), [references/vendor/feapder-1.9.2/tests/test-pipeline/pipeline.py](references/vendor/feapder-1.9.2/tests/test-pipeline/pipeline.py)
