---
title: "Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver | Microsoft Docs"
description: "Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7eb4f6c8c7ddfe0cb0d8a37e27d4e697e760107a
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
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
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure permite às empresas obter recursos de computação e armazenamento no tempo mínimo sem ciclos de aprovisionamento demorado. Máquinas virtuais do Azure permitem às empresas implementar classical aplicações, como o SAP NetWeaver com base em aplicações no Azure e expandir os respetivos fiabilidade e disponibilidade sem ter ainda mais recursos disponíveis no local. Serviços de Máquina Virtual do Azure também suporta a conectividade entre instalações, que permite às empresas ativamente integrar Virtual Machines do Azure para os seus domínios no local, as respetivas nuvens privadas e as respetivas horizontal de sistema do SAP.
Este documento técnico descreve as noções básicas do Microsoft Azure Virtual Machine e fornece instruções de considerações de planeamento e implementação para as instalações do SAP NetWeaver no Azure e como tal, deve ser o documento ler antes de iniciar as implementações reais do SAP NetWeaver no Azure.
O documento complementa a documentação de instalação de SAP e notas de SAP, que representam os recursos para as instalações e implementações de software do SAP primários em determinadas plataformas.

## <a name="summary"></a>Resumo
É um termo bastante utilizado, o que é obtenham mais importância dentro da indústria IT, de pequenas empresas até as empresas têm grandes e multinational a informática em nuvem.

Microsoft Azure é plataforma de serviços em nuvem da Microsoft, que oferece um largo espetro de possibilidades de novo. Agora os clientes conseguem rapidamente aprovisionar e aprovisionar desativação aplicações como um serviço na nuvem, para que não estejam limitadas a técnicas ou budgeting restrições. Em vez de investir tempo e orçamento na infraestrutura de hardware, as empresas podem concentrar-se a aplicação, os processos de negócios e suas vantagens para os clientes e utilizadores.

Com os Serviços da Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma completa de Infraestrutura como um Serviço (IaaS). As aplicações baseadas em SAP NetWeaver são suportadas em Máquinas Virtuais do Azure (IaaS). Este documento descreve como planear e implementar aplicações de NetWeaver SAP com base no Microsoft Azure como a plataforma de eleição.

O documento próprio centra-se em dois aspetos principais:

* A primeira parte descreve dois padrões de implementação suportado para aplicações de NetWeaver SAP com base no Azure. Também descreve processamento geral do Azure com as implementações de SAP em mente.
* Os detalhes da segundos parte implementar dois diferentes cenários descritos na primeira parte.

