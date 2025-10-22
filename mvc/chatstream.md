# 基于bboss mvc和Deepseek实现智能问答教程

## 1. 功能概述

本文件介绍基于bboss MVC框架和Deepseek大模型的实现流式智能问答功能，支持实时流式响应展示，提供良好的用户体验。

本文采用的技术：

- bboss mvc：支持flux/mono 两种restful服务
- bboss httpproxy：基于httpclient5和reactor core实现，支持流式服务调用
- Deepseek模型服务：基于Deepseek官方云服务
- 前端SSE：html5+sse流式交互调用

本文案例工程源码地址：基于gradle管理

https://gitee.com/bboss/bbootdemo

将源码下载后，导入idea进行服务开发和调试运行，gradle工程环境搭建参考文档：

https://esdoc.bbossgroups.com/#/bboss-build

## 2. 核心组件

### 2.1 后端Flux控制器

https://gitee.com/bboss/bbootdemo/blob/master/src/main/java/org/frameworkset/web/react/ReactorController.java

```java
public class ReactorController implements InitializingBean
```


主要功能方法：
- `deepseekBackuppressSession(@RequestBody Map<String,Object> questions)`: 背压及带记忆窗口多轮会话Deepseek问答请求服务
- `afterPropertiesSet()`: 初始化Deepseek大模型HTTP连接池配置,基于bboss-http5

### 2.2 后端Mono控制器

https://gitee.com/bboss/bbootdemo/blob/master/src/main/java/org/frameworkset/web/react/MonoController.java

### 2.3 前端页面

实现流式问答交互的前端界面，包含：
- 用户输入框
- 发送/取消按钮
- 实时消息展示区域
- Markdown格式支持

前端清单

[chatpost.html](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/chatpost.html)  简单文本stream 响应案例

[chatBackuppress.html](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/chatBackuppress.html) 背压stream案例

[chatBackuppressSession.html](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/chatBackuppressSession.html)  多轮会话记忆的背压stream案例

[chatpostServerEvent.html](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/chatpostServerEvent.html)  基于ServerEvent的简单stream案例

[mono.html](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/mono.html) 基于mono的响应是案例

## 3. 配置说明

### 3.1 应用配置  

