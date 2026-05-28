# law-firm-worklog

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

律所工时月报生成 Skill — 把任务管理工具里的已完成任务转成律所工时系统可导入的 Excel/CSV。

**兼容所有主流 AI Agent**：Claude Code、Workbuddy、Codex、OpenCLAW 及任何支持 Skills/Agents 协议的 AI 助手。

## 支持的数据源

任选一个，配置一次后即可月月生成：

| 数据源 | MCP 适配 | 说明 |
|--------|---------|------|
| 滴答清单 / TickTick | ✅ | 中文用户首选；任务有"项目"概念 |
| Notion | ✅ | 在 database 里记任务；需指明状态/完成时间字段 |
| Microsoft To Do | ✅ | Outlook 任务现已并入 To Do |
| 飞书 / Lark | ✅ | 走飞书任务模块（非多维表格） |

新增源只需在 `skill/references/sources/` 加一个适配器 markdown，主流程不动。

## 前置条件

1. **支持 Skills/Agents 协议的 AI 助手**（Claude Code、Workbuddy、Codex、OpenCLAW 等）
2. **任选一种**上述数据源的 MCP（Model Context Protocol）配好并能调用
3. **Python + openpyxl**（仅 Excel 输出需要）：`pip install openpyxl`

## 安装

将 `skill/` 目录安装到你的 AI 助手的 skills 目录：

```bash
# Claude Code
cp -r skill/ ~/.claude/skills/law-firm-worklog/

# 其他 AI 助手（路径可能不同，请参考对应文档）
```

## 使用

### 首次配置（必须走一遍）

```
生成 5 月工时
```

Skill 会检测到没有配置，引导你完成三组设置：

1. **工时模板** — 提供你律所工时系统的表头（给 Excel/CSV 文件路径、粘贴列名、或逐列列出）
2. **数据源 + 工作单元白名单** — 选你用哪个任务工具，再从可用工作单元里勾选哪些算"工作"
3. **项目案号注册表** — 列出活跃项目及其案号，用于自动归类

配置一次性完成，保存在 `user-config/` 下。

### 日常生成

```
生成 5 月工时          # 默认导出 .xlsx
导出 2026-05-01 到 2026-05-24 的工时
导出 CSV               # 要 CSV 格式
```

### 维护

```
新签了 X 项目，案号 ABC-123，关键词 xxx     # 追加项目
删掉 X 项目                                    # 移除项目
我换律所了，改一下姓名                          # 修改个人信息
```

## 输出格式

| 格式 | 触发方式 | 说明 |
|------|---------|------|
| `.xlsx` | 默认 | 加粗灰底表头、细线边框、冻结首行、自动筛选 |
| `.csv` | 说"导出 CSV" | UTF-8 BOM 编码，Excel/WPS 双击不乱码 |

## 仓库结构

```
├── README.md
├── LICENSE
└── skill/
    ├── SKILL.md
    ├── scripts/
    │   └── generate_worklog.py        # Excel 生成脚本
    ├── references/
    │   ├── setup.md                   # 首次配置脚本
    │   └── sources/                   # 各数据源适配器
    │       ├── dida365.md
    │       ├── notion.md
    │       ├── todo.md
    │       └── feishu.md
    └── evals/
        └── evals.json
```

首次运行后自动生成 `user-config/`：

```
user-config/
├── profile.md      # 工作单元白名单 + 列分类表 + 固定值
├── template.xlsx  # 工时表头模板（或 .csv）
└── projects.csv    # 项目案号注册表
```

## 常见问题

| 症状 | 处理 |
|------|------|
| 任务拉不到 | 复核当前 source 的工作单元白名单（按对应 `references/sources/<source>.md` 走） |
| 报鉴权错误 | 在浏览器或 MCP 配置里重新授权；具体提示文案见对应 adapter |
| 任务无法自动归类 | 确认 projects.csv 里有对应项目和关键词 |
| Excel 乱码 | CSV 输出确保用了 UTF-8 BOM；推荐直接用 .xlsx |
| 日期差一天 | 全天任务时区转换，各 source 在 adapter 的 B3 节单独处理（滴答最需要小心） |

## 许可证

MIT