# 🟢 メタ定義: このファイルの責務 (AIエージェント用)

> **AIエージェントへの指示 (Prompt Repetition Strategy):**
> このファイルは「コードの書き方やスタイル」の正解を定義しています。
> 実装、リファクタリング、コードレビューを行う際に必ず読み込み、スタイルの一貫性を維持してください。

- **役割:** 命名規則、ディレクトリ構造、コメント規則、スタイリング（Tailwind）の定義。
- **読むべきタイミング:** ファイル作成、コード記述、リファクタリング時。

---

# SNSアプリ コーディング規約 (Coding Rules)

## 🎨 スタイリングと形式 (Style & Format)

### フロントエンド (Next.js App Router / Tailwind CSS)

- **Routing Links:** ページ遷移には必ず Next.js の `<Link>` コンポーネントを使用する。
- **自動整形:** `prettier-plugin-tailwindcss` を適用し、クラス名の並び順を統一する。
- **デザイン・レイアウトの忠実な再現 (Mockup First):**
    - `docs-private/project-detail/` 配下にある以下のデザイン見本ファイル（`.png`）を必ず参照すること。
        - `ログイン画面見本.png` / `登録画面見本.png` / `投稿画面見本.png` / `投稿作成ページ.png`
    - AIエージェントおよび開発者は、UIコンポーネントを実装・修正する際、これらのモックアップからレイアウトやスタイル（Tailwind CSSクラス）を抽出し、可能な限り忠実に画面を再現すること。
- **スタイリング:** モバイルファーストを徹底し、ユーティリティクラスに直接記述する。`@apply` は原則禁止。
- **Shadcn/ui 活用:** UI コンポーネントには Shadcn/ui を積極的に採用し、一貫性のあるデザインを構築する。

### バックエンド設計思想 (Thin Controller & API Resource)

- **役割分担の徹底:** コントローラーの責務を最小限に抑え、以下のコンポーネントに処理を委譲する。
    - バリデーション → `FormRequest`
    - ビジネスロジック・クエリ → `Service`
    - レスポンス整形 → `JsonResource`
- **命名規則:** クラス名は `PascalCase`、メソッド・変数は `camelCase`、DBカラムは `snake_case` を厳守。
- **use 文の積極使用と FQCN の排除:**
    - コード内でのフルパス指定（例: `\App\Models\User`）は原則禁止。
    - 必ずファイルの冒頭で `use` 文を宣言し、クラス名のみで参照すること。

### 🛠️ 共通ルール

- **論理式の明示的なグルーピング:** 演算子の優先順位に依存せず、括弧 `()` を使用して判定の塊を明示する。
    - **Good:** `return isAxiosError(e) && (e.response !== undefined);`

---

## 📂 ディレクトリ構造と責務 (Directory Structure)

### フロントエンド (Next.js - `src/`)

「App Router」の規約に従う。

- `app/`: ルーティングと Page/Layout コンポーネント（SSR/RSCを意識）
- `components/`:
    - `ui/`: Shadcn などの共通基本UI部品
    - `features/`: ドメイン・機能ごとの複雑なコンポーネント
- `lib/`: 便利関数 (`utils.ts`)、APIフェッチクライアント、外部ライブラリ設定
- `types/`: TypeScript 型定義（自動生成された `schema.d.ts` など）
- `hooks/`: カスタムフックや React Query のクエリフック
- `store/`: Zustandストア

### バックエンド (Laravel - `app/`)

- `Http/Controllers/API/`: Vue/React 向けにJSONのみを返すAPIエンドポイント。
- `Http/Requests/`: FormRequest による入力データ検証。
- `Http/Resources/`: JSONレスポンスの形を整える API Resource。
- `Services/`: ビジネスロジック、複雑なクエリの集約。
- `Models/`: Eloquent モデル、リレーション、キャスト定義。

---

## 📝 コメント規則 (Commenting Rules)

### 1. 意図を語る (Intent over Implementation)

- 「何をしているか」ではなく、**「なぜ」その処理が必要なのか**（背景や目的）を書く。

### 2. 自己文書化コードの優先 (Refactor First)

- コメントを書く前に、コード自体を分かりやすくできないか検討する。
- **説明変数 (Explanatory Variables) の導入:** 複雑な条件式やワンライナーを避け、中間ステップを変数として定義することで、コード自体をロジックの解説にする。
- **明快なコード (Clear Code) vs 賢すぎるコード (Clever Code):** AI が生成しがちな過度に複雑な処理や型ハックを避け、人間の認知負荷を下げる記述を最優先する。
- **型エイリアスによる意図の明示:** 複雑な交差型や型ハックが必要な場合は、意味のある名前を付けた型エイリアス（例: `LooseStringAutocomplete<T>`）に切り出し、その Why をコメントする。
- **変数名・定数化:** マジックナンバーを排除し、意味のある名前をつける。
- **関数抽出:** 複雑な条件式は、判定意図が伝わる名前の関数に抽出する。

### 3. ゾンビコードの禁止

- 不要になったコメントアウトコードは残さずすぐに削除する。Gitの履歴を信頼すること。

### 4. AI エージェントへの文脈提供 (Prompts as Documentation)

- コメントは未来の AI エージェントへの「背景文脈の提供」としても機能する。変更の意図が明確であるほど、AI 修理の精度が向上する。
- `TODO:`, `FIXME:`, `NOTE:` プレフィックスを効果的に使用する。
