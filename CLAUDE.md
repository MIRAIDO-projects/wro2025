# MIRAIDO（熊野未来道）公式サイト エージェントガイド

**プロジェクト:** miraido-astro
**サイト名:** MIRAIDO（熊野未来道）
**内容:** WRO Japan 2025 公認三重予選会の公式サイト + MIRAIDOプロジェクト紹介
**ホスト:** Cloudflare Pages（Cloudflare Workers アダプター使用）
**設定:** wrangler.jsonc
**最終更新:** 2026-03-20

---

## プロジェクト概要

### ビジネス背景

MIRAIDOプロジェクトは「こどもたちのミライを応援する」プロジェクト。
本サイトは WRO Japan 2025 公認三重予選会の運営・告知を担う。

- 開催日: 2025年8月2日(土) 10:00-17:00
- 会場: 三重県立熊野古道センター
- 種目: RoboSports / RoboMission エキスパート部門
- 問い合わせ・参加受付: LINE公式アカウント

### 技術スタック（package.json 実測値）

| カテゴリ | 技術 | バージョン |
|---------|------|-----------|
| フレームワーク | Astro (SSG/SSR) | ^5.16.8 |
| アダプター | @astrojs/cloudflare | ^12.6.12 |
| スタイリング | SCSS（手書き、Tailwind不使用） | - |
| アニメーション | GSAP + SplitText + ScrollTrigger（ローカルJS） | - |
| スライダー | Splide.js（ローカルJS） | - |
| フォント | Cinzel + しっぽり明朝（セルフホスト woff/woff2） | - |

### アーキテクチャ

- **Reactなし** -- 純粋なAstroコンポーネントのみ
- **Tailwind不使用** -- SCSSでスタイリング（BEMライクなクラス命名）
- **CMS不使用** -- 静的HTMLコンテンツ
- **JSライブラリはpublic/js/にローカル配置**（CDNからDLしたものを同梱）

```
src/pages/*.astro           --> ページ（静的HTML + SEOメタ）
src/layouts/Layout.astro    --> 共通レイアウト（CSS/JS読み込み）
src/components/*.astro      --> Header / Footer
src/styles/**/*.scss        --> SCSS（モジュール分割）
public/js/*.js              --> GSAP / Splide（ローカル配置）
public/webfonts/*           --> Cinzel / しっぽり明朝
public/images/*             --> 画像素材
```

---

## ファイル構成（実測）

```
miraido-astro/
├── astro.config.mjs          <-- Cloudflare アダプター設定
├── package.json
├── tsconfig.json
├── wrangler.jsonc             <-- Cloudflare Workers 設定
├── public/
│   ├── favicon.svg
│   ├── images/                (35枚: WRO大会画像、ロゴ、背景等)
│   ├── js/                    (7ファイル: gsap, splide, scrolltrigger等)
│   └── webfonts/              (8ファイル: Cinzel, しっぽり明朝)
└── src/
    ├── components/
    │   ├── Header.astro
    │   └── Footer.astro
    ├── layouts/
    │   └── Layout.astro       <-- 唯一のレイアウト
    ├── pages/
    │   ├── index.astro        <-- トップページ（WRO大会情報、スポンサー募集等）
    │   └── privacy.astro      <-- プライバシーポリシー
    └── styles/
        ├── style.scss         <-- エントリポイント
        ├── style.css          <-- コンパイル済みCSS
        ├── style.css.map
        ├── default/
        │   ├── _font-face.scss
        │   ├── _variables.scss
        │   └── _global.scss
        ├── modules/
        │   ├── _header.scss, _footer.scss
        │   ├── _loading.scss, _basic.scss
        │   ├── _what.scss, _works.scss
        │   ├── _feature.scss, _merit.scss
        │   ├── _flow.scss, _cta.scss
        └── vendors/
            ├── reset.css
            └── splide.min.css
```

---

## ページ構成

### index.astro（トップページ）
ローディング画面 -> ヘッダー -> MIRAIDO紹介（what） -> プロジェクト/WRO種目紹介（works / Splideスライダー） -> WRO三重予選会詳細（feature） -> スポンサー募集（merit） -> 参加方法フロー（flow） -> お問い合わせ/LINE（cta） -> フッター

### privacy.astro
プライバシーポリシー

---

## コーディング規約

### SCSS
- BEMライクなクラス命名（`.section__title`, `.section__text`）
- モジュール単位でファイル分割（`modules/_what.scss` 等）
- 変数は `default/_variables.scss` に集約
- `style.scss` をエントリポイントとして `@use` でインポート

### Astroコンポーネント
- レイアウトは `Layout.astro` に統一
- ページ固有のJSは `<script>` タグでページ末尾に記述
- SEOメタ（title, description, OGP）は各ページの frontmatter で設定

### 画像
- `public/images/` に配置
- alt属性を必ず設定
- WebP等の最適化フォーマットを推奨

### JavaScript
- GSAP / Splide は `public/js/` のローカルファイルを使用
- `Layout.astro` の `<script>` タグで読み込み
- ページ固有のアニメーション初期化はページ内 `<script>` で実行

---

## ワークフロー

グローバル CLAUDE.md の「implementer -> reviewer」フローに従う。

```
依頼 -> implementer（実装） -> reviewer（レビュー）
  -> 重大な指摘あり: implementer に差し戻し
  -> 軽微・提案のみ: 完了
```

### implementer の責務
- Astroコンポーネント / SCSS / ページの実装・修正
- 実装前後のGitコミット必須
- SEO メタ（title, description, OGP, JSON-LD）の設定
- セマンティックHTML の維持
- SCSS モジュール構成の維持

### reviewer チェックリスト
```
- npm run build 成功（エラー・警告なし）
- npm run preview で全ページ表示確認
- メタタグ: title, description, og:title, og:description, og:image
- セマンティックHTML構造の妥当性
- SCSS のモジュール分割が守られているか
- 画像の alt 属性
- モバイル表示の確認（タップターゲット 44x44px以上）
- GSAP / Splide アニメーションの動作
- Git コミット整合性
```

---

## パフォーマンス目標

- LCP < 2.5s
- CLS < 0.1
- INP < 200ms
- Lighthouse 90+（Performance, SEO, Accessibility, Best Practices）

---

## デプロイメント

```bash
npm run dev       # localhost:4321
npm run build     # dist/ 出力
npm run preview   # ビルド結果確認
```

Cloudflare Pages: GitHub main ブランチ push -> 自動デプロイ

---

## 注意事項

- このプロジェクトに React / Tailwind / microCMS は存在しない
- JSライブラリの追加・更新は `public/js/` に手動配置する運用
- フォントは `public/webfonts/` にセルフホスト（外部CDN読み込みしない）
- .env やシークレットのコミット禁止
