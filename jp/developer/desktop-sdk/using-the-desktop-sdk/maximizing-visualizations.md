# 可視化の最大化と単一可視化モード

## 概要

Desktop SDK は、ダッシュボードをユーザーに表示する際に最大化した可視化表示を 1 つだけ表示し、更に最初の可視化表示をロックしてユーザーがダッシュボード全体にアクセスできないようにすることができます。

<img src="images/three_divisions_dashboard_maximized.png" alt="Displaying a dashboard with a maximized visualization" class="responsive-img"/>

### サンプル詳細

可視化した 3 つのダッシュボードがあり、それぞれの可視化で会社の異なる部門のデータが表示されていると仮定します (例えば、Marketing、Sales、HR)。

<img src="images/three_divisions_dashboard.png" alt="Displaying a dashboard with three visualizations" class="responsive-img"/>

この例では、業務アプリケーションでこれらの可視化を使用します。各部署のホームページに表示される情報の一部として含めます。

## 可視化の最大化

最大化された可視化でダッシュボードを開くには、revealView.Dashboard プロパティを割り当てた後、__RevealView__ の __MaximizedVisualization__ プロパティを使用する必要があります。
この属性に視覚化を設定しないと、ダッシュボード全体が表示されます。


**RevealView オブジェクトの設定**に示すように、ページに特定のダッシュボードを表示できます。今回は、__MaximizedVisualization__ 属性を設定する必要があります。以下のコード スニペットに示すように、可視化 Sales を使用してください。

``` csharp
var revealView = new RevealView();
using (var fileStream = File.OpenRead(path))
{
    var dashboard = new RVDashboard(fileStream);
    revealView.Dashboard = dashboard;
    revealView.MaximizedVisualization = dashboard.Visualizations.GetByTitle("Sales");
}
```

最初に最大化した可視化表示は Sales というタイトルの可視化になりますが、それでもエンドユーザーはダッシュボードに戻って残りの可視化を表示できます。

## 単一可視化モード

また、最初の可視化をロックして、常に可視化を 1 つのみ表示するようにすることもできます。これにより、ダッシュボードは単一の視覚化ダッシュボードのように機能します。これが [単一可視化モード] の概念です。

単一可視化モードをオンにするには、以下に示すように、
__SingleVisualizationMode__
プロパティを true に設定します。

``` csharp
revealView.SingleVisualizationMode = true;
```

この 1 行を追加すると、ダッシュボードは単一の視覚化ダッシュボードとして機能します。各部門のホームページでも同じことができます。__dashboard.Visualizations.GetByTitle()__ の可視化のタイトルを適切なタイトルに置き換えてください。


### ロックされた可視化を動的に変更

ページを再ロードせずに、表示されている単一の表示形式を動的に変更することもできます。ユーザーの観点から見ると、アプリは部門のセレクターと最大化された視覚化を備えた単一ページのアプリケーションになります。ユーザーがリストから 1 つの部門を選択すると、最大化された視覚化が更新されます。

以下では、
__RevealView__ の __MaximizeVisualization__ メソッドを使用するか、__MaximizedVisualization__ プロパティを設定してこのシナリオを実現できます。

``` csharp
private void MaximizeVisualization(string title)
        {
            revealView.MaximizeVisualization(revealView.Dashboard.Visualizations.GetTitle(title));
            //or set the property
            revealView.MaximizedVisualization = revealView.Dashboard.Visualizations.GetTitle(title);
        }
```

最後に、カスタム コントロールを上記の方法で接続します。それにより、アプリケーションの選択が変わったときに視覚化が最大化されます。

注意事項:

  - ダッシュボードで可視化のリストを繰り返すことで、ボタンのリストを動的に生成できます。詳細については、__RVDashboard.Visualizations__ をご覧ください。
  - SDK とともに配布されている UpMedia WPF アプリケーションの UpMedia に、**Manufacturing.xaml.cs** の動作を確認できる例があります。このサンプルビューでは、画面下部にすべての可視化がトグル ボタンのリストとして表示されます。
