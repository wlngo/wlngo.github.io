---
title: websocket之SockJS
date: 2025-06-18
updated: 2025-06-18
url: /archives/websocket-SockJs
categories:
tags:
---

# 📡 SockJS 使用手册

SockJS 是一个浏览器和服务器都支持的 JavaScript 库，用于在 WebSocket 不可用时提供类似 WebSocket 的双向通信能力。它自动回退到兼容传输方式（如 AJAX 长轮询、iframe 等），适用于构建兼容性强的实时应用系统。

---

## 🧩 核心特点

- **自动回退**：在浏览器或网络不支持 WebSocket 时自动退回其他通信方式
- **浏览器兼容性强**：支持 IE 8 及以上浏览器
- **标准化接口**：与原生 WebSocket 类似的 API，如 `onopen`、`onmessage`、`onclose`
- **服务器框架集成良好**：与 Spring Boot 等现代后端框架良好集成
- **跨域支持友好**：可配置 CORS，支持跨源通信

---

## 🧪 使用示例

### 客户端（JavaScript）

```
<script src="https://cdn.jsdelivr.net/npm/sockjs-client@1/dist/sockjs.min.js"></script>
<script>
  const sock = new SockJS('http://localhost:8080/ws');

  sock.onopen = function () {
    console.log('连接已打开');
    sock.send('Hello Server');
  };

  sock.onmessage = function (e) {
    console.log('收到消息:', e.data);
  };

  sock.onclose = function () {
    console.log('连接已关闭');
  };
</script>
```

### 服务端（Spring Boot 示例）

```java
@Configuration
@EnableWebSocket
@RequiredArgsConstructor
public class WebSocketConfig implements WebSocketConfigurer {

    private final HttpAuthHandler httpAuthHandler;

    private final WsJwtInterceptor wsJwt;

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry
                .addHandler(httpAuthHandler, "/ws")
                .addInterceptors(wsJwt)
                .setAllowedOrigins("*").withSockJS();
    }
}
```

---

## 🚚 SockJS 支持的多种传输方式

SockJS 会在以下传输方式中自动选择一种最适合的方式：

| 顺序 | 传输方式           | 描述                                       | 是否支持双向 |
|------|--------------------|--------------------------------------------|--------------|
| 1    | WebSocket          | 首选方式，基于 TCP 的真正全双工通信        | ✅            |
| 2    | XHR Streaming      | 长连接持续推送数据                         | ✅            |
| 3    | XHR Polling        | 短轮询模拟实时通信                         | ✅            |
| 4    | EventSource        | HTML5 单向推送，兼容性差                   | ❌            |
| 5    | iframe Streaming   | 利用隐藏 iframe 推送 script 内容           | ✅            |
| 6    | iframe Polling     | 最低效的回退方式                           | ✅            |

**选择优先级顺序：** `WebSocket > XHR-streaming > XHR-polling > EventSource > iframe-streaming > iframe-polling`

---

## 🧠 SockJS vs 原生 WebSocket

| 特性                   | SockJS                      | WebSocket 原生 |
|------------------------|-----------------------------|----------------|
| 支持旧浏览器           | ✅ 是                        | ❌ 否           |
| 自动回退机制           | ✅ 支持                      | ❌ 无           |
| 与 STOMP 协议集成      | ✅ 良好                      | ❌ 手动实现     |
| 性能（支持 WS 时）     | 🚀 快（几乎等同原生）        | 🚀 非常快       |
| 跨域与 HTTP 协调       | ✅ 更易管理                  | ✅ 支持         |

---

## ✅ 使用场景

- 移动设备或旧浏览器环境
- 企业内网中使用 WebSocket 被屏蔽
- 构建如聊天室、实时通知、在线监控等系统
- 与 Spring Boot + STOMP 构建推送系统

---

## 🔍 Curl 请求 SockJS

虽然 SockJS 是为浏览器设计的，但你可以用 `curl` 模拟部分通信步骤（如 xhr_send、xhr 接收等）

### 获取服务信息

```bash
curl http://localhost:8080/ws/info
```

### 发送消息（POST）

```bash
curl -X POST -H "Content-Type: text/plain" --data '"Hello from curl!"' http://localhost:8080/ws/123/abc123/xhr_send
```

### 拉取消息（GET）

```bash
curl http://localhost:8080/ws/123/abc123/xhr
```

> ⚠ 注意：SockJS 的连接 ID 和 session 是动态生成的，使用 curl 完整模拟需要提前抓包或实现 session 管理。建议使用 `websocat` 工具或浏览器调试。

---

## 🔧 推荐工具

- [sockjs-client (GitHub)](https://github.com/sockjs/sockjs-client)
- [`websocat`](https://github.com/vi/websocat)：命令行 WebSocket 工具
- Fiddler / Wireshark：抓包工具
- Postman 插件：测试 WebSocket API

---

## 📘 结语

SockJS 提供了良好的兼容性、容错能力和浏览器支持，是构建生产级实时通信系统时值得考虑的解决方案，特别适用于需要兼容旧环境或复杂网络限制的场景。
降级后不支持 二进制消息仅支持文本消息。

