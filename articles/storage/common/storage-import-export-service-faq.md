---
title: Azure Import/Export サービスに関する FAQ | Microsoft Azure
description: Azure Import/Export サービスについてよく寄せられる質問とその回答を紹介します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 6a2d067625e96aefdcb0197e1abb13b0c41fbe7f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521440"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export サービス: よく寄せられる質問 
以下では、Azure Import/Export サービスを使用してデータを Azure Storage に転送するときによくある質問と、それに対する回答を示します。 質問と回答は次の各カテゴリに分類されます。

- Import/Export サービスについて
- インポート/エクスポートのためのディスクの準備
- Import/Export ジョブ
- ディスクの発送
- その他 

## <a name="about-importexport-service"></a>Import/Export サービスについて

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Azure Import/Export サービスを使用して Azure File Storage をコピーすることはできますか?

はい。 Azure Import/Export サービスでは Azure File Storage へのインポートがサポートされます。 現時点では、Azure Files のエクスポートはサポートされていません。

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>CSP サブスクリプションで Azure Import/Export サービスを使用できますか?

はい。 Azure Import/Export サービスは、クラウド ソリューション プロバイダー (CSP) サブスクリプションをサポートします。

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Azure Import/Export サービスを使用して、PST メールボックスと SharePoint データを O365 にコピーすることはできますか?

はい。 詳しくは、[Office 365 への PST ファイルまたは SharePoint データのインポート](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)に関する記事をご覧ください。

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Azure Import/Export サービスを使用して、Azure Backup サービスにバックアップをオフラインでコピーすることはできますか?

はい。 詳しくは、「[Azure Backup でのオフライン バックアップのワークフロー](../../backup/backup-azure-backup-import-export.md)」をご覧ください。

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>インポート/エクスポート ジョブのドライブを Microsoft から購入できますか?

いいえ。 インポート ジョブとエクスポート ジョブで、自分のドライブを発送する必要があります。


## <a name="preparing-disks-for-importexport"></a>インポート/エクスポートのためのディスクの準備

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>インポート ジョブのドライブ準備手順をスキップすることはできますか? コピーなしでドライブを準備できますか?

いいえ。 データをインポートするために使用するドライブは、Azure WAImportExport ツールを使用して準備する必要があります。 ドライブへのデータのコピーにも、このツールを使用してください。

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>エクスポート ジョブを作成するときに、ディスクの準備作業を実行する必要はありますか?

