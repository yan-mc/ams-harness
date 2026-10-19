# branding/ —— 品牌单一事实来源

本目录保存 AMS 品牌与身份参数（**数据**，不含代码）。M0.1 只建骨架，**尚未修改任何上游源码**。

## 文件

| 文件 | 内容 |
|---|---|
| brand.json | 产品名/appId/协议/安装器/签名/更新源/内核裁剪与隐私开关/图标路径 |
| assets/ | 图标等二进制占位（待设计提供：icon.png、icon-windows.ico、tray-windows.ico、installer-sidebar.bmp） |

## 上游对应改动点（M1 执行，详见方案 §5.2）

| 项 | 上游位置 |
|---|---|
| productName / artifactName / protocols | apps/desktop/scripts/electron-builder-config.mjs |
| appId 默认值 | apps/desktop/scripts/desktop-release-environment.mjs、desktop-package-environment.mjs |
| 窗口标题/菜单/关于/欢迎文案 | apps/desktop/src/locale.ts、apps/desktop/src/main.ts |
| 图标/托盘/安装器侧栏 | apps/desktop/resources/、apps/desktop/installer/ |
| 内置更新器关闭 | apps/desktop/package.json（删除 dshMandatoryUpdatePolicy）、electron-builder config 强制 publish: null |
| 欢迎页（API Key 主路径） | apps/desktop/src/welcome-*.ts、apps/desktop/src/client/WelcomePage.tsx |

## 待办（M1+）

- [ ] 设计提供图标后放入 assets/ 并更新 brand.json 的 assets 字段
- [ ] 在构建脚本中消费 brand.json（避免品牌字符串散落）
- [ ] 关于页加入"基于 DeepSeek Harness（MIT）"说明
- [ ] 校验产物签名与 publisher 名称
