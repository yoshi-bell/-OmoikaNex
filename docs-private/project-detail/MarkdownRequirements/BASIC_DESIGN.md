# Basic Design (基本設計書)

> [!NOTE]
> アプリケーションのルーティング、画面遷移、バリデーションルール等の基本設計を定義します。
> この仕様に基づいてバックエンドのAPIとフロントエンドのコンポーネントが実装されます。
> ※モデル定義は `SCHEMA.md` に統合済みのため省略しています。

## 1. Route & Controller (画面遷移とAPI処理)

本プロジェクトは Next.js (App Router) と Laravel 12 (API) の完全分離構成であるため、フロントエンドの画面パスとバックエンドのAPIパスを個別に定義します。

### Frontend (Next.js 15) Routes

| 画面名称 (Screen Name) | URL パス (Path)    | Next.js コンポーネント (App Router) | 認証 (Auth) | 説明 (Description)                             |
| :--------------------- | :----------------- | :---------------------------------- | :---------: | :--------------------------------------------- |
| **会員登録**           | `/register`        | `app/(auth)/register/page.tsx`      |    不要     | ユーザー登録フォームを表示                     |
| **ログイン**           | `/login`           | `app/(auth)/login/page.tsx`         |    不要     | ログインフォームを表示                         |
| **ホーム (TL)**        | `/` または `/home` | `app/(main)/home/page.tsx`          |    必要     | 全体タイムラインと投稿フォームを表示           |
| **フォローTL**         | `/following`       | `app/(main)/following/page.tsx`     |    必要     | フォロー中のユーザーのみの投稿を表示           |
| **ツイート詳細**       | `/tweet/[id]`      | `app/(main)/tweet/[id]/page.tsx`    |    必要     | 特定の投稿の詳細とコメント一覧、返信UIを表示   |
| **プロフィール**       | `/user/[id]`       | `app/(main)/user/[id]/page.tsx`     |    必要     | 特定ユーザーのプロフィールおよび投稿一覧を表示 |

### Backend (Laravel 12) API Endpoints

| API機能               | メソッド | API パス (Path)            | コントローラー (Controller) | 認証 | 説明 (Description)                                     |
| :-------------------- | :------: | :------------------------- | :-------------------------- | :--: | :----------------------------------------------------- |
| **会員登録**          |   POST   | `/api/register`            | `AuthController@register`   | 不要 | ユーザー作成・トークン発行                             |
| **ログイン**          |   POST   | `/api/login`               | `AuthController@login`      | 不要 | 認証検証・トークン発行                                 |
| **ログアウト**        |   POST   | `/api/logout`              | `AuthController@logout`     | 必要 | 現在のトークンを破棄                                   |
| **ユーザー情報**      |   GET    | `/api/user`                | `UserController@current`    | 必要 | ログイン中のユーザー情報を取得                         |
| **ツイート投稿**      |   POST   | `/api/tweets`              | `TweetController@store`     | 必要 | 新規ツイートまたはコメント（`parent_id` 含む）を作成   |
| **TL取得 (全体)**     |   GET    | `/api/tweets`              | `TweetController@index`     | 必要 | 全ユーザーのツイートをページネーション(Cursor)付き取得 |
| **TL取得 (フォロー)** |   GET    | `/api/tweets/following`    | `TweetController@following` | 必要 | フォロイーのツイートのみを取得                         |
| **ツイート詳細**      |   GET    | `/api/tweets/{tweet}`      | `TweetController@show`      | 必要 | ツイート単体および子コメントを取得                     |
| **ツイート削除**      |  DELETE  | `/api/tweets/{tweet}`      | `TweetController@destroy`   | 必要 | 自身のツイートを削除                                   |
| **いいね・解除**      | POST/DEL | `/api/tweets/{tweet}/like` | `LikeController@toggle`     | 必要 | 該当ツイートへのいいね状態をトグル処理                 |
| **フォロー・解除**    | POST/DEL | `/api/users/{user}/follow` | `FollowController@toggle`   | 必要 | 対象ユーザーへのフォロー状態をトグル処理               |
| **プロフィール取得**  |   GET    | `/api/users/{user}`        | `UserController@show`       | 必要 | ユーザー詳細と統計情報（フォロー数等）を取得           |

---

## 2. Validation Rules (バリデーション定義)

フロントエンド (Zod) とバックエンド (FormRequest) で一貫して適用するバリデーションルールです。

| 対象リクエスト / フォーム | フィールド (Field) | ルール・要件 (Rules & Requirements)                               | エラーメッセージ例 (Error Message)                          |
| :------------------------ | :----------------- | :---------------------------------------------------------------- | :---------------------------------------------------------- |
| **Login**                 | `email`            | 必須, メールアドレス形式                                          | 「メールアドレスを正しい形式で入力してください」            |
|                           | `password`         | 必須, 最低8文字                                                   | 「パスワードは8文字以上で入力してください」                 |
| **Register**              | `name`             | 必須, 1〜50文字以内                                               | 「ユーザー名は50文字以内で入力してください」                |
|                           | `email`            | 必須, メールアドレス形式, **同一メールの重複禁止 (Unique:users)** | 「このメールアドレスは既に登録されています」                |
|                           | `password`         | 必須, 最低8文字, **アルファベットと数字を両方含むこと**           | 「パスワードは英数字を混合して8文字以上で入力してください」 |
| **Tweet / Comment**       | `content`          | 必須, 1〜140文字以内                                              | 「投稿は1文字以上、140文字以内で入力してください」          |
|                           | `parent_id`        | 任意, 数値, 存在チェック (Exists:tweets) ※コメントの場合のみ付与  | (フロント起因の不正操作防止用。通常ユーザー目に触れない)    |
| **Profile Update**        | `profile_text`     | 任意, 0〜160文字以内                                              | 「自己紹介文は160文字以内で入力してください」               |
