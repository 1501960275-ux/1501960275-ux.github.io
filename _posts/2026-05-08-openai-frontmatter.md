---
title: "OpenAi学习-frontmatter"
date: 2026-05-08 10:00:00 +0800
categories: [OpenAi]
tags: [frontmatter, openai]
---


---

## 基础结构

```YAML
---
# 这里全是 frontmatter 配置
---
```

## 核心字段详解

### 1. 基础标识字段

|字段|类型|说明|
|-|-|-|
|`name`|string|Skill 的唯一标识名，全小写，用连字符分隔|
|`version`|string|语义化版本，如 `1.0.0`|
|`description`|string|一句话描述 Skill 的功能|
|`author`|string|作者信息|
|`tags`|array|分类标签，如 `["data", "sql", "visualization"]`|


```YAML
name: hive-data-query
version: 1.2.0
description: 连接 Hive 数仓执行查询并返回结构化结果
author: your-name
tags: ["data", "sql", "hive"]
```

---

### 2. 运行环境配置（关键）

|字段|类型|说明|
|-|-|-|
|`runtime`|string|运行环境：`python`、`node`、`docker`、`shell`|
|`dependencies`|array|依赖包列表，格式因 runtime 而异|
|`env`|array|必需的环境变量名|
|`install`|string|安装脚本命令|


```YAML
runtime: python
dependencies:
  - pyhive>=0.6.5
  - pandas>=2.0.0
  - matplotlib>=3.7.0
env:
  - HIVE_HOST
  - HIVE_PORT
  - HIVE_DATABASE
install: |
  pip install -r requirements.txt
```

---

### 3. 权限与安全字段（生产必配）

|字段|类型|说明|
|-|-|-|
|`permissions`|object|声明 Skill 需要的系统权限|
|`sandbox`|string|沙箱类型：`none`、`process`、`docker`|
|`allowlist`|array|允许执行的外部命令白名单|
|`network`|boolean/object|网络访问权限|


```YAML
permissions:
  filesystem: read    # 只读文件系统
  network: true       # 允许网络访问
  env_access:         # 允许读取的环境变量
    - HIVE_*
    - AWS_*

sandbox: docker       # 强制 Docker 隔离
allowlist:
  - hive
  - beeline
  - python3
```

---

### 4. 交互与行为字段

|字段|类型|说明|
|-|-|-|
|`input_schema`|object|输入参数的 JSON Schema|
|`output_schema`|object|输出结果的 JSON Schema|
|`confirmation`|boolean/string|执行前是否需要用户确认|
|`timeout`|number|最大执行时间（秒）|
|`retries`|number|失败重试次数|


```YAML
input_schema:
  type: object
  properties:
    sql:
      type: string
      description: 要执行的 SQL 查询
    limit:
      type: integer
      default: 100
      description: 返回结果行数限制
  required: ["sql"]

output_schema:
  type: object
  properties:
    rows:
      type: array
    columns:
      type: array
    execution_time_ms:
      type: number

confirmation: "该操作将直接查询生产数仓，确认执行？"
timeout: 30
retries: 1
```

---

### 5. 高级字段

|字段|类型|说明|
|-|-|-|
|`hooks`|object|生命周期钩子：`pre-run`、`post-run`、`on-error`|
|`memory`|object|记忆配置：短期/长期记忆的启用和策略|
|`templates`|array|可用的模板变量列表|
|`examples`|array|Few-shot 示例，帮助 LLM 理解调用方式|


```YAML
hooks:
  pre-run: |
    # 执行前检查：验证 SQL 语法
    echo "正在验证 SQL 语法..."
  post-run: |
    # 执行后：记录查询日志
    echo "查询完成，记录审计日志"
  on-error: |
    # 错误处理：通知管理员
    echo "查询失败，发送告警"

memory:
  short_term: true        # 启用短期记忆（当前对话）
  long_term: false        # 不启用长期记忆

templates:
  - name: baseDir
    description: Skill 所在目录路径
  - name: tempDir
    description: 临时文件目录路径

examples:
  - input: 
      sql: "SELECT * FROM orders LIMIT 10"
    output:
      rows: [...]
      columns: ["order_id", "amount", "create_time"]
```

---

## 完整示例：数据查询 Skill

