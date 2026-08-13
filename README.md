# Rule

面向 [Surge](https://nssurge.com/) 与 [Shadowrocket](https://apps.apple.com/app/shadowrocket/id932747118) 的个人分流规则仓库。

规则按用途和建议路由方向拆分为独立的 `.list` 文件，可以按需远程引用。仓库只提供规则集，不包含节点、订阅、策略组或完整客户端配置。

## 目录结构

| 路径 | 内容 | 常见策略 |
| --- | --- | --- |
| `Base/` | 局域网和基础白名单 | `DIRECT`，并优先匹配 |
| `China/Media/` | 中国大陆媒体服务 | `DIRECT` 或地区专用策略 |
| `China/Network/` | 中国大陆网络及下载节点 | `DIRECT` |
| `China/Services/` | 中国大陆应用、金融和生活服务 | 以文件头建议为准 |
| `Proxy/AI/` | AI 产品及其依赖服务 | AI 专用代理策略 |
| `Proxy/Commerce/` | 电商、支付和数字资产服务 | 通用或地区专用代理策略 |
| `Proxy/Communication/` | 社交、论坛和即时通信服务 | 通信专用代理策略 |
| `Proxy/Developer/` | 代码托管和开发工具 | 开发专用代理策略 |
| `Proxy/Finance/` | 券商及金融服务 | 出口稳定的专用策略 |
| `Proxy/Infrastructure/` | CDN、云主机和安全基础设施 | 通用代理策略 |
| `Proxy/Knowledge/` | 搜索、阅读、教育和知识服务 | 通用代理策略 |
| `Proxy/Media/` | 图片、音频和视频平台 | 媒体专用代理策略 |
| `Proxy/News/` | 国际及财经新闻 | 新闻专用代理策略 |
| `Proxy/Services/` | Apple、Google、Microsoft 等综合服务 | 服务专用代理策略 |
| `Proxy/Streaming/` | Netflix、Disney+、YouTube 等流媒体 | 对应地区或解锁策略 |

每个规则文件的开头均包含名称、用途和建议策略。目录表示主要分类，不代表其中所有文件都必须使用同一个策略，实际配置应以文件头说明为准。

## 使用方式

远程规则的基础地址为：

```text
https://raw.githubusercontent.com/rakuyoMo/rule/main
```

在 Surge 或 Shadowrocket 配置的 `[Rule]` 段中通过 `RULE-SET` 引用所需文件。例如：

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/Base/LocalAreaNetwork.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/Base/Unbreak.list,DIRECT

RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/Proxy/AI/OpenAI.list,AI
RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/Proxy/Streaming/Netflix.list,Netflix
RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/China/Media/Bilibili.list,Bilibili

RULE-SET,https://raw.githubusercontent.com/rakuyoMo/rule/main/China/Network/ChinaNetwork.list,DIRECT
FINAL,Proxy
```

`AI`、`Netflix`、`Bilibili` 和 `Proxy` 是示例策略组名称，使用前需要在客户端中自行定义。策略组名称区分大小写，应与实际配置保持一致。

也可以在客户端的远程规则集管理界面中添加单个 `.list` 文件的 Raw 地址，再为其指定策略。

## 匹配顺序

规则通常按从具体到宽泛的顺序匹配，建议依次放置：

1. `Base/LocalAreaNetwork.list` 与 `Base/Unbreak.list`。
2. 有明确策略的业务规则，例如 AI、流媒体、金融和通信服务。
3. 其他需要单独处理的 `China/` 与 `Proxy/` 规则。
4. `China/Network/ChinaNetwork.list` 这类区域兜底规则。
5. 客户端的 `FINAL` 规则。

同一域名可能同时属于公司、基础设施或业务规则集。最终采用哪个策略取决于引用顺序，因此更新配置后应检查客户端的规则命中记录。

## 规则格式与兼容性

仓库目前使用 Surge 风格的纯文本规则，包含以下类型：

- `DOMAIN`、`DOMAIN-SUFFIX`、`DOMAIN-KEYWORD`
- `IP-CIDR`、`IP-CIDR6`、`IP-ASN`、`GEOIP`
- `USER-AGENT`、`PROCESS-NAME`
- `URL-REGEX`、`OR`

基础域名与 IP 规则通常可直接用于较新的 Surge 和 Shadowrocket。`USER-AGENT`、`PROCESS-NAME`、`URL-REGEX`、`IP-ASN`、`OR` 等高级类型的支持情况可能随客户端及版本变化；若导入时报错，请先确认当前客户端的规则语法支持范围。

IP、ASN 与 GEOIP 规则通常带有 `no-resolve`，用于避免匹配过程触发额外 DNS 查询。不要在客户端不支持该参数时直接照搬。

## 更新与贡献

提交变更时请遵循以下原则：

- 将规则放入路由意图和业务职责最匹配的文件。
- 保留文件头中的名称、用途和建议策略。
- 新增规则前检查当前文件及相邻分类，避免无意义重复。
- 域名使用小写，IP 地址使用合法 CIDR，并保持一行一条规则。
- 不在规则条目中附加节点、订阅信息、凭据或具体策略组名称。

更完整的维护约定见 [AGENTS.md](AGENTS.md)。

## 许可

本仓库采用 [MIT License](LICENSE)。
