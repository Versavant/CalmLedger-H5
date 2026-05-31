# H5 — CalmLedger 跨端共享 H5 页面

供 iOS / Android / 鸿蒙复用。当前包含隐私声明页面。

## 隐私声明加载策略

基于版本号的本地优先加载：远程版本 > 本地版本时才走 OSS，否则走本地 bundle。

### 版本号规则

```
remoteVersion > bundledVersion  →  走 OSS（热更）
remoteVersion ≤ bundledVersion  →  走本地 bundle（零流量，默认）
```

### 本地版本号

各端各自维护本地版本号，跟随 app 发版递增。

- **iOS**: `PrivacyView.swift` 中 `bundledPrivacyVersion`
- **Android / 鸿蒙**: TBD

隐私内容不变时本地版本号不动，无需任何远程配置。

### 远程配置（OSS `remote_config.json`）

```json
{
  "privacyPolicyURL": "https://calmledger.oss-cn-hangzhou.aliyuncs.com/privacy.html",
  "privacyPolicyVersion": 1
}
```

- `privacyPolicyURL` — OSS 上隐私声明页面的公网地址
- `privacyPolicyVersion` — OSS 上页面的版本号（整数）

### 热更流程

1. 修改 `privacy.html` 内容
2. 上传到 OSS：`ossutil cp H5/privacy.html oss://calmledger/privacy.html -f`
3. 更新 `remote_config.json`，把 `privacyPolicyVersion` 设为一个大于当前所有客户端本地版本的整数
4. 各端下一次展示隐私声明时自动从 OSS 拉取新版本

### 发版收敛

发版时把新的 `privacy.html` 同步到各端 bundle 目录，并把本地版本号更新为远程版本号。收敛后自动切回本地加载，省流量。

## OSS

- Bucket: `calmledger`
- 公网地址: `https://calmledger.oss-cn-hangzhou.aliyuncs.com/`

## 文件

| 文件 | 说明 |
|---|---|
| `privacy.html` | 隐私声明页面（清醒账簿） |
