---
title: Azure NetApp Files の容量プールまたはボリュームをサイズ変更する | Microsoft Docs
description: 容量プールまたはボリュームをサイズ変更する方法について説明します。
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
ms.topic: how-to-article
ms.date: 04/03/2018
ms.author: b-juche
ms.openlocfilehash: 72da85fb7296d02bc6d5f7fcd3279953c312c920
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012010"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>容量プールまたはボリュームをサイズ変更する
容量プールまたはボリュームは、必要に応じてサイズ変更できます。 

## <a name="resize-the-capacity-pool"></a>容量プールをサイズ変更する 

容量プールのサイズは、4 TiB 単位で増減できます。 容量プールをサイズ変更すると、購入した Azure NetApp Files の容量が変更されます。

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、サイズ変更する容量プールをクリックします。 
2. 容量プールの名前を右クリックするか、容量プールの行の最後にある「…」アイコンをクリックして、コンテキスト メニューを表示します。 
3. コンテキスト メニューのオプションを使用して、容量プールをサイズ変更するか、削除します。

## <a name="resize-a-volume"></a>ボリュームをサイズ変更する

ボリュームは、必要に応じてサイズ変更できます。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、**[ボリューム]** をクリックします。 
2. サイズ変更するボリュームの名前を右クリックするか、ボリュームの行の最後にある「...」アイコンをクリックして、コンテキスト メニューを表示します。
3. コンテキスト メニューのオプションを使用して、ボリュームをサイズ変更するか、削除します。