```YAML
---
name: hive-data-query
version: 1.2.0
description: 连接 Hive 数仓执行 SQL 查询并返回结构化结果
author: data-team
tags: ["data", "sql", "hive", "warehouse"]

runtime: python
dependencies:
  - pyhive>=0.6.5
  - pandas>=2.0.0
  - tabulate>=0.9.0
env:
  - HIVE_HOST
  - HIVE_PORT
  - HIVE_DATABASE
  - HIVE_USER
  - HIVE_PASSWORD
install: |
  pip install pyhive pandas tabulate

permissions:
  filesystem: read
  network: true
  env_access:
    - HIVE_*

sandbox: docker
allowlist:
  - python3

input_schema:
  type: object
  properties:
    sql:
      type: string
      description: 要执行的 Hive SQL 查询语句
    format:
      type: string
      enum: ["json", "markdown", "csv"]
      default: "json"
      description: 返回结果格式
    limit:
      type: integer
      default: 100
      maximum: 10000
      description: 最大返回行数
  required: ["sql"]

output_schema:
  type: object
  properties:
    data:
      type: array
      description: 查询结果数据
    columns:
      type: array
      description: 列名列表
    row_count:
      type: integer
      description: 实际返回行数
    execution_time_ms:
      type: number
      description: 执行耗时

confirmation: "⚠️ 该操作将访问生产数仓，请确认 SQL 语句安全后执行"
timeout: 60
retries: 2

hooks:
  pre-run: |
    # SQL 注入检查：禁止 DDL/DML 操作
    echo "正在检查 SQL 安全性..."
  post-run: |
    # 记录审计日志
    echo "查询完成 | SQL: {{input.sql}} | 耗时: {{output.execution_time_ms}}ms"

memory:
  short_term: true
  long_term: false

templates:
  - name: baseDir
    description: Skill 根目录
  - name: tempDir
    description: 临时文件目录

examples:
  - input:
      sql: "SELECT order_id, amount FROM orders WHERE dt='2024-01-01' LIMIT 5"
      format: "markdown"
    output:
      data: [[1001, 299.99], [1002, 150.00]]
      columns: ["order_id", "amount"]
      row_count: 2
      execution_time_ms: 1250
---
```

---

## 关键注意事项

|注意点|说明|
|-|-|
|**Schema 必填**|`input_schema` 和 `output_schema` 强烈建议配置，这是 Skill 稳定性的核心|
|**env 变量声明**|所有用到的环境变量必须在 `env` 中声明，未声明的变量会被沙箱拦截|
|**sandbox 选择**|涉及代码执行、网络访问的 Skill 必须用 `docker`，纯文本处理可用 `process`|
|**timeout 合理设置**|大数据查询建议 60s+，简单操作 10s 内，避免资源占用|
|**confirmation 分级**|读操作可免确认，写操作/生产环境访问必须强制确认|


---

---

## Hooks 类型

|Hook|触发时机|典型用途|
|-|-|-|
|`pre-run`|Skill 主逻辑执行**前**|参数校验、安全检查、资源初始化|
|`post-run`|Skill 主逻辑执行**后**|日志记录、结果加工、资源清理|
|`on-error`|Skill 执行**异常时**|错误告警、降级处理、回滚操作|


---

## 基础语法

```YAML
hooks:
  pre-run: |
    # 多行 shell 脚本
    echo "开始执行..."
    
  post-run: |
    echo "执行完成"
    
  on-error: |
    echo "出错了: $ERROR_MESSAGE"
```

---

## 可用变量

在 Hooks 中可以使用这些模板变量：

|变量|说明|可用阶段|
|-|-|-|
|`{{input}}`|用户输入的完整参数|pre-run, post-run, on-error|
|`{{input.字段名}}`|具体输入字段|pre-run, post-run, on-error|
|`{{output}}`|Skill 输出结果|post-run|
|`{{output.字段名}}`|具体输出字段|post-run|
|`{{error}}`|错误信息对象|on-error|
|`{{error.message}}`|错误描述|on-error|
|`{{error.code}}`|错误码|on-error|
|`{{baseDir}}`|Skill 所在目录|全部|
|`{{tempDir}}`|临时目录路径|全部|
|`{{timestamp}}`|当前时间戳|全部|
|`{{duration_ms}}`|执行耗时（毫秒）|post-run, on-error|


---

## 实战场景详解

### 场景 1：SQL 安全检查（pre-run）

数据开发最常见的需求——防止危险 SQL 执行。

