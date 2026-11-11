# 夜版构建（个人 fork）

上游 `appshubcc/Bettbox` 只在打 tag 时构建，且合并 PR 极慢；这个 fork 用 `nightly.yaml` 自己出包。

## 触发方式

| 触发 | 构建内容 |
|---|---|
| 每天 UTC 19:00（北京 03:00） | Linux x64 |
| push 到 `integration/upstream-prs` | Linux x64 |
| Actions → nightly → Run workflow（勾选 `android`） | Linux x64 + Android arm64（debug 签名） |

产物只上传 **artifact**（保留 14 天），不建 Release、不使用任何签名密钥。

## 取产物

```bash
# 列出最近的构建
gh run list -R xiaquwan1001/Bettbox --workflow nightly.yaml -L 5

# 下载 Linux 包（含 Bettbox、BettboxCore、lib/、data/ 与 sha256sum.txt）
gh run download <run-id> -R xiaquwan1001/Bettbox -n bettbox-linux-x64-nightly
```

也可以在仓库 Actions 页面点进某次运行，在 Artifacts 里手动下载。

## 安装（Linux x64）

```bash
tar -xzf Bettbox-linux-x64-YYYYMMDD.tar.gz -C /tmp/bettbox-nightly
sudo cp -a /tmp/bettbox-nightly/. /usr/share/Bettbox/   # /usr/bin/Bettbox 是符号链接，不用动
```

覆盖前建议先备份：`cp -a /usr/share/Bettbox ~/bettbox-backup-$(date +%F)`。

## Android

`bettbox-android-arm64-nightly-debug` 里的 APK 是 **debug 签名**（`applicationIdSuffix=.debug`），包名为 `com.appshub.bettbox.debug`，可与官方版并存，数据目录独立。

## 这个分支上有什么

`integration/upstream-prs` = 上游 main + 以下改动：

- 扩展 ExternalControl 命令通道（上游 PR #609）
- `external-controller` 端口可配置（上游 PR #610）
- `set <field> <value>` 命令（堆叠在 #609 之上，分支 `feat/external-control-set`）
- 6 个 cherry-pick 的上游 PR：#565 #575 #589 #398 #479 #576

配套的命令行客户端是仓库外的 `bx`（往 `Bettbox.control.sock` 写一行命令）。
