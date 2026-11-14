# HaiLens 门面页 · 上线说明

这个文件夹可以直接拖到 Cloudflare Pages，不需要 Git、不需要命令行。

**本地预览**：双击 `index.html` 就能看（用的是相对路径，不联网也正常）。

---

## 一、上线三步

### 1. 建 Pages 项目

Cloudflare 后台 → Workers & Pages → Create → Pages → **Upload assets**（不是连 Git）。

项目名填 `hailens`，上传这个 `site` 文件夹里的**所有内容**（不是上传 site 文件夹本身）。

上传完会给你一个 `hailens.pages.dev` 的地址，先打开看一眼。

### 2. 加自定义域

项目 → Custom domains → Set up a custom domain：

1. 先加 `hailenslab.com`
2. 再加 `www.hailenslab.com`

**让它自动写记录，不要手动加 CNAME。** Cloudflare 会自己处理。

如果根域上还留着以前那两条 `192.0.2.1` 的占位 A 记录，**先删掉**，否则会冲突。

### 3. www 跳根域

Rules → Redirect Rules → 新建：

- 如果 `Hostname` 等于 `www.hailenslab.com`
- 则 301 到 `https://hailenslab.com` + 保留路径

---

## 二、表单：只改一行

打开 `index.html`，搜索 `REPLACE_ME`：

```html
<form class="form" id="lead-form" action="https://formspree.io/f/REPLACE_ME" method="POST">
```

换成 Formspree 给你的 endpoint（形如 `https://formspree.io/f/xayzbkqw`）。

**没换也能用**：页面里有一段兜底脚本，检测到还是 `REPLACE_ME` 时，提交会改成打开邮件客户端、把内容填好发给 `hji@hailenslab.com`。所以不会出现「点了没反应」。

Formspree 里要把收件邮箱设成 `hji@hailenslab.com`，并打开垃圾过滤。

---

## 三、还缺什么

### A. 只能你操作的（我进不去你的账号）

| # | 事项 | 说明 |
| --- | --- | --- |
| 1 | Cloudflare 建 Pages 项目 + 上传 | 约 10 分钟 |
| 2 | 加两个自定义域 | 约 5 分钟，等 DNS 生效 |
| 3 | 删旧的占位 A 记录 | 如果有 |
| 4 | Formspree 注册 + 拿 endpoint | 约 10 分钟 |

**动 DNS 之前先截图存一份现有记录。** 尤其别碰 MX、SPF、DKIM、DMARC 这四条——那是 Zoho 的命根子。

### B. 要你拍板的（会影响页面内容）

| # | 问题 | 现在页面怎么写的 |
| --- | --- | --- |
| 1 | 中文名用不用「海镜」 | 用了，出现在 title 和页脚。不用就全局替换成 HaiLens |
| 2 | 邮箱是不是 `hji@hailenslab.com` | 是，出现在顶栏、表单兜底、页脚、错误提示 |
| 3 | 「每周最多接 10 份」还留不留 | 留了，在表单上方。守不住会掉信任 |
| 4 | 要不要放一份脱敏样品 | 没放。放了信任度↑，方法会暴露给同行 |

### C. 还没做，但随时能加

- **英文版**（`/en/`）：如果客户里有海外合作方要看
- **样品落地页**：把一份脱敏体检做成网页，比 PDF 更容易被转发
- **访问统计**：Cloudflare Web Analytics，免费、无 cookie、不用弹窗

---

## 四、上线后必测五条

1. 手机打开 `hailenslab.com`，看有没有横向滚动、字有没有被切
2. 提交一条真数据（用你自己的邮箱），看 Formspree 后台有没有
3. 看 Zoho **收件箱和垃圾箱两处**——第一次很可能进垃圾箱
4. 如果进了垃圾箱，把发件地址加白名单，再测一次
5. 隔 24 小时再测一次（新发件人可能被临时拦）

**第 3 条不过，等于表单没通。** 别跳过。

---

## 五、文件说明

```
site/
├── index.html                首页（唯一要改的是 REPLACE_ME）
├── thanks.html               提交成功页（noindex，不进搜索）
├── 404.html                  找不到页
├── robots.txt                允许收录，屏蔽 /thanks.html
├── _headers                  安全头（防 iframe 嵌套、禁 MIME 嗅探）
└── assets/
    ├── style.css             全部样式，改主题色只改顶部变量
    ├── favicon.svg           浏览器标签图标
    ├── apple-touch-icon.png  存到手机桌面的图标
    └── og.png                微信／LinkedIn 分享时的预览卡（1200×630）
```

---

## 六、已知取舍

- **页面不带价格**。第一份免费，之后回信给价——这是 11-12 定的。
- **表单只收集，不自动交付**。提交后是你人工做，48 小时内回信。页面已写明，不会让人以为点完就能下载。
- **没有 Cookie 提示、没有统计脚本**。所以现在你无法知道有多少人来了没留资。想要这个数据，加 Cloudflare Web Analytics（无 cookie，不需要提示条）。
- **中文名「海镜」是我提的**，不是你原有的资产。不用就删，版面不塌。
