---
title: Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ | Microsoft Docs
description: Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: mtillman
editor: piotrci
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15b52920774a878cd386ced5966d507768a8af70
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627391"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題

次の情報と例を使用して Azure Active Directory (Azure AD) のグループベースのライセンスについて詳しく理解できます。

## <a name="usage-location"></a>利用場所

一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 [Azure Portal](https://portal.azure.com) の **[ユーザー]** &gt; **[プロファイル]** &gt; **[設定]** で指定できます。

グループ ライセンスの割り当ての際、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 複数の場所にユーザーがいる場合、そのことをユーザー オブジェクトに正しく反映させてから、ライセンスが付与されたグループにユーザーを追加するようにしてください。

> [!NOTE]
> グループ ライセンスの割り当てによってユーザーの既存の利用場所の値が変更されることはありません。 Azure AD でのユーザー作成フローの一環として利用場所を常に設定することをお勧めします (AAD Connect 構成など)。これにより、ライセンス割り当ての結果が常に正しいことが保証され、ユーザーは許可されていない場所でサービスを受けることができなくなります。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>グループベースのライセンスを動的グループとともに使用する

グループベースのライセンスは任意のセキュリティ グループとともに使用できます。つまり、Azure AD の動的グループと組み合わせることができます。 動的グループでは、ユーザー オブジェクト属性に対して規則が実行され、グループのユーザーが自動的に追加、削除されます。

たとえば、ユーザーに割り当てる一連の製品の動的グループを作成できます。 各グループは属性でユーザーを追加する規則によって設定され、各グループには、受け取る必要があるライセンスが割り当てられます。 属性をオンプレミスで割り当てて Azure AD と同期するか、属性をクラウドで直接管理できます。

ライセンスはグループに追加された後すぐに、ユーザーに割り当てられます。 属性が変更されると、ユーザーはグループを離れ、ライセンスが削除されます。

### <a name="example"></a>例

どのユーザーが Microsoft Web サービスにアクセスできる必要があるかを決定するオンプレミスの ID 管理ソリューションの例について考えてみましょう。 **extensionAttribute1** を使用して、ユーザーが必要とするライセンスを表す文字列値を格納します。 Azure AD Connect がそれを Azure AD と同期します。

ユーザーはいずれか 1 つのライセンスのみが必要な場合もあれば、両方が必要となる場合もあります。 Office 365 Enterprise E5 および Enterprise Mobility + Security (EMS) ライセンスをグループのユーザーに配布する例を次に示します。

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: 基本サービス

![Office 365 Enterprise E5 の基本サービスのスクリーンショット](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: ライセンスを持つユーザー

![Enterprise Mobility + Security のライセンスを持つユーザーのスクリーンショット](./media/licensing-group-advanced/o365-e5-licensed-users.png)

この例では、ユーザーに両方のライセンスを割り当てる場合、1 人のユーザーを変更し、extensionAttribute1 の値を `EMS;E5_baseservices;` に設定します。 この変更はオンプレミスで実行できます。 変更がクラウドと同期されると、ユーザーは両方のグループに自動的に追加され、ライセンスが割り当てられます。

![ユーザーの extensionAttribute1 を設定する方法を示すスクリーン ショット](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> 既存のグループのメンバーシップ規則を変更するときには注意が必要です。 規則を変更すると、グループのメンバーシップが再評価され、新しい規則と一致しなくなったユーザーが削除されます (新しい規則とも一致しているユーザーについては、このプロセス中に影響はありません)。 このプロセス中に、これらのユーザーはライセンスが削除されます。その結果、サービスを利用できなくなったり、場合によってはデータが失われたりする可能性があります。

> ライセンス割り当ての基盤となる大きな動的グループがある場合、大規模な変更を加える際は、まず小さなテスト グループで検証し、それからメインのグループに適用することをご検討ください。

## <a name="multiple-groups-and-multiple-licenses"></a>複数のグループと複数のライセンス

ユーザーは、ライセンスを持つ複数のグループのメンバーになることができます。 考慮事項をいくつか以下に示します。

- 同じ製品の複数のライセンスが重複することがあり、その結果、有効になっているすべてのサービスがユーザーに適用されることがあります。 次の例は、2 つのライセンス グループを示します。"*E3 base services (E3 基本サービス)*" にはすべてのユーザーに先にデプロイする基本サービスが含まれています。 "*E3 extended services (E3 拡張サービス)*" には一部のユーザーにのみデプロイするその他のサービス (Sway と Planner) が含まれています。 この例では、ユーザーは両方のグループに追加されました。

  ![有効になっているサービスのスクリーンショット](./media/licensing-group-advanced/view-enabled-services.png)

  その結果、この製品のライセンスを 1 つのみ使用して、製品の 12 のサービスのうち 7 つが有効になっています。

- "*E3*" ライセンスを選ぶと、どのグループが原因でどのサービスがユーザーに対して有効になったかという情報などの詳細が表示されます。

  ![グループ別に有効になっているサービスのスクリーンショット](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>グループ ライセンスと共存する直接ライセンス

ユーザーがグループからライセンスを継承する場合、ユーザーのプロパティでライセンス割り当てを直接削除または変更することはできません。 グループで変更を行い、すべてのユーザーに反映する必要があります。

ただし、継承されたライセンスに加えて、ユーザーに対して同じ製品のライセンスを直接割り当てることができます。 他のユーザーに影響を与えずに、1 人のユーザーに対してのみ製品の追加サービスを有効にすることができます。

直接割り当てられたライセンスは削除できます。また、継承されたライセンスに影響しません。 グループから Office 365 Enterprise E3 ライセンスを継承するユーザーについて考えてみましょう。

1. 最初に、ユーザーは "*E3 basic services (E3 基本サービス)*" グループからのみライセンスを継承します。このグループでは、次のように 4 つのサービス プランが有効になります。

  ![E3 グループで有効になっているサービスのスクリーンショット](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. **[割り当て]** を選択して、E3 ライセンスをユーザーに直接割り当てることができます。 このケースでは、Yammer Enterprise 以外のすべてのサービス プランを無効にします。

  ![ユーザーにライセンスを直接割り当てる方法を示すスクリーンショット](./media/licensing-group-advanced/assign-license-to-user.png)

3. その結果、ユーザーは引き続き E3 製品のライセンスを 1 つだけ使用します。 ただし、直接割り当てにより Yammer Enterprise のサービスはそのユーザーに対してのみ有効になります。 グループ メンバーシップと直接割り当てによって有効になっているサービスを確認できます。

  ![継承と直接割り当てのスクリーンショット](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 直接割り当てを使用すると、次の操作を行うことができます。

  - Yammer Enterprise はユーザー オブジェクトに対して直接無効にできます。 その他のサービスと異なり、図の **[オン]/[オフ]** の切り替えがこのサービスに対して有効になっています。 サービスがそのユーザーに対して直接有効になっており、変更できるためです。
  - 直接割り当てられたライセンスの一部として、追加サービスも有効にすることができます。
  - **[削除]** ボタンを使用して、ユーザーから直接ライセンスを削除できます。 ユーザーは継承されたグループ ライセンスのみ持つようになり、元のサービスだけが有効なままであることを確認できます。

    ![直接割り当てを削除する方法を示すスクリーンショット](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>製品に追加される新しいサービスを管理する
Microsoft が製品に新しいサービスを追加すると、既定では、製品ライセンスを割り当てたすべてのグループでサービスが有効になります。 製品の変更に関する通知をサブスクライブしているテナントのユーザーは、今後のサービスの追加について事前に通知するメールを受け取ります。

管理者は、変更によって影響を受けるすべてのグループを確認し、各グループで新しいサービスを無効にするなどのアクションを実行できます。 たとえば、デプロイする特定のサービスのみを対象とするグループを作成した場合、それらのグループに再度アクセスし、新しく追加されたサービスが無効になっていることを確認することができます。

このプロセスの例を次に示します。

1. 当初、*Office 365 Enterprise E5* 製品がいくつかのグループに割り当てられています。 "*O365 E5 - Exchange のみ*" と呼ばれるこれらのグループの 1 つは、"*Exchange Online (プラン 2)*" サービスのみをメンバーに対して有効にするよう設計されています。

2. ユーザーは Microsoft から、E5 製品が新しいサービス (*Microsoft Stream*) で拡張される予定であるという内容の通知を受け取ります。 サービスがテナントで利用可能になると、次の操作を行うことができます。

3. [**[Azure Active Directory] > [ライセンス] > [すべての製品]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) ブレードに移動して、*[Office 365 Enterprise E5]* を選択してから、**[ライセンスされているグループ]** を選択し、その製品を持つすべてのグループの一覧を表示します。

4. 確認するグループをクリックします (この場合は、*[O365 E5 - Exchange のみ]*)。 これにより、**[ライセンス]** タブが開きます。E5 ライセンスをクリックすると、すべての有効なサービスを一覧表示するブレードが開きます。
> [!NOTE]
> このグループで *Exchange Online* サービスに加え、*Microsoft Stream* サービスも自動的に追加され、有効になっています。

  ![グループ ライセンスに追加された新しいサービスのスクリーン ショット](./media/licensing-group-advanced/manage-new-services.png)

5. このグループで新しいサービスを無効にする場合は、サービスの横にある **[オン]/[オフ]** の切り替えをクリックし、**[保存]** ボタンをクリックして変更を確認します。 これで、Azure AD はグループ内のすべてのユーザーに対する処理を実行して変更を適用します。新しいユーザーをグループに追加しても、*Microsoft Stream* サービスは有効にならなくなります。

  > [!NOTE]
  > 他のライセンス割り当て (そのユーザーが属する他のグループまたはライセンスの直接割り当て) によって、そのサービスがまだユーザーに対して有効になっている可能性があります。

6. 必要に応じて、この製品が割り当てられている他のグループにも同じ手順を実行します。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell を使用して、継承したライセンスと直接ライセンスを持つユーザーを表示する
PowerShell スクリプトを使用して、ユーザーがライセンスを直接割り当てられたかグループから継承されたかを確認できます。

1. `connect-msolservice` コマンドレットを実行してテナントを認証し、接続します。

2. `Get-MsolAccountSku` を使用して、テナントにプロビジョニングされているすべての製品ライセンスを検出します。

  ![Get-Msolaccountsku コマンドレットのスクリーンショット](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 対象ライセンスの *AccountSkuId* 値と、[この PowerShell スクリプト](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)を併用します。 そうすると、このライセンスを持っているユーザーの一覧と、ライセンスの割り当て状況に関する情報が生成されます。

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>監査ログを使用してグループベースのライセンスのアクティビティを監視する

[Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md#audit-logs)を使用して、グループベースのライセンスに関連するすべてのアクティビティを参照できます。以下に例を挙げます。
- グループのライセンスの変更者
- グループ ライセンスの変更処理が開始された時と完了した時
- グループ ライセンス割り当ての結果としてユーザーに加えられたライセンス変更の内容

>[!NOTE]
> 監査ログは、ポータルの Azure Active Directory セクションのほとんどのブレードで利用できます。 アクセス元のブレードによっては、フィルターが事前に適用され、そのブレードのコンテキストに関連するアクティビティだけが表示される場合があります。 期待どおりの結果が表示されない場合は、[フィルター処理オプション](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs)を調べるか、[ **[Azure Active Directory] > [アクティビティ] > [監査ログ]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)の下で、フィルター処理されていない監査ログにアクセスします。

### <a name="find-out-who-modified-a-group-license"></a>グループ ライセンスの変更者を検出する

1. **[アクティビティ]** フィルターを *[グループ ライセンスの設定]* に設定し、**[適用]** をクリックします。
2. 結果には、グループで設定または変更されているライセンスの事例がすべて含まれます。
>[!TIP]
> *[ターゲット]* フィルターにグループの名前を入力して、結果の範囲を指定することもできます。

3. 変更内容の詳細を参照するには、リスト ビュー内の項目をクリックします。 *[変更されたプロパティ]* の下に、ライセンス割り当ての新旧両方の値の一覧が表示されます。

最新のグループ ライセンスの変更と詳細の例を次に示します。

![グループ ライセンスの変更のスクリーン ショット](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>グループ変更処理が開始された時と完了した時を検出する

グループ上でライセンスが変更されると、Azure AD ではすべてのユーザーに対してその変更の適用が開始されます。

1. グループ上の処理が開始された時点を参照するには、**[アクティビティ]** フィルターを *[ユーザーに対するグループ ベースのライセンスの適用の開始]* に設定します。 この操作のアクターは、*Microsoft Azure AD グループベースのライセンス* - すべてのグループ ライセンスの変更を実行するために使用されるシステム アカウントであることにご注意ください。
>[!TIP]
> *[変更されたプロパティ]* フィールドを表示するには、一覧内の項目をクリックします。処理するために選ばれたライセンスの変更が表示されます。 これは、グループに複数の変更を加えて、処理した変更がわからなくなった場合に便利です。

2. 同様に、処理が完了したグループを表示するには、*[ユーザーに対するグループ ベースのライセンスの適用の終了]* フィルター値を使用します。
>[!TIP]
> この場合、*[変更されたプロパティ]* フィールドに結果の概要が含まれます。これは、処理の結果エラーが発生したかどうかをすばやく確認する場合に便利です。 サンプル出力:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. すべてのユーザー変更を含む、グループが処理された状況に関する完全なログを参照するには、次のフィルターを設定します。
  - **[開始者 (アクター)]**: "Microsoft Azure AD グループベースのライセンス"
  - **[期間]** (省略可能): 特定のグループが処理を開始した時と完了した時がわかっている場合、その期間を表すユーザー設定の範囲

このサンプル出力は、処理の開始時、処理結果のユーザー変更すべて、処理の終了時を示します。

![グループ ライセンスの変更のスクリーン ショット](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> *[ユーザー ライセンスの変更]* に関連する項目をクリックすると、個々のユーザーごとに適用されたライセンスの変更の詳細が表示されます。

## <a name="deleting-a-group-with-an-assigned-license"></a>ライセンスが割り当てられているグループを削除する

アクティブなライセンスが割り当てられているグループを削除することはできません。 管理者がグループを削除できてしまうと、気が付かないうちに、ユーザーからライセンスが削除されてしまいます。このため、こうしたグループを削除するには、最初にそのグループからライセンスを削除する必要があります。

Azure Portal でグループを削除しようとすると、次のようなエラー通知が表示されることがあります。![グループの削除失敗のスクリーンショット](./media/licensing-group-advanced/groupdeletionfailed.png)

グループの **[ライセンス]** タブに移動し、割り当てられているライセンスがないかどうかを確認します。 ライセンスが割り当てられている場合は、そのライセンスを削除してから、再度グループ削除してみてください。

PowerShell または Graph API でグループを削除しようとした場合も、同様のエラーが発生することがあります。 オンプレミスから同期されたグループを使用している場合、Azure AD でのグループ削除に失敗すると、Azure AD Connect によってエラーが通知されます。 このような場合は、そのグループにライセンスが割り当てられていないかどうかを確認し、そのライセンスを最初に削除します。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

グループベースのライセンスを使用する場合、次の制限事項と既知の問題の一覧について理解することをお勧めします。

- グループベースのライセンスでは、他のグループを含むグループ (入れ子になったグループ) を現在サポートしていません。 入れ子になったグループにライセンスを適用した場合は、グループの第 1 レベルのユーザー メンバーだけにライセンスが適用されます。

- この機能はセキュリティ グループでのみ使用できます。 Office グループでは現在サポートされておらず、ライセンスの割り当て処理で使用することはできません。

- 現在、[Office 365 管理ポータル](https://portal.office.com )では、グループベースのライセンスはサポートされていません。 ユーザーがライセンスをグループから継承する場合、このライセンスは Office 管理ポータルに通常のユーザー ライセンスとして表示されます。 そのライセンスを変更しようとした場合、または削除しようとした場合、ポータルはエラー メッセージを返します。 継承されたグループのライセンスをユーザーで直接変更することはできません。

- ユーザーがグループから削除され、ライセンスを失うと、そのライセンスのサービス プラン (例: SharePoint Online) は、「**中断**」状態に設定されます。 サービス プランは最終的な無効状態には設定されていません。 この予防措置によって、管理者がグループ メンバーシップの管理で間違いを犯した場合にユーザー データが誤って削除されるのを回避することができます。

- 大きなグループ (例: ユーザーが 100,000 人) に対してライセンスを割り当てるまたはライセンスを変更すると、パフォーマンスに影響が及ぶことがあります。 特に、Azure AD のオートメーションによって生成される変更の量によっては、Azure AD とオンプレミスのシステム間のディレクトリの同期のパフォーマンスに悪影響を及ぼす可能性があります。

- ある種の負荷の大きな状況では、ライセンスの処理に遅延が生じ、グループ ライセンスの追加/削除、グループのユーザーの追加/削除などの変更の処理にかかる時間が長くなることがあります。 変更の処理にかかっている時間が 24 時間を超えた場合には、[サポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) Microsoft に調査を依頼してください。 Microsoft では、*一般提供*の開始までにこの機能のパフォーマンス指標を向上させる予定です。

- ライセンス管理のオートメーションは、環境内のすべての種類の変更に自動的には反応しません。 たとえば、ライセンスの不足により一部のユーザーがエラー状態になる場合があります。 使用可能なシート数を解放するために、他のユーザーから直接割り当てられた一部のライセンスを削除できます。 ただし、システムがこの変更に自動的に反応してそのエラー状態のユーザーを修正することはありません。

  この種の制限の回避策として、Azure AD の 使用可能なシート数を解放するために、**[グループ]** ブレードに移動し、**[再処理]** ボタンをクリックします。 このコマンドにより、そのグループ内のすべてのユーザーが処理され、可能であればエラー状態が解決されます。

- グループベースのライセンスでは、Exchange Online でプロキシ アドレス構成が重複しているためにユーザーにライセンスを割り当てることができなかった場合、エラーは記録されません。ライセンス割り当ての際に、このようなユーザーはスキップされます。 この問題を特定して解決する方法の詳細については、[このセクション](licensing-groups-resolve-problems.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online)を参照してください。

## <a name="next-steps"></a>次の手順

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [What is group-based licensing in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Assigning licenses to a group in Azure Active Directory](licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
* [How to migrate individual licensed users to group-based licensing in Azure Active Directory](licensing-groups-migrate-users.md) (Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法)
