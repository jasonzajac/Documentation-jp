# データ ソースの置き換え  (MS SQL サーバー)

## 概要

ダッシュボードのデータを (Reveal SDKによって) ロードして処理する前に、ダッシュボードの各視覚化に使用される構成またはデータをオーバーライドできます。

__RevealSdkSettings__ の DataSourceProvider プロパティを設定する必要があります。

インターフェイス __IRVDataSourceProvider__ を実装するクラスは、ダッシュボードで使用されているデータ ソースを置換または変更することができます。

## 使用事例

以下に、一般的なユースケースのリストがあります。

  - 現在のユーザー、またはアプリが userId、division、company、customer などのその他の属性に応じて、使用するデータベースの名前を変更できます。これにより、マルチテナント データベースからデータを取得する単一のダッシュボードを持つことができます。

  - 使用されているテーブルの名前、ロードするファイルのパスなどを変更することができます。ユースケースは上記のものと似ています。

  - データ ソースをメモリ内のデータ ソースに置き換えることができます。Reveal App はインメモリ データ ソースをサポートしていないため、CSV ファイルを使用してダッシュボードを設計し、このコールバックを使用して CSV データ ソースをインメモリデータ ソースに置き換えることができます。このシナリオでは、データは実際にメモリからロードされます (またはカスタムデータローダを使用して)。インメモリ データ ソースの使用方法の詳細については、[**インメモリ データのサポート**](in-memory-data.html)を参照してください。

## コード

次のコード スニペットは、ダッシュボードのデータ ソース アイテムを置き換える方法の例を示しています。メソッド __ChangeDataSourceItemAsync__ は、ダッシュボードがデータを取得する必要があるたびに呼び出されます。

``` csharp
public class SampleDataSourceProvider : IRVDataSourceProvider
{
   public Task<RVDataSourceItem> ChangeDataSourceItemAsync(RVDataSourceItem dataSourceItem)
   {
        var sqlServerDsi = dataSourceItem as RVSqlServerDataSourceItem;
        if (sqlServerDsi != null)
        {
            // Change SQL Server host and database
            var sqlServerDS = (RVSqlServerDataSource)sqlServerDsi.DataSource;
            sqlServerDS.Host = "10.0.0.20";
            sqlServerDS.Database = "Adventure Works";

            // Change SQL Server table/view
            sqlServerDsi.Table = "Employees";
            return Task.FromResult((RVDataSourceItem)sqlServerDsi);
        }

        // Fully replace a data source item with a new one
        if (dataSourceItem.Title == "Top Customers")
        {
            var sqlDs = new RVSqlServerDataSource();
            sqlDs.Host = "salesdb.local";
            sqlDs.Database = "Sales";

            var sqlDsi = new RVSqlServerDataSourceItem(sqlDs);
            sqlServerDsi.Table = "Customers";

            return Task.FromResult((RVDataSourceItem)sqlServerDsi);
        }

        // return the original data source item
        return Task.FromResult((RVDataSourceItem)dataSourceItem);
    }
}
```

上記の例では、次の 2 つの置換が実行されます。

  - MS SQL Server データベースを使用するすべてのデータ ソースは、ハードコードされたサーバー 10.0.0.20、Adventure Works データベース、および Employees テーブルを使用するように変更されます。

    > [!NOTE]
    > これは単純化されたシナリオで、同じテーブルからデータを取得するためにすべての可視化を置き換えても、現実のシナリオとしては意味がありません。実際のアプリケーションでは、userId、dashboardId、データ ソース自体の値 (サーバー、データベースなど) などの追加情報を使用して新しい値を推測します。

  - Top Customers というタイトルのすべてのデータ ソース アイテムは、Customers テーブルを使用して salesdb.local サーバーの Sales データベースからデータを取得する新しい SQL Server データ ソースに設定されます。

__IRVDataSourceProvider__ の実装に加え、__RevealSdkSettings__ の __DataSourceProvider__ プロパティに実装を設定します。

``` csharp
RevealSdkSettings.DataSourceProvider = new SampleDataSourceProvider();
```
