# 🚀 【SNSアプリ】次世代フルスタック開発ロードマップ

**テーマ：「過去の血と汗の結晶（型安全基盤）を初手からフル装備した最強のTwitterクローン開発」**

## アーキテクチャ構成

- **バックエンド:** Laravel 12 (API専用 / ステートレス)
- **認証方式:** トークン認証 (Laravel Sanctum API Token / HttpOnly Cookie + BFF層)
- **フロントエンド:** Next.js 15 (App Router) + TypeScript
- **コンポーネント / UI:** Tailwind CSS, Shadcn UI
- **状態管理 (State):** Zustand (グローバルUI状態), React Query (サーバー非同期取得/キャッシュ)
- **型・バリデーション:** Zod, React Hook Form (RHF), openapi-typescript

---

## 🏗️ Phase 1: インフラと「型同期」パイプラインの初期構築 (Day 1)

過去、手動での型定義による不整合に苦しんだ経験を活かし、**「API仕様＝フロントエンドの型」**となるパイプラインを開発の**初日**に構築する。

- **Backend (Laravel)**
    - Laravel 12のインストールとAPIルーティングの設定
    - Sanctum トークン認証の基盤設定 (Session Cookieベースではない)
    - `Scribe` (または `Scramble`) パッケージの導入によるOpenAPI自動生成準備
- **Frontend (Next.js)**
    - Next.js App Router (TypeScript, Tailwind) のセットアップ
    - `openapi-typescript` によるAPI仕様の型アノテーション自動生成 (`codegen` のコマンド化)
- **📝 チェックポイント**
    - この時点では画面は作らない。簡単な `/api/me` を作成し、バックエンドの変更がフロントの `schema.d.ts` に自動同期されることを確認する。

## 🛡️ Phase 2: 「鉄壁のフォーム基盤」の構築 (Day 2-3)

過去の課題であった「useStateだらけのフォーム」と「バックエンド依存のバリデーション」を排除し、初手からZodとReact Hook Formで構成する。

- **フル装備フォーム基盤の実装**
    - Zod: `schemas.ts` に「IDブランド型（Brand Types）」「ログイン」「ユーザー登録」のフロントエンド検証スキーマを定義。
    - `React Hook Form (RHF) + @hookform/resolvers/zod`: Zodのバリデーションルールを統合した強力なカスタムフォームフック (`useAuthForm` など) の作成。
    - フォームエラーメッセージとUIの共通レイヤー作成。
- **📝 チェックポイント**
    - 以降、どれほど複雑なフォーム（ツイート送信・プロフィール編集など）を追加しても「Zod定義 → RHFに渡す」という一本道になっていることを確認する。

## 🌐 Phase 3: データフェッチと「状態管理」の確立 (Day 4-5)

SWRの `useData` で苦労した「通信データの管理」と「状態の整合性」を、React QueryとZustandを用いた現代的アプローチで解決する。

- **サーバー・ステート管理: React Query (TanStack Query)**
    - SNSのタイムライン（無限スクロール・Cursor Pagination）や「いいね」の取得用非同期フックを作成。
    - OpenAPIの自動生成型と紐付け、完全に型安全なクエリ基盤を確立する。
- **クライアント・ステート管理: Zustand**
    - 「現在のログインユーザー情報」「ツイート投稿モーダル（ウィンドウ）の開閉状態」「グローバルトースト通知」等を一元管理。
    - `useState` のカスケード（バケツリレー）を完全排除した設計にする。

## 🚀 Phase 4: コア機能（SNSロジック）の爆速開発 (Day 6〜)

強力な基盤（型付きAPI、RHFフォーム、Queryキャッシュ）が完成しているため、ここからはビジネスロジックの開発のみに集中する。

1. **認証機能**: HttpOnly Cookieを経由させたセキュアなBFF(Backends For Frontends)トークン管理、サインアップ・サインイン実装。
2. **ツイート機能**: テキスト・画像投稿の実装。
3. **タイムライン・フィード**: 自分とフォロー中のユーザーの時系列投稿一覧（Infinite Scroll）。
4. **エンゲージメント機能**: 多対多リレーションを利用した「いいね」および「リポスト（リツイート）」。
5. **フォロー機能**: ユーザーテーブルに対する自己参照（Self-Referencing）多対多テーブルを使ったフォロー/フォロワーロジック。

## 💎 Phase 5: 高度なUI/UXとパフォーマンス・チューニング

型・安全性が担保されている土台の上で、仕上げのUX向上とバックエンドの最適化を行う。

- **楽観的UI更新 (Optimistic Update)**:
    - React Queryの機能を利用し、「いいねボタン」などをタップした瞬間にAPI結果を待たずにUI上でハート色やカウント数を更新する最高のUXを実現する。
- **N+1問題の根絶 (Eager Loading)**:
    - Laravel側で Strict Mode (`Model::preventLazyLoading()`) を有効化。
    - `withCount()` や `with()` メソッドを的確につかい、複雑なタイムライン取得クエリを最適化・チューニングする。

---

### プロジェクト総括 (目標・狙い)

過去のプロジェクトにおいて「リファクタリングによる負債解消の手法」として学習・適用した高度な技術(Zod, OpenAPI型生成, Brand型等)を、すべて**「アーキテクチャの基本設計段階からフロントロードすること」**で、最凶の堅牢性と最速の開発体験を実現する。
このプロセスを経ることで、FindyでのTypeScript・PHP双方の技術偏差値を圧倒的スピードで向上させることを見込む。
