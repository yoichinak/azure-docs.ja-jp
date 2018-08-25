---
title: Azure CLI 1.0 を使用したクラシック Linux VM の作成 | Microsoft Docs
description: クラシック デプロイ モデルを使用して Azure CLI 1.0 で Linux 仮想マシンを作成する方法について説明する
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 13d0ef93c3828c514e46e37494a66f7003eac827
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931616"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Azure CLI 1.0 を使用してクラシック Linux VM を作成する方法
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager バージョンについては、[こちら](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

このトピックでは、クラシック デプロイ モデルを使用して Azure CLI 1.0 で Linux 仮想マシン (VM) を作成する方法について説明します。 ここでは、Azure 上の使用可能な **イメージ** にある Linux イメージを使用します。 Azure CLI 1.0 コマンドでは、次のような構成を選択できます。

* 仮想ネットワークと VM との接続
* 既存のクラウド サービスへの VM の追加
* 既存のストレージ アカウントへの VM の追加
* 可用性セットまたは場所への VM の追加

> [!IMPORTANT]
> VM が仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、VM の作成時に必ず仮想ネットワークを指定する必要があります。 VM が仮想ネットワークに接続するように設定できるのは、VM の作成時に限られています。 仮想ネットワークの詳細については、「 [Virtual Network の概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して Linux VM を作成する方法
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

