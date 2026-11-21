# 蓝莓剧场 · Blueberry Theater

<p align="center">
  <img src="assets/icon.png" width="112" alt="Blueberry Theater">
</p>

<p align="center">
  <b>HarmonyOS NEXT 原生短剧聚合播放器</b><br>
  <sub>多源切换 · 剧种混排 · 六维筛选 · 沉浸光感玻璃界面 · mpv 播放内核</sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-HarmonyOS%20NEXT-34D399?style=flat-square" alt="Platform">
  <img src="https://img.shields.io/badge/API-24%20%286.1.1%29-34D399?style=flat-square" alt="API">
  <img src="https://img.shields.io/badge/ABI-arm64--v8a-34D399?style=flat-square" alt="ABI">
  <img src="https://img.shields.io/badge/Version-1.0.0-1f2937?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/Source-Closed-1f2937?style=flat-square" alt="Source">
</p>

---

## ⬇️ 下载安装

> **本仓库不包含源代码，仅发布可安装的应用程序包（.hap）。**

| 版本 | 安装包 | 大小 | 平台要求 | 下载 |
| :--- | :--- | :--- | :--- | :--- |
| **v1.0.0** | `blueberry-theater-1.0.0.hap` | 38.4 MB | HarmonyOS NEXT 6.1.1 (API 24) / arm64 | [前往 Releases 下载](https://github.com/docomola/blueberry-theater/releases/latest) |

### 安装方式 A：hdc 命令行（推荐）

```bash
# 1. 手机进入「设置 → 关于本机」连点版本号开启开发者模式
# 2. 打开「开发者选项 → USB 调试」，连接电脑并在手机上允许调试
hdc list targets                      # 确认设备已识别
hdc install -r blueberry-theater-1.0.0.hap
```

安装完成后桌面出现「蓝莓剧场」图标，首次启动需允许网络访问。

### 安装方式 B：DevEco Studio

连接真机后，在 **Device Manager / 运行配置** 中直接把 `.hap` 拖入设备，或用「Install HAP」菜单选择文件安装。

### 安装方式 C：无线调试

同一局域网下执行 `hdc tconn <手机IP>:<端口>`（端口在「无线调试」页面查看），再执行方式 A 的 `hdc install` 命令。

> ⚠️ **签名说明**：当前安装包使用**调试签名（debug 证书）**，属于开发测试分发。若你的设备不在调试设备列表中，安装可能报签名校验失败（错误码 `9568289` / `9568320`），此时需要自行重新签名后安装。正式商店分发版本请关注后续 Release。

---

## ✨ 功能特性

| 模块 | 说明 |
| :--- | :--- |
| **多源聚合** | 内置 4 个内容源（饭果 / 红果 / 星果 / 河果），首页分类行最左「源」胶囊一键切换；当前源无数据时自动按顺序兜底到下一个可用源 |
| **剧种混排** | 「剧种」维度默认**全部**，真人剧 / 漫剧 / AI 剧三路并发抓取后交错排序，首屏不会只出现单一剧种；选中某一剧种则只筛该剧种 |
| **跨剧种题材** | 题材库为三套剧种题材表的并集（28 项），混排态下自动跳过「该剧种没有这个题材」的分支，不产生无效请求 |
| **六维筛选** | `源 · 剧种 · 题材 · 设定 · 背景 · 受众 · 排序`，筛选行横向可滑，下拉面板按文字自适应宽度 |
| **沉浸光感玻璃** | 基于官方 HDS 材质的悬浮底栏（滚动自动收起 / 展开）、玻璃顶栏与自绘玻璃下拉面板，深色底 + 亮边勾勒 |
| **播放内核** | 接入 libmpv（`@mpv-ohos/mpv-arkts`）硬解播放，上滑切集、进度记忆、源级失败自动提示换源 |
| **剧集详情** | 播放地址懒解析（针对 CMS 类片源），避免并发抓播放页触发风控 |
| **收藏与记录** | 本地收藏列表、播放进度持久化，卸载前数据保留 |
| **自建片源** | 设置页支持接入自建 CMS 片源服务器（可选，不影响内置源使用） |
| **主题** | 多种强调色与深浅外观切换 |

---

## 📱 运行环境

- **系统**：HarmonyOS NEXT 6.1.1（API 24）及以上
- **架构**：arm64-v8a（当前仅打包 64 位原生库）
- **权限**：仅申请 `ohos.permission.INTERNET`（网络访问）与 `ohos.permission.GET_NETWORK_INFO`（网络状态检测）
- **账号**：无需注册登录，不采集任何个人数据

---

## 🧱 技术栈

- **语言 / UI**：ArkTS + ArkUI 声明式开发（状态管理 V1）
- **架构**：Stage 模型，MVVM + 按特性分层（`feature/*` 为独立能力域）
- **播放**：libmpv + OpenHarmony NAPI 封装（`libmpv.so` 33.8 MB，为安装包体积主因）
- **网络**：`@ohos.net.http`，含自研 gzip 解压、签名链与设备指纹模块
- **物料**：`hm-glass` 玻璃材质组件（用于顶栏图标托板）

---

## 🗒️ 版本记录

### v1.0.0 — 2026-11-21

首个公开测试版本。

- 四个内容源接入与自动兜底
- 剧种「全部」混排 + 跨剧种题材筛选
- 六维筛选面板（宽度自适应、滚动位置复位、短表向上翻转不裁切）
- 沉浸光感玻璃底栏 / 顶栏
- mpv 播放内核，上滑切集、进度记忆
- 收藏、播放记录、主题切换、自建片源

---

## ⚖️ 免责声明

1. 本项目**不存储、不上传、不分发任何影视内容**。所有片名、封面与视频流均由第三方公开接口在用户设备端按需实时获取并直接播放。
2. 本项目仅为 **HarmonyOS NEXT 客户端技术学习与交流** 用途，不得用于任何商业用途或二次分发。
3. 若相关权利人认为本项目的技术实现侵犯其权益，请通过 Issue 联系，作者将在核实后立即停止分发。
4. 使用本应用产生的一切后果由使用者自行承担。

---

## 🔒 关于源代码

本仓库**仅发布编译后的安装包与说明文档**，不提供源代码、构建脚本或签名材料。技术交流与问题反馈请通过 [Issues](https://github.com/docomola/blueberry-theater/issues) 提交。

<p align="center"><sub>Built for HarmonyOS NEXT · 蓝莓剧场 © 2026</sub></p>
