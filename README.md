# Company Slide Builder

Claude Codeに指示するだけで、全22パターンの高品質ビジネススライドを自動生成するClaude Codeスキルです。
トヨマネ式メソッド + Cynthialyデザインシステム + ルバート図解パターンを組み合わせた設計思想。

**日本の上場企業95社 + 霞が関省庁17機関のブランドカラープリセットを内蔵。** 企業名を伝えるだけで即座にブランドカラーが適用されます。

> **Note**: このリポジトリは現在プライベートです。将来的にパブリック公開を予定しています。

## クイックスタート

### 前提条件

- [Node.js](https://nodejs.org/) v16以上
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) がインストール済み

### 1. リポジトリをクローン

```bash
git clone https://github.com/groundcobra009/company-slide-builder.git
cd company-slide-builder
```

### 2. 依存関係をインストール

```bash
npm install
```

### 3. 出力ディレクトリを作成

```bash
mkdir -p output
```

### 4. Claude Codeで使う

```bash
claude
```

Claude Codeが起動したら、自然言語で指示するだけでスライドが作成されます:

```
「AI市場の最新動向について10枚のプレゼン資料を作って」
「株式会社〇〇のブランドカラーでテンプレートを設定して」
```

### 5. 通知機能を使う場合（任意）

スライド生成後にDiscordやメールで自動通知を受け取りたい場合は、環境変数を設定してください。

```bash
cp .env.example .env
# .env を編集して自分のメールアドレス等を設定
```

