# 来自AI的回答

`websockets` 是 Python 中一个用于实现 WebSocket 通信的库，支持客户端和服务端的开发，并提供了简洁、易用的 API 接口。

以下是 `websockets` 库的基本用法：

## 安装

使用 pip 命令安装 `websockets` 库：

```
pip install websockets

```

## 创建 WebSocket 服务端

创建 WebSocket 服务端的示例代码如下：

```
pythonCopy Codeimport asyncio
import websockets

async def server(websocket, path):
    # 新的客户端连接时执行
    print(f"Client connected: {websocket.remote_address}")
    await websocket.send("Hello, world!")

    # 处理客户端消息
    async for message in websocket:
        print(f"Received message from {websocket.remote_address}: {message}")
        await websocket.send(f"Server received: {message}")

    # 断开连接时执行
    print(f"Client disconnected: {websocket.remote_address}")

# 启动服务器
start_server = websockets.serve(server, "localhost", 8765)

# 运行事件循环
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，首先定义了一个 `server` 函数作为 WebSocket 的请求处理函数，该函数会在客户端连接时执行。在该函数中，首先通过 `await websocket.send("Hello, world!")` 向客户端发送一条消息，然后通过 `async for message in websocket:` 循环接收客户端的消息，并回复接收到的消息。

接下来通过 `websockets.serve` 函数创建 WebSocket 服务器。最后通过 `asyncio.get_event_loop().run_forever()` 启动事件循环，使服务器可以接收来自客户端的连接请求。

## 创建 WebSocket 客户端

创建 WebSocket 客户端的示例代码如下：

```
pythonCopy Codeimport asyncio
import websockets

async def client():
    async with websockets.connect("ws://localhost:8765/") as websocket:
        await websocket.send("Hello, server!")

        # 处理服务器消息
        async for message in websocket:
            print(f"Received message from server: {message}")

# 运行事件循环
asyncio.get_event_loop().run_until_complete(client())

```

在上述代码中，首先定义了一个 `client` 协程用于发送和接收 WebSocket 消息。在该协程中，通过 `websockets.connect` 方法创建 WebSocket 连接，并通过 `await websocket.send("Hello, server!")` 发送一条消息。然后通过 `async for message in websocket:` 循环接收服务器的消息，并打印出来。

最后通过 `asyncio.get_event_loop().run_until_complete` 启动事件循环，使客户端可以连接到 WebSocket 服务器。

以上就是 `websockets` 库的基本用法，通过 `websockets` 库，我们可以非常方便地实现 WebSocket 客户端和服务端的开发。更多详细说明可以参考官方文档：<https://websockets.readthedocs.io/en/stable/intro.html>

***

`websockets` 库提供了许多高级的用法，下面列举一些常见的：

## 向多个客户端广播消息

通过 WebSocket 可以向多个客户端广播消息，示例代码如下：

```
import asyncio
import websockets

# 存储 WebSocket 连接的集合
connected = set()

async def server(websocket, path):
    # 新的客户端连接时执行
    print(f"Client connected: {websocket.remote_address}")
    connected.add(websocket)

    try:
        # 处理客户端消息
        async for message in websocket:
            print(f"Received message from {websocket.remote_address}: {message}")

            # 广播消息给所有其他客户端
            for conn in connected:
                if conn != websocket:
                    await conn.send(f"Client {websocket.remote_address} says: {message}")

    finally:
        # 断开连接时执行
        print(f"Client disconnected: {websocket.remote_address}")
        connected.remove(websocket)

# 启动服务器
start_server = websockets.serve(server, "localhost", 8765)

# 运行事件循环
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们定义了一个 `connected` 集合来保存所有与服务器连接的 WebSocket 对象，在新客户端连接时，将其添加到 `connected` 集合中。然后向所有其他客户端广播收到的消息。

## SSL/TLS 安全连接

WebSocket 也支持使用 SSL/TLS 安全连接，示例代码如下：

```
pythonCopy Codeimport asyncio
import pathlib
import ssl
import websockets

ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
ssl_context.load_cert_chain(
    pathlib.Path("cert.pem"),
    pathlib.Path("key.pem")
)

async def server(websocket, path):
    # 服务器处理逻辑...

start_server = websockets.serve(
    server, "localhost", 8765,
    ssl=ssl_context
)

asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们创建了一个 `SSLContext` 对象，并加载 SSL 证书和密钥，然后在创建 WebSocket 服务器时添加 `ssl` 参数即可建立 SSL/TLS 安全连接。

## 集成 Flask 框架

如果您使用 Flask 框架开发 Web 应用，可以很方便地将 WebSocket 与 Flask 集成，示例代码如下：

```
pythonCopy Codefrom flask import Flask, render_template
import asyncio
import websockets

app = Flask(__name__)

# 存储 WebSocket 连接的集合
connected = set()

@app.route("/")
def index():
    return render_template("index.html")

@app.websocket("/ws")
async def websocket(ws):
    # 新的客户端连接时执行
    connected.add(ws)

    try:
        # 处理客户端消息
        async for message in ws:
            print(f"Received message from {ws.remote_address}: {message}")

            # 广播消息给所有其他客户端
            for conn in connected:
                if conn != ws:
                    await conn.send(f"Client {ws.remote_address} says: {message}")

    finally:
        # 断开连接时执行
        print(f"Client disconnected: {ws.remote_address}")
        connected.remove(ws)

# 启动 Flask 应用
if __name__ == "__main__":
    start_server = websockets.serve(websocket, "localhost", 8765)
    asyncio.get_event_loop().run_until_complete(start_server)
    app.run()

