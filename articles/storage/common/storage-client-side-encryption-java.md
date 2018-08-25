---
title: Java による Microsoft Azure Storage のクライアント側の暗号化 | Microsoft Docs
description: Java 用 Azure Storage クライアント ライブラリはクライアント側の暗号化と Azure Key Vault との統合を支援して、Azure Storage アプリケーションのセキュリティを最大限に高めます。
services: storage
author: lakasa
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 995b54d8eb53efc6001e29366e22e09b246f0bff
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522018"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Java による Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>概要
[Java 用 Azure Storage クライアント ライブラリ](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) は、Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化し、クライアントにダウンロードするときにデータを復号化する作業を支援します。 また、このライブラリは [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) の統合にも役立ち、ストレージ アカウント キー管理に利用することもできます。

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>エンベロープ手法による暗号化と復号化
暗号化と復号化のプロセスは、エンベロープ手法に倣います。  

### <a name="encryption-via-the-envelope-technique"></a>エンベロープ手法による暗号化
エンベロープ手法による暗号化は、次の方法で機能します。  

1. Azure ストレージ クライアント ライブラリは、1 回使用の対称キーであるコンテンツ暗号化キー (CEK) を生成します。  
2. ユーザー データは、この CEK を使用して暗号化されます。   
3. CEK は、キー暗号化キー (KEK) を使用してラップ (暗号化) されます。 KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルに管理することも、Azure Key Vault に保存することもできます。  
   ストレージ クライアント ライブラリ自体が KEK にアクセスすることはありません。 ライブラリは、Key Vault によって提供されるキー ラップ アルゴリズムを呼び出すだけです。 ユーザーは、必要な場合は、キー ラップ/ラップ解除にカスタム プロバイダーを使用できます。  
4. 暗号化されたデータは、Azure Storage サービスにアップロードされます。 ラップされたキーは追加の暗号化メタデータと共にメタデータとして (BLOB に) 格納されるか、暗号化されたデータ (キュー メッセージやテーブル エンティティ) によって補間されます。

### <a name="decryption-via-the-envelope-technique"></a>エンベロープ手法による復号化
エンベロープ手法による復号化は、次の方法で機能します。  

1. クライアント ライブラリでは、ユーザーがキー暗号化キー (KEK) をローカルまたは Azure Key Vault のいずれかで管理することを前提としています。 ユーザーは、暗号化に使用された特定のキーを把握しておく必要はありません。 代わりに、さまざまなキー識別子をキーに解決する Key Resolver を設定、使用できます。  
2. クライアント ライブラリは、暗号化されたデータおよびサービスに格納されている暗号化に関する情報 (ある場合) をダウンロードします。  
3. 次に、ラップされたコンテンツ暗号化キー (CEK) が、キー暗号化キー (KEK) によりラップ解除 (復号化) されます。 ここでも、クライアント ライブラリが KEK にアクセスすることはありません。 これは、単にカスタムの、または Key Vault プロバイダーのラップ解除アルゴリズムを起動するだけです。  
4. 次に、コンテンツ暗号化キー (CEK) を使用して、暗号化されたユーザー データを復号化します。