Para obter recursos adicionais, consulte capítulo [recursos] [ planning-guide-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições de compromisso
Ao longo do documento, vamos utilizar os seguintes termos:

* IaaS: Infraestrutura como serviço
* PaaS: Plataforma como serviço
* SaaS: Software como um serviço
* ARM: O Azure Resource Manager
* Componente SAP: uma individuais SAP aplicação tal como ECC, BW, Gestor de solução ou EP.  Componentes SAP podem ser baseados em tecnologias tradicionais ABAP ou Java ou uma aplicação não baseada em NetWeaver como objetos de negócio.
* Ambiente de SAP: um ou mais componentes do SAP logicamente agrupados para efetuar uma função de negócio, tais como o desenvolvimento, QAS, formação, DR ou de produção.
* SAP horizontal: Isto refere-se aos ativos SAP todos num cliente horizontal IT. O horizontal SAP inclui todos os ambientes de não produção e produção.
* Sistema SAP: A combinação de camada DBMS e a camada de aplicação de, por exemplo, um sistema de desenvolvimento do SAP ERP, sistema de teste de SAP BW, sistema de produção do SAP CRM, etc.. Nas implementações do Azure, não é suportado para dividir a estas duas camadas entre no local e o Azure. Isto significa que um sistema SAP é implementado no local ou está implementado no Azure. No entanto, pode implementar os sistemas diferentes de um horizontal SAP para o Azure ou no local. Por exemplo, pode implementar o desenvolvimento de SAP CRM e sistemas de teste no Azure, mas o SAP CRM produção sistema no local.
* Implementação apenas de nuvem: uma implementação em que a subscrição do Azure não está ligada através de um site para site ou a ligação do ExpressRoute para a infraestrutura de rede no local. Em comum documentação do Azure estes tipos de implementações também estão descritos como implementações "Apenas na Cloud". Máquinas virtuais implementadas com este método são acedidas através da internet e um endereço IP público e/ou um nome DNS público atribuído para as VMs no Azure. Para o Microsoft Windows local no Active Directory (AD) e DNS não estiver expandido para o Azure nestes tipos de implementações. Por conseguinte, as VMs não fazem parte do Active Directory no local. Mesmo se aplica a utilizar, por exemplo, OpenLDAP + Kerberos de implementações de Linux.

> [!NOTE]
> Implementação apenas de nuvem neste documento é definida como concluídas SAP landscapes estão em execução no modo exclusivo no Azure sem a extensão do Active Directory / OpenLDAP ou resolução de nomes no local na nuvem pública. Configurações de apenas na nuvem não são suportadas para sistemas de SAP de produção ou configurações de onde SAP STMS ou outros recursos no local têm de ser utilizada entre sistemas SAP alojados no Azure e recursos que residem no local.
>
>

* Em vários locais: Descreve um cenário onde as VMs implementadas para uma subscrição do Azure com o site para site, multilocal ou ExpressRoute conectividade entre o datacenter(s) no local e o Azure. Documentação do Azure em comum, estes tipos de implementações também estão descritos como cenários de vários locais. O motivo para a ligação está a expandir domínios no local/OpenLDAP de diretório Active Directory no local, DNS e no local no Azure. O horizontal no local é expandido para os recursos da subscrição do Azure. Com esta extensão, as VMs podem fazer parte do domínio no local. Utilizadores de domínio do domínio no local podem aceder aos servidores e podem executar serviços em dessas VMs (por exemplo, o DBMS serviços). É possível resolução do nome e a comunicação entre as VMs implementadas no local e as VMs implementadas do Azure. Este é o cenário que esperamos maioria dos recursos SAP para ser implementada numa. Para obter mais informações, consulte [isto] [ vpn-gateway-cross-premises-options] artigo e [isto][vpn-gateway-site-to-site-create].

> [!NOTE]
> Implementações de vários locais dos sistemas SAP onde com sistemas SAP de Virtual Machines do Azure são membros de um domínio no local são suportadas para sistemas de SAP de produção. Configurações em vários locais são suportadas para a implementação de partes ou concluir SAP landscapes no Azure. Mesmo em execução horizontal SAP completa no Azure necessita de ter as VMs que está a ser parte do domínio no local e anúncios/OpenLDAP. Nas versões anteriores da documentação, abordámos cenários híbridos-IT, onde o termo *híbrida* tem Root no facto de que não há uma conectividade entre instalações, entre no local e o Azure. Plus, o facto de que as VMs no Azure fazem parte do Active Directory no local / OpenLDAP.
>
>

Alguma documentação do Microsoft descreve cenários de vários locais um pouco diferente, especialmente para configurações DBMS HA. No caso de documentos relacionados com o SAP, o cenário entre locais apenas boils para baixo para ter um site para site ou conectividade privada (ExpressRoute) e o facto de que o horizontal SAP é distribuído entre no local e o Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
Os guias seguintes adicionais estão disponíveis para o tópico de implementações de SAP no Azure:

* [Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver (deste documento)][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]

> [!IMPORTANT]
> Onde quer que é utilizado possíveis uma hiperligação para o guia de instalação do SAP referente (referência InstGuide-01, consulte <http://service.sap.com/instguides>). Quando se trata os pré-requisitos e o processo de instalação, os guias de instalação do SAP NetWeaver deve sempre ser lidos cuidadosamente, enquanto este documento abrange apenas a tarefas específicas para sistemas de SAP NetWeaver instalado no Microsoft Azure Máquina Virtual.
>
>

As seguintes notas de SAP estão relacionadas com o tópico do SAP no Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP no Azure: os produtos e dimensionamento suportados |
| [2015553] |SAP no Microsoft Azure: suporta a pré-requisitos |
| [1999351] |Resolução de problemas avançada monitorização do Azure para SAP |
| [2178632] |Chave de monitorização métricas para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: avançada de monitorização |
| [2191498] |SAP no Linux com o Azure: avançada de monitorização |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7. x: instalação e a atualização |
| [2069760] |Oracle Linux 7. x SAP instalação e a atualização |
| [1597355] |Recomendação de espaço de comutação para Linux |

Leia também o [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas de SAP para o Linux.

Limitações gerais predefinido e limitações máximas de subscrições do Azure podem ser encontradas na [neste artigo][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Cenários possíveis
SAP, muitas vezes, é apresentado como uma das aplicações mais fundamentais em empresas. A arquitetura e as operações destas aplicações principalmente é muito complexa e assegurar que cumpre os requisitos de desempenho e disponibilidade é importante.

Assim que as empresas têm de refletir cuidadosamente sobre os quais as aplicações podem ser executadas num ambiente de nuvem pública, independentemente do fornecedor de nuvem que escolheu.

Tipos de sistema possíveis para a implementação SAP NetWeaver com base em aplicações na nuvem pública ambientes estão listados abaixo:

1. Sistemas de produção média
2. Sistemas de desenvolvimento
3. Sistemas de teste
4. Sistemas de protótipo
5. Learning / sistemas de demonstração

Para poder implementar com êxito o SAP sistemas no IaaS do Azure ou IaaS em geral, é importante compreender as diferenças significativas entre as ofertas de outsourcers tradicionais ou de que os fornecedores e ofertas de IaaS. Enquanto o fornecedor de alojamento tradicional ou outsourcer feita infraestrutura (tipo de rede, armazenamento e servidor) para um cliente que pretende alojar a carga de trabalho, em vez disso, é responsabilidade do cliente para escolher a carga de trabalho adequada para implementações IaaS.

Como primeiro passo, o que os clientes precisam verificar os seguintes itens:

* O SAP suportados tipos VM do Azure
* O SAP suportado produtos/versões no Azure
* O DBMS e SO suportados versões para as versões do SAP específicas no Azure
* Débito SAPS fornecido pelo SKUs diferentes do Azure

As respostas a estas questões podem ser lidos na nota SAP [1928533].

Como segundo passo, limitações de largura de banda e de recursos do Azure tem de ser comparadas para consumo de recursos real dos sistemas no local. Por conseguinte, os clientes precisam de estar familiarizado com as diferentes capacidades dos tipos de Azure suportados com SAP na área de:

* Recursos de CPU e memória de diferentes tipos VM e
* Largura de banda IOPS de diferentes tipos VM e
* Capacidades de rede de diferentes tipos VM.

A maioria dos dados pode ser encontrada [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Tenha em atenção que os limites indicados na ligação acima são os limites superiores. Não significa que os limites para qualquer um dos recursos, por exemplo IOPS podem ser fornecido em todas as circunstâncias. As exceções são os recursos de CPU e memória de um tipo VM que escolheu. Para os tipos VM suportados pelo SAP, os recursos de CPU e memória estão reservados e como tal, disponível em qualquer ponto no tempo para consumo dentro da VM.

A plataforma Microsoft Azure como outras plataformas de IaaS é uma plataforma de multi-inquilino. Isto significa que o armazenamento, rede e outros recursos são partilhados entre inquilinos. Lógica de limitação e quota inteligente é utilizada para impedir que um inquilino afetar o desempenho de outro inquilino (vizinho inúteis) de uma forma drastic. Embora lógica no Azure tenta manter variações como na largura de banda teve plataformas pequenas e altamente partilhadas tendem a introduzir maior variações de como na disponibilidade de recursos/largura de banda do que muitos clientes são utilizados para as respetivas implementações no local. Como resultado, podem ocorrer diferentes níveis de largura de banda relativas à rede ou armazenamento e/s (o volume, bem como latência) de um minuto para minuto. A probabilidade de que um sistema SAP no Azure foi experiência variações como maior do que um sistema local tem de ser levados em consideração.

Um último passo consiste em avaliar os requisitos de disponibilidade. Pode acontecer, que a infraestrutura do Azure subjacente tem de obter atualizado e requer que os anfitriões a executar as VMs de ser reiniciado. Nestes casos, as VMs em execução nesses anfitriões seriam possível desligadas e reiniciadas bem. A temporização de tal manutenção é efetuada durante o horário comercial de não-core de uma determinada região, mas a janela de potencial de algumas horas durante o qual um computador será reiniciado é relativamente grande. Existem várias tecnologias dentro da plataforma Azure que pode ser configurado para mitigar alguns ou todos os o impacto dessas atualizações. Melhoramentos futuros da plataforma do Azure, DBMS e SAP aplicação foram concebidos para minimizar o impacto dessas reinícios.

Para poder implementar com êxito um sistema SAP para o Azure, o local SAP sistemas sistema operativo, a base de dados e tem de aparecer na matriz de suporte do Azure de SAP, aplicações SAP cabem dentro os recursos do Azure pode fornecer a infraestrutura e que possa trabalhar com ofertas disponibilidade SLAs Microsoft Azure. Como esses sistemas são identificados, terá de decidir um dos seguintes cenários de implementação de dois.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Apenas na nuvem - implementações de Máquina Virtual no Azure sem dependências na rede de cliente no local
![VM único com o cenário de formação implementado no Azure ou de demonstração SAP][planning-guide-figure-100]

Este cenário é comum para trainings ou sistemas de demonstração, onde todos os componentes de SAP e o software de SAP não estão instalados dentro de uma única VM. Sistemas de SAP de produção não são suportados neste cenário de implementação. Em geral, este cenário cumpre os seguintes requisitos:

* As VMs estão acessíveis através da rede pública. Conectividade de rede diretamente para as aplicações com VMS para a rede no local da empresa ou o proprietário do conteúdo demonstrações ou trainings ou o cliente não é necessária.
* Em caso de várias VMs que representa a trainings ou cenário de demonstração, resolução do nome e de comunicações de rede tem de funcionar entre as VMs. Mas as comunicações entre o conjunto de VMs tem de estar isolado, para que vários conjuntos de VMs podem ser implementados lado sem interferências.  
* A conectividade à Internet é necessária para o utilizador final para início de sessão remoto às VMs alojado no Azure. Consoante o convidado SO, serviços de Terminal/RDS ou VNC/ssh é utilizado para aceder a VM para satisfazer as tarefas de preparação ou efetuar as demonstrações. Se o SAP portas, tais como 3200, 3300 & 3600 podem também ser expostos a instância da aplicação SAP pode ser acedida a partir de qualquer ambiente de trabalho ligada à Internet.
* Os sistemas SAP (e VM(s)) representam um cenário de autónomo no Azure, que apenas requer acesso à internet pública de acesso de utilizador final e não necessitam de uma ligação para as outras VMs do Azure.
* Um browser e SAPGUI estarem instalados e executar diretamente na VM.
* Não são necessárias uma reposição rápida de uma VM para o estado original e a nova implementação desse estado original novamente.
* No caso de cenários de preparação e de demonstração, que são realizadas em várias VMs, um Active Directory / serviço OpenLDAP e/ou de DNS é necessário para cada conjunto de VMs.

![Grupo de VMS que representa uma demonstração ou cenário de formação num serviço em nuvem do Azure][planning-guide-figure-200]

É importante lembrar que a VM em cada um dos conjuntos têm de ser implementado em paralelo, onde os nomes VM em cada conjunto de são os mesmos.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Em vários locais - implementação única ou várias VMs SAP no Azure com o requisito de que está a ser totalmente integrado à rede no local
![VPN com conectividade de Site para Site (em vários locais)][planning-guide-figure-300]

Este cenário é um cenário de vários locais com muitos padrões de implementação possível. Pode ser descrito simplesmente como horizontal executar algumas partes do SAP no local e outras partes do SAP horizontal no Azure. Todos os aspetos do facto que parte dos componentes SAP estão em execução no Azure devem estar transparentes para os utilizadores finais. Por conseguinte, o sistema de correção de transporte de SAP (STMS), comunicação de RFC, impressão, segurança (por exemplo, SSO), etc. funciona sem problemas para os sistemas SAP em execução no Azure. Mas o cenário de vários locais também descreve um cenário onde o horizontal SAP completa é executada no Azure com o domínio do cliente e o DNS expandido no Azure.

> [!NOTE]
> Este é o cenário de implementação, o que é suportado para a execução de sistemas SAP produtivos.
>
>

Leitura [neste artigo] [ vpn-gateway-create-site-to-site-rm-powershell] para obter mais informações sobre como ligar a sua rede no local para o Microsoft Azure

> [!IMPORTANT]
> Quando é se fala sobre cenários de vários locais entre implementações de cliente do Azure e no local, podemos procura granularidade dos sistemas SAP todo. Cenários que são *não suportado* para vários locais cenários são:
>
> * Executar diferentes camadas de aplicações SAP em métodos de implementação diferentes. Por exemplo a executar o DBMS camada no local, mas a camada de aplicação do SAP em VMs implementadas como VMs do Azure ou vice-versa.
> * Alguns componentes de uma camada SAP no Azure e alguns no local. Por exemplo dividir instâncias da camada de aplicação SAP no local e as VMs do Azure.
> * Não é suportada a distribuição de VMs instâncias em execução SAP de um sistema através de várias regiões do Azure.
>
> O motivo para estas restrições é o requisito para uma rede de elevado desempenho muito baixa latência dentro de um sistema SAP, especialmente entre instâncias da aplicação e a camada DBMS de um sistema SAP.
>
>

### <a name="supported-os-and-database-releases"></a>SO e versões de base de dados suportados
* Software de servidor de Microsoft suportado para serviços de Máquina Virtual do Azure está listado neste artigo: <http://support.microsoft.com/kb/2721672>.
* Versões do sistema da base de dados suportadas nos serviços de Máquina Virtual do Azure em conjunto com o software SAP suportadas versões do sistema de operativo, estão documentadas na nota SAP [1928533].
* Aplicações SAP e versões suportadas nos serviços de Máquina Virtual do Azure estão documentadas na nota SAP [1928533].
* Imagens de 64 bits só são suportadas para ser executado como convidado VMs no Azure para cenários SAP. Isto também significa que apenas aplicações de SAP de 64 bits e as bases de dados são suportados.

## <a name="microsoft-azure-virtual-machine-services"></a>Serviços de Máquina Virtual do Microsoft Azure
A plataforma Microsoft Azure é uma plataforma de serviços internet escala na nuvem alojada e que operava nos centros de dados do Microsoft. A plataforma inclui os serviços de Máquina Virtual do Microsoft Azure (infraestrutura como um serviço ou IaaS) e um conjunto de plataforma avançado, como um capacidades de serviço (PaaS).

A plataforma do Azure reduz a necessidade de tecnologia prévio e comprar de infraestrutura. Simplifica a manutenção e a funcionar de aplicações, fornecendo a pedido computação e armazenamento para alojar, dimensionar e gerir a aplicação web e aplicações ligadas. Gestão de infraestrutura é automatizada com uma plataforma que foi concebida para elevada disponibilidade e dimensionamento para corresponder às necessidades de utilização com a opção de um modelo de preços pay as you go dinâmico.

![Posicionamento dos serviços de Máquina Virtual do Microsoft Azure][planning-guide-figure-400]

Com os serviços de Máquina Virtual do Azure, Microsoft está a ativar a implementação de imagens de servidor personalizado para o Azure como instâncias de IaaS (consulte a figura 4). As máquinas virtuais no Azure se baseiam em unidades de disco rígido virtuais (VHD) de Hyper-V e podem executar sistemas operativos diferentes como o SO convidado.

Perspetiva de um operacional, o serviço de Máquina Virtual do Azure oferece experiências semelhantes como máquinas virtuais implementadas no local. No entanto, tem a vantagem significativa que não precisa de obter, administrar e gerir a infraestrutura. Os programadores e os administradores têm controlo total da imagem do sistema operativo, das máquinas virtuais. Os administradores podem iniciar sessão remotamente essas máquinas virtuais para efetuar a manutenção e a resolução de problemas de tarefas, bem como as tarefas de implementação de software. In regard to implementação, as restrições só são os tamanhos e capacidades das VMs do Azure. Estes podem não ser bem como granulares numa configuração de como isto foi feito no local. Há uma opção de tipos VM que representam uma combinação de:

* Número de vCPUs,
* Memória,
* Número de VHDs que podem ser anexados
* Larguras de banda de rede e armazenamento.

O tamanho e limitações de vários tamanhos de diferentes máquinas virtuais oferecidas podem ser vistos numa tabela no [neste artigo (Linux)] [ virtual-machines-sizes-linux] e [neste artigo (Windows)] [virtual-machines-sizes-windows].

Como tenha em consideração, existem diferentes famílias ou série de máquinas virtuais. Pode distinguir as famílias de VMs seguintes:

* Tipos de a0 A7 VM: não todas essas são certificadas para SAP. Primeiro série VM que foi introduzida IaaS do Azure com.
* Tipos de a8-A11 VM: as instâncias de computação de alto desempenho. Em execução em efetuar uma melhor diferentes anfitriões que outras VMs de uma série de computação.
* Tipos de VM de série D/Dv2: melhor efetuar a A0 A7. Nem todos os tipos de VM são certificados com SAP.
* Tipos de VM de série DS/série DSv2: semelhante à série D/Dv2, mas tem de conseguir estabelecer ligação à Premium Storage do Azure (consulte o capítulo [Premium Storage do Azure] [ planning-guide-3.3.2] deste documento). Novo nem todos os tipos VM são certificados com SAP.
* Tipos de VM de série de G: tipos VM de elevada da memória.
* Tipos de VM de série GS: como a série de G, mas, incluindo a opção para utilizar o Premium Storage do Azure (consulte o capítulo [Premium Storage do Azure] [ planning-guide-3.3.2] deste documento). Ao utilizar VMs de série GS como servidores de base de dados, é obrigatório para utilizar o armazenamento Premium para ficheiros de registo de dados e a transação de base de dados

Pode encontrar a mesma CPU e configurações da memória na série VM diferente. Contudo, ao procurar o desempenho de débito destas VMs fora da série diferente poderá diferem significativamente. Apesar de ter a mesma configuração de CPU e memória. Razão é que o hardware de servidor de anfitrião subjacente na introdução de diferentes tipos VM tinha características de débito diferentes.  Normalmente, a diferença mostrada no desempenho de débito também é refletida no preço do diferentes VMs.

Nem todas as séries VM diferentes poderão ser-lhe oferecidas em cada uma das regiões do Azure (para regiões do Azure Consulte seguinte capítulo). Lembre-se também de que nem todas as VMs ou série de VM é certificada para SAP.

> [!IMPORTANT]
> A utilização de aplicações de NetWeaver SAP com base, apenas o subconjunto dos tipos VM e configurações listados na nota SAP [1928533] são suportados.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões do Azure
Microsoft permite implementar máquinas virtuais para, por isso, denominado *regiões do Azure*. Uma região do Azure pode ser um ou vários centros de dados que se encontrem próximos. Para a maior parte das regiões geopolíticas do mundo, a Microsoft tem, pelo menos, duas regiões do Azure. Por exemplo, na Europa há uma região do Azure de *Europa do Norte* e um dos *Europa Ocidental*. Estas duas regiões do Azure numa região geopolítica, separadas por distância significativa suficiente para que perante desastres naturais ou técnicas não afetam ambas as regiões do Azure na mesma região geopolítica. Uma vez que a Microsoft está aumentar firmemente a criar os novos regiões do Azure em diferentes regiões geopolíticas global, o número destas regiões aumentar firmemente está a crescer e a partir de 2015 Dec atingido o número de 20 regiões do Azure com regiões adicionais anunciadas já. Como um cliente pode implementar sistemas SAP para todas as estas regiões, incluindo as duas regiões do Azure na China. Para obter informações atualizadas atuais sobre regiões do Azure, consulte este Web site: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>O conceito de Máquina Virtual do Microsoft Azure
O Microsoft Azure oferece uma infraestrutura como uma solução de serviço (IaaS) para alojar máquinas virtuais com funcionalidades semelhantes como uma solução de virtualização no local. Conseguir criar máquinas virtuais a partir de dentro do portal do Azure, PowerShell ou o CLI, que também oferecem capacidades de gestão e implementação.

O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Utilize o mesmo modelo para implementar repetidamente a aplicação durante cada fase do ciclo de vida de aplicação.

Obter mais informações sobre como utilizar os modelos do Resource Manager podem ser encontradas aqui:

* [Implementar e gerir máquinas virtuais utilizando modelos Azure Resource Manager e a CLI do Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Gerir máquinas virtuais utilizando o Azure Resource Manager e o PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://Azure.microsoft.com/Documentation/Templates/>

Outra funcionalidade interessante é a capacidade para criar imagens de máquinas virtuais, que lhe permite preparar determinados repositórios a partir da qual conseguir implementar rapidamente a instâncias de Máquina Virtual que satisfaçam os requisitos.

Podem encontrar mais informações sobre como criar imagens de máquinas virtuais no [neste artigo (Linux)] [ virtual-machines-linux-capture-image-resource-manager] e [neste artigo (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de falhas
Domínios de falhas representam uma unidade física de falha, muito estritamente relacionada para a infraestrutura física contida nos centros de dados e um painel físico ou um bastidor pode ser considerado um domínio de falhas, não existe nenhum mapeamento um para um direto entre os dois.

Quando implementar várias máquinas virtuais como parte de um sistema SAP nos serviços de Máquina Virtual do Microsoft Azure, pode influenciar o controlador de recursos de infraestrutura do Azure para implementar a aplicação em diferentes domínios de falhas, aumentando assim que cumprem os requisitos do SLA do Microsoft Azure. No entanto, a distribuição de domínios de falhas através de uma unidade de escala do Azure (coleção de centenas de nós de computação ou nós de armazenamento e redes) ou a atribuição de VMs a um domínio de falhas específico é algo durante o qual não tem controlo direto. Para direcionar o controlador de recursos de infraestrutura do Azure para implementar um conjunto de VMs diferentes domínios de falhas, terá de atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implementação. Para obter mais informações sobre conjuntos de disponibilidade do Azure, consulte o capítulo [conjuntos de disponibilidade do Azure] [ planning-guide-3.2.3] neste documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domínios de atualização
Domínios de atualização representam uma unidade lógica que ajudam a determinar como uma VM dentro de um sistema SAP, que consiste em instâncias SAP em execução em várias VMs, é atualizada. Quando ocorre uma atualização, o Microsoft Azure realiza o processo de atualização destes domínios de atualização de um de cada. Pelo VMs no momento da implementação através de domínios de atualização diferentes, pode proteger o sistema SAP parcialmente de potenciais períodos de inatividade. Para forçar a Azure para implementar as VMs de um sistema SAP distribuídas por domínios de atualização diferentes, tem de definir um atributo específico no momento da implementação de cada VM. Domínios de falhas semelhante, uma unidade de escala do Azure está dividida em vários domínios de atualização. Para direcionar o controlador de recursos de infraestrutura do Azure para implementar um conjunto de VMs diferentes domínios de atualização, terá de atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implementação. Para obter mais informações sobre conjuntos de disponibilidade do Azure, consulte o capítulo [conjuntos de disponibilidade do Azure] [ planning-guide-3.2.3] abaixo.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade do Azure
Máquinas virtuais do Azure dentro de um conjunto de disponibilidade de Azure são distribuídas por controlador de recursos de infraestrutura do Azure através de diferentes falhas e de domínios de atualização. O objetivo da distribuição através de diferentes falhas e de domínios de atualização é impedir que todas as VMs de um sistema SAP a ser encerrado no caso de manutenção da infraestrutura ou de uma falha de dentro de um domínio de falhas. Por predefinição, as VMs não fazem parte de um conjunto de disponibilidade. A participação de uma VM num conjunto de disponibilidade está definida no momento da implementação ou mais tarde por uma reconfiguração e a implementação de uma VM.

Para compreender o conceito de conjuntos de disponibilidade do Azure e a forma como os conjuntos de disponibilidade relacionadas com falhas e domínios de atualização, leia [neste artigo][virtual-machines-manage-availability]

Para definir os conjuntos de disponibilidade para ARM através de um modelo json consulte [as especificações de api de rest](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e procure "availability".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento do Microsoft Azure e os discos de dados
Máquinas virtuais do Microsoft Azure utilizar tipos de armazenamento diferente. Quando implementar o SAP nos serviços de Máquina Virtual do Azure, é importante compreender as diferenças entre estes dois principais tipos de armazenamento:

* Armazenamento voláteis não persistente.
* Armazenamento persistente.

O armazenamento não persistentes é ligado diretamente às máquinas virtuais em execução e reside em nós de computação, o armazenamento de instância local (armazenamento temporário). O tamanho depende do tamanho da Máquina Virtual escolhido quando a implementação iniciada. Este tipo de armazenamento é volátil e, por conseguinte, o disco é inicializado quando uma instância de Máquina Virtual for reiniciada. Normalmente, o ficheiro de paginação para o sistema operativo está localizado neste disco temporário.

- - -
> ![Windows][Logo_Windows] Windows
>
> Em VMs do Windows, a unidade temporária está montada como unidade D:\ numa VM implementada.
>
> ![Linux][Logo_Linux] Linux
>
> Em VMs do Linux, está montado como /mnt/resource ou /mnt. Consulte mais detalhes aqui:
>
> * [Como anexar um disco de dados para uma Máquina Virtual Linux][virtual-machines-linux-how-to-attach-disk]
> * <https://Docs.microsoft.com/Azure/Storage/Storage-About-Disks-and-VHDs-Linux#Temporary-Disk>
>
>

- - -
A unidade real é volátil porque esta está a obter armazenada no próprio servidor do anfitrião. Se a VM moveu numa reimplementação (por exemplo, devido a manutenção no anfitrião ou encerrar e reiniciar) o conteúdo da unidade é perdido. Por conseguinte, não é uma opção para armazenar quaisquer dados importantes nesta unidade. O tipo de suporte de dados utilizado para este tipo de armazenamento difere entre série VM diferente com as características de desempenho muito diferentes que, a partir de Junho de 2015, ter o seguinte aspeto:

* A5-A7: Muito limitado de desempenho. Não é recomendado para nada para além do ficheiro de paginação
* A8-A11: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo.
* Série de D: Características de desempenho muito bom com algumas, em seguida, milhares de IOPS e > débito de 1GB por segundo.
* Série DS: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo.
* Série de G: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo.
* Série GS: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo.

A aplicar aos tipos VM que são certificados com SAP declarações acima. A série de VM com excelente IOPS e débito se qualificam para tire partido por algumas funcionalidades DBMS. Para obter mais informações, consulte o [guia de implementação DBMS][dbms-guide].

Armazenamento do Microsoft Azure fornece armazenamento persistente e típicos níveis de proteção e redundância visto no armazenamento SAN. Os discos com base no armazenamento do Azure estão disco rígido virtual (VHD) localizado nos serviços de armazenamento do Azure. O disco de SO local (c Windows\, Linux/dev/sda1) é armazenado no armazenamento do Azure, e adicionais Volumes/discos montados para a VM obter armazenados, demasiado.

É possível carregar um VHD existente no local ou criar campanhas vazias no Azure e ligar as VMS implementadas.

Depois de criar ou carregar um VHD para o armazenamento do Azure, é possível montar e anexe-las para uma Máquina Virtual existente e copie o VHD (desmontado) existente.

Como os VHDs são mantidos, dados e alterações naqueles são seguras quando a ser reiniciado e recriar uma instância de Máquina Virtual. Mesmo que uma instância é eliminada, estes VHDs permanecem seguros e podem voltar a implementar ou em caso de discos de SO não podem ser montados para as outras VMs.

Dentro da rede de armazenamento do Azure podem ser configurados níveis diferentes de redundância:

* Nível mínimo que pode ser seleccionado é *redundância local*, que é equivalente a três-réplica dos dados no mesmo centro de dados de uma região do Azure (consulte o capítulo [regiões do Azure] [ planning-guide-3.1]).
* Os centros de armazenamento com redundância de zona, o que se propaga as imagens de três através de dados diferentes na mesma região do Azure.
* Nível de redundância de predefinido é a redundância geográfica, o que replica, de forma assíncrona, o conteúdo da outra imagens três dos dados para outra região do Azure, que está alojado na mesma região geopolítica.

Consulte também a tabela em cima deste artigo sobre as opções de redundância diferentes: <https://azure.microsoft.com/pricing/details/storage/>

Obter mais informações sobre o Storage do Azure podem ser encontradas aqui:

* <https://Azure.microsoft.com/Documentation/Services/Storage/>
* <https://Azure.microsoft.com/Services/site-Recovery>
* <https://Docs.microsoft.com/REST/API/storageservices/Understanding-Block-BLOBs--Append-BLOBs--and-Page-BLOBs>
* <https://blogs.msdn.com/b/azuresecurity/Archive/2015/11/17/Azure-Disk-Encryption-for-Linux-and-Windows-virtual-Machines-Public-Preview.aspx>

#### <a name="azure-standard-storage"></a>Armazenamento padrão do Azure
Armazenamento padrão do Azure era do tipo de armazenamento disponível quando foi lançada IaaS do Azure. Ocorreram quotas IOPS impostas por um único disco. Latência teve não estava na mesma classe como dispositivos SAN/NAS normalmente implementados para sistemas SAP gama alta de classe alojada no local. Contudo, o armazenamento padrão do Azure foi tentativa suficiente para centenas de muitos sistemas SAP entretanto implementados no Azure.

Os discos que estão armazenados em contas de armazenamento padrão do Azure são-lhe cobrados com base nos dados reais são armazenados, o volume de transações de armazenamento, transferências de dados de saída e opção de redundância escolhido. Muitos discos podem ser criados no máximo 1TB de tamanho, mas, desde que os permanecerem vazios há sem qualquer encargo. Se, em seguida, de preencher um VHD com 100GB, são-lhe cobrados para 100GB de armazenamento e não para o tamanho nominal que o VHD foi criado com.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento Premium do Azure
Abril de 2015, a Microsoft introduziu Premium Storage do Azure. Armazenamento Premium obteve introduzido com o objetivo para fornecer:

* Latência de e/s melhor.
* Melhorar o débito.
* Menor a variabilidade de latência de e/s.

Para essa finalidade, foram introduzidas alterações muitos dos quais são os dois mais significativos:

* Utilização de discos SSD em nós de armazenamento do Azure
* Leitura de uma nova cache que é copiado por SSD local de um nó de computação do Azure

Opposite to onde não mudou capacidades de armazenamento Standard depende do tamanho do disco (ou VHD), Premium Storage atualmente tem três categorias de outro disco, que são apresentadas neste artigo: <https://azure.microsoft.com/pricing/ detalhes / / não geridos-discos de armazenamento />

Verá que o débito/disco IOPS de disco e de disco estão dependentes na categoria do tamanho dos discos

Custo base no caso de armazenamento Premium não é o volume de dados real armazenado nessas disks, mas a categoria de tamanho de tal um disco, independentemente da quantidade de dados que são armazenados no disco.

Também pode criar discos no armazenamento Premium que não são diretamente mapeamento para as categorias de tamanho mostradas. Isto poderá ser o caso, especialmente quando copiar discos de armazenamento Standard para armazenamento Premium. Nestes casos, é efetuado um mapeamento para a opção de disco de armazenamento Premium maior seguinte.

Lembre-se de que apenas determinado série VM pode beneficiar do Premium Storage do Azure. A partir de Dec de 2015, estes são o e GS-série DS. A série DS é basicamente o mesmo como série D com a exceção que-série DS tem a capacidade de armazenamento Premium montagem baseadas em VMs adicionalmente para discos que estão alojados no armazenamento padrão do Azure. Mesma coisa é válida para G-série em comparação comparada a série GS.

Se está a verificar enviados a parte das VMs-série DS no [neste artigo (Linux)] [ virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows], tenha em atenção que Existem limitações de volume de dados em discos de armazenamento Premium na granularidade do nível de VM. Série DS diferente ou série GS VMs também tem diferentes limitações que no que diz respeito ao número de discos de dados que podem ser montados. Estes limites estão documentados no artigo mencionado acima, bem como. Mas essencialmente significa que, se, por exemplo, montar 32 x P30 discos para uma única VM DS14 não pode obter 32 x o débito máximo de um disco de P30. Em vez disso, o débito máximo no nível VM, conforme documentado no artigo limita o débito de dados.

Obter mais informações sobre o armazenamento Premium podem ser encontradas aqui: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Discos geridos
Discos geridos são um novo tipo de recurso no Azure Resource Manager que pode ser utilizado em vez dos VHDs que são armazenados em contas do Storage do Azure. Discos geridos alinham automaticamente com o conjunto de disponibilidade da máquina virtual que estão ligados a e, por conseguinte, aumentam a disponibilidade de máquina virtual e os serviços que estão em execução na máquina virtual. Para obter mais informações, leia o [artigo de descrição geral](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Recomendamos que utilize disco gerida, porque estes simplificam a implementação e gestão das suas máquinas virtuais.
SAP só suporta atualmente os discos Premium geridos. Para obter mais informações, leia a nota SAP [1928533].

#### <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure
Ao implementar os serviços ou VMs no Azure, a implementação de VHDs e imagens de VM pode ser organizada em unidades denominadas contas do Storage do Azure. Quando planear uma implementação do Azure, terá de considerar atentamente as restrições do Azure. No lado, há um número limitado de contas do Storage por subscrição do Azure. Apesar de cada conta de armazenamento do Azure pode conter um grande número de ficheiros VHD, há um limite fixo no total de IOPS por conta de armazenamento. Quando implementar centenas de SAP VMs com sistemas DBMS significativas de e/s de chamadas de criação, recomenda-se ao distribuir o IOPS elevado DBMS VMs, entre várias contas do Storage do Azure. Deve ter cuidado para não exceder o limite atual de contas do Storage do Azure por subscrição. Porque o armazenamento é uma parte vital da implementação da base de dados para um sistema SAP, este conceito é abordado mais detalhadamente na já referenciado [guia de implementação DBMS][dbms-guide].

Podem encontrar mais informações sobre as contas de armazenamento do Azure no [neste artigo][storage-scalability-targets]. Ler este artigo, tenha em atenção que existem diferenças nas limitações das contas de armazenamento padrão do Azure e contas de armazenamento Premium. As principais diferenças são o volume de dados que podem ser armazenados na conta do Storage. No armazenamento Standard, o volume é um magnitude maior do que com o Premium Storage. No outro lado, a conta de armazenamento padrão é gravemente limitada IOPS (consulte a coluna **taxa de pedidos de Total**), enquanto que a conta de armazenamento do Azure Premium não tem nenhum desse limitação. Vamos abordar os detalhes e resultados destas diferenças quando debater as implementações de sistemas SAP, especialmente os servidores DBMS.

Dentro de uma conta de armazenamento, terá a possibilidade de criar contentores diferentes para fins de organizar e categorizar os VHDs diferentes. Estes contentores são normalmente utilizadas para, por exemplo, separados VHDs de VMs diferentes. Não existem nenhum implicações de desempenho na utilização de apenas um contentor ou de vários contentores por baixo de uma única conta de armazenamento do Azure.

No Azure um nome VHD, segue-se a seguinte ligação de nomenclatura tem de fornecer um nome exclusivo para o VHD no Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Tal como mencionado que a cadeia acima tem de identificar exclusivamente o VHD que é armazenado no Storage do Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Funcionamento em rede do Microsoft Azure
Microsoft Azure fornece uma infraestrutura de rede, que permite o mapeamento de todos os cenários, queremos tenha em consideração com o software SAP. As capacidades são:

* Acesso a partir do exterior, diretamente às VMs através de serviços de Terminal do Windows ou ssh/VNC
* Acesso a serviços e portas específicas utilizadas por aplicações de VMS
* Interno comunicação e resolução de nome entre um grupo de VMs implementadas como VMs do Azure
* Conectividade entre instalações, entre a rede no local de um cliente e a rede do Azure
* Cruzada região do Azure ou centro de dados conetividade entre sites do Azure

Podem encontrar mais informações aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existem muitos possibilidades diferentes para configurar o nome e a resolução IP no Azure. Neste documento, apenas na nuvem cenários dependem da predefinição de utilizar o DNS do Azure (contrariamente definir um próprio serviço DNS). Também é um novo serviço DNS do Azure, o que pode ser utilizado em vez de configurar o seu próprio servidor DNS. Podem encontrar mais informações no [neste artigo] [ virtual-networks-manage-dns-in-vnet] e no [nesta página](https://azure.microsoft.com/services/dns/).

Para cenários de vários locais, iremos são depender do facto de que no local OpenLDAP/AD/DNS tiver sido expandido através de VPN ou ligação privada para o Azure. Determinados cenários conforme documentado aqui, poderá ser necessário ter uma réplica de AD/OpenLDAP instalada no Azure.

Porque a rede e resolução de nomes é uma parte vital da implementação da base de dados para um sistema SAP, este conceito é abordado mais detalhadamente no [guia de implementação DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure
Ao criar cópias de segurança uma rede Virtual do Azure, pode definir o intervalo de endereços de endereços IP privados, atribuído por funcionalidade DHCP do Azure. Em cenários de vários locais, o intervalo de endereços IP definido é ainda alocado utilizando DHCP pelo Azure. No entanto, a resolução do nome de domínio é efetuada no local (partindo do princípio de que as VMs são uma parte de um domínio no local) e, por conseguinte, pode resolver endereços para além dos diferentes serviços em nuvem do Azure.

Cada máquina Virtual no Azure tem de estar ligado a uma rede Virtual.

Podem encontrar mais detalhes no [neste artigo] [ resource-groups-networking] e no [nesta página](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (Não foi TODO de MShermannd localizar um artigo que inclui o tópico OpenLDAP + ARM;)
[comment]: <> (MSSedusch < https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Por predefinição, depois de uma VM está implementada não é possível alterar a configuração de rede Virtual. As definições de TCP/IP tem de ser deixadas para o servidor DHCP do Azure. Comportamento predefinido é a atribuição de IP dinâmico.
>
>

O endereço MAC de placa de rede virtual pode alterar, por exemplo depois de redimensionamento e o convidado Windows ou Linux SO escolherá a nova placa de rede e utiliza automaticamente o DHCP para atribuir os endereços IP e DNS neste caso.

##### <a name="static-ip-assignment"></a>Atribuição de IP estático
É possível atribuir endereços IP reservados ou fixos a VMs dentro de uma rede Virtual do Azure. Executar as VMs numa rede Virtual do Azure é aberto uma excelente possibilidade tirar partido desta funcionalidade, se necessário ou necessários para alguns cenários. A atribuição de IP permanece válida em toda a existência da VM, independentemente da se a VM está em execução ou de encerramento. Como resultado, terá de ter o número global de VMs (VMS em execução e paradas) em consideração ao definir o intervalo de endereços IP para a rede Virtual. O endereço IP permanecerá atribuído até que a VM e respetiva Interface de rede é eliminado ou até que o endereço IP anular obtém atribuído novamente. Para obter mais informações, leia o artigo [neste artigo][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Vários NICs por VM
Pode definir várias placas de interface de rede virtual (vNIC) para uma máquina de Virtual do Azure. Com a capacidade de ter vários vNICs que pode começar a configurar o tráfego de rede separação onde, por exemplo, o tráfego de cliente é encaminhado através de um vNIC e back-end o tráfego é encaminhada através de um segundo vNIC. Dependentes do tipo de VM não existe são diferentes limitações que no que diz respeito ao número de vNICs. Podem encontrar detalhes exatos, funcionalidades e restrições nestes artigos:

* [Criar uma VM do Windows com vários NICs][virtual-networks-multiple-nics-windows]
* [Criar uma VM com Linux com vários NICs][virtual-networks-multiple-nics-linux]
* [Implementar várias NIC VMs através de um modelo][virtual-network-deploy-multinic-arm-template]
* [Implementar várias NIC VMs com o PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implementar várias NIC VMs utilizando a CLI do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conetividade site a Site
Em vários locais é VMs do Azure e no local ligado com uma ligação de VPN transparente e permanente. É esperado para se tornar o padrão de implementação de SAP mais comuns no Azure. Pressuposto é que processos com instâncias SAP no Azure e de procedimentos operacionais deverão funcionar transparente. Este significa que deve ser capaz de impressão fora estes sistemas, bem como os utilizar sistema de gestão de transporte (TMS) o SAP para o transporte altera a partir de um sistema de desenvolvimento no Azure para um sistema de teste, o que é implementado no local. Pode encontrar mais documentação em torno do site para site na [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN
Para criar uma ligação site a site (Centro de dados no local ao centro de dados do Azure), terá de obter e configurar um dispositivo VPN ou utilizar o encaminhamento e acesso serviço remoto (RRAS) que foi introduzida como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma ligação de VPN de site para site com o PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Acerca dos dispositivos VPN para ligações de Gateway de VPN de Site a Site][vpn-gateway-about-vpn-devices]
* [FAQ do VPN Gateway][vpn-gateway-vpn-faq]

![Ligação site a site entre no local e o Azure][planning-guide-figure-600]

A figura acima mostra duas subscrições do Azure tem subintervalos de endereço IP reservado para utilização em redes virtuais no Azure. A conectividade da rede no local para o Azure é estabelecida através de VPN.

#### <a name="point-to-site-vpn"></a>VPN ponto a Site
VPN ponto a site requer que cada computador cliente estabelecer ligação com a respetiva VPN no Azure. Para os cenários SAP que está a visualizar, a conetividade ponto a site não é prática. Por conseguinte, não existem referências adicionais são indicadas a conectividade VPN de ponto a site.

Podem encontrar mais informações aqui
* [Configurar uma ligação de Ponto a Site a uma VNet com o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN multilocal
Azure nowadays também oferece a possibilidade de criar a conectividade VPN multilocal para uma subscrição do Azure. Anteriormente, uma única subscrição estava limitada a uma ligação de VPN de site para site. Esta limitação ausente correu com ligações de VPN multilocal para uma única subscrição. Isto possibilita a tirar partido de mais do que uma região do Azure para uma subscrição específica através de configurações em vários locais.

Para obter documentação mais, consulte [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO foi encontrada nenhuma ligação de docu ARM)

#### <a name="vnet-to-vnet-connection"></a>Ligação VNet a VNet
Através da VPN multilocal, tem de configurar uma rede Virtual do Azure separados em cada uma das regiões. No entanto muito frequentemente tiver o requisito de que os componentes de software em diferentes regiões devem comunicar entre si. Idealmente, esta comunicação não deve ser encaminhada de uma região do Azure no local e a partir daí para outra região do Azure. Para o atalho, o Azure oferece a possibilidade de configurar uma ligação de uma rede Virtual do Azure numa região a outra rede Virtual do Azure alojada noutra região. Esta funcionalidade é denominada ligação VNet a VNet. Obter mais detalhes sobre esta funcionalidade podem ser encontrados aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Ligação privada ao ExpressRoute do Azure
O Microsoft Azure ExpressRoute permite a criação de ligações privadas entre centros de dados do Azure e a infraestrutura do cliente no local ou num ambiente de colocalização. ExpressRoute oferecido pelo MPLS vários fornecedores VPN (pacote de comutadores) ou de outros fornecedores de serviços de rede. As ligações do ExpressRoute não passam para a Internet pública. As ligações ExpressRoute oferecem maior segurança, mais fiabilidade através de vários circuitos paralelos, velocidades mais rápidas e latências inferiores em relação a ligações típicas através da Internet.

Encontrar mais detalhes sobre o ExpressRoute do Azure e ofertas aqui:

* <https://Azure.microsoft.com/Documentation/Services/expressroute/>
* <https://Azure.microsoft.com/pricing/details/expressroute/>
* <https://Azure.microsoft.com/Documentation/articles/expressroute-FAQs/>

Expressroute permite várias subscrições do Azure através de um circuito do ExpressRoute, conforme documentado aqui

* <https://Azure.microsoft.com/Documentation/articles/expressroute-howto-linkvnet-Arm/>
* <https://Azure.microsoft.com/Documentation/articles/expressroute-howto-circuit-Arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Imposição do túnel em caso de vários locais
Para VMs a associar a domínios no local através do site para site, ponto a site ou ExpressRoute, tem de certificar-se de que as definições de proxy da Internet são obter implementadas para todos os utilizadores, bem como as VMs. Por predefinição, o software em execução nessas VMs ou utilizadores utilizando um browser para aceder à internet não passará através do proxy da empresa, mas seria ligar diretamente através do Azure para a internet. Mas, mesmo a definição de proxy não é uma solução de 100% para direcionar o tráfego através do proxy da empresa, uma vez que é responsabilidade do software e serviços para verificar se o proxy. Se não está a ser fazer que software em execução na VM ou um administrador manipula as definições, o tráfego à Internet pode ser detoured novamente diretamente através do Azure para a Internet.

Para evitar isto, pode configurar a imposição do túnel com conectividade de site a site entre no local e o Azure. A descrição detalhada da funcionalidade a imposição do túnel for publicada aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Imposição de túnel com o ExpressRoute é ativado por clientes de publicidade uma rota predefinida através de sessões de peering de BGP de ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Resumo de rede do Azure
Este capítulo continha muitos pontos importantes sobre redes do Azure. Eis um resumo dos objetivos principais:

* Redes virtuais do Azure permite configurar a rede de acordo com as suas próprias necessidades
* Redes virtuais do Azure podem ser aproveitadas para atribuir os intervalos de endereços IP para VMs ou atribuir endereços IP fixos para VMs
* Para configurar uma ligação Site a Site ou ponto a Site terá de criar primeiro uma rede Virtual do Azure
* Depois de uma máquina virtual tiver sido implementada, já não é possível alterar a rede Virtual atribuído à VM do

### <a name="quotas-in-azure-virtual-machine-services"></a>Quotas nos serviços de Máquina Virtual do Azure
É necessário ser encriptado sobre o facto de que a infraestrutura de armazenamento e de rede é partilhada entre VMs em execução uma variedade de serviços na infraestrutura do Azure. E, tal como em centros de dados do cliente, sobre-aprovisionar de alguns dos recursos de infraestrutura de efetuar para um nível. A plataforma do Microsoft Azure utiliza o disco, CPU, rede e outras quotas para limitar o consumo de recursos e preservar desempenho consistente e determinística.  Os diferentes tipos VM (A5 A6, etc.) tem quotas diferentes para o número de discos, CPU, RAM e de rede.

> [!NOTE]
> Recursos de CPU e memória dos tipos VM suportados pelo SAP são pré-alocados em nós anfitriões. Isto significa que, depois da implementação da VM, os recursos do anfitrião estão disponíveis conforme definido pelo tipo de VM.
>
>

Quando planear e dimensionar SAP soluções do Azure tem de ser consideradas as quotas para cada tamanho da máquina virtual. As quotas VM são descritas [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

As quotas descritas representam os valores máximos teórico.  O limite de IOPS por disco pode ser conseguido com o IOs pequenos (8kb), mas, possivelmente, não pode ser conseguido com o IOs grande (1Mb).  O limite IOPS é aplicado a granularidade de um único disco.

Como uma árvore de decisão aproximada para decidir se um sistema SAP se ajusta à serviços de Máquina Virtual do Azure e as respetivas capacidades ou se um sistema existente tem de ser configurado de forma diferente para implementar o sistema no Azure, pode ser utilizada a árvore de decisões abaixo:

![Árvore de decisões para decidir a capacidade de implementar o SAP no Azure][planning-guide-figure-700]

**Passo 1**: as informações mais importantes para começar são o requisito de SAPS para um determinado sistema SAP. Os requisitos de SAPS necessário separados para a parte DBMS e a parte da aplicação de SAP, mesmo se o sistema SAP já estiver implementado no local numa configuração de camada 2. Para sistemas existentes, SAPS relacionados com hardware em utilização, muitas vezes, podem ser determinados ou estimadas com base nos testes de desempenho SAP existentes. Os resultados podem ser encontrados aqui: <http://global.sap.com/campaigns/benchmark/index.epx>.
Para sistemas SAP recentemente implementados, deve realizou um exercício de dimensionamento, o que deve determinar os requisitos de SAPS do sistema.
Consulte também este blogue e o documento anexado para dimensionamento SAP no Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Passo 2**: para sistemas existentes, o volume de e/s e operações de e/s por segundo no servidor DBMS devem ser medidas. Para sistemas recentemente planeados, o exercício de dimensionamento para o novo sistema também deverá dar-ideias aproximadas dos requisitos de e/s no lado DBMS. Se não souber, eventualmente, terá de realizar uma prova de conceito.

**Passo 3**: Compare o requisito de SAPS para o servidor DBMS com SAPS podem fornecer os diferentes tipos VM do Azure. As informações no SAPS de diferentes tipos de VM do Azure estão documentadas na nota SAP [1928533]. O foco deve estar na DBMS VM primeiro uma vez que a camada de base de dados está a camada de um sistema de SAP NetWeaver ampliar na maioria das implementações. Em contrapartida, é possível ampliar a camada de aplicação SAP. Se nenhum do SAP suportado tipos de VM do Azure podem fornecer os SAPS necessárias, não é possível executar a carga de trabalho do sistema SAP planeada no Azure. Terá de a implementar o sistema local ou precisar de alterar o volume de carga de trabalho para o sistema.

**Passo 4**: conforme documentado [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows], Azure impõe uma quota IOPS por disco independente se utilizar o armazenamento Standard ou Premium. Depende do tipo VM, o número de discos de dados, que podem ser montados varia. Como resultado, pode calcular um número IOPS máximo que pode ser conseguido com cada um dos tipos VM diferentes. Depende o esquema do ficheiro de base de dados, pode stripe discos para se tornar um volume no SO convidado. No entanto, se o volume IOPS atual de um sistema SAP implementado excede os limites calculados o maior do tipo de VM do Azure e se não houver nenhuma oportunidade para compensar com mais memória, a carga de trabalho do sistema SAP pode gravemente afetada. Nestes casos, pode atingiu um ponto em que não deve implementar o sistema no Azure.

**Passo 5**: especialmente nos sistemas SAP, que são implementado no local na camada 2 configurações, as possibilidades são que o sistema poderá ter de ser configurada no Azure numa configuração de 3 camadas. Neste passo, tem de verificar se há um componente SAP camada da aplicação, que não pode ser ampliada e que seria se ajusta os recursos de CPU e memória que os diferentes tipos de VM do Azure oferecem. Se existir, de facto, um componente deste tipo, o sistema SAP e respetiva carga de trabalho não podem ser implementados no Azure. Mas se pode dimensionar os componentes de aplicações do SAP para várias VMs do Azure, o sistema pode ser implementado no Azure.

**Passo 6**: se os componentes da camada de aplicação de DBMS e SAP podem ser executados em VMs do Azure, a configuração tem de ser definido com a:

* Número de VMs do Azure
* Tipos VM para os componentes individuais
* Número de VHDs numa VM DBMS para fornecer suficiente IOPS

## <a name="managing-azure-assets"></a>Gerir recursos do Azure
### <a name="azure-portal"></a>Portal do Azure
O portal do Azure é um dos três interfaces para gerir implementações de VM do Azure. As tarefas de gestão básicas, como implementar VMs a partir de imagens, podem ser feitas através do portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes do Azure também são tarefas do portal do Azure pode processar muito bem. No entanto, as funcionalidades como carregar VHDs no local para o Azure ou copiar um VHD no Azure são tarefas, que necessitam de ferramentas de terceiros ou administração através do PowerShell ou a CLI.

![Portal do Microsoft Azure - descrição geral de Máquina Virtual][planning-guide-figure-800]

[comment]: <> (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Tarefas de administração e a configuração para a instância de Máquina Virtual são possíveis a partir do portal do Azure.

Para além de reiniciar e encerrar uma Máquina Virtual também pode anexar, desanexar e criar discos de dados para a instância de Máquina Virtual, para a instância de preparação de imagem de captura e configurar o tamanho da instância de Máquina Virtual.

O portal do Azure fornece funcionalidades básicas para implementar e configurar as VMs e muitos outros serviços do Azure. No entanto não disponível funcionalidade é abrangida pelo portal do Azure. No portal do Azure, não é possível executar tarefas, como:

* VHDs de carregamento para o Azure
* Copiar VMs

[comment]: <> (MShermannd TODO o sobre a automatização de serviço para SAP VMs?)
[comment]: <> (MSSedusch de implementação do SO de várias VMs entretanto possíveis)
[comment]: <> (Também MSSedusch qualquer tipo de automatização relativas a implementação não é possível com o portal do Azure. Tarefas, tais como a implementação de script de várias VMs não é possível através do portal do Azure.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestão através de cmdlets do PowerShell do Microsoft Azure
O Windows PowerShell é uma arquitetura de poderosa e extensível que foi amplamente ADOTOU pelos clientes implementar grandes quantidades de sistemas no Azure. Após a instalação de cmdlets do PowerShell num ambiente de trabalho, portáteis ou estação de gestão dedicado, os cmdlets do PowerShell pode ser executados remotamente.

O processo para ativar um ambiente de trabalho/portátil local para a utilização de cmdlets do Azure PowerShell e como configurar os para utilização com as subscrições do Azure está descrita em [neste artigo][powershell-install-configure].

Mais passos sobre como instalar, atualizar e configurar o Azure PowerShell cmdlets também podem ser encontrados na [este capítulo do guia de implementação][deployment-guide-4.1].

Experiência do cliente até ao momento foi que o PowerShell (PS) certamente é a ferramenta mais poderosa para implementar as VMs e criar passos personalizados na implementação de VMs. Todas as instâncias SAP de em execução no Azure de que os clientes estão a utilizar cmdlets de PS para complementar as tarefas de gestão que não no portal do Azure ou mesmo estiver a utilizar cmdlets de PS exclusivamente para gerir as respetivas implementações no Azure. Uma vez que os cmdlets específicos do Azure partilham a mesma Convenção de nomenclatura como os cmdlets relacionados com o Windows mais de 2000, é uma tarefa fácil para os administradores do Windows tirar partido destes cmdlets.

Consulte o exemplo aqui: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO descrevem novos comandos da CLI quando testado)
Implementação da extensão de monitorização do Azure para SAP (consulte o capítulo [solução de monitorização do Azure para SAP] [ planning-guide-9.1] neste documento) só é possível através do PowerShell ou a CLI. Por conseguinte, é obrigatório para definir e configurar o PowerShell ou o CLI ao implementar ou administrar um sistema de SAP NetWeaver no Azure.  

Como o Azure oferece uma funcionalidade mais, novos cmdlets de PS são vão ser adicionadas que necessita de uma atualização dos cmdlets. Por conseguinte faz sentido para verificar o site de transferir o Azure, pelo menos, uma vez mês <https://azure.microsoft.com/downloads/> para uma nova versão dos cmdlets. A nova versão é instalada por cima a versão mais antiga.

Para obter uma lista geral do relacionadas com o Azure PowerShell comandos verifique aqui: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestão através de comandos da CLI do Microsoft Azure
Para os clientes que utilizam o Linux e pretendem gerir o Azure Powershell de recursos podem não ser uma opção. A Microsoft oferece o CLI do Azure como alternativa.
A CLI do Azure fornece um conjunto de código aberto, comandos de várias plataformas para trabalhar com a plataforma do Azure. A CLI do Azure fornece muito a mesma funcionalidade que foi encontrada no portal do Azure.

Para obter informações sobre a instalação, configuração e como utilizar a CLI Consulte comandos para realizar tarefas do Azure

* [Instalar a CLI do Azure][xplat-cli]
* [Implementar e gerir máquinas virtuais utilizando modelos Azure Resource Manager e a CLI do Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também capítulo [CLI do Azure para VMs com Linux] [ deployment-guide-4.5.2] no [guia de implementação] [ planning-guide] sobre como utilizar a CLI do Azure para implementar a monitorização do Azure Extensão para SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes formas de implementar as VMs para SAP no Azure
Este capítulo aprender as diferentes formas de implementar uma VM no Azure. Procedimentos de preparação adicional, bem como o processamento de VHDs e as VMs do Azure é abrangidos neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implementação de VMs para SAP
Microsoft Azure oferece várias formas de implementar as VMs e discos associados. Deste modo, é muito importante compreender as diferenças, uma vez que os preparativos das VMs poderão diferir consoante o método de implementação. Em geral, podemos observe os seguintes cenários:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Mover uma VM no local para o Azure com um disco não generalizado
Planear mover um sistema específico do SAP no local para o Azure. Isto pode ser feito através do carregamento o VHD, que contém o sistema operativo, os binários de SAP e binários DBMS plus os VHDs com os ficheiros de dados e de registo do DBMS para o Azure. Contrast para [cenário &#2; abaixo][planning-guide-5.1.2], mantenha o nome de anfitrião, o SID de SAP, e as contas de utilizador SAP na VM do Azure, que foram configuradas no ambiente no local. Por conseguinte, generalizar a imagem não é necessário. Consulte capítulos [preparação para mover uma VM no local para o Azure com um disco não generalizado] [ planning-guide-5.2.1] deste documento para obter passos de preparação no local e o carregamento de VMs não generalizado ou VHDs no Azure. Capítulo leitura [cenário 3: mover uma VM no local através de um VHD de Azure não generalizado com SAP] [ deployment-guide-3.4] no [guia de implementação] [ deployment-guide] para passos detalhados de implementar essa uma imagem no Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implementar uma VM com uma imagem específica do cliente
Devido às necessidades de correção específicos da sua versão do SO ou DBMS, as imagens fornecidas no Azure Marketplace podem não atender as suas necessidades. Por conseguinte, poderá ter de criar uma VM utilizando a sua própria imagem de VM de SO/DBMS privada, que pode ser implementada várias vezes posteriormente. Para preparar essa uma imagem de privada duplicação, os seguintes itens tem de ser considerados como estando:

- - -
> ![Windows][Logo_Windows] Windows
>
> Ver mais detalhes aqui: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> definições do Windows (por exemplo, o SID do Windows e o nome de anfitrião) tem de ser abstracted/generalizado na VM no local através de o comando sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Siga os passos descritos nestes artigos para [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], ou [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], para preparar um VHD para ser carregado para o Azure.
>
>

- - -
Se já tiver instalado o conteúdo SAP na VM no local (especialmente para sistemas de camada 2), pode adaptar as definições do sistema SAP após a implementação da VM do Azure através de instância de mudar o nome de procedimento suportado pelo Gestor de aprovisionamento de Software para SAP (nota SAP [1619720]). Consulte capítulos [preparação para implementar uma VM com uma imagem específica do cliente para SAP] [ planning-guide-5.2.2] e [carregar um VHD no local para o Azure] [ planning-guide-5.3.2]deste documento para obter passos de preparação no local e o carregamento de uma VM generalizado para o Azure. Capítulo leitura [cenário 2: implementar uma VM com uma imagem personalizada para SAP] [ deployment-guide-3.3] no [guia de implementação] [ deployment-guide] para obter passos detalhados de implementação Este tipo uma imagem no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementar uma VM fora do Azure Marketplace
Gostaria de utilizar um Microsoft ou por terceiros fornecidos a imagem de VM do Azure Marketplace para implementar a VM. Depois de implementar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP e/ou o DBMS no interior da sua VM, tal como teria num ambiente no local. Para obter mais descrição da implementação, consulte capítulo [cenário 1: implementar uma VM fora do Azure Marketplace para SAP] [ deployment-guide-3.2] no [guia de implementação][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparar as VMs com SAP para o Azure
Antes de carregar as VMs no Azure, tem de certificar-se de que as VMs e VHDs satisfazer determinados requisitos. Existem pequenas diferenças consoante o método de implementação que é utilizado.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma VM no local para o Azure com um disco não generalizado
É um método de implementação comum mover uma VM existente que é executado um sistema SAP no local para o Azure. Essa VM e o sistema SAP na VM apenas devem ser executado no Azure utilizando o mesmo nome de anfitrião e, muito provavelmente, com o mesmo SID de SAP. Neste caso, SO de VM do convidado não deve ser generalizado para múltiplas implementações. Se a rede no local foi expandida para o Azure (consulte o capítulo [em vários locais - implementação única ou várias VMs SAP no Azure com o requisito de que está a ser totalmente integrado à rede no local] [ planning-guide-2.2] neste documento), em seguida, mesmo as mesmas contas de domínio podem ser utilizadas dentro da VM, como os quais foram utilizados antes no local.

Requisitos ao preparar o seu próprio disco da VM do Azure são:

* Originalmente o VHD que contém o sistema operativo pode ter um tamanho máximo de 127GB apenas. Esta limitação foi eliminado o aperto no final de Março de 2015. Agora que o VHD que contém o sistema operativo pode ser até 1TB de tamanho como qualquer outro armazenamento do Azure alojada VHD bem.
* Tem de estar no formato VHD fixo. Dinâmica VHDs ou VHDs no formato VHDx não são suportados ainda no Azure. Os VHDs dinâmicos serão convertidos em VHDs estáticos ao carregar o VHD com mini-comandos de PowerShell ou a CLI
* VHDs que estão montados para a VM e devem ser montados novamente no Azure para a necessidade VM para ter um formato VHD fixo. Leia [neste artigo (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) e [neste artigo (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) para limites de tamanho de discos de dados. Os VHDs dinâmicos serão convertidos em VHDs estáticos ao carregar o VHD com mini-comandos de PowerShell ou a CLI
* Adicione outra conta local com privilégios de administrador que pode ser utilizado pelo suporte da Microsoft ou que podem ser atribuídos como contexto de serviços e aplicações sejam executadas em até que a implementação da VM e os utilizadores mais adequados pode ser utilizada.
* Para o caso de utilizar um cenário de implementação apenas de nuvem (consulte o capítulo [apenas na nuvem - implementações de Máquina Virtual no Azure sem dependências na rede de cliente no local] [ planning-guide-2.1] deste documento) em combinação com este método de implementação, as contas de domínio poderão não funcionar depois do disco do Azure estiver implementado no Azure. Isto é particularmente verdadeiro para as contas que são utilizadas para executar serviços como os DBMS ou SAP aplicações. Por conseguinte, tem de substituir essas contas de domínio com contas locais da VM e eliminar as contas de domínio no local na VM. Manter os utilizadores de domínio no local da imagem de VM não é um problema quando a VM é implementada no cenário entre locais, conforme descrito em capítulo [em vários locais - implementação única ou várias VMs SAP no Azure com o requisito de que está a ser completamente integrado da rede no local] [ planning-guide-2.2] neste documento.
* Se as contas de domínio foram utilizadas como inícios de sessão DBMS ou os utilizadores quando executar o sistema local e essas VMs são deveria ser implementado em cenários de apenas na nuvem, os utilizadores de domínio tem de ser eliminado. Tem de certificar-se de que o administrador local e o outro utilizador local de VM é adicionado como um início de sessão/utilizador para o DBMS como administradores.
* Adicione outras contas locais como aqueles podem ser necessários para o cenário de implementação específica.

- - -
> ![Windows][Logo_Windows] Windows
>
> Neste cenário, nenhuma generalização (sysprep) da VM é necessário para carregar e implementar a VM no Azure.
> Certifique-se de que a unidade que d:\ não é utilizado.
> Definir automount de disco para discos ligados, conforme descrito em capítulo [definição automount para discos ligados] [ planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Este cenário não generalização (waagent-deprovision) da VM, é necessário para carregar e implementar a VM no Azure.
> Certifique-se de que o recurso/mnt/não é utilizado e que todos os discos estão montados através do uuid. Para o disco do SO, certifique-se de que a entrada de bootloader também reflete a montagem baseado no uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implementar uma VM com uma imagem específica do cliente para SAP
Ficheiros VHD que contêm um SO generalizado são armazenados nos contentores nas contas de armazenamento do Azure ou imagens de disco gerido. Pode implementar uma nova VM a partir de uma imagem deste tipo ao consultar a imagem VHD ou disco gerida como uma origem nos seus ficheiros de modelo de implementação, conforme descrito em capítulo [cenário 2: implementar uma VM com uma imagem personalizada para SAP] [ deployment-guide-3.3]do [guia de implementação][deployment-guide].

Requisitos quando preparar a sua própria imagem de VM do Azure são:

* Originalmente o VHD que contém o sistema operativo pode ter um tamanho máximo de 127GB apenas. Esta limitação foi eliminado o aperto no final de Março de 2015. Agora que o VHD que contém o sistema operativo pode ser até 1TB de tamanho como qualquer outro armazenamento do Azure alojada VHD bem.
* Tem de estar no formato VHD fixo. Dinâmica VHDs ou VHDs no formato VHDx não são suportados ainda no Azure. Os VHDs dinâmicos serão convertidos em VHDs estáticos ao carregar o VHD com mini-comandos de PowerShell ou a CLI
* VHDs que estão montados para a VM e devem ser montados novamente no Azure para a necessidade VM para ter um formato VHD fixo. Leia [neste artigo (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) e [neste artigo (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) para limites de tamanho de discos de dados. Os VHDs dinâmicos serão convertidos em VHDs estáticos ao carregar o VHD com mini-comandos de PowerShell ou a CLI
* Uma vez que todos os utilizadores de domínio registado como os utilizadores na VM não existirá num cenário apenas na nuvem (consulte o capítulo [apenas na nuvem - implementações de Máquina Virtual no Azure sem dependências na rede de cliente no local] [ planning-guide-2.1] deste documento), serviços, utilizar esse domínio contas poderão não funcionar depois da imagem é implementada no Azure. Isto é particularmente verdadeiro para as contas que são utilizadas para executar serviços como o DBMS ou SAP aplicações. Por conseguinte, tem de substituir essas contas de domínio com contas locais da VM e eliminar as contas de domínio no local na VM. Manter os utilizadores de domínio no local da imagem de VM poderão não ser um problema quando a VM é implementada no cenário entre locais, conforme descrito em capítulo [em vários locais - implementação única ou várias VMs SAP no Azure com o requisito de que está a ser totalmente integrado à rede no local] [ planning-guide-2.2] neste documento.
* Adicione outra conta local com privilégios de administrador que pode ser utilizado pelo suporte da Microsoft nas investigações de problema ou que podem ser atribuídos como contexto de serviços e aplicações sejam executadas em até que a implementação da VM e os utilizadores mais adequados pode ser utilizada.
* Em implementações apenas na nuvem e onde as contas de domínio foram utilizadas como inícios de sessão DBMS ou ao executar o sistema local, os utilizadores de domínio devem ser eliminados. Tem de certificar-se de que o administrador local e o outro utilizador local de VM é adicionado como um utilizador/início de sessão do DBMS como administradores.
* Adicione outras contas locais como aqueles podem ser necessários para o cenário de implementação específica.
* Se a imagem contém uma instalação do SAP NetWeaver e mudar o nome do nome do anfitrião do nome original at the point of a implementação do Azure, é provável que, recomenda-se para copiar as versões mais recentes do DVD do Gestor de SAP de aprovisionamento de Software para o modelo. Isto permitirá utilizar facilmente a funcionalidade de mudança de nome de SAP fornecido para adaptar o nome de anfitrião foi alterada e/ou alterar o SID do sistema SAP dentro da imagem VM implementada, assim que uma nova cópia é iniciada.

- - -
> ![Windows][Logo_Windows] Windows
>
> Certifique-se que unidade D:\ não é utilizado automount de disco do conjunto de discos ligados, conforme descrito em capítulo [definição automount para discos ligados] [ planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que o recurso/mnt/não é utilizado e que todos os discos estão montados através do uuid. Para o disco do SO, certifique-se que a entrada de bootloader também reflete a montagem baseado no uuid.
>
>

- - -
* GUI de SAP (para administrativas e fins do programa de configuração) podem ser instalados previamente nesse modelo.
* Pode ser instalado outro software necessário para executar as VMs com êxito em cenários de vários locais, desde que este software pode trabalhar com a mudança de nome da VM.

Se a VM está preparada suficientemente ser genérico e, eventualmente, independentemente de contas/utilizadores não está disponíveis no cenário de implementação do Azure visada, o último passo de preparação de generalizar essa uma imagem é efetuado.

##### <a name="generalizing-a-vm"></a>Generalizar uma VM
- - -
> ![Windows][Logo_Windows] Windows
>
> É o último passo para iniciar sessão a uma VM com uma conta de administrador. Abra uma janela de comandos do Windows como *administrador*. Aceda a %windir%\windows\system32\sysprep e executar sysprep.exe.
> Será apresentada uma janela de pequena. É importante verificar o **Generalize** opção (a predefinição é não marcada) e altere a opção de encerramento da predefinido de 'Reiniciar' para 'encerramento'. Este procedimento assume que o processo de sysprep é executado no local no SO convidado de uma VM.
> Se pretender efetuar o procedimento com uma VM já em execução no Azure, siga os passos descritos no [neste artigo](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para utilizar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferência de VMs e VHDs no local para Azure
Uma vez que o carregamento de imagens VM e os discos para o Azure não é possível através do portal do Azure, terá de utilizar cmdlets do Azure PowerShell ou a CLI. Possibilidade de outra é a utilização da ferramenta 'AzCopy'. A ferramenta pode copiar VHDs entre no local e o Azure (em ambas as direções). Também pode copiar VHDs entre regiões do Azure. Consulte [esta documentação] [ storage-use-azcopy] para transferência e utilização do AzCopy.

Uma terceira alternativa seria utilizar várias ferramentas orientadas para GUI de terceiros. No entanto, certifique-se que estas ferramentas são que suportam Blobs de páginas do Azure. Para fins de nosso é necessário utilizar o arquivo de Blob de página do Azure (as diferenças são descritas aqui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Além disso, as ferramentas fornecidas pelo Azure são muito eficientes em compressão as VMs e os VHDs que tem de ser carregado. Isto é importante porque esta eficiência na compressão reduz o tempo de carregamento (que varia mesmo assim, dependendo da ligação de carregamento à internet a instalações no local e a região de implementação do Azure visada). É um justa pressuposto de que o carregamento de uma VM ou o VHD da localização alfabetos aos dados do Azure com base em E.U.A. centros irão demorar mais do que carregar os mesmos VHDs/VMs para o Azure alfabetos centros de dados.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregar um VHD no local para o Azure
Para carregar uma VM existente ou VHD a partir da rede no local VM ou VHD tem de cumprir os requisitos, tal como indicado na capítulo [preparação para mover uma VM no local para o Azure com um disco não generalizado] [ planning-guide-5.2.1] deste documento.

Este tipo uma VM não tem de ser generalizado e pode ser carregada o estado e a forma tem após encerramento no lado no local. O mesmo se aplica-se os VHDs adicionais que não contém qualquer sistema operativo.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregar um VHD e tornando-a um disco do Azure
Neste caso, pretendemos carregar um VHD, com ou sem sistema operativo no mesmo e montá-la para uma VM como um disco de dados ou utilizá-lo como disco do SO. Este é um processo de vários passo

**PowerShell**

* Inicie sessão na sua subscrição com *Login-AzureRmAccount*
* Definir a subscrição da sua contexto com *Set-AzureRmContext* e SubscriptionId de parâmetro ou SubscriptionName - consulte <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Carregar o VHD com *adicionar AzureRmVhd* para uma conta de armazenamento do Azure - consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Opcional) Criar um disco gerido a partir de VHD com *New-AzureRmDisk* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Definir o disco de SO de uma nova configuração VM ao VHD ou disco geridos com *conjunto AzureRmVMOSDisk* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Criar uma nova VM a partir da configuração de VM com *New-AzureRmVM* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Adicionar um disco de dados para uma nova VM com *adicionar AzureRmVMDataDisk* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**CLI 2.0 do Azure**

* Inicie sessão na sua subscrição com *início de sessão az*
* Selecione a sua subscrição com *az conta set - subscrição`<subscription name or id`>*
* Carregar o VHD com *carregamento de blob de armazenamento az* -consulte [utilizando a CLI do Azure com o Storage do Azure][storage-azure-cli]
* (Opcional) Criar um disco gerido a partir de VHD com *criar disco de az* -Consulte https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Criar uma nova VM especificação carregado VHD ou disco geridos como disco de SO com *az vm criar* e o parâmetro *– anexar--disco do SO*
* Adicionar um disco de dados para uma nova VM com *anexar o disco da vm az* e o parâmetro *– novo*

**Modelo**

* Carregar o VHD com a CLI do Powershell ou do Azure
* (Opcional) Criar um disco gerido a partir de VHD com o Powershell, a CLI do Azure ou o portal do Azure
* Implemente a VM com um modelo JSON que referencia o VHD, conforme mostrado no [este modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json) ou ao utilizar discos geridos, conforme mostrado no [este modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-disk-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implementação de uma imagem VM
Para carregar uma VM existente ou VHD a partir da rede no local para utilizá-la como uma imagem de VM do Azure VM ou VHD tem de cumprir os requisitos listados em capítulo [preparação para implementar uma VM com uma imagem específica do cliente para SAP] [ planning-guide-5.2.2] deste documento.

* Utilize *sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar o VM - consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma Máquina virtual Linux para utilizar como um modelo do Resource Manager] [ capture-image-linux-step-2-create-vm-image] para Linux
* Inicie sessão na sua subscrição com *Login-AzureRmAccount*
* Definir a subscrição da sua contexto com *Set-AzureRmContext* e SubscriptionId de parâmetro ou SubscriptionName - consulte <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Carregar o VHD com *adicionar AzureRmVhd* para uma conta de armazenamento do Azure - consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Opcional) Criar uma imagem de disco gerida a partir de VHD com *New-AzureRmImage* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Definir o disco de SO de uma nova configuração VM para o
  * O VHD com *Set AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Gerido a imagem de disco *conjunto AzureRmVMSourceImage* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Criar uma nova VM a partir da configuração de VM com *New-AzureRmVM* -consulte <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**CLI 2.0 do Azure**

* Utilize *sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar o VM - consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma Máquina virtual Linux para utilizar como um modelo do Resource Manager] [ capture-image-linux-step-2-create-vm-image] para Linux
* Inicie sessão na sua subscrição com *início de sessão az*
* Selecione a sua subscrição com *az conta set - subscrição`<subscription name or id`>*
* Carregar o VHD com *carregamento de blob de armazenamento az* -consulte [utilizando a CLI do Azure com o Storage do Azure][storage-azure-cli]
* (Opcional) Criar uma imagem de disco gerida a partir de VHD com *criar imagem az* -Consulte https://docs.microsoft.com/cli/azure/image#az_image_create
* Criar uma nova VM especificação VHD ou a imagem de disco gerido carregado como disco de SO com *az vm criar* e o parâmetro *– imagem*

**Modelo**

* Utilize *sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar o VM - consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma Máquina virtual Linux para utilizar como um modelo do Resource Manager] [ capture-image-linux-step-2-create-vm-image] para Linux
* Carregar o VHD com a CLI do Powershell ou do Azure
* (Opcional) Criar uma imagem de disco gerida a partir de VHD com o Powershell, a CLI do Azure ou o portal do Azure
* Implemente a VM com um modelo JSON que façam referência a imagem do VHD, conforme mostrado no [este modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-image/azuredeploy.json) ou utilizando a imagem de disco gerida, conforme mostrado no [este modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Transferência de VHDs ou discos geridos no local
Infraestrutura do Azure como um serviço não é uma rua unidirecional de apenas a capacidade de carregar VHDs e SAP sistemas. Pode mover SAP sistemas a partir do Azure novamente para o mundo no local.

Durante a hora de transferência de VHDs ou discos geridos não pode ser Active Directory. Mesmo quando a transferência de discos que estão montados para VMs, a VM tem de ser encerrado e desalocada. Se pretender transferir o conteúdo que, em seguida, deve ser utilizado para configurar um novo sistema no local e se é aceitável durante a hora da transferência e a configuração do novo sistema que o sistema no Azure pode ainda estar operacional da base de dados , pode evitar um longo período de inatividade, efetuando uma cópia de segurança da base de dados comprimidos num disco e transferir apenas esse disco em vez de transferirem também a VM de base do SO.

#### <a name="powershell"></a>PowerShell

  * Transferência de um disco gerido  
  Terá primeiro de obter acesso para o blob subjacente do disco geridos. Em seguida, pode copiar o blob subjacente para uma nova conta de armazenamento e transferir o blob a partir desta conta de armazenamento.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Transferência de um VHD  
  Depois do sistema SAP está parado e a VM está a ser encerrada, pode utilizar o cmdlet do PowerShell AzureRmVhd guardar no destino no local para transferir os discos VHD para o mundo no local. Para tal, é necessário o URL do VHD que pode encontrar no 'Armazenamento de secção' do portal do Azure (é necessário para navegar para a conta de armazenamento e o contentor de armazenamento onde foi criado o VHD) e precisa de saber onde o VHD deve ser copiado.

  Em seguida, pode tirar partido do comando, basta definindo o parâmetro SourceUri como o URL de VHD para transferir e o LocalFilePath como a localização física do VHD (incluindo o respetivo nome). O comando foi aspeto:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Para obter mais detalhes sobre o cmdlet AzureRmVhd guardar, verifique aqui <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Transferência de um disco gerido  
  Terá primeiro de obter acesso para o blob subjacente do disco geridos. Em seguida, pode copiar o blob subjacente para uma nova conta de armazenamento e transferir o blob a partir desta conta de armazenamento.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Transferência de um VHD   
  Depois do sistema SAP está parado e a VM está a ser encerrada, pode utilizar o comando da CLI do Azure _transferências de blob storage do azure_ no destino no local para transferir o VHD discos de volta para o mundo no local. Para o efeito, é necessário o nome e o contentor de VHD que pode encontrar em 'Armazenamento secção' do portal do Azure (é necessário para navegar para a conta de armazenamento e o contentor de armazenamento onde foi criado o VHD) e precisa de saber onde o VHD deve ser copi Ed para.

  Em seguida, pode tirar partido do comando definindo simplesmente o blob de parâmetros e um contentor do VHD de transferir e de destino como a localização de destino físico do VHD (incluindo o respetivo nome). O comando foi aspeto:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferência de VMs e os discos no Azure
#### <a name="copying-sap-systems-within-azure"></a>Copiar sistemas SAP no Azure
Um sistema SAP ou mesmo DBMS servidor dedicado que suporta uma camada de aplicação SAP será provavelmente constituídas por vários discos que contém o sistema operativo com os binários ou os ficheiros de dados e de registo da base de dados SAP. Nem a funcionalidade de copiar discos do Azure, nem a funcionalidade do Azure de guardar discos num disco local tem um mecanismo de sincronização, o qual seria vários discos de instantâneo de forma síncrona. Por conseguinte, o estado dos discos copiados ou guardados, mesmo que se encontram estão montados contra a mesma VM seria diferente. Isto significa que as maiúsculas e minúsculas concreta de ter dados diferentes e logfile(s) contidos nos discos diferentes, a base de dados no end seria possível inconsistente.

**Conclusão: Para poder copiar ou guardar discos que fazem parte de uma configuração de sistema do SAP terá de parar o sistema SAP e também necessário desligar a VM implementada. Apenas, em seguida, pode copiar ou transferir o conjunto de discos para criar uma cópia do sistema SAP no Azure ou no local.**

Os discos de dados podem ser armazenados como ficheiros VHD numa conta de armazenamento do Azure e podem ser anexados diretamente a uma máquina virtual ou ser utilizados como uma imagem. Neste caso, o VHD é copiado para outra localização antes de estar ligado à máquina virtual. O nome completo do ficheiro VHD no Azure tem de ser exclusivo no Azure. Tal como mencionado anteriormente já, o nome é tipos de um nome de três partes que se pareça com:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Os discos de dados também podem ser geridos discos. Neste caso, o disco geridos é utilizado para criar um novo disco geridos antes de estar ligado à máquina virtual. O nome do disco geridos têm de ser exclusivo dentro de um grupo de recursos.

##### <a name="powershell"></a>PowerShell
Pode utilizar cmdlets do Azure PowerShell para copiar um VHD, conforme mostrado no [neste artigo][storage-powershell-guide-full-copy-vhd]. Para criar um novo disco gerida, utilize o novo AzureRmDiskConfig e New-AzureRmDisk conforme mostrado no exemplo seguinte.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Pode utilizar a CLI do Azure para copiar um VHD, conforme mostrado no [neste artigo][storage-azure-cli-copy-blobs]. Para criar um novo disco gerida, utilize *criar disco de az* conforme mostrado no exemplo seguinte.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Ferramentas de armazenamento do Azure
* <http://storageexplorer.com/>

Edições Professional do exploradores de armazenamento do Azure podem ser encontradas aqui:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD próprio dentro de uma conta de armazenamento é um processo que demora apenas alguns segundos (semelhantes a criação de instantâneos com cópia lento e copie na escrita de hardware de SAN). Depois de ter uma cópia do ficheiro VHD anexe-o a uma máquina virtual ou utilizá-la como uma imagem para anexar cópias do VHD para as máquinas virtuais.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>CLI 2.0
```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiar os discos entre contas de armazenamento do Azure
Não é possível efetuar esta tarefa no portal do Azure. Pode utilizar cmdlets do PowerShell do Azure, CLI do Azure ou um browser de armazenamento de terceiros. Os comandos da CLI ou os cmdlets do PowerShell, podem criar e gerir blobs, que incluem a capacidade de forma assíncrona copiar os blobs em contas de armazenamento e em regiões dentro da subscrição do Azure.

##### <a name="powershell"></a>PowerShell
Também pode copiar VHDs entre subscrições. Para obter mais informações, leia [neste artigo][storage-powershell-guide-full-copy-vhd].

O fluxo básico de lógica de cmdlet PS tem o seguinte aspeto:

* Criar um contexto de conta de armazenamento para o **origem** conta de armazenamento com *New-AzureStorageContext* -consulte <https://msdn.microsoft.com/library/dn806380.aspx>
* Criar um contexto de conta de armazenamento para o **destino** conta de armazenamento com *New-AzureStorageContext* -consulte <https://msdn.microsoft.com/library/dn806380.aspx>
* Inicie a cópia com o

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verificar o estado da cópia em ciclo com

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o VHD de novo a uma máquina virtual, conforme descrito acima.

Para exemplos, consulte [neste artigo][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Inicie a cópia com o

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Verificar o estado se a cópia em ciclo com

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Anexe o VHD de novo a uma máquina virtual, conforme descrito acima.

Para exemplos, consulte [neste artigo][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Processamento de disco
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura de disco/VM para implementações de SAP
Idealmente, o processamento da estrutura de uma VM e os discos associados deve ser muito simple. Nas instalações no local, os clientes desenvolveu várias formas de structuring uma instalação de servidor.

* Um base disco que contém o sistema operativo e todos os binários do DBMS e/ou o SAP. Desde Março de 2015, este disco pode ser até 1TB de tamanho em vez de restrições anteriores que limitada-127 GB.
* Ficheiro da base de dados SAP de registo de um ou vários discos que contém o DBMS e o ficheiro de registo do DBMS armazenamento na área temp (se o DBMS suportar isto). Se os requisitos de IOPS de registo de base de dados são elevados, terá de stripe vários discos para atingir o volume IOPS necessário.
* Um número de discos que contém um ou dois ficheiros de base de dados da base de dados SAP e, bem como os ficheiros de dados temp DBMS (se o DBMS suportar isto).

![Configuração de referência da VM do IaaS do Azure para SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd TODO descrevem a estrutura do Linux)

- - -
> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes vimos configurações onde, por exemplo, SAP e DBMS binários não foram instalados na unidade c:\ onde foi instalado o sistema operativo. Ocorreram vários motivos para tal, mas quando iremos frequentou volta a raiz, normalmente, foi que as unidades foram pequenas e as atualizações de SO necessário espaço adicional 10 a 15 anos há. Ambas as condições não aplicam demasiado frequentemente já estes dias. Hoje em dia unidade c:\ pode ser mapeada em discos de grande volume ou VMs. Para manter as implementações simples na sua estrutura, recomenda-se a seguir o padrão de implementação seguinte para sistemas de SAP NetWeaver no Azure
>
> O ficheiro de paginação do sistema operativo Windows deve estar na unidade d: (não persistentes disco)
>
> ![Linux][Logo_Linux] Linux
>
> Coloque o swapfile Linux em /mnt/mnt/recursos no Linux, conforme descrito em [neste artigo][virtual-machines-linux-agent-user-guide]. O ficheiro de comutação pode ser configurado no ficheiro de configuração de /etc/waagent.conf o agente Linux. Adicionar ou alterar as seguintes definições:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, é necessário reiniciar o agente do Linux com

```
sudo service waagent restart
```

Leia a nota SAP [1597355] para obter mais detalhes sobre o tamanho do ficheiro de troca recomendada

- - -
O número de discos utilizados para os ficheiros de dados DBMS e o tipo de armazenamento do Azure estes discos estão alojados no deve ser determinado pelos requisitos de IOPS e a latência necessário. Quotas exatas descritas [neste artigo (Linux)] [ virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

Experiência de implementações de SAP ao longo dos últimos anos 2 taught-nos alguns lições que podem ser resumidas como:

* Tráfego IOPS para ficheiros de dados diferentes não é sempre o mesmo, uma vez que os sistemas de cliente existente poderão ter outro nome dado um tamanho ficheiros de dados que representa as respetivas bases de dados SAP. Como resultado, acabou para melhor utilizar uma configuração do RAID através de vários discos para colocar os ficheiros de dados que LUNs carved fora dos. Ocorreram situações, especialmente com o armazenamento padrão do Azure onde uma taxa IOPS atingiu a quota de um único disco contra o registo de transações DBMS. Tais cenários é recomendada a utilização do armazenamento Premium ou agregar em alternativa o armazenamento Standard vários discos com um software RAID.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Melhores práticas do desempenho para o SQL Server em Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Configurar Software RAID no Linux][virtual-machines-linux-configure-raid]
> * [Configurar LVM numa VM com Linux no Azure][virtual-machines-linux-configure-lvm]
> * [Os segredos de armazenamento do Azure e otimizações de e/s do Linux](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Armazenamento Premium está a ser mostrada significativo um melhor desempenho, especialmente para gravações de registo de transação críticos. Para cenários SAP que são esperados para a entrega de produção, tais como desempenho, é altamente recomenda utilizar série de VM que pode tirar partido do Premium Storage do Azure.

Tenha em atenção que o disco que contém o sistema operativo, e como recomendamos, os binários do SAP e a base de dados (base VM), já não faz limitada a 127GB. Agora pode ter até 1TB de tamanho. Deve ser espaço suficiente para manter todos os ficheiros necessários, incluindo, por exemplo, os registos da tarefa batch SAP.

Para mais sugestões e obter mais detalhes, especificamente para DBMS VMs, consulte o [DBMS guia de implementação][dbms-guide]

#### <a name="disk-handling"></a>Processamento de disco
Na maioria dos cenários tem de criar discos adicionais para implementar a base de dados SAP para a VM. Falamos sobre as considerações de número de discos de capítulo [estrutura de disco/VM para implementações de SAP] [ planning-guide-5.5.1] deste documento. O portal do Azure permite anexar e desanexar os discos depois de uma VM base é implementada. Os discos podem estar em execução, bem como quando está parado e expor/anular a exposição quando a VM está a funcionar. Ao anexar um disco, o portal do Azure oferece anexar um disco vazio ou um disco existente, que, neste ponto no tempo não está ligado a outra VM.

**Tenha em atenção**: discos só podem ser anexados a uma VM em qualquer momento.

![Anexar / desanexar os discos com o armazenamento padrão do Azure][planning-guide-figure-1400]

Durante a implementação de uma nova máquina virtual, pode decidir se pretende utilizar discos geridos ou coloque os discos em contas de armazenamento do Azure. Se pretender utilizar o Premium Storage, recomendamos que utilize discos geridos.

Em seguida, terá de decidir se pretende criar um disco novo e vazio ou se pretender selecionar um disco existente que foi carregado anteriormente e deve ser ligado à VM agora.

**IMPORTANTE**: **fazer não** pretende utilizar a colocação em cache do anfitrião com o armazenamento padrão do Azure. Deve deixar a preferência de Cache do anfitrião com a predefinição de nenhuma. Com o Premium Storage do Azure deverá ativar a colocação em cache de leitura se o característica de e/s é principalmente leitura como o tráfego de e/s normal contra os ficheiros de dados de base de dados. Em caso de ficheiro de registo de transação de base de dados se recomenda a sem colocação em cache.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal do Azure][virtual-machines-linux-attach-disk-portal]
>
> Se os discos estão anexados, terá de iniciar sessão na VM para abrir o Gestor de discos do Windows. Se não estiver ativada automount conforme recomendado nas capítulo [definição automount para discos ligados][planning-guide-5.5.3], tem de ser colocado online e inicializar o volume anexado recentemente.
>
> ![Linux][Logo_Linux] Linux
>
> Se os discos estão anexados, terá de iniciar sessão para a VM e inicializar os discos, conforme descrito em [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Se o novo disco for um disco vazio, tem de formatar o disco bem. Para a formatação, especialmente para ficheiros de registo e dados DBMS as mesmas recomendações relativamente às implementações bare-metal do DBMS aplicam-se.

Como já mencionados capítulo [o conceito de Máquina Virtual do Azure de Microsoft][planning-guide-3.2], uma conta de armazenamento do Azure fornece os recursos infinito em termos de volume de e/s, volume de dados e de IOPS. Normalmente, DBMS VMs são mais afetados por este problema. Poderá ser melhor utilizar uma conta de armazenamento separada para cada VM, se tiver o volume de e/s elevado algumas VMs para implementar para se manter no limite do volume de conta de armazenamento do Azure. Caso contrário, terá de ver como pode equilibrar estas VMs entre contas de armazenamento diferentes sem atingir o limite de cada conta de armazenamento única. Obter mais detalhes são abordados no [guia de implementação DBMS][dbms-guide]. Também devem ter estas limitações em mente para a aplicação de SAP pura server VMs ou outras VMs que exijam eventualmente VHDs adicionais. Estas restrições não se aplicam se utilizar disco geridos. Se planear utilizar o Premium Storage, recomendamos a utilização de disco gerido.

Outro tópico que é relevante para as contas de armazenamento é se os VHDs numa conta de armazenamento estão a obter georreplicação. A georreplicação está ativada ou desativada no nível da conta de armazenamento e não no nível da VM. Se a georreplicação está ativada, os VHDs dentro da conta de armazenamento seriam replicados no outro Datacenter do Azure na mesma região. Antes de decidir sobre isto, necessário pensar sobre a restrição seguinte:

Replicação geográfica do Azure funciona localmente em cada VHD numa VM e não replicar o IOs por ordem cronológica em vários VHDs numa VM. Por conseguinte, o VHD que representa a VM base, bem como quaisquer VHDs adicionais ligados à VM são replicados independentes entre si. Isto significa que não há nenhuma sincronização entre as alterações no VHD diferentes. O facto de que o IOs são replicados independentemente a ordem em que são escritos significa que a georreplicação não é de valor para os servidores de base de dados que tenham as bases de dados distribuídas por vários VHDs. Para além de DBMS, também poderão existir outras aplicações em que os processos de escrita ou manipular dados VHDs diferentes e onde é importante manter a ordem das alterações. Se for um requisito, não deve estar ativada georreplicação no Azure. Depende de se precisa ou pretender georreplicação para um conjunto de VMs, mas não para outro conjunto, pode já categorizar VMs e os seus VHDs relacionados para diferentes contas de armazenamento que têm a georreplicação ativada ou desativada.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Definição automount para discos ligados
- - -
> ![Windows][Logo_Windows] Windows
>
> Para VMs que são criadas a partir da própria imagens ou discos, é necessário verificar e, possivelmente, defina o parâmetro de automount. Definir este parâmetro irá permitir que a VM após um reinício ou a reimplementação no Azure para montar as unidades anexado/montadas novamente automaticamente.
> O parâmetro está definido para as imagens fornecidas pelo Microsoft no Azure Marketplace.
>
> Para definir o automount, consulte a documentação do executável da linha de comandos diskpart.exe aqui:
>
> * [Opções da linha de comandos DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](http://technet.microsoft.com/library/cc753703.aspx)
>
> A janela de linha de comandos do Windows deve ser aberta como administrador.
>
> Se os discos estão anexados, terá de iniciar sessão na VM para abrir o Gestor de discos do Windows. Se não estiver ativada automount conforme recomendado nas capítulo [definição automount para discos ligados][planning-guide-5.5.3], a recentemente anexado volume > tem de ser colocado online e ser inicializados.
>
> ![Linux][Logo_Linux] Linux
>
> Tem de inicializar um disco vazio anexado recentemente, conforme descrito em [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Também terá de adicionar novos discos para o /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Implementação final
Para a implementação final e os passos exatos, especialmente relativamente a implementação de SAP expandido monitorização, consulte o [guia de implementação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Aceder a sistemas SAP em execução em VMs do Azure
Para cenários apenas na nuvem, pode querer ligar a esses sistemas SAP através da internet pública através de SAP GUI. Para nestes casos, os procedimentos seguintes têm de ser aplicadas.

Mais tarde no documento, vamos abordar o outro cenário principais, ligar a sistemas SAP em implementações de vários locais que tenham uma ligação site a site (túnel VPN) ou Azure ExpressRoute ligação entre a sistemas no local e do Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto para sistemas SAP
Com o Azure Resource Manager existem não existem pontos finais predefinidos deixam de ser, como o modelo clássico anteriores. Todas as portas de uma VM de ARM do Azure estão abertas, desde que:

1. Nenhum grupo de segurança de rede está definido para a sub-rede ou a interface de rede. Tráfego de rede para as VMs do Azure pode estar protegido por so-called "grupos de segurança de rede". Para obter mais informações consulte [o que é um grupo de segurança de rede (NSG)?][virtual-networks-nsg]
2. Nenhum Balanceador de carga do Azure está definido para a interface de rede   

Consulte a diferença de arquitetura entre o modelo clássico e ARM, conforme descrito em [neste artigo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuração da conetividade SAP sistema e GUI do SAP para o cenário de apenas na nuvem
Consulte este artigo que descreve os detalhes deste tópico: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Alterar as definições da Firewall dentro da VM
Poderá ser necessário configurar a firewall no suas máquinas virtuais para permitir tráfego de entrada para o sistema SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Por predefinição, a Firewall do Windows dentro de uma VM do Azure do implementado é ativada. Agora tem de permitir que a porta de SAP ser aberta, caso contrário o GUI do SAP não será possível estabelecer ligação.
> Para efetuar este procedimento:
>
> * Abra o painel de controlo e segurança \ Firewall para **definições avançadas**.
> * Agora, faça duplo clique em regras de entrada e escolheu **nova regra**.
> * No Assistente de seguinte optar por criar um novo **porta** regra.
> * No próximo passo do assistente, deixe a TCP e escreva o número de porta que pretende abrir. Uma vez que a nossa ID de instância do SAP 00, pegámos 3200. Se a instância tem um número de instância diferente, a porta que é definido anteriormente com base no número de instância deve ser aberta.
> * A parte seguinte do assistente, terá de sair do item **permitir ligação** marcada.
> * No próximo passo do assistente tem de definir se a regra aplica-se a rede de domínio, privados e público. Ajuste-lo se for necessário para as suas necessidades. No entanto, ligar-se com SAP GUI a partir do exterior através da rede pública, tem de ter a regra aplicada para a rede pública.
> * No último passo do assistente, nome da regra e guardar, premindo **concluir**.
>
> A regra de entra em vigor imediatamente.
>
> ![Definição da regra de porta][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens de Linux no Azure Marketplace não ativar a firewall iptables por predefinição e a ligação ao sistema de SAP deverão funcionar. Se estiver ativada iptables ou outra firewall, consulte a documentação do iptables ou a firewall utilizada para permitir tráfego tcp de entrada para a porta 32xx (onde xx é o número de sistema do seu sistema SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Recomendações de segurança
O SAP GUI não ligar imediatamente para qualquer das instâncias do SAP (porta 32xx) que estão em execução, mas ligar pela primeira vez através da porta aberta para o processo de servidor de mensagem SAP (porta 36xx). No passado muito mesma porta foi utilizada pelo servidor de mensagem para a comunicação interna para as instâncias da aplicação. Para impedir que os servidores no local aplicação inadvertidamente comunique com um servidor de mensagens no Azure podem ser alteradas as portas de comunicação interno. É altamente recomendado para alterar a comunicação entre o servidor de mensagem SAP e respetivas instâncias de aplicação interna para um número de porta diferente em sistemas que foi clonado a partir de sistemas no local, como um clone de desenvolvimento para etc de teste do projeto. Pode fazê-lo com o parâmetro de perfil predefinido:

> rdisp/msserv_internal
>
>

conforme documentado no [definições de segurança para o servidor de mensagem de SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Conceitos de implementação apenas de nuvem de instâncias SAP
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>VM única com SAP NetWeaver demonstração/formação cenário
![Com sistemas de demonstração de VM SAP únicos com os mesmos nomes VM, isolada na Cloud Services do Azure][planning-guide-figure-1700]

Neste cenário (consulte o capítulo [apenas na nuvem] [ planning-guide-2.1] deste documento) estiver a implementar um cenário de sistema típico formação/demonstração onde o cenário de formação completa/demonstração está contido numa única VM. Iremos partem do princípio de que a implementação é feita através de modelos de imagem VM. Também partimos do pressuposto que múltiplo destas demonstração/trainings necessidade de VMs implementadas com as VMs terem o mesmo nome.

É do pressuposto de que criou uma imagem de VM, conforme descrito em algumas secções de capítulo [preparar VMs com SAP para o Azure] [ planning-guide-5.2] neste documento.

A sequência de eventos para implementar o cenário tem o seguinte aspeto:

##### <a name="powershell"></a>PowerShell
* Criar um novo grupo de recursos para cada horizontal de formação/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Criar uma nova conta de armazenamento, se não pretender utilizar discos geridos

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada horizontal de formação/demonstração ativar a utilização do mesmo nome de anfitrião e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permita que apenas o tráfego para a porta 3389 para ativar o acesso de ambiente de trabalho remoto e a porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser utilizado para aceder a máquina virtual a partir da internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Crie uma máquina virtual. Para o cenário de apenas na nuvem a cada VM terão o mesmo nome. O SID de SAP das instâncias do SAP NetWeaver dessas VMs será a mesma bem. Dentro do grupo de recursos do Azure, o nome da VM tem de ser exclusivo, mas em diferentes grupos de recursos do Azure pode executar as VMs com o mesmo nome. A conta de 'Administrador' predefinida do Windows ou raiz para Linux não são válidos. Por conseguinte, um novo nome de utilizador de administrador tem de ser definida, juntamente com uma palavra-passe. O tamanho da VM também tem de ser definido.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione mais discos e restaurar o conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) tem de ser exclusivos no Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>CLI
O código de exemplo seguinte pode ser utilizado no Linux. Para Windows utilize PowerShell conforme descrito acima ou adaptar o exemplo para utilizar % rgName % em vez de $rgName e defina a variável de ambiente utilizando o comando de Windows *definir*.

* Criar um novo grupo de recursos para cada horizontal de formação/demonstração

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Criar uma nova conta de armazenamento

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Crie uma nova rede virtual para cada horizontal de formação/demonstração ativar a utilização do mesmo nome de anfitrião e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permita que apenas o tráfego para a porta 3389 para ativar o acesso de ambiente de trabalho remoto e a porta 22 para SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser utilizado para aceder a máquina virtual a partir da internet

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Crie uma máquina virtual. Para o cenário de apenas na nuvem a cada VM terão o mesmo nome. O SID de SAP das instâncias do SAP NetWeaver dessas VMs será a mesma bem. Dentro do grupo de recursos do Azure, o nome da VM tem de ser exclusivo, mas em diferentes grupos de recursos do Azure pode executar as VMs com o mesmo nome. A conta de 'Administrador' predefinida do Windows ou raiz para Linux não são válidos. Por conseguinte, um novo nome de utilizador de administrador tem de ser definida, juntamente com uma palavra-passe. O tamanho da VM também tem de ser definido.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcionalmente, adicione mais discos e restaurar o conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) tem de ser exclusivos no Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modelo
Pode utilizar os modelos de exemplo no repositório de modelos de início rápido do azure no github.

* [Simples VM com Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [VM do Windows simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM a partir da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementar um conjunto de VMs que necessitam de comunicar no Azure
Este cenário apenas na nuvem é um cenário típico para formação e demonstração fins onde o software que representa a demonstração/formação cenário é distribuído por várias VMs. Os diferentes componentes instalados em VMs diferentes têm de comunicar entre si. Novamente, neste cenário comunicação de rede não no local ou cenário entre locais é necessária.

Este cenário é uma extensão de instalação descrita em capítulo [única VM com o SAP NetWeaver demonstração/formação cenário] [ planning-guide-7.1] deste documento. Neste caso mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo a seguir a horizontal de formação é composta por uma VM de ASCS/SCS SAP, uma VM com um DBMS e uma instância de servidor de aplicações SAP VM.

Antes de criar este cenário tem de pensar sobre como já executadas no cenário antes de definições básicas.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupo de recursos e a atribuição de nome de Máquina Virtual
Todos os nomes de grupo de recursos tem de ser exclusivos. Desenvolver o seu próprio esquema de nomenclatura dos seus recursos, tais como `<rg-name`>-sufixo.

Nome da máquina virtual tem de ser exclusivos dentro do grupo de recursos.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Configurar a rede para a comunicação entre as VMs diferentes
![Conjunto de VMs dentro de uma rede Virtual do Azure][planning-guide-figure-1900]

Para evitar colisões com clones das mesma landscapes de formação/demonstração de atribuição de nomes, tem de criar uma Azure Virtual Network para cada horizontal. Resolução de nomes DNS será fornecida pelo Azure ou pode configurar o próprio servidor DNS fora do Azure (não mais abordados aqui). Neste cenário, não configure nosso própria DNS. Todas as máquinas virtuais dentro de uma rede Virtual do Azure, será possível ativar a comunicação através de nomes de anfitrião.

As razões para separar landscapes de preparação ou de demonstração, redes virtuais e não apenas os grupos de recursos pode ser:

* Tem do horizontal SAP como configurar a suas próprias OpenLDAP/AD e um servidor de domínio deve fazer parte de cada uma dos landscapes.  
* O horizontal SAP como conjunto de cópias de segurança tem componentes que precisam para trabalhar com endereços IP fixos.

Podem encontrar mais detalhes sobre redes virtuais do Azure e como defini-las no [neste artigo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implementar o SAP VMs com conectividade de rede empresarial (em vários locais)
Executar um horizontal SAP e pretender dividir a implementação entre bare-metal para servidores DBMS gama alta de classe, ambientes no local virtualizado para as camadas da aplicação e mais pequenos de camada de 2 configurado IaaS do Azure e sistemas SAP. Pressuposto base é que os sistemas SAP dentro de um horizontal SAP têm de comunicar entre si e com muitos outros componentes de software implementadas na empresa, independentemente da respetiva formulário de implementação. Também não deverá haver nenhum diferenças introduzidas pelo formulário de implementação para o utilizador final ligar com SAP GUI ou outras interfaces. Estas condições só podem ser preenchidas quando criarmos os/OpenLDAP de diretório Active Directory no local e os serviços DNS expandidos para os sistemas através da conectividade de site-a-site/várias-site ou ligações privadas, como o Azure ExpressRoute do Azure.

Para obter mais em segundo plano nos detalhes de implementação do SAP no Azure, gostaríamos que experimentasse ler capítulo [implementação conceitos de Cloud-Only de instâncias SAP] [ planning-guide-7] deste documento para o qual explica algumas das construções Noções básicas do Azure e como estes devem ser utilizadas com aplicações SAP no Azure.

### <a name="scenario-of-an-sap-landscape"></a>Cenário de um horizontal SAP
O cenário entre locais pode aproximadamente descrito como gráficos abaixo:

![Conetividade site a Site no local e de recursos do Azure][planning-guide-figure-2100]

O cenário mostrado acima descreve um cenário onde no local AD/OpenLDAP e DNS é expandido para o Azure. No lado no local, um determinado intervalo de endereços IP é reservado por subscrição do Azure. O intervalo de endereços IP será atribuído a uma rede Virtual do Azure no lado do Azure.

#### <a name="security-considerations"></a>Considerações de segurança
O requisito mínimo é a utilização de protocolos de comunicação segura, como o SSL/TLS para acesso ao browser ou ligações baseados na VPN de acesso de sistema para os serviços do Azure. Pressuposto é que as empresas processam de forma muito diferente da ligação VPN entre a sua rede empresarial e o Azure. Algumas empresas blankly poderão abrir todas as portas. Algumas outras empresas podem querer muito precisas nas portas que precisam para abrir, etc.

Na tabela abaixo SAP típico portas de comunicação estão listadas. Basicamente é suficiente abrir a porta de gateway SAP.

| Serviço | Nome de porta | Exemplo `<nn`> = 01 | Intervalo predefinido (min máx.) | Comentário |
| --- | --- | --- | --- | --- |
| Distribuidor |sapdp`<nn>` consulte * |3201 |3200 - 3299 |Distribuidor SAP, utilizado por SAP GUI para Windows e o Java |
| Servidor de mensagens |sapms`<sid`> consulte * * |3600 |sapms livre`<anySID`> |SID = o ID de sistema de SAP |
| Gateway |sapgw`<nn`> consulte * |3301 |Livre |Gateway SAP, utilizada para comunicação CPIC e RFC |
| Router SAP |sapdp99 |3299 |Livre |Os nomes de serviço de CI (instância central) apenas podem ser reatribuídos em /etc/services para um valor arbitrário após a instalação. |

*) nn = número da instância do SAP

*) sid = o ID de sistema de SAP

Informações mais detalhadas sobre as portas necessárias para diferentes produtos SAP ou serviços por produtos SAP podem ser encontrados aqui <http://scn.sap.com/docs/DOC-17124>.
Com este documento, deve conseguir abrir portas dedicadas no dispositivo VPN necessário para cenários e produtos SAP específicos.

Outra segurança mede quando implementar VMs num cenário foi possível criar um [grupo de segurança de rede] [ virtual-networks-nsg] para definir as regras de acesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Lidar com a série de Máquina Virtual diferente
Durante os últimos 12 meses Microsoft adicionados muitos tipos VM mais diferem no número de vCPUs, memória ou mais importantes no hardware que é executada. Nem todos os dessas VMs são suportadas com SAP (consulte suportado tipos VM na nota SAP [1928533]). Algumas dessas VMS executam gerações de hardware de outro anfitrião. Estes gerações do hardware anfitrião obter implementadas na granularidade de uma Azure-unidade de escala. Casos de significa que podem surgir onde os tamanhos VM diferentes que escolheu não podem ser executados na mesma unidade de escala. Um conjunto de disponibilidade está limitado a capacidade de span baseada em hardware diferente de unidades de escala.  Por exemplo se pretender executar o DBMS em VMs A5 A11 e a camada da aplicação SAP em VMs de série de G, poderá ser forçado a implementar um único SAP ou sistemas SAP diferentes dentro de diferentes conjuntos de disponibilidade.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimir a uma impressora de rede local da instância do SAP no Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impressão através de TCP/IP no cenário entre locais
Configurar as impressoras de rede de TCP/IP com base no local na VM do Azure geral é o mesmo que a sua rede empresarial, partindo do princípio que tem um túnel VPN Site a Site ou a ligação do ExpressRoute estabelecida.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para efetuar este procedimento:
>
> * Algumas impressoras de rede são fornecidos com um Assistente de configuração que torna mais fácil de configurar a impressora na VM do Azure. Se nenhum software de assistente tiver sido distribuído com a impressora, a forma manual para configurar a impressora consiste em criar uma nova porta de impressora TCP/IP.
> * Abra o painel de controlo -> dispositivos e impressoras -> Adicionar uma impressora
> * Selecione adicionar uma impressora utilizando um endereço de TCP/IP ou nome de anfitrião
> * Escreva o endereço IP da impressora
> * Porta de impressora 9100 padrão
> * Se for necessário instale manualmente o controlador de impressora adequadas.
>
> ![Linux][Logo_Linux] Linux
>
> * como para seguir apenas do Windows, o procedimento para instalar uma impressora de rede padrão
> * Basta seguir os guias de Linux públicos para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat e Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.
>
>

- - -
![Impressão de rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada no anfitrião através de SMB (impressora partilhada) no cenário entre locais
Impressoras baseada no anfitrião não são compatíveis rede por predefinição. Mas uma impressora baseada no anfitrião pode ser partilhada entre os computadores numa rede, desde que a impressora está ligada a um computador com tecnologia no. Ligar a sua empresa de rede ou o Site para Site como o ExpressRoute e partilhar a impressora local. O protocolo SMB utiliza NetBIOS em vez de DNS como serviço de nomes. O nome de anfitrião NetBIOS pode ser diferente do nome de anfitrião DNS. As maiúsculas e minúsculas padrão é que o nome de anfitrião NetBIOS e o nome de anfitrião DNS são idênticos. O domínio DNS não fazer sentido no espaço de nome de NetBIOS. Em conformidade, o nome de anfitrião DNS completamente qualificado constituída pelo nome de anfitrião DNS e o domínio DNS não deve ser utilizado no espaço de nomes NetBIOS.

A partilha de impressoras é identificada por um nome exclusivo na rede:

* Nome de anfitrião do anfitrião SMB (sempre necessário).
* Nome da partilha (sempre necessário).
* Nome do domínio se partilhar a impressora não está no mesmo domínio que o sistema SAP.
* Além disso, um nome de utilizador e uma palavra-passe podem ser necessário para aceder à partilha de impressoras.

Como:

- - -
> ![Windows][Logo_Windows] Windows
>
> Partilhe a impressora local.
> Na VM do Azure, abra o Explorador do Windows e escreva o nome de partilha da impressora.
> Um Assistente de instalação da impressora irá ajudá-lo durante o processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Seguem-se alguns exemplos de documentação sobre configurar impressoras de rede no Linux ou, incluindo um capítulo relativos à impressão no Linux. Que vá funcionar da mesma forma na VM com Linux do Azure, desde que a VM faz parte de uma VPN:
>
> * SLES <_Share_or_Windows_Share https://en.opensuse.org/SDB:Printing_via_SMB_ (Samba)>
> * RHEL ou Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Impressora de USB (reencaminhamento impressoras)
No Azure a capacidade dos serviços de ambiente de trabalho remoto para fornecer aos utilizadores o acesso aos seus dispositivos de impressora local numa sessão remota não está disponível.

- - -
> ![Windows][Logo_Windows] Windows
>
> Obter mais detalhes sobre impressão com o Windows podem ser encontrados aqui: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração do SAP sistemas do Azure para a correção e o sistema de transporte (TMS) em vários locais
A alteração do SAP e sistema de transporte (TMS) tem de ser configurado para exportar e importar o pedido de transporte através de sistemas a horizontal. Iremos partem do princípio de que as instâncias de desenvolvimento de um sistema SAP (desenvolvimento) estão localizadas no Azure, enquanto a garantia de qualidade (pergunta e resposta) e sistemas produtivos (PRD) estão no local. Além disso, partimos do princípio que há um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurar o domínio de transporte
Configurar o seu domínio de transporte no sistema designado como o controlador de domínio de transporte conforme descrito em [configurar o controlador de domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Serão gerados um utilizador do sistema que TMSADM será criado e o destino de RFC necessário. Pode verificar estas ligações de RFC com a transação SM59. Resolução de nome de anfitrião deve ser ativada através do seu domínio de transporte.

Como:

* No nosso cenário decidimos que o sistema QAS no local será o controlador de domínio de CTS. Chame transação STMS. É apresentada a caixa de diálogo TMS. É apresentada uma caixa de diálogo de configurar o domínio de transporte. (Esta caixa de diálogo só é apresentada se ainda não tiver configurado a um domínio de transporte.)
* Certifique-se de que o utilizador automaticamente criado TMSADM está autorizado (SM59 -> ABAP ligação -> TMSADM@E61.DOMAIN_E61 -> detalhes -> Utilities(M) -> autorização teste). No ecrã inicial de transação STMS deve mostrar que este sistema de SAP está agora a funcionar como controlador de domínio do transporte conforme mostrado aqui:

![Ecrã inicial de transação STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte
A sequência, incluindo um sistema SAP num domínio transporte procura da seguinte forma:

* No sistema de desenvolvimento no Azure, vá para o sistema de transporte (cliente 000) e chamada de transação STMS. Escolha outra configuração na caixa de diálogo e continuar com o sistema incluem no domínio. Especificar o controlador de domínio como o anfitrião de destino ([, incluindo sistemas SAP no domínio transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema está agora a aguardar para ser incluído no domínio de transporte.
* Por motivos de segurança, em seguida, terá de voltar para o controlador de domínio para confirmar o pedido. Escolha a descrição geral do sistema e aprovar do sistema de espera. Em seguida, confirme o pedido e a configuração serão distribuídas.

Agora, este sistema SAP contém as informações necessárias sobre todos os outros sistemas SAP o domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados para todos os outros sistemas SAP e o sistema SAP é introduzido no perfil de transporte do programa de controlo de transporte. Verifique se RFCs e acesso ao diretório de transporte do domínio de trabalho.

Continuar com a configuração do seu sistema de transporte como habitualmente, tal como descrito na documentação do [alteração e do sistema de transporte](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Como:

* Certifique-se a que sua STMS no local está configurado corretamente.
* Certifique-se de que o nome de anfitrião do controlador de domínio de transporte pode ser resolvido pela máquina virtual no Azure e vice-versa visa.
* Chamada de transação STMS -> configuração outros incluem-> sistema-no domínio.
* Confirme a ligação no sistema TMS de no local.
* Configure camadas, grupos e rotas de transporte como habitualmente.

No site para site ligados em vários locais cenários, a latência entre no local e o Azure ainda podem ser significativo. Se iremos seguir a sequência de transporte objetos através de programação e sistemas para produção de teste ou em consideração aplicar transportes ou pacotes para diferentes sistemas de suporte, tenha em atenção que, depende da localização do diretório central de transporte, alguns dos sistemas irão encontrar latência elevada ler ou escrever dados no diretório central do transporte. A situação é semelhante a configurações de horizontal SAP onde os diferentes sistemas são distribuídos através de centros de dados diferentes com substancial distância entre centros de dados.

Para contornar essa latência e tem os sistemas rápida no ler ou escrever ou do diretório de transporte de trabalho, pode configurar dois domínios de transporte STMS (uma para no local e outra com os sistemas no Azure e os domínios de transporte de ligação. Verifique esta documentação para o qual explica os princípios envolvidos este conceito no TMS SAP nos: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Como:

* Configurar um domínio de transporte em cada localização (no local e do Azure) utilizando a transação STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Ligue os domínios com uma ligação de domínio e confirme a ligação entre os dois domínios.
  <http://help.SAP.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/Content.htm>
* Distribua a configuração para o sistema ligado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizado no Azure e no local (em vários locais)
Necessidades de tráfego RFC entre os sistemas que estão no local e no Azure trabalhar. Para configurar uma transação de chamada de ligação SM59 num sistema de origem em que é necessário definir uma ligação de RFC para o sistema de destino. A configuração é semelhante para a configuração padrão de uma ligação de RFC.

Iremos partem do princípio de que o cenário de vários locais, as VMs no mesmo domínio que executam sistemas SAP que têm de comunicar entre si. Por conseguinte o programa de configuração de uma ligação de RFC entre sistemas SAP não diferem dos passos de configuração e entradas em cenários no local.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Ao aceder aos fileshares local das instâncias de SAP localizadas no Azure ou vice versa
Instâncias SAP localizadas no Azure devem ter acesso a partilhas de ficheiros que estão dentro do local empresarial. Além disso, as instâncias SAP no local tem de aceder a partilhas de ficheiros que estão localizadas no Azure. Para ativar as partilhas de ficheiros tem de configurar as permissões e as opções de partilha no sistema local. Certifique-se abrir as portas na ligação VPN ou ExpressRoute entre o Azure e o seu centro de dados.

## <a name="supportability"></a>Suportabilidade
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure solução de monitorização para SAP
Para ativar a monitorização dos sistemas SAP crítico de missão no Azure SAP ferramentas SAPOSCOL ou SAP o agente do anfitrião de monitorização obter dados desligar o anfitrião do serviço de Máquina Virtual do Azure através de uma extensão de monitorização do Azure para SAP. Uma vez que os pedidos por SAP foram muito específicos para aplicações de SAP, Microsoft decidiu não genericamente implementa a funcionalidade necessária para o Azure, mas deixe-o para que os clientes implementem os componentes de monitorização necessários e configurações de às máquinas virtuais em execução no Azure. No entanto, o ciclo de vida de implementação e gestão dos componentes de monitorização irá ser principalmente automatizada pelo Azure.

#### <a name="solution-design"></a>Design de solução
A solução desenvolvida para ativar a monitorização de SAP baseia-se a arquitetura do agente da VM do Azure e a arquitetura de extensão. A ideia da estrutura do agente da VM do Azure e a extensão é permitir a instalação de aplicações de software disponíveis na galeria da extensão da VM do Azure numa VM. A ideia princípio atrás este conceito é permitir que (em casos como a extensão de monitorização do Azure para SAP), a implementação da funcionalidade especial para uma VM e a configuração desse software no momento da implementação.

O 'Azure agente da VM' que permite o processamento de extensões de VM do Azure específicas dentro da VM é injetado VMs do Windows por predefinição na criação de VM no portal do Azure. Em caso de SUSE, Red Hat ou Oracle Linux, o agente da VM já faz parte da imagem do Azure Marketplace. No caso de um seria carregue uma VM com Linux no local para o Azure tem de ser instalado manualmente o agente da VM.

Os blocos modulares básicos da solução de monitorização no Azure para SAP procura como esta:

![Componentes de extensão do Microsoft Azure][planning-guide-figure-2400]

Como é mostrado no diagrama de blocos acima, uma parte da solução de monitorização para SAP está alojada na imagem de VM do Azure e Galeria de extensão do Azure que é um repositório replicado globalmente, que é gerido por operações do Azure. É da responsabilidade do agrupamento SAP/MS conjunta a implementação do Azure do SAP para trabalhar com as operações do Azure para publicar novas versões da extensão de monitorização do Azure para SAP a trabalhar.

Ao implementar uma nova VM do Windows, o agente da VM do Azure é automaticamente adicionado à VM. A função deste agente é coordenar a carregar e a configuração das extensões do Azure para a monitorização do SAP NetWeaver sistemas. Para VMs com Linux o agente da VM do Azure já faz parte da Azure Marketplace da imagem do SO.

No entanto, é um passo que ainda tem de ser executada pelo cliente. Esta é a ativação e a configuração de recolha de desempenho. O processo relacionado com a configuração é um processo automatizado por um script do PowerShell ou comando da CLI. Pode ser transferido o script do PowerShell Azure Microsoft Script Center, conforme descrito no [guia de implementação][deployment-guide].

A arquitetura geral da solução de monitorização do Azure para SAP tem o seguinte:

![Solução de monitorização para SAP NetWeaver do Azure][planning-guide-figure-2500]

**Para os procedimentos exato e para obter passos detalhados de utilizar estes cmdlets do PowerShell ou comando da CLI durante as implementações, siga as instruções indicadas no [guia de implementação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração do Azure localizado instância do SAP para SAProuter
Instâncias SAP em execução no Azure tem de ser acessível a partir do SAProuter bem.

![Ligação de rede de SAP Router][planning-guide-figure-2600]

Um SAProuter permite a comunicação de TCP/IP entre sistemas participantes se não houver nenhuma ligação direta do IP. Isto fornece a vantagem que não é necessária nenhuma ligação de ponto a ponto entre os parceiros de comunicação no nível de rede. O SAProuter está a escutar na porta 3299 por predefinição.
Para ligar a instâncias SAP através de um SAProuter é necessário dar o nome de cadeia e o anfitrião de SAProuter com qualquer tentativa de ligar.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Até ao momento o foco do documento foi SAP NetWeaver em geral ou ABAP de NetWeaver SAP da pilha. Nesta secção pequeno, são listadas considerações específicas para a pilha de SAP Java. Uma das aplicações mais importantes do SAP NetWeaver Java exclusivamente com base é SAP Enterprise Portal. Outros SAP NetWeaver com base em aplicações como o SAP PI e SAP solução Manager utilizar o SAP NetWeaver ABAP e de pilhas de Java. Por conseguinte, certamente for necessário a ter em consideração aspetos específicos relacionados com a pilha do SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>Portal da empresa SAP
O programa de configuração de um Portal de SAP uma Máquina Virtual no Azure não diferem dos de uma instalação local no se estiver a implementar em cenários de vários locais. Uma vez que o DNS é feito no local, as definições de porta instâncias individuais da podem ser feitas como configurado no local. As restrições descritas neste documento, até ao momento e recomendações aplicam-se para uma aplicação, como o SAP Enterprise Portal ou a pilha do SAP NetWeaver Java em geral.

![Portal de expostas SAP][planning-guide-figure-2700]

Um cenário de implementação especiais por alguns clientes é a exposição direta do Portal da empresa do SAP à Internet enquanto o anfitrião de máquina virtual está ligado à rede da empresa através do túnel VPN de site para site ou ExpressRoute. Para um cenário, tem de certificar-se de que portas específicas estão abertas e não bloqueadas por grupo de segurança de rede ou firewall. O mesmo mechanics seria necessária a aplicar quando pretende ligar a uma instância de SAP Java no local num cenário apenas na nuvem.

O URI de portal inicial é http (s):`<Portalserver`>: 5XX00/irj onde a porta está formada por 50000 mais (Systemnumber?? 100). O sistema de URI do SAP portal predefinida 00 `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. Para obter mais detalhes, tem uma vista de olhos <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuração de ponto final][planning-guide-figure-2800]

Se pretender personalizar o URL de e/ou as portas do Portal da empresa do SAP, verifique esta documentação:

* [Alterar o URL do Portal](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar os números de porta predefinidos, números de porta do Portal](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Elevada disponibilidade (ed) e recuperação de desastre (DR) para NetWeaver SAP em execução em Azure Virtual Machines
### <a name="definition-of-terminologies"></a>Definição de terminologies
O termo **elevada disponibilidade (HA)** geralmente está relacionado com um conjunto de tecnologias que minimiza interrupções de IT, fornecendo a continuidade do negócio de serviços de TI através de redundante, com tolerância a falhas ou componentes de protegido de ativação pós-falha dentro do **mesmo** Centro de dados. No nosso caso, dentro de uma região do Azure.

**Recuperação após desastre (DR)** está também direcionado para minimizar perturbações de serviços IT e os respetivos recuperação mas across **diferentes** centros de dados, que são normalmente localizada centenas de kilometers ausente. No nosso caso, normalmente, entre diferentes regiões do Azure na mesma região geopolítica ou como estabelecida por si como um cliente.

### <a name="overview-of-high-availability"></a>Descrição geral da elevada disponibilidade
Iremos pode separar o debate sobre SAP elevada disponibilidade no Azure em duas partes:

* **Elevada disponibilidade da infraestrutura do Azure**, por exemplo HA de (VMs) de computação, rede, armazenamento, etc. e a respetivos benefícios para aumentar a disponibilidade de aplicações SAP.
* **Disponibilidade elevada da aplicação do SAP**, por exemplo os componentes de software do HA do SAP:
  * Servidores de aplicações SAP
  * Instância do SAP ASCS/SCS
  * Servidor de base de dados

e como pode ser combinado com a infraestrutura do Azure HA.

SAP elevada disponibilidade no Azure tem algumas diferenças em comparação com a elevada disponibilidade num ambiente físico ou virtual no local. O documento seguinte do SAP descreve padrão configurações de SAP elevada disponibilidade em ambientes virtualizados no Windows: <http://scn.sap.com/docs/DOC-44415>. Não está integrado em sapinst SAP-HA uma configuração para Linux, tal como existe para o Windows. Relativamente à HA do SAP no local para Linux encontrar mais informações aqui: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Elevada disponibilidade da infraestrutura do Azure
Não há atualmente um SLA de VM única de 99,9%. Para obter uma ideia como a disponibilidade de uma única VM poderá ter o aspeto como pode simplesmente criar o produto do SLA do Azure disponíveis diferentes: <https://azure.microsoft.com/support/legal/sla/>.

A base para o cálculo é 30 dias, por mês ou 43200 minutos. Por conseguinte, o período de indisponibilidade 0,05% corresponde a 21.6 minutos. Normalmente, a disponibilidade dos serviços diferentes será multiplicar da seguinte forma:

(Serviço de disponibilidade #1/100) * (serviço de disponibilidade #2/100) * (serviço de disponibilidade #3/100) 

Como:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 ou um disponibilidade global da 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Elevada disponibilidade de máquina virtual (VM)
Existem dois tipos de eventos de plataforma Azure que podem afetar a disponibilidade das máquinas virtuais: a ser planeada a manutenção e a manutenção não planeada.

* Manutenção planeada eventos são atualizações periódicas foi criadas pela Microsoft para a plataforma subjacente do Azure para melhorar a fiabilidade, desempenho e segurança da infraestrutura da plataforma que executam as máquinas virtuais em geral.
* Eventos de manutenção não planeada ocorrerem quando o hardware ou a infraestrutura física subjacente a máquina virtual falhou de alguma forma. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando é detetada uma falha de tal, a plataforma do Azure irá migrar automaticamente a máquina virtual do servidor físico mau estado de funcionamento que aloja a máquina virtual para um servidor físico em bom estado. Estes eventos são raros, mas também podem provocar o reinício da máquina virtual.

Podem encontrar mais detalhes nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância do armazenamento do Azure
Os dados na sua conta de armazenamento do Microsoft Azure estão sempre replicados para garantir durabilidade e elevada disponibilidade, a cumprir o SLA de armazenamento do Azure, mesmo face a falhas de hardware transitórias.

Uma vez que o armazenamento do Azure é manter os três imagens dos dados por predefinição, RAID5 ou RAID1 entre múltiplos discos do Azure não são necessários.

Podem encontrar mais detalhes neste artigo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>A utilização de reinício VM de infraestrutura do Azure para alcançar a elevada disponibilidade de aplicações SAP
Se optar por não utilizar funcionalidades como o Clustering de ativação pós-falha de servidor ao Windows ' (WSFC) ou Pacemaker no Linux (atualmente só é suportado para o SLES 12 e superior), reinicie de VM do Azure é utilizado para proteger um sistema de SAP contra períodos de indisponibilidade planeado e imprevisto do Azure infraestrutura de servidor físico e global plataforma Azure subjacente.

> [!NOTE]
> É importante mencionar que principalmente reiniciar de VM do Azure protege VMs e não aplicações. Reinício de VM não oferece elevada disponibilidade para aplicações SAP, mas oferecem um determinado nível de disponibilidade da infraestrutura e, por conseguinte, indiretamente maior disponibilidade dos sistemas SAP. Não há também nenhum SLA para o tempo demorará para reiniciar uma VM após uma falha de anfitrião planeadas ou imprevistas. Por conseguinte, este método de elevada disponibilidade não é adequado para componentes mais importantes de um sistema SAP como (A) SCS ou DBMS.
>
>

Outro elemento importante da infraestrutura de elevada disponibilidade é armazenamento. Por exemplo o SLA de armazenamento do Azure é disponibilidade mínima de 99,9%. Se um implementa todas as VMs com os respetivos discos para uma conta do Storage do Azure único, potenciais indisponibilidade fará com que a indisponibilidade de todas as VMs que são colocadas nessa conta do Storage do Azure e também todos os SAP componentes que estejam dessas VMs em execução do armazenamento do Azure.  

Em vez de colocando todas as VMs para uma única conta de armazenamento do Azure, também pode utilizar o armazenamento dedicado contas para cada VM e, desta forma aumentam a disponibilidade geral de VM e SAP da aplicação através da utilização de várias contas do Storage do Azure independentes.

Discos gerida do Azure são automaticamente colocados no domínio de falhas da máquina virtual que estão ligados a. Se colocar duas máquinas virtuais na disponibilidade de um conjunto e utilizar discos geridos, a plataforma tratará de distribuir os discos gerido em diferentes domínios de falhas bem. Se planear utilizar o Premium Storage, recomendamos vivamente utilizando discos de gerir também.

Um exemplo de arquitetura de um sistema de SAP NetWeaver que utiliza as contas de armazenamento e HA da infraestrutura do Azure pode ter o seguinte aspeto:

![Utilizar a infraestrutura do Azure HA para alcançar a elevada disponibilidade do SAP aplicação][planning-guide-figure-2900]

Um exemplo de arquitetura de um sistema de SAP NetWeaver que utiliza a infraestrutura do Azure HA e discos geridos foi ter este aspeto:

![Utilizar a infraestrutura do Azure HA para alcançar a elevada disponibilidade do SAP aplicação][planning-guide-figure-2901]

Para componentes mais importantes do SAP foi alcançado o seguinte até ao momento:

* Elevada disponibilidade dos servidores de aplicações SAP (AS)

  Instâncias de servidor de aplicações SAP são os componentes redundantes. Cada SAP como instância está implementada na sua própria VM, que está a ser executado noutra falhas do Azure e o domínio de atualização (consulte capítulos [domínios de falhas] [ planning-guide-3.2.1] e [atualizar domínios][planning-guide-3.2.2]). Isto é certificar-se ao utilizar conjuntos de disponibilidade do Azure (consulte o capítulo [conjuntos de disponibilidade do Azure][planning-guide-3.2.3]). Potencial indisponibilidade planeada ou não planeada de um índice de falhas do Azure ou o domínio de atualização irá fazer com que indisponibilidade de um número restrito de VMs com as respetivas SAP AS instâncias.

  Cada SAP como instância é colocada na sua própria conta do Storage do Azure - indisponibilidade potencial de uma conta de armazenamento do Azure irá fazer com que indisponibilidade de apenas uma VM com AS respetivas SAP instância. No entanto, tenha em atenção de que há um limite de contas do Storage do Azure dentro de uma subscrição do Azure. Para garantir o início automático da instância (A) SCS após o reinício VM, certifique-se definir o parâmetro Autostart na instância (A) SCS iniciar perfil descrito capítulo [utilizando Autostart para instâncias SAP][planning-guide-11.5].
  Leia também capítulo [elevada disponibilidade para servidores de aplicações SAP] [ planning-guide-11.4.1] para obter mais detalhes.

  Mesmo que utilize discos geridos, os discos também são armazenados numa conta de armazenamento do Azure e podem não estar disponíveis um evento de uma falha de armazenamento.

* *Superior* instância SCS de disponibilidade do SAP (A)

  Aqui vamos utilizar o reinício de VM do Azure para proteger a VM com a instância SCS SAP (A) instalada. No caso de um período de indisponibilidade planeado ou não planeado do Azure servidores, VMs irá ser reiniciada noutro servidor disponível. Conforme mencionado anteriormente, principalmente reiniciar de VM do Azure protege VMs e não aplicações nesta instância do SCS de maiúsculas e minúsculas (A). Através de reiniciar a VM que irá aceder indiretamente maior disponibilidade da instância do SCS SAP (A). Para assegurar o início automático da instância (A) SCS após o reinício VM, certifique-se definir o parâmetro de início automático (A) perfil de início de instância SCS descrito capítulo [utilizando Autostart para instâncias SAP][planning-guide-11.5]. Isto significa que o (A) SCS instância como um único ponto de falha (SPOF) em execução numa única VM será o fator determinative a disponibilidade de horizontal SAP todo.

* *Superior* disponibilidade de servidor DBMS

  Aqui, semelhante para o caso de utilização do SAP (A) SCS instância, vamos utilizar reiniciar de VM do Azure para proteger a VM com o software instalado DBMS e iremos alcançar a elevada disponibilidade do software DBMS através de reiniciar a VM.
  DBMS em execução numa VM única também é um SPOF e é o fator determinative a disponibilidade de horizontal SAP todo.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Aplicação de SAP elevada disponibilidade no IaaS do Azure
Para alcançar completa SAP sistema elevada disponibilidade, é necessário proteger críticos de todos os componentes do sistema SAP, para servidores de aplicações do exemplo redundantes SAP e exclusivos componentes (por exemplo único ponto da falha) como instância SCS SAP (A) e o DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Elevada disponibilidade para servidores de aplicações SAP
Para as instâncias de caixa de diálogo/servidores de aplicações SAP não é necessário pensar sobre uma solução de elevada disponibilidade específico. Elevada disponibilidade simplesmente é conseguida através da redundância e assim ter espaço suficiente-las nos diferentes máquinas virtuais. Eles devem todos ser colocados no mesmo Azure conjunto de disponibilidade para evitar que as VMs podem ser atualizadas ao mesmo tempo durante o período de indisponibilidade de manutenção planeada. A funcionalidade básica que baseia-se em diferentes domínios de falhas dentro de uma unidade de escala do Azure e a atualização já foi introduzida no capítulo [atualizar domínios][planning-guide-3.2.2]. Conjuntos de disponibilidade do Azure eram apresentados no capítulo [conjuntos de disponibilidade do Azure] [ planning-guide-3.2.3] deste documento.

Não há nenhum infinito número de falhas e domínios de atualização de mensagens em fila que podem ser utilizados por um conjunto de disponibilidade do Azure dentro de uma unidade de escala do Azure. Isto significa que colocar um número de VMs para um conjunto de disponibilidade, mais cedo ou posterior mais do que uma que VM termina no mesmo domínio de atualização ou falhas.

A implementar algumas instâncias de servidor de aplicações SAP no respetivos VMs dedicadas e partindo do princípio que obtivemos cinco domínios de atualização, a imagem seguinte emerges no final. O número real de máximo de domínios de falhas e atualização dentro de um conjunto de disponibilidade poderá alterar no futuro:

![HA dos servidores de aplicações SAP no Azure][planning-guide-figure-3000]

Podem encontrar mais detalhes nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Elevada disponibilidade para a instância SCS SAP (A) no Windows
Cluster de ativação pós-falha de servidor do Windows (WSFC) é uma solução frequentemente utilizada para proteger a instância do SCS SAP (A). Também está integrado em sapinst no formulário de uma instalação"HA". Neste ponto no tempo a infraestrutura do Azure não é capaz de fornecer a funcionalidade para configurar o Cluster de ativação pós-falha do Windows Server necessárias da mesma forma como foi feito no local.

A partir de Janeiro de 2016 a plataforma de nuvem do Azure com o sistema operativo Windows não fornece a possibilidade de utilizar um volume partilhado de cluster num disco partilhado entre duas VMs do Azure.

Uma solução válida é embora a utilização do software de terceiros 3rd que fornece um volume partilhado através da replicação de disco síncrona e transparente, que pode ser integrada no WSFC. Esta abordagem implica que apenas o nó de cluster ativo é capaz de aceder a um das cópias do disco num ponto no tempo. A partir de Janeiro de 2016 este HA configuração é suportada para proteger a instância SCS SAP (A) no Windows SO convidado em VMs do Azure em combinação com o software de terceiros 3rd SIOS DataKeeper.

A solução de SIOS DataKeeper fornece um recurso de cluster de disco partilhado para Clusters de ativação pós-falha do Windows, fazendo com que:

* Um VHD adicionais do Azure anexados a cada uma das máquinas virtuais (VMs) que estão numa configuração de Cluster do Windows
* SIOS DataKeeper Cluster Edition em execução em ambos os nós VM
* Ter SIOS DataKeeper Cluster Edition configurado de forma que em sincronia reflete o conteúdo do VHD adicional ligado ao volume de VMs de origem adicionais VHD ligado volume de VM de destino.
* SIOS DataKeeper é abstrair os volumes locais de origem e de destino e apresente-los ao Cluster de ativação pós-falha do Windows como um único disco partilhado.

Pode encontrar todos os detalhes sobre como instalar um Cluster de ativação pós-falha do Windows com SIOS DataKeeper e SAP no [Clustering SAP ASCS instância utilizando o Cluster de ativação pós-falha do Windows Server no Azure com SIOS DataKeeper] [ ha-guide-classic] documento técnico.

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Elevada disponibilidade para a instância do SAP (A) SCS no Linux
A partir de 2015 Dec também não há nenhum equivalente ao disco partilhado WSFC para VMs com Linux no Azure. Soluções alternativas utilizando software de terceiros 3rd como SIOS para Windows não são validadas ainda para executar o SAP em Linux no Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Elevada disponibilidade para a instância de base de dados SAP
Típica SAP DBMS HA configuração baseia-se em duas VMs de DBMS onde a funcionalidade de elevada disponibilidade DBMS é utilizada para replicar dados na instância DBMS Active Directory para a segunda VM numa instância DBMS passiva.

Funcionalidade de recuperação após desastre e elevada disponibilidade para o DBMS em geral, bem como específicos DBMS descritas o [guia de implementação DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Elevada disponibilidade de ponto a ponto para o sistema SAP concluída
Seguem-se dois exemplos concluída SAP NetWeaver HA da arquitetura de no Azure - uma para o Windows e outro para Linux.

Não gerido discos apenas: os conceitos conforme explicado abaixo poderão ter de ser um pouco comprometidos ao implementar vários sistemas de SAP e o número de VMs implementadas está a exceder o limite máximo de contas do Storage por subscrição. Nestes casos, os VHDs das VMs, tem de ser combinados de uma conta de armazenamento. Normalmente, deverá fazê-lo através da combinação de camada de aplicação de VHDs de SAP VMs dos diferentes sistemas SAP.  Podemos também combinadas VHDs diferentes das VMs DBMS diferentes sistemas diferentes de SAP numa conta de armazenamento do Azure. Deste modo, mantendo os limites IOPS de contas do Storage do Azure em mente (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA no Windows
![Arquitetura de HA do SAP NetWeaver aplicações com o SQL Server em IaaS do Azure][planning-guide-figure-3200]

As construções de Azure seguintes são utilizadas para o sistema do SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e alojar a aplicação de patches:

* O sistema completo é implementado no Azure (é necessário - camada DBMS, (A) instância SCS e a camada de aplicação completa têm de executar na mesma localização).
* O sistema completo é executado dentro de uma subscrição do Azure (obrigatório).
* O sistema completo é executado dentro de uma Azure Virtual Network (obrigatório).
* É possível a separação de VMs de um sistema SAP em três conjuntos de disponibilidade, mesmo com todas as VMs que pertencem à mesma rede Virtual.
* Todas as VMs instâncias em execução DBMS de um sistema SAP estão a ser um conjunto de disponibilidade. Iremos partem do princípio de que não há mais do que um VM a executar instâncias DBMS por sistema desde nativa DBMS elevada disponibilidade funcionalidades são utilizadas, como o AlwaysOn do SQL Server ou o Oracle Data Guard.
* Todas as VMs DBMS instâncias em execução utilizam a sua própria conta de armazenamento. Ficheiros de registo e dados DBMS são replicados a partir de uma conta de armazenamento para outra conta de armazenamento utilizando as funções de elevada disponibilidade DBMS sincronizar os dados. Indisponibilidade de uma conta de armazenamento fará com que a indisponibilidade de um nó de cluster do Windows do SQL Server, mas não o serviço SQL Server todo.
* Todas as VMs em execução (A) instância SCS de um sistema SAP estão a ser um conjunto de disponibilidade. Um Cluster de ativação pós-falha Windows Server (WSFC) está configurado no interior dessas VMs para proteger o (A) instância SCS.
* Todas as VMs em execução (A) instâncias SCS utilizam a sua própria conta de armazenamento. (A) Ficheiros de instância do SCS e pasta global SAP são replicadas a partir de uma conta de armazenamento para outra conta de armazenamento utilizando a replicação de SIOS DataKeeper. Indisponibilidade de uma conta de armazenamento fará com que a indisponibilidade de um nó de cluster (A) SCS Windows, mas não o inteiro (A) serviço SCS.
* TODAS as VMs que representa a camada de servidor de aplicações SAP é numa terceira conjunto de disponibilidade.
* TODAS as VMs com servidores de aplicações SAP utilizam a sua própria conta de armazenamento. Indisponibilidade de uma conta de armazenamento fará com que a indisponibilidade de um servidor de aplicação de SAP, onde AS outras SAP continua a ser executado.

A figura seguinte ilustrado a mesma horizontal utilizando discos geridos.

![Arquitetura de HA do SAP NetWeaver aplicações com o SQL Server em IaaS do Azure][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA no Linux
A arquitetura para SAP HA no Linux no Azure é basicamente o mesmo para Windows, tal como descrito acima. A partir de Janeiro de 2016, não há nenhuma solução SAP (A) SCS HA ainda suportada no Linux no Azure

Como um consequence a partir de Janeiro de 2016 um sistema de SAP Linux-do Azure não é possível alcançar a mesmo disponibilidade como um sistema de SAP-Windows-Azure devido à falta HA SCS o (A) instância e a base de dados do SAP ASE de instância única.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Utilizar o início automático para instâncias SAP
SAP oferecidos a funcionalidade para iniciar as instâncias SAP imediatamente após o início do SO dentro da VM. Os passos exatos documentados no artigo da Base de dados de conhecimento SAP [1909114]. No entanto, SAP é não recomendamos a utilizar a definição já porque não existe nenhum controlo pela ordem dos reinícios de instância, partindo do princípio de mais do que uma VM obteve afetado ou foi executada várias instâncias por VM. Partindo do princípio de um cenário típico do Azure de uma instância de servidor de aplicações SAP uma VM e o caso de uma única VM, eventualmente, obter reiniciado, o início automático não está realmente crítico e pode ser ativado adicionando este parâmetro:

    Autostart = 1

Para o perfil de início da instância do SAP ABAP e/ou de Java.

> [!NOTE]
> O parâmetro Autostart pode ter alguns downfalls bem. Em mais detalhe, o parâmetro aciona o início de uma instância de Java ou SAP ABAP quando é iniciado o serviço de Windows/Linux relacionado da instância. Certamente for esse o caso quando arrancar o sistema operativo. No entanto, reinícios dos serviços SAP também uma coisa comuns para funcionalidades de gestão de ciclo de vida do Software de SAP como soma ou outras atualizações ou atualiza. Estas funcionalidades não estão à espera de uma instância será reiniciado automaticamente em todos os. Por conseguinte, o parâmetro Autostart deve ser desativado antes de executar estas tarefas. O parâmetro Autostart também não deve ser utilizado para as instâncias SAP estão agrupados em cluster, como SCS/ASCS/CI.
>
>

Consulte informações adicionais sobre o início automático para SAP instâncias aqui:

* [Iniciar/parar SAP, juntamente com o seu servidor Unix iniciar/parar](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciar e parar os agentes de gestão do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como ativar automaticamente iniciar da base de dados HANA](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Maior sistemas SAP de 3 camadas
Aspetos de elevada disponibilidade das configurações de SAP de 3 camadas já obteve abordados nas secções anteriores. Mas e sobre os sistemas onde os requisitos de servidor DBMS são demasiado grandes para que o localizado no Azure, mas a camada de aplicação do SAP podem ser implementados no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização das configurações de SAP de 3 camadas
Não é suportada para dividir a camada de aplicação de camada de si próprio ou a aplicação e DBMS entre no local e o Azure. Um sistema SAP é totalmente implementado no local ou no Azure. Também não é suportado para ter alguns dos servidores de aplicação a ser executado no local e outros no Azure. Que é o ponto de partida do debate. Podemos também são não suporta para que os componentes DBMS de um sistema SAP e a camada de servidor do SAP aplicação implementada em duas regiões do Azure diferente. Por exemplo DBMS na camada de aplicação EUA oeste e SAP nos EUA Central. Motivo para não suportar a essas configurações é sensibilidade a latência da arquitetura do SAP NetWeaver.

No entanto, ao longo de último ano center parceiros desenvolvidas conjunta localizações de dados para regiões do Azure. Estas localizações conjunta são, muitas vezes, muito perto dos dados do Azure físicos centros dentro de uma região do Azure. A ligação de ativos na localização conjunta através do ExpressRoute para o Azure e de curta distância podem resultar numa latência que é inferior a 2ms. Nestes casos, para localizar a camada DBMS (incluindo o armazenamento SAN/NAS) nessa colocalização e o SAP camada de aplicação no Azure é possível. A partir de Dec de 2015, não temos todas as implementações que assim o desejar. Mas diferentes clientes com implementações de aplicações SAP não estiver a utilizar já essas abordagens.

### <a name="offline-backup-of-sap-systems"></a>Sistemas de cópia de segurança do SAP offline
Dependente a configuração de SAP escolhida existe (camada 2 ou 3 camadas) pode ser necessário fazer cópias de segurança. O conteúdo da VM próprio mais ter uma cópia de segurança da base de dados. As cópias de segurança relacionadas com o DBMS deverão ser feito com métodos de base de dados. Uma descrição detalhada das bases de dados diferentes, pode ser encontrado na [DBMS guia][dbms-guide]. Por outro lado, os dados SAP podem ser feitos de forma offline (incluindo o conteúdo da base de dados bem) conforme descrito nesta secção ou online, tal como descrito na secção seguinte.

A cópia de segurança offline basicamente precisaria que um encerramento de VM através do portal do Azure e uma cópia do disco da VM base anexados todos os discos para a VM. Isto seria preservar um ponto na imagem de hora da VM e o respetivo disco associado. Recomenda-se para copiar as cópias de segurança para uma conta de armazenamento do Azure diferente. Por conseguinte, o procedimento descrito em capítulo [copiar os discos entre contas de armazenamento do Azure] [ planning-guide-5.4.2] deste documento seria aplicada.
Para além de encerramento no portal do Azure um pode também fazê-lo através do Powershell ou o CLI conforme descrito aqui: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Um restauro do Estado de que seria são compostos de que pretende eliminar a VM base, bem como os discos da base VM originais e montado discos, novamente copiar os discos guardados para o original conta de armazenamento ou grupo de recursos para discos geridos e, em seguida, voltar a implementar o sistema.
Este artigo mostra um exemplo de como este processo no Powershell do script: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se instalar uma nova licença SAP, uma vez que a restaurar uma cópia de segurança VM, tal como descrito acima cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Cópia de segurança online de um sistema SAP
Cópia de segurança do DBMS é executada com métodos DBMS específicos conforme descrito no [DBMS guia][dbms-guide].

Outras VMs no sistema SAP podem ser feitas utilizando a funcionalidade de cópia de segurança do Azure Máquina Virtual. Cópia de segurança de Máquina Virtual do Azure foi introduzida no início 2015 e entretanto é um método padrão para fazer uma cópia de segurança de uma VM no Azure completa. Cópia de segurança do Azure armazena as cópias de segurança no Azure e permite que um restauro de uma VM.

> [!NOTE]
> A partir de 2015 Dec utilizando a cópia de segurança de VM não manter o ID de VM exclusivo que é utilizado para SAP licenciamento. Isto significa que um restauro a partir de uma cópia de segurança VM requer a instalação de uma nova chave de licença SAP como a VM restaurada é considerada uma nova VM e não uma substituição de um anteriores que foi guardado.
>
> ![Windows][Logo_Windows] Windows
>
> Teoria, as VMs que bases de dados de execução podem ser feitas de forma consistente, bem como se o sistema DBMS suporta o VSS do Windows (serviço de cópia sombra de volumes <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) como, por exemplo, SQL Server efetua.
> No entanto, tenha em atenção de que com base em cópias de segurança de VM do Azure no momento restaura das bases de dados não são possíveis. Por conseguinte, recomenda-se efetuar cópias de segurança das bases de dados com a funcionalidade DBMS em vez de depender de cópia de segurança do Azure.
>
> Para se familiarizar com o Backup de Máquina Virtual do Azure, comece por aqui: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Outras possibilidades estão a utilizar uma combinação do Microsoft Data Protection Manager instalada numa VM do Azure e cópia de segurança do Azure para bases de dados de cópia de segurança/restauro. Podem encontrar mais informações aqui: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Não há nenhum equivalente para VSS do Windows no Linux. Por conseguinte, apenas as cópias de segurança consistente com ficheiros estão possíveis mas não consistentes com aplicações cópias de segurança. A cópia de segurança do SAP DBMS deve ser feita utilizando a funcionalidade DBMS. O sistema de ficheiros que inclui os dados relacionados com o SAP pode ser guardado, por exemplo, utilizando tar conforme descrito aqui: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como site de DR para landscapes SAP de produção
Desde Mid 2014, às extensões vários componentes em torno do Hyper-V, o System Center e o Azure ativar a utilização do Azure como site de DR para VMs em execução no local com base no Hyper-V.

Um blogue com detalhes sobre como implementar esta solução é documentado aqui: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Resumo
Os pontos de chaves de elevada disponibilidade para sistemas SAP no Azure são:

* Neste ponto no tempo, o ponto de falha único SAP não pode estar protegido exatamente da mesma forma como pode ser feita em implementações no local. O motivo é que ainda não não possível construir clusters no Azure sem a utilização do software de terceiros 3rd de disco partilhado.
* Para a camada DBMS tem de utilizar a funcionalidade de DBMS não depende da tecnologia de cluster de disco partilhado. Detalhes estão documentados no [DBMS guia][dbms-guide].
* Para minimizar o impacto dos problemas dentro de domínios de falhas na manutenção da infraestrutura ou de anfitrião do Azure, deve utilizar conjuntos de disponibilidade do Azure:
  * É recomendado ter um conjunto de disponibilidade para a camada de aplicação SAP.
  * É recomendado ter um conjunto de disponibilidade separado para a camada de SAP DBMS.
  * NÃO se recomenda a aplicar a mesma conjunto de disponibilidade para as VMs de sistemas SAP diferentes.
  * Recomenda-se para utilizar os discos Premium geridos.
* Para fins de cópia de segurança da camada SAP DBMS, verifique o [DBMS guia][dbms-guide].
* Cópia de segurança de instâncias de caixa de diálogo de SAP só faz sentido pouco, uma vez que é normalmente mais rápida ao Reimplementar a instâncias de caixa de diálogo simples.
* Cópia de segurança a VM que contém o diretório global do sistema SAP e com todos os perfis de várias instâncias, fazer sentido e deve ser efetuada com cópia de segurança do Windows ou, por exemplo, tar no Linux. Uma vez que existem diferenças entre o Windows Server 2008 (R2) e Windows Server 2012 (R2), que facilitam a fazer cópias de segurança utilizando a mais recente do Windows Server versões, recomendamos a executar o Windows Server 2012 (R2) como sistema operativo Windows.