```

在上述代码中，我们定义了一个 Flask 应用，并通过 `@app.websocket` 装饰器将 WebSocket 路由添加到 Flask 应用中。然后在 WebSocket 处理函数中对客户端发来的消息进行广播。

以上就是 `websockets` 库一些常用的高级用法，可以根据具体需求选择使用。更多详细说明可以参考官方文档：<https://websockets.readthedocs.io/en/stable/>

***

当然，`websockets` 库还有许多其他常见的高级用法，下面列举一些：

## 控制 WebSocket 连接

`websockets` 库提供多种控制 WebSocket 连接的方法，下面列举一些：

*   `ping()` / `pong()`: 发送 ping/pong 消息用于保持 WebSocket 连接心跳。
*   `close()`: 显式关闭 WebSocket 连接。
*   `terminate()`: 强制关闭 WebSocket 连接。

示例代码如下：

```
async def server(websocket, path):
    # 发送 ping 消息
    await websocket.ping("Are you there?")

    # 处理客户端消息
    async for message in websocket:
        print(f"Received message: {message}")

        # 发送 pong 消息
        await websocket.pong("I'm here!")

        # 关闭 WebSocket 连接
        if message == "close":
            await websocket.close()

        # 强制关闭 WebSocket 连接
        if message == "terminate":
            websocket.terminate()

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们使用 `ping()` / `pong()` 方法保持 WebSocket 连接心跳，并使用 `close()` / `terminate()` 方法关闭 WebSocket 连接。

## 限制 WebSocket 连接数目

如果您希望限制 WebSocket 连接数目，可以使用 `asyncio.Semaphore` 对象进行限制。示例代码如下：

```
# 定义信号量
MAX_CONNECTIONS = 10
semaphore = asyncio.Semaphore(MAX_CONNECTIONS)

async def server(websocket, path):
    # 请求信号量
    async with semaphore:
        # 处理客户端消息
        async for message in websocket:
            print(f"Received message: {message}")

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们定义了一个 `MAX_CONNECTIONS` 常量来限制 WebSocket 连接数目，并使用 `asyncio.Semaphore` 对象进行限制。

## 自定义 WebSocket 握手

如果您希望自定义 WebSocket 握手过程，可以使用 `websocket.handshake()` 方法完成握手。示例代码如下：

```
pythonCopy Codeasync def server(websocket, path):
    # 自定义握手过程
    headers = websocket.request_headers
    key = headers["Sec-WebSocket-Key"]
    response = b"HTTP/1.1 101 Switching Protocols\r\n"
    response += b"Upgrade: websocket\r\n"
    response += b"Connection: Upgrade\r\n"
    response += b"Sec-WebSocket-Accept: "
    response += base64.b64encode(
        hashlib.sha1(key.encode() + b"258EAFA5-E914-47DA-95CA-C5AB0DC85B11").digest()
    )
    response += b"\r\n\r\n"
    await websocket.write(response)

    # 处理客户端消息
    async for message in websocket:
        print(f"Received message: {message}")

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们使用 `websocket.handshake()` 方法自定义 WebSocket 握手过程，并使用 `websocket.write()` 方法编写响应消息。

希望这些高级用法可以对您有所帮助，更多详细说明可以参考官方文档：<https://websockets.readthedocs.io/en/stable/>

***

当然，`websockets` 库还有许多其他高级用法，下面列举一些：

## 异步上下文管理器

在 `websockets` 库中，WebSocket 对象也可以用作异步上下文管理器，以更方便地管理 WebSocket 连接生命周期。示例代码如下：

```
pythonCopy Codeasync def server(websocket, path):
    async with websocket:
        # 处理客户端消息
        async for message in websocket:
            print(f"Received message: {message}")

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们使用 `async with` 语句将 WebSocket 对象作为异步上下文管理器，自动管理 WebSocket 连接的生命周期。

## 自定义传输实现

如果您需要自定义 WebSocket 传输层实现，可以通过继承 `websockets.transport.BaseTransport` 类并实现相应方法来实现。示例代码如下：

```
pythonCopy Codeclass MyTransport(websockets.transport.BaseTransport):
    async def send(self, data):
        # send data via your custom transport here
        pass

    async def recv(self):
        # receive data via your custom transport here
        return data

    async def close(self, code=1000, reason=""):
        # close the transport here
        pass

async def server(websocket, path):
    # 使用自定义传输实现
    transport = MyTransport(websocket)
    websocket = websockets.WebSocketCommonProtocol(transport=transport)
    # 处理客户端消息
    async for message in websocket:
        print(f"Received message: {message}")

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们自定义了一个 `MyTransport` 类来实现自定义传输层实现，并通过传入 `transport=MyTransport(websocket)` 来使用自定义传输实现。

## 批量发送消息

如果您需要向多个客户端批量发送消息，可以使用 `asyncio.gather()` 方法并发发送消息。示例代码如下：

```
async def broadcast(message, clients):
    # 并发发送消息
    tasks = [client.send(message) for client in clients]
    await asyncio.gather(*tasks)

async def server(websocket, path):
    # 存储 WebSocket 连接的集合
    connected = set()

    # 新的客户端连接时执行
    print(f"Client connected: {websocket.remote_address}")
    connected.add(websocket)

    try:
        # 处理客户端消息
        async for message in websocket:
            print(f"Received message from {websocket.remote_address}: {message}")

            # 向所有其他客户端批量发送消息
            await broadcast(f"Client {websocket.remote_address} says: {message}", connected - {websocket})

    finally:
        # 断开连接时执行
        print(f"Client disconnected: {websocket.remote_address}")
        connected.remove(websocket)

start_server = websockets.serve(server, "localhost", 8765)
asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```

在上述代码中，我们定义了一个 `broadcast()` 函数来并发向多个客户端发送消息。

希望这些高级用法可以对您有所帮助，更多详细说明可以参考官方文档：<https://websockets.readthedocs.io/en/stable/>
