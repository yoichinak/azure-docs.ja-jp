---
title: SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ | Microsoft Docs
description: SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6b0ea7479910f7026974e37f8c05099453c0b26
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "42143617"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

このガイドは Microsoft Azure への SAP ソフトウェアの実装およびデプロイに関するドキュメントの一部です。 このガイドを読む前に、「[計画および実装ガイド][planning-guide]」をお読みください。 このドキュメントでは、Azure のサービスとしてのインフラストラクチャ (IaaS) 機能を使用して Microsoft Azure Virtual Machines (VM) 上の SAP とさまざまなリレーショナル データベース管理システム (RDBMS) および関連製品を組み合わせてデプロイする方法について説明します。

特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Note を補足する内容となっています。

## <a name="general-considerations"></a>一般的な考慮事項
この章では、Azure VM で SAP 関連 DBMS システムを実行する場合の考慮事項について説明します。 この章では、特定の DBMS システムについてはほとんど言及していません。 特定の DBMS システムについては、このホワイト ペーパーの後の章で説明します。

### <a name="definitions-upfront"></a>用語の定義
このドキュメントでは、次の用語を使用します。

* IaaS: サービスとしてのインフラストラクチャ。
* PaaS: サービスとしてのプラットフォーム。
* SaaS: サービスとしてのソフトウェア。
* SAP コンポーネント: 個別の SAP アプリケーション (ECC、BW、Solution Manager、EP など)。  SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
* SAP 環境: 開発、QAS、トレーニング、DR、実稼働などのビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
* SAP ランドスケープ: これは、お客様の IT 環境内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
* SAP システム: DBMS 層とアプリケーション レイヤーを組み合わせたシステム (SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなど)。Azure デプロイメントでは、オンプレミスと Azure 間でこれら 2 つの層を分割することはできません。 つまり、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 ただし、SAP ランドスケープの異なるシステムを Azure またはオンプレミスにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。
* クラウドのみのデプロイ: Azure サブスクリプションが、サイト間接続または ExpressRoute 接続経由でオンプレミス ネットワーク インフラストラクチャに接続されていないデプロイです。 この種のデプロイメントは、共通の Azure ドキュメントでも "クラウドのみ" のデプロイメントとして説明されています。 この方法でデプロイされた仮想マシンは、インターネットと、Azure 内の VM に割り当てられたパブリック インターネット エンドポイントを経由してアクセスされます。 オンプレミスの Active Directory (AD) と DNS は、これらのタイプのデプロイメントでは Azure に拡張されません。 そのため、VM はオンプレミスの Active Directory の一部にはなりません。 注: このドキュメントで言う「クラウドのみのデプロイメント」とは、Active Directory の拡張機能や、オンプレミスからパブリック クラウドへの名前解決を使用せず、Azure 内だけで実行されている完全な SAP ランドスケープのことを指します。 クラウドのみの構成は、Azure 上でホストされている SAP システムとオンプレミスにあるリソースとの間に、SAP STMS またはその他のオンプレミスのリソースを使用する必要がある、運用 SAP のシステムや構成用にはサポートされていません。
* クロスプレミス: オンプレミスのデータ センターと Azure との間に、サイト間、マルチサイトまたは ExpressRoute 接続を使用した Azure サブスクリプションに、VM をデプロイするシナリオのことを指します。 この種のデプロイメントは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、サブスクリプションの Azure 資産に拡張されます。 この拡張により。VM はオンプレミス ドメインの一部になることができます。 オンプレミス ドメインのドメイン ユーザーはこのサーバーにアクセスできます。また、その VM のサービス (DBMS サービスなど) を実行できます。 オンプレミスにデプロイした VM 間、および Azure にデプロイした VM 間の通信および名前解決は可能です。 これが Azure 上に SAP 資産をデプロイする場合の最も一般的なシナリオだと思われます。 詳しくは、[こちら][vpn-gateway-cross-premises-options]と[こちら][vpn-gateway-site-to-site-create]の記事をご覧ください。

> [!NOTE]
> SAP システムを実行する Azure Virtual Machines がオンプレミス ドメインのメンバーである SAP システムのクロスプレミス デプロイは、運用 SAP システムの場合にサポートされます。 クロスプレミス構成では、Azure に SAP ランドスケープの一部または全部をデプロイする場合にサポートされます。 Azure で完全な SAP ランドスケープを実行する場合でも、これらの VM をオンプレミス ドメインと ADS の一部に含める必要があります。 ハイブリッド IT シナリオについて記載した以前のバージョンのドキュメントでは、*ハイブリッド*という用語はオンプレミスと Azure 間のクロスプレミス接続が存在するという事実に基づいていると説明しました。 このケースでは、*ハイブリッド*とは Azure の VM がオンプレミス Active Directory の一部であることも意味します。
> 
> 

一部の Microsoft ドキュメントでは、クロスプレミス シナリオ、特に DBMS HA 構成の場合に少し異なる記述をしています。 SAP 関連ドキュメントでは、クロスプレミス シナリオはサイト間接続またはプライベート (ExpressRoute) 接続を使用することを表し、SAP ランドスケープがオンプレミスと Azure 間で分散されるという事実を表します。

### <a name="resources"></a>リソース
Azure での SAP デプロイについて、次のガイドが提供されています。

* [SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ][deployment-guide]に関する記事
* [SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ (本ドキュメント)][dbms-guide]

次の SAP Notes は、SAP on Azure に関連します。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure: Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] |SAP on Microsoft Azure: Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1409604] |Virtualization on Windows: Enhanced Monitoring (Windows での仮想化: 拡張された監視機能) |
| [2191498] |SAP on Linux with Azure: Enhanced Monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能) |
| [2039619] |SAP Applications on Microsoft Azure using the Oracle Database: Supported Products and Versions (Oracle データベースを使用した Microsoft Azure 上の SAP アプリケーション: サポートされている製品とバージョン) |
| [2233094] |DB6: SAP Applications on Azure Using IBM DB2 for Linux, UNIX, and Windows - Additional Information (DB6: Linux、UNIX、および Windows 向けの IBM DB2 を使用した Azure 上の SAP アプリケーション - 追加情報) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] |SUSE LINUX Enterprise Server 12: Installation notes (SUSE Linux Enterprise Server 12: インストールに関する注意事項) |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation and Upgrade (Red Hat Enterprise Linux 7.x: インストールおよびアップグレード) |
| [2069760] |Oracle Linux 7.x SAP のインストールとアップグレード |
| [1597355] |Linux のスワップ領域に関する推奨事項 |
| [2171857] |Oracle Database 12c - Linux でサポートされているファイル システム |
| [1114181] |Oracle Database 11g - Linux でサポートされているファイル システム |


Linux に関するすべての SAP ノートが記載された、[SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) もご覧ください。

Microsoft Azure のアーキテクチャと Microsoft Azure Virtual Machines のデプロイおよび操作方法に関する実用的な知識が必要です。 詳細については、<https://azure.microsoft.com/documentation/> を参照してください。

> [!NOTE]
> Microsoft Azure Platform で提供する、サービスとしての Microsoft Azure Platform (PaaS) については説明 **しません** 。 このホワイト ペーパーでは、オンプレミス環境でデータベース管理システム (DBMS) を実行する場合と同様に、Microsoft Azure Virtual Machines (IaaS) で DBMS を実行する方法について説明します。 これらの 2 つの製品のデータベース機能は非常に異なっているため、混同しないでください。 関連項目: <https://azure.microsoft.com/services/sql-database/>
> 
> 

現在説明している IaaS については、一般的に、Windows、Linux、および DBMS のインストールと構成はオンプレミスに設置する仮想マシンまたはベア メタル マシンと本質的には同じです。 ただし、IaaS を利用する場合、アーキテクチャおよびシステム管理の実装の決定については異なる部分があります。 このドキュメントの目的は、IaaS を使用する場合に知っておくべき特定のアーキテクチャおよびシステム管理の相違について説明することです。

一般に、ここで取り上げる相違点の大まかな範囲は次のとおりです。

* SAP システムの適切な VM/ディスク レイアウトを計画して、適切なデータ ファイル レイアウトがあり、ワークロードに対して十分な IOPS を実現できることを確認する。
* IaaS を使用する場合のネットワークの考慮事項。
* データベースのレイアウトを最適化するために特定のデータベース機能を使用する。
* IaaS のバックアップと復元の考慮事項。
* さまざまな種類の展開用のイメージを使用する。
* Azure IaaS での高可用性。

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS デプロイの構造
以降の章に進むにあたって、「[デプロイ ガイド][deployment-guide]」の[この章][deployment-guide-3]で説明した内容を理解しておく必要があります。 この章を読む前に、別の VM シリーズとその相違点、および Azure Standard と Premium Storage の違いについての知識を理解しておく必要があります。

2015 年 3 月まで、オペレーティング システムを含むディスクのサイズは 127 GB に制限されていました。 この制限は 2015 年 3 月に撤廃されました (詳細については、<https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/> を参照してください)。 その時点から、オペレーティング システムを含むディスクをその他のディスクと同じサイズにできるようになりました。 ただし現在でも、オペレーティング システム、DBMS、および最終的に導入する SAP バイナリとデータベース ファイルとを分けたデプロイ構造にすることをお勧めしています。 そのため、Microsoft は、Azure Virtual Machines で実行する SAP システムに、オペレーティング システムとともにインストールされるベース VM (またはディスク)、データベース管理システムの実行可能ファイル、および SAP の実行可能ファイルが含まれていると想定しています。 DBMS のデータとログ ファイルは別のディスク ファイルの Azure Storage (Standard または Premium Storage) に格納され、元の Azure オペレーティング システム イメージ VM に論理ディスクとして接続されます。 

Azure Standard Storage または Premium Storage の使用状況 (DS シリーズまたは GS シリーズの VM を使用するなど) によっては、Azure にはその他のクォータがあります。詳しくは、[こちら (Linux)][virtual-machines-sizes-linux] および[こちら (Windows)][virtual-machines-sizes-windows] の記事をご覧ｋづあさい。 ディスクのレイアウトを計画するときは、次の項目のクォータの最適なバランスを見つける必要があります。

* データ ファイルの数。
* ファイルが含まれているディスクの数。
* 1 つのディスクの IOPS クォータ。
* ディスクごとのデータ スループット。
* VM サイズに対して追加できるデータ ディスクの数。
* VM が提供できるストレージの全体的なスループット。

Azure はデータ ディスクごとに IOPS クォータを適用します。 Azure Standard Storage と Premium Storage でホストされているディスクの場合、これらのクォータは異なります。 また、この 2 つのストレージ タイプの I/O 待機時間は大きく異なり、Premium Storage のほうが I/O 待機時間が短くなります。 各 VM タイプでは接続できるデータ ディスクの数に限りがあります。 別の制限として、Azure Premium Storage を利用できるのは特定の VM タイプのみです。 つまり、特定の VM タイプを決定する場合、CPU とメモリの要件だけではなく、一般的にディスクの数または Premium Storage ディスクの種類によって拡大縮小される IOPS、待機時間、ディスクのスループット要件によっても左右される場合があります。 特に Premium Storage では各ディスクで実現する必要がある IOPS とスループットの値によってディスクのサイズも決まる場合があります。

全体の IOPS レート、マウントされるディスクの数、VM のサイズはすべて相互に関連があるため、SAP システムの Azure の構成はオンプレミス デプロイとは異なる場合があります。 LUN ごとの IOPS 制限は、通常、オンプレミス デプロイでは設定可能です。 Azure Storage では、これらの制限は固定であるか、または Premium Storage のディスク タイプに依存します。 そのため、オンプレミス デプロイの場合、お客様のデータベース サーバーの構成では、SAP および DBMS などの特殊な実行可能ファイル用に多数の異なるボリュームを使用したり、一時的なデータベースまたはテーブル スペース用に特別なボリュームを使用したりしています。 このようなオンプレミス システムを Azure に移行すると、実行可能ファイルまたはデータベース用のディスクが無駄になり、潜在的な IOPS 帯域幅が無駄になる可能性があります。これにより、IOPS がまったく実行されないか、あまり実行されなくなります。 そのため、Azure VM では、可能であれば DBMS および SAP の実行可能ファイルを OS ディスクにインストールすることをお勧めしています。

データベース ファイルとログ ファイルの配置および使用する Azure Storage タイプは、IOPS、待機時間、およびスループットの要件によって定義する必要があります。 トランザクション ログ向けに十分な IOPS を確保するためには、トランザクション ログ ファイル用の複数のディスクを使用するか、大容量の Premium Storage ディスクを使用する必要がある場合があります。 このような場合、ユーザーによってはトランザクション ログを含むディスクでソフトウェア RAID (Windows の場合は Windows 記憶域プール、Linux の場合は MDADM および LVM (Logical Volume Manager) など) を構築します。

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azure VM の D:\ ドライブは、Azure の計算ノード上のいくつかのローカル ディスクによってサポートされる非永続ドライブです。 非永続であるということは、VM が再起動されると、D:\ ドライブ上のコンテンツに加えられた変更が失われることを意味します。 「変更」とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどのことです。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM は、Azure の計算ノード上のローカル ディスクによってサポートされる非永続ドライブである /mnt/resource ドライブを自動的にマウントします。 非永続であるということは、VM が再起動されると、/mnt/resource ドライブ上のコンテンツに加えられた変更が失われることを意味します。 変更とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどのことです。
> 
> 

- - -
Azure VM シリーズに依存しますが、計算ノード上のローカル ディスクはさまざまなパフォーマンスを示します。パフォーマンスは次のように分類できます。

* A0 ～ A7: パフォーマンスが非常に制限されている。 Windows ページ ファイルを超えるものは使用できない
* A8 ～ A11: 数万 IOPS と 1 GB/秒を超えるスループットによる非常に良好なパフォーマンス特性
* D シリーズ: 数万 IOPS と 1 GB/秒を超えるスループットによる非常に良好なパフォーマンス特性
* DS シリーズ: 数万 IOPS と 1 GB/秒を超えるスループットによる非常に良好なパフォーマンス特性
* G シリーズ: 数万 IOPS と 1 GB/秒を超えるスループットによる非常に良好なパフォーマンス特性
* GS シリーズ: 数万 IOPS と 1 GB/秒を超えるスループットによる非常に良好なパフォーマンス特性

上記の内容は、SAP が認定する VM の種類に適用されます。 IOPS とスループット品質に優れた VM シリーズは、tempdb または一時テーブル領域などの一部の DBMS 機能向けです。

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM とデータ ディスクのキャッシュ
ポータルを通じてこれらのデータ ディスクを作成する場合、またはアップロードしたディスクを VM にマウントする場合、Azure Storage に配置されたこれらのディスクと VM 間の I/O トラフィックをキャッシュするかどうかを選択できます。 Azure Standard および Premium Storage は、この種類のキャッシュに 2 つのテクノロジを使用します。 どちらの場合でも、キャッシュ自体は VM の一時ディスク (Windows は D:\、Linux は /mnt/resource) によって使用される同じドライブでサポートされるディスクになります。

Azure Standard Storage で可能なキャッシュの種類は次のとおりです。

* キャッシュなし
* 読み取りキャッシュ
* 読み取りキャッシュと書き込みキャッシュ

一定の決まったパフォーマンスを得られるように、Azure Standard Storage のキャッシュを **DBMS 関連データ ファイル、ログ ファイル、テーブル スペースを含むすべてのディスクに対して "NONE" に設定する**必要があります。 VM のキャッシュは既定のままにしておくことができます。

Azure Premium Storage については、次のキャッシュ オプションが存在します。