GitHub Secretsの設定方法は [環境変数（GitHub Secrets）](#環境変数github-secrets) を参照してください。

設定が完了すると、スライド作成後に以下のような通知が届きます:

**Discord通知**

<img src="docs/images/discord-notification-example.png" width="600" alt="Discord通知の表示例">

**メール通知**

<img src="docs/images/email-notification-example.png" width="600" alt="メール通知の表示例">

## 使い方

### Claude Code経由（推奨）

Claude Codeを起動してスライド作成を指示するだけです。内部で以下のスキルが自動的に連携します:

- **slide-builder**: トピックリサーチ → トヨマネ式設計 → スクリプト生成 → PPTX出力 → 配信
- **design-template**: 企業ブランドカラーのリサーチ・適用、またはモノトーンデフォルト設定

### スクリプトを直接書く場合

`scripts/template.js` をrequireしてNode.jsスクリプトを作成・実行できます。

```javascript
var path = require("path");
var t = require("./scripts/template.js");
var pptxgen = t.pptxgen;

var pres = new pptxgen();
pres.layout = t.config.layout;

t.addTitleSlide(pres, "タイトル", "サブタイトル", "作成者");
// ... スライドを追加 ...

pres.writeFile({ fileName: path.resolve(__dirname, "output/presentation.pptx") });
```

## スキル構成

Claude Codeから使えるスキルが2つあります。

### 1. slide-builder（スライド作成）

リサーチ → 設計 → 生成 → 配信までを一括実行します。

```
ユーザーリクエスト → slide-builder-planner
  ├── content-researcher（トピックリサーチ）
  ├── slide-architect（トヨマネ式7ステップ設計）
  └── slide-scripter（スクリプト生成・実行・配信）
```

### 2. design-template（テンプレートカスタマイズ）

企業ブランドカラーをリサーチして適用、またはモノトーンデフォルトを設定します。

```
ユーザーリクエスト → design-template-planner
  ├── brand-researcher（企業ブランドリサーチ）
  └── template-generator（テンプレート生成・適用）
```

## 利用可能な22パターン

| # | パターン | 用途 |
|---|---------|------|
| 1 | 表紙 | プレゼンの冒頭 |
| 2 | サマリー | 結論と理由の提示 |
| 3 | セクション扉 | 章の区切り |
| 4 | 本文 | テキスト中心の説明 |
| 5 | 列挙型 | 番号付きリスト |
| 6 | 2カラム比較 | 左右2列の比較 |
| 7 | 統計数値 | KPI・数値の強調 |
| 8 | まとめ | 結論とNext Steps |
| 9 | 画像 | 画像の表示 |
| 10 | グラフ | 棒・円・折れ線グラフ |
| 11 | フロー（横型） | 横方向のプロセスフロー |
| 11b | フロー（縦型） | 縦方向のプロセスフロー |
| 12 | 比較対照 | 2要素の詳細比較 |
| 13 | 4象限マトリックス | 2軸4領域の分類 |
| 14 | サイクル図 | 循環プロセス |
| 15 | ガントチャート | スケジュール・工程表 |
| 16 | テーブル | データ一覧・比較表 |
| 17 | 背景型 | カテゴリ＋項目リスト |
| 18 | 拡散型 | 1→多の分岐構造 |
| 19 | 上昇型 | 段階的な成長・ステップ |
| 20 | フロー表型 | フロー矢印＋マトリックス表 |
| 21 | フローマトリックス型 | フロー矢印＋行列マトリックス |
| 22 | マトリックス型 | 行ラベル×列ラベルの表 |

## 環境変数（GitHub Secrets）

スライドを `downloads/` にプッシュすると、GitHub Actionsで自動通知が実行されます。
未設定の項目は自動的にスキップされるため、通知が不要であれば設定不要です。

### GitHub Secretsに設定する変数

リポジトリの **Settings > Secrets and variables > Actions** で設定してください。

| 変数名 | 必須 | 説明 | 設定値の例 |
|--------|------|------|-----------|
| `MAIL_USERNAME` | 任意 | Gmailアドレス（SMTP認証用） | `taro.yamada@gmail.com` |
| `MAIL_PASSWORD` | 任意 | Googleアプリパスワード（16文字） | `abcd efgh ijkl mnop` |
| `MAIL_TO` | 任意 | メール送信先アドレス | `team@example.com` |
| `MAIL_FROM` | 任意 | メール送信元アドレス（通常はMAIL_USERNAMEと同じ） | `taro.yamada@gmail.com` |
| `DISCORD_WEBHOOK_URL` | 任意 | DiscordチャンネルのWebhook URL | `https://discord.com/api/webhooks/1234567890/AbCdEfGh...` |

> **メール通知を使う場合**: `MAIL_USERNAME`・`MAIL_PASSWORD`・`MAIL_TO`・`MAIL_FROM` の4つすべてを設定してください。1つでも欠けるとメール通知はスキップされます。

> **Discord通知を使う場合**: `DISCORD_WEBHOOK_URL` のみ設定すればOKです。

### GitHub Secretsの設定手順

1. GitHubでリポジトリページを開く
2. **Settings**（歯車アイコン）をクリック
3. 左メニューの **Secrets and variables** > **Actions** をクリック
4. **New repository secret** ボタンをクリック
5. **Name** にシークレット名（例: `MAIL_USERNAME`）を入力
6. **Secret** に値（例: `taro.yamada@gmail.com`）を入力
7. **Add secret** をクリック
8. 上記の各変数について繰り返す

### MAIL_PASSWORD の取得方法（Googleアプリパスワード）

通常のGmailパスワードではSMTP認証が失敗します。以下の手順で **アプリパスワード** を発行してください。

#### 前提条件

- Googleアカウントで **2段階認証プロセス** が有効になっていること
- 2段階認証が未設定の場合は、先に有効化が必要です

#### Step 1: Googleアカウントのセキュリティページを開く

1. ブラウザで [https://myaccount.google.com/security](https://myaccount.google.com/security) にアクセス
2. Googleアカウントにログインする

<img src="docs/images/google-security-page.png" width="600" alt="Googleアカウントのセキュリティページ">

#### Step 2: 2段階認証を有効化する（まだの場合）

1. 「Googleにログインする方法」セクションの **2段階認証プロセス** をクリック
2. 画面の指示に従って、電話番号の確認・認証アプリの設定などを完了する
3. 2段階認証が「オン」になっていることを確認する

<!-- スクリーンショット: 2段階認証の設定画面 -->
<!-- <img src="docs/images/google-2fa-setup.png" width="600" alt="2段階認証プロセスの設定画面"> -->

#### Step 3: アプリパスワードを発行する

1. [https://myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) にアクセス
   - または、セキュリティページ → 2段階認証プロセス → ページ下部の **アプリ パスワード** をクリック
2. 「アプリ名」に任意の名前を入力する（例: `GitHub Actions`、`SlideBuilder通知` など）
3. **作成** ボタンをクリック

<img src="docs/images/google-app-password-create.png" width="600" alt="アプリパスワードの発行画面">

4. 16文字のアプリパスワードが表示される（例: `abcd efgh ijkl mnop`）

<img src="docs/images/google-app-password-result.png" width="600" alt="生成されたアプリパスワード">

5. **パスワードをコピーして、すぐにGitHub Secretsに登録する**

> **重要**: このパスワードは **1度しか表示されません**。画面を閉じると再表示できないため、表示されたらすぐに次のStep 4でGitHub Secretsに登録してください。

#### アプリパスワードの管理について

Google の案内では「覚えておく必要はない」とありますが、GitHub Actions で使う場合は **作成直後にGitHub Secretsへ登録する作業が必要** です。GitHub Secrets に保存すればワークフローから参照できますが、値そのものを後から画面で再表示することはできません。

**推奨する運用フロー:**

1. Google でアプリパスワードを新規作成
2. すぐに GitHub Actions の Secret に登録（下記 Step 4）
3. 動作確認
4. ローカルには残さない

> **補足**: パスワードを忘れた場合やSecret を再設定したい場合は、[アプリパスワード管理ページ](https://myaccount.google.com/apppasswords) で古いものを削除し、新しく発行し直してください。メモアプリやテキストファイルへの保存は避け、保管が必要な場合はパスワードマネージャー1か所に限定するのが安全です。

#### Step 4: GitHubに登録する

1. GitHubリポジトリの **Settings > Secrets and variables > Actions** を開く
2. **New repository secret** をクリック

<img src="docs/images/github-secrets-setup.png" width="600" alt="GitHub Secretsの設定画面">

3. 以下を入力して **Add secret** をクリック:
   - **Name**: `MAIL_PASSWORD`
   - **Secret**: Step 3でコピーした16文字のパスワード（スペースは含めても含めなくてもOK）

<img src="docs/images/github-secrets-add.png" width="600" alt="New repository secretの入力画面">

#### アプリパスワードに関するよくある質問

<details>
<summary><strong>Q: アプリパスワードは何回でも発行できますか？</strong></summary>

はい、何回でも発行できます。用途ごとに別々のアプリパスワードを発行することも可能です（例: 「GitHub Actions用」「別のアプリ用」など）。
</details>

<details>
<summary><strong>Q: 一度表示されたパスワードを後から確認できますか？</strong></summary>

いいえ、アプリパスワードは **発行時に一度だけ** 表示されます。閉じてしまうと再表示はできません。パスワードを忘れた場合は、古いものを削除して新しいアプリパスワードを発行してください。
</details>

<details>
<summary><strong>Q: アプリパスワードを削除・無効化したい場合は？</strong></summary>

[アプリパスワード管理ページ](https://myaccount.google.com/apppasswords) から、発行済みのアプリパスワードを個別に削除（取り消し）できます。削除すると、そのパスワードを使った認証は即座に無効になります。
</details>

<details>
<summary><strong>Q: 2段階認証を無効にするとどうなりますか？</strong></summary>

2段階認証を無効にすると、発行済みのアプリパスワードはすべて自動的に無効化されます。再度2段階認証を有効にした後、新しいアプリパスワードの発行が必要です。
</details>

### Discord Webhook URLの取得方法

1. Discordで通知を送りたいチャンネルの設定（歯車アイコン）を開く
2. **連携サービス** > **ウェブフック** をクリック
3. **新しいウェブフック** をクリック
4. 名前を設定（例: `SlideBuilder通知`）
5. **ウェブフックURLをコピー** をクリック

<img src="docs/images/discord-webhook-create.png" width="600" alt="Discord Webhook作成画面">

6. GitHubの **Settings > Secrets and variables > Actions > New repository secret** で:
   - **Name**: `DISCORD_WEBHOOK_URL`
   - **Secret**: コピーしたURL

## 企業プリセットライブラリ（112社/機関）

企業名を指定するだけで、ブランドカラーが自動適用されます。WebSearchによるリサーチが不要になり、即座にスライド作成を開始できます。

```
「トヨタのブランドカラーでスライドを作って」
「農林水産省のテンプレートで資料を作成して」
```

### 使い方（スクリプトから直接使う場合）

```javascript
var presets = require("./scripts/presets");

// 名前で検索（部分一致）
var preset = presets.find("トヨタ");
var preset = presets.find("農林水産");
var preset = presets.find("デジタル庁");

// slugで取得
var preset = presets.get("toyota");

// template.js に適用
var t = require("./scripts/template.js");
presets.apply(preset, t);

// 一覧取得
var list = presets.list(); // [{slug, name}, ...]
```

### 収録プリセット一覧

<details>
<summary><strong>テック企業（18社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `toyota` | トヨタ自動車 | `#EB0A1E` |
| `sony` | ソニーグループ | `#000000` |
| `panasonic` | パナソニック | `#0054A6` |
| `hitachi` | 日立製作所 | `#E4002B` |
| `toshiba` | 東芝 | `#CC0000` |
| `sharp` | シャープ | `#E6000D` |
| `nec` | NEC | `#1414A0` |
| `fujitsu` | 富士通 | `#E60012` |
| `keyence` | キーエンス | `#E4002B` |
| `canon` | キヤノン | `#BC0024` |
| `nikon` | ニコン | `#FFE100` |
| `fujifilm` | 富士フイルム | `#EE1337` |
| `olympus` | オリンパス | `#08107B` |
| `nintendo` | 任天堂 | `#E60012` |
| `murata` | 村田製作所 | `#F5002F` |
| `nidec` | ニデック | `#0AA547` |
| `denso` | デンソー | `#E6232A` |
| `hoya` | HOYA | `#003087` |

</details>

<details>
<summary><strong>通信・IT（12社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `docomo` | NTTドコモ | `#CC0033` |
| `ntt` | 日本電信電話 | `#0068B7` |
| `softbank` | ソフトバンク | `#A0A0A0` |
| `kddi` | KDDI | `#FF6600` |
| `rakuten` | 楽天グループ | `#BF0000` |
| `z-holdings` | Zホールディングス | `#FF0033` |
| `cyberagent` | サイバーエージェント | `#1DB446` |
| `dena` | DeNA | `#E4002B` |
| `mercari` | メルカリ | `#FF0211` |
| `moneyforward` | マネーフォワード | `#5B62DE` |
| `line` | LINE | `#06C755` |
| `recruit` | リクルート | `#2B2B2B` |

</details>

<details>
<summary><strong>金融（10社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `mufg` | 三菱UFJフィナンシャル・グループ | `#E60012` |
| `smfg` | 三井住友フィナンシャルグループ | `#009B63` |
| `mizuho` | みずほフィナンシャルグループ | `#1E3C78` |
| `nomura` | 野村ホールディングス | `#CC0000` |
| `daiwa-securities` | 大和証券グループ | `#F39800` |
| `tokio-marine` | 東京海上ホールディングス | `#003399` |
| `msad` | MS&ADインシュアランスグループ | `#003E7E` |
| `sompo` | SOMPOホールディングス | `#003E82` |
| `dai-ichi-life` | 第一生命ホールディングス | `#E50020` |
| `japan-post` | 日本郵政グループ | `#CC0000` |

</details>

<details>
<summary><strong>総合商社（5社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `mitsubishi-corp` | 三菱商事 | `#E60012` |
| `mitsui` | 三井物産 | `#003E7E` |
| `itochu` | 伊藤忠商事 | `#C41E3A` |
| `sumitomo-corp` | 住友商事 | `#003366` |
| `marubeni` | 丸紅 | `#CC0000` |

</details>

<details>
<summary><strong>自動車・製造（9社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `honda` | 本田技研工業 | `#CC0000` |
| `nissan` | 日産自動車 | `#C3002F` |
| `suzuki` | スズキ | `#003399` |
| `mazda` | マツダ | `#910428` |
| `subaru` | SUBARU | `#003366` |
| `isuzu` | いすゞ自動車 | `#CC0000` |
| `bridgestone` | ブリヂストン | `#E60012` |
| `komatsu` | コマツ | `#FFB800` |
| `daikin` | ダイキン工業 | `#004B97` |

</details>

<details>
<summary><strong>運輸（5社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `jr-east` | JR東日本 | `#009944` |
| `jr-west` | JR西日本 | `#0072BC` |
| `jr-central` | JR東海 | `#FF6600` |
| `ana` | ANAホールディングス | `#003B73` |
| `jal` | 日本航空 | `#CC0000` |

</details>

<details>
<summary><strong>エネルギー（4社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `eneos` | ENEOS | `#E60012` |
| `tepco` | 東京電力ホールディングス | `#003399` |
| `kansai-electric` | 関西電力 | `#003E82` |
| `inpex` | INPEX | `#003E7E` |

</details>

<details>
<summary><strong>製薬（7社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `takeda` | 武田薬品工業 | `#E60012` |
| `daiichi-sankyo` | 第一三共 | `#E2005E` |
| `astellas` | アステラス製薬 | `#0070C0` |
| `otsuka` | 大塚ホールディングス | `#003399` |
| `eisai` | エーザイ | `#CC0033` |
| `shionogi` | 塩野義製薬 | `#003E82` |
| `chugai` | 中外製薬 | `#E60012` |

</details>

<details>
<summary><strong>食品・飲料（6社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `asahi` | アサヒグループホールディングス | `#003087` |
| `kirin` | キリンホールディングス | `#CC0000` |
| `ajinomoto` | 味の素 | `#E60012` |
| `meiji` | 明治ホールディングス | `#E8530E` |
| `nissin-foods` | 日清食品ホールディングス | `#CC0000` |
| `yakult` | ヤクルト本社 | `#CC0000` |

</details>

<details>
<summary><strong>消費財・小売（6社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `fast-retailing` | ファーストリテイリング | `#FF0000` |
| `seven-and-i` | セブン&アイ・ホールディングス | `#E60012` |
| `aeon` | イオン | `#9B1F84` |
| `kao` | 花王 | `#E60012` |
| `unicharm` | ユニ・チャーム | `#003399` |
| `nippon-paint` | 日本ペイントホールディングス | `#003E82` |

</details>

<details>
<summary><strong>不動産（5社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `mitsui-fudosan` | 三井不動産 | `#003366` |
| `mitsubishi-estate` | 三菱地所 | `#CC0000` |
| `sumitomo-realty` | 住友不動産 | `#003399` |
| `daiwa-house` | 大和ハウス工業 | `#E85D0F` |
| `sekisui-house` | 積水ハウス | `#003399` |

</details>

<details>
<summary><strong>建設（4社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `kajima` | 鹿島建設 | `#003E82` |
| `shimizu` | 清水建設 | `#003399` |
| `obayashi` | 大林組 | `#003366` |
| `taisei` | 大成建設 | `#E8530E` |

</details>

<details>
<summary><strong>素材（4社）</strong></summary>

| slug | 企業名 | Primary Color |
|------|--------|---------------|
| `shin-etsu` | 信越化学工業 | `#003087` |
| `tokyo-electron` | 東京エレクトロン | `#003E7E` |
| `nippon-steel` | 日本製鉄 | `#003366` |
| `jfe` | JFEホールディングス | `#003399` |

</details>

<details>
<summary><strong>霞が関 省庁（17機関）</strong></summary>

| slug | 機関名 | Primary Color |
|------|--------|---------------|
| `cao` | 内閣府 | `#003366` |
| `soumu` | 総務省 | `#003399` |
| `moj` | 法務省 | `#006633` |
| `mofa` | 外務省 | `#003366` |
| `mof` | 財務省 | `#003366` |
| `mext` | 文部科学省 | `#003399` |
| `mhlw` | 厚生労働省 | `#003E82` |
| `maff` | 農林水産省 | `#006633` |
| `meti` | 経済産業省 | `#003399` |
| `mlit` | 国土交通省 | `#003399` |
| `env` | 環境省 | `#006633` |
| `mod` | 防衛省 | `#1A2744` |
| `digital` | デジタル庁 | `#6B21A8` |
| `reconstruction` | 復興庁 | `#009944` |
| `fsa` | 金融庁 | `#003366` |
| `caa` | 消費者庁 | `#E87511` |
| `cfa` | こども家庭庁 | `#E84B8A` |

</details>

## デザインルール（カラー・フォント・レイアウト）

### カラーパレット

`design-template` スキルで企業ブランドカラーに変更可能です。指定がなければモノトーンがデフォルトで適用されます。

| 変数名 | デフォルトHEX | 用途 |
|--------|-------------|------|
| `DARK_GREEN` | `#0D2623` | メインカラー。タイトルバー、ヘッダー背景 |
| `CREAM_YELLOW` | `#E8DE9F` | アクセントカラー。番号バッジ、矢印 |
| `LIGHT_GRAY` | `#F3F3F3` | コンテンツ背景、カードベース |
| `TEXT_DARK` | `#0D2623` | 見出し・強調テキスト |
| `TEXT_MEDIUM` | `#434343` | 本文テキスト |
| `TEXT_LIGHT` | `#595959` | 補助テキスト |
| `WHITE` | `#FFFFFF` | 白テキスト（暗背景上） |
| `HIGHLIGHT_YELLOW` | `#FFFF00` | ハイライト |

### フォント

| 項目 | 値 |
|------|-----|
| フォントファミリー | Noto Sans JP |
| 最小フォントサイズ | 16pt（出所表記のみ12pt） |

### レイアウト

| 項目 | 値 |
|------|-----|
| アスペクト比 | 16:9 |
| 左右マージン | 0.6in |
| コンテンツ幅 | 8.8in |

## テスト

```bash
npm test
```

全22パターンのテストスライドが `output/test_all_patterns.pptx` に生成されます。

## ディレクトリ構成

```
company-slide-builder/
├── .claude/
│   ├── skills/              ← Claude Codeスキル（ルーター）
│   │   ├── design-template.md
│   │   └── slide-builder.md
│   ├── agents/              ← エージェント定義
│   │   ├── brand-researcher.md
│   │   ├── content-researcher.md
│   │   ├── design-template-planner.md
│   │   ├── slide-architect.md
│   │   ├── slide-builder-planner.md
│   │   ├── slide-scripter.md
│   │   └── template-generator.md
│   └── workflows/           ← ワークフロー定義
│       ├── design-template_workflow.md
│       └── slide-builder_workflow.md
├── .github/workflows/       ← GitHub Actions（自動通知）
│   └── notify-pptx.yml
├── docs/
│   └── images/              ← README用スクリーンショット
├── scripts/
│   ├── template.js          ← コアテンプレートライブラリ（22パターン）
│   ├── test.js              ← 全パターンテスト
│   └── presets/             ← 企業カラープリセット（112社/機関）
│       ├── index.js         ← プリセット管理（検索・適用）
│       ├── docomo.js        ← NTTドコモ
│       ├── toyota.js        ← トヨタ自動車
│       ├── ...              ← 上場企業95社 + 省庁17機関
│       └── cfa.js           ← こども家庭庁
├── references/              ← 参照用IR資料（gitignored）
│   └── docomo/              ← 企業ごとにサブディレクトリ
├── downloads/               ← 配信用（GitHub経由ダウンロード）
│   ├── pptx/
│   └── pdf/
├── output/                  ← 生成ファイル出力先（gitignored）
├── .env.example             ← 環境変数テンプレート
├── .gitignore
├── CLAUDE.md                ← Claude Code用ルール
├── README.md
├── package.json
└── package-lock.json
```

## ライセンス

MIT
