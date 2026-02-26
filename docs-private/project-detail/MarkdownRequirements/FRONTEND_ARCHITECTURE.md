# Frontend Architecture (フロントエンド画面・UI設計)

> [!NOTE]
> このドキュメントは `project-detail/ViewComponent一覧.csv` の画面・コンポーネント仕様を、
> 今回のプロジェクト基盤である **Next.js 15 (App Router)** 向けに再設計したものです。
> 画面パスと、機能を構成するUIコンポーネントの構造を定義します。

## 1. 画面設計 (App Router Pages)

| App Component パス (ファイル名)  | ルートパス (URL) | 種別    | 説明                                                                           | デザイン見本 (Mockup)                                        |
| :------------------------------- | :--------------- | :------ | :----------------------------------------------------------------------------- | :----------------------------------------------------------- |
| `app/(auth)/login/page.tsx`      | `/login`         | Public  | ログイン画面                                                                   | `ログイン画面見本.png`                                       |
| `app/(auth)/register/page.tsx`   | `/register`      | Public  | 新規会員登録画面                                                               | `登録画面見本.png`                                           |
| `app/(main)/layout.tsx`          | (Layout)         | Private | ログイン後の共通レイアウト（サイドバーナビゲーション等）                       | （各画面見本を参照）                                         |
| `app/(main)/home/page.tsx`       | `/home` (or `/`) | Private | 全体タイムラインと新規ツイート投稿フォームを表示                               | `投稿画面見本.png`                                           |
| `app/(main)/following/page.tsx`  | `/following`     | Private | フォロー中のユーザーのツイートのみを抽出したタイムライン                       | `投稿画面見本.png` と同等のレイアウト                        |
| `app/(main)/tweet/[id]/page.tsx` | `/tweet/{id}`    | Private | 特定のツイートの詳細表示、およびコメント（返信）の一覧・投稿フォーム           | `投稿作成ページ.png`                                         |
| `app/(main)/user/[id]/page.tsx`  | `/user/{id}`     | Private | 特定ユーザーのプロフィール情報、フォロー状態、およびそのユーザーのツイート一覧 | （プロフ見本なし：他画面のコンポーネントを組み合わせて実装） |

## 2. コンポーネント設計 (UI Components)

本プロジェクトでは、「共通UI部品（Shadcn）」と「特定機能に依存する部品（Features）」を分離して管理し、保守性を高めます。

### 2-1. 共通レイアウト・ナビゲーション (components/layouts)

| Component ファイル名             | 説明                                                                           | 主な使用箇所            |
| :------------------------------- | :----------------------------------------------------------------------------- | :---------------------- |
| `components/layouts/Sidebar.tsx` | 左側の主なナビゲーション（ホーム、プロフィールへのリンク）と共通「投稿」ボタン | `app/(main)/layout.tsx` |
| `components/layouts/Header.tsx`  | モバイル向けの上部ヘッダー（ハンバーガーメニュー等）                           | `app/(main)/layout.tsx` |

### 2-2. 共通UI要素 (components/ui - Shadcn UI)

ボタン、入力フォーム、ダイアログなど、機能を持たない純粋なUI部品です。（例の一部）
| Component ファイル名 | 説明 |
| :--- | :--- |
| `components/ui/button.tsx` | 各種アクションボタン（バリアント管理） |
| `components/ui/input.tsx` | テキスト入力フィールド |
| `components/ui/textarea.tsx` | ツイート制作用の複数行入力フィールド |
| `components/ui/avatar.tsx` | ユーザーアイコンの表示 |
| `components/ui/toast.tsx` | いいねエラーなどの通知用トースト |

### 2-3. 機能固有コンポーネント (features/)

特定のビジネスロジック（API通信、状態管理）と密結合するコンポーネント群です。

#### Auth (認証系)

| Component ファイル名                        | 説明                                  | 主な使用箇所                   |
| :------------------------------------------ | :------------------------------------ | :----------------------------- |
| `features/auth/components/LoginForm.tsx`    | ログイン処理（React Hook Form + Zod） | `app/(auth)/login/page.tsx`    |
| `features/auth/components/RegisterForm.tsx` | 新規登録処理（React Hook Form + Zod） | `app/(auth)/register/page.tsx` |

#### Tweet & Timeline (ツイート系)

| Component ファイル名                      | 説明                                                                               | 主な使用箇所                     |
| :---------------------------------------- | :--------------------------------------------------------------------------------- | :------------------------------- |
| `features/tweet/components/TweetCard.tsx` | ツイート単体のUI。投稿者、本文、日時を表示し、「いいね」「削除」のアクションを持つ | TL、詳細、プロフ画面             |
| `features/tweet/components/TweetForm.tsx` | 新規投稿およびコメント用の入力フォーム                                             | `home`, `tweet/[id]`             |
| `features/tweet/components/Timeline.tsx`  | 無限スクロール(React Query)を管理し、複数の `TweetCard` をレンダリングするラッパー | `home`, `following`, `user/[id]` |

#### User & Profile (ユーザー・フォロー系)

| Component ファイル名                         | 説明                                                  | 主な使用箇所                    |
| :------------------------------------------- | :---------------------------------------------------- | :------------------------------ |
| `features/user/components/ProfileHeader.tsx` | ユーザー情報の表示と「フォロー」ボタン                | `app/(main)/user/[id]/page.tsx` |
| `features/user/components/FollowButton.tsx`  | フォロー/アンフォロー状態を管理・トグルする独立ボタン | `ProfileHeader.tsx`             |
