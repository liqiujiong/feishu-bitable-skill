---
name: feishu-bitable
description: 调用飞书多维表格（Bitable）API执行数据表、记录、字段、视图相关操作。用于在 openclaw 会话中处理“列出/创建/更新/删除记录或数据表”“批量导入记录”“按条件查询记录”“读取字段或视图结构”等任务；当用户提供 app_token、table_id、record_id 或希望自动化管理多维表格数据时触发。
metadata: {"openclaw":{"emoji":"📊","requires":{"env":["FEISHU_APP_ID","FEISHU_APP_SECRET"]},"primaryEnv":"FEISHU_APP_ID"}}
---

# 飞书多维表格（Bitable）技能

在 openclaw 中调用本技能时，按以下流程执行，优先给出可直接运行的 `bin/cli.js` 命令。

## 1. 前置检查

先确认依赖和凭证可用，再执行业务命令。

```bash
npm install
node bin/cli.js test
```

环境变量要求：

```bash
export FEISHU_APP_ID=cli_xxxxxx
export FEISHU_APP_SECRET=your_app_secret
```

如果 `test` 失败，先排查凭证、权限和网络，不要直接执行写操作。

## 2. 任务决策

按用户意图选择命令：

- 查看应用信息：`get-app`
- 管理数据表：`list-tables`、`create-table`
- 管理记录：`list-records`、`create-record`、`batch-create`、`update-record`、`delete-record`
- 查看结构信息：`list-fields`、`list-views`

## 3. 命令模板

统一使用：

```bash
node bin/cli.js <command> [options]
```

高频模板：

```bash
# 列出数据表
node bin/cli.js list-tables --app-token basxxxxxx

# 创建数据表
node bin/cli.js create-table \
  --app-token basxxxxxx \
  --name "任务管理" \
  --fields @examples/create-table.json

# 查询记录（推荐公式 filter）
node bin/cli.js list-records \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --filter 'CurrentValue.[状态]="进行中"' \
  --sort '["-创建时间"]' \
  --page-size 50

# 新增记录
node bin/cli.js create-record \
  --app-token basxxxxxx \
  --table-id tblxxxxxx \
  --data '{"任务名称":"完成API开发","状态":"进行中"}'

# 批量新增记录（--data 接受 JSON 数组或 @文件）
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
```

## 4. 参数与输出约定

- `--data`、`--fields`、`--sort` 支持 JSON 字符串或 `@文件路径`。
- `list-records --filter` 推荐飞书公式字符串；传 JSON 条件对象时会自动转换为公式。
- 命令输出为 JSON，失败时输出中文错误并返回非 0 退出码。

## 5. 执行约束

- 写操作（`create-*`、`update-record`、`delete-record`、`batch-create`）前，先确认 `app_token`、`table_id`、字段名和数据格式。
- 默认 `page-size` 建议不超过 100，避免请求过大。
- 遇到权限错误时，提示检查飞书应用权限（至少包含多维表格读写相关权限）。

## 6. 参考资料（按需读取）

- 详细使用说明：[README.md](README.md)
- 示例输入文件：`examples/create-table.json`、`examples/create-records.json`
- API实现：`src/api.js`
- CLI实现：`bin/cli.js`

## 7. 参数细则（执行时优先参考）

全局规则：

- `app-token` 格式通常为 `bas...`，对应多维表格应用 token。
- `table-id` 格式通常为 `tbl...`，`record-id` 格式通常为 `rec...`。
- 数值参数（如 `--page-size`）传正整数；建议 `1-100`。
- `@文件` 语法按当前工作目录解析路径。

命令参数说明：

- `test`
  - 无参数。
  - 依赖环境变量：`FEISHU_APP_ID` + (`FEISHU_APP_SECRET` 或 `FEISHU_APP_SECRET_PATH`)。
- `get-app`
  - 必填：`--app-token <token>`
  - 用途：验证 app_token 是否可访问，获取应用基础信息。
- `list-tables`
  - 必填：`--app-token <token>`
  - 可选：`--page-size <number>`、`--page-token <token>`
- `create-table`
  - 必填：`--app-token <token>`、`--name <name>`
  - 可选：`--fields <json|@file>`
  - `--fields` 需传数组，元素为字段定义对象。
- `list-records`
  - 必填：`--app-token <token>`、`--table-id <id>`
  - 可选：`--page-size <number>`、`--page-token <token>`、`--filter <expr>`、`--sort <json|@file>`
  - `--filter` 支持两种输入：
    - 飞书公式字符串（推荐），如 `CurrentValue.[状态]="进行中"`。
    - JSON 条件对象（兼容旧格式），会自动转换为公式。
  - `--sort` 传 JSON 数组，例如 `["-创建时间"]`。
- `create-record`
  - 必填：`--app-token <token>`、`--table-id <id>`、`--data <json|@file>`
  - `--data` 传对象（键为字段名，值为字段值）。
- `batch-create`
  - 必填：`--app-token <token>`、`--table-id <id>`、`--data <json|@file>`
  - `--data` 必须是数组，每个元素是单条记录对象。
- `update-record`
  - 必填：`--app-token <token>`、`--table-id <id>`、`--record-id <id>`、`--data <json|@file>`
  - `--data` 传部分字段即可（按需更新）。
- `delete-record`
  - 必填：`--app-token <token>`、`--table-id <id>`、`--record-id <id>`
- `list-fields`
  - 必填：`--app-token <token>`、`--table-id <id>`
  - 可选：`--page-size <number>`、`--page-token <token>`
- `list-views`
  - 必填：`--app-token <token>`、`--table-id <id>`
  - 可选：`--page-size <number>`、`--page-token <token>`

输入格式建议：

- 单条记录写入优先用内联 JSON：`--data '{"任务名称":"A","状态":"待办"}'`
- 批量导入优先用文件：`--data @examples/create-records.json`
- 条件查询优先公式字符串，避免 JSON 条件与字段类型不匹配。

常见报错与处理：

- `缺少必填参数: xxx`：补齐对应命令参数。
- `无法读取或解析文件 ...`：检查 `@文件路径` 是否存在且 JSON 合法。
- `...失败: 网络错误`：检查网络与飞书开放平台可达性。
- 权限相关失败：检查应用是否开通多维表格读写权限，以及目标 app/table 是否在授权范围内。
