# H5 — CalmLedger 跨端共享法律与支持页面

供 iOS、Android、鸿蒙和 App Store 元数据复用。此目录是 OSS 多语言法律页面的本地备份和发布源。

## 文件结构

```text
index/index_XX.html                14 种语言落地页备份
privacy/privacy_XX.html            14 种语言隐私政策备份
terms/terms_XX.html                14 种语言用户条款备份
remote-links.json                  14 种语言远端 URL 的机器可读备份
```

语言代码：`zh-Hans`、`zh-Hant`、`en`、`ja`、`ko`、`fr`、`de`、`es`、`pt`、`ru`、`it`、`nl`、`pl`、`tr`。

- App 内置源位于 `../Apple/CalmLedger/CalmLedger/Resources/privacy/` 和 `../Apple/CalmLedger/CalmLedger/Resources/terms/`。
- 修改法律内容时，应同时同步 App 内置文件、本目录对应语言备份、对应语言落地页链接和 OSS 对象。
- `remote-links.json` 是落地页、隐私政策和用户条款远端地址的独立备份；远端路径变化时必须与本文件中的链接表一起更新。

## OSS 远端约定

- Bucket：`calmledger`（Bucket 默认 private，法律页面对象必须单独设为 `public-read`）
- 区域：`cn-hangzhou`
- 公网前缀：`https://calmledger.oss-cn-hangzhou.aliyuncs.com/`
- 落地页对象：`oss://calmledger/index/index_XX`
- 隐私对象：`oss://calmledger/privacy/privacy_XX`
- 条款对象：`oss://calmledger/terms/terms_XX`

重要：OSS 对象名没有 `.html` 后缀。本地备份保留 `.html` 后缀。上传时必须设置：

```text
ACL                 public-read
Content-Type        text/html; charset=utf-8
Content-Disposition inline
```

使用带 `.html` 后缀的 OSS 默认域名对象可能被阿里云强制作为附件下载，不能用作 App Store 的公开网页。

## 14 种语言远端链接

完整的 42 个远端 URL 以 `remote-links.json` 为准，包含每种语言的 `index`、`privacy` 和 `terms`。路径格式如下：

| 类型 | 公网 URL 格式 |
|---|---|
| 落地页 | `https://calmledger.oss-cn-hangzhou.aliyuncs.com/index/index_XX` |
| 隐私政策 | `https://calmledger.oss-cn-hangzhou.aliyuncs.com/privacy/privacy_XX` |
| 用户条款 | `https://calmledger.oss-cn-hangzhou.aliyuncs.com/terms/terms_XX` |

## 上传命令

简体中文示例：

```bash
ossutil cp index/index_zh-Hans.html oss://calmledger/index/index_zh-Hans -f --acl public-read --content-type 'text/html; charset=utf-8' --content-disposition inline
ossutil cp privacy/privacy_zh-Hans.html oss://calmledger/privacy/privacy_zh-Hans -f --acl public-read --content-type 'text/html; charset=utf-8' --content-disposition inline
ossutil cp terms/terms_zh-Hans.html oss://calmledger/terms/terms_zh-Hans -f --acl public-read --content-type 'text/html; charset=utf-8' --content-disposition inline
```

批量上传时按上述 14 个语言代码逐个上传，不要给远端对象名追加 `.html`。

上传后至少检查：

```bash
curl -I https://calmledger.oss-cn-hangzhou.aliyuncs.com/index/index_zh-Hans
curl -I https://calmledger.oss-cn-hangzhou.aliyuncs.com/privacy/privacy_zh-Hans
curl -I https://calmledger.oss-cn-hangzhou.aliyuncs.com/terms/terms_zh-Hans
```

预期结果：HTTP `200`、`Content-Type: text/html; charset=utf-8`、`Content-Disposition: inline`。

## App 内加载策略

App 默认加载 bundle 中与当前语言匹配的法律页面。仅当远端版本号大于客户端内置版本号时，才使用远端 URL 热更新。

```text
remoteVersion > bundledVersion  → 使用 OSS
remoteVersion ≤ bundledVersion  → 使用本地 bundle
```

iOS 内置版本号位置：

- 隐私政策：`PrivacyView.swift` 的 `bundledPrivacyVersion`
- 用户条款：`TermsView.swift` 的 `bundledTermsVersion`

远程配置中的简中兜底链接应使用：

```json
{
  "privacyPolicyURL": "https://calmledger.oss-cn-hangzhou.aliyuncs.com/privacy/privacy_zh-Hans",
  "termsOfUseURL": "https://calmledger.oss-cn-hangzhou.aliyuncs.com/terms/terms_zh-Hans"
}
```

## 旧地址

- `https://versavant.github.io/CalmLedger-H5/privacy.html`：旧 GitHub Pages 镜像，不是当前 OSS 多语言页面的事实源。
- `https://calmledger.oss-cn-hangzhou.aliyuncs.com/privacy.html`：旧根目录地址，不用于正式多语言页面。
