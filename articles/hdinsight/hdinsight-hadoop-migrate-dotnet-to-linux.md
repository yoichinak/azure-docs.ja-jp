---
title: Linux ベースの HDInsight での Hadoop MapReduce と .NET の使用 - Azure
description: Linux ベースの HDInsight で MapReduce をストリーミングするために .NET アプリケーションを使用する方法について説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 8650bad4d980efba78fe753200bca364bda26488
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595979"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Windows ベースの HDInsight から Linux ベースの HDInsight への .NET ソリューションの移行

Linux ベースの HDInsight クラスターでは、[Mono (https://mono-project.com)](https://mono-project.com) を使用して .NET アプリケーションを実行します。 Mono では、Linux ベースの HDInsight で MapReduce アプリケーションなどの .NET コンポーネントを使用することができます。 このドキュメントでは、Linux ベースの HDInsight 上で Mono を使用するために、Windows ベースの HDInsight クラスター用に作成された .NET ソリューションを移行する方法について学習します。

## <a name="mono-compatibility-with-net"></a>Mono と .NET の互換性

Mono バージョン 4.2.1 は HDInsight バージョン 3.6 に付属しています。 HDInsight に付属する Mono のバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md)」を参照してください。 特定のバージョンの Mono をインストールするには、[Mono のインストールと更新](hdinsight-hadoop-install-mono.md)に関するドキュメントを参照してください。

Mono と .NET 間の互換性の詳細については、[Mono の互換性に関するドキュメント (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/) をご覧ください。

> [!IMPORTANT]
> SCP.NET フレームワークは Mono と互換性があります。 詳細については、「[Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](storm/apache-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

## <a name="automated-portability-analysis"></a>自動の移植性分析

[.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) を使用して、アプリケーションと Mono の間の非互換性のレポートを生成できます。 アナライザーを構成してアプリケーションの Mono への移植性を確認するには、次の手順を使用します。

1. [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) をインストールします。 インストール時に、使用する Visual Studio のバージョンを選択します。

2. Visual Studio 2015 以降では、__[Analyze] \(分析)__ > __[Portability Analyzer Settings] \(移植性アナライザー設定)__ を選択し、__[Mono]__ セクションで __[4.5]__ がオンに設定されていることを確認します。

    ![Analyzer 設定の [Mono] セクションで [4.5] がチェックされた状態](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    __[OK]__ をクリックして構成を保存します。

3. __[Analyze] \(分析)__ > __[Analyze Assembly Portability] \(アセンブリ移植性の分析)__ を選択します。 ソリューションを含むアセンブリを選択して、__[開く] \(Open)__ を選択して分析を開始します。

4. 分析が完了したら、__[Analyze] \(分析)__ > __[View analysis reports] \(分析レポートを表示)__ を選択します。 __[Portability Analysis Results] \(移植性分析の結果)__ で、__[Open report] \(レポートを開く)__ を選択してレポートを開きます。

    ![移植性アナライザーの結果ダイアログ](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> アナライザーはソリューションのすべての問題に対応できません。 たとえば、Mono が Windows 上で実行されている場合、`c:\temp\file.txt` というファイル パスは有効と見なされます。 このパスは Linux プラットフォームでは無効です。

## <a name="manual-portability-analysis"></a>手動の移植性分析

[アプリケーションの移植性に関するドキュメント (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/) の情報を使用して、コードの手動監査を実行します。

## <a name="modify-and-build"></a>変更およびビルド

Visual Studio を引き続き使用して、HDInsight 用の .NET ソリューションを構築してもかまいません。 ただし、プロジェクトは .NET Framework 4.5 を使用するように構成されていなければなりません。

## <a name="deploy-and-test"></a>デプロイとテスト

.NET Portability Analyzer または手動の分析で得た推奨事項を使用してソリューションを変更したら、HDInsight でソリューションをテストする必要があります。 Linux ベースの HDInsight クラスター上でソリューションをテストすることで、修正する必要がある微妙な問題が明らかになる場合があります。 ソリューションをテストするとき、アプリケーション内で追加のログ記録を有効にしておくことをお勧めします。

ログへのアクセスの詳細については、次のドキュメントを参照してください。

* [Linux ベースの HDInsight での YARN アプリケーション ログへのアクセス](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>次の手順

* [HDInsight の MapReduce での C# の使用](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Hive と Pig での C# ユーザー定義関数の使用](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight での Storm の C# トポロジの開発](storm/apache-storm-develop-csharp-visual-studio-topology.md)
