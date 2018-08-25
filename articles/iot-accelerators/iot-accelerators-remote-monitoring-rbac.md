---
title: リモート監視のアクセス制御 - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータでロール ベースのアクセス制御 (RBAC) を構成する方法について説明します
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 8886c08e88c1dd46d2b325b9ba8e9e0b8ae104f9
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007699"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータでロール ベースのアクセス制御を構成する

この記事では、リモート監視ソリューション アクセラレータでロール ベースのアクセス制御を構成する方法について説明します。 ロール ベースのアクセス制御を使用して、ソリューションの特定の機能に対する個々のユーザーのアクセスを制限できます。

## <a name="default-settings"></a>既定の設定

リモート監視ソリューションの初めてのデプロイでは、**管理**と**読み取り専用**という 2 つのロールが含まれます。

**管理**ロールのユーザーは、ソリューションに対するフルアクセスを持ちます。 **読み取り専用**ロールのユーザーは、次のタスクのいずれも実行できません。

- アラームの更新
- アラームの削除
- デバイスの作成
- デバイスの更新
- デバイスの削除
- デバイス グループの作成
- デバイス グループの更新
- デバイス グループの削除
- 規則の作成
- 規則の更新
- 規則の削除
- ジョブの作成
- SIM 管理の更新

リモート監視ソリューションのデプロイを実行するユーザーには**管理者**ロールが自動的に割り当てられ、そのユーザーが Azure Active Directory アプリケーション所有者になります。 アプリケーション所有者は、Azure Portal で他のユーザーにロールを割り当てることができます。

別のユーザーがソリューションのロールを割り当てられるようにする場合は、Azure Portal でそのユーザーをアプリケーション所有者として設定する必要があります。

## <a name="add-or-remove-users"></a>ユーザーを追加または削除する