## <a name="encryption-mechanism"></a>暗号化メカニズム
ストレージ クライアント ライブラリは [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) を使用して、ユーザー データを暗号化します。 具体的には、AES で [暗号ブロック チェーン (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) モードを使用します。 サービスごとに動作が多少異なるため、以下でそれぞれについて説明します。

### <a name="blobs"></a>BLOB
現在、クライアント ライブラリでは BLOB 全体の暗号化のみがサポートされています。 具体的には、**upload*** メソッドや **openOutputStream** メソッドを使用するときに暗号化がサポートされます。 ダウンロードについては、完全ダウンロードと一部の範囲のダウンロードの両方がサポートされています。  

暗号化中、クライアント ライブラリは 16 バイトのランダムな初期化ベクトル (IV) と 32 バイトのランダムなコンテンツ暗号化キー (CEK) を生成し、この情報を使用して BLOB データのエンベロープ暗号化を実行します。 ラップされた CEK と一部の追加暗号化メタデータが、サービスの暗号化された BLOB と共に、BLOB メタデータとして格納されます。

> [!WARNING]
> BLOB のメタデータを編集またはアップロードする場合は、このメタデータを保持している必要があります。 このメタデータなしで新しいメタデータをアップロードした場合、ラップされた CEK、IV、およびその他のメタデータは失われ、BLOB コンテンツが再度取得可能になることはありません。
> 
> 

暗号化された BLOB のダウンロードには、便利なメソッド **download*/openInputStream** を使用した BLOB 全体のコンテンツの取得も含まれます。 ラップされた CEK はラップ解除され、復号化されたデータをユーザーに返すために IV (この場合 BLOB メタデータとして格納された) と共に使用されます。

暗号化された BLOB での任意の範囲 (**downloadRange*** メソッド) のダウンロードでは、ユーザーが指定した範囲が調整されます。これは、少量の追加データを取得して、要求された範囲を正常に復号化するためです。  

このスキームを使用して、すべての BLOB 型 (ブロック BLOB、ページ BLOB、および追加 BLOB) を暗号化/復号化できます。

### <a name="queues"></a>キュー
キュー メッセージの書式は一定でないため、クライアント ライブラリでは、メッセージ テキストで初期化ベクトル (IV) と暗号化されたコンテンツ暗号化キー (CEK) を含むカスタム書式が定義されます。  

暗号化中、クライアント ライブラリは 16 バイトのランダムな IV と 32 バイトのランダムな CEK を生成し、この情報を使用してキュー メッセージ テキストのエンベロープ暗号化を実行します。 次に、ラップされた CEK と追加の暗号化メタデータのいくつかが、暗号化されたキュー メッセージに追加されます。 この変更されたメッセージ (下記参照) は、サービスに格納されます。

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

復号化中、ラップされたキーがキュー メッセージから抽出され、ラップ解除されます。 また、IV もキュー メッセージから抽出され、これをラップ解除されたキーと共に使用して、キュー メッセージ データが復号化されます。 暗号化メタデータは小さいため (500 バイト未満)、キュー メッセージの 64 KB という上限を考慮したとき、影響が小さくて済みます。

### <a name="tables"></a>テーブル
クライアント ライブラリでは、挿入および置換操作のエンティティ プロパティの暗号化がサポートされます。

> [!NOTE]
> 現在、マージはサポートされていません。 別のキーを使用してプロパティのサブセットが以前に暗号化されている場合、新しいプロパティをマージしたり、メタデータを更新したりするとデータ損失が発生します。 マージでは、追加のサービス呼び出しをして既存のエンティティをサービスから読み込むか、プロパティごとに新しいキーを使用する必要があります。いずれの方法も、パフォーマンス上の理由でお勧めできません。
> 
> 

テーブル データの暗号化は、次のように機能します。  

1. 暗号化するプロパティをユーザーが指定します。  
2. クライアント ライブラリは 16 バイトのランダムな初期化ベクトル (IV) と 32 バイトのランダムなコンテンツ暗号化キー (CEK) を各エンティティごとに生成し、プロパティごとに新しい IV を派生させることで暗号化する個々のプロパティでエンベロープ暗号化を実行します。 暗号化されたプロパティは、バイナリ データとして格納されます。  
3. 次に、ラップされた CEK と追加の暗号化メタデータが、2 つの追加の予約済みプロパティとして格納されます。 最初の予約済みプロパティ (_ClientEncryptionMetadata1) は文字列プロパティで、IV、バージョン、およびラップされたキーに関する情報を保持します。 2 番目の予約済みプロパティ (_ClientEncryptionMetadata2) はバイナリ プロパティで、暗号化されたプロパティに関する情報を保持します。 この 2 番目のプロパティ (_ClientEncryptionMetadata2) 内の情報自体が暗号化されます。  
4. これらの暗号化に必要な追加の予約済みプロパティにより、ユーザーが所有できるカスタム プロパティは 252 ではなく、250 個になります。 エンティティの合計サイズは、1 MB 未満である必要があります。  
   
   暗号化できるのは、文字列プロパティのみであることに注意してください。 他の種類のプロパティを暗号化する必要がある場合は、文字列に変換する必要があります。 暗号化された文字列はバイナリ プロパティとしてサービスで保存され、復号化された後、文字列に変換されて戻されます。
   
   テーブルの場合、暗号化ポリシーに加え、ユーザーは暗号化するプロパティを指定する必要があります。 これを実行するには、[Encrypt] 属性を指定するか (TableEntity から派生した POCO エンティティ用)、または要求オプションで暗号化リゾルバーを指定します。 暗号化リゾルバーは、パーティション キー、行キー、プロパティ名を取得するデリゲートで、プロパティを暗号化するかどうかを示すブール値を返します。 暗号化時、クライアント ライブラリはこの情報を使用して、ネットワークへの書き込み時にプロパティを暗号化するかどうかを決定します。 また、デリゲートは、プロパティの暗号化方法に関するロジックを使用する可能性にも備えます。 (X の場合、プロパティ A を暗号化し、それ以外の場合はプロパティ A および B を暗号化するなど。)エンティティの読み込み中、またはクエリの実行中は、この情報を指定する必要はありません。

### <a name="batch-operations"></a>バッチ操作
バッチ操作では、そのバッチ操作のすべての行で同じ KEK が使用されます。これは、クライアント ライブラリで、各バッチ操作あたり 1 つの options オブジェクト (従って、1 ポリシー/KEK) のみが許可されるためです。 ただし、クライアント ライブラリは、バッチ内の行ごとに 1 つの新しいランダム IV とランダム CEK を内部的に生成します。 ユーザーは、暗号化リゾルバーでこの動作を定義することで、バッチの各操作で個別のプロパティを暗号化することも選択できます。

### <a name="queries"></a>クエリ
> [!NOTE]
> エンティティは暗号化されているため、暗号化されたプロパティでフィルター処理を行うクエリを実行できません。  実行しようとすると、暗号化されたデータと、暗号化されていないデータの比較が行われるため、結果が不正確になります。
> 
>
クエリ操作を実行するには、結果セット内のすべてのキーを解決できる Key Resolver を指定する必要があります。 クエリの結果に含まれたエンティティをプロバイダーに解決できない場合、クライアント ライブラリでエラーがスローされます。 クエリでサーバー側のプロジェクションが実行される場合、クライアント ライブラリは既定で、特別な暗号化メタデータ プロパティ (_ClientEncryptionMetadata1 および _ClientEncryptionMetadata2) を選択した列に追加します。

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Azure Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 詳細については、「 [Azure Key Vault とは](../../key-vault/key-vault-whatis.md)」を参照してください。

ストレージ クライアント ライブラリは Key Vault のコア ライブラリを使用して、Azure 全体でのキー管理用の一般的なフレームワークを提供します。 Key Vault 拡張機能ライブラリを使用すると追加のメリットも得られます。 拡張機能ライブラリには、シンプルかつシームレスな対称/RSA ローカルおよびクラウドのキー プロバイダーに関する便利な機能や、集計またはキャッシュに関する機能が用意されています。

### <a name="interface-and-dependencies"></a>インターフェイスと依存関係
次の 3 種類の Key Vault パッケージがあります。  

* azure-keyvault-core には、IKey と IKeyResolver が含まれています。 これは、依存関係のない小型パッケージです。 Java 用ストレージ クライアント ライブラリでは、依存関係としてそれを定義します。
* azure-keyVault には Key Vault REST クライアントが含まれています。  
* azure-keyvault-extensions には、暗号化アルゴリズム、RSAKey、および SymmetricKey の実装を含む拡張機能コードが含まれています。 これは、コアおよび KeyVault 名前空間に依存し、集計リゾルバー (複数のキー プロバイダーを使用する必要がある場合) およびキャッシュ キー リゾルバーを定義する機能を提供します。 ストレージ クライアント ライブラリはこのパッケージに直接依存しませんが、Azure Key Vault を使用してキーを格納するか、Key Vault 拡張機能を使用してローカルおよびクラウドの暗号化プロバイダーを使用する必要がある場合はこのパッケージが必要です。  
  
  Key Vault は値の高いマスター キー向けで、Key Vault ごとのスロットルの上限はこれを念頭に設計されています。 Key Vault を使用してクライアント側の暗号化を実行するときに推奨されるモデルは、Key Vault 内のシークレットやローカルにキャッシュされたシークレットとして格納された対象マスター キーを使用することです。 次の操作を実行する必要があります。  

1. シークレットをオフラインで作成し、Key Vault にアップロードします。  
2. シークレットのベース識別子をパラメーターとして使用して、シークレットの現在のバージョンを暗号化用に解決し、この情報をローカルでキャッシュします。 キャッシュ用の CachingKeyResolver の使用: ユーザーが自身のキャッシュ ロジックを実装することは想定されていません。  
3. 暗号化ポリシーの作成時に、入力としてキャッシュ リゾルバーを使用します。
   Key Vault の使用方法について詳しくは、暗号化コードのサンプルを参照してください。 <fix URL>  

## <a name="best-practices"></a>ベスト プラクティス
暗号化のサポートは、Java 用のストレージ クライアント ライブラリでのみ使用できます。

> [!IMPORTANT]
> クライアント側の暗号化を使用する場合は、次の重要な点に注意してください。
> 
> * 暗号化された BLOB を読み書きするときは、完全 BLOB アップロード コマンドと範囲/完全 BLOB ダウンロード コマンドを使用してください。 Put Block、Put Block List、Write Pages、Clear Pages、または Append Block などのプロトコル操作で暗号化された BLOB に書き込まないでください。暗号化された BLOB が壊れたり、読み取り不可能になったりすることがあります。
> * テーブルの場合、同様の制約があります。 暗号化メタデータを更新せずに暗号化されたプロパティを更新する行為は避けてください。  
> * 暗号化された BLOB にメタデータを設定する場合、メタデータの設定は付加的には行われないため、復号化に必要な暗号化関連メタデータが上書きされる可能性があります。 これはスナップショットにも該当します。暗号化された BLOB のスナップショットを作成するときにメタデータを指定しないでください。 メタデータを設定する必要がある場合は、最初に **downloadAttributes** メソッドを呼び出し、現在の暗号化メタデータを取得してください。メタデータの設定中に、同時書き込みは行わないでください。  
> * 暗号化されたデータのみを処理する必要のあるユーザーについては、既定の要求オプションで、 **requireEncryption** フラグを有効にします。 詳細については、以下をご覧ください。  
> 
> 

## <a name="client-api--interface"></a>クライアント API/インターフェイス
EncryptionPolicy オブジェクトの作成では、キーのみ (IKey の実装)、リゾルバーのみ (IKeyResolver の実装)、またはその両方を指定できます。 IKey は基本的なキーの種類で、キー識別子を使用して識別され、ラップ/ラップ解除のロジックを指定します。 IKeyResolver は復号化プロセス中のキーの解決に使用します。 これは、IKey で指定されたキー識別子を返す ResolveKey メソッドを定義します。 これは、複数の場所で管理されている複数のキーの中から選択するための機能を提供します。

* 暗号化では、キーは常に使用され、キーがないとエラーが発生します。  
* 復号化の場合:  
  
  * キーを取得するよう指定した場合にキー リゾルバーが起動します。 リゾルバーが指定されていても、キー識別子のマッピングがない場合、エラーがスローされます。  
  * リゾルバーが指定されていない場合にキーが指定されると、そのキーの識別子が必須キー識別子と一致すると、そのキーが使用されます。 識別子が一致しなければ、エラーがスローされます。  
    
    BLOB、キュー、テーブルや、Key Vault 統合の詳しいエンド ツー エンド シナリオについては、「[暗号化のサンプル](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>」をご覧ください。

### <a name="requireencryption-mode"></a>RequireEncryption モード
すべてのアップロードとダウンロードを暗号化する必要がある場合、オプションで操作のモードを有効にすることができます。 このモードでは、暗号化ポリシーを設定せずにデータをアップロードしようとしたり、サービスで暗号化されていないデータをダウンロードしようとしたりすると、クライアントで失敗します。 要求オプション オブジェクトの **requireEncryption** フラグによって、この動作が制御されます。 Azure Storage に保存されているすべてのオブジェクトがアプリケーションによって暗号化される場合、サービス クライアント オブジェクトの既定の要求オプションに **requireEncryption** プロパティを設定できます。   

たとえば、 **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** を使用して、そのクライアント オブジェクトを介して実行されるすべての BLOB 操作に対して暗号化を要求します。

### <a name="blob-service-encryption"></a>Blob service 暗号化
**BlobEncryptionPolicy** オブジェクトを作成し、それを要求オプションに設定します (API ごとに、または **DefaultRequestOptions** を使用してクライアント レベルで設定します)。 その他の操作はすべて、クライアント ライブラリが内部的に処理します。

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Queue サービス暗号化
**QueueEncryptionPolicy** オブジェクトを作成し、それを要求オプションに設定します (API ごとに、または **DefaultRequestOptions** を使用してクライアント レベルで設定します)。 その他の操作はすべて、クライアント ライブラリが内部的に処理します。

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Table service 暗号化
暗号化ポリシーを作成し、要求オプションにそれを設定するだけでなく、**EncryptionResolver** を **TableRequestOptions** に指定するか、エンティティの getter と setter に [Encrypt] 属性を設定する必要があります。

### <a name="using-the-resolver"></a>リゾルバーの使用

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>属性の使用
前述のように、エンティティで TableEntity を実装した場合、getter プロパティおよび setter プロパティは、 **EncryptionResolver**を指定するのではなく、[Encrypt] 属性で修飾できます。

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>暗号化とパフォーマンス
ストレージ データを暗号化すると、パフォーマンスのオーバーヘッドが増えることに注意してください。 コンテンツ キーと IV を生成する必要があり、コンテンツ自体を暗号化する必要があります。また、追加のメタデータをフォーマットおよびアップロードする必要もあります。 このオーバーヘッドは、暗号化されるデータの量によって異なります。 開発中に、アプリケーションのパフォーマンスを常にテストすることをお勧めします。

## <a name="next-steps"></a>次の手順
* [Azure Storage Client Library for Java の Maven パッケージ](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* [Azure Storage Client Library for Java のソースコード](https://github.com/Azure/azure-storage-java)   
* Java Maven パッケージの Azure Key Vault Maven Library をダウンロードする:
  * [コア](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) パッケージ
  * [クライアント](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) パッケージ
* [Azure Key Vault のドキュメント](../../key-vault/key-vault-whatis.md)
