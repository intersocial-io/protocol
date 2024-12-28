# InterSocial Protocol
InterSocial Protocol，原名“蔚蓝档案官方百合站”，是一个去中心化社交协议。
## 1. Actor
Actor用于表达可以发送事件的客体（用户、功能接口等）。

一个Actor需要具有一个名称（name），这个名称在同一服务器下是唯一的。

Actor需要具备如下的端点：
### 1.1. Actor Endpoint
URL定义：`https://<Actor所在的服务器>/.well-known/intersocial/actor/<Actor的名称>`

形如：`https://example.com/.well-known/intersocial/actor/alice`

返回的数据类似于：
```
{
  "code":200
  "actor_type":"std:person",
  "section":{
    "std:nickname":"alice",
    "std:headimage":"https://example.com/alice.png",
    "std:diy:section":[
      ["Company", "example company"],
      ["Pronouns": "she/her"]
    ]
  }
}
```
其中，actor_type代表Actor类型，例如：
- 标准个人社交账号：`std:person`
- 标准个人博客账号：`std:blog`
- 标准组织/企业账号：`std:group`
- 标准机器人账号：`std:robot`

section代表信息条目，例如：
- 昵称：`std:nickname` \
  \- 普通string
- 头像：`std:headimage` \
  \- 图片URL或IPFS QmID
- 横幅图：`std:diy:background` \
  \- 图片URL或IPFS QmID
- QQ号：`std:contact:qq` \
  \- string类型
- 手机号：`std:contact:tel` \
  \- string类型
- 微信号：`std:contact:weixin` \
  \- 普通string
- B站UID：`std:contact:bilibili` \
  \- string类型，内容直接为UID数字，形如`12345678910`
- Matrix账号：`std:matrixid` \
  \- string类型，形如`@alice:example.com`
- 网站：`std:website` \
  \- URL
- Github：`std:github` \
  \- URL
- 首页主题色：`std:diy:color` \
  \- Hex，必须带#号，全大写或全小写
- 个人信息栏：`std:diy:section` \
  \- list，包含多个二元素的list，其中每个子list的第一项是条目名（string类型），第二项是条目值（string类型）

### 1.2. Actor Message Entrypoint
URL定义：`https://<Actor所在的服务器>/.well-known/lilipub/stream/intersocial:<Actor的名称>`

形如：`https://example.com/.well-known/lilipub/stream/intersocial:alice`

URL参数（均为可选）：
- page：页码，0为第一页；
- page_size：页面大小；

返回一个json，包含了该用户发布的事件ID（事件将在下文中定义），形式如下：
```
{
  "code":200,
  "events":["1","4","6","7","8","9","13","15"]
}
```
## 2. Message
Message代表了用户发布的互动（包括文本、表情回应等）。Message的ID应当是一个数字，在同一服务器应当唯一。
### 2.1 Message Entrypoint
URL定义：`https://<Actor所在的服务器>/.well-known/lilipub/events/<Message的ID>`

形如：`https://example.com/.well-known/lilipub/events/3155`

返回的数据类似于：
```
{
  "code":"200",
  "type":"text",
  "owned_stream":"intersocial:alice",
  "other_streams":[],
  "app":"io.lilipub.note"
  "data":{
    "io.lilipub.content":"hello world"
  }
}
```

其中，owned_stream代表actor的lilipub id，格式为`intersocial:<Actor的名称>`；

type为老版本lilipub的项，在intersocial的lilipub中不采用，固定为text；

app代表着消息的类型，定义如下：
- 动态/评论：`io.lilipub.note`
- 文章：`io.lilipub.article`
- 视频：`io.lilipub.video`
- 音频：`io.lilipub.music`
- 表情回应：`cn.yostar.ba-yuri.protocol.emojiback`

data代表着数据条目，定义如下：
- 标题：`io.lilipub.title` \
  \- string类型
- 文章正文：`io.lilipub.content` \
  \- string类型，支持markdown
- 发送时间：`io.lilipub.timestamp` \
  \- int类型，秒级别的UNIX时间戳
- 内容警告：`cn.yostar.ba-yuri.protocol.warning` \
  \- string类型
- 动态内容：`io.lilipub.content` \
  \- string类型
- 文章头图：`io.lilipub.headimg` \
  \- string类型
- 音频/视频源：`io.lilipub.stream` \
  \- URL或IPFS QmID
- 音频/视频简介：`io.lilipub.content` \
  \- string类型
- 音频/视频源类型：`io.lilipub.stream.method` \
  \- string枚举型，支持`video_file`、`hls`、`ipfs_qmid`
- 表情回应：`io.lilipub.content` \
  \- unicode emoji或图片URL或IPFS QmID
- 被回复的内容ID：`cn.yostar.ba-yuri.protocol.replyto` \
  \- 具有两个元素的list，第一个元素是string，为内容所在服务器域名；第二个元素是string，为内容ID。
- 最近修改时间：`io.lilipub.timestamp.modify` \
  \- int类型，秒级别的UNIX时间戳
