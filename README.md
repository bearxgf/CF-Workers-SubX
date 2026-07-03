# ⚙ CF-Workers-SubX — 自建汇聚订阅

基于 Cloudflare Workers 的高性能**订阅聚合与格式转换系统**，可将多个节点和订阅合并为单一链接，支持自动适配与自定义分流，简化订阅管理。

> 本项目 Fork 自 [cmliu/CF-Workers-SUB](https://github.com/cmliu/CF-Workers-SUB)，感谢原作者。

---

## ⚠️ 重要提示

> [!CAUTION]
> **汇聚订阅非 base64 格式时**，会自动生成一个**有效期为 24 小时的临时订阅**，并提交给**订阅转换后端**来完成订阅转换，可避免你的汇聚订阅地址泄露。

> [!WARNING]
> **汇聚订阅非 base64 格式时**，如果你的节点数量**非常庞大**，订阅转换后端将需要较长时间才能完成订阅转换，这会导致部分客户端提示订阅超时（说白了就是**节点太多容易导致 Clash 订阅超时**）！
>
> 建议适当删减订阅节点数量，提高订阅转换效率。

---

## 🚀 快速部署

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://workers.cloudflare.com/deploy?url=https://github.com/bearxgf/CF-Workers-SubX)

---

## 🛠 功能特点

1. **节点链接自动转换** — 将节点链接自动转换为 Base64 格式的订阅链接
2. **多订阅汇聚** — 将多个订阅（例如不同机场）合并成一个订阅，一个订阅地址获取所有节点
3. **全格式自动适配** — 依托订阅转换服务，自动转换为不同客户端所需格式，支持：
   - Clash / Clash Meta / Mihomo
   - Sing-box
   - Surge
   - Quantumult X
   - Loon
   - V2RayN / Base64
4. **专属代理分流规则** — 自定义分流规则，实现个性化分流模式
5. **Telegram 通知** — 支持订阅访问通知、异常访问报警
6. **访客订阅** — 支持设置独立的访客 TOKEN，分享订阅更安全

---

## 📦 Pages 部署方法（推荐）

<details>
<summary><code><strong>「 点击展开 GitHub Pages 部署文字教程 」</strong></code></summary>

### 1. Fork 本仓库
在 GitHub 上 Fork 本项目，并点上 ⭐ Star！

### 2. 部署到 Cloudflare Pages
- 登录 [Cloudflare 控制台](https://dash.cloudflare.com/)，进入 **Workers & Pages**
- 点击 **创建** → **Pages** → **连接到 Git**
- 选择你 Fork 的 `CF-Workers-SubX` 项目，点击 **开始设置**
- 构建设置保持默认即可，点击 **保存并部署**

### 3. 绑定自定义域（可选）
- 在 Pages 控制台的 **自定义域** 选项卡中，点击 **设置自定义域**
- 填入你的自定义次级域名（不要使用根域名），例如：
  - 你的域名是 `fuck.cloudns.biz`，则添加 `sub.fuck.cloudns.biz`
- 按 Cloudflare 要求，在你的域名 DNS 服务商添加 CNAME 记录，指向 `CF-Workers-SubX.pages.dev`，然后点击 **激活域**

### 4. 配置环境变量
在 Pages 控制台 **设置** → **环境变量** 中添加：

| 变量名 | 示例值 | 必填 | 说明 |
|--------|--------|------|------|
| `TOKEN` | `auto` | ✅ | 订阅访问入口路径，例如设为 `auto` 则订阅地址为 `/auto` |

### 5. 绑定 KV 命名空间
1. 进入 **Workers & Pages** → **KV**，点击 **创建命名空间**，名称填写 `sub_data`
2. 回到 Pages 项目 **设置** → **绑定** → **KV 命名空间**，点击 **添加绑定**：
   - 变量名称：`KV`（必须全大写）
   - KV 命名空间：选择刚创建的 `sub_data`
3. 点击 **保存**

### 6. 添加你的节点和订阅
1. 访问 `https://你的域名/auto`（TOKEN 为 `auto` 时）
2. 在页面中输入你的自建节点链接和机场订阅链接，每行一个，例如：
   ```
   vless://your-uuid@your-server.com:443?encryption=none&security=tls&type=ws&host=your-host&path=%2F#节点名称
   vmess://base64-encoded-config
   https://sub.example.com/auto
   https://another-sub.pages.dev
   ```
3. 点击保存即可生效

</details>

---

## 🛠️ Workers 部署方法

<details>
<summary><code><strong>「 点击展开 Workers 部署文字教程 」</strong></code></summary>

### 1. 部署 Worker
- 在 Cloudflare Workers 控制台中创建一个新的 Worker
- 将本仓库 [index.js](index.js) 的内容粘贴到 Worker 编辑器
- 点击 **部署**

### 2. 配置订阅入口
假设你的 Worker 域名为 `sub.yourname.workers.dev`：
- 通过环境变量 `TOKEN` 设置你的专属订阅入口（默认值：`auto`）
- 你的订阅地址为：
  ```
  https://sub.yourname.workers.dev/auto
  或
  https://sub.yourname.workers.dev/?token=auto
  ```

### 3. 绑定 KV 命名空间
1. 进入 **Workers & Pages** → **KV**，创建命名空间 `sub_data`
2. 在 Worker **设置** → **绑定** → **KV 命名空间** 中点击 **添加绑定**：
   - 变量名称：`KV`（必须全大写）
   - KV 命名空间：选择 `sub_data`

### 4. 添加节点和订阅链接
1. 访问 `https://sub.yourname.workers.dev/auto`
2. 在页面中输入你的节点和订阅链接，每行一个，例如：
   ```
   vless://b7a392e2-...@cf.example.xyz:443?encryption=none&security=tls&type=ws&host=...&path=%2F...#节点名称
   vmess://ew0KICAid...
   https://sub.xf.free.hr/auto
   https://hy2sub.pages.dev
   ```
3. 保存后即可使用

</details>

---

## 🔗 使用方式

假设你的 Worker / Pages 域名为 `sub.example.com`，TOKEN 设为 `auto`：

### 管理后台
```
https://sub.example.com/auto
```
在页面输入框内粘贴你的机场订阅地址或自建节点链接，保存即可生效。

### 获取订阅

| 客户端 | 订阅地址 |
|--------|----------|
| **V2RayN / Base64（默认）** | `https://sub.example.com/auto` |
| **Clash / Clash Meta** | `https://sub.example.com/auto?clash` |
| **Sing-box** | `https://sub.example.com/auto?sb` |
| **Surge** | `https://sub.example.com/auto?surge` |
| **Quantumult X** | `https://sub.example.com/auto?quanx` |
| **Loon** | `https://sub.example.com/auto?loon` |

系统会根据你客户端的 User-Agent 自动识别格式，无需手动加参数即可获得对应格式的订阅。

---

## 📋 全部环境变量说明

| 变量名 | 示例 | 必填 | 备注 |
|--------|------|------|------|
| `TOKEN` | `auto` | ✅ | 订阅配置的访问路径，例如设为 `auto` 则订阅地址为 `/auto` |
| `GUESTTOKEN` / `GUEST` | `guest123` | ❌ | 访客订阅 TOKEN，用于分享给他人使用，格式：`/sub?token=guest123` |
| `LINK` | 节点/订阅链接（换行分隔） | ❌ | 直接配置节点与订阅链接（绑定 KV 后此变量不再使用） |
| `LINKSUB` | 订阅链接（换行分隔） | ❌ | 额外的机场订阅链接（绑定 KV 后此变量不再使用） |
| `TGTOKEN` | `6894123456:XXXXXXXXXX...` | ❌ | Telegram Bot Token，用于发送通知 |
| `TGID` | `6946912345` | ❌ | 接收 Telegram 通知的账户数字 ID |
| `TG` | `0` 或 `1` | ❌ | 是否开启 Telegram 通知（`1` = 开启） |
| `SUBNAME` | `CF-Workers-SubX` | ❌ | 订阅名称，会显示在客户端中 |
| `SUBAPI` | `SUBAPI.cmliussss.net` | ❌ | 订阅转换后端地址 |
| `SUBCONFIG` | `https://raw.github.../ACL4SSR_Online_MultiCountry.ini` | ❌ | 订阅转换配置文件地址 |
| `SUBUPTIME` | `6` | ❌ | 客户端订阅更新间隔（小时） |
| `URL302` | `https://example.com` | ❌ | 非授权访问时的 302 跳转地址 |
| `URL` | `https://example.com` | ❌ | 非授权访问时代理的目标地址 |
| `WARP` | 节点链接 | ❌ | 需要额外追加的 WARP 节点 |

---

## ⚠️ 注意事项

- **TGTOKEN** 和 **TGID** 需要先在 Telegram 注册获取。TGTOKEN 是通过 [@BotFather](https://t.me/BotFather) 创建的 Bot 凭证，TGID 是接收通知的用户或群组数字 ID（可通过 [@userinfobot](https://t.me/userinfobot) 查询）
- KV 命名空间绑定时，变量名必须是 **`KV`**（全大写），否则无法识别
- 订阅转换为非 Base64 格式时，会生成临时订阅地址提交给转换后端，确保你的汇聚订阅地址不会泄露
- 节点数量过多时建议分批或精简，避免订阅转换超时

---

## 🙏 致谢

- 原作者 [cmliu/CF-Workers-SUB](https://github.com/cmliu/CF-Workers-SUB)
- [Alice Networks LTD](https://alicenetworks.net/) — 提供订阅转换服务
- [ACL4SSR](https://github.com/ACL4SSR/ACL4SSR) — 分流规则配置
- [mianayang](https://github.com/mianayang) — 原始参考代码
- [肥羊](https://sub.v1.mk/)

---

*项目地址: https://github.com/bearxgf/CF-Workers-SubX*
