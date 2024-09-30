+++
title = 'Telegram Bot'
date = 2024-09-15T12:19:46+08:00
draft = true
+++

因为python-telegram-bot的官方文档太抽象 于是就写了

<!--more-->

可以使用 table of contents 快速跳到想看的内容

## 结构

先注册一个 application

```python
application = ApplicationBuilder().token("your_token").build()
```

创建 handler (以获取 telegram user id 为例)，get_id 是函数名

```python
get_id_handler = CommandHandler("getid", get_id)
application.add_handler(get_id_handler)
```

然后定义函数，一定要用异步

```python
async def get_id(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await context.bot.send_message(
        chat_id=update.effective_chat.id,
        text=update.effective_chat.id
    )
```

运行 /getid 指令，即可获取到 user id

## 发送消息

### 发送文本消息

```python
await context.bot.send_message(
    chat_id=update.effective_chat.id,
    text="Hello, world!"
)
```

### 发送图片消息

photo 可以是url，也可以是本地文件

```python
async def send_image(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await context.bot.send_photo(
        chat_id=update.effective_chat.id,
        photo=open("path/to/image.jpg", "rb")
    )
```


```

## 案例