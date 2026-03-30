# Volta とは

## 概要

Volta は **Node.js のバージョン管理ツール**です。

プロジェクトごとに異なる Node.js や npm のバージョンを使い分けることができます。
`package.json` に使用するバージョンを記録しておくことで、チームメンバー全員が同じ環境を再現できます。

---

## なぜ必要なの？

Node.js はバージョンによって動作が異なることがあります。
例えば「自分の PC では動くのに、他の人の PC では動かない」という問題が起きがちです。

Volta を使うと：

- プロジェクトに入ると**自動で正しい Node.js バージョンに切り替わる**
- `package.json` にバージョンが記録されるので**チームで環境を統一できる**
- 複数プロジェクトで異なるバージョンを**同時に管理できる**

---

## インストール

```bash
# Linux / macOS (WSL含む)
curl https://get.volta.sh | bash
```

インストール後、ターミナルを再起動して確認：

```bash
volta --version
```

---

## 基本的な操作

### Node.js をインストールする

```bash
volta install node@16       # バージョン指定
volta install node@latest   # 最新版
```

### npm をインストールする

```bash
volta install npm@8
```

### インストール済みの一覧を確認する

```bash
volta list
```

### プロジェクトに使用バージョンを固定する

```bash
volta pin node@16.17.1
volta pin npm@8.15.0
```

実行すると `package.json` に以下のように記録されます：

```json
"volta": {
  "node": "16.17.1",
  "npm": "8.15.0"
}
```

これにより、そのプロジェクトのディレクトリに入るだけで自動的に指定バージョンが使われます。

---

## よく使うコマンド一覧

| コマンド | 説明 |
|---|---|
| `volta install node@<version>` | Node.js をインストール |
| `volta install npm@<version>` | npm をインストール |
| `volta pin node@<version>` | プロジェクトのバージョンを固定 |
| `volta list` | インストール済みの一覧を表示 |
| `volta --version` | Volta 自体のバージョンを確認 |

---

## このプロジェクトでの設定

`package.json` を見ると以下のように設定されています：

```json
"volta": {
  "node": "16.17.1"
}
```

`~/projects/reversi` ディレクトリに入ると、自動的に Node.js 16.17.1 が使われます。
