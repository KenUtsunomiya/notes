## OAuth 2.0 とは 

- パスワードを共有せずに第三者クライアントに限定的かつ失効可能な**アクセス権（トークン）を委譲する**業界標準プロトコル
- Webアプリケーション、モバイルアプリ、SPA、IoTデバイスなど多彩なクライアント環境に対応するために複数の認可フロー（グラントタイプ）を提供する

## 主要コンポーネント

### リソース所有者

- 実際のデータ所有者。ほとんどの場合はエンドユーザー

### クライアント

- ユーザーに代わって認可サーバーにアクセス権を要求するアプリケーション

### 認可サーバー

- ユーザー認証とアクセストークン発行を担うサーバー

### 保護対象リソース 

- 発行されたトークンを検証し、保護されたリソースを提供するAPIサーバー

## 認可フロー

### Authorization Code Grant

![[oauth.png]]

1. ユーザーがクライアントにリソースへのアクセスを委譲するための Access Request を送る（e.g. Google Account でログインする）
   
2. クライアントはユーザーを認可サーバーの `/authorize` エンドポイントへリダイレクトし、`response_type=code` や `client_id`、`redirect_uri`、`scope`、　　`state` をリクエストパラメータに含めて送信する
   
3. 認可サーバーがユーザーへ、クライアントが要求するスコープへの同意を求めるリクエストを送信する
   
4. ユーザーがログインし、要求されたスコープへ同意する
   
5. 認可サーバーが、クライアントが指定した `redirect_uri` に対して一時的な認可コードをクエリパラメータに含めて送信する
   
6. クライアントが、認可コードとクライアントシークレットを `/token` エンドポイントに POST リクエストを送信する
```http
POST /token HTTP/1.1
Host: authorization-server.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=SplxlOBeZQQYbYS6WxSbIA&
redirect_uri=https%3A%2F%2Fclient.example.com%2Fcb&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET
```

7. アクセストークンとリフレッシュトークンをクライアントに送信する
```json
{
  "access_token":"SlAV32hkKG",
  "token_type":"Bearer",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA"
}
```

8. クライアントが、`Authorization: Bearer <access_token>` ヘッダーを付与してリソースサーバーにアクセスリクエストを送信する
   
9. リソースサーバーがクライアントにリソースを返却する