```YAML
hooks:
  pre-run: |
    # 1. 提取 SQL 内容
    SQL="{{input.sql}}"
    
    # 2. 转换为小写用于匹配
    SQL_LOWER=$(echo "$SQL" | tr '[:upper:]' '[:lower:]')
    
    # 3. 定义黑名单关键字
    BLACKLIST="drop table|truncate table|delete from|update .* set|alter table|create table|insert into|replace into"
    
    # 4. 检查是否命中黑名单
    if echo "$SQL_LOWER" | grep -Ei "$BLACKLIST" > /dev/null; then
      echo "❌ 安全拦截：检测到危险操作关键字"
      echo "SQL: $SQL"
      exit 1
    fi
    
    # 5. 检查是否包含 WHERE 子句（防止全表扫描）
    if echo "$SQL_LOWER" | grep -Ei "select .* from" > /dev/null; then
      if ! echo "$SQL_LOWER" | grep -Ei "where|limit" > /dev/null; then
        echo "⚠️ 警告：SELECT 语句缺少 WHERE 或 LIMIT，可能导致全表扫描"
        exit 1
      fi
    fi
    
    # 6. 检查 LIMIT 上限
    LIMIT=$(echo "$SQL" | grep -oEi 'limit\s+[0-9]+' | grep -oE '[0-9]+' | tail -1)
    if [ -n "$LIMIT" ] && [ "$LIMIT" -gt 10000 ]; then
      echo "❌ LIMIT 不能超过 10000，当前: $LIMIT"
      exit 1
    fi
    
    echo "✅ SQL 安全检查通过"
```

---

### 场景 2：审计日志记录（post-run）

记录谁、什么时候、查了什么、返回多少条。

```YAML
hooks:
  post-run: |
    # 构建审计日志
    LOG_ENTRY=$(cat <<EOF
    {
      "timestamp": "{{timestamp}}",
      "skill": "hive-data-query",
      "user_input": {
        "sql": "{{input.sql}}",
        "format": "{{input.format}}",
        "limit": "{{input.limit}}"
      },
      "execution": {
        "duration_ms": {{duration_ms}},
        "row_count": {{output.row_count}},
        "status": "success"
      },
      "output_columns": {{output.columns}}
    }
    EOF
    )
    
    # 写入审计日志文件（按天轮转）
    LOG_FILE="{{baseDir}}/logs/audit_$(date +%Y%m%d).log"
    mkdir -p "{{baseDir}}/logs"
    echo "$LOG_ENTRY" >> "$LOG_FILE"
    
    # 可选：发送到远程日志系统
    # curl -X POST https://log-system.company.com/api/events \
    #   -H "Content-Type: application/json" \
    #   -d "$LOG_ENTRY"
    
    echo "✅ 审计日志已记录: $LOG_FILE"
```

---

### 场景 3：异常告警（on-error）

执行失败时通知到钉钉/飞书/Slack。

```YAML
hooks:
  on-error: |
    # 构建告警消息
    ALERT_MSG=$(cat <<EOF
    🚨 Skill 执行异常告警
    
    Skill: hive-data-query
    时间: {{timestamp}}
    耗时: {{duration_ms}}ms
    
    用户输入:
    \`\`\`sql
    {{input.sql}}
    \`\`\`
    
    错误信息:
    {{error.message}}
    
    错误码: {{error.code}}
    EOF
    )
    
    # 发送到钉钉 Webhook（需要 DINGTALK_WEBHOOK 环境变量）
    if [ -n "$DINGTALK_WEBHOOK" ]; then
      curl -s -X POST "$DINGTALK_WEBHOOK" \
        -H "Content-Type: application/json" \
        -d "{
          \"msgtype\": \"text\",
          \"text\": {
            \"content\": \"$ALERT_MSG\"
          }
        }" > /dev/null
      
      echo "📢 告警已发送到钉钉"
    else
      echo "⚠️ 未配置 DINGTALK_WEBHOOK，告警仅打印到控制台"
      echo "$ALERT_MSG"
    fi
    
    # 可选：记录错误日志
    ERROR_LOG="{{baseDir}}/logs/error_$(date +%Y%m%d).log"
    mkdir -p "{{baseDir}}/logs"
    echo "{{timestamp}} | {{error.code}} | {{error.message}} | {{input.sql}}" >> "$ERROR_LOG"
```

---

### 场景 4：资源初始化与清理（pre-run + post-run）

连接池管理、临时文件处理。

