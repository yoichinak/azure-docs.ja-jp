---
title: 'Azure AD Connect: サポートされるトポロジ | Microsoft Docs'
description: このトピックでは、Azure AD Connect のサポートされているトポロジとサポートされていないトポロジについて詳しく説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6a0f18a47bb50144d0a4d428617e0dc5c7509196
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442248"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect のトポロジ
この記事では、主な統合ソリューションとして Azure AD Connect 同期を使用する、さまざまなオンプレミス トポロジおよび Azure Active Directory (Azure AD) トポロジについて説明します。 この記事には、サポートされている構成とサポートされていない構成の両方が含まれています。

以下に、この記事での図の凡例を示します。

| 説明 | シンボル |
| --- | --- |
| オンプレミスの Active Directory フォレスト |![オンプレミスの Active Directory フォレスト](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| オンプレミス Active Directory とフィルター処理されたインポート |![Active Directory とフィルター処理されたインポート](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Azure AD Connect 同期サーバー |![Azure AD Connect 同期サーバー](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Azure AD Connect 同期サーバー "ステージング モード" |![Azure AD Connect 同期サーバー "ステージング モード"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync と Forefront Identity Manager (FIM) 2010 または Microsoft Identity Manager (MIM) 2016 |![GALSync と FIM 2010 または MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Azure AD Connect 同期サーバー、詳細 |![Azure AD Connect 同期サーバー、詳細](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| サポートされていないシナリオ |![サポートされていないシナリオ](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> 公式に文書化されている構成やアクションを除き、Microsoft は Azure AD Connect Sync の変更や操作をサポートしません。 このような構成やアクションを行うと、Azure AD Connect Sync が不整合な状態になったり、サポートされていない状態になったりする可能性があります。結果的に、Microsoft ではこのようなデプロイについてテクニカル サポートを提供できなくなります。


## <a name="single-forest-single-azure-ad-tenant"></a>単一のフォレスト、単一の Azure AD テナント
![単一のフォレストと単一のテナントのトポロジ](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

最も一般的なトポロジは、1 つのオンプレミス フォレスト (1 つまたは複数のドメイン) と、1 つの Azure AD テナントです。 Azure AD 認証では、パスワード ハッシュ同期が使用されます。 Azure AD Connect の高速インストールでは、このトポロジのみがサポートされます。

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>単一のフォレスト、1 つの Azure AD テナントに対する複数の同期サーバー
![サポートされていない、単一のフォレストのフィルター処理されたトポロジ](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

同じ Azure AD テナントに接続される複数の Azure AD Connect 同期サーバーはサポートされていません ([ステージング サーバー](#staging-server)は除きます)。 これらのサーバーが相互に排他的な一連のオブジェクトと同期するように構成されている場合でもサポートされません。 1 台のサーバーからフォレスト内のすべてのドメインに到達できない場合や、複数のサーバー間で負荷を分散したい場合に、このトポロジを検討したことがあるかもしれません。

## <a name="multiple-forests-single-azure-ad-tenant"></a>複数のフォレスト、単一の Azure AD テナント
![複数のフォレストと単一のテナントのトポロジ](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

多くの組織の環境には、オンプレミス Active Directory フォレストが複数存在します。 複数のオンプレミス Active Directory フォレストが使用される理由はさまざまです。 一般的な例として、アカウント リソース フォレストのある設計や、合併や買収の結果としての状況があります。

複数のフォレストがある場合は、1 つの Azure AD Connect 同期サーバーがすべてのフォレストにアクセスできる必要があります。 サーバーをドメインに参加させる必要はありません。 すべてのフォレストに到達する必要がある場合は、境界ネットワーク (DMZ、非武装地帯、スクリーン サブネットとも呼ばれます) にサーバーを配置できます。

Azure AD Connect のインストール ウィザードには、複数のフォレストで表されるユーザーを統合するためのいくつかのオプションが用意されています。 その目的は、ユーザーが Azure AD 内で 1 回だけ表されるようにすることです。 インストール ウィザードのカスタム インストール パスで構成できる一般的なトポロジはいくつかあります。 **[ユーザーを一意に識別]** ページで、トポロジを表す対応するオプションを選択します。 統合は、ユーザーに対してのみ構成されます。 重複しているグループは既定の構成に統合されません。

一般的なトポロジについては、[分離トポロジ](#multiple-forests-separate-topologies)、[フル メッシュ](#multiple-forests-full-mesh-with-optional-galsync)、および[アカウント リソース トポロジ](#multiple-forests-account-resource-forest)に関するセクションで説明しています。

Azure AD Connect 同期の既定の構成では、次のことを前提としています。

* 各ユーザーが持つ有効なアカウントは 1 つのみで、このアカウントが配置されているフォレストがユーザーの認証に使用されます。 これは、パスワード ハッシュ同期、パススルー認証、およびフェデレーションを前提としています。 UserPrincipalName と sourceAnchor/immutableID は、このフォレストから取得されます。
* 各ユーザーは、メールボックスを 1 つだけ持っています。
* ユーザーのメールボックスをホストするフォレストは、Exchange のグローバル アドレス一覧 (GAL) で確認できる属性に対して最適なデータ品質を備えています。 ユーザーにメールボックスがない場合、どのフォレストを使用してもこれらの属性値を提供できます。
* リンクされたメールボックスがある場合は、別のフォレストに、サインインに使用されるアカウントもあります。

環境がこれらの前提と一致しない場合は、次のようになります。

* アクティブなアカウントまたはメールボックスが複数ある場合、同期エンジンは 1 つを選び、他は無視します。
* 他のアクティブなアカウントを持たないリンクされたメールボックスは、Azure AD にはエクスポートされません。 ユーザー アカウントは、どのグループのメンバーとしても表されません。 DirSync のリンクされたメールボックスは、常に通常のメールボックスとして表されます。 この変更は、マルチフォレスト シナリオをより適切にサポートするための意図的に異なる動作です。

詳細については、[既定の構成](active-directory-aadconnectsync-understanding-default-configuration.md)に関するページを参照してください。

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>複数のフォレスト、1 つの Azure AD テナントに対する複数の同期サーバー
![複数のフォレストと複数の同期サーバーのサポートされていないトポロジ](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

1 つの Azure AD テナントに接続する複数の Azure AD Connect 同期サーバーはサポートされていません。 例外として、 [ステージング サーバー](#staging-server)の使用があります。

### <a name="multiple-forests-separate-topologies"></a>複数のフォレスト、分離トポロジ
![ユーザーがすべてのディレクトリで 1 度だけ示されるオプション](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![複数のフォレストと分離トポロジの説明図](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

この環境では、すべてのオンプレミス フォレストが個別のエンティティとして扱われます。 他のどのフォレストにもユーザーは存在しません。 各フォレストには独自の Exchange 組織があり、フォレスト間に GALSync はありません。 このトポロジは、合併や買収後の組織や、各部署が独立して運営されている組織で見られます。 Azure AD ではこれらのフォレストは同じ組織内にあり、統合された GAL で表示されます。 前の図では、すべてのフォレスト内の各オブジェクトが、一度メタバースに表され、ターゲットの Azure AD テナントに集約されます。

### <a name="multiple-forests-match-users"></a>複数のフォレスト: ユーザーの一致
これらのすべてのシナリオで共通しているのは、配布グループとセキュリティ グループにユーザー、連絡先、および外部セキュリティ プリンシパル (FSP) を組み合わせて含めることができることです。 FSP は、セキュリティ グループ内の他のフォレストのメンバーを表すために、Active Directory Domain Services (ADDS) で使用されます。 すべての FSP は、Azure AD 内の実際のオブジェクトに解決されます。

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>複数のフォレスト - オプションの GALSync を使用したフル メッシュ
![ユーザーの ID が複数のディレクトリに存在する場合の照合にメール属性を使用するオプション](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![複数のフォレストのフル メッシュ トポロジ](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

フル メッシュ トポロジでは、ユーザーおよびリソースを任意のフォレストに配置することができます。 一般的には、フォレスト間に双方向の信頼があります。

複数のフォレストに Exchange が存在する場合は、オンプレミス GALSync ソリューションが (オプションで) 存在することがあります。 その場合、すべてのユーザーが他のすべてのフォレストにおける連絡先として表されます。 通常、GALSync は FIM 2010 または MIM 2016 を通じて実装されます。 オンプレミス GALSync では、Azure AD Connect は使用できません。

このシナリオでは、ID オブジェクトはメール属性を通じて結合されます。 あるフォレストのメールボックスを持つユーザーが、他のフォレストの連絡先と結合されます。

### <a name="multiple-forests-account-resource-forest"></a>複数のフォレスト: アカウント リソース フォレスト
![ID が複数のディレクトリに存在する場合の照合に ObjectSID および msExchMasterAccountSID 属性を使用するオプション](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![複数のフォレストのアカウント リソース フォレスト トポロジ](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

アカウント リソース フォレスト トポロジでは、アクティブなユーザー アカウントを持つ 1 つ以上の "*アカウント*" フォレストが存在します。 また、アカウントが無効になった 1 つ以上の "*リソース*" フォレストも存在します。

このシナリオでは、1 つ (以上) のリソース フォレストがすべてのアカウント フォレストを信頼します。 リソース フォレストには、通常、Exchange および Lync を使用する拡張 Active Directory スキーマがあります。 すべての Exchange および Lync サービスと、他の共有サービスは、このフォレストに配置されます。 ユーザーのユーザー アカウントはこのフォレストで無効になり、メールボックスはアカウント フォレストにリンクされます。

## <a name="office-365-and-topology-considerations"></a>Office 365 とトポロジの考慮事項
Office 365 の一部のワークロードでは、サポートされるトポロジに一定の制限が生じます。

| ワークロード | 制限 |
| --------- | --------- |
| Exchange Online | Exchange Online でサポートされているハイブリッド トポロジの詳細については、「[Hybrid deployments with multiple Active Directory forests (複数の Active Directory フォレストを伴うハイブリッド展開)](https://technet.microsoft.com/library/jj873754.aspx)」を参照してください。 |
| Skype for Business | 複数のオンプレミス フォレストを使用している場合は、アカウント リソース フォレスト トポロジのみがサポートされます。 詳細については、「[Skype for Business Server 2015 の環境要件](https://technet.microsoft.com/library/dn933910.aspx)」を参照してください。 |

大規模な組織の場合は、[Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) 機能を使用することを検討してください。 これにより、ユーザーのリソースが配置されているデータ センターのリージョンを定義できます。

## <a name="staging-server"></a>ステージング サーバー
![トポロジでのステージング サーバー](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect では、"*ステージング モード*" でのセカンド サーバーのインストールがサポートされています。 このモードのサーバーは、接続されたすべてのディレクトリからデータを読み取りますが、接続されたディレクトリへの書き込みは行いません。 通常の同期サイクルを使用するため、ID データの更新されたコピーを保持します。

プライマリ サーバーで障害が発生した場合は、ステージング サーバーにフェールオーバーできます。 この操作は、Azure AD Connect ウィザードで実行します。 このセカンド サーバーは、インフラストラクチャをプライマリ サーバーと共有していないため、別のデータ センターに配置することができます。 プライマリ サーバーで行われたすべての構成の変更をセカンド サーバーに手動でコピーする必要があります。

ステージング サーバーは、新しいカスタム構成と、それがデータに与える影響をテストする場合に使用できます。 変化をプレビューし、構成を調整できます。 新しい構成に問題がなければ、ステージング サーバーをアクティブ サーバーにし、元のアクティブ サーバーをステージング モードに設定できます。

この方法は、アクティブな同期サーバーを交換する場合にも使用できます。 新しいサーバーを準備し、ステージング モードに設定してください。 サーバーが良好な状態であることを確認し、ステージング モードを無効 (アクティブ) にしたら、現在アクティブなサーバーをシャットダウンします。

異なるデータ センターに複数のバックアップを用意する場合は、複数のステージング サーバーを持つことができます。

## <a name="multiple-azure-ad-tenants"></a>複数の Azure AD テナント
組織の Azure AD には 1 つのテナントを置くことをお勧めします。
複数の Azure AD テナントの使用を計画する前に、[Azure AD の管理単位の管理](../users-groups-roles/directory-administrative-units.md)に関する記事を参照してください。 単一のテナントを使用できる一般的なシナリオを説明しています。

![複数のフォレストと複数のテナントのトポロジ](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect 同期サーバーと Azure AD テナントには、一対一のリレーションシップがあります。 各 Azure AD テナントに、1 つの Azure AD Connect 同期サーバーをインストールする必要があります。 Azure AD テナントのインスタンスは、分離される設計になっています。 つまり、あるテナントのユーザーは、他のテナントのユーザーを認識することができません。 この分離が望ましい場合、これはサポートされている構成です。 そうでない場合は、単一 Azure AD テナント モデルを使用する必要があります。

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Azure AD テナントでの各オブジェクトの 1 回のみの使用
![単一のフォレストのフィルター処理されたトポロジ](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

このトポロジでは、1 つの Azure AD Connect 同期サーバーが各 Azure AD テナントに接続されます。 各 Azure AD Connect 同期サーバーについては、操作対象のオブジェクトのセットが相互排他的になるようなフィルター処理を構成する必要があります。 たとえば、各サーバーのスコープを特定のドメインまたは組織単位に設定できます。

DNS ドメインは 1 つの Azure AD テナントにのみ登録できます。 オンプレミス Active Directory インスタンスのユーザーの UPN でも、別の名前空間を使用する必要があります。 たとえば、前の図では、3 つの個別の UPN サフィックスがオンプレミス Active Directory インスタンスの contoso.com、fabrikam.com、および wingtiptoys.com に登録されています。 各オンプレミス Active Directory ドメインのユーザーは、別の名前空間を使用します。

>[!NOTE]
>グローバル アドレス一覧同期 (GalSync) は、このトポロジでは自動的に行われず、各テナントの Exchange Online と Skype for Business Online に完全なグローバル アドレス一覧 (GAL) が含まれるようにするには、カスタム MIM の追加実装が必要です。


このトポロジには次の制約があります。その制約を除けば、各シナリオはサポートされます。

* Azure AD テナントのいずれか 1 つのみが、オンプレミスの Active Directory インスタンスを持つ Exchange ハイブリッドを有効にできます。
* Windows 10 デバイスは、1 つの Azure AD テナントだけに関連付けることができます。
* パスワード ハッシュ同期とパススルー認証のシングル サインオン (SSO) オプションは、1 つの Azure AD テナントでのみ使用できます。

オブジェクトの相互排他的なセットの要件は、書き戻しにも適用されます。 一部の書き戻し機能は、単一オンプレミス構成を前提としているため、このトポロジではサポートされていません。 次のような機能が該当します。

* 既定の構成によるグループの書き戻し。
* デバイスの書き戻し。

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Azure AD テナントでの各オブジェクトの複数回の使用
![単一のフォレストと複数のテナントのサポートされていないトポロジ](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![単一のフォレストと複数のコネクタのサポートされていないトポロジ](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

次のタスクはサポートされていません。

* 複数の Azure AD テナントへの同じユーザーの同期。
* 1 つの Azure AD テナントのユーザーを他の Azure AD テナントで連絡先として表示するような構成の変更。
* 複数の Azure AD テナントに接続するような Azure AD Connect 同期の変更。

### <a name="galsync-by-using-writeback"></a>書き戻しの使用による GALSync
![複数のフォレストと複数のディレクトリのサポートされていないトポロジ (GALSync は Azure AD に重点を置いている)](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![複数のフォレストと複数のディレクトリのサポートされていないトポロジ (GALSync はオンプレミスの Azure AD に重点を置いている)](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD のテナントは、分離するように設計されています。 次のタスクはサポートされていません。

* 他の Azure AD テナントからデータを読み取るような Azure AD Connect 同期の構成の変更。
* Azure AD Connect 同期を使用した、別のオンプレミス Active Directory インスタンスへの、連絡先としてのユーザーのエクスポート。

### <a name="galsync-with-on-premises-sync-server"></a>GALSync とオンプレミスの同期サーバー
![複数のフォレストと複数のディレクトリのトポロジにおける GALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 または MIM 2016 オンプレミスを使用して、2 つの Exchange 組織間でユーザーを同期できます (GALSync 経由)。 1 つの組織内のユーザーは、他の組織では外部ユーザーおよび連絡先として表示されます。 これらの異なるオンプレミス Active Directory インスタンスは、独自の Azure AD テナントと同期できます。

## <a name="next-steps"></a>次の手順
これらのシナリオのために Azure AD Connect をインストールする方法については、「 [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。

[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

詳細については、[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)に関するページを参照してください。