リモート監視ソリューションに対してユーザーの追加または削除を行うには、Azure Portal を使用します。 次の手順では、リモート監視ソリューションのデプロイ時に作成された [Azure Active Directory エンタープライズ アプリケーション](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application)を使用しています。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 使用する[ディレクトリにユーザーが存在している](../active-directory/fundamentals/add-users-azure-active-directory.md)ことを確認します。 使用するディレクトリは、[Microsoft Azure IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators) サイトにサインインしたときに選択したディレクトリです。 [ページ](https://www.azureiotsolutions.com/Accelerators)の右上隅にディレクトリ名が表示されています。

1. Azure Portal で、ソリューション用の**エンタープライズ アプリケーション**を見つけます。 アプリケーション名は、リモート監視ソリューションの名前です。 次のスクリーンショットでは、ソリューションとアプリケーションの表示名は **contoso rm4** です。

    ![エンタープライズ アプリケーション](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. アプリケーションをクリックし、**[所有者]** をクリックして、自分がアプリケーションの所有者であることを確認します。 次のスクリーンショットでは、**Contoso admin** が **contoso rm4** アプリケーションの所有者です。

    ![所有者](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    自分が所有者でない場合は、一覧に追加してもらうように既存の所有者に頼む必要があります。 所有者だけが、**管理者**や**読み取り専用**などのアプリケーション ロールを他のユーザーに割り当てることができます。

1. アプリケーションのロールに割り当てられているユーザーの一覧を表示するには、**[ユーザーとグループ]** をクリックします。

1. ユーザーを追加するには、**[+ ユーザーの追加]** をクリックします。次に、**[ユーザーとグループ、選択なし]** をクリックし、ディレクトリからユーザーを選択します。

1. ロールにユーザーを割り当てるには、**[ロールの選択、選択なし]** をクリックし、ユーザーに対して**管理者**または**読み取り専用**ロールのいずれかを選択します。 **[選択]** をクリックし、**[割り当て]** をクリックします。

    ![Select role](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. これで、ユーザーは、ロールによって定義されたアクセス許可を使用してリモート監視ソリューションにアクセスできます。

1. ポータルの **[ユーザーとグループ]** ページで、アプリケーションからユーザーを削除できます。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

初めてデプロイされたとき、リモート監視ソリューションには、**管理者** ロールと**読み取り専用**ロールが含まれています。 異なるアクセス許可セットを持つカスタム ロールを追加できます。 カスタム ロールを定義するには、以下を実行する必要があります。

- Azure Portal で新しいロールをアプリケーションに追加します。
- 新しいロールのポリシーを、認証および承認マイクロサービスに定義します。
- ソリューションの Web UI を更新します。

### <a name="define-a-custom-role-in-the-azure-portal"></a>Azure Portal でカスタム ロールを作成する

次の手順は、Azure Active Directory 内のアプリケーションにロールを追加する方法について説明しています。 この例では、リモート監視ソリューションでのデバイスの作成、更新、および削除をメンバーに許可する新しいロールを作成します。

1. Azure Portal で、ソリューション用の**アプリの登録**を見つけます。 アプリケーション名は、リモート監視ソリューションの名前です。 次のスクリーンショットでは、ソリューションとアプリケーションの表示名は **contoso rm4** です。

    ![アプリの登録](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. アプリケーションを選択し、**[マニフェスト]** をクリックします。 アプリケーションに対して定義された 2 つの既存の[アプリ ロール](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles)を確認できます。

    ![マニフェストの表示](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. 次のスニペットに示すように、マニフェストを編集して、**ManageDevices** という名前のロールを追加します。 新しいロールの ID として GUID などの一意の文字列が必要です。 [オンライン GUID ジェネレーター](https://www.guidgenerator.com/)などのサービスを使用して、新しい GUID を生成できます。

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    変更を保存します。

### <a name="define-a-policy-for-the-new-role"></a>新しいロール用のポリシーを定義する

Azure Portal でロールをアプリに追加した後、デバイスを管理するために必要なアクセス許可をロールに割り当てるポリシーを [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/pcs-auth/Services/data/policies/roles.json) 内に定義する必要があります。

1. [認証および承認マイクロサービス](https://github.com/Azure/pcs-auth-dotnet) リポジトリを GitHub からローカル コンピューターに複製します。

1. 次のスニペットに示すように、**Services/data/policies/roles.json** ファイルを編集して **ManageDevices** ロール用のポリシーを追加します。 **ID** と**ロール**の値は、前のセクションのアプリ マニフェスト内のロール定義と一致する必要があります。 許可されるアクションの一覧を使用して、**ManageDevices** ロールを割り当てられたユーザーに、ソリューションに接続されるデバイスの作成、更新、および削除を許可できます。

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. **Services/data/policies/roles.json** ファイルの編集が完了したら、認証および承認マイクロサービスをリビルドして、ソリューション アクセラレータに再デプロイします。

### <a name="how-the-web-ui-enforces-permissions"></a>Web UI によるアクセス許可の適用方法

Web UI は、[認証および承認マイクロサービス](https://github.com/Azure/pcs-auth-dotnet)を使用して、ユーザーが実行できるアクションと UI に表示されるコントロールを決定します。 たとえば、ソリューションの名前が **contoso-rm4** の場合、Web UI は、次の要求を送信することで、現在のユーザーに許可されているアクションの一覧を取得します。

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

**ManageDevices** ロールの**デバイス マネージャー**と呼ばれるユーザーの場合は、応答の本文に次の JSON が含まれます。

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

[Web UI](https://github.com/Azure/pcs-remote-monitoring-webui/) の [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) から抜粋した次のスニペットは、アクセス許可が宣言によって適用される方法を示しています。

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

詳細については、「[Protected Components](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md)」(保護されているコンポーネント) を参照してください。 [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) ファイルに追加のアクセス許可を定義できます。

### <a name="how-the-microservices-enforce-permissions"></a>マイクロサービスがアクセス許可を適用する方法

マイクロサービスは、承認されていない API 要求を防止するために、アクセス許可もチェックします。 マイクロサービスは、API 要求を受信したときに、JWT トークンのデコードと検証を行って、ユーザーのロールに関連付けられているユーザー ID とアクセス許可を取得します。

[IoTHub Manager マイクロサービス](https://github.com/Azure/iothub-manager-dotnet)内の [DevicesController.cs](https://github.com/Azure/iothub-manager-dotnet/blob/master/WebService/v1/Controllers/DevicesController.cs) ファイルから抜粋した次のスニペットは、アクセス許可の適用方法を示しています。

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータで役割に基づくアクセス コントロールがどのように実装されるかを説明しました。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください

リモート監視ソリューションのカスタマイズの詳細については、「[マイクロサービスをカスタマイズして再展開する](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->」をご覧ください。