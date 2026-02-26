# 🟢 メタ定義: このファイルの責務 (AIエージェント用)

> **AIエージェントへの指示 (Prompt Repetition Strategy):**
> このファイルは「機能実装の設計パターンと堅牢性」の正解を定義しています。
> 新機能の設計、型定義、バリデーションロジックの構築、Service層の実装時に必ず読み込んでください。

- **役割:** TypeScript 型ポリシー、Service/DTO パターン、バリデーション戦略、アーキテクチャパターンの定義。
- **読むべきタイミング:** 設計フェーズ、型定義時、バックエンドロジック構築時。

---

# SNSアプリ 設計パターン (Design Patterns)

## 🛡️ TypeScript 型定義ポリシー (Type Safety Policy)

- **any 禁止:** `any` 型の使用を原則禁止。不明な場合は `unknown` + 型ガード（Type Guard）を使用する。
- **SSOT (Single Source of Truth) の徹底化:**
    - APIの型はすべて、`openapi-typescript` 等によってバックエンドから生成された `types/schema.d.ts` を唯一の正解とする。
    - フロントエンド側でモデルの `interface`（例: `interface User { ... }`）を手動作成・保守することは **厳禁**。
- **Branded Types (Nominal Typing):**
    - `UserId` や `TweetId` などの ID 型をただの `number` や `string` ではなく、ブランド型として定義し、誤代入をコンパイル段階で防ぐ。
- **定数管理 (Constants):** ステータス値（pending, approved, etc）などは `constants` ファイルで `as const` を使用して一元管理し、そこから `typeof` および `[keyof typeof ...]` で Union Types を自動生成（導出）する。
- **コンポーネント型 (Props):** React コンポーネントの Props 型は必ず `[Component Name]Props` という命名規則でエクスポートする。
- **Utility Types の活用:** `Pick`, `Omit`, `Partial` などを使用し、生成された大元の型から派生させて再利用性を高める。

---

## 🏗️ アーキテクチャ・パターン

### 1. API中心のデータフロー (Client-Server Separation)

- **パターン:** `Controller (Laravel)` → `JsonResource` → (HTTP/JSON) → `React Query / Fetch` → `Zod Validation (Optional)` → `React Component`
- バックエンドはデータ構造に責任を持ち、フロントエンドは受け取ったデータをキャッシュ（React Query）して表示する。

### 2. 鉄壁のフォーム基盤 (Form & Validation)

- **React Hook Form (RHF) + Zod:**
    - クライアントサイドの「入力中・送信前」の検証は、**必ず Zod を用いて RHF と統合**して行う。
    - 状態管理（`useState`）を使った素のフォーム実装はレガシー技術として扱い、採用しない。
- **Backend Validation:**
    - フロントでの防御をすり抜けた不正値や、DB状態に依存する検証（ユニーク制約など）は Laravel の `FormRequest` で弾き、ステータス `422 Unprocessable Entity` と共にエラーメッセージ一覧を JSON で返す。
    - フロントエンドは 422 エラーを検知したら、AxiosIntercept または React Query の Error Boundary を使って該当フィールド下にメッセージを展開またはトースト表示する。

### 3. グローバル状態管理 (Zustand & Server State)

- **Server State (サーバー由来のデータ):** React Query
    - API からフェッチしたリストや詳細データ、それらの「ロード中」「エラー」などの状態は React Query (TanStack Query) に任せる。
- **Client State (UI専用のデータ):** Zustand
    - モーダルウィンドウの開閉、ダークモード設定、現在のログインユーザーのプレビュー状態など、サーバーと通信しない「UI固有のグローバル変数」は Zustand ストアに保存する。
