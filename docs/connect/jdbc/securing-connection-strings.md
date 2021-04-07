---
title: 接続文字列のセキュリティ保護
description: JDBC Driver for SQL Server を使用する場合に接続文字列情報をセキュリティで保護する方法について説明します。
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 69ce8557-5260-4ea4-81b8-d0c5481f0868
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c9d2744eb4845855d6cb33a15e6a3fd440545d22
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177073"
---
# <a name="securing-connection-strings"></a>接続文字列のセキュリティ保護

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

セキュリティで保護されたアプリケーションを実現する上で、データ ソースへのアクセスを保護することは、最も重要な目的の 1 つです。 データ ソースへのアクセスを制限するには、ユーザー ID、パスワード、データ ソース名などの接続情報をセキュリティで保護して安全策を講じる必要があります。 ソース コードにおいて、ユーザー ID やパスワードをプレーンテキストで保存するのはセキュリティ上の深刻な問題となります。 ユーザー ID やパスワード情報が含まれているコードをコンパイルしてから外部ソースに提供する場合でも、コンパイル済みのコードを逆アセンブルされる可能性があるため、ユーザー ID とパスワードは特定されてしまいます。 そのため、ユーザー ID やパスワードなどの重要な情報は、絶対にコードに入れないようにする必要があります。

## <a name="recommendations"></a>推奨事項

接続 URL とパスワードをアプリケーションのソース コードに一緒に保存しないことをお勧めします。 代わりに、アクセスが制限されている別のファイルにパスワードを保存することをお勧めします。 実行されているアプリケーションのコンテキストに対して、そのファイルへのアクセスを許可することもできます。

また、暗号化したパスワードをファイルに保存する方法もあります。 暗号化 API は、キーをどこかに保存する必要がなく、ユーザーのパスワードから生成していないものを使用してください。 たとえば、証明書に基づく公開キーと秘密キーのペアを使用すること、または両方の当事者がキーの承諾プロトコルを使用して、秘密キーを送信せずに暗号化のための同じ秘密キーを生成する方法 (Diffie-Hellman アルゴリズム) の使用を検討することができます。

外部ソースからの接続文字列情報 (ユーザーが入力するユーザー ID やパスワードなど) を受け取る場合は、ソースからの入力をすべて検証して、正しい書式に従っていること、接続に影響を及ぼすパラメータが追加されていないことを確認する必要があります。

## <a name="see-also"></a>関連項目

[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)
