# newt-help-center-starter-nuxtjs

[Newt](https://www.newt.so/) を利用したシンプルなヘルプセンターサイト
技術構成：Nuxt.js, JavaScript

## 開発をはじめる

### Step1: Newtプロジェクトをセットアップ

1. プロジェクトを作成します
    - プロジェクトUIDを控えておきましょう。プロジェクトUIDは 管理画面URL（ `https://app.newt.so/{プロジェクトUID}` ） もしくは プロジェクト設定 > 一般 から確認できます。
2. Appを作成します
    - Appテンプレートから作成する場合、**Help center**を選択し「このテンプレートを追加」をクリックしてください。
    - スクラッチで作成する場合は、App名とAppUIDを設定して次のステップに進みます。
    - AppUIDを控えておきましょう。AppUIDは管理画面URL（ `https://app.newt.so/{プロジェクトUID}/app/{AppUID}` ） または App設定 > 一般 から確認できます。
3. App設定から、Articleモデル, Categoryモデルを作成します
    - Appテンプレートから作成した場合、すでにモデルが作成されているためこのステップは飛ばします
    - スクラッチで作成した場合は、[Newtプロジェクトの構成](#Newtプロジェクトの構成)に従ってAppとモデルを作成します
4. プロジェクト設定 > APIキー からCDN APIトークンを作成します
    - プロジェクト設定 > APIキー よりCDN APIトークンを作成します
    - 複製マークをクリックしてトークンをコピーしましょう

### Step2: nuxt.config.jsを書き換える

1. Step1で取得したプロジェクトUID, AppUID, CDN APIトークンでnuxt.config.jsのpublicRuntimeConfigを書き換えます

```javascript
export default {
  // ...省略
  publicRuntimeConfig: {
    projectUid: 'プロジェクトUID',
    appUid: 'AppUID',
    token: 'CDN APIトークン',
  }
}
```

### Step3: devサーバーを起動する

Yarnを使う

```bash
# 依存パッケージをインストール
$ yarn install

# localhost:3000でdevサーバーを起動
$ yarn dev
```

NPMを使う

```bash
# 依存パッケージをインストール
$ npm install

# localhost:3000でdevサーバーを起動
$ npm run dev
```

### Step4: Staticなサイトを生成して起動

```bash
# Staticなサイトを生成（SSG）
$ yarn generate

# サーバーを起動
$ yarn start
```

## Newtプロジェクトの構成

`Help center` appの中にArticle, Categoryの2つのモデルを作ります。

| App名（任意） | モデル名（モデルUID） |
| --- | --- |
| Blog | Article (`article`) |
|  | Category (`category`) |
|  | Author (`author`) |

### Article（`uid: article`）モデル

| フィールドID | フィールド名 | フィールドID	フィールド名 | フィールドID	フィールド名 |
| --- | --- | --- | --- |
| title | タイトル | テキスト | 必須フィールド, このフィールドをタイトルに使う |
| description | 説明文 | テキスト |  |
| slug | スラッグ | テキスト | 必須フィールド |
| body | 本文 | Markdown or リッチテキスト |  |
| category | カテゴリ | 参照（Categoryモデル） |  |
| tags | タグ | 参照（Authorモデル） | 複数値 |

### Category（`uid: category`）モデル

| フィールドID | フィールド名 | フィールドID	フィールド名 | フィールドID	フィールド名 |
| --- | --- | --- | --- |
| name | 名前 | テキスト | 必須フィールド, このフィールドをタイトルに使う |
| slug | スラッグ | テキスト | 必須フィールド |
| description | 説明文 | テキスト | 必須フィールド |
| emoji | 絵文字 | テキスト |  |
| sortOrder | 並び順（昇順） | 数値 |  |

Deploy
