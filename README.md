# clash-rules (MRS)

自动跟踪 [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules) 的发布,使用 [mihomo](https://github.com/MetaCubeX/mihomo) 内核自带的 `convert-ruleset` 命令，将其规则转换为 mihomo 的 **`.mrs`** 二进制 rule-set 格式，并发布到本仓库的 [GitHub Release](../../releases/latest)。

- **同步频率**：GitHub Actions 每 3 天自动运行一次（也可手动触发）。
- **版本对齐**：本仓库 Release 的 tag 与上游一致（形如 `YYYYMMDDHHmm`）；上游未更新时自动跳过，不产生重复 Release。
- **稳定链接 / CDN**：通过 [jsDelivr](https://www.jsdelivr.com/) 分发，始终用 `cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/<name>.mrs` 引用。

## 提供的规则（13 个 `.mrs`）

| 文件 | behavior | 说明 |
|---|---|---|
| `apple.mrs` | domain | Apple 在中国大陆可直连域名 |
| `direct.mrs` | domain | 直连域名 |
| `gfw.mrs` | domain | GFWList 域名 |
| `google.mrs` | domain | Google 域名 |
| `greatfire.mrs` | domain | GreatFire 域名 |
| `icloud.mrs` | domain | iCloud 域名 |
| `private.mrs` | domain | 私有网络域名 |
| `proxy.mrs` | domain | 代理域名 |
| `reject.mrs` | domain | 广告 / 隐私域名 |
| `tld-not-cn.mrs` | domain | 非中国大陆使用的顶级域名 |
| `cncidr.mrs` | ipcidr | 中国大陆 IP |
| `lancidr.mrs` | ipcidr | 局域网 / 保留 IP |
| `telegramcidr.mrs` | ipcidr | Telegram IP 段 |

> **注意**：上游的 `applications.txt` 属于 `classical` 类型，而 mrs 格式**只支持 `domain` / `ipcidr`**，无法转换，故本仓库不提供 `applications.mrs`。如需该规则，请直接使用上游文本文件：`https://github.com/Loyalsoldier/clash-rules/releases/latest/download/applications.txt`（`behavior: classical`）。

<!-- 消费链接指向本仓库 yulinfeng000/clash-rules-mrs -->


## 使用方法

在 mihomo / Clash.Meta 配置中引用（按需增减）：

```yaml
rule-providers:
  reject:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/reject.mrs"
    path: ./ruleset/reject.mrs
    interval: 86400
  icloud:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/icloud.mrs"
    path: ./ruleset/icloud.mrs
    interval: 86400
  apple:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/apple.mrs"
    path: ./ruleset/apple.mrs
    interval: 86400
  google:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/google.mrs"
    path: ./ruleset/google.mrs
    interval: 86400
  proxy:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/proxy.mrs"
    path: ./ruleset/proxy.mrs
    interval: 86400
  direct:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/direct.mrs"
    path: ./ruleset/direct.mrs
    interval: 86400
  private:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/private.mrs"
    path: ./ruleset/private.mrs
    interval: 86400
  gfw:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/gfw.mrs"
    path: ./ruleset/gfw.mrs
    interval: 86400
  greatfire:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/greatfire.mrs"
    path: ./ruleset/greatfire.mrs
    interval: 86400
  tld-not-cn:
    type: http
    behavior: domain
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/tld-not-cn.mrs"
    path: ./ruleset/tld-not-cn.mrs
    interval: 86400
  telegramcidr:
    type: http
    behavior: ipcidr
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/telegramcidr.mrs"
    path: ./ruleset/telegramcidr.mrs
    interval: 86400
  lancidr:
    type: http
    behavior: ipcidr
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/lancidr.mrs"
    path: ./ruleset/lancidr.mrs
    interval: 86400
  cncidr:
    type: http
    behavior: ipcidr
    format: mrs
    url: "https://cdn.jsdelivr.net/gh/yulinfeng000/clash-rules-mrs@release/cncidr.mrs"
    path: ./ruleset/cncidr.mrs
    interval: 86400

# 规则顺序参考 Loyalsoldier 官方推荐（先匹配者优先），google 默认走代理，可按需改为 DIRECT
rules:
  - RULE-SET,reject,REJECT
  - RULE-SET,private,DIRECT
  - RULE-SET,icloud,DIRECT
  - RULE-SET,apple,DIRECT
  - RULE-SET,google,PROXY
  - RULE-SET,proxy,PROXY
  - RULE-SET,direct,DIRECT
  - RULE-SET,gfw,PROXY
  - RULE-SET,greatfire,PROXY
  - RULE-SET,tld-not-cn,PROXY
  - RULE-SET,telegramcidr,PROXY
  - RULE-SET,lancidr,DIRECT
  - RULE-SET,cncidr,DIRECT
  - MATCH,PROXY
```

> `domain` 类的 provider 必须写 `behavior: domain`，`*cidr` 类必须写 `behavior: ipcidr`，且都要 `format: mrs`，否则内核会加载失败。

## 工作原理

见 [`.github/workflows/sync-mrs.yml`](.github/workflows/sync-mrs.yml)：

1. 读取上游 clash-rules 最新 Release 的 tag；若与本仓库当前 Release 相同则跳过。
2. 下载 mihomo `compatible` 版二进制。
3. 逐个下载上游 `.txt` 规则，用 `mihomo convert-ruleset <domain|ipcidr> yaml <in>.txt <out>.mrs` 转换。
4. 用 [`softprops/action-gh-release`](https://github.com/softprops/action-gh-release) 以上游 tag 发布所有 `.mrs`。
5. 将 `.mrs` 文件 force-push 到 `release` 分支，供 [jsDelivr CDN](https://www.jsdelivr.com/) 分发。

## 致谢

- 规则数据：[Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules)
- 转换工具：[MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo)（`convert-ruleset`）
