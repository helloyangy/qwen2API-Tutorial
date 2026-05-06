qwen2API 用于将通义千问网页版能力转换为API接口，支持最新qwen3.6-plus模型。

![ae2af77bc5ff3f3493be1fdd4f6fdd9f.png](../_resources/ae2af77bc5ff3f3493be1fdd4f6fdd9f.png)

推荐服务器部署，不要选择国内地区，实现24小时调用自由，还可以共享给朋友，同事等。

腾讯云国外价格是199元一年，2核4G30M带宽，60GBSSD盘 1.5T月流量，推荐首尔地区↓↓↓，系统选Windows，可以同样价格续费。

购买地址：https://curl.qcloud.com/oyWDLkRJ

![22afd9f9fcd8e64750c52595659bbf66.png](../_resources/22afd9f9fcd8e64750c52595659bbf66.png)

**教程**

1.Ubuntu24系统安装Docker

```
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

![d7d4d40767f0c4696dcc2c3aac3e1897.png](../_resources/d7d4d40767f0c4696dcc2c3aac3e1897.png)

2.准备目录

```
mkdir qwen2api && cd qwen2api
mkdir -p data logs
```

![3409cd12dcf1095c35dd87329ba570da.png](../_resources/3409cd12dcf1095c35dd87329ba570da.png)

3.创建 docker-compose.yml

```
 sudo nano docker-compose.yml
```

 填入以下内容

```
services:
  qwen2api:
    image: yujunzhixue/qwen2api:latest
    container_name: qwen2api
    restart: unless-stopped
    env_file:
      - path: .env
        required: false
    ports:
      - "7860:7860"
    volumes:
      - ./data:/workspace/data
      - ./logs:/workspace/logs
    shm_size: '256m'
    environment:
      PYTHONIOENCODING: utf-8
      PORT: "7860"
      ENGINE_MODE: "hybrid"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7860/healthz"]
      interval: 30s
      timeout: 10s
      start_period: 120s
      retries: 3
```

 ![2cf344dab0364c8839201c6772c07a87.png](../_resources/2cf344dab0364c8839201c6772c07a87.png)

 4.创建 .env
 
```
 sudo nano .env
```

 填入以下内容，登录密钥可以自行修改

```
 # ========== 必须修改 ==========
ADMIN_KEY=change-me-now              # 管理台登录密钥，必须修改为强密码！

# ========== 基础配置 ==========
PORT=7860                            # 服务监听端口
WORKERS=1                            # Uvicorn worker 数量，必须保持 1（多 worker 会导致 JSON 文件冲突）
LOG_LEVEL=INFO                       # 日志级别：DEBUG/INFO/WARNING/ERROR

# ========== 并发控制 ==========
MAX_INFLIGHT=1                       # 每账号最大并发请求数（账号多时可改为 2）
MAX_RETRIES=3                        # 请求失败最大重试次数（网络不稳定时增加到 5）

# ========== 限流冷却 ==========
ACCOUNT_MIN_INTERVAL_MS=1200         # 同账号两次请求最小间隔（毫秒），被限流时启用
REQUEST_JITTER_MIN_MS=120            # 请求前随机抖动最小值（毫秒）
REQUEST_JITTER_MAX_MS=360            # 请求前随机抖动最大值（毫秒）
RATE_LIMIT_BASE_COOLDOWN=600         # 限流基础冷却时间（秒），频繁限流时增加到 1200
RATE_LIMIT_MAX_COOLDOWN=3600         # 限流最大冷却时间（秒）

# ========== 数据文件路径（Docker 部署通常不需要改）==========
ACCOUNTS_FILE=/workspace/data/accounts.json
USERS_FILE=/workspace/data/users.json
CONTEXT_CACHE_FILE=/workspace/data/context_cache.json
UPLOADED_FILES_FILE=/workspace/data/uploaded_files.json
```

![cfeb841c92e4f48b15ae76a6208a2465.png](../_resources/cfeb841c92e4f48b15ae76a6208a2465.png)

5.启动服务

```
sudo docker compose up -d
```

![e2f585dd648c309ac09aabbcf4bd4584.png](../_resources/e2f585dd648c309ac09aabbcf4bd4584.png)

6.浏览器访问

```
http://你的服务器IP:7860
```

![d1869169b18639aabf34d5b85ef8ff1c.png](../_resources/d1869169b18639aabf34d5b85ef8ff1c.png)

7.在系统设置，输入登录密钥

![1ff042a5a83110d20157a3fa3f54a788.png](../_resources/1ff042a5a83110d20157a3fa3f54a788.png)

8.获取以下千问的token填进去

https://chat.qwen.ai/

![1a410d8e21090d0d1f3e70b41e3765ae.png](../_resources/1a410d8e21090d0d1f3e70b41e3765ae.png)

9.账号添加成功

![53e3511740fc7f52ccdae74c528c5b8a.png](../_resources/53e3511740fc7f52ccdae74c528c5b8a.png)

10.测试成功

![5d9ab1c1fd668f57fe3f78ac9be7e52a.png](../_resources/5d9ab1c1fd668f57fe3f78ac9be7e52a.png)

接口是：http://127.0.0.1:7860/v1/chat/completions

密钥：需要新建一个

模型：qwen3.6-plus