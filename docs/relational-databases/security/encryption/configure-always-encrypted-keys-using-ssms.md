---
title: SQL Server Management Studio を使用した Always Encrypted キーのプロビジョニング | Microsoft Docs
description: SQL Server Management Studio を使用して、Always Encrypted の列マスター キーと列暗号化キーをプロビジョニングする方法について説明します。
ms.custom: ''
ms.date: 04/15/2021
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
f1_keywords:
- SQL13.SWB.COLUMNMASTERKEY.PAGE.F1
- SQL13.SWB.COLUMNENCRYPTIONKEY.PAGE.F1
helpviewer_keywords:
- Always Encrypted, configure with SSMS
ms.assetid: 29816a41-f105-4414-8be1-070675d62e84
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: bf0e2f94fd60488871439407a70e478c23a6f958
ms.sourcegitcommit: 233be9adaee3d19b946ce15cfcb2323e6e178170
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107561051"
---
# <a name="provision-always-encrypted-keys-using-sql-server-management-studio"></a>SQL Server Management Studio を使用して Always Encrypted キーをプロビジョニングする
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

この記事では、[SQL Server Management Studio (SSMS)](../../../ssms/download-sql-server-management-studio-ssms.md) を使用して、Always Encrypted の列マスター キーと列暗号化キーをプロビジョニングするための手順について説明します。

推奨されるベスト プラクティスやセキュリティ上の重要な考慮事項など、Always Encrypted のキー管理の概要については、「[Always Encrypted のキー管理の概要](overview-of-key-management-for-always-encrypted.md)」をご覧ください。

<a name="provisioncmk"></a>
## <a name="provision-column-master-keys-with-the-new-column-master-key-dialog"></a>[新しい列マスター キー] ダイアログを使用して列マスター キーをプロビジョニングする

**[新しい列マスター キー]** ダイアログでは、列マスター キーを生成することも、キー ストア内の既存のキーを選択することもできます。さらに、作成または選択したキーに対して列マスター キーのメタデータをデータベースに作成することができます。

1.  **オブジェクト エクスプローラー** を使用し、データベースの下の **[セキュリティ]、[Always Encrypted キー]** の順にアクセスします。
2.  **[列マスター キー]** フォルダーを右クリックし、 **[新しい列マスター キー...]** を選択します。 
3.  **[新しい列マスター キー]** ダイアログで、列マスター キーのメタデータ オブジェクトの名前を入力します。
4.  キー ストアを選択します。
    - **証明書ストア - 現在のユーザー** - Windows 証明書ストアでの現在のユーザーの証明書ストアの場所を示します。これは個人的なストアです。 
    - **証明書ストア - ローカル コンピューター** - Windows 証明書ストアでのローカル コンピューターの証明書ストアの場所を示します。 
    - **Azure Key Vault** - Azure にサインインする必要があります ( **[サインイン]** をクリック)。 サインインすると、ご自分の Azure サブスクリプションのいずれかと、キー コンテナーまたはマネージド HSM を選択できるようになります (SSMS 18.9 以降が必要)。
        > [!NOTE]
        > **[新しい列マスター キー]** ダイアログでは現在、承認に対してロールのアクセス許可を使用するキー コンテナーはサポートされていません。 アクセス ポリシーを使用するキー コンテナーのみがサポートされています。

        > [!NOTE]
        > Azure Key Vault の[マネージド HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm/overview) に格納されている列マスター キーを使用するには、SSMS 18.9 以降のバージョンが必要です。

    - **キー ストア プロバイダー (KSP)** - Cryptography Next Generation (CNG) API を実装するキー ストア プロバイダー (KSP) を介してアクセスできるキー ストアを示します。 通常、この種のストアは、ハードウェア セキュリティ モジュール (HSM) となります。 このオプションを選択したら、KSP を選択する必要があります。 既定では、**Microsoft ソフトウェア キー ストア プロバイダー** が選択されます。 HSM に格納されている列マスター キーを使用する場合は、デバイス用の KSP を選択します (ダイアログを開く前に、コンピューターにインストールし、構成しておく必要があります)。
    -   **暗号化サービス プロバイダー (CSP)** - 暗号化 API (CAPI) を実装する暗号化サービス プロバイダー (CSP) を介してアクセスできるキー ストアです。 通常、そのようなストアは、ハードウェア セキュリティ モジュール (HSM) です。 このオプションを選択したら、CSP を選択する必要があります。  HSM に格納されている列マスター キーを使用する場合は、デバイス用の CSP を選択します (ダイアログを開く前に、コンピューターにインストールし、構成しておく必要があります)。
    
    > [!NOTE]
    > CAPI は非推奨の API であるため、既定では暗号化サービス プロバイダー (CAPI) オプションは無効になります。 これを有効にするには、Windows レジストリの **[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\sql13\Tools\Client\Always Encrypted]** キーの下に CAPI Provider Enabled DWORD 値を作成し、これを 1 に設定します。 キー ストアで CNG がサポートされていない場合は、CAPI ではなく CNG を使用する必要があります。
   
    上記のキー ストアの詳細については、「[Always Encrypted の列マスター キーの作成と保存](create-and-store-column-master-keys-always-encrypted.md)」をご覧ください。

