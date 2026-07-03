# CF-Workers-SubX

基于 Cloudflare Workers 的高性能订阅聚合与格式转换系统。
A high-performance subscription aggregation and format conversion system.

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://workers.cloudflare.com/deploy?url=https://github.com/bearxgf/CF-Workers-SubX)

---

## 🚀 核心功能 / Core Features
* **全格式转换**：支持 Clash, Sing-box, Surge, Loon, Base64 一键互转。
* **极速聚合**：多订阅自动抓取与去重分发，完美解决机场节点管理痛点。

---

## 📌 部署指南 / Deployment Guide

### 第一步：创建数据仓库 (KV 命名空间)
1. 登录 [Cloudflare 控制台](https://dash.cloudflare.com/)，进入 **Workers & Pages** -> **KV**。
2. 点击 **创建命名空间 (Create a namespace)**，输入名称 `sub_data` 并确认。

### 第二步：部署 Worker
1. 点击上方的 **[一键部署按钮]([https://workers.cloudflare.com/deploy?url=https://github.com/bearxgf/CF-Workers-SubX](https://workers.cloudflare.com/deploy?url=https://github.com/bearxgf/CF-Workers-SubX)** 跳转至 Cloudflare。
2. 创建 Worker 后进入 **编辑代码 (Edit Code)** 页面。
3. 清空原有内容，将本仓库的 [index.js](index.js) 代码全部粘贴进去，点击 **部署 (Deploy)**。

### 第三步：绑定配置 (关键步骤)
1. 进入 Worker **设置 (Settings)** -> **变量 (Variables)**。
2. **绑定 KV**：在 **KV 命名空间绑定** 区域点击 **添加绑定**：
   * 变量名称：`KV` (注意：必须严格全大写)
   * KV 命名空间：选择你刚才创建的 `sub_data`。
3. **设置 TOKEN**：在 **环境变量** 区域点击 **添加变量**：
   * 变量名称：`TOKEN`
   * 值：输入一个自定义密码 (例如 `mysecret888`)。
4. 点击页面下方的 **保存并部署**。

---

## 🔗 如何使用 / How to Use

假设你的 Worker 域名是 `sub-hub.yourname.workers.dev`，TOKEN 是 `mysecret888`：

* **管理后台**：访问 `https://sub-hub.yourname.workers.dev/mysecret888`
  * 在页面底部的输入框内粘贴你的机场订阅地址，点击“保存”即可生效。

* **获取转换订阅**：直接将以下链接填入你的软件中：
  * **Clash**: `https://sub-hub.yourname.workers.dev/mysecret888?clash`
  * **Sing-box**: `https://sub-hub.yourname.workers.dev/mysecret888?sb`
  * **Base64**: `https://sub-hub.yourname.workers.dev/mysecret888?b64`

---

## 🛠️ 系统逻辑架构 / System Architecture



*项目地址: https://github.com/bearxgf/CF-Workers-SubX*
