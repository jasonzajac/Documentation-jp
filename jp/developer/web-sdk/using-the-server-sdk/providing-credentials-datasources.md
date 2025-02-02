# データ ソースへ資格情報を提供

## 概要

Server SDK では、データ ソースにアクセスするときに使用される一連の資格情報を渡すことができます。

## コード

最初の手順は、__IRVAuthenticationProvider__ インターフェースを実装するクラスを作成することです。
MyAuthenticationProvider と呼ばれるサンプル実装を次に示します:

``` csharp
public class MyAuthenticationProvider : IRVAuthenticationProvider
{
    public Task<IRVDataSourceCredential> ResolveCredentialsAsync(IRVUserContext userContext, RVDashboardDataSource dataSource)
    {
        IRVDataSourceCredential userCredential = null;
        if (dataSource is RVPostgresDataSource)
        {
            userCredential = new RVUsernamePasswordDataSourceCredential("postgresuser", "password");
        }
        else if (dataSource is RVSqlServerDataSource)
        {
            // The "domain" parameter is not always needed and this depends on your SQL Server configuration. 
            userCredential = new RVUsernamePasswordDataSourceCredential("sqlserveruser", "password", "domain");
        }
        else if (dataSource is RVGoogleDriveDataSource)
        {
            userCredential = new RVBearerTokenDataSourceCredential("fhJhbUci0mJSUzi1nIiSint....", "user@company.com");
        }
        else if (dataSource is RVRESTDataSource)
        {
            userCredential = new RVUsernamePasswordDataSourceCredential(); // Anonymous
        }
        return Task.FromResult<IRVDataSourceCredential>(userCredential);
    }
}
```
クラスを作成した後、次の手順は、次のように **AddReveal** 呼び出し (ConfigureServices メソッド) にクラスを登録することです。

```csharp
services
    .AddMvc()
        .AddReveal(builder =>
        {
            builder
            ...
            .AddAuthenticationProvider<MyAuthenticationProvider>()
            ...
        });
``` 

## 実装するクラスの選択

使用できるクラスは 2 つあり、どちらも __IRVDataSourceCredential__ インターフェイスを実装します。以下に詳述するように、データ ソースに応じてクラスを選択する必要があります。

  - クラス __RVBearerTokenDataSourceCredential__ は以下で動作します。

      - アナリティクス ツール (Google アナリティクス)。
    
      - コンテンツ マネージャーとクラウド サービス (Box、Dropbox、Google Drive、OneDrive、SharePoint Online)。

  - クラス __RVUsernamePasswordDataSourceCredential__ は以下と動作します。

      - カスタマー リレーションシップ マネージャ- (Microsoft Dynamics CRM オンプレミスおよびオンライン)。
    
      - データベース (Microsoft SQL Server、Microsoft Analysis Services サーバー、MySQL、PostgreSQL、Oracle、Sybase)。

  - **両クラス**は以下と動作します。

      - その他のデータ ソース (OData フィード、Web Resources、REST API)。

## 認証なし

認証なしで匿名のリソースで作業することがあります。この場合、空のコンストラクタを持つ __RVUsernamePasswordDataSourceCredential__ を使用できます。これは、そのクラスで機能するすべてのデータ ソースに対して実行できます。

上記のサンプルで使用したコード スニペット:

``` csharp
else if (dataSource is RVRESTDataSource)
{
     userCredential = new RVUsernamePasswordDataSourceCredential();
}
```
