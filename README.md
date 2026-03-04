# 飞书多维表格技能

用于在 openclaw 或本地命令行中调用飞书多维表格（Bitable）API，完成数据表与记录的常见操作。

## 🦞 OpenClaw Skill 安装指南 (OpenClaw Installation)

### 👤 给用户的安装步骤 (For Users)
你可以通过以下两种方式将此飞书多维表格 (Feishu Bitable) 技能安装到你的 OpenClaw 助手中：

**方式一：对话一键安装（推荐）**
直接复制本仓库的链接，粘贴到你与 OpenClaw 的聊天窗口中，并发送指令：
> "请帮我安装这个技能：https://github.com/liqiujiong/feishu-bitable-skill"
OpenClaw 会在后台自动读取该仓库并完成部署。

**方式二：本地命令行安装**
如果你在本地终端运行 OpenClaw，可以通过以下命令将此技能直接克隆到你的技能工作区：
\`\`\`bash
cd ~/.openclaw/workspace/skills/
git clone https://github.com/liqiujiong/feishu-bitable-skill.git
\`\`\`
安装完成后，请根据要求配置好飞书相关的环境变量。

---

### 🤖 给 OpenClaw Agent 的系统读取指令 (System Prompt for Agent)
> **[Agent Instruction]** If you are an OpenClaw AI Agent reading this README to install the skill, please execute the following workflow:
> 1. **Identify**: Recognize this repository as a valid OpenClaw Skill for interacting with Feishu Bitable.
> 2. **Clone**: Clone this repository into your local skills directory (typically `~/.openclaw/workspace/skills/`).
> 3. **Parse**: Locate and parse the `SKILL.md` file in the root directory to configure the skill's capabilities, required tool actions, and environment variables.
> 4. **Confirm**: Once deployed, notify the user that the "Feishu Bitable Skill" has been successfully installed and is ready to use. Briefly provide an example of how they can use this new capability.

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
export FEISHU_APP_SECRET_PATH=~/.openclaw/secrets/feishu_app_secret
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
2. 确保飞书应用具备多维表格读写相关权限,直接到飞书开放平台机器人管理部分，导入下面的权限即可
```
{
"scopes": {
"tenant": [
"base:app:copy",
"base:app:create",
"base:app:read",
"base:app:update",
"base:collaborator:create",
"base:collaborator:delete",
"base:collaborator:read",
"base:dashboard:copy",
"base:dashboard:read",
"base:field:create",
"base:field:delete",
"base:field:read",
"base:field:update",
"base:form:read",
"base:form:update",
"base:record:create",
"base:record:delete",
"base:record:read",
"base:record:retrieve",
"base:record:update",
"base:role:create",
"base:role:delete",
"base:role:read",
"base:role:update",
"base:table:create",
"base:table:delete",
"base:table:read",
"base:table:update",
"base:view:read",
"base:view:write_only",
"bitable:app:readonly",
"contact:contact.base:readonly",
"im:message",
"im:message.group_at_msg:readonly",
"im:message.p2p_msg:readonly",
"im:message:send_as_bot",
"im:resource",
"wiki:wiki:readonly"
],
"user": [
"bitable:app",
"bitable:app:readonly",
"contact:contact.base:readonly"
]
}
}
```
4. `--data`、`--fields`、`--sort` 支持 JSON 字符串或 `@文件路径`。
5. 飞书多维表格还有一个细节是需要把机器人添加到文档应用中,多维表格'右上角'--'添加应用'
