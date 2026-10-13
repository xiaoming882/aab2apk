# aab2apk

在 Android 设备上将 AAB 转换为已签名 APK，无需电脑、无需网络、无需 Termux。

Convert Android App Bundle (AAB) to signed APK on-device. No PC, no network, no Termux required.

---

## ✨ 特性

- 📱 **纯手机运行** — 不需要电脑、服务器、Termux
- 🌐 **完全离线** — 不联网、不上传、不泄露任何文件
- 🔐 **自动签名** — 导入一次密钥，永久复用
- 📦 **本地工具链** — 内置 `bundletool` / `aapt2` / `apksigner`，全部通过 D8 脱糖后在设备端执行
- 🎨 **Material You** — 动态取色，跟随壁纸
- 🌍 **中英文自适应** — 系统语言自动切换
- 🖥️ **彩色编译日志** — 8 个阶段全过程可视化，可复制、可清空、可中止
- 📋 **转换历史** — 点击直接安装，长按弹出菜单（安装 / 分享 / 删除）
- 🔒 **横屏不重载** — 通过 `configChanges` 保持状态

---

## 📸 截图

| 主页 | 转换成功 | 编译日志 |
|:---:|:---:|:---:|
| ![home](screenshots/home.jpg) | ![success](screenshots/success.jpg) | ![log](screenshots/log.jpg) |

> 上传截图后把上面路径换成实际文件

---

## 🚀 使用方式

1. **导入 `android.jar`** — 从 Android SDK 或 CodeAssist 内部提取
2. **导入签名密钥** — 支持 `JKS` / `PKCS12` / `BKS`（推荐 BKS）
3. **选择 AAB 文件** — 从任意目录选取 `.aab`
4. **点击「转换为 APK」** — 等待 8 个阶段完成
5. **点击「安装 APK」** — 直接调起系统安装

### 首次使用

- 若提示「安装未知应用」，按引导打开开关
- BKS 密钥密码 **不得超过 7 位**（BouncyCastle 限制）

---

## 🔧 技术亮点

这个项目把 Google 官方的 `bundletool` 搬进了手机 App，踩了一路的坑，最终的方案是这样的：

### 1. D8 脱糖

`bundletool` 是一个完整的 Java SE 应用，包含 `invokedynamic`（Lambda 表达式）等 Android 不支持的字节码指令。使用 `d8 --min-api 24` 对其 JAR 进行脱糖，得到纯 DEX 格式。

```bash
zip -d bundletool-all-1.15.6.jar "*.dex"
d8 --min-api 24 --output libbundletool.jar bundletool-all-1.15.6.jar