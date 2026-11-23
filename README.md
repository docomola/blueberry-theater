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
  <img src="https://img.shields.io/badge/Version-1.0.1-34D399?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/Signature-Unsigned-f59e0b?style=flat-square" alt="Signature">
  <img src="https://img.shields.io/badge/Source-Closed-1f2937?style=flat-square" alt="Source">
</p>

---

## ⬇️ 下载安装

> **本仓库不包含源代码，且发布的是「未签名」安装包 —— 必须用自己的开发者证书重签后才能安装。**
> 未签名包无法 `hdc install` 直装，这是正常现象，不是包损坏。

| 版本 | 安装包 | 大小 | 签名状态 | 下载 |
| :--- | :--- | :--- | :--- | :--- |
| **v1.0.1** ⭐ 最新 | `blueberry-theater-1.0.1-unsigned.hap` | 38.1 MB | **未签名** | [前往 Releases 下载](https://github.com/docomola/blueberry-theater/releases/latest) |
| v1.0.0 | — | — | 已下架 | 见下方说明 |

> ℹ️ **v1.0.0 的签名安装包已下架**：它使用调试证书签署，只对作者本机调试设备有效，其他设备一律报 `9568289` / `9568320`。同时 v1.0.0 的鸿果源在 2026-11-20 后的服务端环境下大概率取不到播放地址，不建议再使用。

---

### 安装流程总览

未签名包需要三步：**准备签名材料 → 重签 → 安装**。

#### 第一步：准备签名材料

需要三样东西：密钥库 `*.p12`、证书 `*.cer`、Profile `*.p7b`。

**路径 A：DevEco Studio 自动签名（最简单）**

1. 用 DevEco Studio 新建一个空工程（Empty Ability 即可）
2. 把该工程 `AppScope/app.json5` 里的 `bundleName` 改成 **`com.example.articlehub`**
3. 登录华为开发者账号 → `File → Project Structure → Signing Configs` → 勾选 **Automatically generate signature**
4. 等待生成完成后，工程下 `.ohos/config/` 目录里就有 `default_*.p12`、`default_*.cer`、`default_*.p7b`

> ⚠️ `bundleName` 必须是 `com.example.articlehub`，Profile 里的 `bundle-name` 与之不匹配会导致安装校验失败。

**路径 B：华为开发者联盟手动申请**

在 AppGallery Connect 创建应用（包名同样填 `com.example.articlehub`），申请**调试证书 + 调试 Profile**（或发布证书 + 发布 Profile），并在 Profile 里加入目标设备的 UDID。

#### 第二步：重签

用 HarmonyOS SDK 自带的 `hap-sign-tool.jar`（位于 `<SDK>/default/openharmony/toolchains/lib/`）：

```bash
java -jar hap-sign-tool.jar sign-app \
  -mode "localSign" \
  -keyAlias "你的密钥别名" \
  -keyPwd "密钥口令" \
  -keystorePwd "密钥库口令" \
  -keystoreFile "default_xxx.p12" \
  -appCertFile "default_xxx.cer" \
  -profileFile "default_xxx.p7b" \
  -inFile "blueberry-theater-1.0.1-unsigned.hap" \
  -outFile "blueberry-theater-1.0.1-signed.hap" \
  -signAlg "SHA256withECDSA"
```

成功后会得到 `blueberry-theater-1.0.1-signed.hap`。

#### 第三步：安装

```bash
# 1. 手机进入「设置 → 关于本机」连点版本号开启开发者模式
# 2. 打开「开发者选项 → USB 调试」，连接电脑并在手机上允许调试
hdc list targets
hdc install -r blueberry-theater-1.0.1-signed.hap
```

无线调试：先 `hdc tconn <手机IP>:<端口>`（端口见「无线调试」页面），再执行上面的 install。

> ⚠️ **如果之前装过别的签名版本**（包括作者签发的旧调试包），签名不一致会报 `9568289`。先 `hdc uninstall com.example.articlehub` 再装。卸载会清除收藏与播放记录。

---

## ✨ 功能特性

| 模块 | 说明 |
| :--- | :--- |
| **多源聚合** | 内置 4 个内容源（饭果 / 红果 / 星果 / 河果），首页分类行最左「源」胶囊一键切换；当前源无数据时自动按顺序兜底到下一个可用源 |
| **剧种混排** | 「剧种」维度默认**全部**，真人剧 / 漫剧 / AI 剧三路并发抓取后交错排序，首屏不会只出现单一剧种；选中某一剧种则只筛该剧种 |
| **跨剧种题材** | 题材库为三套剧种题材表的并集（28 项），混排态下自动跳过「该剧种没有这个题材」的分支，不产生无效请求 |
| **六维筛选** | `源 · 剧种 · 题材 · 设定 · 背景 · 受众 · 排序`，筛选行横向可滑，下拉面板按文字自适应宽度 |
| **沉浸光感玻璃** | 基于官方 HDS 材质的悬浮底栏（滚动自动收起 / 展开）、玻璃顶栏与自绘玻璃下拉面板，深色底 + 亮边勾勒 |
| **播放内核** | 接入 libmpv（`@mpv-ohos/mpv-arkts`）硬解播放；无 UI 起播、单击暂停 / 续播、双击全屏；控制行含上一集 / 下一集 / 倍速 / 横竖屏 / 全屏，方向支持自动跟随传感器 |
| **刷剧页** | 满宽 16:9 竖屏刷剧流，右侧动作栏提供选集面板与一键清屏（隐去全部遮挡 UI）；暂停浮层可直接切集、调倍速、转横竖屏 |
| **观看进度** | 记录每部剧看到第几集、第几秒，下次从断点续播；播放页与刷剧页共用同一份进度 |
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

### v1.0.1 — 2026-11-23

**取流稳定性修复 + 播放与刷剧页体验重做。**

- **分发方式变更**：安装包改为**未签名**（`-unsigned.hap`），需自行用开发者证书重签后安装；同时移除 v1.0.0 的调试签名包（只对作者本机设备有效）
- **取流**：新增 `video_tab` 回退通道，解决服务端自 2026-11-20 起对匿名设备返回「HTTP 200 + 空正文」导致的无法播放；失败分类修正为「网络异常不换设备，仅空正文换身份」
- **播放页**：默认无 UI 播放，起播 2.6 秒自动隐藏控制层；单击暂停 / 续播，双击全屏；控制行加入上一集、下一集、倍速、横竖屏；方向三态（自动 / 强制横屏 / 强制竖屏）；横屏画面精确居中
- **刷剧页**：视频区满宽 16:9 并与顶栏留安全距；右侧动作栏改为收藏 / 剧集 / 清屏，清屏一键隐去全部遮挡 UI；暂停浮层加入上一集 / 下一集 / 倍速 / 横竖屏；移除热度信息，集数标签显示「全 N 集 · 第 M 集」
- **观看进度记忆**（新）：记录看到第几集第几秒，下次从断点续播，播放页与刷剧页共用同一份进度
- **性能**：滑动不再加载封面图；加载转圈延迟 1200 ms 才出现，连续滑动不再闪圈
- **分类**：剧种「全部」混排改为三路并发 + 交错排序；题材库取三套剧种并集（28 项）
- **其他**：源显示名「红果」调整为「鸿果」（仅显示名）；撤除弹幕功能

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

本仓库**仅发布编译后的未签名安装包与说明文档**，不提供源代码、构建脚本、签名证书或 Profile。技术交流与问题反馈请通过 [Issues](https://github.com/docomola/blueberry-theater/issues) 提交。

<p align="center"><sub>Built for HarmonyOS NEXT · 蓝莓剧场 © 2026</sub></p>
