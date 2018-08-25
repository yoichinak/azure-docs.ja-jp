---
title: 最初の Azure SQL Database の設計 - C# | Microsoft Docs
description: ADO.NET を利用し、C# プログラムで最初の Azure SQL データベースを設計し、それに接続する方法について説明します。
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.date: 06/07/2018
ms.openlocfilehash: 4e2238968d29bc0081a472c9c05662cb7813d866
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697715"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>C&#x23; と ADO.NET で Azure SQL データベースを設計し、接続する

Azure SQL Database は、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal、ADO.NET、および Visual Studio を使用して以下の手順を実行する方法を説明します。 

> [!div class="checklist"]
> * Azure Portal でデータベースを作成する
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * ADO.NET と Visual Studio を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入、更新、削除する 
> * ADO.NET を使用してデータのクエリを実行する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

[Visual Studio Community 2017、Visual Studio Professional 2017、Visual Studio Enterprise 2017 のいずれか](https://www.visualstudio.com/downloads/)のインストール。

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、データベースとテーブルの作成、データの読み込みとクエリ実行、以前の特定の時点へのデータベースの復元などの基本的なデータベース タスクについて学習しました。 以下の方法について学習しました。
> [!div class="checklist"]
> * データベースを作成する
> * ファイアウォール規則の設定
> * [Visual Studio と C#](sql-database-connect-query-dotnet-visual-studio.md) を使用して SQL Database に接続する
> * テーブルの作成
> * データを挿入、更新、削除する
> * データのクエリを実行する

次のチュートリアルに進み、データの移行について確認してください。

> [!div class="nextstepaction"]
> [SQL Server データベースを Azure SQL Database に移行する](sql-database-migrate-your-sql-server-database.md)

