# Company Slide Builder

Claude Codeに指示するだけで、全22パターンの高品質ビジネススライドを自動生成します。
トヨマネ式メソッド + Cynthialyデザインシステム + ルバート図解パターンを組み合わせた設計思想。

## セットアップ

```bash
# 1. クローン
git clone <このリポジトリのURL>
cd company-slide-builder

# 2. 依存関係インストール
npm install

# 3. 出力ディレクトリ作成
mkdir -p output

# 4. 環境変数設定（通知機能を使う場合）
cp .env.example .env
# .env を編集
```

## 使い方

`scripts/template.js` をrequireしてスクリプトを作成・実行します。

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
未設定の項目は自動的にスキップされます。

### GitHub Secretsに設定する変数

リポジトリの **Settings > Secrets and variables > Actions** で設定してください。

| 変数名 | 必須 | 説明 |
|--------|------|------|
| `MAIL_USERNAME` | 任意 | Gmailアドレス（SMTP認証用） |
| `MAIL_PASSWORD` | 任意 | Googleアプリパスワード（16文字） |
| `MAIL_TO` | 任意 | メール送信先アドレス |
| `MAIL_FROM` | 任意 | メール送信元アドレス |
| `DISCORD_WEBHOOK_URL` | 任意 | DiscordチャンネルのWebhook URL |

### MAIL_PASSWORD の取得方法（Googleアプリパスワード）

1. [Googleアカウント](https://myaccount.google.com/) にログイン
2. **セキュリティ** > **2段階認証プロセス** を有効化（まだの場合）
3. **2段階認証プロセス** のページ下部にある **アプリ パスワード** をクリック
4. アプリ名に「GitHub Actions」など任意の名前を入力して **作成**
5. 表示された16文字のパスワードをコピー
6. GitHubリポジトリの **Settings > Secrets and variables > Actions > New repository secret** で:
   - Name: `MAIL_PASSWORD`
   - Secret: コピーした16文字のパスワード

> **注意**: 通常のGmailパスワードではSMTP認証が失敗します。必ずアプリパスワードを使用してください。

## デザインルール（カラー・フォント・レイアウト）

### カラーパレット（デフォルト: モノトーン）

`design-template` スキルで企業ブランドカラーに変更可能です。

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
├── scripts/
│   ├── template.js          ← コアテンプレートライブラリ（22パターン）
│   └── test.js              ← 全パターンテスト
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
