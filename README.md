# Telegram Channel Praser

> Yes, 'praser' is a typo of 'parser' - it's a feature, not a bug

将 *Telegram* 的频道内的消息转换为 图片 + *Markdown* 文字的内容，并存储到本地数据库。
内置  *HTTP* 服务器，可通过 *RESTful API* 灵活调用。
基于 *Telegram MTProto* 协议，灵活导入、监听频道信息。

**Telegram讨论组：** https://t.me/dyaogroup

## 免责声明
本项目因使用 Telegram MTProto 协议，所以无法承诺您的 Telegram 账号不会被 Telegram 官方所滥权，所以使用本项目所导致您的 Telegram 账号的损失应由自己承担。
当您下载此副本后，即视为您接受本免责声明，也熟知使用此项目所可能带来的后果。

## 当前特性
- 启动后自动拉取频道历史消息
- 监听 *Telegram Updates*：新消息、编辑消息、删除消息
- *SQLite3* 历史消息本地存储
- 基于 *Hono* 的 *RESTful API*，方便拉取消息

## 使用方法

### Telegram Developer API
1. 在 [这里](https://my.telegram.org/ "这里") 登录你的 Telegram 账号
2. 点击“API development tools”，填入 *App title* 和 *Short name* 保存即可。
3. 保存后你需要 *api_id* 和 *api_hash*

> **申请 API 属于高危操作**，特别是新注册的 Telegram 账号和使用 VoIP 语音号码注册的账号会加大封号概率，如被封号，请尽快向客服申诉申请解封。同时请勿将 API 泄露给他人。

> 来自 Telegram X 安卓端的 *api_id* 和 *api_hash*，**并不保证可用性**  
API_ID=21724  
API_HASH=3e0cb5efcd52300aec5994fdfc5bdc16  

### 修改 .env.example 为 .env
修改 .env 中的对应配置
```
# 将 Telegram 获得的api_id api_hash 填入下方
API_ID=123456
API_HASH=1234567890abcdef1234567890abcdef

# 来自 Telegram X 安卓端的 api_id 和 api_hash，并不保证可用性
# API_ID=21724
# API_HASH=3e0cb5efcd52300aec5994fdfc5bdc16

# 频道名称，复制时去掉@
# 如果需要监听新消息/编辑消息/删除消息，需要加入该频道
CHANNEL_ID=test
# API分页功能，显示多少条内容一页
CHANNEL_PAGE_SIZE=10

# HTTP 监听端口
PORT=3000
```

## 启动程序

1. 你需要使用 [Bun](https://bun.sh/ "Bun")（一款高性能的 Javascript 运行环境）来运行本程序。

一键安装 Bun: `curl -fsSL https://bun.sh/install | bash`

2. 使用 `bun install` 来安装所有依赖。
3. 使用 `bun run dev` 启动服务端。
4. 根据提示输入手机号、Telegram收到的验证码、二次密码等。
5. 第一次使用会创建数据库并拉取频道内容，后续每次启动只会拉取最新内容。

> 只需要第一次获取 Session 即可，后续可直接启动 services。

## 持久化服务

本项目使用 pm2 进行服务管理，确认 Session 登录成功后一键启动： `pm2 start ecosystem.config.js`

安装依赖：`bun i -g pm2`

## API 接口

### 健康检查

地址：`/api/v1/status`  
参数：无，推荐追加随机参数以绕过浏览器缓存
返回：200, OK

### 列出消息

地址：`/api/v1/list`  
参数：`page`（可选，默认为page=1）  
返回：指定数量的频道消息内容（由新到旧）

... 待完善，可参见 ./utils/routers.ts 定义

## 删除本地缓存

如果要更换 IP 或者修改监听频道目标 / 账号，请务必执行以下操作：

> 注意：如果你修改了配置文件中的 `MESSAGE_SQLITE_FILE` 或 `SESSION_FILE` 路径，请相应地修改脚本，在此不做赘述。

```bash
bash ./clear-cache.sh [选项]

选项:
  -M, --message    删除消息缓存 (如果需要修改监听频道)
  -S, --session    删除会话缓存 (如果更换了 IP / 账号)

示例:
  ./clear-cache.sh -M          # 只删除消息缓存
  ./clear-cache.sh -S          # 只删除会话缓存
  ./clear-cache.sh -M -S       # 同时删除消息和会话缓存
```

## 开源协议

本项目基于 [*GPL-3.0*](./LICENSE) 开源协议，您可以在遵守协议的前提下自由使用、修改、分发本项目。