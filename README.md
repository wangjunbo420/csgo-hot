# GitHub Pages 热更新目录

这个文件夹里的内容就是要上传到 GitHub 仓库根目录的文件：

```text
github-pages-hot\
  .nojekyll
  manifest.json
  backend.zip
  README.md
```

## 第一次部署

1. 登录 GitHub，新建一个 **公开** 仓库，例如 `csgo-hot`。
2. 把本文件夹里的 `.nojekyll`、`manifest.json`、`backend.zip` 上传到仓库根目录（`main` 分支）。
3. 打开仓库 `Settings` → `Pages`：
   - Source 选 `Deploy from a branch`
   - Branch 选 `main`，目录选 `/ (root)`
   - 保存。
4. 等 1–2 分钟，浏览器打开：

```text
https://你的用户名.github.io/csgo-hot/manifest.json
```

能看到 JSON 就说明部署成功。如果 404，检查仓库是否公开、Pages 是否已启用、文件是否在根目录。

5. 客户端配置：

```text
D:\csgo饰品\csgo-item-trading-analyzer\data\update-config.json
```

```json
{
  "enabled": true,
  "base_url": "https://你的用户名.github.io/csgo-hot"
}
```

注意 `base_url` 不要以 `/` 结尾，也不用写 `manifest.json`。

6. 重启程序，启动时会检查 `base_url/manifest.json`。当前内置后端是 `2026.11.18.1309`，这个包是 `2026.11.19.1`，会比当前版本新，能触发一次更新；更新完成后重启程序生效。

## 以后每次发版

```powershell
# 前端有改动时先构建
cd E:\beifen-qwen
npm run build

# 生成热更包（版本号必须比当前版本大）
& "E:\beifen-qwen\desktop\build_hot_update.ps1" -Version 2026.11.19.2 -Notes "修复 xxx"

# 把新生成的两个文件覆盖到本文件夹
Copy-Item "E:\beifen-qwen\desktop\release\hotupdate\backend.zip" "C:\Users\ASUS\Doubao\chats\2026-11-15\new-chat\github-pages-hot\backend.zip" -Force
Copy-Item "E:\beifen-qwen\desktop\release\hotupdate\manifest.json" "C:\Users\ASUS\Doubao\chats\2026-11-15\new-chat\github-pages-hot\manifest.json" -Force
```

然后提交并推送：

```powershell
cd C:\Users\ASUS\Doubao\chats\2026-11-15\new-chat\github-pages-hot
git add .
git commit -m "hot update 2026.11.19.2"
git push
```

等 Pages 部署完成（通常 1–2 分钟），客户端下次启动会拉到新版本。

## 缓存说明

GitHub Pages 有 CDN 缓存。客户端代码已经做了两层处理：

- 请求清单时加 `?t=时间戳`，不会拿到旧 `manifest.json`；
- 下载更新包时加 `?v=版本号`，不会拿到旧 `backend.zip`。

所以不需要手动改文件名，也不需要等 CDN 缓存过期。

## 注意

- 仓库必须是公开的，否则客户端无法匿名下载。
- 这个仓库只放更新包，不要放 `data`、数据库、Cookie、日志或任何账号信息。
- GitHub Pages 在国内访问有时会慢或失败；失败时客户端会保留旧版继续运行，不会把程序弄坏。
- GitHub Pages 只能放静态文件，**不能记录用户状态**。用户状态仍然需要一台能跑 Python 的服务器，或者改用 Cloudflare Workers 之类的免费服务。
- 如果以后更新包超过 100MB，GitHub Pages 单文件限制会挡住；目前包只有 171KB，远低于限制。