[application.properties](https://gitee.com/bboss/bbootdemo/blob/master/src/main/resources/application.properties)

```properties
# Deepseek API配置
http.hosts=https://api.deepseek.com
http.apiKeyId = sk-9fca957909d94ed5a9f7852be1aefa2a

# HTTP连接池配置
http.timeoutConnection = 5000000
http.timeoutSocket = 50000
http.maxTotal = 200
http.defaultMaxPerRoute = 200
```

可从Deepseek官方申请apiKey：https://platform.deepseek.com/api_keys

在`application.properties`中增加服务Webroot目录（在idea开发调试时需要配置，打包发布运行时需注释掉web.docBase配置）、启动端口以及应用上下文路径配置：

```properties
web.docBase=C:/workspace/bbossgroups/bboss-demos/bbootdemo/WebRoot
web.contextPath=demoproject
web.port=80
```



### 3.2 Mvc路由配置 

 [bboss-socket.xml](https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/WEB-INF/conf/bboss-socket.xml)

```xml
<property name="/reactor/*.api" class="org.frameworkset.web.react.ReactorController"/>
<property name="/reactor/mono/*.api" class="org.frameworkset.web.react.MonoController"/>
```

将`/reactor/*.api`路径映射到`ReactorController`控制器。

将/reactor/mono/*.api路径映射到MonoController控制器

## 4. 使用流程

### 4.1 后端处理流程

1. 系统启动时，`ReactorController`通过`afterPropertiesSet()`方法加载配置并启动HTTP连接池
2. 用户从前端发送问题到`/reactor/deepseekChatServerEventBackuppress.api`接口
3. 控制器接收问题内容，构造Deepseek API请求参数
4. 通过`HttpRequestProxy.streamChatCompletion()`方法发送流式请求到Deepseek API
5. 将响应以流式方式返回给前端

### 4.2 前端交互流程

1. 用户在输入框输入问题，点击发送或按回车键
2. 前端通过Fetch API发送POST请求到后端接口
3. 显示"正在思考中..."提示
4. 接收后端流式响应数据
5. 实时解析并展示Markdown格式的回答内容

## 5. API接口说明

### 5.1 Deepseek问答接口

示例：背压案例接口

- **URL**: `/reactor/deepseekChatServerEventBackuppress.api`

- **方法**: POST

- **请求格式**: JSON

- **请求参数**:
  ```json
  {
    "message": "用户问题内容"
  }
  ```

- **响应格式**: 流式文本数据

其他接口访问源码文件了解

## 6. 核心实现代码案例

### 6.1 后端核心实现

#### Flux Deepseek问答接口

具体实现以实际代码为准--背压案例，更多细节访问源码了解：https://gitee.com/bboss/bbootdemo/blob/master/src/main/java/org/frameworkset/web/react/ReactorController.java

```java
 public Flux<List<ServerEvent>> deepseekChatServerEventBackuppress(@RequestBody Map<String,Object> questions) {
        String message = (String)questions.get("message");
        Map<String, Object> requestMap = new HashMap<>();
        requestMap.put("model", "deepseek-chat");

        List<Map<String, String>> messages = new ArrayList<>();
        Map<String, String> userMessage = new HashMap<>();
        userMessage.put("role", "user");
        userMessage.put("content", message);
        messages.add(userMessage);

        requestMap.put("messages", messages);
        requestMap.put("stream", true);
        requestMap.put("max_tokens", 2048);
        requestMap.put("temperature", 0.7);
        Flux<ServerEvent> flux = HttpRequestProxy.streamChatCompletionEvent("/chat/completions",requestMap);

        return flux.doOnNext(chunk -> {
            if(!chunk.isDone()) {
                logger.info(chunk.getData()); //调试打印Deepseek回复片段
            }
        }).limitRate(5) // 限制请求速率
                .buffer(3) ;   // 每3个元素缓冲一次;


    }
```


#### Deepseek服务数据源初始化

```java
@Override
public void afterPropertiesSet() throws Exception {
    //加载配置文件，启动负载均衡器
    HttpRequestProxy.startHttpPools("application.properties");
}
```

#### Mono后端接口

更多内容，访问源码了解：https://gitee.com/bboss/bbootdemo/blob/master/src/main/java/org/frameworkset/web/react/MonoController.java

```java
public Mono<User> getUser(String id){
    return userService.findUserById(id);
}
```

### 6.3 前端核心实现

#### 背压Flux服务JavaScript客户端类

更多内容，访问页面源码了解：https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/chatBackuppress.html

```javascript
class ReactiveChatClient {
        constructor() {
            this.eventSource = null;
            this.abortController = null;
            this.isStreaming = false;

            this.chatContainer = document.getElementById('chat-container');
            this.questionInput = document.getElementById('question-input');
            this.sendBtn = document.getElementById('send-btn');
            this.cancelBtn = document.getElementById('cancel-btn');

            // 配置marked选项
            marked.setOptions({
                breaks: true,
                gfm: true
            });

            this.initEventListeners();
        }

        initEventListeners() {
            this.sendBtn.addEventListener('click', () => this.sendMessage());
            this.cancelBtn.addEventListener('click', () => this.cancelStream());
            this.questionInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    this.sendMessage();
                }
            });
        }

        sendMessage() {
            const message = this.questionInput.value.trim();
            if (!message || this.isStreaming) return;

            // 显示用户消息
            this.displayMessage(message, 'user');
            this.questionInput.value = '';

            // 显示机器人正在输入指示器
            const indicatorId = this.showTypingIndicator();

            // 修改为POST请求URL
            const url = `/metrics/reactor/deepseekChatServerEventBackuppress.api`;

            // 创建AbortController用于取消请求
            this.abortController = new AbortController();
            this.isStreaming = true;

            // 使用fetch API进行POST流式请求
            fetch(url, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ message: message }),
                signal: this.abortController.signal
            })
                .then(response => {
                    // 移除正在输入指示器
                    this.removeTypingIndicator(indicatorId);

                    if (!response.ok) {
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }

                    // 获取响应流
                    const reader = response.body.getReader();
                    const decoder = new TextDecoder();
                    let botMessageElement = this.createBotMessageElement();
                    let accumulatedContent = '';
                    // 在ReactiveChatClient类中添加行缓冲区属性
// 添加处理单行数据的方法

                    let lineBuffer = '';
                    // 修改readStream函数实现逐行处理
                    const readStream = () => {
                        reader.read().then(({ done, value }) => {
                            if (done) {
                                // 处理缓冲区中剩余的数据
                                if (lineBuffer.trim()) {
                                    try {
                                        const jsonData = JSON.parse(lineBuffer.trim());
                                        //jsonData为数组类型，遍历其中元素
                                        for (let i = 0; i < jsonData.length; i++) {
                                            //处理jsonData中的数据
                                            const line = jsonData[i];
                                            if (line.data) {
                                                accumulatedContent += line.data;
                                                this.updateBotMessage(botMessageElement, accumulatedContent);
                                            }
                                        }
                                        
                                        
                                    } catch (e) {
                                        // 如果不是JSON格式，作为普通文本处理
                                        accumulatedContent += lineBuffer;
                                        this.updateBotMessage(botMessageElement, accumulatedContent);
                                    }
                                }
                                this.isStreaming = false;
                                return;
                            }

                            // 解码当前chunk
                            const chunk = decoder.decode(value, { stream: true });

                            // 将新数据添加到行缓冲区
                            if(lineBuffer) {
                                lineBuffer += chunk;
                            }
                            else{
                                lineBuffer = chunk;
                            }

                            // 按行分割数据
                            const lines = lineBuffer.split('\n');

                            // 保留最后一行（可能是不完整的）在缓冲区中
                            lineBuffer = lines.pop() || '';

                            // 处理完整的行
                            lines.forEach(line => {
                                if (line.trim()) {
                                    try {
                                        const jsonData = JSON.parse(line.trim());
                                        //jsonData为数组类型，遍历其中元素
                                        for (let i = 0; i < jsonData.length; i++) {
                                            //处理jsonData中的数据
                                            const lineData = jsonData[i];
                                            if (lineData.data) {
                                                accumulatedContent += lineData.data;
                                                this.updateBotMessage(botMessageElement, accumulatedContent);
                                            }
                                        }                                        
                                    } catch (e) {
                                        // 如果不是JSON格式，作为普通文本处理
                                        accumulatedContent += line;
                                        this.updateBotMessage(botMessageElement, accumulatedContent);
                                    }
                                }
                            });

                            // 继续读取
                            readStream();
                        }).catch(error => {
                            if (error.name !== 'AbortError') {
                                console.error('Stream reading error:', error);
                                this.displayErrorMessage('接收数据时出错');
                            }
                            this.isStreaming = false;
                        });
                    };

                    readStream();
                })
                .catch(error => {
                    this.removeTypingIndicator(indicatorId);
                    this.isStreaming = false;

                    if (error.name === 'AbortError') {
                        this.displayErrorMessage('请求已取消');
                    } else {
                        console.error('Fetch error:', error);
                        this.displayErrorMessage('发送请求时出错: ' + error.message);
                    }
                });
        }

        cancelStream() {
            if (this.isStreaming && this.abortController) {
                this.abortController.abort();
                this.isStreaming = false;
                this.displayErrorMessage('请求已被用户取消');
            }
        }

        displayMessage(content, sender) {
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${sender}-message`;

            if (sender === 'bot') {
                // 对于机器人消息，解析Markdown
                messageDiv.innerHTML = marked.parse(content);
            } else {
                // 用户消息保持纯文本
                messageDiv.textContent = content;
            }

            this.chatContainer.appendChild(messageDiv);
            this.scrollToBottom();
        }

        createBotMessageElement() {
            const messageDiv = document.createElement('div');
            messageDiv.className = 'message bot-message';
            this.chatContainer.appendChild(messageDiv);
            this.scrollToBottom();
            return messageDiv;
        }

        updateBotMessage(element, content) {
            // 实时更新Markdown内容
            element.innerHTML = marked.parse(content);
            this.scrollToBottom();
        }

        showTypingIndicator() {
            const indicator = document.createElement('div');
            indicator.className = 'message bot-message typing-indicator';
            indicator.id = 'typing-' + Date.now();
            indicator.textContent = '正在思考中...';
            this.chatContainer.appendChild(indicator);
            this.scrollToBottom();
            return indicator.id;
        }

        removeTypingIndicator(id) {
            const indicator = document.getElementById(id);
            if (indicator) {
                indicator.remove();
            }
        }

        displayErrorMessage(message) {
            const errorDiv = document.createElement('div');
            errorDiv.className = 'message bot-message';
            errorDiv.style.color = 'red';
            errorDiv.textContent = message;
            this.chatContainer.appendChild(errorDiv);
            this.scrollToBottom();
        }

        scrollToBottom() {
            this.chatContainer.scrollTop = this.chatContainer.scrollHeight;
        }
    }

    // 初始化应用
    document.addEventListener('DOMContentLoaded', () => {
        new ReactiveChatClient();
    });
```

#### Mono服务Javascript客户端类

直接访问页面了解：https://gitee.com/bboss/bbootdemo/blob/master/WebRoot/mono.html

## 7. 技术特点

1. **流式响应**: 使用Reactor `Flux`和Mono实现流式数据传输，支持实时展示回答内容
2. **Markdown支持**: 前端集成`marked.js`库，支持Markdown格式内容渲染
3. **中断控制**: 支持用户主动取消正在进行的问答请求
4. **连接池管理**: 后端使用HTTP连接池管理与Deepseek API的连接
5. **错误处理**: 完善的异常处理和用户提示机制

## 8. 部署与运行

部署运行步骤：

1. 配置`application.properties`中的Deepseek API密钥
2. 确保网络可以访问`https://api.deepseek.com`
3. 启动应用后访问`http://localhost/demoproject/chatBackuppressSession.html`
4. 在输入框中输入问题，即可与Deepseek模型进行问答交互

### 8.1 开发调试

在idea中启动服务：直接运行或者Debug test目录下的org.frameworkset.test.Main类即可

![image-20251019215348293](../images/stream/idea-main.png)

启动Main类后，打开浏览器输入地址，体验问答功能：

http://127.0.0.1/demoproject/chatpost.html

![](..\images\stream\result.png)

### 8.2 打包发布

运行源码工程目录下的指令：

windows环境：release.bat

linux环境：release.sh

![image-20251019231951787](..\images\stream\release.png)

发布成功后，将在bbootdemo\build\distributions目录下生成部署包：bbootdemo.zip

上传服务器后，解压并运行指令启动服务：restart或者startup

![](..\images\stream\run.png)

## 9. 扩展建议

1. 可增加对话历史记录功能
2. 可支持多种大模型切换
3. 可添加用户认证和权限控制
4. 可优化前端UI界面和交互体验