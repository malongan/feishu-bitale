---
name: feishu-bitale
description: "飞书多维表格控制 - 读取、新增、更新、删除飞书表格记录。触发词：读取表格、更新表格、新增任务、飞书表格"
---

# 飞书多维表格控制

通过 lark-cli 直接操作飞书多维表格。

## 环境

- lark-cli 路径：`/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli`
- 授权状态：已通过 Device Flow 授权

## 固定表格信息

| 表格 | Base Token | Table ID |
|------|------------|----------|
| 设计任务管理 | `Gbe2bURz0aBuzWsv6vicM1NpnEg` | `tbl9RolVlj1k5fwl` |

## 🔧 常用命令

### 1. 列出所有记录
```bash
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-list \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl
```

### 2. 筛选待处理任务
```bash
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-list \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
  --filter "状态 = 待处理"
```

### 3. 新增记录
```bash
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-create \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
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
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-update \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
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
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-delete \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
  --record-id <RECORD_ID>
```

### 6. 获取表结构（字段ID）
```bash
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base table-info \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl
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
TASKS=$(/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-list \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
  --filter "状态 = 待处理" \
  --format json)

# 2. 解析任务数据（需要用 jq 或 python 解析）
echo "$TASKS" | python3 -c "import sys,json; ..."
```

### 完成任务后更新表格
```bash
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base +record-update \
  --base-token Gbe2bURz0aBuzWsv6vicM1NpnEg \
  --table-id tbl9RolVlj1k5fwl \
  --record-id <RECORD_ID> \
  --data '{
    "状态": "已完成",
    "预览图URL": "https://malongan.github.io/images/xxx.png",
    "完成时间": "2026-06-22"
  }'
```

## ⚠️ 注意事项

1. **Record ID 获取**：列表命令输出的第一条记录的 ID 字段
2. **中文编码**：确保 --data 参数中的中文正确传递
3. **JSON 格式**：复杂数据用 JSON 格式，字段名为中文
4. **权限**：操作受飞书表格权限控制

## 🆕 新建其他表格

如需创建新的飞书多维表格：
```bash
# 1. 创建 Base
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base create \
  --name "表格名称"

# 2. 在 Base 中创建数据表
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base table-create \
  --base-token <NEW_BASE_TOKEN> \
  --name "数据表名称"

# 3. 获取 Table ID 后添加字段
/opt/homebrew/lib/node_modules/@larksuite/cli/bin/lark-cli base field-create \
  --base-token <NEW_BASE_TOKEN> \
  --table-id <NEW_TABLE_ID> \
  --field-name "字段名" \
  --field-type text
```
