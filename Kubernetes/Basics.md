## 認証（Authentication）

### 定義

- 認証（AuthN）は、主体が「誰であるか」を検証するプロセスである [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html?utm_source=chatgpt.com)。
    

### 主な手法

- **パスワード認証**：最も一般的な方式。パスワード＋ソルトを用いてハッシュ化し、認証を行う [Auth0](https://auth0.com/docs/authenticate?utm_source=chatgpt.com)。
    
- **トークンベース認証**：JWT などのトークンを発行し、以降のリクエストで認証情報として利用する [Auth0](https://auth0.com/docs/get-started/identity-fundamentals/authentication-and-authorization?utm_source=chatgpt.com)。
    
- **多要素認証（MFA／2FA）**：パスワード＋ワンタイムパスワード（TOTP）や SMS、ハードウェアトークンなど複数の要素を組み合わせる [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Multifactor_Authentication_Cheat_Sheet.html?utm_source=chatgpt.com)。
    

---

## 認可（Authorization）

### 定義

- 認可（AuthZ）は、認証済み主体が「何にアクセスできるか」を判断・付与するプロセスである [owasp.org](https://owasp.org/www-community/Access_Control?utm_source=chatgpt.com)。
    

### アクセス制御モデル

- **RBAC（Role‑Based Access Control）**
    
    - ユーザーに「役割（Role）」を割り当て、役割に紐づく権限を一括管理するモデル [Permit](https://www.permit.io/blog/rbac-vs-abac?utm_source=chatgpt.com)。
        
- **ABAC（Attribute‑Based Access Control）**
    
    - ユーザー属性・リソース属性・環境属性など多様な「属性（Attribute）」を組み合わせて柔軟にポリシーを定義するモデル [NIST出版物](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-162.pdf?utm_source=chatgpt.com)。
        
- **ハイブリッド**
    
    - RBAC のシンプルさと ABAC の柔軟性を組み合わせるケースも増えている [AWS Documentation](https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/access-control-types.html?utm_source=chatgpt.com)。
        

---

## 認証と認可の違い

- 認証は「本人確認（Who?）」、認可は「権限付与（What?）」を担う [Okta](https://www.okta.com/identity-101/authentication-vs-authorization/?utm_source=chatgpt.com)。
    
- どちらも単独ではなく、組み合わせて初めてアプリケーションの安全なアクセス制御が実現できる。
    

---

## セキュリティのベストプラクティス

1. **常に TLS/HTTPS を利用**
    
    - 通信経路を暗号化し、中間者攻撃（MITM）を防ぐ。
        
2. **最小権限の原則（Least Privilege）を適用**
    
    - ユーザーやサービスに必要最低限の権限のみを付与する [アイデンティティセキュリティ](https://www.sailpoint.com/identity-library/difference-between-authentication-and-authorization?utm_source=chatgpt.com)。
        
3. **トークン管理と失効**
    
    - トークンの有効期限を短くし、リフレッシュトークン失効機能を実装する。
        
4. **ログ監査とモニタリング**
    
    - 認証／認可イベントを記録し、不正アクセスや異常動作を早期に検知する。
        
