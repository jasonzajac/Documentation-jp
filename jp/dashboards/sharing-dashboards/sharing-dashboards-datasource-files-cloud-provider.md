# クラウド ファイルをデータ ソースとするダッシュボードを共有する

ダッシュボードを他の Reveal ユーザーと共有する場合は、ダッシュボードで表示形式を作成するために使用したデータ ソースを考慮する必要があります。データ ソースがクラウド プロバイダーにアップロードされたファイルである場合、ダッシュボードを共有するユーザーは、ファイルへのアクセスを許可しない限り、ダッシュボードを開くことができない場合があります。

クラウド プロバイダーのデータ ソース ファイルへのアクセスを許可すると、このファイルへの**共有可能なリンク**が作成されます。このファイルをデータ ソースとして使用するダッシュボードは、他のユーザーと共有すると正しく表示されます。リンク自体は、Reveal ユーザーと共有されませんが、暗号化されてサーバーに保存されます。

次の場合に共有可能なリンクを作成できます:

  - [クラウド プロバイダーにアップロードされたファイル](#cloud-data-source-file) を表示形式のデータ ソースとして選択する場合。

  - クラウド ファイル データ ソースを使用する[ダッシュボードを共有する](#grant-access-shared-dashboard)場合。

  - [ワークスペースに](#save-dashboard-workspace)ダッシュボードを保存する場合。

<a name='cloud-providers'></a>
## サポートされるクラウド プロバイダー

次のクラウド プロバイダーにアップロードされたファイルを使用するダッシュボードへのアクセスを許可できます:

  - Google Drive

  - One Drive

  - Dropbox

<a name='cloud-data-source-file'></a>
## クラウド ファイルをデータ ソースとして選択する場合のアクセス許可の付与

データ ソースを選択した時点でクラウド ファイルへのアクセスを許可して、表示形式を構築できます。[サポートされているクラウド プロバイダー](#cloud-providers)のいずれかにアップロードされたファイルを選択すると、次のダイアログが表示されます。

<img src="images/allow-access-box-sharing.png" alt="Allow access checkbox in the Set up your file dialog" class="responsive-img"/>

**[アクセスの許可]** チェックボックスが選択されていることを確認してください。選択されていない場合、共有可能なリンクは無効になります。

<a name='grant-access-shared-dashboard'></a>
## ダッシュボードの共有時にアクセスを許可する

他のユーザーと共有するときに、共有可能なリンクが無効になっているクラウド ファイルを使用するダッシュボードへのアクセスを許可できます。

1.  ダッシュボードの*オーバーフロー ボタン*をクリック/タップします。

2.  **[共有]** を選択します。

3.  次のダイアログが開きます:

    <img src="images/grant-access-share-dashboard-dialog.png" alt="A dialog to grant access to cloud file data sources" width="40%"/>

    **[アクセスの許可]** をクリック/タップして、[ダッシュボードを共有する](share-a-dashboard.html)ユーザーの選択に進みます。

<a name='save-dashboard-workspace'></a>
## ワークスペース内のクラウド ファイル データ ソースを含むダッシュボードの保存

クラウド ファイルをデータ ソースとして使用する 1 つ以上の表示形式を含むダッシュボードをワークスペースに保存することができます。これらのデータ ソース ファイルへの共有可能なリンクが作成されていない (無効になっている) 場合、Reveal はアクセスを許可するように求めます。

<img src="images/save-dashboard-disabled-link-workspace.png" alt="Allow access to a dashboard with a disabled link when saving in a workspace" class="responsive-img"/>

これにより、ワークスペースに保存したダッシュボードをすべてのワークスペース メンバーが確実に表示できるようになります。

## 関連項目

共有されているダッシュボードのコンテンツが表示されない場合は、[クラウド ファイルを使用した共有ダッシュボードへのアクセスのリクエスト](request-access-shared-dashboards-cloud-files.html)をお読みください。
