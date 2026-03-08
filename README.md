# ***云星AI公共版API调用官方文档***
#### yxai 公开版完全免费,但是面向其他普通用户的官方版有每日限额额度度

## 感谢:
字节跳动-dobao  
百度-百度智能云/百度千帆

## 创建对话接口调用方式
### 原理：
接收到你的API申请时  
成为一个中间件  
连接百度千帆的api  
向你返回结果

### 支持调用方式：

调用中间件API=POST（推荐）	也兼容 GET/OPTIONS（适配跨域预检）  
中间件内部调用百度千帆API=POST（强制）	百度千帆该接口仅支持 POST，不可修改  
官方API=POST（强制）  百度千帆该接口仅支持 POST，不可修改  

### 调用地址：
#### 1. 中间件
##### https://api-yxai.rth1.xyz/api/conve.php
#### 2.官方版
##### https://api-yxai.rth1.xyz/api/conve2.php （和中间件版本通用，改个API就行）

### 返回数据：
```json
{"request_id":"b86c20ee-accf-4215-a5b0-364f02c64758","conversation_id":"341eb380-5228-4c86-8331-36926e862bee"}
```
### 返回数据介绍
```json
{"request_id":"不用管","conversation_id":"对话接口ID，需要保管"}
```

### 调用方法：
#### cmd调用：
#### 最简调用（推荐）
```cmd
curl -X POST https://api-yxai.rth1.xyz/api/conve.php
```
#### 带详细信息的调用（便于排查问题）
```cmd
curl -v -X POST https://api-yxai.rth1.xyz/api/conve.php
```

### 返回示例（成功）：
```json  
{"request_id":"bba0b90e-c5f7-4d62-b9df-cf1885c3464a","conversation_id":"319f9294-fc75-4ec4-94f3-4d306bd6f43e"}
```

### 返回示例（失败）：
```json  
{"error":true,"message":"CURL请求错误: Could not resolve host: qianfan.baidubce.com","request_id":"","conversation_id":""
```

### 前端js调用：
#### 写法 1：原生 fetch（无需依赖)
```javascript
fetch('https://api-yxai.rth1.xyz/api/conve.php', {  
  method: 'POST',  
  headers: {  
    'Content-Type': 'application/json' // 可选，中间件已兼容  
  }  
})  
.then(response => response.json()) // 解析JSON响应  
.then(data => {  
  console.log('调用成功：', data);  
  // 示例：获取conversation_id  
  if (data.conversation_id) {  
    alert('会话ID：' + data.conversation_id);  
  }  
})  
.catch(error => {  
  console.error('调用失败：', error);  
});  
```

#### 写法 2：Axios（前端框架常用，需先引入 Axios）
```javascript
（需先引入：<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>）  
axios.post('https://api-yxai.rth1.xyz/api/conve.php')  
.then(response => {  
  console.log('调用成功：', response.data);  
})  
.catch(error => {  
  console.error('调用失败：', error);  
});  
```

### 后端PHP：
```php
<?php  
$url = 'https://api-yxai.rth1.xyz/api/conve.php';  

// 初始化curl  
$ch = curl_init($url);  
curl_setopt_array($ch, [  
  CURLOPT_POST => true,  
  CURLOPT_RETURNTRANSFER => true,  
  CURLOPT_TIMEOUT => 30,  
  CURLOPT_SSL_VERIFYPEER => false, // 测试环境关闭，生产建议开启  
  CURLOPT_SSL_VERIFYHOST => false  
]);

// 执行请求  
$response = curl_exec($ch);  
if (curl_errno($ch)) {  
  die('调用失败：' . curl_error($ch));  
}  
curl_close($ch);

// 解析并使用返回数据  
$result = json_decode($response, true);  
if ($result && !$result['error']) {  
  echo '会话ID：' . $result['conversation_id'];  
} else {  
  echo '失败原因：' . $result['message'];  
}  
?>  
```

### Python：
#### Python调用示例（需安装requests、urllib3库：pip install requests）
```Python
import requests  
import urllib3  

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)  

url = "https://api-yxai.rth1.xyz/api/conve.php"  
try:  
    response = requests.post(  
        url,  
        timeout=60,  
        verify=False,  
        headers={"User-Agent": "Mozilla/5.0"}   
    )  
    print(response.text)  
except Exception as e:  
    print(f"详细错误：{str(e)}")  
```



## 对话接口调用方式
# 注意:会话 ID conversation_id 不通过API传输会自动随机生成(官方百度千帆自带机制)
### 调用地址：

##### https://api-yxai.rth1.xyz/api/query.php

