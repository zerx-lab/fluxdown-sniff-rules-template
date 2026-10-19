# FluxDown 嗅探规则仓库模板

用这个模板创建**你自己的** FluxDown 嗅探规则仓库。在 [zerx-lab/fluxdown-sniff-rules](https://github.com/zerx-lab/fluxdown-sniff-rules) 登记一次之后，你每发布一个 Release，规则仓都会在一小时内自动收录、签名，并下发给所有 FluxDown 用户，不需要每次提 PR 等审核。

*English: create your own FluxDown sniff-rule repository from this template, register it once in [zerx-lab/fluxdown-sniff-rules](https://github.com/zerx-lab/fluxdown-sniff-rules), then every Release you publish is picked up automatically within an hour. Full guide: <https://fluxdown.com/docs/en/contributing/sniff-rules/>.*

完整说明：[贡献嗅探规则](https://fluxdown.com/docs/zh/contributing/sniff-rules/) · 字段参考：[嗅探规则参考](https://fluxdown.com/docs/zh/browser-extension/sniff-rules/)

## 1. 创建仓库

点右上角 **Use this template → Create a new repository**，仓库必须是 **Public**。

## 2. 改 `sniff-rules.json`

```json
{
  "namespace": "your-name",
  "hosts": ["example.com"],
  "rules": [ ... ],
  "tests": [ ... ]
}
```

| 字段 | 说明 |
| --- | --- |
| `namespace` | 你的命名空间：小写字母、数字、连字符，2–31 个字符，例如 GitHub 用户名。不能用 `misc`、`official` 等保留字。登记后不能改。 |
| `hosts` | 你打算**认领**的站点域名（含全部子域），例如 `["example.com", "example-cdn.net"]`。只用于登记前的自检；登记后以规则仓 `registry.json` 里的认领为准。 |
| `rules` | 规则列表。每条 `id` 必须是 `community.<namespace>.<名字>`，`tier` 必须是 `community`；规则里用到的每个 `host` / `pageHost` / `mirror.hosts` 都必须落在认领站点内。 |
| `tests` | 规则测试（强烈建议）。收录时会和规则仓里所有规则一起运行，失败则不收录。写法见文档「编写规则测试」。 |

先在 FluxDown 扩展「设置 → 嗅探规则 → 本地规则」里把规则调通，再把 `local.xxx` 改成 `community.<namespace>.xxx`、`tier` 改成 `community` 粘进来最省事。

## 3. 本地检查（可选）

```bash
git clone https://github.com/zerx-lab/fluxdown-sniff-rules ../fluxdown-sniff-rules
node ../fluxdown-sniff-rules/scripts/check-source.mjs --file sniff-rules.json --repo <你的用户名>/<仓库名>
```

需要 Node.js 22+。推送到 GitHub 后，`check` 工作流会自动做同样的检查。

## 4. 发布第一个 Release

```bash
git tag v1.0.0
git push origin v1.0.0
```

`release` 工作流会先检查，通过后创建 Release 并上传 `sniff-rules.json`。也可以在网页上手动创建 Release，只要附件名是 `sniff-rules.json`、不是草稿或预发布。

## 5. 登记（只需一次）

向 [zerx-lab/fluxdown-sniff-rules](https://github.com/zerx-lab/fluxdown-sniff-rules) 提一个**只修改 `registry.json`** 的 PR，在 `sources` 里追加：

```json
{ "ns": "your-name", "repo": "your-name/your-repo", "hosts": ["example.com"], "description": "一句话说明" }
```

机器人会拉取你的最新 Release，跑与收录完全相同的检查并在 PR 里评论结果。维护者审核认领范围后合并，一小时内收录。

## 6. 之后的更新

1. 改 `sniff-rules.json`。**修改已发布的规则时把它的 `version` 加一**，否则不会被收录。
2. 推送新标签（如 `v1.0.1`）发布新 Release。
3. 规则仓每小时收录一次。失败时会在规则仓开一个 `stage-fail: <namespace>` 的 issue 并 @ 你，旧版本继续生效；修好后发新 Release，issue 自动关闭。

注意：

- **不要替换旧 Release 的附件**，那会被判为篡改并拒收；永远发新的 Release。
- 想认领更多站点，向规则仓提 PR 修改你在 `registry.json` 里的 `hosts`。
- 想停止维护：提 PR 删除你的登记条目，你的规则会从社区包中移除。
