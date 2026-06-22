# feishu-bitale

飞书多维表格（Bitable）控制技能 - 让 AI Agent 能够读取、新增、更新、删除飞书表格记录。

## 功能

- 📋 读取表格记录（支持筛选）
- ➕ 新增任务记录
- ✏️ 更新记录状态
- 🗑️ 删除记录
- 🔍 获取表结构信息

## 适用场景

- 设计任务管理系统（用户填表 → AI 自动处理 → 回填结果）
- 项目进度管理
- 内容创作协作
- 任何需要在线表格 + AI 自动化的工作流

---

## 安装教程

### 前置要求

- Node.js 18+（用于运行 lark-cli）
- npm 或 yarn
- 飞书账号

### 步骤一：安装 lark-cli

```bash
npm install -g @larksuite/cli
```

或使用 npx（无需安装）：

```bash
npx @larksuite/cli <命令>
```

### 步骤二：授权登录

```bash
# 启动 Device Flow 授权
lark-cli auth login

# 按提示操作：
# 1. 选择 "User authorization (Device Flow)"
# 2. 复制显示的 URL 到浏览器打开
# 3. 用飞书账号授权
# 4. 授权成功后返回终端继续
```

授权后，配置会保存在 `~/.lark-cli/` 目录。

### 步骤三：安装 SKILL.md

将 `skills/feishu-bitale/SKILL.md` 复制到你的 Agent skills 目录：

```
your-agent/skills/feishu-bitale/SKILL.md
```

### 步骤四：配置表格信息

**⚠️ 请替换为你自己的表格信息：**

在 SKILL.md 中修改你的表格信息：

```markdown
## 固定表格信息

| 表格 | Base Token | Table ID |
|------|------------|----------|
| 你的表格 | YOUR_BASE_TOKEN | YOUR_TABLE_ID |
```

---

## 使用方法

### 常用命令

#### 1. 列出所有记录
```bash
lark-cli base +record-list \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID
```

#### 2. 筛选待处理任务
```bash
lark-cli base +record-list \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --filter "状态 = 待处理"
```

#### 3. 新增记录
```bash
lark-cli base +record-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --data '{
    "任务名称": "新任务",
    "IP角色": "芒果仔",
    "状态": "待处理"
  }'
```

#### 4. 更新记录
```bash
lark-cli base +record-update \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --record-id RECORD_ID \
  --data '{
    "状态": "已完成",
    "预览图URL": "https://xxx.png"
  }'
```

#### 5. 删除记录
```bash
lark-cli base +record-delete \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --record-id RECORD_ID
```

---

## 获取表格信息

### 获取 Base Token 和 Table ID

1. 打开飞书多维表格
2. 点击右上角「分享」按钮
3. 复制链接，格式如下：

```
https://xxx.feishu.cn/base/BASE_TOKEN?table=TABLE_ID
```

**⚠️ 请替换为你自己的 Base Token 和 Table ID**

### 获取 Record ID

使用列表命令，输出的第一列 `_record_id` 即为记录 ID：

```
lark-cli base +record-list --base-token YOUR_BASE_TOKEN --table-id YOUR_TABLE_ID
```

输出格式：
```
| _record_id | 字段1 | 字段2 | ... |
|------------|-------|-------| ... |
| abc123     | 值1   | 值2   | ... |
```

---

## 创建飞书多维表格（示例）

如果还没有表格，可以创建一个「设计任务管理」表：

### 1. 创建 Base
```bash
lark-cli base create --name "设计任务管理"
```
记录输出的 Base Token。

### 2. 创建数据表
```bash
lark-cli base table-create \
  --base-token YOUR_BASE_TOKEN \
  --name "设计任务"
```
记录输出的 Table ID。

### 3. 添加字段
```bash
# 任务名称（文本）
lark-cli base field-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --field-name "任务名称" \
  --field-type text

# 状态（单选）
lark-cli base field-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --field-name "状态" \
  --field-type single_select

# IP角色（单选）
lark-cli base field-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --field-name "IP角色" \
  --field-type single_select
```

---

## 字段类型对照

| 飞书字段类型 | lark-cli 参数 |
|-------------|--------------|
| 文本 | `--field-type text` |
| 单选 | `--field-type single_select` |
| 多选 | `--field-type multi_select` |
| 数字 | `--field-type number` |
| 日期 | `--field-type date` |
| 复选框 | `--field-type checkbox` |
| 附件 | `--field-type attachment` |

---

## Agent 协作示例

### 工作流

```
用户填写飞书表格（状态=待处理）
    ↓
Agent 读取待处理任务
    ↓
Agent 生成图片
    ↓
Agent 上传图床
    ↓
Agent 更新表格（状态=已完成，预览图URL=xxx）
```

### Agent Prompt 示例

```
你是设计师助手。请每隔5分钟检查一次飞书表格，
读取状态为"待处理"的任务，完成后将结果更新回表格。

表格信息：
- Base Token: YOUR_BASE_TOKEN
- Table ID: YOUR_TABLE_ID

处理流程：
1. 读取待处理任务
2. 生成图片
3. 上传图床
4. 更新状态为"已完成"
5. 填入预览图URL
```

---

## ⚠️ 安全提示

- **Base Token 和 Table ID** 是访问你飞书表格的凭证
- **请勿将你的真实 Token 分享给他人**
- 公开仓库中只使用占位符（如 `YOUR_BASE_TOKEN`）

---

## 常见问题

### Q: 授权过期怎么办？

```bash
lark-cli auth login
```

### Q: 如何查看所有命令？

```bash
lark-cli --help
lark-cli base --help
```

### Q: 支持哪些操作？

```bash
lark-cli base --help
```

输出：
```
Commands:
  +record-list      List records
  +record-create    Create record
  +record-update    Update record
  +record-delete    Delete record
  table-info        Get table info
  table-create      Create table
  field-create      Create field
  field-delete      Delete field
  field-update      Update field
```

---

## License

MIT License - 可自由使用于商业和非商业项目。

---

## 更新日志

### v1.0.0 (2026-06-22)
- 初始版本
- 支持基础的 CRUD 操作
- 提供完整的安装教程
- ⚠️ 修复：移除真实凭证，使用占位符
