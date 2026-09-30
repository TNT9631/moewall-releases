# 哦鲸鲸 MoeWall · 正式版发布页

> ⚠️ **这个仓库只放发布产物，不放源码。** 源码在私有仓 `TNT9631/moewall`。

## 这里有什么

| 文件 | 说明 |
|---|---|
| `update.json` | 版本清单 —— App 的「检查更新」直接读它（结构见 `update.json.example`） |
| Release 资产 | `MoeWall-vX.Y.Z.apk`（正式版，固定签名） |

## App 怎么用（**不需要任何 token**）

```text
检查更新  GET https://raw.githubusercontent.com/TNT9631/moewall-releases/main/update.json
下载 APK  GET https://api.github.com/repos/TNT9631/moewall-releases/releases/assets/<asset_id>
          Accept: application/octet-stream
```

⚠️ **不要用 `browser_download_url`**（`github.com/.../releases/download/...`）——
实测该域名在部分网络下会超时（12s），而 `api.github.com` / `objects.githubusercontent.com` 稳定可达。

## 发布流程（全自动，不靠人手推）

1. 私有仓打正式版 tag → CI 构建 → 私有仓出 Release
2. **同一步**把 **APK + `update.json`** 推到本仓
   （用一枚**只对本仓 `contents:write`** 的 PAT，存在**私有仓 Secrets** 里）
3. 每次都刷新 `update.json` 的 `versionCode` / `asset_id` / `sha256`

## 安全约定（务必遵守 —— 用户 2026-09-30："以后发布正式版要加固，安全第一"）

1. **绝不把源码 / 口令 / 逆向记录 / 私人源配置带进来**（本仓是**公开**的）
2. PAT **只存私有仓 Secrets**，**永不进 APK**；权限最小化：fine-grained、只此一仓、只 `contents:write`、设有效期、定期轮换
3. `update.json` 必须带 **`sha256`** —— App 下载后**校验**，不一致就丢弃
4. App 侧还应**校验 APK 签名与自身一致**（防替换）
5. **只发正式版**（不带 `-tN` 测试后缀）；测试包永不进本仓
