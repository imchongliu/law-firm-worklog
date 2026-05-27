# law-firm-worklog

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

律所工时月报生成 Skill — 把滴答清单已完成任务转成律所工时系统可导入的 Excel/CSV。

## 前置条件

1. **Claude Code**（任意版本）
2. **滴答清单 MCP** — 配置好 `dida365` MCP server，确保 `mcp__dida364__*` 工具在 Claude Code 中可用。参考：[(https://help.dida365.com/articles/7438132116019216384](https://help.dida365.com/articles/7438132116019216384)
3. **Python + openpyxl**（仅 Excel 输出需要）：`pip install openpyxl`

## 安装

```bash
# 将 skill/ 目录下的内容复制到 Claude Code skills 目录
cp -r skill/ ~/.claude/skills/law-firm-worklog/
```

或通过 Claude Code Skill 管理界面导入。

## 使用

### 首次配置（必须走一遍）

```
生成 5 月工时
```

Skill 会检测到没有配置，引导你完成三组设置：

1. **工时模板** — 提供你律所工时系统的表头（给 Excel/CSV 文件路径、粘贴列名、或逐列列出）
2. **工作项目白名单** — 从滴答清单项目列表中勾选哪些是"工作"项目
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
    │   └── generate_worklog.py   # Excel 生成脚本
    ├── references/
    │   └── setup.md              # 首次配置脚本
    └── evals/
        └── evals.json
```

首次运行后自动生成 `user-config/`：

```
user-config/
├── profile.md      # 工作项目白名单 + 列分类表 + 固定值
├── template.xlsx   # 工时表头模板（或 .csv）
└── projects.csv    # 项目案号注册表
```

## 常见问题

| 症状 | 处理 |
|------|------|
| 滴答任务拉不到 | 用 `list_projects` 复核工作项目 ID 白名单 |
| 报鉴权错误 | 浏览器重新登录滴答，MCP 自动刷新 token |
| 任务无法自动归类 | 确认 projects.csv 里有对应项目和关键词 |
| Excel 乱码 | CSV 输出确保用了 UTF-8 BOM；推荐直接用 .xlsx |
| 日期差一天 | 全天任务有时区转换问题，Skill 已处理（B3 规则） |

## 许可证

MIT