いいえ。 いくつかの事前チェックをお勧めします。 必要なディスクの数を確認するには、WAImportExport ツールの PreviewExport コマンドを使います。 詳細については、「 [Previewing Drive Usage for an Export Job (エクスポート ジョブのドライブ使用率のプレビュー)](https://msdn.microsoft.com/library/azure/dn722414.aspx)」をご覧ください。 このコマンドは、使用するドライブのサイズに基づいて、選択した BLOB に対するドライブの使用率をプレビューするのに役立ちます。 また、エクスポート ジョブに対して発送されるハード ドライブを読み書きできることを確認します。

## <a name="importexport-jobs"></a>Import/Export ジョブ

### <a name="can-i-cancel-my-job"></a>ジョブを取り消すことはできますか?
はい。 ジョブの状態が**作成**または**発送**のときは、ジョブを取り消すことができます。 これらのステージを過ぎると、ジョブをキャンセルすることはできず、最後のステージまで行われます。

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Azure Portal では、どのくらいの期間、完了したジョブの状態を確認できますか?
完了したジョブの状態は、最大 90 日間、表示されます。 90 日が経過すると、完了したジョブは削除されます。

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>10 台を超えるドライブでインポートまたはエクスポートするには、どうすればよいでしょうか?
1 つのインポート ジョブまたはエクスポート ジョブが参照できるのは、10 台のドライブまでです。 10 台を超えるドライブを発送するには、複数のジョブを作成する必要があります。 同じジョブに関連付けられているドライブは、同じパッケージにまとめて発送する必要があります。 データ容量が複数ディスクのインポート ジョブにわたるときの詳細とガイダンスについては、Microsoft (bulkimport@microsoft.com) に問い合わせてください。                                                              

## <a name="shipping-disks"></a>ディスクの発送

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>一度に発送できる HDD は最大で何個ですか?
1 回で発送できる HDD の数に制限はありません。 ディスクが複数のジョブに属する場合は、次のようにすることをお勧めします。 
- 対応するジョブ名のラベルをディスクに付けます。
- -1、-2 などのサフィックスが付いた追跡番号でジョブを更新します。

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>HDD 以外のものをパッケージに含める必要はありますか?
発送パッケージではハード ドライブだけを発送してください。 電源ケーブルや USB ケーブルなどは同梱しないでください。

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>FedEx や DHL を利用してドライブを発送する必要がありますか?
FedEx、DHL、UPS などの既知の運送業者や、米国郵政公社を利用して Azure データ センターにドライブを発送できます。 ただし、データ センターからユーザーへのドライブの返送の場合は、以下を指定する必要があります。

- 米国およびヨーロッパにおける FedEx アカウント番号
- または、アジアおよびオーストラリア地域での DHL アカウント番号

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>ドライブを海外発送する場合、何か制限はありますか?
発送する物理メディアが国境を越える場合があることに注意してください。 お客様は、物理メディアおよびデータが輸入および輸出に関して該当する法律に準拠していることを確認する必要があります。 物理メディアを発送する前に、アドバイザーに依頼して、メディアおよびデータが指定のデータ センターに合法的に発送できることを確認してもらってください。 それにより、マイクロソフトへのメディアの到着が遅れるのを防ぐことができます。

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>ジョブの作成時に、ストレージ アカウントの場所とは異なる場所が送付先住所として提供されました。 どうすればよいですか。

一部のストレージ アカウントの場所は、別の送付先にマップされています。 以前に利用できた送付先が、一時的に別の場所にマップされている場合もあります。 ドライブを発送する前に、ジョブの作成時に提供された送付先住所を必ず確認してください。

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>ドライブを発送するときに、運送業者からデータ センターの連絡先の住所と電話番号を求められました。 何を提供すればよいですか?

電話番号と DC の住所は、ジョブの作成の一環として提供されます。


## <a name="miscellaneous"></a>その他

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>サポートされる要件に適合しない HDD を誤って発送した場合は、どうなりますか?

Azure データ センターでは、サポートされる要件に適合しないドライブは返送されます。 サポートされる要件を満たすのが、パッケージ内の一部のドライブだけである場合は、それらのドライブが処理され、要件を満たさないドライブは返送されます。

### <a name="does-the-service-format-the-drives-before-returning-them"></a>ドライブは返送前にフォーマットされるのですか?

いいえ。 ドライブはすべて BitLocker で暗号化されます。

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>このサービスでインポートされたデータには、どのようにしてアクセスできますか?

Azure ストレージ アカウントのデータにアクセスするには、Azure portal または[ストレージ エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)を使います。  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>インポートの完了後、ストレージ アカウント内でデータはどのような状態になりますか? ディレクトリ階層は保持されますか?

インポート ジョブのハード ドライブを準備するときに、データセット CSV の DstBlobPathOrPrefix フィールドでコピー先が指定されます。 これは、ハード ドライブのデータのコピー先となるストレージ アカウント内のコピー先コンテナーです。 このコピー先コンテナー内に、ハード ドライブのフォルダーの仮想ディレクトリが作成され、ファイルの BLOB が作成されます。 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>ストレージ アカウント内に既に存在するファイルがドライブに含まれている場合、既存の BLOB またはファイルは上書きされますか?

場合によります。 ドライブを準備するときに、データセット CSV の /Disposition:<rename|no-overwrite|overwrite> というフィールドを使用して、コピー先のファイルを上書きするか無視するかを指定できます。 既定では、既存の BLOB またはファイルは上書きされず、新しいファイルの名前が変更されます。

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>WAImportExport ツールは、32 ビット オペレーティング システムと互換性がありますか?
いいえ。 WAImportExport ツールは、64 ビット Windows オペレーティング システムとのみ互換性があります。 サポートされる OS の全一覧については、[サポートされるオペレーティング システム]()に関するページをご覧ください。 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Azure Import/Export でサポートされるブロック BLOB とページ BLOB の最大サイズはいくつですか?

ブロック BLOB の最大サイズは、約 4.768 TB または 5,000,000 MB です。
ページ BLOB の最大サイズは 1 TB です。


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Azure Import/Export は AES-256 暗号化をサポートしていますか?
Azure Import/Export サービスは、AES-128 BitLocker 暗号化を既定で使用します。 データをコピーする前に BitLocker を使用して手動で暗号化することにより、これを AES-256 に変更できます。 

- [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) を使用している場合のサンプル コマンドは次のとおりです
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) を使用している場合は、"AlreadyEncrypted" を指定し、driveset CSV 内にキーを指定します。
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>次の手順

* [Azure Import/Export とは](storage-import-export-service.md)