5. お客様が [!INCLUDE [sssql19-md](../../../includes/sssql19-md.md)] を使用していて、お使いの SQL Server インスタンスがセキュア エンクレーブを使って構成されている場合は、 **[エンクレーブ計算を許可する]** チェックボックスをオンにして、マスター キーをエンクレーブ対応にすることができます。 詳細については、「[セキュア エンクレーブを使用する Always Encrypted](always-encrypted-enclaves.md)」をご覧ください。 

    > [!NOTE]
    > お使いの SQL Server インスタンスがセキュア エンクレーブを使って正しく構成されていない場合は、 **[エンクレーブ計算を許可する]** チェックボックスは表示されません。

6.  キー ストアにある既存のキーを選択するか、あるいは **[キーの生成]** ボタンまたは **[証明書の生成]** ボタンをクリックしてキー ストアにキーを作成します。 
7.  **[OK]** をクリックします。新しいキーが一覧に表示されます。 

ダイアログを完了すると、SQL Server Management Studio によって、データベースに列マスター キーのメタデータが作成されます。 ダイアログでこれを実現するには、 [CREATE COLUMN MASTER KEY (Transact-SQL)](../../../t-sql/statements/create-column-master-key-transact-sql.md) ステートメントを生成し発行します。

::: moniker range=">=sql-server-ver15"

エンクレーブ対応の列マスター キーを構成する場合は、SSMS により、そのメタデータも列マスター キーを使って署名されます。 

::: moniker-end

### <a name="permissions-for-provisioning-a-column-master-key"></a>列マスター キーをプロビジョニングするための権限

ダイアログを使って列マスター キーを作成するためには、そのデータベースで、*ALTER ANY COLUMN MASTER KEY* データベース権限が必要です。 キー列マスター キーにアクセスして使用するには、キー ストアのアクセス許可も必要です。 キーの管理操作に必要なキー ストアのアクセス許可の詳細については、「[Always Encrypted の列マスター キーを作成して保存する](create-and-store-column-master-keys-always-encrypted.md)」に移動し、キー ストアに関するセクションを見つけてください。

<a name="provisioncek"></a> 
## <a name="provision-column-encryption-keys-with-the-new-column-encryption-key-dialog"></a>[新しい列の暗号化キー] ダイアログを使用して列の暗号化キーをプロビジョニングする

**[新しい列の暗号化キー]** ダイアログでは、列暗号化キーを生成し、それを列マスター キーで暗号化し、データベースに列暗号化キーのメタデータを作成することができます。

1.  **オブジェクト エクスプローラー** を使用して、データベースの下にあるフォルダーを **[セキュリティ]、[Always Encrypted キー]** の順に移動します。
2.  **[列暗号化キー]** フォルダーを右クリックし、 **[新しい列の暗号化キー...]** を選択します。 
3.  **[新しい列の暗号化キー]** ダイアログで、列暗号化キーのメタデータ オブジェクトの名前を入力します。
4.  データベースの列マスター キーを表すメタデータ オブジェクトを選択します。
5.  **[OK]** をクリックします。 

