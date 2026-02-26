# 🔵 メタ定義: このファイルの責務 (AIエージェント用)

> **AIエージェントへの指示 (Prompt Repetition Strategy):**
> このファイルはプロジェクトの「設計図」であり「憲法」です。
> 実装を開始する前に、内容を**「合計2回」**読み込み、不変の原則（API分離・フロントロードされた型安全・テスト駆動）を完全に理解してください。

- **役割:** プロジェクトの「仕様」「設計方針」「不変の原則」の定義。
- **読むべきタイミング:** 新しい機能の実装、DB変更、または設計上の判断が必要な時。
- **思考の優先順位:** 設計と方針に関して最優先。ここにある原則に反するコードを書いてはならない。
- **詳細な実装ルール:** コーディング規約、設計パターン、テスト指針については、下部の **「ドキュメントナビゲーション」** に従い、`04_CODING_RULES.md`, `05_DESIGN_PATTERNS.md`, `06_TESTING_GUIDE.md` を参照すること。

---

# SNSアプリ ルールとアーキテクチャ (Rules & Architecture)

## ⚖️ プロジェクト憲法 (The Constitution)

> **警告:** 以下のルールは、本プロジェクトにおいて**最優先される絶対的な制約**である。
> AIエージェントは独自の判断でこれに違反する提案や実装を行ってはならない。

1.  **言語と対話の厳守 (Japanese Only Policy):**
    - 思考、応答、ユーザーとの対話、およびドキュメント記述はすべて**日本語**で行うこと。
    - 英語での応答は、コード内の識別子や技術用語を除き、原則禁止とする。
    - Gitコミットメッセージは「日本語」かつ「プレフィックス（`feat:`, `fix:`, `docs:`, `test:`, `chore:`, `refactor:`）」を必ず使用すること。
2.  **型安全性のフロントロード化 (Type Safety Front-loading):**
    - 過去プロジェクトで後から苦労して導入した技術（Zod, RHF, Zustand, OpenAPI連携, Branded Types）を**最初から標準アーキテクチャとして組み込む**。
    - フロントエンドの型定義を手動で作成・保守してはならない。必ずバックエンドの仕様（Scramble）による自動生成型定義を利用すること。
3.  **APIベースの完全分離構成 (Headless Architecture):**
    - バックエンド（Laravel）は純粋なAPIサーバーとして振る舞い、ステートレスにする。
    - フロントエンド（Next.js）がUIと状態管理を担う。
4.  **品質の保証 (Testing & Verification):**
    - 新機能の開発にあたっては、その品質を担保するため、必ず対応するテスト（Pest/PHPUnit または Playwright/Vitest）を作成・実行し、継続的な動作保証の基盤を構築すること。

---

## 🏗️ アーキテクチャと設計思想 (Architecture & Philosophy)

### アーキテクチャ: 完全分離型 (Fully Decoupled)

- **Backend:** Laravel 12 (API専用 / ステートレス)
- **Frontend:** Next.js 15 (App Router) + TypeScript
- **コンポーネント / UI:** Tailwind CSS, Shadcn/ui
- **状態管理 (State):** Zustand (グローバルUI状態), React Query (サーバー非同期取得/キャッシュ)
- **型・バリデーション:** Zod, React Hook Form (RHF), openapi-typescript

### 認証 (Authentication)

- **Laravel Sanctum (API Token方式)** を採用。SPA認証（Session Cookieベース）ではない。
- Next.jsのサーバー（Route HandlersやServer Actions）をBFF層として使い、取得したトークンを **HttpOnly Cookie** としてセキュアに管理する。

### 開発方針

- **技術スタックのモダン化:** バックエンドとフロントエンドを分離し、よりスケーラブルな構成にする。
- **ドキュメント駆動開発:** AIのリカバリと品質担保のため、実装前に必ず設計をドキュメント化し、実装後にログを残すサイクルを徹底する。

---

## 🔒 技術仕様と重要ルール (Technical Specs & Rules)

### データベース設計

- **マイグレーション:** Laravel 12の標準構成に準拠。
- **最適化:** SNSにおける大量データとN+1問題を回避するため、Strict Mode (`Model::preventLazyLoading()`) を有効化し、適切な Eager Loading (`with()`, `withCount()`) を設計時に考慮する。

### バリデーション

- **Backend:** FormRequestによる厳格検証。
- **Frontend:** `Zod` + `React Hook Form` をUI（Shadcn UI）と強固に統合した「鉄壁のフォーム基盤」を構築する。

---

## 📚 ドキュメントナビゲーション

AIエージェントは、現在のタスクに応じて以下のファイルを読み込むこと。

1.  **コーディング中:** `04_CODING_RULES.md`（命名、スタイル、ディレクトリ構造）
2.  **実装パターン検討中:** `05_DESIGN_PATTERNS.md`（設計、型ポリシー、バリデーション）
3.  **テスト作成中:** `06_TESTING_GUIDE.md`（テスト戦略、モック、各テストフレームワーク）

---

## 📂 ドキュメント構成 (Documentation Structure)

- **`03_WORKFLOW.md`:** 開発の手順書・ロードマップ（作業時はこれを常に見る）。
- **`04_CODING_RULES.md`:** コーディング規約。
- **`05_DESIGN_PATTERNS.md`:** 設計パターン。
- **`06_TESTING_GUIDE.md`:** テスト指針。
- **`07_DEV_LOG.md`:** 開発ログ。
- **`01_AGENT_RECOVERY_MANUAL.md`:** エージェント復旧マニュアル。
