# patches/ams/ —— 内核组合层与裁剪清单

本目录是 **AMS 对 DSH 组合的权威声明**（数据/配置，不含业务代码）。

> 目录约定：上游 patches/ 根目录存放 pnpm patchedDependencies 的依赖补丁（*.patch，原样保留）；AMS 自己的组合/裁剪文件统一放在 patches/ams/ 子目录，避免与上游补丁混淆。M0.1 只建骨架；实际生效点在 M1 的内核构建与 profile 初始化。

## 两类"patch"

| 类型 | 文件 | 作用 |
|---|---|---|
| DSH loader patch（组合层） | product.patch.yml | 以 --patch overlay 形式应用在内核 profile 组合的最上层（L6，优先级最高）；用于禁用遥测/账号/危险行、设置默认值、开发模式开关 |
| 构建/包裁剪（源码或产物层） | trim.manifest.json | 行禁用清单、包级移除清单、构建脚本改动清单，分 P0/P1/P2 阶段；M0.1 不执行 |
| 上游锁定 | upstream.json | 构建与发布读取：tag/commit/dsh 版本/裁剪开关 |

说明：electron-updater 与强制更新策略**不是** loader 行，属构建层（electron-builder 配置 + package.json 字段），在 trim.manifest.json 的 buildChanges 中登记，M1 实施。

## 组合层级（来自 06_方案设计/02_主体/主体详细方案.md §5.5.2）

~~~text
L1 dsh-base  ->  L2 dsh-web-app  ->  L3 @ams/music-bundle（launcher 安装）
-> L4 profile 用户 patch  ->  L5 home patch  ->  L6 patches/product.patch.yml（内核 --patch，最高优先级）
~~~

## 约定

- 每条禁用项必须写：原因、依据（方案章节/证据）、阶段（P0/P1/P2）；机器可读版本在 trim.manifest.json。
- 行 id 以实际 dump 为准：首次构建后执行 dsh --profile music --dump-config 校验 id 是否存在；不存在的 id 由 app-boot 警告（不致命），需清理。
- 开发模式条件（AMS_DEV=1）允许 !!js 表达式，仅在 loader 的 config/disabled 位置使用。
- 不在此目录写业务逻辑；不直接改上游 bundle 的 cordis.patch.yml（见 README-AMS.md §3）。

## 待办（M1+）

- [ ] 用 dsh --dump-config 校验 product.patch.yml 的行 id
- [ ] 把 product.patch.yml 接入内核构建产物 resources/ams/product.patch.yml
- [ ] 按 trim.manifest.json 实现裁剪开关（AMS_TRIM_* env）
- [ ] 产品断言脚本：无遥测/账号/内置更新器、组合快照