```YAML
hooks:
  pre-run: |
    # 初始化临时工作目录
    WORK_DIR="{{tempDir}}/hive_query_{{timestamp}}"
    mkdir -p "$WORK_DIR"
    echo "WORK_DIR=$WORK_DIR" > "{{tempDir}}/env.sh"
    
    # 预检查 Hive 连接
    echo "🔍 检查 Hive 连接可用性..."
    # beeline -u "jdbc:hive2://$HIVE_HOST:$HIVE_PORT" -e "SHOW DATABASES;" > /dev/null 2>&1
    # if [ $? -ne 0 ]; then
    #   echo "❌ Hive 连接失败"
    #   exit 1
    # fi
    
    echo "✅ 环境初始化完成，工作目录: $WORK_DIR"

  post-run: |
    # 读取环境变量
    source "{{tempDir}}/env.sh"
    
    # 清理临时文件
    if [ -d "$WORK_DIR" ]; then
      rm -rf "$WORK_DIR"
      echo "🧹 临时目录已清理: $WORK_DIR"
    fi
    
    # 可选：结果持久化到对象存储
    # if [ "{{output.row_count}}" -gt 0 ]; then
    #   aws s3 cp "{{output.file_path}}" "s3://data-warehouse-results/queries/"
    # fi
    
    echo "✅ 资源清理完成"
```

---

### 场景 5：结果后处理（post-run）

对查询结果做二次加工。

```YAML
hooks:
  post-run: |
    # 如果返回行数超过阈值，自动添加警告标记
    ROW_COUNT={{output.row_count}}
    LIMIT={{input.limit}}
    
    if [ "$ROW_COUNT" -ge "$LIMIT" ]; then
      # 修改输出，添加警告（假设输出是 JSON）
      MODIFIED_OUTPUT=$(echo '{{output}}' | jq '. + {warning: "结果可能截断，返回'${ROW_COUNT}'行，达到 LIMIT 上限"}')
      echo "$MODIFIED_OUTPUT" > "{{tempDir}}/modified_output.json"
      echo "⚠️ 结果达到上限，已添加警告标记"
    fi
    
    # 数据质量检查：检查是否有 NULL 率过高的列
    # 这里简化处理，实际可以用 Python 脚本做更复杂的分析
    echo "🔍 数据质量检查完成"
```

---

## 高级技巧

### 1. 条件执行

用环境变量控制 Hook 是否启用：

```YAML
hooks:
  pre-run: |
    # 只在生产环境执行严格检查
    if [ "$ENV" = "production" ]; then
      # 严格的安全检查...
    else
      echo "🧪 测试环境，跳过严格安全检查"
    fi
```

### 2. 多步骤流水线

一个 Hook 里可以执行多个独立步骤：

```YAML
hooks:
  post-run: |
    # 步骤 1: 记录日志
    echo "[LOG] {{timestamp}} | Success | {{duration_ms}}ms" >> "{{baseDir}}/pipeline.log"
    
    # 步骤 2: 触发下游任务
    if [ "{{output.row_count}}" -gt 0 ]; then
      echo "[TRIGGER] 数据就绪，通知下游处理..."
      # curl -X POST ... 
    fi
    
    # 步骤 3: 指标上报
    echo "[METRIC] query_duration{{skill=\"hive-query\"}} {{duration_ms}}" | curl --data-binary @- http://pushgateway:9091/metrics/job/skill
```

### 3. 与 Skill 主体通信

通过临时文件传递数据：

```YAML
hooks:
  pre-run: |
    # 生成查询 ID，供主体使用
    QUERY_ID="q_$(date +%s)_$RANDOM"
    echo "$QUERY_ID" > "{{tempDir}}/query_id.txt"
    echo "QUERY_ID=$QUERY_ID" > "{{tempDir}}/hook_env"
    
  # Skill 主体中读取：
  # import os
  # query_id = open(os.path.join(os.environ.get('TEMP_DIR'), 'query_id.txt')).read().strip()
```

---

## 常见错误

|错误|原因|解决|
|-|-|-|
|Hook 不执行|YAML 缩进错误|确保 `hooks:` 下的 `pre-run:` 等子项缩进一致|
|变量未解析|变量名拼写错误|检查 `{{input.xxx}}` 是否与 `input_schema` 定义一致|
|权限被拒|Hook 脚本执行了未声明的操作|检查 `permissions` 和 `allowlist` 配置|
|错误被吞掉|`on-error` 里又出错|确保 `on-error` 脚本本身健壮，用 `set -e` 要小心|


