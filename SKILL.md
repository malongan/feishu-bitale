---
name: feishu-bitale
description: "飞书多维表格控制 - 读取、新增、更新、删除飞书表格记录。触发词：读取表格、更新表格、新增任务、飞书表格"
---

# 飞书多维表格控制

通过 lark-cli 直接操作飞书多维表格。

## 环境

- lark-cli 安装：
  ```bash
  npm install -g @larksuite/cli
  ```
- 授权：
  ```bash
  lark-cli auth login
  ```

## 固定表格信息

**⚠️ 请替换为你自己的表格信息：**

```markdown
| 表格 | Base Token | Table ID |
|------|------------|----------|
| 你的表格 | YOUR_BASE_TOKEN | YOUR_TABLE_ID |
```

## 🔧 常用命令

### 1. 列出所有记录
```bash
lark-cli base +record-list \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID
```

### 2. 筛选待处理任务
```bash
lark-cli base +record-list \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --filter "状态 = 待处理"
```

### 3. 新增记录
```bash
lark-cli base +record-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --data '{
    "任务名称": "任务名称",
    "IP角色": "芒果仔",
    "主题/场景": "主题",
    "主标题": "主标题",
    "副标题": "副标题",
    "场景描述": "场景描述",
    "风格编号": "ST0000",
    "尺寸比例": "9:16",
    "状态": "待处理",
    "参考图": "https://xxx.png"
  }'
```

### 4. 更新记录
```bash
lark-cli base +record-update \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --record-id <RECORD_ID> \
  --data '{
    "状态": "已完成",
    "预览图URL": "https://xxx.png",
    "完成时间": "2026-06-22",
    "备注": "备注内容"
  }'
```

### 5. 删除记录
```bash
lark-cli base +record-delete \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --record-id <RECORD_ID>
```

### 6. 获取表结构（字段ID）
```bash
lark-cli base table-info \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID
```

## 📊 字段类型说明

| 字段类型 | lark-cli 参数格式 |
|----------|------------------|
| 文本 (text) | `"字段名": "值"` |
| 单选 (select) | `"字段名": "选项值"` |
| 数字 (number) | `"字段名": 123` |
| 日期 (date) | `"字段名": "2026-06-22"` |

## 🔄 工作流程示例

### 读取并处理待处理任务
```bash
# 1. 读取待处理任务
lark-cli base +record-list \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --filter "状态 = 待处理"

# 2. 解析任务数据（需要用 jq 或 python 解析）
```

### 完成任务后更新表格
```bash
lark-cli base +record-update \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --record-id <RECORD_ID> \
  --data '{
    "状态": "已完成",
    "预览图URL": "https://xxx.png",
    "完成时间": "2026-06-22"
  }'
```

## ⚠️ 注意事项

1. **安全提示**：Base Token 和 Table ID 是访问你表格的凭证，请勿泄露给他人
2. **Record ID 获取**：列表命令输出的第一列 `_record_id`
3. **中文编码**：确保 --data 参数中的中文正确传递
4. **JSON 格式**：复杂数据用 JSON 格式，字段名为中文
5. **权限**：操作受飞书表格权限控制

## 🆕 新建其他表格

如需创建新的飞书多维表格：
```bash
# 1. 创建 Base
lark-cli base create --name "表格名称"

# 2. 在 Base 中创建数据表
lark-cli base table-create \
  --base-token YOUR_BASE_TOKEN \
  --name "数据表名称"

# 3. 获取 Table ID 后添加字段
lark-cli base field-create \
  --base-token YOUR_BASE_TOKEN \
  --table-id YOUR_TABLE_ID \
  --field-name "字段名" \
  --field-type text
```
