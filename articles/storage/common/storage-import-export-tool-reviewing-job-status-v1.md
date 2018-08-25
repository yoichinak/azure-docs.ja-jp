---
title: Azure Import/Export ジョブの状態の確認 - v1 | Microsoft Docs
description: Import/Export ジョブの実行時に作成されたログ ファイルを使用して、Import/Export ジョブの状態を確認する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 51a73a4466f3460beead0ce03ddbf011a0b9cc3b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527663"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>コピー ログ ファイルによる Azure Import/Export ジョブの状態の確認
Microsoft Azure Import/Export サービスは、インポートまたはエクスポート ジョブに関連付けられたドライブを処理する場合、Blob のインポートまたはエクスポートを行うストレージ アカウントにコピー ログ ファイルを書き込みます。 ログ ファイルには、インポートまたはエクスポートされた各ファイルの状態の詳細が含まれます。 各コピー ログ ファイルへの URL は、完了したジョブの状態をクエリした際に返されます。詳細は「[Get Job](/rest/api/storageservices/Get-Job3)」をご覧ください。  

## <a name="example-urls"></a>URL の例

2 つのドライブを使用したインポート ジョブのコピー ログ ファイルの URL の例は次の通りです。  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 コピー ログの形式とステータス コードの一覧は、「[Azure Import/Export サービスのログ ファイルの形式](../storage-import-export-file-format-log.md)」をご覧ください。  
  
## <a name="next-steps"></a>次の手順
 
 * [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)   
 * [インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [インポート ジョブの修復](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [エクスポート ジョブの修復](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Azure Import/Export ツールのトラブルシューティング](storage-import-export-tool-troubleshooting-v1.md)