---

## 完整实战模板

```YAML
---
name: production-sql-query
version: 2.0.0
description: 生产环境 SQL 查询（带完整 Hooks 生命周期）
runtime: python
dependencies:
  - pyhive
  - pandas
env:
  - HIVE_HOST
  - HIVE_PORT
  - DINGTALK_WEBHOOK

permissions:
  filesystem: read
  network: true
  env_access:
    - HIVE_*
    - DINGTALK_*

sandbox: docker
allowlist:
  - python3
  - curl

input_schema:
  type: object
  properties:
    sql:
      type: string
    limit:
      type: integer
      default: 100
  required: ["sql"]

output_schema:
  type: object
  properties:
    data:
      type: array
    row_count:
      type: integer

hooks:
  pre-run: |
    set -e
    echo "🔐 [pre-run] 开始安全检查"
    
    SQL="{{input.sql}}"
    SQL_LOWER=$(echo "$SQL" | tr '[:upper:]' '[:lower:]')
    
    # 危险操作拦截
    if echo "$SQL_LOWER" | grep -Ei "drop|truncate|delete|update|alter|create|insert|replace" > /dev/null; then
      echo "❌ 拦截危险操作"
      exit 1
    fi
    
    # 检查 LIMIT
    if ! echo "$SQL_LOWER" | grep -Ei "limit" > /dev/null; then
      echo "❌ 缺少 LIMIT 子句"
      exit 1
    fi
    
    echo "✅ [pre-run] 检查通过"

  post-run: |
    echo "📝 [post-run] 记录审计日志"
    
    LOG='{"ts":"{{timestamp}}","sql":"{{input.sql}}","rows":{{output.row_count}},"ms":{{duration_ms}}}'
    echo "$LOG" >> "{{baseDir}}/audit.log"
    
    # 大数据量告警
    if [ "{{output.row_count}}" -gt 5000 ]; then
      echo "⚠️ [post-run] 返回数据量较大: {{output.row_count}} 行"
    fi

  on-error: |
    echo "🚨 [on-error] 异常处理"
    
    MSG="Skill 异常: {{error.message}}\nSQL: {{input.sql}}\n时间: {{timestamp}}"
    
    # 发送告警（如果配置了 Webhook）
    if [ -n "$DINGTALK_WEBHOOK" ]; then
      curl -s -X POST "$DINGTALK_WEBHOOK" \
        -H "Content-Type: application/json" \
        -d "{\"msgtype\":\"text\",\"text\":{\"content\":\"$MSG\"}}" > /dev/null || true
    fi
    
    # 记录错误
    echo "{{timestamp}} | {{error.code}} | {{error.message}}" >> "{{baseDir}}/error.log"
---
```

---



## Memory 机制

Memory 解决的是 Agent/Skill 的**状态持久化**问题——让 Skill 能记住之前的交互、用户偏好、中间结果，而不是每次调用都从零开始。

### 两种记忆类型

|类型|作用域|生命周期|典型用途|
|-|-|-|-|
|**Short-term Memory**|单次对话/线程|对话结束后清除|多轮上下文、中间变量、当前任务状态|
|**Long-term Memory**|跨对话/用户级|持久化存储|用户偏好、历史查询模式、业务知识|


---

### 基础配置

```YAML
memory:
  short_term:
    enabled: true
    max_tokens: 4000        # 上下文窗口保留的 token 数
    strategy: "sliding"     # 滑动窗口 / 摘要压缩
    
  long_term:
    enabled: true
    storage: "vector"       # 存储后端：vector / kv / sql
    namespace: "user_{{user_id}}"  # 隔离不同用户的数据
    retention_days: 90      # 数据保留天数
```

---

### Short-term Memory 实战

**场景：多轮 SQL 查询**

用户第一轮问"查一下昨天的订单"，第二轮说"按地区分组"——Skill 需要知道"查订单"的上下文。

```YAML
memory:
  short_term:
    enabled: true
    max_tokens: 6000
    strategy: "sliding"    # 新消息进来，旧消息滑出
    
    # 保留的关键字段（不被滑动淘汰）
    pinned_keys:
      - "current_table"     # 当前操作的表
      - "current_filters"   # 已应用的过滤条件
      - "query_context"     # 查询业务语义
```

**Skill 主体中读取短期记忆：**

