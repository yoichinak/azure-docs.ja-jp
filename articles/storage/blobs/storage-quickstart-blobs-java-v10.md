---
title: 'Azure クイック スタート: Java Storage SDK V10 を使用してオブジェクト ストレージ内に BLOB を作成する | Microsoft Docs'
description: このクイック スタートでは、Java Storage SDK を使用して、オブジェクト (Azure BLOB) ストレージ内にコンテナーを作成し、ファイルをアップロードし、オブジェクトを一覧表示し、ダウンロードします。
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: 76b4cf2a8c16aa207b51faf3cc5252e47a368123
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42022858"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10-preview"></a>クイック スタート: Java Storage SDK V10 (プレビュー) を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、新しい Java Storage SDK を使用して、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 この新しい Java SDK では、リアクティブ プログラミング モデルと、非同期操作機能がある RxJava を使用します。 RxJava の詳細については、「[Java VM のリアクティブ拡張機能に関するページ](https://github.com/ReactiveX/RxJava)」を参照してください。 

## <a name="prerequisites"></a>前提条件

次のアプリケーションをインストールして、構成します。

* コマンド ラインから動作する [Maven](http://maven.apache.org/download.cgi)、またはご使用の Java 統合開発環境
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart)は、基本的なコンソール アプリケーションです。 

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。

プロジェクトのインポートが完了したら、**Quickstart.java** (**src/main/java/quickstart** にあります) を開きます。

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
このソリューションでは、ストレージ アカウントの名前とキーを安全に保存する必要があります。 サンプルを実行するマシンにローカルの環境変数に保存します。 ご使用のオペレーティング システムに応じて、Linux または Windows の例に従い、環境変数を作成します。

### <a name="linux-example"></a>Linux の場合の例

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows の場合の例

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルでは、Windows ユーザー向けに既定のディレクトリ (**AppData\Local\Temp**) にテスト ファイルが作成されます。 次の手順を実行するよう求められます。

1. テスト ファイルを Azure Blob Storage にアップロードするコマンドを入力します。
2. コンテナー内の BLOB を一覧表示します。
3. アップロードしたファイルを新しい名前でダウンロードし、古いファイルと新しいファイルを比較できるようにします。 

Maven を使用してコマンド ラインでサンプルを実行する場合は、シェルを開き、先ほど複製したディレクトリ内の **storage-blobs-java-v10-quickstart** を参照します。 次に「`mvn compile exec:java`」と入力します。

この例は、アプリケーションを Windows 上で実行した場合の出力を示しています。

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

サンプルを制御するため、そのコードを実行するコマンドを入力します。 入力には、大文字と小文字の区別があります。

[Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) などのツールを使って、Blob Storage 内のファイルを表示することもできます。 Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認します。 次に、**[E]** を選択し、**[Enter]\(入力\)** を選択してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、**Quickstart.java** ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次のセクションでは、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に、Blob Storage へのアクセスと管理のために使用されるオブジェクトへの参照を作成します。 これらのオブジェクトは、相互に関連した形で構築されます。 各オブジェクトは、一覧内の次のオブジェクトによって使用されます。

1. ストレージ アカウントを指す **StorageURL** オブジェクトのインスタンスを作成します。

    * [StorageURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) オブジェクトは、ストレージ アカウントを表したものです。 このオブジェクトは、新しいパイプラインを生成するために使用します。 
    * パイプラインは、承認、ログの記録、および再試行のメカニズムを使用して要求と応答を操作するために使用される一連のポリシーです。 詳細については、「[HTTP パイプラインに関する記事](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)」を参照してください。  
    * パイプラインを使用して、[ServiceURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview) オブジェクトのインスタンスを作成します。
    * **ServiceURL** オブジェクトを使用して、[ContainerURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) のインスタンスを作成します。
    * **ContainerURL** は、BLOB コンテナーに対して操作を実行するために必要です。

2. アクセスしているコンテナーを表す **ContainerURL** オブジェクトのインスタンスを作成します。 コンテナーが BLOB を整理する方法は、コンピューター上のフォルダーがファイルを整理する方法と同じです。

    * **ContainerURL** は、コンテナー サービスへのアクセス ポイントとして利用できます。 
    * [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview) オブジェクトのインスタンスを作成するには、[ContainerURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) を使用します。
    * **BlobURL** は、BLOB を作成するために必要です。

3. 関心がある特定の BLOB を指す **BlobURL** オブジェクトのインスタンスを作成します。 

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」 (コンテナー、BLOB、およびメタデータの名前付けと参照) を参照してください。

### <a name="create-a-container"></a>コンテナーを作成する 

このセクションでは、**ContainerURL** のインスタンスを作成し、 このインスタンスと共に新しいコンテナーを作成します。 このサンプルのコンテナーは **quickstartblobs** という名前です。 

この例では、[ContainerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview) を使用します。そのため、サンプルを実行するたびに新しいコンテナーを作成できます。 または、コードから作成する必要がないように、前もってコンテナーを作成しておいてもかまいません。

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 ブロック BLOB は最もよく使用されます。 このクイック スタートでも使用されています。 

1. ファイルを BLOB にアップロードするには、ターゲット コンテナーの BLOB に対する参照を取得します。 
2. BLOB 参照を取得すると、次のいずれかの API を使用してファイルを BLOB にアップロードできます。

    * 低レベルの API。 たとえば、**BlockBlobURL** のインスタンス内に [BlockBlobURL.upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) (PutBlob とも呼ばれる) や [BlockBlobURL.stageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) (PutBLock とも呼ばれる) があります。 

    * [TransferManager クラス](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview)内で提供される高レベル API。 たとえば、[TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview) メソッドがあります。 

    この操作では、BLOB がまだ存在しない場合は、BLOB が作成されます。 既に存在する場合は、BLOB が上書されます。

次のサンプル コードは、アップロードとダウンロードで使用されるローカル ファイルを作成し、 アップロード対象のファイルを **sourceFile** として格納し、BLOB の URL を **blob** に格納します。 次の例では、ファイルを **quickstart** という名前のコンテナーにアップロードします。

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

ブロック BLOB は、あらゆる種類のテキスト ファイルまたはバイナリ ファイルに使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 [BLOB の追加] は、末尾にデータを付加するために使用され、ログのためによく使用されます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview) を使用して、コンテナー内のオブジェクトの一覧を取得できます。 このメソッドは、最大 5,000 個のオブジェクトを一度に返します。コンテナー内に一覧表示するアイテムがまだある場合は、継続マーカー (次マーカー) も返します。 前回の **listBlobsFlatSegment** 応答に次マーカーがあるときは、自身を繰り返し呼び出すヘルパー関数を作成します。

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[BlobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview) を使用して、ローカル ディスクに BLOB をダウンロードします。

次のコードは、前のセクションでアップロードされた BLOB をダウンロードします。 **_DOWNLOADED** は、ローカル ディスク上で両方のファイルを確認できるように、BLOB の名前にサフィックスとして追加されます。 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートでアップロードした BLOB が不要になった場合は、[ContainerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview) を使用してコンテナー全体を削除できます。 このメソッドで、コンテナー内のファイルも削除されます。

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Java を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 

> [!div class="nextstepaction"]
> [Storage SDK V10 for Java のソース コード](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [API リファレンス](https://docs.microsoft.com/en-us/java/api/overview/azure/storage/client?view=azure-java-preview)
> [RxJava の詳細](https://github.com/ReactiveX/RxJava)
