---
title: ubuntu
category: ROS
layout: 2017/sheet
tags: [Featured]
updated: 2022-06-16
weight: -10
intro: Ubuntu
---

Getting started
---------------

{: .-three-column}

### CapsLockキーをMenuキーにする

`/etc/default/keyboard`を編集して以下の一行を追加する

```/etc/default/keyboard
XKBOPTIONS="menu:nocaps"`
```

編集が終了したら再起動

### フォルダを英語化する

以下を実行

```bash
LANG=C xdg-user-dirs-gtk-update
```

### KDEの残骸を消す

```bash
sudo apt remove plasma-desktop --autoremove
sudo apt-get remove kde* --autoremove
sudo apt-get remove plasma* --autoremove
sudo update-alternatives --config default.plymouth
sudo update-initramfs -u
sudo update-grub
sudo systemctl disable sddm
sudo systemctl stop sddm
sudo apt-get purge --auto-remove sddm
sudo systemctl enable gdm3
sudo systemctl start gdm3
reboot
```

[参考](https://trendoceans.com/how-to-remove-the-kde-plasma-environment-in-ubuntu/)

### KDEのWalletの無効化

KDE付属のパスワード管理ソフト「kwallet」

~/.config/kwalletrcに

```jsx
Enabled=false
```

と書き込む

（有効化はログアウトかプラズマの再起動が必要）

参考：

[KDEウォレットを無効にする方法は？](https://www.webdevqa.jp.net/ja/kde5/kde%E3%82%A6%E3%82%A9%E3%83%AC%E3%83%83%E3%83%88%E3%82%92%E7%84%A1%E5%8A%B9%E3%81%AB%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95%E3%81%AF%EF%BC%9F/957133052/)


### aptのupgrade系コマンドの違い

aptのサブコマンドで似たような以下の3つのコマンドがある

- upgrade
- dist-upgrade
- full-upgrade

#### sudo apt upgrade

sources.listで設定されたPPAなどの取得元から利用可能なアップグレードをインストールする．

アップグレードするパッケージに新たな依存関係が追加された場合，新しい依存パッケージが追加でインストールされるが，
**アップグレードの過程で何らかのパッケージの削除が必要となる場合，アップグレードは行われない**

#### sudo apt-get dist-upgrade / full-upgrade

**dist-upgradeとfull-upgradeは同様の機能**

dist-upgradeはapt-getとの互換性の為に残されている気がする．

**upgradeとは違い，システム全体をアップグレードするためなら既存パッケージの削除を厭わない．**

### Discordを最小化状態で起動する(Ubuntu)

```bash
discord --start-minimized
```

[Option for Discord to start minimized on Linux](https://support.discord.com/hc/en-us/community/posts/360048037971-Option-for-Discord-to-start-minimized-on-Linux)

### GitHubにssh鍵を追加する

```bash
cd ~/.ssh
ssh-keygen -t rsa
```

- 名前とパスワードを聞かれるので入力
- 公開鍵（〜.pub）の中身をgithubに登録

[https://github.com/settings/keys](https://github.com/settings/keys)

- configに登録

```bash
Host github github.com
  HostName github.com
  IdentityFile ~/.ssh/github
  User git
```

- テスト

```bash
ssh -T github
```

### Slackを最小化した状態で立ち上げる

-uオプションをつける

```bash
slack -u
```

### Bashでの文字エスケープ

Bashでの文字エスケープは**シングルクオートが基本**

- 以下が問題なく使える
  - ダブルクオーテーション””
  - 空白
  - 変数
- 工夫が必要なもの
  - シングルクオート’’
    - ダブルクオートで囲むと良い
      - 「'」→「"'"」

参考：[https://qiita.com/kawaz/items/f8d68f11d31aa3ea3d1c](https://qiita.com/kawaz/items/f8d68f11d31aa3ea3d1c)


### gitのsubmoduleの移動方法

```bash
git mv <from> <to>
```