```Python
# OpenClaw 自动将短期记忆注入到上下文中
# 你也可以主动读取
from openclaw import get_thread_memory

mem = get_thread_memory()

# 获取上一轮的上下文
last_context = mem.get("query_context", {})
current_table = mem.get("current_table", "orders")

# 用户说"按地区分组" → 结合上下文补全 SQL
if "group" in user_input and "by" in user_input:
    sql = f"SELECT region, COUNT(*) FROM {current_table} {last_context.get('where', '')} GROUP BY region"
```

**多轮对话示例：**

|轮次|用户输入|Skill 理解（结合记忆）|生成的 SQL|
|-|-|-|-|
|1|查昨天订单|新查询|`SELECT * FROM orders WHERE dt='2024-01-01'`|
|2|按地区分组|对"昨天订单"分组|`SELECT region, COUNT(*) FROM orders WHERE dt='2024-01-01' GROUP BY region`|
|3|只看金额大于100的|叠加过滤条件|`... WHERE dt='2024-01-01' AND amount>100 GROUP BY region`|
|4|换成商品维度|保留时间过滤，换分组字段|`... WHERE dt='2024-01-01' AND amount>100 GROUP BY product_id`|


---

### Long-term Memory 实战

**场景：记住用户的查询偏好**

```YAML
memory:
  long_term:
    enabled: true
    storage: "vector"       # 用向量存储语义记忆
    namespace: "user_{{user_id}}"
    retention_days: 90
    
    # 索引配置
    embedding:
      model: "text-embedding-3-small"
      dimensions: 1536
    
    # 结构化存储（非语义类数据）
    kv_store:
      backend: "sqlite"     # 或 redis / postgres
      path: "{{baseDir}}/memory/{{user_id}}.db"
```

**写入长期记忆（在 post-run Hook 中）：**

```YAML
hooks:
  post-run: |
    # 记录用户的查询模式
    python3 << 'PYEOF'
    import json
    from openclaw import save_long_term_memory
    
    # 提取查询特征
    query_pattern = {
        "tables": extract_tables("{{input.sql}}"),      # ["orders", "users"]
        "filters": extract_filters("{{input.sql}}"),    # ["dt='2024-01-01'", "amount>100"]
        "aggregations": extract_aggs("{{input.sql}}"), # ["COUNT", "SUM"]
        "output_format": "{{input.format}}",
        "timestamp": "{{timestamp}}"
    }
    
    # 语义化存储（用于相似查询推荐）
    save_long_term_memory(
        key=f"query_{{timestamp}}",
        value=json.dumps(query_pattern),
        tags=["sql", "query_pattern", "user_preference"],
        # 用自然语言描述，便于语义检索
        description=f"用户查询了 {query_pattern['tables']}，关注 {query_pattern['aggregations']}"
    )
    PYEOF
```

**读取长期记忆（在 pre-run 中做智能推荐）：**

```Python
from openclaw import search_long_term_memory

# 用户输入："查一下销售情况"
# 系统检索相似历史查询
similar_queries = search_long_term_memory(
    query="销售情况 订单 金额",  # 语义检索
    filter_tags=["sql"],
    top_k=3
)

# 返回结果：
# [
#   {"description": "用户查询了 ['orders']，关注 ['SUM']", "value": {...}, "score": 0.92},
#   {"description": "用户查询了 ['orders', 'products']，关注 ['COUNT']", "value": {...}, "score": 0.85}
# ]

# Skill 据此推荐："您之前常用这类查询，是否直接执行：SELECT SUM(amount) FROM orders WHERE dt=..."
```

---

### Memory 高级技巧

**1. 记忆分层**

```YAML
memory:
  # L1: 线程级（当前对话）
  short_term:
    enabled: true
    
  # L2: 会话级（同一天内的多个对话）
  session:
    enabled: true
    ttl_hours: 24
    
  # L3: 用户级（长期偏好）
  long_term:
    enabled: true
    
  # L4: 全局级（团队共享知识）
  global:
    enabled: true
    namespace: "team_data_knowledge"
```

**2. 记忆压缩**

```YAML
memory:
  short_term:
    strategy: "summarize"    # 对话太长时，自动摘要
    
    # 触发压缩的条件
    compress_trigger:
      token_threshold: 4000
      message_count: 20
      
    # 保留的关键信息
    preserve_patterns:
      - "current_.*"        # 保留 current_ 开头的状态变量
      - "user_intent"       # 保留用户意图
```