### 返回数据：
#### 正常：
```json
{  
    "code": 200,          // 状态码：200成功，500失败  
    "msg": "success",     // 提示信息：失败时显示错误原因  
    "data": {             // 核心数据  
        "answer": "你好，我是云星AI-公开版...", // AI的核心回答  
        "conversation_id": "26bb2bfc-33cb-467f-b311-13e013cd03f6", // 会话ID  
        "message_id": "08767d7c-03c9-4496-bc82-e4bb93e829cc",      // 消息ID  
        "request_id": "6200fcbf-e345-4c2a-a4ee-a3ad0c3867aa",      // 请求ID  
        "date": "2026-03-07T11:31:35Z",                            // 响应时间  
    }  
}  
```

###### 虽然具体是不是这样我也不知道，我学的又不是后端qwq
#### 对话ID错误：

#### query（提问内容）错误：
```json  
{  
    "code": 500,  
    "msg": "参数错误：query（提问内容）不能为空",  
    "data": null  
}
```

### 请求方式：
##### 目前仅支持post
`application/json` | 前端JS、后端调用 |  
`application/x-www-form-urlencoded` | 传统表单提交、简单AJAX请求 |

### 关键说明
1. **不支持 GET 请求**：因为涉及会话ID（Cookie）和用户输入，GET 请求会暴露参数且不安全，代码中只处理 `POST` 请求；
2. **自动兼容两种 Content-Type**：代码里通过 `file_get_contents('php://input')` 读取原始输入，再兼容 `$_POST`，所以无论前端传 JSON 还是表单格式，都能解析到 `query` 参数；
3. **和 API 方的适配**：调用方接口接收请求后，会自动以 `application/json` 格式调用你的 `query.php`（API 方），无需你额外处理。

### 所需数据:
调用时必须传递的参数（必填项）和可选参数：  
参数名	类型	是否必填	说明  
query	字符串	是	用户的提问内容（比如："你好，你是什么？"）  
conversation_id	字符串	否	会话 ID，用于连续对话（不传则自动生成）  

### 调用方法：
方式 1：使用命令行（curl）调用（测试 / 调试首选）  
这是最直接的测试方式，打开终端 / 命令提示符，执行以下命令：  
```bash

curl -X POST "https://api-yxai.rth1.xyz/api/query.php" \
-H "Content-Type: application/json" \
-d '{
    "query": "你好，你是什么？",
    "conversation_id": "26bb2bfc-33cb-467f-b311-13e013cd03f6"
}'
```
##### 执行后会直接返回 JSON 响应

方法 2:使用传统HTML表单调用
# 该方法出现了bug,正在修改测试中qwq
```html
<!DOCTYPE html>
<meta charset="utf-8">
<title>传统表单调用 AI</title>
<body>
    <!-- 传统表单 -->
    <form method="POST" action="你的调用方文件地址.php" target="_blank">
        <input type="text" name="query" placeholder="输入你的问题" required>
        <button type="submit">提交</button>
    </form>
</body>
</html>
```

方法 3:使用前端js
```javascript
// 定义请求参数
const requestData = {
    query: "你好，你是什么？",
    conversation_id: "26bb2bfc-33cb-467f-b311-13e013cd03f6"
};

// 发送请求
fetch("https://api-yxai.rth1.xyz/api/query.php", {
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Accept": "application/json"
    },
    body: JSON.stringify(requestData)
})
.then(response => response.json())
.then(data => {
    // 处理响应结果
    if (data.code === 200) {
        console.log("AI回答：", data.data.answer); // 提取核心回答
        console.log("会话ID：", data.data.conversation_id); // 用于后续连续对话
    } else {
        console.error("请求失败：", data.msg);
    }
})
.catch(error => {
    console.error("网络错误：", error);
});
```

方法 4:我一个 Axios 下去
```javascript
// 引入Axios（网页需先加载：<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>）
axios.post("https://api-yxai.rth1.xyz/api/query.php", {
    query: "你好，你是什么？",
    conversation_id: "26bb2bfc-33cb-467f-b311-13e013cd03f6"
})
.then(response => {
    const data = response.data;
    if (data.code === 200) {
        alert("AI回答：" + data.data.answer);
    } else {
        alert("错误：" + data.msg);
    }
})
.catch(error => {
    alert("请求出错：" + error.message);
});
```

方法 5:后端PHP
```php
// 后端调用该接口（JSON格式）
$postData = json_encode(['query' => '你好']);
$ch = curl_init('你的调用方文件地址');
curl_setopt_array($ch, [
  CURLOPT_POST => true,
  CURLOPT_POSTFIELDS => $postData,
  CURLOPT_HTTPHEADER => ['Content-Type: application/json'],
  CURLOPT_RETURNTRANSFER => true
]);
$response = curl_exec($ch);
curl_close($ch);
```
