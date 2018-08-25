---
title: Pandas を使用して Azure BLOB ストレージのデータを探索する | Microsoft Docs
description: Pandas を使用して Azure BLOB コンテナーに格納されているデータを探索する方法について説明します。
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 61943da154483d8e815fa135fba30b136724b011
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246659"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Pandas を使用して Azure BLOB ストレージのデータを探索する
このドキュメントでは、 [Pandas](http://pandas.pydata.org/) Python パッケージを使用して Azure BLOB コンテナーに格納されているデータを探索する方法について説明します。

次の **メニュー** は、ツールを使用してさまざまなストレージ環境のデータを探索する方法を説明するトピックにリンクしています。 このタスクは、 [Data Science Process](overview.md)の 1 ステップです。

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md)」をご覧ください。
* Azure BLOB ストレージ アカウントにデータが格納されている。 手順については、「 [Azure Storage との間でのデータの移動](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Pandas データ フレームにデータを読み込む
データセットを探索および操作するには、先にデータを BLOB ソースからローカル ファイルにダウンロードしておく必要があります。このローカル ファイルを、Pandas データ フレームに読み込むことができます。 この手順に必要な操作は次のとおりです。

1. BLOB サービスを使用する次の Python コード サンプルで Azure BLOB からデータをダウンロードします。 次のコードの変数を、実際の値に置き換えます。 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. ダウンロードしたファイルから、Pandas データ フレームにデータを読み込みます。
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

これで、データを探索し、このデータセットでの特徴を生成する準備が整いました。

## <a name="blob-dataexploration"></a>Pandas を使用したデータ探索の例
次に、Pandas を使用してデータを探索する方法の例をいくつかを示します。

1. **行と列の数** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. 次のデータセットの始めまたは終わりの数**行**を**検査**します。
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. 次のサンプル コードを使用して、インポートされた各列の **データ型** を確認します。
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. 次のように、データ セット内の列の **基本的な統計** を確認します。
   
        dataframe_blobdata.describe()
5. 次のように、各列の値のエントリの数を確認します。
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **不足値の数をカウント** します。
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. データ内の特定の列に **不足値** がある場合は、次のように削除できます。
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   不足値を置き換えるを別の方法として、モード関数を使用した以下のものを実行します。
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. 変数の分布をプロットする可変個の箱を使用して、 **ヒストグラム図** を作成します。    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. scatterplot を使用して、または組み込みの相関関数を使用して、変数間の **関連付け** を確認します。
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

