# 调试工具 · 桌面版站点

「调试工具」Windows 桌面客户端的**发布站点**：Gitee Pages / GitHub Pages 直接托管页面与当前安装包，
软件内置的在线更新也是读这里的 `version.json`。

## 下载

| 渠道 | 地址 | 说明 |
|---|---|---|
| Gitee Release（首选） | https://gitee.com/ooohhhxxx/tool-site-web/releases/download/latest/UltimateToolWeb-Setup.exe | 国内速度最好 |
| 本站备用（GitHub Pages） | https://ooohhhxxx.github.io/tool-site-web/UltimateToolWeb-Setup.exe | gitee 打不开时用，同一个安装包 |
| 站点首页 | https://ooohhhxxx.github.io/tool-site-web/ | 说明、更新日志、操作手册 |

安装包 SHA256 见 [`version.json`](version.json)，下载后可自行校验：

```powershell
Get-FileHash .\UltimateToolWeb-Setup.exe -Algorithm SHA256
```

## 仓库结构

```
index.html                     站点首页（产品说明 + 更新日志）
SOP.html                       操作手册（SOP-UTW-001）
version.json                   版本清单：版本号 / 大小 / SHA256 / 下载地址 / 更新说明
UltimateToolWeb-Setup.exe      当前版本的安装包（作为 Pages 备用下载）
favicon.ico  .nojekyll         静态资源
.github/workflows/pages.yml    Pages 部署：校验 version.json ↔ 安装包 → 发布
```

## 关于这个仓库的两条约定

1. **仓库里只保留当前版本的一个安装包。** 发新版直接覆盖同名文件，不要累积历史包。
   历史上曾把每一版都提交进来，仓库一度涨到 800 MB 以上，附件配额也被撑满，教训在这里。
2. **`version.json` 是唯一可信来源。** 页面文案、安装包大小、SHA256 三者必须一致，
   `pages.yml` 会在部署前硬校验，对不上直接拒绝发布——宁可不发，也不发错包。

## 发布流程（维护者）

在客户端源码仓库执行：

```powershell
bump_version.py 1.0.3            # 同步 7 个文件的版本号
npm run build                    # 前端构建到 backend\static
build_web.bat                    # PyInstaller + Inno Setup -> dist_out\
publish_gitee.py --prune 5 --mirror-pages
```

`--prune 5` 发布后只保留最近 5 个 Release（Gitee 附件配额 1 GB，不清理迟早撑满）；
`--mirror-pages` 会把安装包复制到本仓库根目录，供 Pages 备用下载。

然后在本仓库更新 `index.html` / `SOP.html` / `version.json` 三处版本信息，提交并推送：

```powershell
git add -A && git commit -m "release: v1.0.3 …"
git push gitee main && git push github main
```

推送后 GitHub Actions 会自动校验并部署 Pages。线上验收：

```powershell
check_release.py --exe ..\UltimateToolWeb\dist_out\UltimateToolWeb-Setup.exe
```

## 历史版本

Release 只保留最近几版（附件配额有限），需要更早的版本请从客户端源码仓库的 git 历史里取。
