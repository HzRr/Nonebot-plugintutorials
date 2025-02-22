# 实例5 迫害群友

> *温馨提示：这是一个比较恶趣味的插件，慎用慎用*

难度：★★★

作者：[MRSlouzk](https://github.com/MRSlouzk)

### 功能

某位特定群友一发送消息，bot就会跟着他发送一条消息，消息内容随意定制且随机抽取。为了防止大乱可以通过开关调整是否打开迫害。

### 要点

- 如何知道TA发了消息
- 随机抽取消息
- 如何将设置转换为持久化数据

### 实现

##### 如何知道TA发了消息

我们在这里使用 `on_message()` 加 `Rule` 进行检测，当 `Rule` 匹配就可以说明这是TA发的消息。

```python
from nonebot import on_message
from nonebot.adapters.onebot.v11.event import GroupMessageEvent

def rule(event: GroupMessageEvent):
    return event.user_id == "QQ Number"

msg = on_message(rule=rule)
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    pass
```

> 在这里，只是检测单个 `QQ` 号。如果需要检测多个 `ID` ，则替换掉 `rule` 的判断即可。
> ```python
> qq_list = ["123456789","987654321"]
>
> def rule(event: GroupMessageEvent):
>     return event.user_id in qq_list
> ```

### 随机抽取消息

成功与 `rule` 匹配之后，开始处理消息。

我们需要在一个列表里抽取随机一条消息进行回复。

此处我们需要用到 `random` 中的 `randint` 函数进行随机数抽取。

先定义一个用来储存回复消息的 `msg_list` :

`msg_list = ["你好呀~", "你好!", "贴贴!"]`

然后我们在处理函数中使用 `randint` 抽取随机数:

```python
from random import randint

msg_list = ["你好呀~", "你好!", "贴贴!"]

msg = on_message(rule=rule)
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    count = len(msg_list)
    num = randint(0,count-1)
    pass
```

最后进行返回即可:

```python
from random import randint

msg_list = ["你好呀~", "你好!", "贴贴!"]

msg = on_message(rule=rule)
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    count = len(msg_list)
    num = randint(0,count-1)
    msg.finish(MessageSegment.reply(event.message_id) + msg_list[num])
```

> 此处的 `reply` 代表回复消息， `event.message_id` 代表消息 ID 。
> `msg_list[num]` 代表获取 `msg_list` 中的第 `num` 个变量。

#### 实现

```python
from nonebot import on_message
from nonebot.adapters.onebot.v11.event import GroupMessageEvent
from nonebot.adapters.onebot.v11.message import MessageSegment
from random import randint

qq_list = ["123456789","987654321"]
msg_list = ["你好呀~", "你好!", "贴贴!"]

def rule(event: GroupMessageEvent):
    return event.user_id in qq_list

msg = on_message(rule=rule)
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    count = len(msg_list)
    num = randint(0,count-1)
    msg.finish(MessageSegment.reply(event.message_id) + msg_list[num])
```

##### 如何将设置转换为持久化数据

我们的回复消息不可能一直写在代码里，所以我们需要一个文件专门存储我们的消息。

在这里，我们使用 `yaml` 进行储存。

> 为方便编辑，使用 `yaml` 。
> 如果您喜欢使用 `json` ，只需稍稍修改代码并将 `allow_unicode=True` 更改为 `ensure_ascii=False` 即可解决中文自动转义的问题。
> (懒得写&不会写 `.env` 读取配置 -( )

首先我们需要定义一个初始的配置文件数据。

```python
config_name = "reply_config.yaml" # 默认配置文件名
default_config = {"qq_list": ["123456789", "987654321"], "msg_list": ["你好呀~", "你好!", "贴贴!"]} # 默认配置文件内容(字典)
```

接下来，我们要先检测配置文件是否存在，如果直接读取可能导致报错。

`if path.exists(config_name) == False: pass`

假设不存在，我们则写入文件:

```python
from os import path
from yaml import dump

if path.exists(config_name) == False:
    with open(config_name, "w", encoding="utf-8") as f:
        f.write(dump(default_config, allow_unicode=True))
        f.close()
```

接下来，我们需要读取文件进行获取配置:

```python
from yaml import load, Loader

with open(config_name, "r", encoding="utf-8") as f:
    config = load(f.read(), Loader=Loader)
    f.close()

qq_list = config["qq_list"]
msg_list = config["msg_list"]
```

这样，就可以实现从文件读取配置了，而无需修改插件本身。

#### 完整代码

```python
from nonebot import on_message
from nonebot.adapters.onebot.v11.event import GroupMessageEvent
from nonebot.adapters.onebot.v11.message import MessageSegment
from nonebot.log import logger
from random import randint
from yaml import Loader, load, dump
from os import path

# 插件内部配置 #
config_name = "reply_config.yaml"
default_config = {"qq_list": ["123456789", "987654321"], "msg_list": ["你好呀~", "你好!", "贴贴!"]}
# 插件内部配置 #

# 创造新配置文件 #
if path.exists(config_name) == False:
    with open(config_name, "w", encoding="utf-8") as f:
        f.write(dump(default_config, allow_unicode=True))
        f.close()
# 创造新配置文件 #

# 读取配置 #
with open(config_name, "r", encoding="utf-8") as f:
    config = load(f.read(), Loader=Loader)
    f.close()
# 读取配置 #

# 设置配置 #
qq_list = config["qq_list"]
msg_list = config["msg_list"]
# 设置配置 #

# 规则匹配 #
def rule(event: GroupMessageEvent):
    return event.user_id in qq_list
# 规则匹配 #

# 消息处理 #
msg = on_message(rule=rule)
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    count = len(msg_list)
    num = randint(0,count-1)
    msg.finish(MessageSegment.reply(event.message_id) + msg_list[num])
# 消息处理 #
```

### **瞎写**改进版

<details>

实现了修改配置会实时更新，但是抛弃了原版 `rule` ，写在了消息处理里；

或许我真的想的会是乱七八糟的吧……

```python
from nonebot import on_message
from nonebot.adapters.onebot.v11.event import GroupMessageEvent
from nonebot.adapters.onebot.v11.message import MessageSegment
from nonebot.log import logger
from random import randint
from yaml import Loader, load, dump
from os import path

# 插件内部配置 #
config_name = "reply_config.yaml"
default_config = {"qq_list": ["123456789", "987654321"], "msg_list": ["你好呀~", "你好!", "贴贴!"]}
# 插件内部配置 #

# 创造新配置文件 #
if path.exists(config_name) == False:
    with open(config_name, "w", encoding="utf-8") as f:
        f.write(dump(default_config, allow_unicode=True))
        f.close()
# 创造新配置文件 #

# 读取配置 #
def read_config():
    with open(config_name, "r", encoding="utf-8") as f:
        config = load(f.read(), Loader=Loader)
        f.close()
    return config
# 读取配置 #

# 消息处理 #
msg = on_message()
@msg.handle()
async def msg_handle(event: GroupMessageEvent):
    # 读取配置 #
    config = read_config()
    qq_list = config["qq_list"]
    msg_list = config["msg_list"]
    # 读取配置 #
    if event.user_id in qq_list:
        count = len(msg_list)
        num = randint(0,count-1)
        msg.finish(MessageSegment.reply(event.message_id) + msg_list[num])
# 消息处理 #
```

</details>