* キャッシュなし
* 読み取りキャッシュ

Azure Premium Storage の推奨事項は、SAP データベースの**データ ファイルには [読み取りキャッシュ]** を利用し、**ログ ファイルのディスには [キャッシュなし]** を選択することです。

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>ソフトウェア RAID
すでに説明したように、構成可能なディスクの数にわたってデータベース ファイルに必要な IOPS 数と、Azure VM が提供するディスクまたは Premium Storage ディスク タイプあたりの最大 IOPS のバランスを取る必要があります。 ディスクにかかる IOPS 負荷に対処する最も簡単な方法は、異なるディスク間でソフトウェア RAID を作成することです。 ソフトウェア RAID から分割した LUN に SAP DBMS のデータ ファイルの数を設定します。 3 つの Premium Storage ディスクのうちの 2 つは、Standard Storage に基づくディスクよりも高い IOPS クォータを提供するため、要件によっては Premium Storage の使用を検討したほうがよい場合があります。 それだけでなく、Azure Premium Storage では I/O 待機時間が大幅に短縮されます。 

同じことがさまざまな DBMS システムのトランザクション ログにも当てはまります。 ファイルが多数ある場合、DBMS システムは一度に 1 つのファイルにしか書き込まないため、Tlog ファイルを追加しても役に立ちません。 単一の Standard Storage ベースのディスクが提供するよりも高い IOPS レートが必要な場合は、複数の Standard Storage のディスクにわたってストライピングすることができます。また、IOPS レートがより高い大容量の Premium Storage ディスク タイプを使用しても、トランザクション ログへの書き込み I/O の待機時間が短くなります。

Azure デプロイメントでソフトウェア RAID の使用をお勧めする状況は次のとおりです。

* トランザクション ログ/Redo ログで Azure が単一のディスクに提供する IOPS よりも高い IOPS が必要な場合。 前述のように、この場合はソフトウェア RAID を使用して複数のディスクにまたがる LUN を作成することにより解決できます。
* SAP データベースのさまざまなデータ ファイルにわたる I/O ワークロードの配分が不均一である場合。 このような場合、1 つのデータ ファイルが頻繁にクォータに達するようになるでしょう。 一方で、別のデータ ファイルは単一ディスクの IOPS クォータにまったく達しない場合があります。 このような場合は、ソフトウェア RAID を使用して複数のディスクにまたがる 1 つの LUN を構築することをお勧めします。 
* データ ファイルごと正確な I/O ワークロードが不明で、DBMS に対する全体的な IOPS ワークロードのみ大まかにわかっている場合。 最も簡単なのは、ソフトウェア RAID を利用して 1 つの LUN を作成することです。 これにより、この LUN の背後にある複数のディスクのクォータの合計が、既知の IOPS レートを満たすようになります。

- - -
> ![Windows][Logo_Windows] Windows
> 
> Windows Server 2012 以上で実行する場合は、Windows 記憶域スペースを使用することをお勧めします。 これは以前のバージョンの Windows の Windows ストライピングよりも効率的です。 オペレーティング システムとして Windows Server 2012 を使用するときは、PowerShell コマンドによって、Windows 記憶域プールと記憶域スペースを作成する必要がある場合があります。 PowerShell コマンドについては、<https://technet.microsoft.com/library/jj851254.aspx> を参照してください。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux でのソフトウェア RAID の構築がサポートされているのは、MDADM および LVM (論理ボリューム マネージャー) のみです。 詳細については、次の記事を参照してください。
> 
> * [Linux でのソフトウェア RAID の構成][virtual-machines-linux-configure-raid] (MDADM の場合)
> * [Azure で Linux VM の LVM を構成する][virtual-machines-linux-configure-lvm]
> 
> 

- - -
一般に Azure Premium Storage と連携させることができる VM シリーズを利用するための考慮事項は次のとおりです。

* SAN/NAS デバイスで提供する I/O 待機時間に近い値が必要な場合。
* Azure Standard Storage が提供できる I/O 待機時間よりも短い値が必要な場合。
* 特定の VM タイプに対して複数の Standard Storage ディスクで達成することができる VM あたりの IOPS よりも高い IOPS。

基盤となる Azure Storage は各ディスクを 3 つ以上のストレージ ノードにレプリケートするため、単純な RAID 0 ストライピングを使用できます。 RAID 5 や RAID 1 を実装する必要はありません。

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure Storage は、ベース VM (OS を含む) とディスクまたは BLOB を 3 つ以上の個別のストレージ ノードに格納します。 ストレージ アカウントまたはマネージド ディスクを作成する場合は、次のような保護の選択肢があります。

![Azure ストレージ アカウントに対して有効化された Geo レプリケーション][dbms-guide-figure-100]

Azure Storage のローカル レプリケーション (ローカル冗長) は、インフラストラクチャの障害によるデータ損失に対する保護のレベルを提供します。これは、ほとんどのお客様が導入する余裕のないものでしょう。 前述のように、4 つの異なるオプションがあり、5 番目は最初の 3 つのいずれかのバリエーションです。 それらについて詳しく説明すると次のようになります。

* **Premium ローカル冗長ストレージ (LRS)**: Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Azure リージョンの同じ Azure データ センター内にデータのレプリカが 3 つあります。 コピーはさまざまなフォールト ドメインとアップグレード ドメイン内に置かれます (概念については、「[計画ガイド][planning-guide]」の[この章][planning-guide-3.2]をご覧ください)。 ストレージ ノードの障害またはディスクの障害によってデータのレプリカが停止した場合、新しいレプリカが自動的に生成されます。
* **ローカル冗長ストレージ (LRS)**: Azure リージョンの同じ Azure データ センター内にデータのレプリカが 3 つあります。 コピーはさまざまなフォールト ドメインとアップグレード ドメイン内に置かれます (概念については、「[計画ガイド][planning-guide]」の[この章][planning-guide-3.2]をご覧ください)。 ストレージ ノードの障害またはディスクの障害によってデータのレプリカが停止した場合、新しいレプリカが自動的に生成されます。 
* **Geo 冗長ストレージ (GRS)**: この場合、たいていは同じ地理的リージョン (北ヨーロッパ、西ヨーロッパなど) 内にある別の Azure リージョンにある追加の 3 つのデータ レプリカを提供する非同期のレプリケーションがあります。 これにより 3 つのレプリカが追加されるため、合計で 6 つのレプリカがあります。 このバリエーションの 1 つは、Geo レプリケーションされた Azure リージョンのデータを読み取り目的で使用できる (読み取りアクセス Geo 冗長) という追加機能です。
* **ゾーン冗長ストレージ (ZRS)**: この場合、同じ Azure リージョンに 3 つのデータ レプリカが残ります。 「[計画ガイド][planning-guide]」の[この章][planning-guide-3.1]で説明したように、Azure リージョンは近接する場所にある複数のデータ センターになる場合があります。 LRS の場合、レプリカが、1 つの Azure リージョンを構成するさまざまなデータ センターに分散されます。

詳しくは、[こちら][storage-redundancy]をご覧ください。

> [!NOTE]
> DBMS デプロイメントの場合、Geo 冗長ストレージを使用することは推奨されていません
> 
> Azure Storage の Geo レプリケーションは非同期です。 ロック ステップでは、1 つの VM にマウントされている個々のディスクのレプリケーションは同期されません。 したがって、これは異なるディスクに分散されている、または複数のディスクをベースとしたソフトウェア RAID に導入されている DBMS ファイルをレプリケートする場合には適していません。 DBMS ソフトウェアでは、永続的なディスク ストレージがさまざまな LUN と基盤になるディスク/スピンドルにわたって正確に同期されている必要があります。 DBMS ソフトウェアは IO 書き込み活動のシーケンスにさまざまなメカニズムを使用します。DBMS は書き込みが数ミリ秒でもずれると、そのレプリケーションの対象となるディスク ストレージが破損していることを報告します。 したがって、geo レプリケートされた複数のディスクにまたがるデータベースを使用したデータベース構成にする場合、このようなレプリケーションはデータベースの提供する手段や機能を使用して実行する必要があります。 Azure Storage の Geo レプリケーションを利用してこのジョブを実行しないようにしてください。 
> 
> この問題は、システム例でシンプルに説明できます。 DBMS のデータ ファイルを含む 8 台のディスクと、トランザクション ログ ファイルを含む 1 台のディスクが搭載された SAP システムを Azure にアップロードしたと仮定します。 9 台のディスクにはそれぞれ、DBMS に従って一定の方法で書き込まれたデータがあります。このデータはデータ ファイルまたはトランザクション ログ ファイルのいずれかに書き込まれます。
> 
> データを適切に Geo レプリケーションして、一貫性のあるデータベースのイメージを維持するために、9 台すべてのディスクのコンテンツを、9 台の異なるディスクに対して実行された I/O 操作の正確な順序で Geo レプリケーションする必要があります。 ただし、Azure Storage の Geo レプリケーションでは、ディスク間の依存関係を宣言することができません。 つまり、Microsoft Azure Storage の Geo レプリケーションでは、これらの 9 台の異なるディスクのコンテンツが相互に関連しており、9 台すべてのディスク間で発生した I/O 操作の順序でレプリケートする場合にのみデータの変更が一貫性のある状態になるという事実を認識できないことを意味します。
> 
> このシナリオの Geo レプリケーションされたイメージは、一貫性のあるデータベース イメージを提供しない可能性が高いだけでなく、Geo 冗長ストレージでパフォーマンスの低下が発生してパフォーマンスに深刻な影響を与える可能性があります。 要約すると、DBMS タイプのワークロードではこの種類のストレージ冗長オプションを使用しないでください。
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Azure Virtual Machines サービスのストレージ アカウントへの VHD のマッピング
この章は Azure Storage のアカウントにのみ適用されます。 Managed Disks を使用する場合、この章で述べられている制限は適用されません。 Managed Disks について詳しくは、このガイドの「[Managed Disks][dbms-guide-managed-disks]」の章をご覧ください。

Azure ストレージ アカウントは、管理の構成要素であるだけでなく、制限事項の対象です。 ただし、制限事項は、対象となるのが Azure Standard Storage アカウントであるか Azure Premium Storage アカウントであるかどうかによって異なります。 正確な機能と制限事項は[こちら][storage-scalability-targets]に記載されています。

Azure Standard Storage は、ストレージ アカウントあたりの IOPS の制限がありますのでご注意ください ([この記事][storage-scalability-targets]の「**合計要求レート**」と記載された行)。 また、最初は Azure サブスクリプションあたりのストレージ アカウントが 100 に制限されています (2015 年 7 月現在)。 そのため、Azure Standard Storage を使用する場合は複数のストレージ アカウント間で VM の IOPS のバランスを取ることをお勧めします。 可能であれば、1 つの VM で 1 つのストレージ アカウントを使用するのが理想的です。 したがって、Azure Standard Storage でホストされている各 VHD がそのクォータ制限に達する可能性がある DBMS デプロイメントについては、Azure Standard Storage を使用する Azure ストレージ アカウントあたり 30 ～ 40 の VHD のみデプロイする必要があります。 一方、Azure Premium Storage を利用し、大容量のデータベース ボリュームを格納すると、IOPS の観点から見て好ましいでしょう。 ただし、Azure Premium Storage アカウントは、Azure Standard Storage アカウントよりもデータ ボリュームを制限を厳しくすることが可能な方法です。 その結果、データ ボリュームの制限に達しない範囲で、Azure Premium Storage アカウント内の VHD の上限数までしかデプロイできません。 最後に、IOPS および容量の機能が制限されている "仮想 SAN" としての Azure Storage アカウントを考えてみましょう。 その結果、オンプレミス デプロイと同様に、異なる "仮想 SAN デバイス" または Azure Storage アカウントにまたがるさまざまな SAP システムの VHD のレイアウトを定義するというタスクは残ります。

Azure Standard Storage の場合、可能であれば、異なるストレージ アカウントからストレージを 1 つの VM に提示しないでください。

DS シリーズまたは GS シリーズの Azure VM を使用すると、Azure Standard Storage アカウントと Premium Storage アカウントから VHD をマウントできます。 このような異種混在のストレージを使用できるとすると、VHD でサポートする Standard Storage にバックアップを書き込み、DBMS のデータ ファイルとログ ファイルを Premium Storage に持つというユースケースが思い浮かびます。 

顧客のデプロイメントとテストに基づき、単一の Azure Standard Storage アカウントではデータベース データ ファイルとログ ファイルを含む約 30 ～ 40 の VHD を許容可能なパフォーマンスでプロビジョニングすることができます。 前述のように、Azure Premium Storage アカウントの制限は、IOPS ではなく、保持できるデータの容量に適用される可能性があります。

オンプレミスの SAN デバイスと同様に、共有では、最終的に Azure ストレージ アカウント上のボトルネックを検出するために何らかの監視が必要です。 Azure Monitoring Extension for SAP と Azure Portal を使用すると、ビジー状態の Azure Storage アカウント検出することができるため、最適な IO パフォーマンスを提供できます。  このような状況が検出された場合、ビジー状態の VM を別の Azure Storage アカウントに移動することが推奨されます。 SAP ホストの監視機能をアクティブ化する方法について詳しくは、「[デプロイ ガイド][deployment-guide]」をご覧ください。

Azure Standard Storage と Azure Standard Storage アカウントに関するベスト プラクティスの概要を説明する別の記事については、<https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx> を参照してください。

#### 
  <a name="f42c6cb5-d563-484d-9667-b07ae51bce29">
  </a>Managed Disks
Managed Disks は Azure Resource Manager の新しいリソースの種類で、Azure Storage アカウントに格納されている VHD の代わりに使用できます。 Managed Disks は接続されている仮想マシンの可用性セットに自動的に配置され、仮想マシンと仮想マシンで実行されているサービスの可用性を向上させます。 詳しくは、[概要の記事](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)をご覧ください。

SAP は現在、Premium Managed Disks のみをサポートします。 詳しくは、SAP Note [1928533] をご覧ください。

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>デプロイされた DBMS VM の Azure Standard Storage から Azure Premium Storage への移動
デプロイされた VM を Azure Standard Storage から Azure Premium Storage に移動したいというお客様に遭遇することが多々あります。 ディスクが Azure Storage アカウントに格納されている場合、これはデータを物理的に移動せずには行うことはできません。 これを行うには、いくつかの方法があります。

* すべての VHD、ベース VHD、データ VHD を新しい Azure Premium Storage アカウントにコピーできます。 Azure Standard Storage の VHD の数を選択する理由の多くは、データ ボリュームが必要であるからではありません。 それほど多くの VHD が必要だった理由は IOPS です。 Azure Premium Storage に移行すれば、少ない VHD で同じ IOPS スループットを達成できます。 Azure Standard Storage では、公称ディスク サイズではなく使用されるデータに対して課金されるという事実を考慮すると、VHD の数はコストに影響しません。 ただし、Azure Premium Storage では、公称ディスク サイズに対して課金できるようになります。 そのため、ほとんどのお客様は Premium Storage でも、必要な IOPS スループットを実現するために必要な Azure VHD の数を維持しようとします。 そのため、ほとんどのお客様は単純な 1 対 1 のコピーは用いないという判断をします。
* まだマウントされていない場合は、SAP データベースのデータベース バックアップを含めることができる 1 つの VHD をマウントします。 バックアップ後に、バックアップを格納した VHD を含むすべての VHD のマウントを解除し、Azure Premium Storage アカウントに、ベース VHD とその VHD をコピーします。 その後、ベース VHD に基づく VM をデプロイし、バックアップで VHD をマウントします。 次に、データベースを復元するために使用する VM 用に追加の空の Premium Storage ディスクを作成します。 これは、復元プロセスの一環として、DBMS によって、データおよびログ ファイルへのパスを変更する許可が与えられていることが前提です。
* 別の方法としては、バックアップ VHD を Azure Premium Storage にコピーし、それを新しくデプロイおよびインストールした VM に対して接続するという、先ほど説明したプロセスのバリエーションの 1 つがあります。
* データベースのデータ ファイルの数を変更する必要がある場合、4 番目の方法を選択できます。 このような場合は、エクスポート/インポートを使用して SAP の同種のシステムのコピーを実行します。 そのエクスポート ファイルを、Azure Premium Storage アカウントにコピーされた VHD に置き、それをインポート プロセスを実行するために使用する VM に接続します。 お客様は、主にデータ ファイルの数を削減したい場合に、このような方法を使用します。

