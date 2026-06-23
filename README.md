# Susu's Castle / Html01Su

静态个人网站与演示集合，包含 Susu 主题的导航页面、专题交互页面以及一个用于课堂点名的交互页面（智能云点名系统）。该仓库由纯静态 HTML/CSS/JavaScript 构成，适合直接作为静态站点托管或在本地用静态服务器预览。

## 主要功能
- 主页（index.html）：站点入口，包含导航、验证弹窗（Cloudflare Turnstile 客户端）和到点名页面/资源的链接。
- Susu/：主题页面集合，包含导航启动页（导航栏.html）、照片/互动页面、iSpring 导出的演示等。
- HBHS/xsxx.html：独立的“智能云点名系统”页面，带有名单、抽选逻辑、倒计时、历史记录和粒子特效，使用 Tailwind CDN 与 canvas-confetti。

## 目录结构（概览）
```
index.html                  # 站点主页，包含 Turnstile 客户端集成与导航
Susu/                       # Susu 主题页面集合（导航、图片、互动、演示导出等）
  导航栏.html                # 卡片式导航启动页（你之前提供的文件）
  学生名单.html              # 学生名单 / 点名入口链接
  dy01.html                  # 爱心照片页面
  10.31/                     # iSpring 演示导出（data/ 资源 + index.html）
  CS/                        # 一系列测试/演示页面（1.1.html ... 033.html）
  联系方式/                  # 联系页面集合
  圣诞树/                    # 节日交互页面
HBHS/                       # 另一个主题/演示集合
  xsxx.html                  # 智能云点名系统（完整的点名交互页面）
README.md                   # 你正在查看的说明文件（由我创建）
```

## 如何本地查看 / 运行
这是一个静态站点，最简单的方式是用浏览器直接打开 `index.html` 或在本地启动一个静态 HTTP 服务器以得到更可靠的资源和功能支持（有些浏览器对本地文件加载外部资源或模块有限制）。

推荐方法：

- 使用 Python 内置服务器（快速）：

```bash
# 在仓库根目录运行
python -m http.server 8000
# 打开 http://localhost:8000/index.html
```

- 使用 npm 的 http-server：

```bash
npx http-server -p 8000
# 打开 http://localhost:8000/index.html
```

## 注意事项与改进建议
- Cloudflare Turnstile
  - `index.html` 中包含客户端小部件：
    ```html
    <div class="cf-turnstile" data-sitekey="..." data-callback="turnstileCallback"></div>
    <script src="https://challenges.cloudflare.com/turnstile/v0/api.js" async defer></script>
    ```
  - 客户端回调会 POST 到 `verify.php`（见 index.html 中的 fetch 调用），但仓库内**没有** `verify.php` 或其他后端验证实现。要在生产环境中使用 Turnstile，请增加一个后端以向 Cloudflare 验证令牌并返回 success 状态。
  - 简单示例（PHP）：
    ```php
    <?php
    // verify.php
    $token = $_POST['cf-turnstile-response'] ?? '';
    $secret = '你的_turnstile_secret_key';
    $resp = file_get_contents("https://challenges.cloudflare.com/turnstile/v0/siteverify", false, stream_context_create([
      'http' => [
        'method' => 'POST',
        'header' => 'Content-Type: application/x-www-form-urlencoded',
        'content' => http_build_query(['secret' => $secret, 'response' => $token])
      ]
    ]));
    echo $resp;
    ```
  - Node/Express 示例或其他语言实现也同样可用。

- 资源依赖
  - 多数页面通过 CDN 引入字体（Google Fonts）、Tailwind、FontAwesome、canvas-confetti 等。若需离线或提高可用性，可将这些资源本地化并更新相应引用。

- 数据管理
  - HBHS/xsxx.html 将学生名单写死在页面中（STUDENT_DATA 常量）。如果希望无需修改页面即可更新名单，建议将名单抽离到 `data/students.json` 并通过 fetch 加载，或提供一个小的管理后端。

- 可访问性与改进
  - 导航按钮在无 JS 的情况下可能不可用（菜单控制依赖 JS），可考虑基于纯 HTML 的可回退菜单或使用 CSS-only 下拉。
  - 增加 meta、规范化语言、完善 ARIA 标签可提升无障碍体验。

## 我已经为你做了什么
- 在仓库根创建了此 `README.md`（说明项目结构、运行方法和注意事项）。

## 我可以继续帮你做的事（选一项）
1. 添加 `verify.php` 示例或 Node 版后端并把它加入仓库，配合 Turnstile 完整演示。  
2. 把 HBHS/xsxx.html 中的 `STUDENT_DATA` 提取为 `Susu/data/students.json` 并修改页面通过 fetch 加载名单。  
3. 本地化 Google Fonts / FontAwesome（把常用资源下载到仓库并替换引用）。  

请回复你想做的下一步（例如："1" 或 "将 STUDENT_DATA 提取为 JSON"），我会立即开始并在此提交修改。