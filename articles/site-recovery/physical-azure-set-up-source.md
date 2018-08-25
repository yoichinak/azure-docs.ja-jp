---
title: ソース環境のセットアップ (物理サーバーから Azure へ) | Microsoft Docs
description: この記事では、Windows または Linux を実行している物理サーバーを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/21/2018
ms.author: raynew
ms.openlocfilehash: 0cbba45ce49667293d8f16bf370424acd70ff78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213488"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>ソース環境のセットアップ (物理サーバーから Azure へ)

この記事では、Windows または Linux を実行している物理サーバーを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、次のものが既にあることを前提としています。
- Recovery Services コンテナー ([Azure Portal](http://portal.azure.com "Azure Portal") 内)。
- 構成サーバーをインストールするための物理コンピューター。
- 構成サーバーをインストールしているコンピューターで TLS 1.0 を無効にした場合は、TLS 1.2 が有効になっていることを確認し、(強力な暗号化を無効にした) コンピューターに .NET Framework バージョン 4.6 以降がインストールされていることを確認します。 [詳細情報](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)。

### <a name="configuration-server-minimum-requirements"></a>構成サーバーの最小要件
次の表は、構成サーバーに最低限必要なハードウェア、ソフトウェア、およびネットワークの要件を示したものです。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS ベースのプロキシ サーバーは、構成サーバーではサポートされません。

## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. Azure Portal で、**Recovery Services** コンテナー ブレードに移動し、コンテナーを選択します。
2. コンテナーの**リソース** メニューで、**[作業の開始]** > **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/physical-azure-set-up-source/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[非仮想化/その他]** を選択し、**[OK]** をクリックします。

    ![Choose goals](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

1. **[ソースの準備]** で、構成サーバーがない場合は **[+ 構成サーバー]** をクリックして追加します。

  ![Set up source](./media/physical-azure-set-up-source/plus-config-srv.png)
2. **[サーバーの追加]** ブレードで、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 統合セットアップを実行する際には、登録キーが必要です。 キーは生成後 5 日間有効です。

    ![Set up source](./media/physical-azure-set-up-source/set-source2.png)
6. 構成サーバーとして使用するマシンで**Azure Site Recovery 統合セットアップ**を実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery 統合セットアップを実行する

> [!TIP]
> コンピューターのシステム クロックの時刻と現地時刻との差が 5 分を超えている場合は、構成サーバーの登録が失敗します。 インストールを開始する前に、システム クロックを[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期させてください。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーはコマンドラインを使用してインストールすることもできます。 [詳細情報](physical-manage-configuration-server.md#install-from-the-command-line)。


## <a name="common-issues"></a>一般的な問題

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>次の手順

次のステップでは、Azure で[ターゲット環境をセットアップ](physical-azure-set-up-target.md)します。