ダイアログを完了すると、SQL Server Management Studio によって新しい列暗号化キーが生成され、選択した列マスター キーのメタデータがデータベースから取得されます。 次に、SSMS では、列マスター キーのメタデータを使って、その列マスター キーを含むキー ストアとの交信が行われ、列暗号化キーが暗号化されます。 最後に、[CREATE COLUMN ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/create-column-encryption-key-transact-sql.md) ステートメントを生成して発行することで、SSMS によって新しい列暗号化のメタデータ データがデータベースに作成されます。

> [!NOTE]
> Azure Key Vault の[マネージド HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm/overview) に格納されている列マスター キーを使用するには、SSMS 18.9 以降のバージョンが必要です。

### <a name="permissions-for-provisioning-a-column-encryption-key"></a>列暗号化キーをプロビジョニングするためのアクセス許可

ダイアログを使って列暗号化キーのメタデータを作成し、列マスター キーのメタデータにアクセスするには、*ALTER ANY COLUMN ENCRYPTION KEY* および *VIEW ANY COLUMN MASTER KEY DEFINITION* データベース権限が必要です。 列マスター キーにアクセスして使用するには、キー ストアのアクセス許可も必要です。 キーの管理操作に必要なキー ストアのアクセス許可の詳細については、「[Always Encrypted の列マスター キーを作成して保存する](create-and-store-column-master-keys-always-encrypted.md)」に移動し、キー ストアに関するセクションを見つけてください。

## <a name="provision-always-encrypted-keys-using-the-always-encrypted-wizard"></a>Always Encrypted ウィザードを使って Always Encrypted キーをプロビジョニングする

[Always Encrypted ウィザード](always-encrypted-wizard.md)は、選択したデータベースの列の暗号化、暗号化解除、および再暗号化を行うためのツールです。 そこでは、既に構成されているキーを使用できますが、新しい列マスター キーと新しい列暗号化を生成することもできます。 

## <a name="next-steps"></a>次の手順
- [Always Encrypted ウィザードを使用して列暗号化を構成する](always-encrypted-wizard.md)
- [DAC パッケージでの Always Encrypted を使用した列暗号化の構成](configure-always-encrypted-using-dacpac.md)
- [SQL Server Management Studio を使用して Always Encrypted キーを交換する](rotate-always-encrypted-keys-using-ssms.md)
- [Always Encrypted を使用したアプリケーションの開発](always-encrypted-client-development.md)
- [SQL Server インポートおよびエクスポート ウィザードで Always Encrypted を使用して列間でデータを移行する](always-encrypted-migrate-using-import-export-wizard.md)

## <a name="see-also"></a>参照
- [常に暗号化](always-encrypted-database-engine.md)
- [Always Encrypted のキー管理の概要](overview-of-key-management-for-always-encrypted.md)
- [Always Encrypted の列マスター キーを作成して保存する](create-and-store-column-master-keys-always-encrypted.md)
- [SQL Server Management Studio を使用して Always Encrypted を構成する](configure-always-encrypted-using-sql-server-management-studio.md)
- [PowerShell を使用して Always Encrypted キーをプロビジョニングする](configure-always-encrypted-keys-using-powershell.md)
- [CREATE COLUMN MASTER KEY (Transact-SQL)](../../../t-sql/statements/create-column-master-key-transact-sql.md)
- [DROP COLUMN MASTER KEY (Transact-SQL)](../../../t-sql/statements/drop-column-master-key-transact-sql.md)
- [CREATE COLUMN ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/create-column-encryption-key-transact-sql.md)
- [ALTER COLUMN ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/alter-column-encryption-key-transact-sql.md)
- [DROP COLUMN ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/drop-column-encryption-key-transact-sql.md) 
- [sys.column_master_keys (Transact-SQL)](../../system-catalog-views/sys-column-master-keys-transact-sql.md)
- [sys.column_encryption_keys (Transact-SQL)](../../system-catalog-views/sys-column-encryption-keys-transact-sql.md)
