---
title: 変数値ファイル (OracleToSQL) の作成 |Microsoft Docs
ms.prod: sql
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
helpviewer_keywords:
- Variable Value File Creation
- Variable Value File, Variable Value File Validation
ms.assetid: f583d81a-8e34-41b1-8100-ee3a6a82213b
author: Shamikg
ms.author: Shamikg
manager: v-thobro
ms.openlocfilehash: 01ed7440d2bc98e971c0ccb48ad4bc4b725e2192
ms.sourcegitcommit: 9c6a37175296144464ffea815f371c024fce7032
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51666921"
---
# <a name="creating-variable-value-files-oracletosql"></a>変数値ファイルの作成 (OracleToSQL)
変数値ファイルは、別に 1 つのサーバーの移行から頻繁に変更されるように、送信元または送信先のサーバー名のコマンドのパラメーターの値を構成する XML ファイルです。 各ソース サーバーの値を格納するための複数の変数ファイルが作成されでマスター スクリプト ファイルで参照されている多数のデータベースの移行が発生すると、 **– v**コマンド ライン スイッチします。 これにより、複数の変数ファイルの変数の値をいくつかのスクリプト ファイルの静的な値を維持するためにします。  
  
> [!NOTE]  
> 1.  変数の名前はプレフィックスし、$ (ドル) 記号が付いています。 変数は、変数値ファイルで値が割り当てられていない場合はその結果、コンソールの実行プロセスを停止するスクリプト ファイルの解析中にエラーが発生します。  
> 2.  エスケープ文字**$** は **$$** します。 パラメーターの変数または静的な値の値を含むかどうか**$** し (ドル) シンボル**$$** 変数ではなく文字として扱うことを指定する必要があります。  
> 3.  保守容易性のために、内部で変数を宣言できます`‘variable-group’`ユーザーの論理的な分離の要素は、変数を定義します。  この要素の使用は必須ではありません。  
  
**使用例:**  
  
**例 1:**  
  
```  
<!--Sample of variable value file commands-->  
  
<variables>  
  
  <variable-group name="ProjectSpecs">  
  
    <variable name="$project_folder$" value="<project-folder>"/>  
  
    <variable name="$project_name$" value="<project-name>"/>  
  
    <variable name="$project_overwrite$" value="<true/false>"/>  
  
    <variable name="$project_type$" value="<project-type>"/>  
  
  </variable-group>  
  
</variables>  
```  
**例 2:**  
  
```  
<!--Sample of variable value file commands-->  
  
<variables>  
  
  <variable-group name="SQLServerParams">  
  
    <variable-group name="SqlServerConnectionParams">  
  
      <variable name="$TargetServerName$" value="<server-name>"/>  
  
      <variable name="$TargetDB$" value="<database-name>"/>  
  
      <variable name="$TargetUserName$" value="<user-name>"/>  
  
      <variable name="$TargetPassword$" value="<password>"/>  
  
      <variable name="$TrustedConnection$" value="<true/false>"/>  
  
    </variable-group>  
  
    <variable-group name="SqlServerObjectParams">  
  
      <variable name="$ObjectName1$" value="<object-name>"/>  
  
      <variable name="$ObjectName2$" value="<object-name>"/>  
  
    </variable-group>  
  
  </variable-group>  
  
</variables>  
```  
  
## <a name="next-step"></a>次の手順  
コンソールの運用には、次の手順は[サーバー接続ファイルを作成する&#40;OracleToSQL&#41;](../../ssma/oracle/creating-the-server-connection-files-oracletosql.md)  
  
## <a name="see-also"></a>参照  
[サーバー ファイル (Oracle) の作成](https://msdn.microsoft.com/002f129e-0868-48ad-a4b4-c68b5007e12e)  
  
