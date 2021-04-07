---
title: カスタム レポート アイテムのクラス ライブラリ | Microsoft Docs
description: カスタム レポート アイテムのクラス ライブラリについて説明し、コード サンプルを利用してクラスの利用方法について理解します。
ms.date: 03/03/2017
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: custom-report-items
ms.topic: reference
helpviewer_keywords:
- custom report items, RDL
- RDL [Reporting Services], custom report items
ms.assetid: f18c5d8f-1d6b-4f0b-8657-c14896c2ce0d
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: d5d58066e16788dcba313bba82e77504c8394a75
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981128"
---
# <a name="custom-report-item-class-libraries"></a>カスタム レポート アイテムのクラス ライブラリ
  カスタム レポート アイテムは、**Microsoft.ReportDesigner** 名前空間のクラスを使用します。 カスタム レポート アイテムを実装する際に使用するクラスは、2 つの主なカテゴリに分類できます。1 つは、カスタム レポート アイテム インフラストラクチャをサポートするためにデザインされた独自のクラス、もう 1 つは、関連するレポート定義言語 (RDL) 要素の機能をカプセル化するマネージド ラッパー クラスです。 これらのクラスの使用方法のコード サンプルについては、「[SQL Server Reporting Services Product Samples](https://go.microsoft.com/fwlink/?LinkId=177889)」 (SQL Server Reporting Services の製品サンプル) を参照してください。  
  
## <a name="custom-report-item-infrastructure-classes"></a>カスタム レポート アイテム インフラストラクチャのクラス  
 以下のクラスは、カスタム レポート アイテムを実装するために使用されます。  
  
> [!NOTE]  
>  以下の各表はすべてを網羅したものではなく、各クラスの最も一般的に使用されるプロパティやメソッドのみが含まれています。  
  
### <a name="microsoftreportdesignercustomreportitemdesigner"></a>Microsoft.ReportDesigner.CustomReportItemDesigner  
 カスタム レポート アイテムのメイン クラスです。 カスタム レポート アイテムの実装のメイン クラスは、このクラスを継承する必要があります。  
  
#### <a name="public-properties"></a>パブリック プロパティ  
  
|プロパティ|説明|  
|-|-|  
|**名前**|カスタム レポート アイテムの名前|  
|**Type**|カスタム レポート アイテムの種類。|  
|**CustomData**|デザイン時に指定されたカスタム レポート アイテムのデータ プロパティをカプセル化する <xref:Microsoft.ReportingServices.RdlObjectModel.CustomData> オブジェクト|  
|**CustomProperties**|カスタム レポート アイテムのカスタム プロパティのコレクション|  
|**Height**|カスタム レポート アイテム コントロールの高さ|  
|**Width**|カスタム レポート アイテム コントロールの幅|  
|**Report**|レポート レベルのプロパティ (レポートのデータセットの一覧など) のコンテナー|  
|**AltReportItem**|カスタム レポート アイテムの実行時コントロールがサポートされていない場合に使用される代替レポート アイテム オブジェクト|  
|**スタイル**|カスタム レポート アイテムのスタイルのプロパティ|  
|**Adornment**|コントロールのインタラクティブな編集のために使用される装飾ウィンドウ|  
|**サイト**|コンポーネントの **ISite**|  
|**DesignerVerbCollection**|コントロールのショートカット メニューのカスタム動詞の配列|  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**BeginEdit**|コントロールのインタラクティブな編集をアクティブにします。|  
|**DoDefaultAction**|コントロールをダブルクリックしたとき、またはコントロールに対して Enter キーを押したときに呼び出されます。|  
|**EndEdit**|コントロールのインタラクティブな編集を非アクティブにします。|  
|**GetService**|サービスを表すオブジェクトを返します。|  
|**InitializeNewComponent**|新しいカスタム レポート アイテムの作成時に呼び出されます。|  
|**Invalidate**|コントロールの表面全体を再描画します。|  
|**OnDragEnter**<br /><br /> **OnDragDrop**|オブジェクトをコントロールにドラッグすると呼び出されます。|  
|**OnPaint**|**Paint** イベントに応答して呼び出されます。|  
  
### <a name="microsoftreportdesignercustomreportitemattribute"></a>Microsoft.ReportDesigner.CustomReportItemAttribute  
 この属性は、カスタム レポート アイテムの種類を識別するために使用されます。 この名前は、レポート デザイナー構成ファイルの **ReportItem** 要素の \<**Name**> 属性の値と一致している必要があります。  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**CustomReportItemAttribute**|CustomReportItemAttribute オブジェクトを構築します。|  
  
### <a name="microsoftreportdesignerlocalizednameattribute"></a>Microsoft.ReportDesigner.LocalizedNameAttribute  
 この属性は、カスタム レポート アイテム デザイナーで使用する表示名を指定するために使用されます。  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**LocalizedNameAttribute**|LocalizedNameAttribute オブジェクトを構築します。|  
  
### <a name="microsoftreportdesigneradornment"></a>Microsoft.ReportDesigner.Adornment  
 **Adornment** クラスは、カスタム レポート アイテムのデザイン時コンポーネントによって、デザイン画面のメインの四角形の外に領域を作成するために使用されます。 これらの領域では、マウス クリックやドラッグ アンド ドロップ操作などのユーザー インターフェイス イベントを扱うことができます。  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**OnShow**|**Adornment** がアクティブになると呼び出されます。|  
|**OnHide**|**Adornment** が非アクティブになると呼び出されます。|  
|**ペイント**|**Paint** イベントに応答して呼び出されます。|  
|**OnDragEnter**<br /><br /> **OnDragOver**<br /><br /> **OnDragLeave**<br /><br /> **OnDragDrop**|オブジェクトを **Adornment** にドラッグすると呼び出されます。|  
  
### <a name="microsoftreportdesigneradornerservice"></a>Microsoft.ReportDesigner.AdornerService  
 このクラスは、カスタム レポート アイテムのデザイン時コンポーネントの **Adornment** オブジェクトをサポートするためにカスタム レポート アイテムによって使用される、表示サービスのコレクションを提供するために使用されます。  
  
#### <a name="public-properties"></a>パブリック プロパティ  
  
|プロパティ|説明|  
|-|-|  
|**AdornerWindowBounds**|Adorner ウィンドウの境界|  
|**AdornerWindowRegion**|Adorner ウィンドウの領域|  
|**AdornerWindowGraphics**|Adorner ウィンドウのグラフィック コンテキスト|  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**ComponentRectInDesignerFrame**|デザイナー フレームの座標に変換されたコンポーネントの境界を返します。|  
|**InvalidateAdorner**|Adorner ウィンドウを無効にします。|  
|**PointToAdorner**|Adorner ウィンドウの座標に変換された画面座標の点を返します。|  
  
### <a name="microsoftreportdesignerexpressioneditor"></a>Microsoft.ReportDesigner.ExpressionEditor  
 このクラスは、カスタム レポート アイテムのデザイン時コントロールから式エディターを呼び出すために使用できます。  
  
#### <a name="public-methods"></a>パブリック メソッド  
  
|メソッド|説明|  
|-|-|  
|**EditValue**|式エディターを呼び出して、渡されたオブジェクト値で初期化します。|  
  
### <a name="microsoftreportdesignerifieldsdataobject"></a>Microsoft.ReportDesigner.IFieldsDataObject  
 このクラスは [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] フィールドのコレクションであり、デザイン環境でドラッグ アンド ドロップ イベントをサポートするために使用されます。 **IReportItemDataObject** から継承します。  
  
#### <a name="public-properties"></a>パブリック プロパティ  
  
|プロパティ|説明|  
|-|-|  
|**DataSetName**|ドロップされるフィールドを含むデータセットの名前|  
|**Fields**|削除されるフィールドのコレクション (**Microsoft.ReportDesigner.Field**)|  
  
## <a name="see-also"></a>参照  
 [レポート定義言語 &#40;SSRS&#41;](../../reporting-services/reports/report-definition-language-ssrs.md)   
 [カスタム レポート アイテムの実行時コンポーネントの作成](../../reporting-services/custom-report-items/creating-a-custom-report-item-run-time-component.md)   
 [カスタム レポート アイテムのデザイン時コンポーネントの作成](../../reporting-services/custom-report-items/creating-a-custom-report-item-design-time-component.md)  
  
  