**3. 记忆冲突解决**

```Python
# 当长期记忆和短期记忆冲突时，优先级策略
memory_resolution:
  priority: "short_term"     # 短期记忆优先（最新信息）
  # 或 "long_term"         # 长期记忆优先（稳定偏好）
  # 或 "merge"             # 合并两者
```

---



## Docker Sandbox 配置

Sandbox 是 Skill 的**安全执行边界**。生产环境必须启用 Docker 隔离，防止 Skill 脚本破坏宿主系统或泄露敏感数据。

---

### 基础配置

```YAML
sandbox: docker

# Docker 详细配置
sandbox_config:
  image: "openclaw/python:3.11-slim"    # 基础镜像
  network: "bridge"                     # 网络模式：bridge / none / host
  volumes:                             # 挂载点
    - source: "{{baseDir}}/data"
      target: "/skill/data"
      readonly: true                   # 只读挂载
    - source: "{{tempDir}}"
      target: "/tmp"
      readonly: false                  # 可写，用于临时文件
  
  resources:                           # 资源限制
    cpu_limit: "1.0"                   # 1 核
    memory_limit: "512m"               # 512MB
    memory_swap_limit: "512m"          # 禁用 swap
    
  timeout: 60                          # 容器最大存活时间（秒）
  
  env:                                 # 传入容器的环境变量
    - "HIVE_HOST"
    - "HIVE_PORT"
    - "HIVE_DATABASE"
    # 敏感变量用 secret 机制
    - name: "HIVE_PASSWORD"
      secret: true                     # 不在日志中打印
```

---

### 安全策略详解

**1. 文件系统隔离**

```YAML
sandbox_config:
  volumes:
    # 只读挂载：Skill 代码、静态数据
    - source: "{{baseDir}}"
      target: "/skill"
      readonly: true
    
    # 可写挂载：临时输出（重启后清空）
    - source: "{{tempDir}}"
      target: "/tmp"
      readonly: false
    
    # 禁止挂载宿主机的敏感目录
    # ❌ 不要这样做：
    # - source: "/etc"
    #   target: "/host_etc"
    
    # ✅ 如果需要访问宿主服务，用网络而非文件挂载
```

**2. 网络隔离**

```YAML
sandbox_config:
  # 模式 1: 完全隔离（纯本地计算）
  network: "none"
  
  # 模式 2: 桥接（允许访问外部服务，但限制入站）
  network: "bridge"
  
  # 模式 3: 自定义网络（访问特定服务）
  network: "skill-network"
  
  # 防火墙规则
  egress_rules:
    - allow: "10.0.0.0/8"      # 允许访问内网
      ports: [10000, 9083]      # Hive 端口
    - allow: "0.0.0.0/0"
      ports: [443]              # 仅允许 HTTPS 出站
    - deny: "0.0.0.0/0"         # 默认拒绝其他所有
```

**3. 资源限制（防资源耗尽攻击）**

```YAML
sandbox_config:
  resources:
    # CPU
    cpu_limit: "0.5"             # 最多半核
    cpu_shares: 512
    
    # 内存
    memory_limit: "256m"
    memory_swap_limit: "0"       # 禁用 swap，强制 OOM 杀死而非拖慢系统
    
    # 磁盘
    disk_limit: "1g"             # 容器内可写目录上限
    disk_inode_limit: 100000     # 防止创建大量小文件
    
    # PIDs
    pids_limit: 50               # 防止 fork 炸弹
```

---

### 完整生产级 Sandbox 配置

