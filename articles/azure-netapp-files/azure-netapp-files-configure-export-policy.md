---
title: Azure NetApp Files ボリュームのエクスポート ポリシーを構成する | Microsoft Docs
description: Azure NetApp Files ボリュームへのアクセスを制御するエクスポート ポリシーの構成方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010958"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>ボリュームのエクスポート ポリシーを構成する (省略可能)

Azure NetApp Files ボリュームへのアクセスを制御するエクスポート ポリシーは、必要に応じて構成することができます。 

## <a name="steps"></a>手順 

1.  [Manage Volume]\(ボリュームの管理\) ブレードから **[Create Export Policy]\(エクスポート ポリシーの作成\)** ブレードをクリックします。 

2.  次のフィールドの情報を指定して、エクスポート ポリシー ルールを作成します。   
    *  **[インデックス]**   
        ルールのインデックス番号を指定します。  
        エクスポート ポリシーは、最大 5 つのルールで構成できます。 ルールは、リスト内の対応するインデックス番号の順に評価されます インデックス番号が小さいルールが先に評価されます。 たとえば、インデックス番号が 1 のルールは、インデックス番号が 2 のルールよりも前に評価されます。 

    * **[許可されたクライアント]**   
        次のいずれかの形式で値を指定します。  
        * IPv4 アドレス (例: `10.1.12.24`) 
        * ビット数として表されたサブネット マスクを指定した IPv4 アドレス (例: `10.1.12.10/4`)

    * **Access (アクセス)**  
        次のアクセス タイプのいずれかを選択します。  
        * アクセス権なし 
        * 読み取りと書き込み
        * 読み取り専用

    * **[プロトコル]**   
        エクスポート ポリシーに使用するプロトコルを指定します。   
        現在、Azure NetApp Files でサポートされるのは NFSv3 のみです。

    ![エクスポート ポリシー](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>次の手順 
* [ボリュームを管理する](azure-netapp-files-manage-volumes.md)
* [仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [スナップショットを管理する](azure-netapp-files-manage-snapshots.md)
