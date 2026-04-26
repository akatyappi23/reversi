# Express とは

## 一言で言うと

**Node.js でWebサーバーを作るための便利パッケージ。**

Node.js だけでもサーバーは作れるが、定番の処理を毎回自分で書く必要がある。
Express を使うと、それらをシンプルに書けるようになる。

---

## インストール

```bash
npm install express
```

TypeScript を使う場合は型定義も追加する。

```bash
npm install --save-dev @types/express
```

---

## 基本の流れ

```
ブラウザ  →  HTTPリクエスト  →  [Expressサーバー]  →  HTTPレスポンス  →  ブラウザ
```

1. ブラウザが「このURLに情報をください」とリクエストを送る
2. Express がリクエストを受け取り、対応する処理を実行する
3. 結果をブラウザに返す

---

## コードの基本構造

```ts
import express from 'express'

const app = express()   // アプリ（サーバーの本体）を作る

// ミドルウェアやルーティングをここに書く

app.listen(3000, () => {
    console.log('サーバー起動: http://localhost:3000')
})
```

---

## ルーティング

**「このURLにリクエストが来たら、この処理をする」** という設定。

```ts
// GET /api/hello にリクエストが来たら
app.get('/api/hello', (req, res) => {
    res.json({ message: 'hello!' })
})
```

### HTTPメソッド

| メソッド | 用途 | Expressでの書き方 |
|---|---|---|
| GET | データを取得する | `app.get(...)` |
| POST | データを送信・作成する | `app.post(...)` |
| PUT | データを更新する | `app.put(...)` |
| DELETE | データを削除する | `app.delete(...)` |

### req と res

- `req`（request） = ブラウザから来た情報
- `res`（response） = ブラウザへ返す情報

```ts
app.get('/api/users/:id', (req, res) => {
    const id = req.params.id      // URLの :id 部分を取得
    const name = req.query.name   // ?name=xxx のクエリパラメータを取得

    res.json({ id, name })        // JSONで返す
    // res.send('テキスト')        // テキストで返す
    // res.status(404).send(...)  // ステータスコードを指定して返す
})
```

---

## ミドルウェア

**「リクエストとレスポンスの間に挟み込む共通処理」**のこと。

```
リクエスト → [ミドルウェア1] → [ミドルウェア2] → [ルーティング] → レスポンス
```

`app.use()` で登録する。上から順に実行される。

```ts
app.use(morgan('dev'))                              // ログを出力する
app.use(express.json())                             // リクエストのJSONを読み取る
app.use(express.static('static'))                   // 静的ファイルを配信する
```

---

## 静的ファイルの配信

**静的ファイル** = サーバーが加工せず、そのままブラウザに渡すファイル（HTML, CSS, 画像など）。

```ts
app.use(express.static('static', { extensions: ['html'] }))
```

この設定で `static/` フォルダ内のファイルが自動的に配信される。

| ブラウザのURL | 返されるファイル |
|---|---|
| `/` | `static/index.html` |
| `/style.css` | `static/style.css` |
| `/image.png` | `static/image.png` |

---

## エラーハンドラー

処理中にエラーが発生したときの処理。引数が **4つ** あるのが特徴。

```ts
app.use(errorHandler)

function errorHandler(err: any, _req: express.Request, res: express.Response, _next: express.NextFunction) {
    console.error('エラーが発生しました', err)
    res.status(500).json({ message: 'サーバーエラーが発生しました' })
}
```

> `express-async-errors` パッケージを使うと、async関数内のエラーも自動でここに流れてくる。

---

## このプロジェクトでの全体像

```ts
import express from 'express'
import morgan from 'morgan'
import 'express-async-errors'

const PORT = 3000
const app = express()

// ① ログを出力する
app.use(morgan('dev'))

// ② static/ フォルダのファイルをそのまま配信する
app.use(express.static('static', { extensions: ['html'] }))

// ③ /api/hello にGETリクエストが来たらJSONを返す
app.get('/api/hello', async (req, res) => {
    res.json({ message: 'hello express!!!' })
})

// ④ エラーが起きたら500エラーを返す
app.use(errorHandler)

// ⑤ ポート3000でサーバーを起動する
app.listen(PORT, () => {
    console.log(`Reversi application started: http://localhost:${PORT}`)
})

function errorHandler(err: any, _req: express.Request, res: express.Response, _next: express.NextFunction) {
    console.error('Unexpected error occurred', err)
    res.status(500).send({ message: 'Unexpected error occurred' })
}
```

### リクエストの流れ

```
ブラウザ: GET /api/hello
    ↓
① morgan    → ログを出力
    ↓
② static    → /api/hello はファイルではないのでスルー
    ↓
③ app.get('/api/hello') → 一致！
    ↓
res.json({ message: 'hello express!!!' })
    ↓
ブラウザ: { "message": "hello express!!!" } を受け取る
```

---

## Node.js だけの場合との比較

同じことを Express なしで書くとこうなる。

```ts
// Node.js だけの場合（Express なし）
import http from 'http'

http.createServer((req, res) => {
    if (req.url === '/api/hello' && req.method === 'GET') {
        res.writeHead(200, { 'Content-Type': 'application/json' })
        res.end(JSON.stringify({ message: 'hello!' }))
    } else {
        res.writeHead(404)
        res.end()
    }
}).listen(3000)
```

URLが増えるほど `if` が増え、コードが複雑になる。Express を使えばシンプルに書ける。
