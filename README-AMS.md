# AMS Harness（DSH fork）— M0.1 骨架

> 本仓库是 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（MIT）的 fork，用作 **AMS Studio 的内核（Kernel）**。
> 状态：**M0.1 骨架（2026-10-19）**。尚未安装依赖、尚未构建、尚未改源码；本次只建立 branding/patches 骨架与 remote。
> 方案依据：工作区文档 06_方案设计/02_主体/主体详细方案.md（本仓库只放"fork 胶水"，业务代码在 ams-plugins）。

## 1. 上游基线（锁定）

| 项 | 值 |
|---|---|
| 上游仓库 | https://github.com/deepseek-ai/deepseek-harness |
| 基线 tag | dsh-v0.2.1-rc.2（2026-10-17） |
| 基线 commit | 687f282e8625c6b8d11ff58211a90524be2e4317（"Merge pull request #6408 ... release-dsh-0.2.1-rc.2"） |
| package.json version | 0.2.1-rc.2 |
| packageManager | pnpm@11.28.5 |
| engines.node | ^22.19.0 或 >=24.0.0 |
| 许可 | MIT（保留上游 LICENSE 与版权声明；本 fork 修改说明见 §6） |

说明：本机已安装的 Desktop 0.2.1-rc.2 构建记录为 commit 332895e0f7c9d4dbfb29729f5eb367fa263ff986，与本 tag commit（687f282e）**不同**（安装包可能由 tag 之后的构建产生）。源码基线以 tag 为准；如需与安装包逐字节对齐，后续再 fetch 该 commit（深历史未拉取）。

## 2. 目录（AMS 新增）

~~~text
ams-harness/
├─ branding/              # 品牌单一事实来源（名称/appId/协议/更新源/图标占位）
│  ├─ README.md
│  ├─ brand.json
│  └─ assets/             # 图标等二进制占位（待设计提供）
├─ patches/               # 上游 pnpm patchedDependencies 目录（原样保留）
│  └─ ams/                # AMS 组合层与裁剪清单（不直接改上游 bundle）
│     ├─ README.md
│     ├─ product.patch.yml   # DSH loader patch（--patch overlay，L6 内核产品层）
│     ├─ trim.manifest.json
│     └─ upstream.json
└─ README-AMS.md          # 本文件
~~~

其余目录均为上游原样：apps/（cli、desktop、desktop-host、web）、packages/、vendor/、native/、python/、docs/、scripts/、website/ 等。**M0.1 未修改任何上游源码文件。**

## 3. 与上游的差异原则（最小 diff）

只允许 5 类改动（详见方案 §4.3）：

1. **品牌**：产品名/appId/协议/图标/欢迎页/菜单/关于/安装器文案；
2. **构建**：electron-builder 配置、签名、产物命名、CI；
3. **裁剪**：Office/LibreOffice/Python/documentpreview/实验功能等（env 开关驱动，见 patches/ams/trim.manifest.json）；
4. **组合**：music profile 模板 + product.patch.yml + 插件 payload 清单；
5. **壳级契约**：AMS 控制面、profile=music、CLI reserved profile、默认 home/workspace。

禁止：修改 Agent 循环/工具流水线/会话格式/slot 语义/权限模型/上游 bundle 的 cordis.patch.yml；禁止在本仓库写音乐业务代码。

## 4. 同步上游流程（草案，M0.2 起执行）

~~~text
upstream/master  --每两周合并-->  dev  --验证-->  main（发布）
冲突处理：优先保留上游结构；AMS 改动集中在 branding/、patches/ 与带 AMS-PATCH 标记的小改；
上游大重构时启用"重放 diff"（附录 A 清单即重放清单）。
~~~

- 本仓库 remote：origin = https://github.com/yan-mc/ams-harness.git（**占位，仓库由 Lead 创建，尚未 push**）；upstream = https://github.com/deepseek-ai/deepseek-harness.git（push URL 已禁用为 no_push）。
- 当前为 **浅克隆（--depth 1 --single-branch）**：无深历史；首次需要合并上游时执行 git fetch upstream 或按需 unshallow。

## 5. 构建命令（环境就绪后执行；M0.1 未执行）

~~~powershell
# 前置：Node 24.x、pnpm 11.28.5、VS 2022 Build Tools（原生模块）
pnpm install --frozen-lockfile
pnpm run build          # tsc + tsdown + Web/Vite
pnpm dsh web            # 本地验证 Web UI（默认 http://127.0.0.1:3080）
# 桌面内核（M1+ 才需要，需 Electron/electron-builder 与签名配置）
node apps/desktop/scripts/package-target.ts win-x64
~~~

## 6. 许可与品牌

- 上游代码 MIT：保留 LICENSE 与版权声明；本 fork 的修改在提交信息/CHANGELOG 与 patches 中说明。
- 品牌规范：不得把 "DeepSeek Harness" 用作产品名；产品名暂定 **AMS Studio**（可改），描述可用"基于 DeepSeek Harness（MIT）构建"。
- 商标/第三方许可详见上游 THIRD_PARTY_NOTICES.md；裁剪后需重新生成（M3）。

## 7. M0.1 状态

- [x] 浅克隆 dsh-v0.2.1-rc.2 到本目录
- [x] remote：origin=yan-mc/ams-harness（占位）+ upstream=deepseek-ai/deepseek-harness
- [x] branding/ 与 patches/ 骨架 + README-AMS.md
- [ ] Node/pnpm 安装（Lead M0.0 进行中）
- [ ] pnpm install / pnpm run build / pnpm dsh web（M0.1 构建验证）
- [ ] 品牌/裁剪源码改动（M1+）

构建记录见工作区：06_方案设计/02_主体/M0_构建记录.md。
