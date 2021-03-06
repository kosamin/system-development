## Azure AD B2C　とは
https://docs.microsoft.com/ja-jp/azure/active-directory-b2c/openid-connect
https://qiita.com/TakahikoKawasaki/items/f2a0d25a4f05790b3baa#%E8%AA%8D%E8%A8%BC%E3%81%A8%E8%AA%8D%E5%8F%AF

あるサービスに対して、企業と顧客の間で使われるIDを提供し、認証をサポートする顧客IDアクセス管理ツール（CIAM）  
顧客は、自分のSNSアカウントを使用して、webサービスやAPIにアクセスできる。  
ウェブアプリ、モバイルアプリ、APIの中央認証機関として機能させることで、全システムに認証機能を構築できる。  

メリット  
ソーシャルログイン認証を実装するとき、各企業に対して実装する必要があるが、AAD B2Cは1つで、複数のソーシャルログイン認証を管理できる。  

## OpenID Connect　とは
- IDトークンの要求方法と、その要求に対する応答方法の仕様のこと
- OAuth2.0規格の拡張仕様
- IDトークンを使って認証と認可の両方行うことができ、OAuthよりも高いセキュリティを持つ。

IDトークンを発行するサーバ　＝OpenIDプロバイダー
1. クライアントアプリがOpenIDプロバイダに対してIDトークンをリクエストする。
2. OpenIDプロバイダがユーザに認証を行う。
3. 本人確認が適切に完了できた場合、OpenIDプロバイダはIDトークンを発行する。
4. OpenIDプロバイダはクライアントアプリにIDトークンをレスポンスする。

***IDトークンを要求するリクエストは、scope リクエストパラメータに openid を含める必要がある。***


## OAuth 2.0　とは
- アクセストークンの要求方法と、その要求に対する応答方法の仕様のこと
- アクセスの認可に特化したプロトコル

ポイント  
第三者のアプリケーションにユーザのID,パスワードを渡さない  
アクセストークンを発行するサーバ　＝　認可サーバ  

1. クライアントアプリが認可サーバに対してアクセストークンを要求する
2. 認可サーバは、要求されている権限をユーザに与えるか確認する。
3. ユーザが権限を与えることを了承した場合、認可サーバはアクセストークンを発行する。
4. クライアントアプリにアクセストークンをレスポンスする。