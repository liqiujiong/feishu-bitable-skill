# 飞书多维表格技能

用于在 Clawdbot 或本地命令行中调用飞书多维表格（Bitable）API，完成数据表与记录的常见操作。

## 快速开始

```bash
npm install
node bin/cli.js test
```

环境变量（任选一种密钥方式）：

```bash
export FEISHU_APP_ID=cli_xxxxxx
export FEISHU_APP_SECRET=your_app_secret
# 或
export FEISHU_APP_SECRET_PATH=~/.clawdbot/secrets/feishu_app_secret
```

## 常用命令

```bash
# 查看命令列表
node bin/cli.js --help

# 查看应用信息
node bin/cli.js get-app --app-token basxxxxxx

# 列出数据表
node bin/cli.js list-tables --app-token basxxxxxx

# 创建数据表
node bin/cli.js create-table \
  --app-token basxxxxxx \
  --name "任务管理" \
  --fields @examples/create-table.json

# 列出记录
node bin/cli.js list-records \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --page-size 50

# 条件查询（推荐公式字符串）
node bin/cli.js list-records \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --filter 'CurrentValue.[状态]="进行中"'

# 创建记录
node bin/cli.js create-record \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --data '{"任务名称":"测试任务","状态":"待办"}'

# 批量创建
node bin/cli.js batch-create \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --data @examples/create-records.json

# 更新记录
node bin/cli.js update-record \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --record-id recxxxxxx \
  --data '{"状态":"已完成"}'

# 删除记录
node bin/cli.js delete-record \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --record-id recxxxxxx

# 查看字段和视图
node bin/cli.js list-fields --app-token basxxxxxx --table-id tblxxxxxx
node bin/cli.js list-views --app-token basxxxxxx --table-id tblxxxxxx
```

## 命令清单

- `test`
- `get-app`
- `list-tables`
- `create-table`
- `list-records`
- `create-record`
- `batch-create`
- `update-record`
- `delete-record`
- `list-fields`
- `list-views`

## 目录结构

```text
feishu-bitable/
├── bin/
│   └── cli.js
├── src/
│   ├── api.js
│   └── utils.js
├── examples/
│   ├── create-table.json
│   └── create-records.json
├── references/
│   └── cli-params.md
├── docs/release/
│   ├── CHECKLIST.md
│   └── description.md
├── SKILL.md
└── README.md
```

## 文档分工

- `SKILL.md`：给 Agent 的执行手册（触发与流程）。
- `references/cli-params.md`：逐命令参数细节与输入格式。
- `docs/release/*`：发布资料，不参与运行逻辑。

## 注意事项

1. 写操作前先执行 `node bin/cli.js test`。
2. 确保飞书应用具备多维表格读写相关权限。
3. `--data`、`--fields`、`--sort` 支持 JSON 字符串或 `@文件路径`。
