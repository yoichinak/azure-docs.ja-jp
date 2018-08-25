---
title: チュートリアル - ポータルを使用して Azure Analysis Services サーバーに基本的なサンプル モデルを追加する | Microsoft Docs
description: このチュートリアル レッスンでは、Azure Analysis Services でサンプル モデルを追加する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c63995a461cee6bc39603a43604b8080942bd88b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442809"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>チュートリアル - ポータルからサンプル モデルを追加する

このチュートリアルでは、サンプルの Adventure Works の表形式モデル データベースをサーバーに追加します。 このサンプル モデルは、Adventure Works Internet Sales (1200) サンプル データ モデルの完成版です。 サンプル モデルは、モデル管理のテスト、ツールおよびクライアント アプリケーションとの接続、モデル データのクエリなどに便利です。 このチュートリアルでは、[Azure portal](https://portal.azure.com) と [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) を使って、次のことを行います。 

> [!div class="checklist"]
> * サーバーに完全なサンプルの表形式データ モデルを追加する 
> * SSMS を使用してモデルに接続する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、次のものが必要です。

- Azure Analysis Services サーバー。 詳しくは、「[サーバーの作成 - ポータル](analysis-services-create-server.md)」をご覧ください。
- サーバー管理者の権限
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[ポータル](https://portal.azure.com/)にサインインします。

## <a name="add-a-sample-model"></a>サンプル モデルを追加する

1. サーバーの **[概要]** で、**[新しいモデル]** をクリックします。

    ![サンプル モデルを作成する](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. **[新しいモデル]** > **[Choose a datasource]\(データソースの選択\)** で、**[サンプル データ]** が選ばれていることを確認して、**[追加]** をクリックします。

    ![サンプル データを選択する](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. **[概要]** で、`adventureworks` サンプル モデルが追加されたことを確認します。

    ![サンプル データを選択する](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

サンプル モデルはキャッシュ メモリ リソースを使っています。 テスト用にサンプル モデルを使っていない場合は、サーバーからモデルを削除する必要があります。

次の手順では、SSMS を使ってサーバーからモデルを削除する方法について説明します。 プレビュー段階の Web デザイナー機能を使ってモデルを削除することもできます。

1. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。

2. **[サーバーに接続]** で、サーバー名を貼り付け、**[認証]** で **[Active Directory - MFA サポートで汎用]** を選び、ユーザー名を入力して、**[接続]** をクリックします。

    ![[サインイン]](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. **オブジェクト エクスプローラー**で `adventureworks` サンプル データベースを右クリックして、**[削除]** をクリックします。

    ![サンプル データベースを削除する](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>次の手順 

このチュートリアルでは、基本的なサンプル モデルをサーバーに追加する方法を学習しました。 モデル データベースが追加されたので、SQL Server Management Studio からこのデータベースに接続し、ユーザー ロールを追加できます。 さらに学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: サーバー管理者とユーザー ロールを構成する](analysis-services-database-users.md)


