---
title: 予測の作成 (基本的なデータ マイニング チュートリアル) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- analysis-services
ms.topic: conceptual
ms.assetid: a8410ed2-bb98-4d51-a9eb-b239be1201c2
author: minewiskan
ms.author: owend
manager: craigg
ms.openlocfilehash: 3940f7c214ab3f9d5d48e83acef5ed2237ceeac3
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48206532"
---
# <a name="creating-predictions-basic-data-mining-tutorial"></a>予測の作成 (基本的なデータ マイニング チュートリアル)
  予測クエリ ビルダーを使用して予測を生成できますし、マイニング モデルの精度をテストし、結果に満足していることにしましたが後、**マイニング モデル予測**データ マイニング モデル タブデザイナー。  
  
 予測クエリ ビルダーには 3 つのビューがあります。 **デザイン**と**クエリ**ビュー、ビルドして、クエリを確認します。 クエリを実行しで結果を表示することができますし、**結果**ビュー。  
  
 すべての予測クエリは、DMX を使用します。DMX とは、データ マイニング拡張機能 (Data Mining Extensions、DMX) 言語の略称です。 DMX の構文は T-SQL と似ていますが、データ マイニング オブジェクトに対するクエリに使用されます。 DMX 構文は複雑ではありませんがなどのクエリ ビルダーを使用して、 [SQL Server データ マイニング アドインでの Office](../../2014/analysis-services/data-mining/sql-server-data-mining-add-ins-for-office.md)入力の選択および学習することを強くお勧めしているため、式をビルドするはるかに簡単になります基本。  
  
## <a name="creating-the-query"></a>クエリを作成します。  
 予測クエリを作成するには、まず、マイニング モデルと入力テーブルを選択します。  
  
#### <a name="to-select-a-model-and-input-table"></a>モデルと入力テーブルを選択するには  
  
1.  **マイニング モデル予測**データ マイニング デザイナーのタブで、**マイニング モデルの**ボックスで、**モデルの選択**します。  
  
2.  **マイニング モデルの選択** ダイアログ ボックスで、ツリーから移動、 **Targeted Mailing**構造体、構造を展開し、選択`TM_Decision_Tree`、順にクリックします**OK**.  
  
3.  **入力テーブルの選択**ボックスで、 **ケース テーブルの**します。  
  
4.  **テーブルの選択** ダイアログ ボックスで、**データ ソース**一覧で、データ ソース ビューを選択します。[!INCLUDE[ssAWDWsp](../includes/ssawdwsp-md.md)]します。  
  
5.  **テーブル/ビュー名**を選択、 **ProspectiveBuyer (dbo)** テーブル、およびクリックして**ok**。  
  
     `ProspectiveBuyer`テーブルに最もよく似た、 **vTargetMail**ケース テーブル。  
  
## <a name="mapping-the-columns"></a>列のマッピング  
 入力テーブルを選択すると、列名に基づいてマイニング モデルと入力テーブルの間に既定のマッピングが作成されます。 構造の列と外部データの列は 1 つ以上一致する必要があります。  
  
> [!IMPORTANT]  
>  モデルの精度を判断するために使用するデータには、予測可能列にマッピングできる列が含まれている必要があります。 そのような列が存在しない場合は、空の値で列を作成できますが、データ型が予測可能列と同じである必要があります。  
  
#### <a name="to-map-the-inputs-to-the-model"></a>入力をモデルにマップするには  
  
1.  結合する線を右クリックし、**マイニング モデルの**ウィンドウ、 **入力テーブルの**ウィンドウ、および選択**接続の変更**します。  
  
     すべての列をマップするわけではありません。 いくつかのマッピングを追加**テーブル列**します。 一致する列を増やすため、現在の日付の列に基づいて新しい生年月日の列も生成します。  
  
2.  **テーブル列**、クリックして、`Bike Buyer`セルし、ドロップダウンから prospectivebuyer.unknown を選択します。  
  
     これにより、予測可能列 [Bike Buyer] が入力テーブルの列にマップされます。  
  
3.  **[OK]** をクリックします。  
  
4.  **ソリューション エクスプ ローラー**を右クリックし、 **Targeted Mailing**データ ソース ビューと選択**ビュー デザイナー**します。  
  
5.  ProspectiveBuyer、テーブルを右クリックして**新しい名前付き計算**します。  
  
6.  **名前付き計算の作成** ダイアログ ボックスの**列名**、型`calcAge`します。  
  
7.  **説明**、型**誕生日に基づく年齢を計算**します。  
  
8.  **式**ボックスに「`DATEDIFF(YYYY,[BirthDate],getdate())`順にクリックします**OK**します。  
  
     入力テーブルがあるないのため、**年齢**モデルでは、1 つに対応する列、入力テーブルの BirthDate 列から顧客の年齢を計算するこの式を使用することができます。 **年齢**が最も大きく影響として識別された両方のモデルでは、入力テーブル内に存在する必要があります自転車の購入を予測する列。  
  
9. データ マイニング デザイナーで、選択、**マイニング モデル予測**タブから、再度開いて、**接続の変更**ウィンドウ。  
  
10. [**テーブル列**、] をクリックして、**年齢**セルし、ドロップダウンから [prospectivebuyer.calcage] を選択します。  
  
    > [!WARNING]  
    >  一覧に列が表示されない場合は、場合によって、デザイナーに読み込まれたデータ ソース ビューの定義を更新する必要があります。 、**ファイル**メニューの **すべてを保存**、終了して、デザイナーでプロジェクトを再度開きます。  
  
11. **[OK]** をクリックします。  
  
## <a name="designing-the-prediction-query"></a>予測クエリのデザイン  
  
