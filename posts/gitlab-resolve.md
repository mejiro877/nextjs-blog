---
title: "【GitLab CI】Gitへの接続失敗でジョブが失敗するときの対処法"
date: "2022-09-21"
thumbnail: "/images/thumbnail04.jpg"
---

# 前提

私が確認した環境は次の通りです。

GitLab サーバ: WSL2 上の docker でローカルに構築
GitLab Runner をインストールしたホスト OS: Windows 10, Windows 11
利用した Executor: docker

# ジョブが失敗した時のメッセージ

失敗した時のジョブの詳細は[CI/CD] > [ジョブ]から確認することができます。
次のようなエラーメッセージが出ていました。

```
fatal: unable to access 'http://localhost/*********/test.git/': Failed to connect to localhost port 80 after 0 ms: Connection refused
ERROR: Job failed: exit code 1
```

# 対処法

Runner の設定ファイル「config.toml」の[[runners]]の設定の url 及び clone_url の値を localhost からプライベート IP アドレスに変更する。（プライベート IP アドレスはご自分の環境に合わせて設定してください。）

```config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker"
  url = "http://192.168.*.*/"
  clone_url = "http://192.168.*.*/"
```

私は WSL2 のローカル上で GitLab サーバーを立てているので、元々は、http\://localhost/で登録していました。
しかし、Executor の docker コンテナ内からだと、GitLab サーバーに対して、localhost の URL ではアクセスできないようで、ホスト OS のプライベート IP アドレスを明示的に指定する必要がありました。
