---
title: Azure HDInsight で非対話型認証 .NET アプリケーションを作成する
description: Azure HDInsight で非対話型認証 Microsoft .NET アプリケーションを作成する方法について説明します。
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: f5f1aae62d8f2959f26a361f3c1187037cafcca5
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591521"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>非対話型認証 .NET HDInsight アプリケーションを作成する
アプリケーション独自の ID (非対話型) またはアプリケーションのサインイン ユーザーの ID (対話型) のいずれかで、Microsoft .NET Azure HDInsight アプリケーションを実行できます。 この記事では、Azure に接続して HDInsight を管理する非対話型認証 .NET アプリケーションを作成する方法について説明します。 対話型アプリケーションのサンプルについては、「[Azure HDInsight への接続](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)」をご覧ください。 

非対話型 .NET アプリケーションでは、以下が必要です。

* Azure サブスクリプションのテナント ID (別名: *ディレクトリ ID*)。 「[Get tenant ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)」 (テナント ID を取得する) を参照してください。
* Azure Active Directory (Azure AD) アプリケーションのクライアント ID。 「[Azure Active Directory アプリケーションを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)」および「[アプリケーション ID と認証キーを取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」をご覧ください。
* Azure AD アプリケーションの秘密鍵。 「[アプリケーション ID と認証キーを取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」をご覧ください。

## <a name="prerequisites"></a>前提条件
* HDInsight クラスター。 [使用に関するチュートリアル](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)をご覧ください。

## <a name="assign-a-role-to-the-azure-ad-application"></a>Azure AD アプリケーションにロールを割り当てる
Azure AD アプリケーションに[ロール](../role-based-access-control/built-in-roles.md)を割り当てて、アクションを実行するためのアクセス許可を付与します。 スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます  (たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションはリソース グループとその中のリソースを読み取ることができます)。このチュートリアルでは、リソース グループ レベルでスコープを設定します。 詳細については、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

**Azure AD アプリケーションに所有者ロールを追加するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューの **[リソース グループ]** を選択します。
3. このチュートリアルの後半で Hive クエリを実行する HDInsight クラスターを含むリソース グループを選択します。 多数のリソース グループがある場合は、フィルターを使って目的のものを見つけることができます。
4. リソース グループ メニューで **[アクセス制御 (IAM)]** を選択します。
5. **[ユーザー]** で **[追加]** を選択します。
6. 説明に従って、所有者ロールを Azure AD アプリケーションに追加します。 ロールが正常に追加されると、**[ユーザー]** にアプリケーションが所有者ロール付きで表示されます。 

## <a name="develop-an-hdinsight-client-application"></a>HDInsight クライアント アプリケーションを開発する

1. C# コンソール アプリケーションを作成します。
2. 次の NuGet パッケージを追加します。

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. 次のコードを実行します。

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>次の手順
* [Azure Portal で Azure Active Directory アプリケーションとサービス プリンシパルを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
* [Azure Resource Manager でサービス プリンシパルを認証する](../azure-resource-manager/resource-group-authenticate-service-principal.md)方法を学習する。
* [Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) について学習する。