1.  最初のボタンのツールバーで、**マイニング モデル予測** タブは、**デザインに切り替える表示/結果ビューに切り替え、クエリ ビューに切り替え**ボタンをクリックします。 このボタンに下向きの矢印をクリックし、選択**デザイン**します。  
  
2.  グリッドで、**マイニング モデル予測** タブで、最初の空行のセルをクリックして、**ソース**列、および選択**予測関数**します。  
  
3.  **予測関数**行の**フィールド**列で、`PredictProbability`します。  
  
     **エイリアス**の同じ行、型の列**結果の確率**します。  
  
4.  **マイニング モデルの**ウィンドウの上部を選択し、[Bike Buyer] をドラッグ、**条件と引数**セル。  
  
     して、[tm_decision_tree]。[Bike Buyer] が表示されます、**条件と引数**セル。  
  
     これにより、`PredictProbability` 関数の対象列を指定します。 関数の詳細については、次を参照してください。[データ マイニング拡張機能&#40;DMX&#41;関数リファレンス](/sql/dmx/data-mining-extensions-dmx-function-reference)します。  
  
5.  次の空白行をクリックして、**ソース**列、および TM_Decision_Tree マイニング モデル**します。**  
  
6.  `TM_Decision_Tree`行の**フィールド**列で、`Bike Buyer`します。  
  
7.  `TM_Decision_Tree`行の**条件と引数**列に「`=1`します。  
  
8.  次の空白行をクリックして、**ソース**列、および選択**ProspectiveBuyer テーブル**します。  
  
9. `ProspectiveBuyer`行の**フィールド**列で、 **ProspectiveBuyerKey**します。  
  
     予測クエリに一意識別子が追加され、自転車を購入する可能性が高い顧客とそうでない顧客を特定できるようになります。  
  
10. グリッドに 5 つの行を追加します。 行ごとに、選択**ProspectiveBuyer テーブル**として、**ソース**し、次の列を追加、**フィールド**セル。  
  
    -   calcAge  
  
    -   LastName  
  
    -   FirstName  
  
    -   AddressLine1  
  
    -   AddressLine2  
  
 最後に、クエリを実行して結果を参照します。  
  
 **予測クエリ ビルダー**もこれらのコントロールが含まれます。  
  
-   **表示** チェック ボックス  
  
     句をデザイナーから削除せずに、句をクエリから削除することができます。 複雑なクエリを使用しているときに、DMX 構文のコピーとウィンドウへの貼り付けを行わずに構文を保持しようとする場合に役立ちます。  
  
-   **[グループ]**  
  
     選択した行の先頭に開く (左) かっこを挿入するか、現在の行の末尾に閉じる (右) かっこを挿入します。  
  
-   **または**  
  
     現在の関数または現在の列の直後に、`AND` 演算子または `OR` 演算子を挿入します。  
  
#### <a name="to-run-the-query-and-view-results"></a>クエリを実行して結果を表示するには  
  
1.  **マイニング モデル予測**] タブで、[、**結果**ボタンをクリックします。  
  
2.  クエリを実行して結果が表示されたら、その結果を確認できます。  
  
     **マイニング モデル予測**タブには、自転車を購入する可能性がある潜在的な顧客の連絡先情報が表示されます。 **結果の確率**列が正しい予測の確率を示します。 これらの結果を使用すると、メールを送信する対象となる潜在顧客を特定できます。  
  
3.  この時点で、結果を保存できます。 この場合、3 つの選択肢があります。  
  
    -   結果で、データの行を右クリックして**コピー**をクリップボードにその値のみ (および列見出し) を保存します。  
  
    -   結果で、任意の行を右クリックして**すべてコピー**をクリップボードに、列見出しを含む、全体の結果セットをコピーします。  
  
    -   クリックして**クエリ結果の保存**結果を次のように、データベースに直接保存します。  
  
        1.  **保存のデータ マイニング クエリの結果**ダイアログ ボックスで、データ ソースを選択するか、新しいデータ ソースを定義します。  
  
        2.  クエリの結果が保存されるテーブルの名前を入力します。  
  
        3.  オプションを使用**DSV に追加**テーブルを作成すると、既存のデータ ソース ビューに追加します。 これは、モデルのすべての関連テーブル (トレーニング データ、予測ソース データ、クエリ結果など) を同じデータ ソース ビューで保持する場合に便利です。  
  
        4.  オプションを使用**場合は上書きが存在する**、最新の結果で、既存のテーブルを更新します。  
  
             予測クエリに列を追加した場合、予測クエリで列の名前またはデータ型を変更した場合、または保存先テーブルで ALTER ステートメントを実行した場合、このオプションを使用してテーブルを上書きする必要があります。  
  
             また、複数の列の名前が同じ場合 (たとえば、既定の列名**式**)、重複した名前の列の別名を作成する必要がありますまたはデザイナーには、SQL 結果を保存しようとするときに、エラーが発生します。サーバー。 SQL Server では、複数の列に同じ名前が含まれることが許可されないためです。  
  
             詳細については、次を参照してください。[保存データ マイニング クエリ結果 ダイアログ ボックス&#40;マイニング モデル予測 ビュー&#41;](../../2014/analysis-services/save-data-mining-query-result-dialog-box-mining-model-prediction-view.md)します。  
  
## <a name="next-task-in-lesson"></a>このレッスンの次の作業  
 [構造データにドリルスルーを使用して&#40;基本的なデータ マイニング チュートリアル&#41;](../../2014/tutorials/using-drillthrough-on-structure-data-basic-data-mining-tutorial.md)  
  
## <a name="see-also"></a>参照  
 [予測クエリ ビルダーを使用した予測クエリの作成](../../2014/analysis-services/data-mining/create-a-prediction-query-using-the-prediction-query-builder.md)  
  
  
