
---
title: ansible
category: Tool
layout: 2017/sheet
tags: [Tools]
updated: 2022-06-16
created: 2023-01-28 04:04:29
weight: -10
intro: ansible
---

## インストール方法

[autoware/setup-dev-env.sh at main · autowarefoundation/autoware · GitHub](https://github.com/autowarefoundation/autoware/blob/main/setup-dev-env.sh#L123-L126)
```bash
python3 -m pipx ensurepath
export PATH="${PIPX_BIN_DIR:=$HOME/.local/bin}:$PATH"
pipx install --include-deps --force "ansible==6.*"
```

## yes/no ではなく true/false

[よこち on Twitter: "ansbile のドキュメントにおけるにおける boolean は yes/no が遣われがちだったが、ansilbe-lint では true/false でないとエラーになる。どうしたもんでしょという issue。 https://t.co/dcNl9R83sl" / Twitter](https://twitter.com/akira6592/status/1554971476612501505)

[[Vote ended on 2022-08-03] Disconnect between Docs and Ansible-lint in regards to truthy statements (booleans) · Issue #116 · ansible-community/community-topics · GitHub](https://github.com/ansible-community/community-topics/issues/116)

議論の結果，yes/noはやめてtrue/falseを使おうとなったようで，ドキュメントもtrue/falseで統一されるようになった