```YAML
---
name: secure-hive-query
version: 3.0.0
runtime: python
dependencies:
  - pyhive==0.6.5
  - pandas==2.1.0

# 权限声明（最小权限原则）
permissions:
  filesystem: none           # 默认不访问文件系统
  network: true              # 但需要网络访问 Hive
  env_access:
    - HIVE_HOST
    - HIVE_PORT
    - HIVE_DATABASE
    - HIVE_USER
    # HIVE_PASSWORD 用 secret 机制，不在这里声明

sandbox: docker

sandbox_config:
  # 使用加固过的基础镜像
  image: "company-registry/openclaw/python-hive:3.11-v1.2"
  
  # 网络：只允许访问 Hive 网段
  network: "bridge"
  egress_rules:
    - allow: "10.0.0.0/8"
      ports: [10000]          # HiveServer2
    - allow: "10.0.0.0/8"
      ports: [9083]           # Hive Metastore
    - deny: "0.0.0.0/0"       # 其他全部拒绝
  
  # 挂载：最小化
  volumes:
    - source: "{{baseDir}}/skill.py"
      target: "/app/skill.py"
      readonly: true
    - source: "{{tempDir}}"
      target: "/tmp"
      readonly: false
      size_limit: "100m"      # 临时目录上限
  
  # 资源限制
  resources:
    cpu_limit: "0.5"
    memory_limit: "256m"
    memory_swap_limit: "0"
    timeout: 30               # 30 秒强制终止
  
  # 环境变量
  env:
    - name: "HIVE_HOST"
      value_from: env          # 从宿主环境读取
    - name: "HIVE_PASSWORD"
      secret: true             # 标记为敏感，日志脱敏
      value_from: secret_ref:
        name: "hive-credentials"
        key: "password"

# 安全相关的 Hooks
hooks:
  pre-run: |
    echo "🔒 安全沙箱检查"
    echo "容器 ID: $(hostname)"
    echo "网络接口: $(ip addr show)"
    echo "资源限制: CPU={{sandbox_config.resources.cpu_limit}}, MEM={{sandbox_config.resources.memory_limit}}"
    
    # 验证网络连通性（只允许 Hive）
    if ! nc -z "$HIVE_HOST" "$HIVE_PORT" -w 3; then
      echo "❌ Hive 连接失败"
      exit 1
    fi
    
    # 验证无法访问外网（安全测试）
    if curl -s --max-time 2 https://www.google.com > /dev/null 2>&1; then
      echo "⚠️ 警告：外网可达，网络隔离可能失效"
    else
      echo "✅ 外网隔离正常"
    fi
    
    echo "✅ 沙箱安全检查通过"

  post-run: |
    # 清理临时文件
    rm -rf /tmp/*
    echo "🧹 沙箱内临时文件已清理"

  on-error: |
    # 记录容器状态
    echo "📊 错误时容器状态:"
    echo "内存使用: $(cat /sys/fs/cgroup/memory/memory.usage_in_bytes 2>/dev/null || echo 'N/A')"
    echo "CPU 统计: $(cat /sys/fs/cgroup/cpu/cpu.stat 2>/dev/null || echo 'N/A')"
---
```

---

### Sandbox 与 Memory 的配合

```YAML
# 安全地使用 Memory：敏感数据不落地
memory:
  short_term:
    enabled: true
    # 内存存储，容器销毁后清除
    storage: "memory"
    
  long_term:
    enabled: true
    # 外部存储，但加密
    storage: "encrypted_vector"
    encryption:
      key_ref: "memory-encryption-key"
    
    # 访问控制：只有特定 Skill 能读写
    access_control:
      allowed_skills: ["secure-hive-query", "audit-logger"]
```

---





## Memory + Sandbox + Hooks 完整架构图

```Markdown
┌─────────────────────────────────────────┐
│           用户请求 (User Input)          │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│  pre-run Hook                           │
│  ├── 安全检查 (SQL 注入、权限)            │
│  ├── 读取 Long-term Memory (用户偏好)     │
│  └── 初始化 Short-term Memory (线程状态)  │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│  Docker Sandbox (隔离执行)               │
│  ├── 资源限制: CPU 0.5 / MEM 256m       │
│  ├── 网络限制: 仅 Hive 网段              │
│  ├── 文件限制: 只读挂载 + 100m 临时目录   │
│  └── Skill 主体执行                       │
│      ├── 读取 Short-term Memory         │
│      ├── 业务逻辑 (查询/计算)             │
│      └── 写入 Short-term Memory         │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│  post-run Hook                          │
│  ├── 写入 Long-term Memory (学习偏好)     │
│  ├── 记录审计日志                         │
│  ├── 发送监控指标                         │
│  └── 清理临时资源                         │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│  on-error Hook (异常时)                 │
│  ├── 记录错误上下文到 Memory             │
│  ├── 发送告警                            │
│  └── 触发降级/重试                        │
└─────────────────────────────────────────┘
```

---

需要我再展开哪个部分？比如：

- **Memory 的向量检索实现细节**（如何设计 embedding 策略）
- **Sandbox 的自定义镜像构建**（如何打包含 Hive 客户端的轻量级镜像）
- **Hooks 与外部系统集成的最佳实践**（Prometheus 指标、日志系统对接）


