# QuickForm API 参考文档

## 官网版（https://quickform.cn）

### 认证方式
所有 CLI 接口通过请求体传递用户名和密码（JSON 格式）。

### 创建任务
```
POST https://quickform.cn/cli/add
Content-Type: application/json

{
  "username": "账号",
  "password": "密码",
  "task_name": "任务名称",
  "description": "任务描述（可选）"
}
```
成功响应：`{"success": true, "apiid": "k5egb0lbzi"}`
失败响应：`{"success": false, "message": "错误原因"}`

常见错误：
- 缺少 task_name：`{"message":"缺少 task_name（任务名称）","success":false}`
- 达到任务上限：`{"message":"已达任务数量上限（当前 3 个），无法创建新任务","success":false}`
- 账号密码错误：401 或 `{"success":false,"message":"..."}`

### 查询任务列表
```
POST https://quickform.cn/cli/list
Content-Type: application/json

{"username": "账号", "password": "密码"}
```

### 提交数据
```
POST https://quickform.cn/api/<apiid>
Content-Type: application/json

{ "field1": "value1", "field2": "value2", ... }
```
字段名称和内容完全自定义，QuickForm 原样存储。

### 获取全部数据
```
GET https://quickform.cn/api/<apiid>/all
```
返回示例：
```json
[
  {"id": 1, "data": {"student_name": "张三", "score": 90}, "created_at": "2026-04-24T10:00:00"},
  {"id": 2, "data": {"student_name": "李四", "score": 75}, "created_at": "2026-04-24T10:01:00"}
]
```

---

## 本地版

### 说明
本地版没有 `/cli/` 系列接口，需要通过 session 登录后操作。

### 默认账号
- 用户名：`wst`
- 密码：`quickform`

### 本地地址获取策略（三步优先级）

**步骤1：读取已保存地址**
```bash
python3 -c "import json,os; d=json.load(open(os.path.expanduser('~/.claude/quickform_credentials.json'))); print(d['local']['base_url'])"
```

**步骤2：自动检测本机 IP**
若无保存地址，自动获取本机局域网 IP 并拼接端口 5001：
```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.connect(("8.8.8.8", 80))
local_ip = s.getsockname()[0]
s.close()
auto_url = f"http://{local_ip}:5001"
print(auto_url)
```
验证自动检测地址是否可用：
```bash
curl -s -o /dev/null -w "%{http_code}" http://<auto_ip>:5001/
# 返回 200 或 302 则可用，否则进入步骤3
```

**步骤3：自动检测失败，提示用户手动输入**
```
自动检测地址 http://<auto_ip>:5001 无法访问，请手动输入本地 QuickForm 地址
（格式：http://IP地址:端口，例如 http://192.168.1.100:5001）：
```

**保存最终确认的地址：**
```python
import json, os
path = os.path.expanduser('~/.claude/quickform_credentials.json')
try:
    d = json.load(open(path))
except:
    d = {}
d.setdefault('local', {})['base_url'] = 'http://最终地址'
json.dump(d, open(path, 'w'), ensure_ascii=False)
```

### 启动服务
```bash
bash ~/Desktop/桌面文件/QuickForm\ 1.5/start.sh &
```
验证：访问 `<LOCAL_BASE_URL>/` 返回正常页面即为成功。

### ⚠️ 重要：本地版 API ID 说明

本地版每个任务有两个 ID：
- **数字序号**（如 `10`）：任务在数据库中的自增 ID，用于访问 `/task/10` 管理页面
- **随机字符串 API ID**（如 `C9VaWGMQEZY`）：系统生成的真实接口 ID，才能用于数据提交和获取

**必须使用随机字符串 API ID**，不能直接用数字序号调用 `/api/10`，否则返回 `{"error":"任务不存在"}`。

### 创建任务并获取真实 API ID（Python session 方式）

```python
import urllib.request, urllib.parse, http.cookiejar, re

cj = http.cookiejar.CookieJar()
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))

# 1. 登录
login_data = urllib.parse.urlencode({'username': 'wst', 'password': 'quickform'}).encode()
req = urllib.request.Request('<LOCAL_BASE_URL>/login', data=login_data)
req.add_header('Content-Type', 'application/x-www-form-urlencoded')
opener.open(req)

# 2. 创建任务，获取数字 ID
task_data = urllib.parse.urlencode({'title': '任务名称', 'description': '描述'}).encode()
req2 = urllib.request.Request('<LOCAL_BASE_URL>/create_task', data=task_data)
req2.add_header('Content-Type', 'application/x-www-form-urlencoded')
resp = opener.open(req2)
numeric_id = re.search(r'/task/(\d+)', resp.geturl()).group(1)

# 3. 从任务详情页解析真实随机字符串 API ID
req3 = urllib.request.Request(f'<LOCAL_BASE_URL>/task/{numeric_id}')
html = opener.open(req3).read().decode('utf-8')
real_api_id = list(set(re.findall(r'/api/([a-zA-Z0-9\-_]+)', html)))[0]

print(f"数字ID: {numeric_id}，真实API ID: {real_api_id}")
```

### 提交数据
```
POST <LOCAL_BASE_URL>/api/<real_api_id>
Content-Type: application/json

{"field1": "value1", ...}
```
成功响应：`{"message": "提交成功"}`

### 获取全部数据
```
GET <LOCAL_BASE_URL>/api/<real_api_id>/all
```

### ⚠️ 本地版数据结构特殊说明

本地版返回的 `data` 字段是 **Python dict 字符串**（使用单引号），而非标准 JSON：

```json
{
  "submissions": [
    {
      "data": "{'student_name': '王小明', 'score': 3}",
      "id": 37,
      "submitted_at": "2026-04-25 07:48:06"
    }
  ]
}
```

在 HTML 大屏中解析时需转换：

```javascript
function parsePyDict(str) {
  const json = str
    .replace(/'/g, '"')
    .replace(/\bNone\b/g, 'null')
    .replace(/\bTrue\b/g, 'true')
    .replace(/\bFalse\b/g, 'false');
  return JSON.parse(json);
}

// 使用示例
const data = parsePyDict(submission.data);
```
