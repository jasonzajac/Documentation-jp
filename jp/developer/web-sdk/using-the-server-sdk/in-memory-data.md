# インメモリ データのサポート

## 概要

アプリケーション状態の一環として、ユーザーが要求したレポートの結果や Reveal でまだサポートされていないデータ ソースからの情報 (カスタムデータベースや特定のファイル形式など) にすでにメモリ内にあるデータを使用します。

インメモリは特別なタイプのデータ ソースで、SDK でのみ使用でき、Reveal アプリケーションでそのまま使用することはできません。そのため、\[インメモリ データ ソース\] を直接使用することはできません。以下のように、別の方法に従ってください。

## インメモリ データ ソースの使用

推奨される方法は、**インメモリ データと一致するスキーマを持つデータファイルを定義する**方法です。データ ファイルは、たとえば CSV または Excel ファイルにすることができ、スキーマは基本的にフィールドのリストと各フィールドのデータ型です。

以下の例では、特定のスキーマを使用してデータファイルを作成し、データベースから情報を取得する代わりにメモリ内のデータを使用する方法について詳しく説明します。

## コード例

以下の例では、人事システムに人事メトリクスを表示するダッシュボードを埋め込むために、社内の Employee のリストでインメモリデータを使用します。データベースから Employee のリストを取得するのではなく、メモリ内のデータを使用します。

これらすべてを実現するには、ダミー データを使用して、Reveal アプリでダッシュボードを作成してエクスポートする必要があります。

### Reveal アプリについて
Reveal アプリは、ダッシュボードを作成し、表示させ、およびチームと共有できるビジネス インテリジェンス ツールです。Reveal アプリの詳細については、[**オンラインデモ**](https://app.revealbi.io/)にアクセスするか、[**ヘルプ ドキュメント**](https://help.revealbi.io/jp/welcome.html)を参照してください。

### データ ファイルとサンプル ダッシュボードの準備

簡略化した Employee には以下のプロパティがあります。

  - *EmployeeID*: string
  - *Fullname*: string
  - *Wage*: numeric

**手順:**

1.  同じスキーマで CSV ファイルを作成

    ``` xml
    EmployeeID,Fullname,Wage
    23,John Smith,345.67
    45,Emma Thompson,432.23
    ```

2.  Dropbox や Google Drive など、しファイル共有システムにファイルをアップロード

3.  ダミーデータを使用してダッシュボードを作成 (実際の生産データは後でアプリケーションで提供する予定です)

4.  ダッシュボードをエクスポート (ダッシュボード → エクスポート → ダッシュボード)

### ダッシュボードの可視化と実際のデータの返却

ダミーのデータではなくカスタムのデータを使ってダッシュボードを可視化する必要があります。

1.  [**データ ソースの置き換え**](replacing-data-sources/replacing-data-sources-mssql.md)で説明されているように、インターフェイスを実装し、AspNetCore サービスを構成するときに
    AddReveal() 呼び出しに登録します。

    次に、メソッド **ChangeDataSourceItemAsync** の実装では、次のようなコードを追加する必要があります:

    ``` csharp
    public Task<RVDataSourceItem> ChangeDataSourceItemAsync(IRVUserContext userContext, string dashboardId, RVDataSourceItem dataSourceItem)
    {
        var csvDsi = dataSourceItem as RVCsvDataSourceItem;
        if (csvDsi != null)
        {
            var inMemDsi = new RVInMemoryDataSourceItem("employees");
            return Task.FromResult((RVDataSourceItem)inMemDsi);
        }
        return Task.FromResult((RVDataSourceItem)null);
    }
    ```

    このようにして、ダッシュボード内の CSV ファイルへのすべての参照を、基本的に employees で識別されるインメモリ データ ソースに置き換えます。この ID は後でデータを返すときに使用されます。

2.  実際のデータを返すメソッドを実装します。これを行うには、__IRVDataProvider__ を実装し、それを AddReveal() 呼び出しに登録します。

    ``` csharp
    public class EmbedDataProvider : IRVDataProvider
    {
        public Task<IRVInMemoryData> GetData(IRVUserContext userContext, RVInMemoryDataSourceItem dataSourceItem)
        {
            var datasetId = dataSourceItem.DatasetId;
            if (datasetId == "employees")
            {
                var data = new List<Employee>()
                {
                    new Employee(){ EmployeeID = "1", Fullname="John Doe", Wage = 80325.61 },
                    new Employee(){ EmployeeID = "2", Fullname="Doe John", Wage = 10325.61 },
                };
                return Task.FromResult<IRVInMemoryData>(new RVInMemoryData<Employee>(data));
            }
            else
            {
                throw new Exception("Invalid data requested");
            }
        }
    }
    ```

    Employee クラスのプロパティは CSV ファイルの列とまったく同じ名前であり、データ型も同じです。フィールド名、フィールド ラベル、またはプロパティのデータ型を変更したい場合は、クラス宣言で属性を使用できます。
    
      - RVSchemaColumn 属性を使用してフィールド名やデータ型を変更できます。
    
      - DisplayName 属性を使用してフィールドラベルを変更できます。

<!-- end list -->

``` csharp
public class Employee
{
    [RVSchemaColumn("EmployeeID", RVSchemaColumnType.Number)]
    public string EmployeeID { get; set; }

    [DisplayName("EmployeeFullname")]
    public string Fullname { get; set; }

    [RVSchemaColumn("MonthlyWage")]
    public double Wage { get; set; }
}
```