# CLI 参数参考

本文件用于按需查询 `bin/cli.js` 的参数细节与输入格式。

## 全局规则

- `--app-token`：通常为 `bas...`，对应 Bitable 应用 token。
- `--table-id`：通常为 `tbl...`。
- `--record-id`：通常为 `rec...`。
- `--page-size`：正整数，建议范围 `1-100`。
- `@文件`：按当前工作目录解析路径，例如 `--data @examples/create-records.json`。
- JSON 参数支持两种来源：内联 JSON 字符串或 `@文件路径`。

## 命令参数

## `test`

- 必填参数：无。
- 依赖环境变量：`FEISHU_APP_ID` + (`FEISHU_APP_SECRET` 或 `FEISHU_APP_SECRET_PATH`)。

## `get-app`

- 必填：`--app-token <token>`
- 作用：验证应用可访问性，获取应用信息。

## `list-tables`

- 必填：`--app-token <token>`
- 可选：`--page-size <number>`、`--page-token <token>`

## `create-table`

- 必填：`--app-token <token>`、`--name <name>`
- 可选：`--fields <json|@file>`
- `--fields` 结构：字段定义数组。

示例：

```json
[
  {
    "field_name": "任务名称",
    "type": "text"
  },
  {
    "field_name": "状态",
    "type": "single_select",
    "property": {
      "options": [{ "name": "待办" }, { "name": "进行中" }, { "name": "已完成" }]
    }
  }
]
```

## `list-records`

- 必填：`--app-token <token>`、`--table-id <id>`
- 可选：`--page-size <number>`、`--page-token <token>`、`--filter <expr>`、`--sort <json|@file>`

`--filter` 两种输入：

- 公式字符串（推荐）：`CurrentValue.[状态]="进行中"`
- 兼容 JSON 条件对象（CLI 会转换为公式）

JSON 条件对象示例：

```json
{
  "conjunction": "and",
  "conditions": [
    {
      "field_name": "状态",
      "operator": "is",
      "value": ["进行中"]
    },
    {
      "field_name": "优先级",
      "operator": "is_not",
      "value": ["低"]
    }
  ]
}
```

支持的 `operator`（JSON 条件对象模式）：

- `is` / `equals`
- `is_not` / `not_equals`
- `gt` / `greater`
- `gte` / `greater_equal`
- `lt` / `less`
- `lte` / `less_equal`
- `is_empty` / `empty`
- `is_not_empty` / `not_empty`

`--sort` 示例：

```json
["-创建时间", "任务名称"]
```

## `create-record`

- 必填：`--app-token <token>`、`--table-id <id>`、`--data <json|@file>`
- `--data` 结构：对象（键为字段名，值为字段值）。

示例：

```json
{
  "任务名称": "完成API开发",
  "状态": "进行中",
  "优先级": "高"
}
```

## `batch-create`

- 必填：`--app-token <token>`、`--table-id <id>`、`--data <json|@file>`
- `--data` 必须是数组，每个元素为单条记录对象。

## `update-record`

- 必填：`--app-token <token>`、`--table-id <id>`、`--record-id <id>`、`--data <json|@file>`
- `--data` 支持部分字段更新。

## `delete-record`

- 必填：`--app-token <token>`、`--table-id <id>`、`--record-id <id>`

## `list-fields`

- 必填：`--app-token <token>`、`--table-id <id>`
- 可选：`--page-size <number>`、`--page-token <token>`

## `list-views`

- 必填：`--app-token <token>`、`--table-id <id>`
- 可选：`--page-size <number>`、`--page-token <token>`

## 常见错误

- `缺少必填参数: xxx`：补齐对应命令参数。
- `无法读取或解析文件 ...`：检查 `@文件路径` 存在且 JSON 合法。
- `...失败: 网络错误`：检查网络与飞书开放平台可达性。
- 权限错误：检查应用权限和目标多维表格是否在授权范围内。
