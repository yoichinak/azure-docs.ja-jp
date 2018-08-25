---
title: Azure IoT Central アプリケーションを CSP として作成して管理する | Microsoft Docs
description: CSP として、顧客に代わって Azure IoT Central アプリケーションを作成する方法を説明します。
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: d32e05e99543b30ee92ea455ae2f800b09d83661
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012110"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>CSP として、顧客に代わって Azure IoT Central アプリケーションを作成して管理する 

Microsoft クラウド ソリューション プロバイダー (CSP) プログラムは、Microsoft リセラー プログラムです。 これは、すべての Microsoft Commercial Online Services を再販売するためのワンストップ プログラムをチャネル パートナーに提供することを目的としています。 詳しくは、「[Cloud Solution Provider プログラムの詳細](https://partner.microsoft.com/cloud-solution-provider)」をご覧ください。

CSP として、[Microsoft パートナー センター](https://partnercenter.microsoft.com/partner/home)を通じて、顧客に代わって Microsoft Azure IoT Central アプリケーションを作成して管理できます。 顧客に代わって CSP によって Azure IoT Central アプリケーションが作成される場合、Azure サービスによって管理される他の CSP と同様に、CSP が顧客の課金を管理します。 Azure IoT Central の料金には、Microsoft パートナー センターの請求金額の合計が表示されます。

最初に、Microsoft パートナー ポータルのアカウントにサインインし、Azure IoT Central アプリケーションを作成する顧客を選択します。 左側のナビゲーションから顧客のサービス管理に移動します。

![Microsoft パートナー センターのお客様が見る画面](media\howto-create-application-asCSP\image1.png)

Azure IoT Central は、管理に使用できるサービスとして表示されます。 ページの Azure IoT Central リンクをクリックして新しいアプリケーションを作成するか、この顧客の既存のアプリケーションを管理します。

![管理できる Azure IoT Central](media\howto-create-application-asCSP\image2.png)

Azure IoT Central の [Application Manager]\(アプリケーション マネージャー\) ページに移動します。 Azure IoT Central では、ユーザーが Microsoft パートナー センターから来て、その特定の顧客を管理することになったというコンテキストが維持されます。 これは、[Application Manager]\(アプリケーション マネージャー\) ページのヘッダーで確認できます。 ここから、この顧客に作成済みの既存のアプリケーションに移動してこれを管理するか、この顧客用の新しいアプリケーションを作成します。

![CSP のマネージャーの作成](media\howto-create-application-asCSP\image3.png)

Azure IoT Central アプリケーションを作成するには、**[新しいアプリケーション]** タイルをクリックします。 これによって、[Application Creation]\(アプリケーションの作成\) ページが読み込まれます。 このページのすべてのフィールドに入力してから、**[作成]** を選択する必要があります。 詳しくは、以下の各フィールドを参照してください。

![CSP 用のアプリケーションの作成ページ](media\howto-create-application-asCSP\image4-1.png)

![CSP 用のアプリケーションの作成ページ](media\howto-create-application-asCSP\image4-2.png)

## <a name="payment-plan"></a>支払プラン

CSP として作成できるのは、有料のアプリケーションのみです。 Azure IoT Central を顧客に示すために、個別に試用版アプリケーションを作成できます。 試用版および有料アプリケーションについては、[IoT Central の価格のページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

## <a name="application-name"></a>アプリケーション名

アプリケーションの名前は、**[Application Manager] (アプリケーション マネージャー)** ページと、各 Azure IoT Central アプリケーション内に表示されます。 Azure IoT Central アプリケーションとして任意の名前を選択できます。 自分および組織内の他のユーザーにとって意味のある名前を選択してください。

## <a name="application-url"></a>アプリケーションの URL

アプリケーションの URL は、アプリケーションへのリンクです。 そのブックマークをブラウザー内に保存するか、またはそれを他のユーザーと共有できます。

アプリケーションの名前を入力すると、アプリケーションの URL が自動生成されます。 必要に応じて、アプリケーションの別の URL を選択できます。 各 Azure IoT Central URL は、Azure IoT Central 内で一意である必要があります。 選択した URL が既に取得されている場合は、エラー メッセージが表示されます。

## <a name="directory"></a>Directory

Azure IoT Central では、ユーザーが Microsoft パートナー ポータルで選択した顧客を管理することになったというコンテキストが維持されるため、ユーザーは単に ディレクトリ フィールド内にその顧客の Azure Active Directory テナントを確認するだけです。 

Azure Active Directory テナントには、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 1 つの Azure Active Directory テナントに複数の Azure サブスクリプションを関連付けることができます。

詳細については、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) に関するページを参照してください。

## <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションを使用すると、Azure サービスのインスタンスを作成できます。 Azure IoT Central は、ユーザーがアクセスできるすべての顧客の Azure サブスクリプションを自動的に検索し、それを **[アプリケーションの作成]** ページのドロップダウンに表示します。 新しい Azure IoT Central アプリケーションを作成するための Azure サブスクリプションを選択します。

Azure サブスクリプションがない場合は、Microsoft パートナー センターで作成できます。 Azure サブスクリプションを作成したら、**[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。

詳細については、[Azure サブスクリプション](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing) に関するページを参照してください。

## <a name="region"></a>リージョン

Azure IoT Central アプリケーションを作成するリージョンを選択します。 通常、最適なパフォーマンスを得るには、デバイスに物理的に最も近いリージョンを選択する必要があります。

詳細については、[Azure リージョン](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions)に関するページを参照してください。

Azure IoT Central を使用できるリージョンは、[[リージョン別の利用可能な製品]](https://azure.microsoft.com/regions/services/) ページで確認できます。

> [!Note]
> いったんリージョンを選択すると、後でアプリケーションを別のリージョンに移動することはできません。

## <a name="application-template"></a>アプリケーション テンプレート

新しい Azure IoT Central アプリケーションには、使用可能なアプリケーション テンプレートのずれかを選択できます。 アプリケーション テンプレートには、使用開始に役立つデバイス テンプレートやダッシュボードなどの事前に定義された項目を含めることができます。

| アプリケーション テンプレート | 説明 |
| -------------------- | ----------- |
| カスタム アプリケーション   | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。 |
| サンプル Contoso       | 単純な接続デバイス用のデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、Azure IoT Central の調査を開始するために使用します。 |
| サンプル Devkit       | MXChip または Raspberry Pi デバイスを接続するための準備ができたデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、これらのいずれかのデバイスでのコードを経験しているデバイス開発者である場合に使用します。 |

## <a name="next-steps"></a>次の手順

ここでは、CSP として Azure IoT Central アプリケーションを作成する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)