## 安全配置 ##
- 基础语法

    - 选项

        1、default-src: 可以为其它指令提供备选项

        2、child-src:元素加载的嵌套浏览上下文

        3、connect-src指令用于控制允许通过脚本接口加载的链接地址,影响下面的

            - <a> ping
            - Fetch
            - XMLHttpRequest
            - WebSocket
            - EventSource

        4、font-src字体的地址被阻止

        5、frame-src

            <frame>标签和<iframe>指定源的限制

        6、img-srcHTTP 指令指定图像和图标的有效来源

        7、manifest-src manifest是一个资源允许的列表

        8、media-src ```<audio>```和```<video>```元素的有效源

        9、object-src

        10、script-src ```<script>```标签

        11、style-src ```<style>```标签

        12、worker-src Worker SharedWorker ServiceWorker

    - 设置

        1、```<host-source>```域名或者ip地址表示的主机名，外加可选的URL协议名和端口号，允许在主机名和端口的位置使用通配符*

            ```http://*.example.com```

        2、 ```<scheme-source>```可以直接指定源（二进制文件，数据等）不推荐使用
        一下浏览器会特意排除blob与filesystem，可以在这里设置

            
            # data:sssssssss
            data:uris
            mediastream:uris
            blob:uris
            filesystem:uris
            
        3、'self'指定与要保护的文件所在的源，包括相同的URL scheme与端口号。必须有单引号。即同源

        4、'unsafe-inline'允许使用内联资源，script标签，javascript: URL之类的

        5、'unsafe-eval'允许使用eval()以及相似的函数来创建代码

        6、'none'不允许任何内容

        7、'nonce-<base64值>'特定使用一次性加密内联的白名单，服务器必须在每一次传输政策时生成唯一的一次性值，否则有安全问题。

        8、```<hash-source>```使用sha256, sha384, sha512编码过得内联脚本或样式

        9、strict-dynamic指定对于含有标记脚本(通过附加一个随机数或散列)的信任，应该传播到由该脚本加载的所有脚本。与此同时，任何白名单以及源表达式例如 'self' 或者 'unsafe-inline' 都会被忽略


- 隐藏Nginx版本号

    ```server_tokens off```
- Content-Security-Policy设置

    避免跨站脚本攻击,限制资源获取：限制网页当中一系列的资源获取的情况，从哪里获取，请求发到哪个地方

    一、 限制域名

        ``` 
        "default-src 'self' https://api.tongji.edu.cn http://xxx.xx.xx"
        除了以上域名，其余均无法访问，有多余往后面添加即可
        ```
    二、 限制局部

        ```
        "form-action 'self'"
        限制跳转，感觉意义不大
        ```