Managed Disks を使用する場合、次のようにして Premium Storage に移行できます。

1. 仮想マシンの割り当て解除
1. 必要に応じて、仮想マシンのサイズを Premium Storage (DS、GS など) をサポートするサイズに変更します。
1. Managed Disks のアカウントの種類を Premium (SSD) に変更する
1. 「[VM とデータ ディスクのキャッシュ][dbms-guide-2.1]」の章で推奨されているように、データ ディスクのキャッシュを変更する
1. 仮想マシンを開始する

### <a name="deployment-of-vms-for-sap-in-azure"></a>Azure で SAP 用 VM をデプロイする
Microsoft Azure では、VM および関連付けられているディスクをデプロイする方法が複数用意されています。 したがって、VM の準備はデプロイの方法によって異なる場合があるため、その違いを理解しておくことが重要です。 一般に、私たちは、次の章で説明されているシナリオを検討します。

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Azure Marketplace から VM をデプロイする
マイクロソフトまたはサード パーティが提供するイメージを Azure Marketplace から取得して VM をデプロイします。 Azure で VM をデプロイした後は、オンプレミス環境の場合と同じガイドラインおよびツールに従って VM 内に SAP ソフトウェアをインストールします。 Azure VM 内に SAP ソフトウェアをインストールする場合、SAP とマイクロソフトは、SAP インストール メディアをディスクにアップロードして保存するか、すべての必要な SAP インストール メディアを格納した「ファイル サーバー」として機能する Azure VM を作成することをお勧めします。

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>顧客固有の一般化されたイメージを使用する VM のデプロイ
OS または DBMS バージョン固有のパッチ要件により、Azure Marketplace で提供されるイメージは、ニーズに適さない場合があります。 そのため、後で繰り返しデプロイできる、独自の「プライベート」OS/DBMS の VM イメージを使用して VM を作成する必要がある場合があります。 複製のためにこのような「プライベート」イメージを準備するには、オンプレミスの VM で OS を一般化する必要があります。 VM を一般化する方法について詳しくは、「[デプロイ ガイド][deployment-guide]」をご覧ください。

使用しているオンプレミス VM (特に 2 層システム) に SAP コンテンツを既にインストールしている場合、SAP Software Provisioning Manager でサポートされているインスタンス名の変更手順に従って、Azure VM のデプロイ後に SAP システムの設定を調整できます (SAP ノート [1619720])。 インストールしていない場合は、Azure VM のデプロイ後に SAP ソフトウェアをインストールできます。

SAP アプリケーションによって使用されるデータベース コンテンツ以降は、SAP インストールで新しくコンテンツを生成するか、DBMS データベース バックアップを含む VHD または Microsoft Azure Storage に直接バックアップする DBMS の機能を利用して、Azure にコンテンツをインポートできます。 この場合、DBMS のデータ ファイルとログ ファイルを含む VHD をオンプレミスで準備し、それをディスクとして Azure にインポートすることもできます。 ただし、オンプレミスから Azure にロードされる DBMS のデータの転送は、オンプレミスで準備する必要がある VHD ディスクで行われます。

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する
オンプレミスから Microsoft Azure に特定の SAP システムを移動することを計画します (リフト アンド シフト)。 これは、OS、SAP バイナリ、最終的な DBMS バイナリが格納されているディスクと、Azure への DBMS のデータおよびログ ファイルが格納されているディスクをアップロードすることで行うことができます。 2 番目のシナリオとは対照的に、ホスト名、SAP SID、および SAP ユーザー アカウントがオンプレミス環境で構成されているため、Azure VM でそれらを保持します。 そのため、イメージを一般化する必要はありません。 このケースはほとんどの場合、SAP ランドスケープの一部がオンプレミスで、および一部が Azure で実行されるクロスプレミスのシナリオに適用されます。

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Azure VM の高可用性と障害復旧
Azure は、SAP および DBMS のデプロイで使用するさまざまなコンポーネントに適用される高可用性 (HA) およびディザスター リカバリー (DR) 機能を提供しています。

### <a name="vms-deployed-on-azure-nodes"></a>Azure ノードにデプロイされた VM
Azure Platform では、デプロイされた VM のライブ マイグレーションなどの機能を提供していません。 つまり、VM をデプロイしたサーバー クラスターをメンテナンスする必要がある場合、VM を停止して再起動する必要があるということを意味します。 Azure でのメンテナンスは、サーバー クラスター内のアップグレード ドメインと呼ばれるものを使用します。 メンテナンスできるのは、一度に 1 つのアップグレード ドメインのみです。 このような再起動中は、VM がシャット ダウンされ、メンテナンスが行われ、VM が再起動する間はサービスが中断されます。 ただし、ほとんどの DBMS ベンダーは高可用性とディザスター リカバリーの機能を提供しており、プライマリ ノードが使用できない場合は、別のノードで DBMS サービスがすぐに再起動されます。 Azure プラットフォームには、アップグレード ドメイン間で VM、ストレージ、その他の Azure サービスを分散させる機能が用意されており、計画的なメンテナンスやインフラストラクチャの障害の影響が、VM やサービスの小さなサブセットにのみにとどまるようにします。  入念に計画することで、オンプレミス インフラストラクチャに匹敵するレベルの可用性を実現することができます。

Microsoft Azure 可用性セットは、VM とその他のサービスがクラスター内の別のフォールト ドメインおよびアップグレード ドメインに分散し、ある時点でシャットダウンされるノードが 1 つだけとなるように VM またはサービスを論理的にグループ化します (詳しくは[こちら (Linux)][virtual-machines-manage-availability-linux] または[こちら (Windows)][virtual-machines-manage-availability-windows] の記事を参照)。

次に示すように VM を展開する際の目的に応じて構成する必要があります。

![DBMS HA 構成の可用性セットの定義][dbms-guide-figure-200]

DBMS デプロイメントの高可用性構成 (使用する個別の DBMS HA 機能とは別) を作成する場合、DBMS VM は次のことを行う必要があります。

