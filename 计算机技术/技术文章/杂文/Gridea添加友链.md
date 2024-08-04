找到你的主题，编辑config.json，添加如下内容

```json
    {
      "name": "friends",
      "label": "友链",
      "group": "友链",
      "type": "array",
      "value": [],
      "arrayItems": [
        { "label": "名称", "name": "siteName", "type": "input", "value": "" },
        { "label": "链接", "name": "siteLink", "type": "input", "value": "" },
        { "label": "Logo", "name": "siteLogo", "type": "picture-upload", "value": "" },
        { "label": "描述", "name": "description", "type": "textarea", "value": "" }
      ],
      "note": ""
    }
```

记得这个内容被包裹在[]中，且与上一块内容有逗号隔开

然后重启客户端，就能在主题-自定义配置的地方添加友链

**注意**：其左下角有个 + 和 - 代表添加一个新的友链和删除当前友链



接着，在templates下创建friends.ejs文件，并添加如下代码

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>友链</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        background-color: #f0f0f0;
        margin: 0;
        padding: 0;
      }

      .main-content {
        max-width: 1200px;
        margin: 0 auto;
        padding: 20px;
      }

      .post-title {
        text-align: center;
        font-size: 3em;
        margin-bottom: 30px;
        color: #333;
        font-family: 'Courier New', Courier, monospace;
        text-transform: uppercase;
        letter-spacing: 5px;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        background: linear-gradient(90deg, #ff8a00, #e52e71);
        -webkit-background-clip: text;
        color: transparent;
      }

      .friend-box {
        float: left;
        width: calc(33.333% - 20px);
        margin: 10px;
        padding: 15px;
        background-color: #fff;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        transition: transform 0.3s, box-shadow 0.3s;
      }

      .friend-box:hover {
        transform: translateY(-10px);
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
      }

      .friend-avatar {
        width: 100px;
        height: 100px;
        border-radius: 50%;
        border: 3px solid #fff;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        float: left;
        margin-right: 15px;
        transition: transform 0.3s, box-shadow 0.3s;
      }

      .friend-avatar:hover {
        transform: rotate(360deg);
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
      }

      .flink-info {
        overflow: hidden;
        line-height: 1.5;
        padding-top: 10px;
      }

      .flink-info a {
        color: #007BFF;
        text-decoration: none;
        font-size: 1.2em;
        display: block;
        margin-bottom: 5px;
      }

      .flink-info a:hover {
        text-decoration: underline;
      }

      .flink-info p {
        margin: 0;
        color: #555;
      }
    </style>
  </head>
  <body>
    <div class="main">
      <div class="main-content">
        <!-- Header -->
        <div class="header">
          <!-- Header Content Here -->
        </div>
        <div class="post-detail">
          <article class="post">
            <h2 class="post-title">有朋自远方来</h2>

            <div class="post-content-wrapper">
              <div class="post-content">
                <% if (site.customConfig.friends) { %>
                  <% site.customConfig.friends.forEach(function(friend) { %>
                    <div class="friend-box">
                      <img class="friend-avatar" src="<%= friend.siteLogo %>" alt="<%= friend.siteName %>">
                      <div class="flink-info">
                        <a href="<%= friend.siteLink %>" target="_blank"><%= friend.siteName %></a>
                        <p><%= friend.description %></p>
                      </div>
                    </div>
                  <% }); %>
                <% } %>
              </div>
            </div>
          </article>
        </div>
        <!-- Footer -->
        <div class="footer">
          <!-- Footer Content Here -->
        </div>
      </div>
    </div>
  </body>
</html>
```

然后在菜单里面创建一个友链标签，添加url地址，参考我的链接见下

https://xiaoyangst.github.io/friends/

如果你想看看主题什么样，现在就可以访问看看





参考链接：https://immmmm.com/gridea-templates-friends/