* VM を同じ Azure Virtual Network に追加します (<https://azure.microsoft.com/documentation/services/virtual-network/>)。
* HA 構成の VM も同じサブネットにある必要があります。 クラウドのみのデプロイでは、異なるサブネット間で名前解決できません。IP 解決のみが機能します。 クロスプレミスのデプロイのサイト間または ExpressRoute 接続を使用して、少なくとも 1 つのサブネットを持つネットワークがすでに確立されています。 名前解決は、オンプレミス AD ポリシーおよびネットワーク インフラストラクチャに従って行われます。 



#### <a name="ip-addresses"></a>IP アドレス
回復力のある方法で HA 構成の VM をセットアップすることを強くお勧めします。 静的 IP アドレスを使用しないかぎり、Azure では、HA 構成内の HA パートナーに対応する IP アドレスに依存することは信頼性がありません。 Azure には、次の 2 つの「シャットダウン」の概念があります。

* Azure Portal または Azure PowerShell コマンドレット Stop AzureRmVM からのシャットダウン。この場合、Virtual Machine はシャットダウンされ、割り当てが解除されます。 課金は使用中のストレージに対してのみ発生するため、お使いの Azure アカウントではこの VM に対して課金されなくなります。 ただし、ネットワーク インターフェイスのプライベート IP アドレスが静的でなかった場合は、IP アドレスがリリースされ、割り当てられた古い IP アドレスが VM の再起動後に再びそのネットワーク インターフェイスに割り当てられるという保証はありません。 Azure Portal または Stop AzureRmVM を呼び出すことによってシャットダウンを実行すると、割り当てが自動的に解除されます。 マシンの割り当てを解除しない場合は、Stop-AzureRmVM -StayProvisioned を使用します。 
* OS レベルから VM をシャット ダウンする場合、VM はシャット ダウンしますが割り当ては解除されません。 ただし、この場合はシャットダウンされているかどうかに関係なく、お使いの Azure アカウントでこの VM に対して引き続き課金されます。 このような場合は、停止した VM の IP アドレスの割り当てがそのまま残ります。 内部から VM をシャットダウンすると、割り当て解除は自動的には適用されません。

クロスプレミス シナリオであっても、既定ではシャットダウンおよび割り当て解除とは、DHCP 設定のオンプレミス ポリシーが異なる場合でも、VM から IP アドレスの割り当てを解除することを意味します。 

* 例外は、[こちら][virtual-networks-reserved-private-ip]で説明しているように、ネットワーク インターフェイスに静的 IP アドレスを割り当てる場合です。
* このような場合、ネットワーク インターフェイスが削除されない限り、IP アドレスは固定です。

> [!IMPORTANT]
> デプロイメント全体をシンプルで管理可能に保つための明確な推奨事項は、関連する異なる VM 間で名前解決が機能する方法で、Azure 内の DBMS HA または DR 構成と連携する VM をセットアップすることです。
> 
> 

## <a name="deployment-of-host-monitoring"></a>ホスト監視のデプロイ
Azure Virtual Machines での SAP アプリケーションを実稼働環境で使用する場合、SAP には Azure Virtual Machines を実行する物理ホストからホスト監視データを取得する機能が必要です。 SAPOSCOL および SAP Host Agent でこの機能を有効にする、特定の SAP Host Agent パッチ レベルが必要になります。 正確なパッチ レベルは SAP Note [1409604]に記載されています。

ホストのデータを SAPOSCOL および SAPHostAgent に配信するコンポーネントのデプロイと、それらのコンポーネントのライフサイクル管理について詳しくは、「[デプロイ ガイド][deployment-guide]」をご覧ください。

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Microsoft SQL Server の特記事項
### <a name="sql-server-iaas"></a>SQL Server IaaS
Microsoft Azure 以降では、Windows Server プラットフォームに組み込まれた既存の SQL Server アプリケーションを Azure Virtual Machines に簡単に移行できます。 Virtual Machine の SQL Server では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行できるため、これらのアプリケーションのデプロイ、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SQL Server では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。 

> [!IMPORTANT]
> Microsoft Azure SQL Database については取り上げません。Microsoft Azure SQL Database は、Microsoft Azure Platform が提供するサービスとしてのプラットフォームです。 このホワイト ペーパーでは、SQL Server 製品の実行について説明します。これは Azure Virtual Machines でのオンプレミス デプロイメントとして知られており、Azure のサービスとしてのインフラストラクチャ機能を使用します。 これらの 2 つの製品のデータベース機能は異なっているため、混同しないでください。 関連項目: <https://azure.microsoft.com/services/sql-database/>
> 
> 

先に進む前に、[こちら][virtual-machines-sql-server-infrastructure-services]のドキュメントを確認することをお勧めします。

以降のセクションでは、上記のリンクにあるドキュメントの一部の情報を集約して説明しています。 SAP に関する特記事項についても説明し、いくつかの概念についてはさらに詳しく説明します。 ただし、SQL Server に特化したドキュメントを読み取る前に、上記のドキュメントに目を通すことを強くお勧めします。

先に進む前に知っておくべき IaaS での SQL Server に固有の情報があります。

* **Virtual Machine SLA**: Azure で実行されている Virtual Machines の SLA (<https://azure.microsoft.com/support/legal/sla/> を参照してください)。  
* **SQL バージョンのサポート**: SAP のお客様に対しては、Microsoft Azure Virtual Machines で SQL Server 2008 R2 以降がサポートされています。 これより前のエディションはサポートされていません。 詳細については、この一般的な [サポートの説明](https://support.microsoft.com/kb/956893) を確認してください。 マイクロソフトは基本的には SQL Server 2008 をサポートしています。 ただし、SQL Server 2008 R2 で導入された SAP の重要な機能によって、SQL Server 2008 R2 が SAP の最小リリースとなっています。 SQL Server 2012 および 2014 は IaaS シナリオに対する統合 (Azure Storage への直接バックアップなど) によってさらに拡張されていることに注意してください。 そのため、このホワイト ペーパーは、Azure に対する SQL Server 2012 および 2014 の最新のパッチ レベルに限定して説明します。
* **SQL 機能のサポート**: SQL Server のほとんどの機能は、いくつかの例外があるものの、Microsoft Azure Virtual Machines でサポートされます。 **共有ディスクを使用した SQL Server フェールオーバー クラスタリングはサポートされていません**。  データベース ミラーリング、AlwaysOn 可用性グループ、レプリケーション、ログ配布、および Service Broker などの分散テクノロジは単一の Azure リージョン内でサポートされます。 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx> に記載されているように、SQL Server AlwaysOn はさまざまな Azure リージョン間でもサポートされます。  詳細については、 [サポートの説明](https://support.microsoft.com/kb/956893) を確認してください。 AlwaysOn 構成をデプロイする方法の例は[こちら][virtual-machines-workload-template-sql-alwayson]の記事に記載されています。 また、[こちら][virtual-machines-sql-server-infrastructure-services]に記載されているベスト プラクティスをご確認ください。 
* 
  **SQL パフォーマンス**: Microsoft Azure がホストする Virtual Machines は、その他のパブリック クラウド仮想化製品と比べて極めて良好に機能しますが、個々の結果は異なる場合があります。 [こちら][virtual-machines-sql-server-performance-best-practices]の記事をご確認ください。
* **Azure Marketplace からのイメージの使用**: 新しい Microsoft Azure VM をデプロイする最も早い方法は、Azure Marketplace からのイメージを使用することです。 Azure Marketplace には、SQL Server を含むイメージがあります。 SQL Server が既にインストールされているイメージは、SAP NetWeaver アプリケーション用にすぐに使用することができません。 その理由は、それらのイメージ内に既定の SQL Server 照合順序がインストールされており、SAP NetWeaver システムで必要な照合順序がインストールされていないためです。 このようなイメージを使用するには、「[Microsoft Azure Marketplace の SQL Server イメージの使用][dbms-guide-5.6]」の章に記載されている手順をご確認ください。 
* 詳細については、「 [料金の詳細」](https://azure.microsoft.com/pricing/) を参照してください。 「[SQL Server 2012 ライセンス ガイド](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf)」と「[SQL Server 2014 ライセンス ガイド](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf)」も、重要なリソースです。

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Azure VM で SAP 関連 SQL Server をインストールするための SQL Server 構成ガイドライン
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>SAP 関連 SQL Server のデプロイメントの VM/VHD 構造に関する推奨事項
一般的な説明に従って、SQL Server 実行可能ファイルには VM の OS ディスク (ドライブ C:\) のシステム ドライブに配置またはインストールされている必要があります。  通常、SAP NetWeaver ワークロードでは、ほとんどの SQL Server システム データベースを高レベルでは使用しません。 そのため、SQL Server のシステム データベース (master、msdb、および model) も C:\ ドライブに残すことができます。 例外は tempdb です。一部の SAP ERP とすべての BW ワークロードでは、大量のデータ ボリュームまたは I/O 操作ボリュームが必要になる場合があり、元の VM に収まりません。 このようなシステムでは、次の手順を実行する必要があります。

* プライマリ tempdb データ ファイルを SAP データベースのプライマリ データ ファイルと同じ論理ドライブに移動します。
* SAP ユーザー データベースのデータ ファイルを含むその他の論理ドライブごとに、別の tempdb データ ファイルを追加します。
* ユーザー データベースのログ ファイルが格納されている論理ドライブに tempdb ログ ファイルを追加します。
* コンピューティング ノードの**ローカル SSD を使用する VM タイプ専用**に、tempdb データとログ ファイルが D:\ ドライブに配置される場合があります。 ただし、複数の tempdb データ ファイルを使用することが推奨される場合があります。 D:\ ドライブのボリュームは VM タイプに応じて異なることに注意してください。

これらの構成によって、tempdb はシステム ドライブが提供するよりも多くの領域を使用ができます。 適切な tempdb のサイズを決定するために、オンプレミスで実行している既存のシステムの tempdb のサイズを確認します。 さらに、このような構成により tempdb に対する IOPS 値をシステム ドライブでは設定できない値に設定できます。 ここでも、オンプレミスで実行しているシステムは、tempdb に必要な IOPS 値が得られるように、tempdb に対する I/O 負荷を監視する目的で使用できます。

SQL Server と SAP データベースを実行し、D:\ ドライブに tempdb データと tempdb ログ ファイルを配置する VM 構成は次のようになります。

![Reference Configuration of Azure IaaS VM for SAP (SAP 用 Azure IaaS VM のリファレンス構成)][dbms-guide-figure-300]

D:\ ドライブは VM タイプによってサイズが異なることに注意してください。 Tempdb のサイズ要件によりますが、D:\ ドライブが小さすぎる場合、SAP データベースのデータ ファイルとログ ファイルと tempdb のデータ ファイルとログ ファイルを組み合わせる必要があるかもしれません。

#### <a name="formatting-the-disks"></a>ディスクのフォーマット
SQL Server の場合、SQL Server のデータ ファイルとログ ファイルを含むディスクの NTFS ブロック サイズは 64K にする必要があります。 D:\ ドライブをフォーマットする必要はありません。 このドライブはフォーマット済みのものです。

データベースの復元または作成によってデータ ファイルの初期化が実行され、ファイルの内容が消去されないことをことを確認するために、SQL Server サービスが実行されているユーザー コンテキストが特定の権限を持っていることを確認する必要があります。 通常、Windows 管理者グループのユーザーは、これらのアクセス権限を持っています。 Windows 管理者以外のユーザーのユーザー コンテキストで SQL Server サービスが実行されている場合は、**[ボリュームの保守タスクを実行]** ユーザー権限をそのユーザーに割り当てる必要があります。  Microsoft サポート技術情報の記事 (<https://support.microsoft.com/kb/2574695>) で詳細をご覧ください。

#### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、まだ実行していない場合、SAP と Microsoft では、既存のデータベースを Azure にアップロードする前に SQL Server のページ圧縮を適用することお勧めします。

Azure にアップロードする前にデータベースの圧縮を実行するための推奨事項は、次の 2 つの理由によるものです。

* アップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短くなります。
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースの圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP SQL Server データベースを圧縮する方法の詳細については、<https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx> を参照してください。

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014: Azure Blob Storage にデータベース ファイルを直接格納する
SQL Server 2014 では、Azure Blob ストアの周囲に VHD の "ラッパー" を用意しなくても、Azure Blob ストアに直接データベース ファイルを格納することができます。 特に、Standard Azure Storage またはそれより小さい VM タイプを使用すると、小さい VM タイプでマウントできるディスクの数の制限が適用されることで IOPS が制限されるという問題を解消できます。 これは、ユーザー データベースに対するもので、SQL Server のシステム データベースに対しては機能しません。 また、SQL Server のデータ ファイルとログ ファイルに対しても機能します。 VHD に "ラッピング" するのではなく、このような方法で SAP SQL Server データベースをデプロイする場合は次の点に留意してください。

* 使用するストレージ アカウントは、SQL Server が実行されている VM をデプロイするために使用したストレージ アカウントと同じ Azure リージョン内にある必要があります。
* 前述の別の Azure Storage アカウントに VHD を分散させることについての考慮事項は、このデプロイ方法の場合にも適用されます。 Azure ストレージ アカウントの制限に対する I/O 操作数を意味します。

[comment]: <> (MSSedusch TODO ですが、これは、ストレージ帯域幅ではなく、ネットワーク帯域幅を使用するのではありませんか。)

この種類のデプロイの詳細については、<https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure> を参照してください。

Azure Premium Storage に SQL Server データ ファイルを直接格納するには、<https://support.microsoft.com/kb/3063054> に記載されているように、SQL Server 2014 以上のパッチ リリースが必要です。 Azure Standard Storage 上に SQL Server データ ファイルを保存する機能は、SQL Server 2014 の製品版で機能します。 ただし、このまったく同じパッチには、Azure Blob Storage を直接使用できるようにする別の一連の修正プログラムが含まれており、SQL Server データ ファイルとバックアップの信頼性を高めることができます。 このため、一般的にはそれらのパッチを使用することをお勧めします。

### <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 のバッファー プール拡張機能
SQL Server 2014 には、バッファー プール拡張と呼ばれる新しい機能が導入されました。 この機能は、サーバーのローカル SSD または VM に基づく第 2 レベルのキャッシュを使用してイン メモリに保持する SQL Server のバッファー プールを拡張します。 これにより、"イン メモリ" にデータの比較的大きい作業セットを保持できます。 Azure VM のローカル SSD に格納されるバッファー プールの拡張機能へのアクセスによって、Azure Standard Storage にアクセスする場合よりも、さまざまな部分で高速化されます。  そのため、IOPS とスループットが優れた VM タイプのローカル D:\ ドライブを利用することが、Azure Storage に対する IOPS 負荷を軽減し、クエリの応答時間を大幅に改善するための妥当な手段となる可能性があります。 これは、Premium Storage を使用していない場合に特に当てはまります。 データ ファイルで推奨されているように、Premium Storage を使用し、計算ノードで Premium Azure 読み取りキャッシュを使用する場合、大きな差は期待できません。 理由は、両方のキャッシュ (SQL Server バッファー プール拡張機能と Premium Storage 読み取りキャッシュ) がコンピューティング ノードのローカル ディスクを使用しているためです。
この機能の詳細については、<https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> を参照してください。 

### <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server のバックアップ/復旧に関する考慮事項
Azure に SQL Server をデプロイする場合、バックアップ方法を確認する必要があります。 実稼働システムでない場合でも、SQL Server でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復旧の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 たとえば、バックアップを復元すると、同じシステムの 3 層システムのセットアップを 2 層 SAP 構成から転送できます。

SQL Server を Azure Storage にバックアップする方法は 3 つあります。

1. SQL Server 2012 CU4 以降では、ネイティブでデータベースを URL にバックアップします。 詳細については、ブログ「[New functionality in SQL Server 2014 - Part 5 - Backup/Restore Enhancements](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)」(SQL Server 2014 の新機能 – パート 5 – バックアップ/復元の機能強化) を参照してください。 「[SQL Server 2012 SP1 CU4 以降][dbms-guide-5.5.1]」の章をご覧ください。
2. SQL 2012 CU4 より前の SQL Server リリースでは、リダイレクト機能を使用して VHD にバックアップし、構成されている Azure Storage の場所に基本的に書き込みストリームを移行できます。 「[SQL Server 2012 SP1 CU3 以前のリリース][dbms-guide-5.5.2]」の章をご覧ください。
3. 最後は、VHD ディスク デバイス上で disk コマンドを実行して従来の SQL Server バックアップを実行する方法です。 これはオンプレミス デプロイメントのパターンとまったく同じであり、このドキュメントで詳細については説明しません。

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 以降
この機能では、Azure BLOB Storage に直接バックアップできます。 この方法を使用しない場合は別のディスクにバックアップする必要がありますが、ディスクと IOPS の容量を消費します。 この概念は基本的に次のようになります。

 ![Microsoft Azure Storage BLOB への SQL Server 2012 バックアップの使用][dbms-guide-figure-400]

このケースの利点は、SQL Server のバックアップを保存するためにディスクを使用する必要がないことです。 割り当てられるディスクが少なくて済むため、データ ファイルとログ ファイルにディスクの IOPS の帯域幅全体を使用できます。 <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations> の「**制限事項**」に記載されているように、バックアップの最大サイズは最大 1 TB に制限されていることに注意してください。 SQL Server バックアップの圧縮を使用していても、バックアップ サイズが 1 TB を超えると、このドキュメントの「[SQL Server 2012 SP1 CU3 以前のリリース][dbms-guide-5.5.2]」の章に記載されている機能を使用する必要があります。

Azure BLOB ストアに対してバックアップからデータベースを復元する場合について記載されている[関連ドキュメント](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure)では、バックアップが 25 GB を超える場合、Azure BLOB ストアから直接復元しないことを推奨しています。 この記事の推奨事項は、パフォーマンスに関する考慮事項に基づくものであり、機能上の制限事項によるものではありません。 そのため、ケースごとにさまざまな条件が適用される場合があります。

このタイプのバックアップをセットアップして活用する方法については、 [こちらの](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) チュートリアルで確認できます。

一連の手順の例については、 [こちら](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)を参照してください。

バックアップの自動化においては、バックアップごとに BLOB の名前が違うかどうかを確認することが最も重要です。 名前が同じ場合、上書きされて復元チェーンが破損します。

3 つの異なるバックアップ タイプを混同しないようにするために、バックアップに使用するストレージ アカウントの下に別のコンテナーを作成することをお勧めします。 このコンテナーは VM ごと、または VM とバックアップ タイプごとに作成できます。 スキーマは次のようになります。

 ![Microsoft Azure Storage BLOB への SQL Server 2012 バックアップの使用 – 別のストレージ アカウントにある別のコンテナー][dbms-guide-figure-500]

上記の例では、VM がデプロイされているストレージ アカウントと同じストレージ アカウントにはバックアップが実行されません。 バックアップ専用の新しいストレージ アカウントがあるものとします。 そのストレージ アカウント内で、バックアップ タイプと VM 名の組み合わせに対して作成された別のコンテナーが存在するとします。 このようにセグメント化することで、異なる VM のバックアップの管理が簡単になります。

バックアップを直接書き込む先の BLOB は、VM のデータ ディスクの数に計上されません。 そのため、データ ファイルとトランザクション ログ ファイルのデータに対して特定の VM SKU のマウントされるデータ ディスクの最大数が最大になり、引き続きストレージ コンテナーに対してバックアップを実行できます。 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 以前のリリース
Azure Storage に対して直接バックアップを実行するには、まず[こちら](https://www.microsoft.com/download/details.aspx?id=40740)の KBA 記事にリンクされている msi をダウンロードします。

x64 インストール ファイルとドキュメントをダウンロードします。 このファイルは **Microsoft SQL Server Backup to Microsoft Azure Tool** というプログラムをインストールします。 製品のドキュメントをよく読んでください。  ツールは、基本的に次のように動作します。

* SQL Server 側から、SQL Server バックアップのディスクの場所が定義されます (場所として、D:\ ドライブを使用しないでください)。
* このツールを使用すると、さまざまな Azure Storage コンテナーにさまざまなタイプのバックアップを行うように指定するルールを定義できます。
* ルールが設定されると、このツールは VHD またはディスクの 1 つへのバックアップの書き込みストリームを、既に定義されている Azure Storage の場所にリダイレクトします。
* このツールは SQL Serverバックアップ用に定義された VHD またはディスクに数 KB ほどの小さなサイズのスタブ ファイルを残します。 **このファイルは Azure Storage から再び復元するために必要ですので、ストレージのこの場所に残しておく必要があります。**
  * スタブ ファイルが失われた (スタブ ファイルが含まれるストレージ メディアの損失など) 場合に、Microsoft Azure Storage アカウントにバックアップするオプションを選択していれば、そのスタブ ファイルが置かれたストレージ コンテナーからスタブ ファイルをダウンロードし、Microsoft Azure Storage を通してスタブ ファイルを復旧できます。 ツールの検出先および同じコンテナーへのアップロード先として構成されているローカル コンピューター上のフォルダーにスタブ ファイルを配置します。元のルールで暗号化が使用されていた場合は、同じ暗号化パスワードを使用します。 

つまり、SQL Server の新しいリリースに対する上記のスキーマは、Azure Storage の場所に直接指示することができない SQL Server のリリースに対しても同様に設置できるということを意味します。

この方法は、Azure Storage に対するバックアップをネイティブでサポートする新しい SQL Server リリースでは使用しないでください。 例外は、Azure へのネイティブ バックアップの制限事項によって Azure へのネイティブ バックアップの実行がブロックされる場合です。

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>SQL Server データベースのバックアップに関するその他の方法
データベースをバックアップするその他の方法は、バックアップを格納する追加のデータ ディスクを VM に接続することです。 このような場合は、ディスクの容量がいっぱいでないことを確認する必要があります。 いっぱいである場合、ディスクのマウントを解除して "アーカイブ" し、新しい空のディスクに置き換える必要があります。 そうする場合は、これらの VHD を、データベース ファイルが含まれる VHD とは別の Azure ストレージ アカウントに保持することも可能です。

もう 1 つの方法は、多くのディスクを接続できる大容量の VM を使用することです (32 個の VHD を持つ D14 など)。 記憶域スペースを使用して、さまざまな DBMS サーバー向けのバックアップ先として、使用する共有を作成できる柔軟な環境を構築します。

[こちら](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) にもベスト プラクティスがいくつか記載されています。 

#### <a name="performance-considerations-for-backupsrestores"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を果たします。 そのため、次のことを想定できます。

* データ ファイルの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップを書き込むためのターゲット (BLOB、VHD、またはディスク) が少ないほど、スループットが低下する。
* VM のサイズが小さいほど、Azure Storage との読み取りおよび書き取りにおけるストレージのスループットのクォータは小さくなる。 バックアップが Azure Blob に直接格納されるかどうかや、VHD に格納された後で再び Azure Blob に格納されるかどうかに依存する。

より新しいリリースでは、バックアップ ターゲットとして Microsoft Azure Storage BLOB を使用する場合、特定のバックアップごとに指定できる URL 対象は 1 つだけに制限されています。

ただし、以前のリリースで "Microsoft SQL Server Backup to Microsoft Azure Tool" を使用する場合は、1 つ以上のファイル ターゲットを定義できます。 1 つ以上のターゲットがある場合、バックアップをスケールでき、バックアップのスループットが高くなります。 つまり、Azure ストレージ アカウントにも複数のファイルができることになります。 テストでは、ファイルの送信先を複数使用することで、SQL Server 2012 SP1 CU4 から実装されたバックアップ拡張機能によって達成できるスループットを確実に達成できます。 また、Azure へのネイティブ バックアップと同様に 1 TB の制限も回避できます。

ただし、スループットもバックアップに使用する Azure ストレージ アカウントの場所によって左右されることを忘れないでください。 VM が実行されているリージョンとは別のリージョンにストレージ アカウントを配置するというアイデアもあるかもしれません。 たとえば、VM 構成を西ヨーロッパで実行し、バックアップに使用するストレージ アカウントは北ヨーロッパに配置するとします。 これはバックアップのスループットに確実に影響を与え、ターゲットのストレージと VM が同じリージョンのデータ センターで実行される場合は実現可能に見える 150 MB/秒のスループットを達成できそうにありません。

#### <a name="managing-backup-blobs"></a>バックアップ BLOB の管理
独自のバックアップを管理する必要があります。 頻繁にトランザクション ログのバックアップを実行することによって多数の BLOB が作成されると想定されるため、それらの BLOB の管理によって Azure Portal に明らかに負荷がかかります。 したがって、Azure Storage エクスプローラーを活用することをお勧めします。 Azure Storage アカウントの管理に役立つものがいくつか提供されています。

* Azure SDK がインストールされた Microsoft Visual Studio (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* サード パーティ製のツール

Backup と SAP on Azure の詳細については、[SAP のバックアップ ガイド](sap-hana-backup-guide.md)に関する記事を参照してください。

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Microsoft Azure Marketplace からの SQL Server イメージの使用
マイクロソフトは Azure Marketplace で VM を提供しています。VM には SQL Server のバージョンがすでに含まれています。 SQL Server および Windows のライセンスを必要とする SAP のお客様は、SQL Server がすでにインストールされている VM をスピン アップすることで、ライセンスの必要性に根本的に対応できるというチャンスがあります。 SAP でそのようなイメージを使用するためには、次の事項を考慮する必要があります。

* SQL Server 評価版以外のバージョンでは、Azure Marketplace から "Windows のみ" の VM をデプロイする場合よりもコストがかかります。 価格を比較する場合は、<https://azure.microsoft.com/pricing/details/virtual-machines/windows/> および <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> を参照してください。 
* また、SQL Server 2012 など、SAP でサポートされている SQL Server のリリースのみを使用できます。
* Azure Marketplace が提供する VM にインストールされている SQL Server インスタンスの照合順序は、SAP NetWeaver が SQL Server インスタンスの実行に要求する照合順序ではありません。 以降のセクションの指示で、照合順序を変更できます。

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Microsoft Windows または SQL Server VM の SQL Server の照合順序を変更する
Azure Marketplace の SQL Server イメージは SAP NetWeaver アプリケーションで必要な照合順序を使用するようにセットアップされていないため、デプロイ後すぐに変更する必要があります。 SQL Server 2012 の場合、VM がデプロイされ、管理者がデプロイされた VM にログインできるようになった時点で次の手順で変更することができます。

* 管理者として Windows コマンド ウィンドウを開きます。
* ディレクトリを C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012 に変更します。
* 次のコマンドを実行します。Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name` /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> は、ギャラリーを使用して最初に VM をデプロイするときに Administrator アカウントとして定義されたアカウントです。

処理にかかるのは、わずか数分間です。 この手順で正しい結果が得られるかどうかを確認するには、次の手順を実行します。

* SQL Server Management Studio を開きます。
* [クエリ] ウィンドウを開きます。
* SQL Server マスター データベースで sp_helpsort コマンドを実行します。

結果が、次のように表示されます。

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

正しい結果でない場合は、SAP のデプロイを中止し、setup コマンドが期待通りに動作しなかった原因を調査します。 上記以外の SQL Server コード ページを持つ SQL Server インスタンスへの SAP NetWeaver アプリケーションのデプロイは、サポートされて **いません** 。

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure の SAP 向け SQL Server 高可用性
ここでこれまでに説明したように、最も古い SQL Server の高可用性機能の使用に必要な共有ストレージを作成することはありません。 この機能は、ユーザー データベース (最終的には tempdb) 用の共有ディスクを使用して、Windows Server フェールオーバー クラスター (WSFC) に 2 つ以上の SQL Server インスタンスをインストールします。 これは、SAP でもサポートされている長期的な標準の高可用性メソッドです。 Azure が共有ストレージをサポートしていないため、共有ディスク クラスター構成を使用した SQL Server の高可用性構成は実現できません。 ただし、高可用性を実現する方法はその他にも多くあります。詳しくは以降のセクションで説明します。

#### <a name="sql-server-log-shipping"></a>SQL Server ログ配布
高可用性 (HA) のメソッドの 1 つは、SQL Server ログ配布です。 HA 構成に参加している VM で名前解決が機能している場合、まったく問題はなく、Azure でのセットアップはオンプレミスで行われるセットアップとまったく変わりません。 IP 解決のみに依存することはお勧めしません。 ログ配布のセットアップとログ配布の指針については、次のドキュメントをご覧ください。

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

高可用性を実現するには、同じ Azure 可用性セット内のログ配布構成にある VM をデプロイする必要があります。

#### <a name="database-mirroring"></a>データベース ミラーリング
SAP でサポートされているデータベース ミラーリング (SAP Note [965908] 参照) は、SAP 接続文字列でのフェールオーバー パートナーの定義に依存しています。 クロスプレミスの場合、同じドメインに 2 つの VM があり、2 つの SQL Server インスタンスが実行されているユーザー コンテキストがドメイン ユーザーであると想定しています。また、関係する 2 つの SQL Server インスタンスのための十分な権限を持っているという前提です。 そのため、Azure でのデータベース ミラーリングのセットアップは、典型的なオンプレミスのセットアップ/構成と違いはありません。

クラウドのみのデプロイの時点で、最も簡単な方法は 1 つのドメイン内にそれらの DBMS VM (および理想的には専用の SAP VM) を持つことができるように Azure で別のドメインをセットアップすることです。

ドメインをセットアップできない場合は、<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql> に記載されているように、データベース ミラーリング エンドポイントの証明書を使用することもできます。

Azure でのデータベース ミラーリングのセットアップのチュートリアルについては、<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> を参照してください。 

#### <a name="sql-server-always-on"></a>SQL Server AlwaysOn
AlwaysOn は SAP オンプレミスでサポートされており (SAP Note [1772688] を参照)、この機能は Azure で SAP と組み合わせて使用するためにサポートされています。 Azure で共有ディスクを作成できないという事実は、異なる VM 間での AlwaysOn Windows Server フェールオーバー クラスター (WSFC) 構成を作成できないということを意味します。 これは、あくまでも共有ディスクをクラスター構成のクォーラムとして使用することができないという意味です。 そのため、Azure で共有ディスクを使用するクォーラムの種類を選択せずに、AlwaysOn WSFC 構成を構築することは可能です。 VM がデプロイされる Azure 環境では VM を名前で解決する必要があり、VM は同じドメインにある必要があります。 これは、Azure のみのデプロイとクロスプレミスのデプロイの場合に当てはまります。 オンプレミスでは可能ですが、現時点で Azure では AD/DNS オブジェクトを作成できないため、SQL Server 可用性グループ リスナー (Azure 可用性セットと混同しないこと) のデプロイに関する特別な考慮事項があります。 そのため、Azure 固有の動作に対応するために、いくつかの別のインストール手順が必要です。

可用性グループ リスナーを使用する場合の考慮事項は次のとおりです。

* 可用性グループ リスナーは、Windows Server 2012 以上を VM のゲスト OS として使用する場合にのみ使用できます。 Windows Server 2012 の場合、このパッチ (<https://support.microsoft.com/kb/2854082>) が適用されていることを確認する必要があります。 
* Windows Server 2008 R2 の場合、このパッチは存在せず、AlwaysOn は、接続文字列にフェールオーバー パートナーを指定するというデータベース ミラーリングと同じ方法で使用する必要があります (SAP default.pfl パラメーター dbs/mss/server によって実行されます。SAP Note [965908] を参照)。
* 可用性グループ リスナーを使用する場合、データベースの VM を専用のロード バランサーに接続する必要があります。 クラウドのみのデプロイにおける名前解決には、SAP システムのすべての VM (アプリケーション サーバー、DBMS サーバー、(A)SCS サーバー) が同じ仮想ネットワーク内にあるか、解決済みの SQL Server VM の VM 名を取得するために SAP アプリケーション レイヤーから etc\host ファイルをメンテナンスする必要があります。 両方の VM が意図せずシャットダウンされた場合に Azure で新しい IP アドレスが割り当てられるのを回避するため、AlwaysOn 構成の VM のネットワーク インターフェイスに静的 IP アドレスを割り当てる必要があります (静的 IP アドレスの定義については[こちら][virtual-networks-reserved-private-ip]の記事を参照)。

[comment]: <> (古いブログ)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Azure の現在の機能では、クラスター名にクラスターが作成されたノードと同じ IP アドレスを割り当てるため、WSFC クラスター構成を構築してそのクラスターに特定の IP アドレスを割り当てる必要がある場合、特別な手順が必要です。 つまり、クラスターに別の IP アドレスを割り当てるためには手動の手順を実行する必要があります。
* TCP/IP エンドポイントを使用して Azure に可用性グループ リスナーを作成しようとしています。TCP/IP エンドポイントは、可用性グループのプライマリ レプリカとセカンダリ レプリカを実行している VM に割り当てられています。
* これらのエンドポイントと ACL をセキュリティで保護する必要があります。

[comment]: <> (TODO 古いブログ)
[comment]: <> ([こちら][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups] で利用できるチュートリアルで Azure で AlwaysOn 構成をインストールするために必要なものの確認と詳細な手順の体験ができます。)
[comment]: <> (Azure ギャラリーを介した事前構成済みの Always On 設定 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (可用性グループ リスナーの作成については [このチュートリアル][virtual-machines-windows-classic-ps-sql-int-listener] に詳しい説明があります)
[comment]: <> (ACL によるネットワーク エンドポイントのセキュリティ強化についてはこちらに詳しい説明があります)
[comment]: <> (*    <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

SQL Server Always On 可用性グループは、異なる Azure リージョンにもデプロイできます。 この機能は Azure Vnet 間接続を利用します ([詳細][virtual-networks-configure-vnet-to-vnet-connection])。

[comment]: <> (TODO 古いブログ)
[comment]: <> (このようなシナリオの SQL Server AlwaysOn 可用性グループの設定については、こちらを参照してください。 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Azure での SQL Server 高可用性の概要
Azure ストレージがコンテンツを保護しているという事実を考えると、ホット スタンバイ イメージを要求する理由はあまりありません。 これは、高可用性シナリオは、次のケースに対してのみ保護する必要があることを意味します。

* Azure またはその他の理由でサーバー クラスター上のメンテナンスのため、全体として、VM を利用できないこと
* SQL Server インスタンス内のソフトウェアの問題
* データが取得されるというヒューマン エラーからの保護とポイント イン タイム リカバリーの必要性

データベース ミラーリングまたは AlwaysOn で対処できる最初の 2 つのケースに適合するテクノロジを検討します。3 番目のケースはログ配布でのみ対応できます。

AlwaysOn のセットアップはよりデータベース ミラーリングよりも複雑である一方で利点もあるため、比較検討する必要があります。 これらの利点は、次のようになります。

* 読み取り可能なセカンダリ レプリカ
* セカンダリ レプリカからのバックアップ
* スケーラビリティの向上
* 複数のセカンダリ レプリカ

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Azure での一般的な SAP 用 SQL Server の概要
このガイドには多くの推奨事項が記載されているため、Azure デプロイを計画する前に 2 回以上読むことをお勧めします。 ただし、一般的な Azure 固有のポイントでの、一般的な DBMS 上位 10 個に従ってください。

[comment]: <> ( 2.3 何と比較してスループットが向上するのでしょうか。1 つの VHD ですか。)
1. Azure で多くのメリットのある SQL Server 2014 のように、最新の DBMS リリースを使用します。 SQL Server の場合、これは SQL Server 2012 SP1 CU4 です。Azure Storage に対するバックアップ機能が含まれます。 ただし、SAP と組み合わせる場合は、少なくとも SQL Server 2014 SP1 CU1 または SQL Server 2012 SP2 と最新の CU を使用することをお勧めします。
2. データ ファイルのレイアウトと Azure の制限事項のバランスをとるために、Azure での SAP システム ランドスケープを慎重に計画します。
   * あまり多くはないが、必要な IOPS を達成するには十分な数のディスクがある。
   * Managed Disks を使用しない場合、IOPS は Azure Storage アカウントごとに制限され、Storage のアカウントは各 Azure サブスクリプション内に制限されることに注意する ([詳細][azure-subscription-service-limits])。 
   * 高いスループットを実現する必要がある場合にのみディスク間でストライピングします。
3. D:\ ドライブにソフトウェアをインストールおよび保持する必要があるファイルを置かない。このドライブは非永続的であるため、Windows の再起動時にすべて失われます。
4. Azure Standard Storage でディスク キャッシュを使用しない。
5. Azure ストレージ アカウントの Geo レプリケーションを使用しない。  DBMS ワークロードにローカル冗長を使用する。
6. DBMS ベンダーの HA/DR ソリューションを使用して、データベースのデータをレプリケートする。
7. 常に名前解決を使用して、IP アドレスに依存しないようにする。
8. 考えられる最高のデータベースの圧縮を使用する。 SQL Server の場合はページ圧縮。
9. Azure Marketplace の SQL Server イメージを使用するよう注意してください。 いずれかの SQL Server を使用する場合は、SAP NetWeaver システムをインストールする前にインスタンスの照合順序を変更する必要があります。
10. 「[デプロイ ガイド][deployment-guide]」に従い、SAP Host Monitoring for Azure をインストールして構成します。

## <a name="specifics-to-sap-ase-on-windows"></a>Windows 上の SAP ASE の仕様
Microsoft Azure 以降では、既存の SAP ASE アプリケーションを Azure Virtual Machines に簡単に移行できます。 Virtual Machine の SAP ASE では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行することで、これらのアプリケーションのデプロイメント、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SAP ASE では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。

Azure Virtual Machines の SLA については、<https://azure.microsoft.com/support/legal/sla/virtual-machines> を参照してください。

Microsoft Azure がホストする Virtual Machines は、他のパブリック クラウド仮想化製品と比べて良好に機能しますが、個々の結果は異なる場合があります。 別の SAP 認定 VM SKU のSAP サイズ設定 SAPS 番号については、別の SAP Note [1928533] をご覧ください。

ステートメントと Azure Storage、SAP VM の展開、SAP Monitoring の使用方法に関する推奨事項は、このドキュメントの最初の 4 つの章で説明したように SAP アプリケーションと共に SAP ASE の展開に適用されます。

### <a name="sap-ase-version-support"></a>SAP ASE バージョンのサポート
SAP は SAP Business Suite 製品と合わせて使用するために SAP ASE バージョン 16.0 を現在サポートしています。 SAP ASE サーバー、または SAP Business Suite 製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。

オンプレミス インストールの場合、Sybase の web サイトから直接 SAP ASE サーバー、または、JDBC、および ODBC ドライバーの更新プログラムをダウンロードしないでください。 オンプレミスと Azure Virtual Machines 内の SAP Business Suite 製品で使用するようにサポートされているパッチについて詳しくは、次の SAP Note をご覧ください。

* [1590719]
* [1973241]

SAP ASE で SAP Business Suite を実行する場合の一般的な情報については、 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM で SAP 関連 SAP ASE をインストールするための SAP ASE 構成ガイドライン
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE のデプロイの構造
一般的な説明のとおり、SAP ASE 実行可能ファイルは VM の OS ディスク (c: ドライブ\) のシステム ドライブに配置またはインストールされている必要があります。 通常、SAP NetWeaver ワークロードは、SAP ASE システムおよびツールのデータベースの大半をほとんど使用していません。 従って、システムおよびツールのデータベース (master、model、saptools、sybmgmtdb、sybsystemdb) は、C:\ ドライブに残すこともできます。 

例外は、すべての作業テーブルと SAP ASE で作成された一時テーブルを含む一時データベースです。この場合、SAP ERP ワークロードおよびすべての BW ワークロードでは、元の VM の OS ディスク (c: ドライブ\) に収まらない大量のデータまたは I/O 操作ボリュームのいずれかが必要になる場合があります。

SAPInst/SWPM システムをインストールするために使用するバージョンによっては、次のデータベースが含まれる場合があります。

* SAP ASE をインストールするときに作成された 1 つの SAP ASE tempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および SAP インストール ルーチンによって作成された追加の saptempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および ERP/BW tempdb の特定の要件を満たすために手動で作成された追加の tempdb (例: 次の SAP Note [1752266])

特定の ERP ワークロードまたはすべての BW ワークロードの場合、(SWPM または手動で) 追加で作成された tempdb の tempdb デバイスを C: 以外のドライブに保持することは、パフォーマンスの観点から理に適っています。 追加の tempdb が存在しない場合は、1 つ作成することをお勧めします (SAP Note [1752266])。

このようなシステムでは、追加で作成された tempdb に対して次の手順を実行する必要があります。

* 最初の tempdb デバイスを SAP データベースの最初のデバイスに移動します。
* SAP データベースのデバイスを含む VHD のそれぞれに tempdb のデバイスを追加します。

この構成によって、tempdb はシステム ドライブが提供するよりも多くの領域を使用できます。 参照用に、オンプレミスで実行している既存のシステムで tempdb デバイスのサイズを確認できます。 また、このような構成により tempdb に対する IOPS 値をシステム ドライブでは設定できない値に設定できます。 繰り返しますが、オンプレミスで実行されているシステムを、tempdb に対する I/O ワークロードの監視に使用できます。

VM の D:\ ドライブに、SAP ASE デバイスを配置しないでください。 これは、tempdb にも当てはまります。tempdb に保管されているオブジェクトが一時的なものであってもです。

#### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、既存の SAP データベースを Azure にアップロードする前に、SAP ASE 圧縮が使用されていることを確認するようお勧めします。

実装されていない場合は、Azure にアップロードする前に圧縮を実行する推奨事項が、次のいくつかの理由から与えられます。

* Azure にアップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短い
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースと LOB の圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP ASE データベースですでに圧縮を使用しているかどうかを確認する方法について詳しくは、SAP Note [1750510] をご覧ください。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit を使用してデータベースのインスタンスを監視する
データベース プラットフォームとして SAP ASE を使用する SAP システムの場合、DBACockpit はトランザクション DBACockpit に埋め込まれたブラウザー ウィンドウとして、または Webdynpro としてアクセスできます。 ただし、データベースの管理と管理を行うための完全な機能は DBACockpit の Webdynpro の実装でのみ使用可能です。

オンプレミス システムと同様に、DBACockpit の Webdynpro 実装で使用されるすべての SAP NetWeaver 機能を有効にするために、いくつかの手順が必要です。 SAP Note [1245200] に従って、Webdynpros の使用を有効にし、必要なものを生成します。 上記の Note の手順に従う際には、インターネット通信マネージャー (icm) に加えて、http 接続や https 接続に使用するポートも構成します。 Http の既定の設定は次のようになります。

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

トランザクション DBACockpit で生成されたリンクは次のようになります。

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP システムをホストする Azure Virtual Machine がサイト間、マルチサイト、または ExpressRoute (クロスプレミス デプロイ) によって接続されているかどうかや、その方法によっては、DBACockpit を開こうとしているコンピューターで解決できる完全修飾ホスト名を ICM が使用していることを確認する必要があります。 ICM がプロファイルのパラメーターに応じて完全修飾ホスト名を判定し、必要な場合に icm/host_name_full に明示的にパラメーターを設定する方法について詳しくは、SAP Note [773830] をご覧ください。

オンプレミスと Azure 間のクロスプレミス接続を使用しないクラウドのみのシナリオで VM をデプロイした場合は、パブリック IP アドレスと、domainlabel を定義する必要があります。 VM のパブリック DNS 名の形式は、次のようになります。

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 名について詳しくは、[こちら][virtual-machines-azurerm-versus-azuresm]をご覧ください。

SAP プロファイル パラメーター icm/host_name_full を Azure VM のリンクの DNS 名に設定すると、リンクは次のようになる場合があります。

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

この場合、次のことを行う必要があります。

* ICM との通信に使用される TCP/IP ポート用に、Azure Portal でネットワーク セキュリティ グループへの受信規則を追加します。
* ICM との通信に使用される TCP/IP ポート用に Windows ファイアウォールの構成への受信規則を追加します。

使用可能なすべての修正を自動的にインポートする場合、使用している SAP のバージョンに該当する修正コレクション SAP Note を定期的に適用することをお勧めします。

* [1558958]
* [1619967]
* [1882376]

SAP ASE の DBA Cockpit に関する詳細については、次の SAP Note で参照することができます。

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE のバックアップ/復旧に関する考慮事項
Azure に SAP ASE をデプロイする場合、バックアップ方法を確認する必要があります。 実稼働システムでない場合でも、SAP ASE でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 たとえば、バックアップを復元すると、同じシステムの 3 層システムのセットアップを 2 層 SAP 構成から転送できます。

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。 次の SAP Note をご覧ください。

* [1588316]
* [1585981]

(ダンプの構成の作成とバックアップのスケジュール設定についての詳細について)。 戦略やニーズに応じて、既存のディスクの 1 つの上にデータベースおよびディスクへのログ ダンプを構成し、バックアップ用にディスクを追加できます。 エラー発生時のデータ損失の危険性を減らすために、データベース デバイスが存在しないディスクを使用することをお勧めします。

データと LOB だけでなく、SAP ASE の圧縮は、バックアップの圧縮を提供します。 データベースとログのダンプが占める領域を小さくするには、バックアップの圧縮を使用することをお勧めします。 詳しくは、SAP Note [1588316] をご覧ください。 バックアップの圧縮はバックアップまたは内部設置型にダンプ Azure 仮想マシンからバックアップを含む VHD をダウンロードする場合、転送されたデータの量を削減するために重要ではもです。

データベースまたはログのダンプ先として D:\ ドライブを使用してはいけません。

#### <a name="performance-considerations-for-backupsrestores"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を果たします。 そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲットの数を増やすために、次の 2 つのオプションが用意されています。必要に応じて一方を使用または組み合わせて使用できます。

* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* ダンプの書き込み先として複数のターゲット ディレクトリを使用するダンプ構成を SAP ASE レベルで作成する

マウントされた複数のディスクの上にボリュームをストライピングする方法については、このガイドの前半で説明しました。 SAP ASE ダンプ構成で複数のディレクトリを使用する方法について詳しくは、[Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) でダンプ構成の作成に使用されるストアド プロシージャ sp_config_dump に関するドキュメントをご覧ください。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM を使用した障害復旧
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase Replication Server を使用したデータのレプリケーション
SAP Sybase Replication Server (SRS) によって、SAP ASE はデータベース トランザクションを離れた場所に非同期的に転送するウォーム スタンバイ ソリューションを提供します。 

SRS のインストールと動作は、Azure Virtual Machine サービスでホストされている VM 内で、オンプレミスと同様に機能します。

SAP ASE HADR には、Azure 内部ロード バランサーは不要です。OS レベルのクラスタリングに対する依存関係はなく、Azure の Windows および Linux VM で動作します。 SAP ASE HADR の詳細については、[SAP ASE HADR ユーザー ガイド](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)をご覧ください。

## <a name="specifics-to-sap-ase-on-linux"></a>Linux 上の SAP ASE の仕様
Microsoft Azure 以降では、既存の SAP ASE アプリケーションを Azure Virtual Machines に簡単に移行できます。 Virtual Machine の SAP ASE では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行することで、これらのアプリケーションのデプロイメント、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SAP ASE では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。

Azure VM をデプロイする際には、<https://azure.microsoft.com/support/legal/sla> にアクセスして正式な SLA を理解しておくことが重要です。

SAP のサイズ設定情報と SAP 認定 VM SKU の一覧は、SAP Note [1928533] をご覧ください。 Azure 仮想マシンの SAP のサイズ設定については、<http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> および <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx> にも記載されています。

ステートメントと Azure Storage、SAP VM の展開、SAP Monitoring の使用方法に関する推奨事項は、このドキュメントの最初の 4 つの章で説明したように SAP アプリケーションと共に SAP ASE の展開に適用されます。

次の 2 つの SAP Note には、Linux 上の ASE とクラウド内の ASE に関する一般的な情報が含まれています。

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE バージョンのサポート
SAP は SAP Business Suite 製品と合わせて使用するために SAP ASE バージョン 16.0 を現在サポートしています。 SAP ASE サーバー、または SAP Business Suite 製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。

オンプレミス インストールの場合、Sybase の web サイトから直接 SAP ASE サーバー、または、JDBC、および ODBC ドライバーの更新プログラムをダウンロードしないでください。 オンプレミスと Azure Virtual Machines 内の SAP Business Suite 製品で使用するようにサポートされているパッチについて詳しくは、次の SAP Note をご覧ください。

* [1590719]
* [1973241]

SAP ASE で SAP Business Suite を実行する場合の一般的な情報については、 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM で SAP 関連 SAP ASE をインストールするための SAP ASE 構成ガイドライン
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE のデプロイの構造
一般的な説明に従って、SAP ASE 実行可能ファイルには VM のルート ファイル システム (/sybase:) に配置またはインストールされている必要があります。 通常、SAP NetWeaver ワークロードは、SAP ASE システムおよびツールのデータベースの大半をほとんど活用していません。 データベース システムおよびツール データベース (master、model、saptools、sybmgmtdb、sybsystemdb) はルート ファイル システム上にも残すことができます。 

例外は、すべての作業テーブルと SAP ASE で作成された一時テーブルを含む一時データベースです。この場合、一部の SAP ERP ワークロードおよびすべての BW ワークロードで、元の VM の OS ディスクに収まらない大量のデータ ボリュームまたは I/O 操作のいずれかが必要になる可能性があります。

SAPInst/SWPM システムをインストールするために使用するバージョンによっては、次のデータベースが含まれる場合があります。

* SAP ASE をインストールするときに作成された 1 つの SAP ASE tempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および SAP インストール ルーチンによって作成された追加の saptempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および ERP/BW tempdb の特定の要件を満たすために手動で作成された追加の tempdb (例: 次の SAP Note [1752266])

特定の ERP ワークロードまたはすべての BW ワークロードの場合、(SWPM または手動で) 追加で作成された tempdb の tempdb デバイスを、単一の Azure データ ディスク、または複数の Azure データ ディスクにまたがる Linux RAID で表すことができる別のファイル システムに保持することは、パフォーマンスの観点から理に適っています。 追加の tempdb が存在しない場合は、1 つ作成することをお勧めします (SAP Note [1752266])。

このようなシステムでは、追加で作成された tempdb に対して次の手順を実行する必要があります。

* 最初の tempdb ディレクトリを SAP データベースの最初のファイル システムに移動します。
* SAP データベースのファイルシステムが含まれる各ディスクに tempdb ディレクトリを追加します。

この構成によって、tempdb はシステム ドライブが提供するよりも多くの領域を使用できます。 参照用に、オンプレミスで実行している既存のシステムで tempdb ディレクトリのサイズを確認できます。 また、このような構成により tempdb に対する IOPS 値をシステム ドライブでは設定できない値に設定できます。 繰り返しますが、オンプレミスで実行されているシステムを、tempdb に対する I/O ワークロードの監視に使用できます。

VM の /mnt または /mnt/resource に SAP ASE ディレクトリを配置しないでください。 これは、tempdb にも当てはまります。tempdb に保管されているオブジェクトが一時的なものであってもです。その理由は、/mnt または/mnt/resource は既定の Azure VM の一時領域であり、永続的でないためです。 Azure VM の一時領域について詳しくは、[こちらの記事][virtual-machines-linux-how-to-attach-disk]に記載されています。

#### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、既存の SAP データベースを Azure にアップロードする前に、SAP ASE 圧縮が使用されていることを確認するようお勧めします。

実装されていない場合は、Azure にアップロードする前に圧縮を実行する推奨事項が、次のいくつかの理由から与えられます。

* Azure にアップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短い
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースと LOB の圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP ASE データベースですでに圧縮を使用しているかどうかを確認する方法について詳しくは、SAP Note [1750510] をご覧ください。 データベース圧縮に関するその他の情報については、SAP Note [2121797] をご覧ください。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit を使用してデータベースのインスタンスを監視する
データベース プラットフォームとして SAP ASE を使用する SAP システムの場合、DBACockpit はトランザクション DBACockpit に埋め込まれたブラウザー ウィンドウとして、または Webdynpro としてアクセスできます。 ただし、データベースの管理と管理を行うための完全な機能は DBACockpit の Webdynpro の実装でのみ使用可能です。

オンプレミス システムと同様に、DBACockpit の Webdynpro 実装で使用されるすべての SAP NetWeaver 機能を有効にするために、いくつかの手順が必要です。 SAP Note [1245200] に従って、Webdynpros の使用を有効にし、必要なものを生成します。 上記の Note の手順に従う際には、インターネット通信マネージャー (icm) に加えて、http 接続や https 接続に使用するポートも構成します。 Http の既定の設定は次のようになります。

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

トランザクション DBACockpit で生成されたリンクは次のようになります。

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP システムをホストする Azure Virtual Machine がサイト間、マルチサイト、または ExpressRoute (クロスプレミス デプロイ) によって接続されているかどうかや、その方法によっては、DBACockpit を開こうとしているコンピューターで解決できる完全修飾ホスト名を ICM が使用していることを確認する必要があります。 ICM がプロファイルのパラメーターに応じて完全修飾ホスト名を判定し、必要な場合に icm/host_name_full に明示的にパラメーターを設定する方法について詳しくは、SAP Note [773830] をご覧ください。

オンプレミスと Azure 間のクロスプレミス接続を使用しないクラウドのみのシナリオで VM をデプロイした場合は、パブリック IP アドレスと、domainlabel を定義する必要があります。 VM のパブリック DNS 名の形式は、次のようになります。

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 名について詳しくは、[こちら][virtual-machines-azurerm-versus-azuresm]をご覧ください。

SAP プロファイル パラメーター icm/host_name_full を Azure VM のリンクの DNS 名に設定すると、リンクは次のようになる場合があります。

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

この場合、次のことを行う必要があります。

* ICM との通信に使用される TCP/IP ポート用に、Azure Portal でネットワーク セキュリティ グループへの受信規則を追加します。
* ICM との通信に使用される TCP/IP ポート用に Windows ファイアウォールの構成への受信規則を追加します。

使用可能なすべての修正を自動的にインポートする場合、使用している SAP のバージョンに該当する修正コレクション SAP Note を定期的に適用することをお勧めします。

* [1558958]
* [1619967]
* [1882376]

SAP ASE の DBA Cockpit に関する詳細については、次の SAP Note で参照することができます。

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE のバックアップ/復旧に関する考慮事項
Azure に SAP ASE をデプロイする場合、バックアップ方法を確認する必要があります。 実稼働システムでない場合でも、SAP ASE でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 たとえば、バックアップを復元すると、同じシステムの 3 層システムのセットアップを 2 層 SAP 構成から転送できます。

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。 次の SAP Note をご覧ください。

* [1588316]
* [1585981]

(ダンプの構成の作成とバックアップのスケジュール設定についての詳細について)。 戦略やニーズに応じて、既存のディスクの 1 つの上にデータベースおよびディスクへのログ ダンプを構成し、バックアップ用にディスクを追加できます。 エラー発生時のデータ損失の危険性を減らすために、データベース ディレクトリやデータベース ファイルが存在しないディスクを使用することをお勧めします。

データと LOB だけでなく、SAP ASE の圧縮は、バックアップの圧縮を提供します。 データベースとログのダンプが占める領域を小さくするには、バックアップの圧縮を使用することをお勧めします。 詳しくは、SAP Note [1588316] をご覧ください。 バックアップの圧縮はバックアップまたは内部設置型にダンプ Azure 仮想マシンからバックアップを含む VHD をダウンロードする場合、転送されたデータの量を削減するために重要ではもです。

データベースまたはログのダンプ先として、Azure 仮想マシンは一時領域 /mnt または /mnt/resource を使用することはできません。

#### <a name="performance-considerations-for-backupsrestores"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を果たします。 そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (Linux ソフトウェア RAID、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲットの数を増やすために、次の 2 つのオプションが用意されています。必要に応じて一方を使用または組み合わせて使用できます。

* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* ダンプの書き込み先として複数のターゲット ディレクトリを使用するダンプ構成を SAP ASE レベルで作成する

マウントされた複数のディスクの上にボリュームをストライピングする方法については、このガイドの前半で説明しました。 SAP ASE ダンプ構成で複数のディレクトリを使用する方法について詳しくは、[Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) でダンプ構成の作成に使用されるストアド プロシージャ sp_config_dump に関するドキュメントをご覧ください。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM を使用した障害復旧
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase Replication Server を使用したデータのレプリケーション
SAP Sybase Replication Server (SRS) によって、SAP ASE はデータベース トランザクションを離れた場所に非同期的に転送するウォーム スタンバイ ソリューションを提供します。 

SRS のインストールと動作は、Azure Virtual Machine サービスでホストされている VM 内で、オンプレミスと同様に機能します。

SAP Replication Server を経由する ASE HADR は、現時点ではサポートされていません。 将来的には Microsoft Azure プラットフォーム用にテストされ、リリースされる可能性があります。

## <a name="specifics-to-oracle-database-on-windows"></a>Windows 上の Oracle データベースの詳細
Oracle ソフトウェアを Microsoft Windows Hyper-V や Azure 上で実行できるようになりました。 

一般的なサポートに続いて、Oracle Database を活用する SAP アプリケーションの特定のシナリオも同様にサポートされます。 詳細については、ドキュメントのこの部分で説明します。

### <a name="oracle-version-support"></a>Oracle バージョンのサポート
Azure Virtual Machines で SAP on Oracle を実行できる Oracle のバージョンと対応する OS のバージョンについて詳しくは、SAP Note [2039619] をご覧ください。

Oracle での SAP Business Suite の実行に関する一般的な情報については、1DX (<https://www.sap.com/community/topic/oracle.html>) をご覧ください。

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための Oracle 構成ガイドライン
#### <a name="storage-configuration"></a>ストレージの構成
NTFS でフォーマットされたディスクを使用した単一インスタンスの Oracle のみサポートされています。 すべてのデータベース ファイルは、VHD または Managed Disks をベースとする NTFS ファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされており、Azure ページ Blob Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) または Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>) に基づいています。 あらゆる種類のネットワーク ドライブまたは Azure ファイルサービスのようなリモート共有:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

は Oracle データベースのファイルではサポートされて **いません** 。

Azure ページ BLOB Storage または Managed Disks をベースとするディスクの使用については、このドキュメントの「[VM とデータ ディスクのキャッシュ][dbms-guide-2.1]」の章と「[Microsoft Azure Storage][dbms-guide-2.3]」の章の説明が Oracle Database によるデプロイにも適用されます。

このドキュメント前半の大部分で説明したように、Azure のディスクの IOPS スループットにクォータが存在します。 正確なクォータは、使用する VM タイプによって異なります。 VM タイプとそのクォータの一覧は、[こちら (Linux)][virtual-machines-sizes-linux] および[こちら (Windows)][virtual-machines-sizes-windows] に記載されています。

サポートされている Azure VM のタイプを識別するには、SAP Note [1928533] をご覧ください。

ディスクあたりの現在の IOPS クォータが要件を満たしている限り、マウントされた単一のディスクにすべてのデータベース ファイルを格納できます。 

高い IOPS が必要な場合は、Windows 記憶域プール (Windows Server 2012 以降でのみ提供) または Windows 2008 R2 の Windows ストライピングを使用して、マウントされた複数のディスクの上に 1 つの大きな論理デバイスを作成することをお勧めします (このドキュメントの「[ソフトウェア RAID][dbms-guide-2.2]」もご覧ください)。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。

#### <a name="backup--restore"></a>バックアップと復元
バックアップと復元機能については、SAP BR*Tools for Oracle が標準の Windows Server オペレーティング システムと Hyper-V と同様にサポートされています。 ディスクへのバックアップとディスクからの復元については Oracle Recovery Manager (RMAN) もサポートされます。

#### <a name="high-availability"></a>高可用性
高可用性とディザスター リカバリーを目的として Oracle Data Guard がサポートされています。 詳しくは、[この][virtual-machines-windows-classic-configure-oracle-data-guard]ドキュメントをご覧ください。

#### <a name="other"></a>その他
このドキュメントの最初の 3 つの章で説明したように、Oracle Database を使用した VM のデプロイについては、Azure 可用性セットや SAP の監視などの他のすべての一般的な領域が適用されます。

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Oracle Linux 上の Oracle Database の詳細
Oracle ソフトウェアを Microsoft Windows Hyper-V や Azure 上で実行できるようになりました。 

一般的なサポートに続いて、Oracle Database を活用する SAP アプリケーションの特定のシナリオも同様にサポートされます。 詳細については、ドキュメントのこの部分で説明します。

### <a name="oracle-version-support"></a>Oracle バージョンのサポート
Azure Virtual Machines で SAP on Oracle を実行できる Oracle のバージョンと対応する OS のバージョンについて詳しくは、SAP Note [2039619] をご覧ください。

Oracle での SAP Business Suite の実行に関する一般的な情報については、1DX (<https://www.sap.com/community/topic/oracle.html>) をご覧ください。

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための Oracle 構成ガイドライン
#### <a name="storage-configuration"></a>ストレージの構成
ext3、ext4、および xfs でフォーマットされたディスクを使用した単一インスタンスの Oracle のみサポートされています。 すべてのデータベース ファイルは、VHD または Managed Disks をベースとするこれらのファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされており、Azure ページ Blob Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) または Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>) に基づいています。 あらゆる種類のネットワーク ドライブまたは Azure ファイルサービスのようなリモート共有:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

は Oracle データベースのファイルではサポートされて **いません** 。

Azure ページ BLOB Storage または Managed Disks をベースとするディスクの使用については、このドキュメントの「[VM とデータ ディスクのキャッシュ][dbms-guide-2.1]」の章と「[Microsoft Azure Storage][dbms-guide-2.3]」の章の説明が Oracle Database によるデプロイにも適用されます。

このドキュメント前半の大部分で説明したように、Azure のディスクの IOPS スループットにクォータが存在します。 正確なクォータは、使用する VM タイプによって異なります。 VM タイプとそのクォータの一覧は、[こちら (Linux)][virtual-machines-sizes-linux] および[こちら (Windows)][virtual-machines-sizes-windows] に記載されています。

サポートされている Azure VM のタイプを識別するには、SAP Note [1928533] をご覧ください。

ディスクあたりの現在の IOPS クォータが要件を満たしている限り、マウントされた単一のディスクにすべてのデータベース ファイルを格納できます。 

高い IOPS が必要な場合は、LVM (Logical Volume Manager) または MDADM を使用して、マウントされた複数のディスクの上に 1 つの大きな論理ボリュームを作成することをお勧めします。 このドキュメントの「[ソフトウェア RAID][dbms-guide-2.2]」の章もご覧ください。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。

#### <a name="backup--restore"></a>バックアップと復元
バックアップと復元機能については、SAP BR*Tools for Oracle がベア メタルおよび Hyper-V と同様にサポートされています。 ディスクへのバックアップとディスクからの復元については Oracle Recovery Manager (RMAN) もサポートされます。

#### <a name="high-availability"></a>高可用性
高可用性とディザスター リカバリーを目的として Oracle Data Guard がサポートされています。 詳しくは、[この][virtual-machines-windows-classic-configure-oracle-data-guard]ドキュメントをご覧ください。

#### <a name="other"></a>その他
このドキュメントの最初の 3 つの章で説明したように、Oracle Database を使用した VM のデプロイについては、Azure 可用性セットや SAP の監視などの他のすべての一般的な領域が適用されます。

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Windows 上の SAP MaxDB データベースの詳細
### <a name="sap-maxdb-version-support"></a>SAP MaxDB バージョンのサポート
SAP は Azure で SAP NetWeaver ベースの製品を使用するために SAP MaxDB バージョン 7.9 を現在サポートしています。 SAP MaxDB サーバー、または SAP NetWeaver ベースの製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。
SAP MaxDB での SAP NetWeaver の実行に関する一般的な情報については、<https://www.sap.com/community/topic/maxdb.html> を参照してください。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS 向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP MaxDB DBMS 向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

Microsoft Windows オペレーティング システムの最新バージョンである Microsoft Windows 2012 R2 を使用することをお勧めします。

### <a name="available-sap-maxdb-documentation"></a>提供されている SAP MaxDB ドキュメント
次の SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP MaxDB 構成ガイドライン
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>ストレージの構成
SAP MaxDB 向けの Azure Storage のベスト プラクティスについては、「[RDBMS デプロイの構造][dbms-guide-2]」の章に記載されている一般的な推奨事項に従ってください。

> [!IMPORTANT]
> 他のデータベースと同様に、SAP MaxDB にはデータとログ ファイルもあります。 ただし、SAP MaxDB 用語では正しい用語は "ボリューム" です ("ファイル" ではありません)。 たとえば、SAP MaxDB のデータ ボリュームとログ ボリュームがあります。 OS ディスクのボリュームとこれらを混同しないでください。 
> 
> 

要するに、次のことを行う必要があります。

* Azure Storage アカウントを使用する場合は、SAP MaxDB データ ボリュームとログ ボリューム (ファイル) を保持する Azure Storage アカウントを、「[Microsoft Azure Storage][dbms-guide-2.3]」の章に記載したように、**ローカル冗長ストレージ (LRS)** に設定します。
* ログ ボリューム (ファイル) の IO パスと SAP MaxDB データ ボリューム (ファイル) の IO パスを分けます。 つまり、SAP MaxDB データ ボリューム (ファイル) を 1 つの論理ドライブにインストールし、SAP MaxDB ログ ボリューム (ファイル) を別の論理ドライブにインストールする必要があります。
* SAP MaxDB データ用かログ ボリューム (ファイル) 用か、および Azure Standard Storage を使用するか Azure Premium Storage を使用するかに応じて、各ディスクに適切なキャッシュ タイプを、「[VM とデータ ディスクのキャッシュ][dbms-guide-2.1]」の章の記載に従って設定します。
* ディスクあたりの現在の IOPS クォータが要件を満たしている限り、マウントされた単一のディスクにすべてのデータ ボリュームを格納し、マウントされた別の単一のディスクにデータベースのすべてのログ ボリュームを格納できます。
* 高い IOPS やより多くの領域が必要な場合は、Microsoft Windows 記憶域プール (Microsoft Windows Server 2012 以降でのみ提供) または Microsoft Windows 2008 R2 の Microsoft Windows ストライピングを使用して、マウントされた複数のディスクの上に 1 つの大きな論理デバイスを作成することをお勧めします。 このドキュメントの「[ソフトウェア RAID][dbms-guide-2.2]」の章もご覧ください。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。
* 最も高い IOPS 要件については、DS シリーズと GS シリーズの VM で提供されている Azure Premium Storage を使用できます。

![SAP MaxDB DBMS 用の Azure IaaS VM の参照の構成][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>バックアップと復元
Azure にSAP MaxDB をデプロイする場合、バックアップ方法を確認する必要があります。 実稼働システムでない場合でも、SAP MaxDB でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害、重大な運用上またら管理上の障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 たとえば、バックアップを復元すると、同じシステムの 3 層システムのセットアップを 2 層 SAP 構成から転送できます。

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。そのため、SAP Note [767598] に記載されている SAP MaxDB ドキュメントのいずれかで説明した標準の SAP MaxDB バックアップ/復元ツールを使用できます。 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップのパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を担います。 そのため、次のことを想定できます。

* データ デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットが低下する。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲット数を増やすには、ニーズに応じて使用または組み合わせることのできるオプションが 2 つあります。

* 専用の個別のボリュームのバックアップ
* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたディスク ボリュームの IOPS スループットを向上させる
* 別の専用の論理ディスクのデバイスを持たせる
  * SAP MaxDB バックアップ ボリューム (ファイル)
  * SAP MaxDB データ ボリューム (ファイル)
  * SAP MaxDB ログ ボリューム (ファイル)

ボリュームをマウントされた複数のディスクでストライピングする方法は、このドキュメントの前半の「[ソフトウェア RAID][dbms-guide-2.2]」の章で取り上げています。 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>その他
このドキュメントの最初の 3 つの章で説明したように、SAP MaxDB データベースを使用した VM のデプロイについては、Azure 可用性セットや SAP の監視などの他のすべての一般的な領域が適用されます。
その他の SAP MaxDB に固有の設定は Azure VM に透過的であり、SAP Note [767598] とこれらの SAP Note に挙げられたドキュメントに記載されています。

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Windows 上の SAP liveCache の詳細
### <a name="sap-livecache-version-support"></a>SAP liveCache バージョンのサポート
Azure Virtual Machines でサポートされている SAP liveCache の最小バージョンは **liveCache 7.9.08.31** と **LCA-Build 25** を含む **SAP LC/LCAPPS 10.0 SP 25** であり、**EhP 2 for SAP SCM 7.0** 以降向けにリリースされています。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS 向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP liveCache DBMS 向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

オペレーティング システム Microsoft Windows Server の最新バージョンを使用することをお勧めします。 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP liveCache 構成ガイドライン
#### <a name="recommended-azure-vm-types"></a>推奨される Azure VM の種類
SAP liveCache は膨大な計算を実行するアプリケーションであるため、RAM と CPU の量と速度よって、SAP liveCache のパフォーマンスんに大きな影響があります。 

SAP でサポートされている Azure VM の種類 (SAP Note [1928533]) については、VM に割り当てられたすべての仮想 CPU リソースが、ハイパーバイザーの専用物理 CPU リソースによってサポートされます。 オーバープロビジョニングが行われない (そのため CPU リソースの競合がない)

同様に、SAP でサポートされるすべての種類の Azure VM インスタンスにおいて、VM メモリは 100% 物理メモリにマッピングされます。たとえば、オーバープロビジョニング (オーバーコミット) は使用されません。

この観点から、新しい D シリーズまたは DS シリーズの Azure VM タイプを (Azure Premium Storage と組み合わせて) 使用することを強くお勧めします。これらは A シリーズよりもプロセッサが 60% 高速であるためです。 RAM と CPU の負荷が最高レベルの場合は、G シリーズと GS シリーズの VM を (Azure Premium Storage と組み合わせて) 最新の Intel  Xeon  プロセッサ E5 v3 ファミリに使用できます。この場合、D/DS シリーズの 2 倍のメモリと 4 倍のソリッド ステート ドライブ ストレージ (SSD) になります。

#### <a name="storage-configuration"></a>ストレージの構成
SAP liveCache は SAP MaxDB テクノロジをベースとしているため、「[ストレージの構成][dbms-guide-8.4.1]」の章で SAP MaxDB について説明したすべての Azure Storage のベスト プラクティスの推奨事項が SAP liveCache に対しても有効です。 

#### <a name="dedicated-azure-vm-for-livecache"></a>liveCache 専用の Azure VM
SAP liveCache は、計算能力を集中的に使用するため、実稼働で使用する場合は、専用の Azure Virtual Machine をデプロイすることを強くお勧めします。 

![実稼働ユース ケースでの liveCache 専用 Azure VM][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>バックアップと復元
パフォーマンスの考慮事項を含め、バックアップと復元については、「[バックアップと復元][dbms-guide-8.4.2]」の章と「[バックアップと復元のパフォーマンスに関する考慮事項][dbms-guide-8.4.3]」の章の関連する SAP MaxDB の部分で既に説明しています。 

#### <a name="other"></a>その他
他のすべての一般的な領域については、[この章][dbms-guide-8.4.4]の関連する SAP MaxDB の部分で既に説明しています。 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Windows 上の SAP コンテンツ サーバーの仕様
SAP コンテンツ サーバーは、さまざまな形式で電子ドキュメントなどのコンテンツを格納する独立した、サーバー ベースのコンポーネントです。 SAP コンテンツ サーバーはテクノロジの開発によって提供され、任意の SAP アプリケーション向けのクロス アプリケーションとして使用されます。 このサーバーは別のシステムにインストールされます。 一般的な内容は、トレーニング教材とナレッジ ウェアハウスまたは mySAP PLM ドキュメント管理システムから送信された技術図面からドキュメントです。 

### <a name="sap-content-server-version-support"></a>SAP コンテンツ サーバーのバージョンのサポート
SAP が現在サポートしているのは次のものです。

* **SAP コンテンツ サーバー** バージョン **6.50 (以降)**
* **SAP MaxDB バージョン 7.9**
* **Microsoft IIS (Internet Information Server) バージョン 8.0 (以降)**

最新バージョンの SAP コンテンツ サーバー (ドキュメント執筆時点では **6.50 SP4**) と最新のバージョンの **Microsoft IIS 8.5** を使用することを強くお勧めします。 

SAP コンテンツ サーバーと Microsoft IIS の最新バージョンを [SAP 製品の可用性マトリックス (PAM)][sap-pam] でご確認ください。

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP コンテンツ サーバー向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP コンテンツ サーバー向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

最新バージョンの Microsoft Windows Server を使用することをお勧めします。

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP コンテンツ サーバー構成ガイドライン
#### <a name="storage-configuration"></a>ストレージの構成
SAP MaxDB データベースのファイルを格納するように SAP コンテンツ サーバーを構成する場合、「[ストレージの構成][dbms-guide-8.4.1]」の章で SAP MaxDB について説明したすべての Azure Storage のベスト プラクティスの推奨事項が SAP コンテンツ サーバーに対しても有効です。 

ファイル システムのファイルを格納するように SAP コンテンツ サーバーを構成する場合、専用の論理ドライブを使用することをお勧めします。 Windows 記憶域スペースを使用することで、「[ソフトウェア RAID][dbms-guide-2.2]」の章で説明したように、論理ディスク サイズと IOPS スループットを向上させることができます。 

#### <a name="sap-content-server-location"></a>SAP コンテンツ サーバーの場所
SAP コンテンツ サーバーは、SAP システムがデプロイされたときと同じ Azure リージョンと Azure VNET にデプロイする必要があります。 専用の Azure VM または SAP システムが実行されているのと同じ VM に SAP コンテンツ サーバー コンポーネントを展開するかどうかを自由に決めることができます。 

![SAP コンテンツ サーバー専用 Azure VM][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP キャッシュ サーバーの場所
SAP キャッシュ サーバーは、(キャッシュされた) ドキュメントへのアクセスをローカルで提供する追加のサーバー ベース コンポーネントです。 SAP キャッシュ サーバーは、SAP コンテンツ サーバーのドキュメントをキャッシュします。 ドキュメントを複数の場所から取得する必要がある場合、この機能によってネットワーク トラフィックを最適化します。 一般的な規則では、SAP キャッシュ サーバーは SAP キャッシュ サーバーにアクセスするクライアントの物理的に近くに設置する必要があります。 

そのため、次の 2 つの選択肢があります。

1. **クライアントがバックエンド SAP システム** バックエンド SAP システムが SAP コンテンツ サーバーにアクセスするように構成されている場合、その SAP システムがクライアントです。 SAP システムと SAP コンテンツ サーバーの両方が、同じ Azure リージョン (同じ Azure データ センター) にデプロイされます。2 つは互いに物理的に近くに設置されます。 そのため、専用の SAP キャッシュ サーバーを用意する必要はありません。 SAP UI クライアント (SAP の GUI または Web ブラウザー) が SAP システムに直接アクセスし、SAP システムは SAP コンテンツ サーバーからドキュメントを取得します。
2. **クライアントがオンプレミス Web ブラウザー** SAP コンテンツ サーバーは、Web ブラウザーから直接アクセスするように構成できます。 この場合は、オンプレミスで実行されている Web ブラウザーが SAP コンテンツ サーバーのクライアントです。 オンプレミス データ センターと Azure データ センターは、物理的に別の場所 (理想的には、相互に近接) に配置されます。 オンプレミス データ センターは、Azure のサイト間 VPN または ExpressRoute を使用して Azure に接続されます。 どちらのオプションでも、Azure へのセキュリティで保護された VPN ネットワーク接続を提供しますが、サイト間のネットワーク接続はオンプレミス データ センターと Azure データ センター間のネットワーク帯域幅と待機時間の SLA を提供しません。 ドキュメントへのアクセスを高速化するために、次のいずれかの操作を行うことができます。
   1. オンプレミス Web ブラウザーに近いオンプレミスに SAP キャッシュ サーバーを設置する ([この図][dbms-guide-900-sap-cache-server-on-premises]のオプション)
   2. オンプレミス データ センターと Azure データ センター間の高速で低待機時間の専用のネットワーク接続を提供する Azure ExpressRoute を構成します。

![オンプレミス SAP キャッシュ サーバーをインストールするオプション][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>バックアップと復元
SAP MaxDB データベースのファイルを格納するように SAP コンテンツ サーバーを構成する場合、バックアップと復元の手順、およびパフォーマンスに関する考慮事項については、「[バックアップと復元][dbms-guide-8.4.2]」の章と「[バックアップと復元のパフォーマンスに関する考慮事項][dbms-guide-8.4.3]」の章の SAP MaxDB の部分で既に説明しています。 

ファイル システムのファイルを格納するように SAP コンテンツ サーバーを構成する場合、ドキュメントが置かれているファイル構造全体の手動バックアップ/復元を実行するという選択肢があります。 SAP MaxDB のバックアップ/復元と同様に、バックアップ専用のディスク ボリュームを用意することをお勧めします。 

#### <a name="other"></a>その他
その他の SAP コンテンツ サーバー固有の設定は Azure VM に透過的であり、さまざまなドキュメントと SAP Note に記載されています。

* <https://service.sap.com/contentserver> 
* SAP Note [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Windows の IBM DB2 for LUW の仕様
Microsoft Azure では、IBM DB2 for Linux, UNIX, and Windows (LUW) で実行される既存の SAP アプリケーションを Azure Virtual Machines に簡単に移行できます。 SAP on IBM DB2 for LUW では、管理者および開発者は同じ開発ツールや管理ツールをオンプレミスで引き続き使用できます。
IBM DB2 for LUW での SAP Business Suite の実行に関する一般的な情報については、SAP Community Network (SCN) (<https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>) をご覧ください。

Azure での SAP on DB2 for LUW に関するその他の情報および更新については、SAP Note [2233094]を参照してください。 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 for Linux, UNIX, and Windows バージョンのサポート
Microsoft Azure Virtual Machine サービスにおける SAP on IBM DB2 for LUW は、DB2 バージョン 10.5 の時点でサポートされています。

サポートされる SAP 製品と Azure VM の種類については、SAP Note [1928533] をご覧ください。

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための IBM DB2 for Linux, UNIX, and Windows 構成ガイドライン
#### <a name="storage-configuration"></a>ストレージの構成
すべてのデータベース ファイルは、直接接続されたディスクをベースとする NTFS ファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされており、Azure ページ Blob Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) または Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>) に基づいています。 あらゆる種類のネットワーク ドライブまたは Azure ファイル サービスのようなリモート共有は次のデータベース ファイルをサポートして **いません** 。 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Azure ページ BLOB Storage または Managed Disks をベースとするディスクを使用する場合、このドキュメントの「[RDBMS デプロイの構造][dbms-guide-2]」の章の説明が、IBM DB2 for LUW データベースでのデプロイにも適用されます。 

このドキュメント前半の大部分で説明したように、ディスクの IOPS スループットにクォータが存在します。 正確なクォータは使用する VM タイプによって異なります。 VM タイプとそのクォータの一覧は、[こちら (Linux)][virtual-machines-sizes-linux] および[こちら (Windows)][virtual-machines-sizes-windows] に記載されています。

ディスクあたりの現在の IOPS クォータが十分である限り、マウントされた単一のディスクにすべてのデータベース ファイルを格納できます。 

パフォーマンスに関する考慮事項についても、SAP インストール ガイドの「データベース ディレクトリのデータの安全性とパフォーマンスに関する考慮事項」の章を参照してください。

または、このドキュメントの「[ソフトウェア RAID][dbms-guide-2.2]」の章に記載されているように、Windows 記憶域プール (Windows Server 2012 以降でのみ提供) または Windows 2008 R2 の Windows ストライピングを使用して、複数のディスクの上に 1 つの大きな論理デバイスを作成できます。
Sapdata と saptmp ディレクトリに対する DB2 ストレージ パスを含むディスクについては、512 KB の物理ディスクのセクター サイズを指定する必要があります。 Windows 記憶域プールを使用する場合は、コマンド ライン インターフェイスで `-LogicalSectorSizeDefault` パラメーターを使用して、手動で記憶域プールを作成する必要があります。 詳細については、<https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool> を参照してください。

#### <a name="backuprestore"></a>バックアップ/復元
IBM DB2 for LUW のバックアップ/復元機能は、標準の Windows Server オペレーティング システムと Hyper-V と同じ方法でサポートされています。

有効なデータベース バックアップ戦略が設定されていることを確認する必要があります。 

ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を果たします。 そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲット数を増やすには、ニーズに応じて使用または組み合わせることのできるオプションが 2 つあります。

* 複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* バックアップの書き込み先として複数のターゲット ディレクトリを使用する

#### <a name="high-availability-and-disaster-recovery"></a>高可用性と障害復旧
Microsoft Cluster Server (MSCS) はサポートされていません。

DB2 の高可用性とディザスター リカバリー (HADR) がサポートされています。 HA 構成の仮想マシンで名前解決が機能している場合、Azure でのセットアップはオンプレミスで行われるセットアップとまったく変わりません。 IP 解決のみに依存することはお勧めしません。

データベース ディスクを格納するストレージ アカウントでは、geo レプリケーションを使用しないでください。 詳しくは、「[Microsoft Azure Storage][dbms-guide-2.3]」の章と「[Azure VM の高可用性とディザスター リカバリー][dbms-guide-3]」の章をご覧ください。

#### <a name="other"></a>その他
このドキュメントの最初の 3 つの章で説明したように、IBM DB2 for LUW を使用した VM のデプロイについては、Azure 可用性セットや SAP の監視などの他のすべての一般的な領域が適用されます。 

「[Azure での一般的な SAP 用 SQL Server の概要][dbms-guide-5.8]」の章をご覧ください。

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Linux の IBM DB2 for LUW の詳細
Microsoft Azure では、IBM DB2 for Linux, UNIX, and Windows (LUW) で実行される既存の SAP アプリケーションを Azure Virtual Machines に簡単に移行できます。 SAP on IBM DB2 for LUW では、管理者および開発者は同じ開発ツールや管理ツールをオンプレミスで引き続き使用できます。 IBM DB2 for LUW での SAP Business Suite の実行に関する一般的な情報については、SAP Community Network (SCN) (<https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>) をご覧ください。

Azure での SAP on DB2 for LUW に関するその他の情報および更新については、SAP Note [2233094]を参照してください。

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 for Linux, UNIX, and Windows バージョンのサポート
Microsoft Azure Virtual Machine サービスにおける SAP on IBM DB2 for LUW は、DB2 バージョン 10.5 の時点でサポートされています。

サポートされる SAP 製品と Azure VM の種類については、SAP Note [1928533] をご覧ください。

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための IBM DB2 for Linux, UNIX, and Windows 構成ガイドライン
#### <a name="storage-configuration"></a>ストレージの構成
すべてのデータベース ファイルは、直接接続されたディスクをベースとするファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされており、Azure ページ Blob Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) または Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>) に基づいています。 あらゆる種類のネットワーク ドライブまたは Azure ファイル サービスのようなリモート共有は次のデータベース ファイルをサポートして **いません** 。

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Azure ページ BLOB Storage をベースとするディスクを使用する場合、このドキュメントの「[RDBMS デプロイの構造][dbms-guide-2]」の章の説明が、IBM DB2 for LUW データベースでのデプロイにも適用されます。

このドキュメント前半の大部分で説明したように、ディスクの IOPS スループットにクォータが存在します。 正確なクォータは使用する VM タイプによって異なります。 VM タイプとそのクォータの一覧は、[こちら (Linux)][virtual-machines-sizes-linux] および[こちら (Windows)][virtual-machines-sizes-windows] に記載されています。

ディスクあたりの現在の IOPS クォータが十分である限り、単一のディスクにすべてのデータベース ファイルを格納できます。

パフォーマンスに関する考慮事項についても、SAP インストール ガイドの「データベース ディレクトリのデータの安全性とパフォーマンスに関する考慮事項」の章を参照してください。

または、このドキュメントの「[ソフトウェア RAID][dbms-guide-2.2]」で説明した LVM (論理ボリューム マネージャー) または MDADM を使用して、複数のディスクの上に 1 つの大きな論理デバイスを作成することもできます。
Sapdata と saptmp ディレクトリに対する DB2 ストレージ パスを含むディスクについては、512 KB の物理ディスクのセクター サイズを指定する必要があります。

#### <a name="backuprestore"></a>バックアップ/復元
IBM DB2 for LUW のバックアップ/復元機能は、オンプレミスの標準の Linux インストールの場合と同様にサポートされています。

有効なデータベース バックアップ戦略が設定されていることを確認する必要があります。

ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 さらに、バックアップの圧縮で使用される CPU 使用量は最大 8 CPU スレッドとなり、VM 上で重要な役割を果たします。 そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲット数を増やすには、ニーズに応じて使用または組み合わせることのできるオプションが 2 つあります。

* 複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* バックアップの書き込み先として複数のターゲット ディレクトリを使用する

#### <a name="high-availability-and-disaster-recovery"></a>高可用性と障害復旧
DB2 の高可用性とディザスター リカバリー (HADR) がサポートされています。 HA 構成の仮想マシンで名前解決が機能している場合、Azure でのセットアップはオンプレミスで行われるセットアップとまったく変わりません。 IP 解決のみに依存することはお勧めしません。

データベース ディスクを格納するストレージ アカウントでは、geo レプリケーションを使用しないでください。 詳しくは、「[Microsoft Azure Storage][dbms-guide-2.3]」の章と「[Azure VM の高可用性とディザスター リカバリー][dbms-guide-3]」の章をご覧ください。

#### <a name="other"></a>その他
このドキュメントの最初の 3 つの章で説明したように、IBM DB2 for LUW を使用した VM のデプロイについては Azure 可用性セットまたは SAP の監視などその他のすべての一般的なトピックが適用されます。

「[Azure での一般的な SAP 用 SQL Server の概要][dbms-guide-5.8]」の章をご覧ください。

