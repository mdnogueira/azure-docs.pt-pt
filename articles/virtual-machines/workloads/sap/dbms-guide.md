---
title: "Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs"
description: "Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87c4573ce3b688cdc63b3a342bbc0bebb416ad36
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia é parte da documentação no implementar e implementar o software SAP no Microsoft Azure. Antes de ler este guia, leia o [guia de implementação e planeamento][planning-guide]. Este documento aborda a implementação de vários sistemas de gestão de base de dados relacional (RDBMS) e produtos relacionados em combinação com SAP no Microsoft Azure máquinas virtuais (VMs) através da infraestrutura do Azure como um capacidades de serviço (IaaS).

O documento complementa a documentação de instalação de SAP e notas de SAP, que representam os recursos para as instalações e implementações de software do SAP primários em determinadas plataformas.

## <a name="general-considerations"></a>Considerações gerais
Este capítulo, considerações de com sistemas de relacionados com o SAP DBMS em VMs do Azure são-lhe apresentadas. Existem alguns referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são processados neste documento, após este capítulo.

### <a name="definitions-upfront"></a>Definições de compromisso
Ao longo do documento, vamos utilizar os seguintes termos:

* IaaS: Infraestrutura como um serviço.
* PaaS: Plataforma como serviço.
* SaaS: Software como um serviço.
* Componente SAP: uma individuais SAP aplicação tal como ECC, BW, Gestor de solução ou EP.  Componentes SAP podem ser baseados em tecnologias tradicionais ABAP ou Java ou uma aplicação não baseada em NetWeaver como objetos de negócio.
* Ambiente de SAP: um ou mais componentes do SAP logicamente agrupados para efetuar uma função de negócio, tais como o desenvolvimento, QAS, formação, DR ou de produção.
* SAP horizontal: Isto refere-se aos ativos SAP todos num cliente horizontal IT. O horizontal SAP inclui todos os ambientes de não produção e produção.
* Sistema SAP: A combinação de camada DBMS e a camada de aplicação de, por exemplo, um sistema de desenvolvimento do SAP ERP, sistema de teste de SAP BW, sistema de produção do SAP CRM, etc. Nas implementações do Azure, não é suportado para dividir a estas duas camadas entre no local e o Azure. Isto significa que um sistema SAP é implementado no local ou está implementado no Azure. No entanto, pode implementar os sistemas diferentes de um horizontal SAP no Azure ou no local. Por exemplo, pode implementar o desenvolvimento de SAP CRM e sistemas de teste no Azure, mas o SAP CRM produção sistema no local.
* Implementação apenas de nuvem: uma implementação em que a subscrição do Azure não está ligada através de um site para site ou a ligação do ExpressRoute para a infraestrutura de rede no local. Em comum documentação do Azure estes tipos de implementações também estão descritos como implementações "Apenas na Cloud". Máquinas virtuais implementadas com este método são acedidas através da Internet e os pontos finais de Internet públicos atribuídos às VMs no Azure. O local do Active Directory (AD) e DNS não estiver expandido para o Azure nestes tipos de implementações. Por conseguinte, as VMs não fazem parte do Active Directory no local. Nota: As implementações de apenas na nuvem neste documento são definidas como concluídas landscapes SAP, que são exclusivamente no Azure sem a extensão do Active Directory ou a resolução de nomes no local em execução na nuvem pública. Configurações de apenas na nuvem não são suportadas para sistemas de SAP de produção ou configurações de onde SAP STMS ou outros recursos no local têm de ser utilizada entre sistemas SAP alojados no Azure e recursos que residem no local.
* Em vários locais: Descreve um cenário onde as VMs implementadas para uma subscrição do Azure com o site para site, multilocal ou ExpressRoute conectividade entre o datacenter(s) no local e o Azure. Documentação do Azure em comum, estes tipos de implementações também estão descritos como cenários de vários locais. O motivo para a ligação está a expandir domínios no local, do Active Directory no local e o DNS no local no Azure. O horizontal no local é expandido para os recursos da subscrição do Azure. Com esta extensão, as VMs podem fazer parte do domínio no local. Utilizadores de domínio do domínio no local podem aceder aos servidores e podem executar serviços em dessas VMs (por exemplo, o DBMS serviços). Resolução do nome e a comunicação entre as VMs implementadas no local e as VMs implementadas no Azure, é possível. Esperamos que esta seja o cenário mais comum para implementar os recursos SAP no Azure. Para obter mais informações, consulte [neste artigo] [ vpn-gateway-cross-premises-options] e [neste artigo][vpn-gateway-site-to-site-create].

> [!NOTE]
> Implementações de vários locais dos sistemas SAP onde com sistemas SAP de Virtual Machines do Azure são membros de um domínio no local são suportadas para sistemas de SAP de produção. Configurações em vários locais são suportadas para a implementação de partes ou concluir SAP landscapes no Azure. Mesmo em execução horizontal SAP completa no Azure necessita de ter dessas VMs fazer parte do domínio no local e anúncios. Nas versões anteriores da documentação, abordámos cenários híbridos-IT, onde o termo *híbrida* tem Root no facto de que não há uma conectividade entre instalações, entre no local e o Azure. Neste caso *híbrida* também significa que as VMs no Azure fazem parte do Active Directory no local.
> 
> 

Alguma documentação do Microsoft descreve cenários de vários locais um pouco diferente, especialmente para configurações DBMS HA. No caso de documentos relacionados com o SAP, as vários locais cenário apenas boils para baixo até ter uma conectividade de site para site ou privada (ExpressRoute) e para o facto de que o SAP horizontal é distribuído entre no local e o Azure.

### <a name="resources"></a>Recursos
Os guias seguintes estão disponíveis para o tópico de implementações de SAP no Azure:

* [Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver (deste documento)][dbms-guide]

As seguintes notas de SAP estão relacionadas com o tópico do SAP no Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure |
| [2015553] |SAP no Microsoft Azure: suporta a pré-requisitos |
| [1999351] |Resolução de problemas avançada monitorização do Azure para SAP |
| [2178632] |Chave de monitorização métricas para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: avançada de monitorização |
| [2191498] |SAP no Linux com o Azure: avançada de monitorização |
| [2039619] |Aplicações de SAP no Microsoft Azure com a base de dados Oracle: suportada produtos e versões |
| [2233094] |DB6: SAP aplicações no Azure utilizando o IBM DB2 para Linux, UNIX e Windows - informações adicionais |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7. x: instalação e a atualização |
| [2069760] |Oracle Linux 7. x SAP instalação e a atualização |
| [1597355] |Recomendação de espaço de comutação para Linux |
| [2171857] |Base de dados Oracle 12c - suporte de sistema de ficheiros no Linux |
| [1114181] |Base de dados Oracle 11g - suporte de sistema de ficheiros no Linux |


Leia também o [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas de SAP para o Linux.

Deve ter um conhecimento prático sobre a arquitetura do Microsoft Azure e como máquinas virtuais do Microsoft Azure estiver implementado e que operava. Pode encontrar mais informações em <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Estamos **não** debater plataforma Microsoft Azure como um ofertas de serviço (PaaS) da plataforma Microsoft Azure. Este documento está prestes a ser executado um sistema de gestão de base de dados (DBMS) no Microsoft Virtual Machines do Azure (IaaS) tal como executaria o DBMS no seu ambiente no local. Capacidades de base de dados e de funcionalidades entre estas duas ofertas são muito diferentes e não devem ser misturadas entre si. Consulte também: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Uma vez que vamos são debater IaaS, em geral a instalação do Windows, Linux e DBMS e configuração são essencialmente o mesmo que qualquer máquina virtual ou máquina metal bare seria instala no local. No entanto, existem algumas gestão arquitetura e sistema decisões de implementação, que são diferentes quando utilizar o IaaS. O objetivo deste documento é explicar o específica da arquitetura e diferenças de gestão do sistema que devem estar preparadas para quando utilizar o IaaS.

Em geral, as áreas gerais de diferença descreve neste documento são:

* Planear o esquema de disco/VM adequado dos sistemas SAP para se certificar de que tem os dados adequados esquema de ficheiros e pode conseguir suficiente IOPS para a sua carga de trabalho.
* Considerações de rede ao utilizar o IaaS.
* Funcionalidades de base de dados específica a utilizar para otimizar o esquema de base de dados.
* Considerações de cópia de segurança e restauro no IaaS.
* A utilização de diferentes tipos de imagens para a implementação.
* Elevada disponibilidade no IaaS do Azure.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de uma implementação de RDBMS
Para seguir este capítulo, é necessário compreender o que foi introduzido no [isto] [ deployment-guide-3] capítulo do [guia de implementação][deployment-guide]. Conhecimentos sobre a série de VM diferente e os respetivos diferenças e as diferenças do Azure Standard e Premium Storage devem estar compreendido e conhecido antes de ler este capítulo.

Até Março de 2015, discos, que contém um sistema operativo foram limitados a 127 GB de tamanho. Esta limitação foi lifted Março de 2015 (para verificação de informações mais <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). A partir daí em discos que contém o sistema operativo pode ter o mesmo tamanho que qualquer outro disco. Contudo, ainda é preferir uma estrutura de implementação em que o sistema operativo, DBMS e eventual binários SAP são separados dos ficheiros de base de dados. Por conseguinte, esperamos sistemas SAP em execução em Azure Virtual Machines tem base VM (ou disco) instalados com o sistema operativo, executáveis de sistema de gestão de base de dados e executáveis SAP. Os ficheiros de dados e de registo DBMS são armazenados no armazenamento do Azure (Standard ou Premium) separado dos discos e anexados como discos lógicos para a imagem de sistema de operativo Azure VM original. 

Dependente tirar partido do Azure Standard ou Premium (por exemplo, ao utilizar o-série DS ou série GS VMs) existe são outras quotas no Azure, que estão documentadas [aqui (Linux)] [ virtual-machines-sizes-linux] e [ aqui (Windows)][virtual-machines-sizes-windows]. Quando planear a esquema do disco, tem de encontrar o equilíbrio melhor as quotas para os seguintes itens:

* O número de ficheiros de dados.
* O número de discos que contêm os ficheiros.
* As quotas IOPS de um único disco.
* O débito de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* O débito de armazenamento geral pode fornecer uma VM.

Azure impõe uma quota IOPS por disco de dados. Estas quotas são diferentes para discos alojados no Azure de armazenamento Standard e Premium. Latências de e/s também são muito diferentes entre os dois tipos de armazenamento com o armazenamento Premium entrega fatores melhor latências de e/s. Cada um dos diferentes tipos VM tem um número limitado de discos de dados que seja capaz de ligar. Outra restrição é que apenas determinados tipos VM podem tirar partido do Premium Storage do Azure. Isto significa que a decisão para um determinado tipo VM poderá não apenas controlada pelos requisitos de CPU e memória, mas também, o IOPS, requisitos de débito latência e dos discos que normalmente são ampliados com o número de discos ou o tipo de discos de armazenamento Premium. Especialmente com o armazenamento Premium o tamanho de um disco também poderão ser determinadas líquido pelo número de IOPS e débito que tem de ser possível através de cada disco.

O facto de que a velocidade global de IOPS, o número de discos montados e o tamanho da VM estão todos associadas em conjunto, poderá causar uma configuração de um sistema SAP ser diferente da respetiva implementação no local do Azure. Os limites IOPS por LUN são normalmente configuráveis em implementações no local. Enquanto o Storage do Azure esses limites são fixo ou como o tipo de disco dependente armazenamento Premium. Por isso, com as implementações no local, vemos as configurações do cliente dos servidores de base de dados que estão a utilizar diferentes volumes muitas para executáveis especiais, como o SAP e o DBMS ou volumes especiais para bases de dados temporárias ou espaços de tabela. Quando um sistema de no local é movido para o Azure, este poderá dar origem a uma perdas de largura de banda IOPS potencial por perder um disco para executáveis ou de bases de dados, que não efetuar qualquer ou não uma grande quantidade de IOPS. Por conseguinte, em VMs do Azure é recomendável que o DBMS e SAP executáveis ser instalada no disco do SO se possível.

O posicionamento dos ficheiros de base de dados e ficheiros de registo e o tipo de armazenamento do Azure utilizados, deve ser definido pelos requisitos de débito, latência e IOPS. Para poder suficiente IOPS para o registo de transações, poderá ser forçado para tirar partido dos vários discos para o ficheiro de registo de transação ou utilize um disco de armazenamento Premium maior. Nesse caso um iria criar um software RAID (por exemplo Windows armazenamento agrupamento para Windows ou MDADM e LVM (Gestor de Volume lógica) para Linux) com os discos que contêm o registo de transações.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Unidade D:\ numa VM do Azure é uma unidade não persistente, que é suportada por alguns discos locais no nó de computação do Azure. Porque é não persistente, isto significa que todas as alterações efetuadas para o conteúdo na unidade D:\ é perdida quando a VM é reiniciada. Por "quaisquer alterações," estamos significa ficheiros guardados, diretórios criados, aplicações instaladas, etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> As VMs do Linux do Azure automaticamente montar uma unidade no /mnt/resource que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Porque é não persistente, isto significa que as alterações efetuadas ao conteúdo /mnt/resource serão perdidas quando a VM é reiniciada. Por quaisquer alterações, iremos significa que os ficheiros guardados, diretórios criados, aplicações instaladas, etc.
> 
> 

- - -
Depende da série de VM do Azure, os discos locais no nó de computação desempenho diferentes, que pode ser classificado como de mostrar:

* A0-A7: Muito limitado de desempenho. Não é utilizável para nada para além do ficheiro de paginação do windows
* A8-A11: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo
* Série de D: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo
* Série DS: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo
* Série de G: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo
* Série GS: Características de desempenho muito bom com algumas IOPS dez thousand e > débito de 1GB por segundo

A aplicar aos tipos VM que são certificados com SAP declarações acima. A série de VM com excelente IOPS e débito se qualificam para tire partido por algumas funcionalidades DBMS, como tempdb ou espaço de tabela temporária.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>A colocação em cache para VMs e os discos de dados
Quando criamos discos de dados através do portal ou quando é carregados discos para VMs de montagem, mas pode escolher que o tráfego de e/s entre a VM e os discos localizados no armazenamento do Azure são colocadas em cache. Azure Standard e Premium Storage utilizam duas tecnologias diferentes para este tipo de cache. Em ambos os casos, a cache do próprio seria disco copiado nas mesmas unidades utilizadas por disco temporário (D:\ no Windows) ou /mnt/resource no Linux da VM.

Para armazenamento padrão do Azure, os tipos de cache possíveis são:

* Não colocar em cache
* A colocação em cache de leitura
* Leitura e escrita a colocação em cache

Para obter um desempenho consistente e determinística, deve definir a colocação em cache no armazenamento padrão do Azure para todos os discos que contém **ficheiros de dados relacionados com o DBMS, ficheiros de registo e tabela espaço para 'NONE'**. A colocação em cache da VM pode permanecer com a predefinição.

Existem as seguintes opções de colocação em cache para o Premium Storage do Azure:

* Não colocar em cache
* A colocação em cache de leitura

Recomendação para o Premium Storage do Azure é tirar partido das **a colocação em cache para ficheiros de dados de leitura** da base de dados SAP e escolha **sem colocação em cache para os discos de ficheiros de registo**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID de software
Como já indicado acima, tem de equilibrar o número de IOPS necessários para os ficheiros de base de dados entre o número de discos, pode configurar e fornece o uma VM do Azure IOPS máximo por disco ou tipo de disco de armazenamento Premium. É fácil de lidar com a carga IOPS através de discos para criar um RAID de software através de discos diferentes. Em seguida, colocar um número de ficheiros de dados do DBMS SAP nos LUNS carved fora do RAID de software. Depende dos requisitos que convém considerar a utilização do armazenamento Premium, bem como desde a dois dos três diferentes discos do armazenamento Premium fornecem superior quota IOPS discos com base no armazenamento Standard. Para além da latência de e/s de melhor significativa, fornecida pelo Premium Storage do Azure. 

Mesmo se aplica para o registo de transações dos sistemas DBMS diferentes. Com muitos dos mesmos basta apenas adicionar ficheiros de Tlog mais ajuda, uma vez que os sistemas DBMS escrever dos ficheiros em apenas uma vez. Se forem necessárias a taxas IOPS superiores que pode fornecer um único disco de armazenamento Standard com base, pode stripe através de vários discos de armazenamento Standard ou pode utilizar um tipo de disco de armazenamento Premium maior que, para além de taxas IOPS superiores, também fornece fatores menor latência para a operação de escrita posso / SO para o registo de transações.

Situações teve nas implementações do Azure, o qual seriam favor utilizando um RAID de software são:

* Registo de transações de registo/Refazer necessitam de mais de IOPS que o Azure oferece para um único disco. Conforme mencionado Isto pode ser resolvido ao criar um LUN através de vários discos com RAID de software.
* E/s carga de trabalho a distribuição desigual os ficheiros de dados diferente da base de dados SAP. Nestes casos, um pode deparar atingir a quota em vez disso, muitas vezes, um ficheiro de dados. Enquanto que outros ficheiros de dados não são mesmo obter próximo a quota IOPS de um único disco. Nestes casos, a solução mais fácil é criar um LUN através de vários discos com RAID de software. 
* Não sabe que a carga de trabalho de e/s exata por ficheiro de dados é e apenas aproximadamente saibam qual que a carga de trabalho IOPS geral contra o DBMS. Mais fácil fazer é criar um LUN com a ajuda do RAID de software. A soma de quotas de vários discos atrás este LUN, em seguida, deve satisfazer a taxa IOPS conhecida.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Recomendamos que utilize espaços de armazenamento do Windows se executada no Windows Server 2012 ou superior. É mais eficiente do que o Windows Striping de versões anteriores do Windows. Poderá ter de criar os agrupamentos de armazenamento do Windows e os espaços de armazenamento, os comandos do PowerShell quando utilizar o Windows Server 2012 como sistema operativo. Os comandos do PowerShell podem ser encontrados aqui <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Apenas MDADM LVM (Gestor de Volume lógica) são suportados e para criar um software RAID no Linux. Para obter mais informações, leia os artigos seguintes:
> 
> * [Configurar Software RAID no Linux] [ virtual-machines-linux-configure-raid] (para MDADM)
> * [Configurar LVM numa VM com Linux no Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Considerações para tirar partido da VM série, que é capaz de trabalhar com o Premium Storage do Azure, normalmente, são:

* Dar resposta às exigências para latências de e/s que estão próximos que fornecem dispositivos SAN/NAS.
* Pedido para a latência de e/s melhor fatores que podem fornecer armazenamento padrão do Azure.
* Superior IOPS por VM ao que foi obtido com vários discos de armazenamento Standard contra um determinado tipo VM.

Uma vez que o armazenamento subjacente do Azure replica cada disco para, pelo menos, três nós de armazenamento, simple RAID 0 striping pode ser utilizado. Não é necessário implementar RAID5 ou RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Armazenamento do Microsoft Azure
Armazenamento do Microsoft Azure armazena VM base (com um SO) e discos ou BLOBs para, pelo menos, três nós de armazenamento separada. Quando criar uma conta de armazenamento ou disco gerido, há uma opção de proteção conforme mostrado aqui:

![Georreplicação ativada para a conta de armazenamento do Azure][dbms-guide-figure-100]

Azure replicação de Local de armazenamento (localmente redundante) fornece níveis de proteção contra perda de dados devido uma falha de infraestrutura que alguns clientes podem suportar a implementação. Conforme mostrado acima existem quatro opções diferentes com um Quinta a ser uma variação de uma das três primeiro. Procura próximo em-los iremos poder diferenciar os:

* **Premium localmente redundante armazenamento (LRS)**: armazenamento do Azure Premium fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais em execução I/O intensivo cargas de trabalho. Existem três réplicas dos dados no mesmo datacenter do Azure de uma região do Azure. As cópias estão em diferentes falhas e domínios de atualização (para conceitos consulte [isto] [ planning-guide-3.2] capítulo no [guia de planeamento][planning-guide]). Em caso de uma réplica dos dados ficar fora de serviço devido a uma falha de nó de armazenamento ou a falha de disco, é gerada automaticamente uma nova réplica.
* **Localmente redundante armazenamento (LRS)**: neste caso, existem três réplicas dos dados no mesmo datacenter do Azure de uma região do Azure. As cópias estão em diferentes falhas e domínios de atualização (para conceitos consulte [isto] [ planning-guide-3.2] capítulo no [guia de planeamento][planning-guide]). Em caso de uma réplica dos dados ficar fora de serviço devido a uma falha de nó de armazenamento ou a falha de disco, é gerada automaticamente uma nova réplica. 
* **Georreplicação redundante armazenamento (GRS)**: neste caso, não há uma replicação assíncrona que feeds um adicionais três réplicas dos dados noutra região do Azure, que se encontra na maioria dos casos na mesma região geográfica (por exemplo, Europa do Norte e Europa Ocidental ). Isto resulta em três réplicas adicionais, pelo que existem seis réplicas na soma. Uma variação desta situação é um complemento onde os dados de georreplicação replicadas região do Azure podem ser utilizados para fins de leitura (acesso de leitura Georredundante).
* **Zona de armazenamento redundantes (ZRS)**: neste caso, as três réplicas dos dados permanecem na mesma região do Azure. Conforme explicado no [isto] [ planning-guide-3.1] capítulo do [guia de planeamento] [ planning-guide] uma região do Azure pode ser um número dos centros de dados próximos. No caso do LRS as réplicas teriam de ser distribuídas por diferentes dos centros de dados que tornam uma região do Azure.

Podem encontrar mais informações [aqui][storage-redundancy].

> [!NOTE]
> Para implementações de DBMS, não é recomendada a utilização do armazenamento redundante Georreplicação
> 
> Georreplicação-replicação do Storage do Azure é assíncrona. Replicação de discos individuais montado para uma única VM não estão sincronizadas no passo de bloqueio. Por conseguinte, não é adequado replicar os ficheiros DBMS que são distribuídos por discos diferentes ou implementados contra um RAID com base em vários discos de software. O software DBMS requer que o armazenamento de disco persistente precisamente está sincronizado transversalmente LUNs diferentes e subjacentes/spindles de discos. DBMS software utiliza vários mecanismos para atividades de sequência de escrita de e/s e uma DBMS relatórios que o armazenamento de disco visado pela replicação está danificado se estes variam mesmo por alguns milissegundos. Por conseguinte, se um pretende realmente uma configuração de base de dados com uma base de dados transferido entre múltiplos discos georreplicação, essa uma replicação tem de ser executada com meios de base de dados e a funcionalidade. Um não deverá confiar no Azure armazenamento Georreplicação para efetuar esta tarefa. 
> 
> O problema é mais simples explicar com um sistema de exemplo. Vamos assumir que tem um sistema SAP carregado para o Azure, o que tem oito discos que contém ficheiros de dados do DBMS e um disco que contém o ficheiro de registo de transações. Cada um destes discos nove tem os dados escritos num método consistente, de acordo com o DBMS, se os dados que está a ser escritos para os ficheiros de registo de dados ou a transação.
> 
> Na ordem corretamente georreplicação-replicar os dados e manter uma imagem de base de dados consistente, o conteúdo de todos os nove discos procedimento teria de ser georreplicados pela ordem exata as operações de e/s foram executadas contra os nove discos diferentes. No entanto, georreplicação de armazenamento do Azure não permite a declarar as dependências entre os discos. Isto significa georreplicação de armazenamento do Microsoft Azure não sabe mais sobre o facto de que o conteúdo nestes discos diferentes nove está relacionado entre si e que as alterações de dados são consistentes apenas quando replicar pela ordem as operações de e/s foi efetuada em todos os os nove discos.
> 
> Para além de possibilidades a ser elevado que as imagens georreplicação no cenário não fornecem uma imagem de base de dados consistente, há também uma penalidade de desempenho que aparece com o armazenamento de redundante georreplicação que gravemente pode afeta o desempenho. Em resumo, não utilize este tipo de redundância de armazenamento para cargas de trabalho do DBMS tipo.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapeamento de VHDs em contas de armazenamento do serviço de Máquina Virtual do Azure
Este capítulo aplica-se apenas às contas do Storage do Azure. Se pretender utilizar discos geridos, as limitações mencionadas neste capítulo não se aplicam. Para obter mais informações sobre discos geridos, leia o capítulo [discos geridos] [ dbms-guide-managed-disks] deste guia.

Uma conta de armazenamento do Azure é não apenas uma construção administrativa, mas também um assunto limitações. Enquanto que as limitações variam na se abordadas uma conta de armazenamento padrão do Azure ou uma conta de armazenamento do Azure Premium. São listadas exatas capacidades e limitações [aqui][storage-scalability-targets]

Pelo para armazenamento padrão do Azure que é importante salientar não existe um limite no IOPS por conta de armazenamento (que contém a linha **taxa de pedidos de Total** no [o artigo][storage-scalability-targets]). Além disso, não há um limite inicial de 100 contas de armazenamento por subscrição do Azure (em Julho de 2015). Por conseguinte, é recomendado para balancear o IOPS de VMs entre várias contas de armazenamento ao utilizar o armazenamento padrão do Azure. Enquanto que uma única VM utiliza Idealmente uma conta de armazenamento se possível. Por isso, se abordadas implementações DBMS onde cada VHD que está alojado no armazenamento padrão do Azure foi possível atingir o limite de quota, deve implementar apenas 30-40 VHDs por conta de armazenamento do Azure que utiliza o armazenamento padrão do Azure. Por outro lado, se tirar partido do Premium Storage do Azure e pretende armazenar os volumes de grandes dimensões de base de dados, poderá ser bem em termos de IOPS. Mas, uma conta de armazenamento do Azure Premium é a forma mais restritiva no volume de dados do que uma conta de armazenamento padrão do Azure. Como resultado, só é possível implementar um número limitado de VHDs dentro de uma conta de armazenamento do Azure Premium antes de atingir o limite do volume de dados. No final, considere uma conta de armazenamento do Azure como uma 'SAN Virtual' que limitou as capacidades de IOPS e/ou a capacidade. Como resultado, a tarefa permanece, tal como em implementações no local, para definir o esquema dos VHDs dos diferentes sistemas SAP através da diferentes 'imaginary dispositivos SAN' ou as contas de armazenamento do Azure.

Para o armazenamento padrão do Azure, não se recomenda apresentar o armazenamento das contas do storage diferente para uma única VM se possível.

Ao utilizar o DS ou GS-série de VMs do Azure, é possível montar VHDs fora das contas de armazenamento padrão do Azure e contas de armazenamento Premium. Casos de utilização, tal como escrever as cópias de segurança para o armazenamento Standard efetuada VHDs e ter DBMS dados e ficheiros de registo de armazenamento Premium chamem atenção onde pode ser aproveitado esse armazenamento heterogéneo. 

Com base nas implementações de cliente e o teste cerca de 30 para 40 VHDs que contêm os ficheiros de dados de base de dados e ficheiros de registo podem ser aprovisionados numa única do Azure Standard conta de armazenamento com um desempenho aceitável. Conforme mencionado anteriormente, a limitação de uma conta de armazenamento do Azure Premium é provável que seja a capacidade de dados pode conter e não de IOPS.

Como com SAN dispositivos no local, partilha requer alguns monitorização para, eventualmente, detetar congestionamentos de uma conta de armazenamento do Azure. A extensão de monitorização do Azure para SAP e o portal do Azure são ferramentas que podem ser utilizadas para detetar ocupado contas do Storage do Azure que pode ser fornecimento de desempenho inferior ao ideal de e/s.  Se esta situação é detetada, recomenda-se para mover as VMs ocupadas para outra conta de armazenamento do Azure. Consulte o [guia de implementação] [ deployment-guide] para obter detalhes sobre como ativar o SAP alojam as capacidades de monitorização.

Outro artigo resumir as melhores práticas em torno de armazenamento padrão do Azure e as contas de armazenamento padrão do Azure pode ser encontrado aqui <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Discos geridos
Discos geridos são um novo tipo de recurso no Azure Resource Manager que pode ser utilizado em vez dos VHDs que são armazenados em contas do Storage do Azure. Discos geridos alinham automaticamente com o conjunto de disponibilidade da máquina virtual que estão ligados a e, por conseguinte, aumentam a disponibilidade de máquina virtual e os serviços que estão em execução na máquina virtual. Para obter mais informações, leia o [artigo de descrição geral](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP só suporta atualmente os discos Premium geridos. Leia a nota [1928533] para obter mais detalhes.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Mover implementado DBMS VMs de armazenamento padrão do Azure para armazenamento do Azure Premium
Estamos a encontrar bastante alguns cenários onde pretende que como cliente para mover uma VM implementada padrão do armazenamento do Azure para o Premium Storage do Azure. Se os discos são armazenados em contas do Storage do Azure, isto não é possível sem mover fisicamente os dados. Existem várias formas para alcançar o objetivo:

* Pode simplesmente copiar todos os VHDs, VHD de base, bem como VHDs de dados para uma nova conta de armazenamento do Azure Premium. Muitas vezes, escolheu o número de VHDs no armazenamento padrão do Azure não devido ao facto de que é necessário o volume de dados. No entanto, é necessários VHDs tantos devido ao IOPS. Agora que mover para o Premium Storage do Azure foi possível ir forma menos VHDs para conseguir o mesmo débito IOPS. Dado o facto de que padrão do armazenamento do Azure para os dados utilizados e não o tamanho do disco nominal paga, o número de VHDs realmente é relevante em termos de custos. No entanto, com o Premium Storage do Azure, teria de pagar para o tamanho do disco nominal. Por conseguinte, a maioria dos clientes tenta mantenha o número de Azure VHDs no armazenamento Premium, o número necessário para alcançar o débito IOPS necessário. Por isso, a maioria dos clientes decida em relação a forma como uma simples 1:1 cópia.
* Se ainda não foi montado, montar um VHD único que pode conter uma cópia de segurança da base de dados da base de dados SAP. Após a cópia de segurança, pode desmontar todos os VHDs, incluindo o VHD que contém a cópia de segurança e copie o VHD de base e o VHD com a cópia de segurança para uma conta de Premium Storage do Azure. Em seguida, teria de implementar a VM com base num base VHD e montar o VHD com a cópia de segurança. Agora, criar discos de armazenamento Premium vazio adicionais para a VM que são utilizados para restaurar a base de dados para. Esta parte do princípio de que o DBMS permite-lhe alterar caminhos para os ficheiros de dados e de registo como parte do processo de restauro.
* Possibilidade de outra é uma variação do processo anteriores, onde apenas copiar a cópia de segurança VHD para o Premium Storage do Azure e ligá-lo contra uma VM que recentemente implementado e instalado.
* A possibilidade de quarta que deverá escolher quando estiver no need alterar o número de ficheiros de dados da base de dados. Nesse caso, executará uma cópia de sistema homogéneo SAP através de exportação/importação. Colocar os ficheiros de exportação para uma imagem VHD que é copiada para uma conta de armazenamento do Azure Premium e anexe-o a uma VM que utiliza para executar os processos de importação. Os clientes utilizam esta possibilidade principalmente quando pretende reduzir o número de ficheiros de dados.

Se utilizar discos geridos, pode migrar para o Premium Storage por:

1. Desalocar máquina virtual
2. Se necessário, redimensione a máquina virtual para um tamanho que suporte o Premium Storage (por exemplo DS ou GS)
3. Alterar o tipo de conta de disco gerida para Premium (SSD)
4. Iniciar a máquina virtual

### <a name="deployment-of-vms-for-sap-in-azure"></a>Implementação de VMs para SAP no Azure
Microsoft Azure oferece várias formas de implementar as VMs e discos associados. Deste modo, é importante compreender as diferenças, uma vez que os preparativos das VMs podem divergir depende da forma de implementação. Em geral, vamos ver para os cenários descritos nos capítulos seguintes.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Implementar uma VM do Azure Marketplace
Pretende colocar um Microsoft ou por terceiros fornecido a imagem do Azure Marketplace para implementar a VM. Depois de implementar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP no interior da sua VM, tal como teria num ambiente no local. Para instalar o software SAP dentro da VM do Azure, SAP Microsoft Recomendamos carregar e armazenar o suporte de dados de instalação do SAP dos discos de ou para criar uma VM do Azure funcionar como "servidor de ficheiros', que contém todos os necessário SAP suporte de instalação do.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Implementar uma VM com uma imagem de generalizado específicas do cliente
Devido às necessidades de correção específicos sobre a versão de SO ou DBMS, as imagens fornecidas no Azure Marketplace podem não atender as suas necessidades. Por conseguinte, poderá ter de criar uma VM utilizando a sua própria imagem de VM de SO/DBMS 'private', que pode ser implementada várias vezes posteriormente. Para preparar uma imagem 'private' essa duplicação, o SO deve ser generalizado na VM no local. Consulte o [guia de implementação] [ deployment-guide] para obter detalhes sobre como generalizar uma VM.

Se já tiver instalado o conteúdo SAP na VM no local (especialmente para sistemas de camada 2), pode adaptar as definições do sistema SAP após a implementação da VM do Azure através de instância de mudar o nome de procedimento suportado pelo Gestor de aprovisionamento de Software para SAP (nota SAP [1619720]). Caso contrário, pode instalar o software SAP mais tarde após a implementação da VM do Azure.

Medida de base de dados do conteúdo utilizado pela aplicação SAP, pode optar por gerar o conteúdo raiz por uma instalação de SAP ou pode importar o conteúdo no Azure através de um VHD com uma cópia de segurança de base de dados DBMS ou tirar partido das capacidades do DBMS diretamente fazer cópias de segurança para  Armazenamento do Microsoft Azure. Neste caso, pode também preparar os VHDs com o DBMS dados e registo de ficheiros no local e, em seguida, importar os como discos para o Azure. Mas a transferência de dados DBMS, que está a obter carregados no local para o Azure funciona através de discos VHD que precisem de ser preparado no local.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Mover uma VM no local para o Azure com um disco não generalizado
Planear mover um sistema específico do SAP no local para o Azure (comparação de precisão e shift). Isto pode ser feito através do carregamento de disco, que contém o sistema operativo, os binários SAP e eventual binários DBMS e os discos com os ficheiros de dados e de registo do DBMS para o Azure. No opposite to cenário #2 acima, mantenha o nome de anfitrião, SAP SID e contas de utilizador do SAP na VM do Azure como que foram configuradas no ambiente no local. Por conseguinte, generalizar a imagem não é necessário. Este caso aplica-se principalmente para cenários de vários locais onde o horizontal SAP parte é executada no local e em partes no Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Elevada disponibilidade e recuperação após desastre com as VMs do Azure
O Azure disponibiliza as seguintes elevada disponibilidade (ed) e recuperação após desastre (DR) funcionalidades, que se aplicam a vários componentes que utilizaria para implementações de SAP e DBMS

### <a name="vms-deployed-on-azure-nodes"></a>VMs implementadas em nós do Azure
A plataforma do Azure não oferecem funcionalidades como a migração em direto para VMs implementadas. Isto significa que o se houver manutenção necessários num cluster de servidor no qual está implementada uma VM, a VM tem de obter parada e reiniciada. Manutenção no Azure é efetuada utilizando, por isso, denominado domínios de atualização nos clusters de servidores. Apenas um domínio de atualização num momento está a ser mantido. Durante o reinício, há uma interrupção do serviço enquanto a VM está a ser encerrada, efetuar a manutenção e reinício da VM. No entanto, a maioria dos fornecedores DBMS fornece funcionalidade de elevada disponibilidade e recuperação após desastre rapidamente reinicia os serviços DBMS noutro nó quando o nó principal não está disponível. A plataforma do Azure oferece funcionalidades para distribuir as VMs, armazenamento e outros serviços do Azure em vários domínios de atualização para se certificar de que o falhas de infraestrutura ou de manutenção planeadas seriam afeta apenas um pequeno subconjunto de VMs ou serviços.  Com um planeamento cuidadoso, é possível alcançar os níveis de disponibilidade comparáveis à infraestruturas no local.

Conjuntos de disponibilidade do Microsoft Azure são um agrupamento lógico das VMs ou serviços que garante VMs e outros serviços são distribuídos para falhas diferentes e domínios de atualização num cluster, de modo que seria só existir um encerramento de nó em qualquer um ponto no tempo (lido [(Linux)] [ virtual-machines-manage-availability-linux] ou [(Windows)] [ virtual-machines-manage-availability-windows] artigo para obter mais detalhes).

Tem de ser configuradas ao objetivo ao implementar uma VMs conforme visto aqui:

![Definição do conjunto de disponibilidade para o DBMS HA configurações][dbms-guide-figure-200]

Se queremos criar configurações de elevada disponibilidade de implementações de DBMS (independentemente da individuais DBMS HA funcionalidade utilizada), as VMs DBMS teria de:

* Adicionar VMs a mesma rede Virtual do Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* As VMs da configuração HA também devem estar na mesma sub-rede. Resolução de nome entre as diferentes sub-redes não é possível em implementações apenas na nuvem, só funciona de resolução IP. Utilizar o site para site ou de conectividade do ExpressRoute para implementações de vários locais, uma rede com pelo menos uma subrede já é estabelecida. Resolução de nomes é efetuada, de acordo com o local infraestrutura de rede e políticas do AD. 

[comment]: <> (Teste de TODO MSSedusch se ainda verdadeiro no ARM)

#### <a name="ip-addresses"></a>Endereços IP
É altamente recomendado para as VMs para HA configurações de configuração de uma forma resiliente. Depender de endereços IP para resolver o partner(s) HA dentro da configuração do HA não é fidedigna no Azure, a menos que são utilizados endereços IP estáticos. Existem dois conceitos de "Encerramento" no Azure:

* Encerrar baixo através do portal do Azure ou do Azure PowerShell cmdlet Stop-AzureRmVM: neste caso, a Máquina Virtual obtém encerramento e anular atribuído. A conta do Azure já não é cobrada para esta VM, pelo que os encargos de apenas implicar são para o armazenamento utilizado. No entanto, se o endereço IP privado da interface de rede não estava estático, o endereço IP é libertado e não é garantido que a interface de rede obtém o endereço IP antigo atribuído novamente após um reinício da VM. A encerrar a efetuar para baixo através do portal do Azure ou da chamada Stop-AzureRmVM automaticamente faz com que Desalocação. Se não pretender desalocar a utilização de máquina Stop-AzureRmVM - StayProvisioned 
* Se desligar a VM a partir de um nível de SO, a VM obtém encerrar e não anular atribuída. No entanto, neste caso, a conta do Azure é ainda cobrada para a VM, apesar do facto de que é encerrado. Nesse caso, a atribuição do endereço IP para uma VM parada permanece intacta. Encerrar VM a partir do dentro não automaticamente forçar Desalocação.

Mesmo para cenários de vários locais, por predefinição um encerramento e Desalocação significa desativação de atribuição de endereços IP da VM, mesmo se as políticas locais nas definições de DHCP são diferentes. 

* A exceção é se um atribui um endereço IP estático para uma interface de rede, como descrita [aqui][virtual-networks-reserved-private-ip].
* Nesse caso o endereço IP permanece fixo, desde que a interface de rede não é eliminada.

> [!IMPORTANT]
> Para poder manter a implementação de toda simples e fácil de gerir, desmarque recomenda-se configurar as VMs partnering num DBMS HA DR configuração ou no Azure de forma a que não existe uma resolução de nome funcionar entre as VMs diferentes envolvidas.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Implementação do anfitrião de monitorização
Para produtiva utilização das aplicações SAP em Azure Virtual Machines, SAP requer a capacidade para obter dados dos anfitriões físicos a máquinas de virtuais do Azure em execução de monitorização de anfitrião. Um nível de patch de agente do anfitrião SAP específico é necessário que permite que esta capacidade SAPOSCOL e SAP o agente do anfitrião. O nível de patch exato está documentado na nota SAP [1409604].

Para obter mais informações sobre a implementação dos componentes que fornecer dados de anfitrião para SAPOSCOL e SAP o agente do anfitrião e a gestão de ciclo de vida desses componentes, consulte o [guia de implementação][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Informações específicas para o Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server IaaS
A partir do Microsoft Azure, pode migrar facilmente as aplicações do SQL Server existentes criadas na plataforma do Windows Server para máquinas virtuais do Azure. SQL Server numa máquina Virtual permite-lhe reduzir o custo total de propriedade de implementação, gestão e manutenção de aplicações da empresa leque migrando facilmente estas aplicações para o Microsoft Azure. Com o SQL Server, uma Máquina Virtual no Azure, os administradores e programadores podem continuar a utilizar o mesmo desenvolvimento e ferramentas de administração estão disponíveis no local. 

> [!IMPORTANT]
> Não podemos são debater Microsoft Azure SQL da base de dados, que é uma plataforma como uma oferta de serviço da plataforma Microsoft Azure. É o debate neste documento sobre a execução do produto do SQL Server, dado que é conhecido para implementações no local em máquinas virtuais do Azure, tirar partido da infraestrutura como uma capacidade de serviço do Azure. Capacidades de base de dados e de funcionalidades entre estas duas ofertas são diferentes e não devem ser misturadas entre si. Consulte também: <https://azure.microsoft.com/services/sql-database/>
> 
> 

É vivamente recomendado rever [isto] [ virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas secções seguintes peças de partes da documentação em ligação acima são agregadas e mencionadas. Informações específicas em torno do SAP são mencionadas bem e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendado para completar a documentação acima primeiro antes de ler a documentação específica do servidor SQL.

Há alguns do SQL Server em IaaS informações específicas que deve saber antes de continuar:

* **SLA da máquina virtual**: há um SLA para máquinas virtuais em execução no Azure, o que pode ser encontrado aqui: <https://azure.microsoft.com/support/legal/sla/>  
* **Versão do SQL Server suporta**: para clientes SAP, suportamos o SQL Server 2008 R2 e posteriores na máquina de Virtual do Microsoft Azure. Não são suportadas as edições anteriores. Reveja esta geral [declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Tenha em atenção que no geral do SQL Server 2008 é suportada pela Microsoft, bem como. No entanto, devido à funcionalidade significativa para SAP, que foi introduzida com o SQL Server 2008 R2, SQL Server 2008 R2 é a versão mínima para SAP. Tenha em atenção que o SQL Server 2012 ou 2014 obteve expandido com uma integração mais profunda para o cenário de IaaS (como criar cópias de segurança diretamente com o armazenamento do Azure). Por conseguinte, iremos restringir neste documento para o SQL Server 2012 ou 2014 com o respetivo nível de patch mais recente do Azure.
* **Suporte de funcionalidades do SQL Server**: mais SQL Server são suportadas as funcionalidades em máquinas virtuais do Microsoft Azure com algumas exceções. **Não é suportada a SQL Server Clustering de ativação pós-falha utilizando discos partilhados**.  Distribuída tecnologias, como o espelhamento da base de dados, grupos de Disponibilidade AlwaysOn, replicação, envio de registo e Mediador de serviço são suportadas numa única região do Azure. SQL Server AlwaysOn também é suportado entre diferentes regiões do Azure conforme documentado aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Reveja o [declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Um exemplo sobre como implementar uma configuração de AlwaysOn é apresentado na [isto] [ virtual-machines-workload-template-sql-alwayson] artigo. Além disso, veja as melhores práticas documentados [aqui][virtual-machines-sql-server-infrastructure-services] 
* **Desempenho de SQL**: estamos a certeza de que o Microsoft Azure máquinas virtuais alojadas efetuar muito bem em comparação com outras ofertas de Virtualização de nuvem pública, mas resultados individuais podem variar. Veja [isto] [ virtual-machines-sql-server-performance-best-practices] artigo.
* **Utilização de imagens do Azure Marketplace**: A forma mais rápida para implementar uma nova VM do Azure da Microsoft está a utilizar uma imagem do Azure Marketplace. Existem imagens no Azure Marketplace, que contém o SQL Server. As imagens onde do SQL Server já está instalado não podem ser utilizadas imediatamente para SAP NetWeaver aplicações. O motivo é que o agrupamento do SQL Server predefinido está instalado nessas imagens e não o agrupamento de SAP NetWeaver sistemas. Para utilizar imagens, consulte os passos documentados no capítulo [utilizando uma imagem do SQL Server fora do Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Veja [detalhes dos preços](https://azure.microsoft.com/pricing/) para obter mais informações. O [guia de licenciamento do SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e [guia de licenciamento do SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) também são um recurso importante.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Diretrizes de configuração do SQL Server para instalações de SAP relacionadas com o SQL Server em VMs do Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações na estrutura de VM/VHD para as implementações de SAP relacionadas com o SQL Server
De acordo com a descrição geral, executáveis do SQL Server devem estar localizado ou instalada na unidade de sistema do disco do SO da VM (unidade c:\).  Normalmente, a maioria das bases de dados de sistema do SQL Server não é utilizada num nível elevado SAP NetWeaver carga de trabalho. Por conseguinte, as bases de dados do sistema do SQL Server (mestre, msdb e modelo) podem permanecer na unidade C:\. Uma exceção pode ser tempdb, que, no caso de algumas ERP SAP e todas as cargas de trabalho de BW, pode necessitar de mais elevado volume de dados ou volume de operações de e/s, que não é possível ajustar a VM original. Para esses sistemas, devem ser efetuados os seguintes passos:

* Mova os ficheiros de dados tempdb primária para a unidade lógica mesma como os ficheiros de dados principal da base de dados SAP.
* Adicione os ficheiros de dados tempdb adicionais para cada uma das outras unidades lógicas que contém um ficheiro de dados da base de dados de utilizador do SAP.
* Adicione o ficheiro de registo de tempdb para a unidade lógica, que contém o ficheiro de registo de utilizador da base de dados.
* **Exclusivamente para tipos VM que utilizam o SSD local** no registo de dados tempdb do nó de computação e os ficheiros podem ser colocados na unidade D:\. Contudo, poderá ser recomenda a utilização de vários ficheiros de dados tempdb. Tenha em atenção volumes de unidade D:\ são diferentes consoante o tipo VM.

Estas configurações ativar tempdb consumir mais espaço do que a unidade do sistema é capaz de fornecer. Para determinar o tamanho de tempdb adequada, um pode verificar os tamanhos de tempdb em sistemas existentes, que são executadas no local. Além disso, este tipo de configuração seria ativar números IOPS contra tempdb, que não pode ser fornecida com a unidade do sistema. Novamente, os sistemas que estejam a executar no local podem ser utilizados para monitorizar a carga de trabalho de e/s contra tempdb para que podem derivar os números IOPS que esperava ver no seu tempdb.

Uma configuração de VM, que executa o SQL Server com uma base de dados SAP e onde os dados de tempdb e tempdb logfile são colocados na unidade D:\ deverá ter o seguinte aspeto:

![Configuração de referência da VM do IaaS do Azure para SAP][dbms-guide-figure-300]

Lembre-se de que a unidade D:\ tem diferentes tamanhos dependentes do tipo VM. Depende o requisito de tamanho de tempdb, poderá ser forçado a par tempdb dados ficheiros de registo e com os ficheiros de dados e de registo da base de dados SAP nos casos em que a unidade D:\ é demasiado pequena.

#### <a name="formatting-the-disks"></a>Os discos de formatação
Para o SQL Server tamanho para discos que contém dados do SQL Server do bloco de NTFS e os ficheiros de registo devem ser 64K. Não é necessário para formatar a unidade D:\. Esta unidade vem previamente formatada.

Para se certificar de que o restauro ou a criação de bases de dados é não inicializar os ficheiros de dados por zeroing o conteúdo dos ficheiros, um deve certificar-se de que o contexto de utilizador, que o SQL Server está em execução no tem uma determinada permissão. Normalmente, os utilizadores no grupo de administrador do Windows têm estas permissões. Se o serviço SQL Server é executado no contexto de utilizador do utilizador de não - administrador do Windows, terá de atribuir o direito de utilizador de que o utilizador **efetuar tarefas de manutenção de volume**.  Consulte os detalhes neste artigo da Base de dados de Conhecimento Microsoft: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações de onde a largura de banda de e/s pode ser um fator restritivo, todas as medidas, o que reduz o IOPS poderão ajudar a Stretch Database a carga de trabalho um pode executar um cenário de IaaS, como o Azure. Por conseguinte, se ainda não o fez, aplicar a compressão de página do servidor de SQL é vivamente recomendado pelo SAP e Microsoft antes de carregar uma base de dados SAP para o Azure.

A recomendação para efetuar a compressão de base de dados antes de carregar para o Azure é fornecida fora duas razões:

* É inferior a quantidade de dados para ser carregado.
* A duração da execução de compressão é mais curta, partindo do princípio que um pode utilizar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou inferior e/s latência no local.
* Tamanhos de base de dados mais pequenos poderão originar menos custos para alocação de disco

Compressão de base de dados, bem como funciona uma Azure Virtual Machines, como no local. Para obter mais detalhes sobre a comprimir a base de dados SAP SQL Server existente, verifique aqui: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 – armazenar ficheiros de base de dados diretamente no Blob Storage do Azure
SQL Server 2014 abre-se a possibilidade de armazenar ficheiros de base de dados diretamente no arquivo de Blob do Azure sem "wrapper" de um VHD em torno deles. Especialmente com a utilização do armazenamento do Azure Standard ou tipos VM mais pequenos, isto permite cenários em que consegue superar os limites de IOPS que seriam impostas por um número limitado de discos que podem ser montados para alguns tipos VM mais pequenos. Isto funciona para bases de dados de utilizador nem para sistema bases de dados do SQL Server. Este exemplo também funciona para os ficheiros de registo e de dados do SQL Server. Se gostaria de implementar uma base de dados do SQL Server do SAP desta forma, em vez de 'encapsulamento de aplicações' para o VHD, tenha o seguinte em consideração:

* A conta de armazenamento utilizado tem de estar na mesma região do Azure como aquele que é utilizada para implementar o SQL Server de VM está a executar.
* São aplicáveis considerações listadas anteriormente sobre a distribuição de VHDs através de diferentes contas de armazenamento do Azure para que este método de implementações bem. Significa que a contagem de operações de e/s contra os limites da conta do Storage do Azure.

[comment]: <> (MSSedusch TODO, mas este procedimento irá utilizar largura de banda de rede e não armazenamento largura de banda, não-lo?)

Detalhes sobre este tipo de implementação estão listados aqui: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Para armazenar ficheiros de dados do SQL Server diretamente no armazenamento do Azure Premium, tem de ter uma versão de patch do SQL Server 2014 mínima, que é descrita aqui: <https://support.microsoft.com/kb/3063054>. Armazenar ficheiros de dados do SQL Server no armazenamento padrão do Azure funciona com a versão de lançamento do SQL Server 2014. No entanto, os patches muito mesmos contêm outra série de correções, o que fazer com que a utilização direta do armazenamento de Blobs do Azure para ficheiros de dados do SQL Server e as cópias de segurança mais fiável. Por conseguinte, recomendamos a utilização destes patches em geral.

### <a name="sql-server-2014-buffer-pool-extension"></a>Extensão do conjunto de memória intermédia do SQL Server 2014
SQL Server 2014 introduziu uma nova funcionalidade, o qual se denomina a extensão do conjunto de memória intermédia. Esta funcionalidade expande o conjunto de memória intermédia do SQL Server, que é guardada na memória com uma cache de nível segundo que é copiada por SSD local de um servidor ou de VM. Isto permite para manter um conjunto de trabalho maior de dados 'na memória'. Em comparação com a aceder ao armazenamento padrão do Azure o acesso para a extensão do conjunto de memória intermédia, que é armazenado em SSDs locais de uma VM do Azure é vários fatores mais rapidamente.  Por conseguinte, tirar partido da unidade D:\ local dos tipos de VM que tenham excelente IOPS e débito pode ser uma forma muito razoável para reduzir a carga IOPS contra o Storage do Azure e melhorar significativamente os tempos de resposta das consultas. Isto aplica-se especialmente quando não utilizar o Premium Storage. Em caso de Premium Storage e a utilização da Cache de leitura do Azure Premium no nó de computação, conforme recomendado para ficheiros de dados, não existem diferenças significativas esperadas. Razão é que ambas as caches (extensão do conjunto de memória intermédia do SQL Server e Cache de leitura do Premium Storage) estão a utilizar discos locais de nós de computação.
Para mais detalhes sobre esta funcionalidade, consulte esta documentação: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considerações sobre a cópia de segurança/recuperação para o SQL Server
Quando implementar o SQL Server no Azure, a metodologia de cópia de segurança deve ser revista. Mesmo que o sistema não é um sistema produtivo, a base de dados SAP alojada pelo SQL Server deve ser feita periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança agora é menos importante no que respeita à compensação uma falha de armazenamento. O motivo de prioridade para manter um plano de cópia de segurança e recuperação adequado é maior do que pode compensar erros lógica/manual, fornecendo o ponto de funcionalidades de recuperação de tempo. Por isso, o objetivo é qualquer uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. Por exemplo, foi possível transferir a partir de uma configuração de camada 2 de SAP para uma configuração de sistema de 3 camadas do mesmo sistema restaurando uma cópia de segurança.

Existem três formas diferentes de cópia de segurança do SQL Server para o Storage do Azure:

1. SQL Server 2012 CU4 e superior pode nativamente fazer uma cópia de segurança de bases de dados para um URL. Isto está detalhado no blogue [nova funcionalidade no SQL Server 2014 - parte 5 - cópia de segurança/restauro melhoramentos](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Consulte o capítulo [CU4 do SQL Server 2012 SP1 e posterior][dbms-guide-5.5.1].
2. Versões do SQL Server antes de CU4 do SQL Server 2012 podem utilizar uma funcionalidade de redirecionamento para cópia de segurança a um VHD e mudam basicamente o fluxo de escrita para uma localização de armazenamento do Azure que tenha sido configurada. Consulte o capítulo [SQL Server 2012 SP1 CU3 e versões anteriores][dbms-guide-5.5.2].
3. O método final consiste em Efetuar cópia de segurança convencional do SQL Server para o comando de disco para um dispositivo de disco. Esta é idêntica ao padrão de implementação no local e não é abordada em detalhe neste documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e posterior
Esta funcionalidade permite-lhe diretamente cópia de segurança para armazenamento de BLOBS do Azure. Sem este método, deve criar cópias de segurança para outros discos que consumam disco e a capacidade IOPS. A ideia é basicamente:

 ![Utilizando a cópia de segurança do SQL Server 2012 para o BLOB de armazenamento do Microsoft Azure][dbms-guide-figure-400]

Neste caso, a vantagem é que um não necessita de gaste discos para armazenar cópias de segurança do SQL Server no. Por isso, tem discos menos atribuídos e a largura de banda completa do disco que IOPS pode ser utilizado para ficheiros de registo e de dados. Tenha em atenção que o tamanho máximo de uma cópia de segurança está limitado a um máximo de 1 TB, conforme documentado na secção **limitações** neste artigo: <https://docs.microsoft.com/sql/relational-databases/backup-restore/ SQL Server--cópia de segurança-para-url do servidor #limitations>. Se o tamanho da cópia de segurança, não obstante compressão de cópias de segurança do SQL Server a utilizar iria exceder a 1 TB de tamanho, a funcionalidade descrita capítulo [SQL Server 2012 SP1 CU3 e versões anteriores] [ dbms-guide-5.5.2] neste documento tem de ser utilizado.

[Documentação relacionada](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) que descrevem o restauro de bases de dados de cópias de segurança no arquivo de Blob do Azure Recomendamos não para restaurar diretamente a partir do arquivo de BLOB do Azure, se a cópia de segurança > 25 GB. A recomendação deste artigo é simplesmente com base em considerações de desempenho e não devido a restrições de funcionais. Por conseguinte, podem aplicar condições diferentes numa base de maiúsculas e minúsculas, maiúsculas e minúsculas.

Pode encontrar documentação sobre como este tipo de cópia de segurança está configurado e utilizado na [isto](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) tutorial

Um exemplo de sequência de passos pode ser lidos [aqui](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatizar as cópias de segurança, é de importância mais alta para se certificar de que os BLOBs para cada cópia de segurança são denominados de forma diferente. Caso contrário, são substituídas e a cadeia restore foi interrompida.

Por ordem para não misture segurança coisas entre os três tipos diferentes de cópias de segurança, é recomendado criar contentores diferentes sob a conta de armazenamento utilizada para cópias de segurança. Os contentores pode ser por VM apenas ou por VM e o tipo de cópia de segurança. O esquema foi aspeto:

 ![Utilizando a cópia de segurança do SQL Server 2012 para o BLOB de armazenamento do Microsoft Azure - diferentes contentores na conta de armazenamento separada][dbms-guide-figure-500]

No exemplo acima, as cópias de segurança não seriam executadas para a mesma conta de armazenamento onde as VMs implementadas. Seria uma nova conta de armazenamento especificamente para as cópias de segurança. Dentro as contas de armazenamento, seria possível diferentes contentores criados com uma matriz do tipo de cópia de segurança e o nome da VM. Essa segmentação torna mais fácil administrate as cópias de segurança das VMs diferentes.

Os BLOBs um escreve diretamente as cópias de segurança, não adicionar a contagem dos dados de discos de uma VM. Por conseguinte, um pode maximizar o número máximo de discos de dados montada do SKU VM específicos para os dados e transação ficheiro de registo e ainda executar uma cópia de segurança contra um contentor de armazenamento. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versões anteriores
O primeiro passo tem de efetuar para alcançar a uma cópia de segurança diretamente com o armazenamento de Azure seria a transferir msi, que está ligado a [isto](https://www.microsoft.com/download/details.aspx?id=40740) artigo KBA.

Ficheiro de instalação de transferência de x64 e a documentação. O ficheiro instala um programa designado: **cópia de segurança do Microsoft SQL Server para o Microsoft Azure ferramenta**. Leia cuidadosamente a documentação do produto.  A ferramenta basicamente funciona da seguinte forma:

* Do lado do servidor de SQL, uma localização do disco para a cópia de segurança do SQL Server se encontra definida (não utilize a unidade D:\ para este).
* A ferramenta permite-lhe definir as regras, o que podem ser utilizadas para direcionar os diferentes tipos de cópias de segurança para diferentes contentores de armazenamento do Azure.
* Assim que as regras são cumpridos, a ferramenta redireciona o fluxo de escrita da cópia de segurança para um dos VHDs/discos para a localização de armazenamento do Azure, que foi definida anteriormente.
* A ferramenta deixa um ficheiro pequeno stub alguns KB tamanho no VHD/disco, que foi definido para o SQL Server cópia de segurança. **Este ficheiro deve ser mantido na localização de armazenamento, uma vez que é necessário para restaurar novamente do armazenamento do Azure.**
  * Se tiver perdido o ficheiro de stub (por exemplo através da perda do suporte de dados de armazenamento que continha o ficheiro de stub) e tiver escolhido a opção de cópia de segurança para uma conta de armazenamento do Microsoft Azure, pode recuperar o ficheiro de stub através de armazenamento do Microsoft Azure, transferindo-a partir do contentor de armazenamento na qual foi efetuada. Em seguida, deve colocar o ficheiro de stub para uma pasta no computador local onde a ferramenta está configurada para detetar e carregar ao mesmo contentor com a mesma palavra-passe de encriptação, se a encriptação foi utilizada com a regra original. 

Isto significa que o esquema, tal como descrito acima em versões mais recentes do SQL Server pode ser colocado no local, bem como em versões do SQL Server, que não são permitidos endereço direto uma localização de armazenamento do Azure.

Este método não deve ser utilizado com versões mais recentes do SQL Server, que suportam a cópia de segurança se nativamente com o armazenamento do Azure. Exceções são onde estão a bloquear execução de cópia de segurança nativa no Azure limitações da cópia de segurança nativa no Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Outras possibilidades para cópia de segurança de bases de dados do SQL Server
Outras possibilidades para cópia de segurança de bases de dados é anexe discos de dados adicionais para uma VM que utiliza para armazenar as cópias de segurança. Nesse caso, terá de certificar-se de que os discos não estão em execução completos. Se for esse o caso, terá de desinstalar os discos e, por isso, a speak 'Arquivar'-lo e substitua-o por um disco novo vazio. Se passar para baixo do que o caminho, pretender manter estes VHDs em contas do Storage do Azure separado das que os VHDs com os ficheiros de base de dados.

A possibilidade de segundo consiste em utilizar uma grande VM que pode ter muitos discos ligados, por exemplo um D14 com 32VHDs. Utilize espaços de armazenamento para criar um ambiente flexível onde foi possível criar partilhas que são utilizadas, em seguida, como destinos de cópia de segurança para os servidores DBMS diferentes.

Algumas melhores práticas obteve documentadas [aqui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) bem. 

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para as cópias de segurança/restauros
Tal como em implementações bare-metal, o desempenho de cópia de segurança/restauro está dependente de quantos volumes podem ser lidas em paralelo e que o débito desses volumes poderão. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com apenas oito threads de CPU. Por conseguinte, um pode assumir:

* O menos o número de discos utilizados para armazenar os dados de ficheiros, o mais pequeno o débito global ler.
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança.
* Os destinos menos (BLOBs, os VHDs ou discos) para escrever a cópia de segurança, o menor o débito.
* O mais pequeno da VM tamanho, o menor o débito quota de armazenamento escrita e leitura a partir do armazenamento do Azure. Independentemente do se as cópias de segurança diretamente são armazenadas no Blob do Azure ou se são armazenados em VHDs novamente são armazenados em Blobs do Azure.

Quando utilizar um BLOB de armazenamento do Microsoft Azure como destino de cópia de segurança nas versões mais recentes, está restrito a designar apenas um URL de destino para cada cópia de segurança específica.

Mas, ao utilizar "Microsoft SQL Server cópia de segurança a ferramenta Microsoft Azure" em versões mais antigas, pode definir mais do que um destino de ficheiro. Com mais do que um destino, pode aumentar a cópia de segurança e o débito da cópia de segurança é superior. Isto resulta, em seguida, em múltiplos ficheiros, bem como na conta do Storage do Azure. No nosso teste, utilizar vários destinos de ficheiro sem dúvida uma pode alcançar o débito, o qual um foi alcançar com as extensões de cópia de segurança implementada do SQL Server 2012 SP1 CU4 no. Poderá também não é bloqueadas pelo limite de 1TB, como a cópia de segurança nativo no Azure.

No entanto, tenha em mente, o débito também depende da localização da conta do Storage do Azure que utiliza para a cópia de segurança. Poderá ser uma ideia localizar a conta de armazenamento numa região diferente da que as VMs estão a executar. Por exemplo seria execute a configuração de VM na Europa Ocidental, mas colocar a conta de armazenamento que utilizar para cópia de segurança contra na Europa do Norte. Certamente que tem um impacto sobre o débito de cópia de segurança e não é provável que geram um débito de 150MB/seg conforme parece ser possível em casos onde o armazenamento de destino e as VMs em execução no mesmo centro de dados regional.

#### <a name="managing-backup-blobs"></a>Gestão de BLOBs de cópia de segurança
Não há um requisito para gerir as cópias de segurança por si. Uma vez que as expectativas que muitas blobs são criados pela execução de cópias de segurança de registo de transações frequentes, administração desses blobs facilmente pode sobrecarregar o portal do Azure. Por conseguinte, é recommendable tirar partido do Explorador de armazenamento do Azure. Existem várias boas perfis disponíveis, que podem ajudar a gerir uma conta de armazenamento do Azure

* Microsoft Visual Studio com o Azure SDK instalado (<https://azure.microsoft.com/downloads/>)
* Explorador de armazenamento do Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* Ferramentas de terceiros

Para ver um debate mais completo de cópia de segurança e SAP no Azure, consulte [o guia de cópia de segurança do SAP](sap-hana-backup-guide.md) para obter mais informações.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Utilizar uma imagem do SQL Server fora do Microsoft Azure Marketplace
A Microsoft oferece VMs no Azure Marketplace, que já contenham versões do SQL Server. Para clientes SAP que necessitam de licenças para o SQL Server e Windows, esta situação pode ser uma oportunidade para cobrir basicamente a necessidade de licenças por girar das VMs com o SQL Server já instalado. Para utilizar imagens para SAP, as seguintes considerações têm de ser tomadas:

* As versões de avaliação não SQL Server adquirir custos superiores que apenas um 'Windows-only' VM implementadas a partir do Azure Marketplace. Consulte estes artigos para comparar os preços: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Só pode utilizar versões do SQL Server, que são suportadas pelo SAP, como o SQL Server 2012.
* O agrupamento de instância do SQL Server, o que é instalado em VMs disponibilizadas no Azure Marketplace não é o agrupamento que SAP NetWeaver requer a instância do SQL Server para executar. Pode alterar o agrupamento embora com as instruções na secção seguinte.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterar o agrupamento de servidor SQL de um servidor de Microsoft Windows/SQL VM
Uma vez que as imagens do SQL Server no Azure Marketplace não estão configuradas para utilizar o agrupamento, que é necessário por aplicações SAP NetWeaver, tem de ser alterada imediatamente após a implementação. Para o SQL Server 2012, isto pode ser feito com os seguintes passos, assim que a VM foi implementada e um administrador poderá iniciar sessão na VM implementada:

* Abra uma janela de comandos do Windows, como administrador.
* Altere o diretório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup.exe /QUIET /ACTION = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta que foi definida como a conta de administrador quando implementar a VM pela primeira vez através da galeria.

O processo só deve demorar alguns minutos. Para garantir que indica se o passo terminou a cópia de segurança com o resultado correto, execute os seguintes passos:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort a base de dados mestra do SQL Server.

O resultado desejado deve ter o seguinte aspeto:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se não for o resultado, pare a implementação SAP e investigar por que motivo o comando de configuração não funcionar conforme esperado. Implementação de aplicações do SAP NetWeaver numa instância do SQL Server com codepages de SQL Server diferentes dos mencionados acima é **não** suportado.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server elevada disponibilidade para SAP no Azure
Tal como mencionado anteriormente neste documento, não há nenhum possibilidade de criar o armazenamento partilhado, o que é necessário para a utilização da funcionalidade de elevada disponibilidade do SQL Server mais antiga. Esta funcionalidade seria instalar dois ou mais instâncias do SQL Server num Windows Server ativação pós-falha de Cluster (WSFC) utilizando um disco partilhado para as bases de dados do utilizador (e, eventualmente, tempdb). Este é o método de elevada disponibilidade padrão de muito tempo, o que também é suportado pelo SAP. Porque o Azure não suporta o armazenamento partilhado, configurações de elevada disponibilidade do SQL Server com uma configuração de cluster de disco partilhado não podem ser realizadas. No entanto, muitos outros métodos de elevada disponibilidade são ainda possíveis e são descritos nas secções seguintes.

#### <a name="sql-server-log-shipping"></a>Envio de registos do SQL Server
Um dos métodos de elevada disponibilidade (HA) é o envio de registo do SQL Server. Se as VMs que participam na configuração do HA tiverem trabalhar resolução do nome, não existe nenhum problema e o programa de configuração do Azure não diferem dos qualquer configuração que é efetuada no local. Não se recomenda a depender de resolução IP apenas. Relativamente a configurar o envio de registo e estes princípios em torno de envio de registo, consulte esta documentação:

<https://Docs.microsoft.com/SQL/Database-Engine/log-Shipping/About-log-Shipping-SQL-Server>

Para poder alcançar realmente qualquer elevada disponibilidade, um tem de implementar as VMs que estejam dentro do tal envio de registo configuração de estar dentro do mesmo conjunto de disponibilidade de Azure.

#### <a name="database-mirroring"></a>O espelhamento da base de dados
Espelhamento de bases de dados, como suportada pelo SAP (consulte a nota SAP [965908]) baseia-se numa definição de um parceiro de ativação pós-falha na cadeia de ligação de SAP. Para os casos em vários locais, partimos do pressuposto que as duas VMs estão no mesmo domínio e de que as instâncias de SQL Server de contexto as duas do utilizador estão a utilizar, bem como um utilizador de domínio e tem privilégios suficientes no duas instâncias do SQL Server envolvidas. Por conseguinte, a configuração do espelhamento de base de dados no Azure não diferem entre uma programa de configuração/configuração típica no local.

A partir de implementações de apenas na nuvem, o método mais fácil é ter outra configuração do domínio no Azure para que essas DBMS VMs (e VMs de SAP Idealmente dedicadas) dentro de um domínio.

Se a um domínio não for possível, um pode também utilizar certificados para a base de dados espelhamento pontos finais, conforme descrito aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para configurar o espelhamento da base de dados no Azure pode ser encontrado aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server Always On
Como Always On é suportada para SAP no local (ver nota SAP [1772688]), é suportada a ser utilizado em combinação com SAP no Azure. O facto de que não é possível criar discos partilhados no Azure, não significa que um não é possível criar uma configuração sempre no Windows Server ativação pós-falha Cluster (WSFC) entre diferentes VMs. Apenas significa que não têm a possibilidade de utilizar um disco partilhado como um quórum na configuração do cluster. Por conseguinte, pode criar uma configuração de sempre WSFC no Azure e simplesmente não selecione o tipo de quórum que utiliza o disco partilhado. O ambiente do Azure dessas VMs implementadas no deve resolver as VMs por nome e as VMs deve estar no mesmo domínio. Isto é verdadeiro para o Azure apenas e implementações de vários locais. Existem algumas considerações especiais em torno da implementação de escuta do grupo disponibilidade do SQL Server (não deve ser confundido com o conjunto de disponibilidade do Azure), uma vez que o Azure, neste ponto no tempo não permite simplesmente criar um objeto do AD/DNS, é possível no local. Por conseguinte, alguns passos de instalação diferente são necessários para ultrapassar o comportamento específico do Azure.

Algumas considerações utilizando um serviço de escuta do grupo de disponibilidade são:

* Utilizar um serviço de escuta do grupo de disponibilidade só é possível com o Windows Server 2012 ou superior como SO convidado da VM. Para o Windows Server 2012 tem de certificar-se de que esta correção é aplicada: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2 este patch existe e Always On teria de ser utilizados da mesma forma como o espelhamento da base de dados, especificando um parceiro de ativação pós-falha na cadeia de ligações (feito o SAP default.pfl parâmetro dbs/mss/servidor - Consulte a nota SAP [965908]).
* Quando utilizar um serviço de escuta do grupo de disponibilidade, as VMs de base de dados tem de ser ligada a um balanceador de carga dedicado. A resolução de nomes em implementações apenas na nuvem a necessitem de uma todas as VMs de um sistema SAP (servidores de aplicações, servidor DBMS e (A) servidor SCS) estão na mesma rede virtual ou precisaria de uma camada de aplicação SAP a manutenção do ficheiro etc\host na ordem Para obter os nomes VM das VMs de SQL Server resolvido. Para evitar que o Azure está a atribuir novos endereços IP nos casos em que ambas as VMs estão incidentally encerramento, um deve atribuir endereços IP estáticos para as interfaces de rede desses VMs na configuração Always On (definir um endereço IP estático é descrito no [isto] [ virtual-networks-reserved-private-ip] artigo)

[comment]: <> (Blogues antigo)
[comment]: <> (< https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, < https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Existem especiais passos necessários quando criar a configuração de cluster WSFC em que o cluster tem um endereço IP especial atribuído, porque o Azure com a respetiva funcionalidade atual iria atribuir o nome do cluster o mesmo endereço IP, o nó de cluster é criado no. Isto significa que um passo manual tem de ser efetuado para atribuir um endereço IP diferente para o cluster.
* O serviço de escuta do grupo de disponibilidade vai ser criada no Azure com pontos finais de TCP/IP, que são atribuídos às VMs com as réplicas primários e secundários do grupo de disponibilidade.
* Poderá ser necessário para proteger estes pontos finais com ACLs.

[comment]: <> (Blogue de antigo de tarefas)
[comment]: <> (Os passos detalhados e necessities da instalação de uma configuração de AlwaysOn no Azure melhor são reportadas quando walking através do tutorial disponível [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Pré-configurada configuração AlwaysOn através da galeria do Azure < https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (A criação de um serviço de escuta do grupo de disponibilidade é melhor descrita no tutorial [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comment]: <> (Proteger pontos finais de rede com as ACLs são explicados melhor aqui:)
[comment]: <> (* < https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>)
[comment]: <> (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

É possível implementar um servidor sempre no grupo de disponibilidade SQL através de diferentes regiões do Azure, bem como. Esta funcionalidade tira partido da conectividade do Azure VNet a Vnet ([mais detalhes][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blogue de antigo de tarefas)
[comment]: <> (O programa de configuração de grupos de Disponibilidade AlwaysOn do SQL Server neste cenário é descrito aqui: < https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Resumo de elevada disponibilidade, SQL Server no Azure
Tendo em conta o facto de que o armazenamento do Azure está a proteger o conteúdo, não há uma menor razão para insist numa imagem de modo de espera de acesso frequente. Isto significa que o seu cenário de elevada disponibilidade necessita para apenas proteção contra os seguintes casos:

* Indisponibilidade da VM como um todo devido a manutenção no servidor de cluster no Azure ou outros motivos
* Problemas de software na instância do SQL Server
* Proteger contra erro manual em que dados são eliminados e recuperação do ponto no tempo é necessário

Observar um pode argue que os dois primeiros casos podem ser abrangidos por espelhamento da base de dados ou Always On, enquanto que as maiúsculas e minúsculas terceira só podem ser abrangida pelo envio de registos de tecnologias de correspondentes.

Tem de equilibrar a configuração mais complexa do Always On, em comparação com espelhamento de base de dados, com as vantagens de Always On. As vantagens podem ser apresentadas como:

* Réplicas secundárias legíveis.
* Cópias de segurança de réplicas secundárias.
* Melhor escalabilidade.
* Mais do que um réplicas secundárias.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Geral SQL Server para SAP no resumo do Azure
Existem muitos recomendações neste guia e recomendamos que leia-mais do que uma vez antes de planear a implementação do Azure. Em geral, no entanto, é necessário seguir os DBMS gerais dez principais em pontos de específicos do Azure:

[comment]: <> (2.3 um maior débito que que? A um VHD?)
1. Utilize a versão mais recente do DBMS, como o SQL Server 2014, o que tem as maioria das vantagens no Azure. Para o SQL Server, este é o SQL Server 2012 SP1 CU4, que inclui a funcionalidade de cópia de segurança com o Storage do Azure. No entanto, em conjunto com o SAP, recomendamos, pelo menos, CU1 do SQL Server 2014 SP1 ou SQL Server 2012 SP2 e o CU mais recente.
2. Planeie cuidadosamente a horizontal do sistema SAP no Azure para balancear o esquema do ficheiro de dados e restrições do Azure:
   * Não tem demasiados discos, mas tem espaço suficiente para garantir que pode alcançar o IOPS necessária.
   * Se não utilizar discos geridos, lembre-se de que o IOPS também estão limitado por conta de armazenamento do Azure e de que as contas de armazenamento estão limitadas dentro de cada subscrição do Azure ([mais detalhes][azure-subscription-service-limits]). 
   * Apenas faixa entre discos se precisar de alcançar um maior débito.
3. Nunca instalar software ou colocar todos os ficheiros que necessitam de persistência na unidade D:\ como é não permanente e nada nesta unidade é perdido um reinício do Windows.
4. Não utilize a colocação em cache no disco para armazenamento padrão do Azure.
5. Não utilize contas do Storage georreplicação do Azure.  Utilize localmente redundante para cargas de trabalho DBMS.
6. Utilize a solução do seu fornecedor DBMS HA/DR para replicar dados de base de dados.
7. Sempre utilizem a resolução de nome, não se baseiam em endereços IP.
8. Utilize a compressão de base de dados mais elevada possível. Para o SQL Server, esta é a compressão de página.
9. Seja cuidadoso com imagens do SQL Server no Azure Marketplace. Se utilizar um SQL Server, tem de alterar o agrupamento de instância antes de instalar qualquer sistema SAP NetWeaver no mesmo.
10. Instalar e configurar a monitorização de anfitrião de SAP para o Azure conforme descrito em [guia de implementação][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Informações específicas para SAP ASE no Windows
A partir do Microsoft Azure, pode migrar facilmente as aplicações SAP ASE existentes para máquinas virtuais do Azure. SAP ASE numa máquina Virtual permite-lhe reduzir o custo total de propriedade de implementação, gestão e manutenção de aplicações da empresa leque migrando facilmente estas aplicações para o Microsoft Azure. Com SAP ASE uma Máquina Virtual no Azure, os administradores e programadores podem continuar a utilizar o mesmo desenvolvimento e ferramentas de administração estão disponíveis no local.

Há um SLA para a Azure Virtual Machines, que pode ser encontrada aqui: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Estamos a certeza de que o Microsoft Azure máquinas virtuais alojadas efetua muito bem em comparação com outras ofertas de Virtualização de nuvem pública, mas resultados individuais podem variar. SAP dimensionamento números SAPS do SAP diferentes certificadas SKUs de VM é fornecido na nota SAP separado [1928533].

As instruções e recomendações in regard to a utilização do armazenamento do Azure, implementação de SAP VMs ou SAP monitorização, aplicam-se para implementações do SAP ASE em conjunto com aplicações SAP conforme indicado em toda os quatro primeiras capítulos deste documento.

### <a name="sap-ase-version-support"></a>Suporte para a versão de ASE SAP
SAP atualmente suporta SAP ASE versão 16.0 para utilização com produtos do SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou os controladores ODBC e de JDBC para ser utilizado com produtos do SAP Business Suite são fornecidas apenas através do Marketplace, serviço SAP em: <https://support.sap.com/swdc>.

Para instalações no local, não transferir as atualizações para o servidor do SAP ASE ou para os controladores JDBC e ODBC diretamente a partir de sites Sybase. Para obter informações detalhadas sobre correções de erros, o que são suportados para utilização com SAP Business Suite produtos no local e em Azure Virtual Machines consulte as notas de SAP seguintes:

* [1590719]
* [1973241]

Informações gerais sobre a execução do SAP Business Suite num SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados com o SAP ASE instalações em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implementação SAP ASE
De acordo com a descrição geral, SAP ASE executáveis devem estar localizado ou instalada na unidade de sistema do disco do SO da VM (unidade c:\). Normalmente, a maioria das SAP ASE sistema e as ferramentas de bases de dados não é realmente aproveitada rígido por carga de trabalho do SAP NetWeaver. Por conseguinte, as bases de dados e ferramentas de sistema (master, model, saptools, sybmgmtdb, sybsystemdb) podem permanecer na unidade C:\. 

Uma exceção pode ser a base de dados temporária que contém todas as tabelas de trabalho e tabelas temporárias criadas pelo ASE SAP, que, em caso de algumas ERP SAP e todas as cargas de trabalho BW, pode necessitar de mais elevado volume de dados ou volume de operações de e/s, que não é possível ajustar SO da VM original disco (unidade c:\).

Dependendo da versão do SAPInst/SWPM utilizado para instalar o sistema, pode conter a base de dados:

* Um único tempdb SAP ASE, o que é criado ao instalar o SAP ASE
* Um tempdb SAP ASE criado ao instalar SAP ASE e um saptempdb adicional criados pela rotina de instalação do SAP
* Um tempdb SAP ASE criado ao instalar SAP ASE e um tempdb adicional que foi criada manualmente (por exemplo a seguir a nota [1752266]) para satisfazer os requisitos de tempdb específico ERP/BW

Em caso de ERP específico ou todas as cargas de trabalho BW, faz sentido, in regard to desempenho, para manter os dispositivos tempdb a tempdb adicionalmente criado (por SWPM ou manualmente) numa unidade que não sejam C:\. Não se existir nenhum tempdb adicional, é recomendado para criar um (nota SAP [1752266]).

Para esses sistemas os seguintes passos devem ser efetuados para a tempdb criada além disso:

* Mover o primeiro dispositivo de tempdb para o primeiro dispositivo da base de dados SAP
* Adicionar dispositivos de tempdb para cada um dos VHDs que contêm um dispositivo da base de dados SAP

Esta configuração permite tempdb está a consumir mais espaço do que a unidade do sistema é capaz de fornecer. Como uma referência um pode verificar os tamanhos de dispositivo de tempdb em sistemas existentes, que são executadas no local. Ou este tipo de configuração seria ativar números IOPS contra tempdb, que não pode ser fornecida com a unidade do sistema. Novamente os sistemas que estejam a executar no local podem ser utilizados para monitorizar a carga de trabalho de e/s contra tempdb.

Nunca colocar todos os dispositivos SAP ASE para a unidade D:\ da VM. Isto também se aplica a tempdb, mesmo se os objetos mantidos a tempdb apenas são temporários.

#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações de onde a largura de banda de e/s pode ser um fator restritivo, todas as medidas, o que reduz o IOPS poderão ajudar a Stretch Database a carga de trabalho um pode executar um cenário de IaaS, como o Azure. Por conseguinte, é vivamente recomendado para se certificar de que a compressão de SAP ASE é utilizada antes de carregar uma base de dados SAP para o Azure.

A recomendação para efetuar a compressão antes de carregar para o Azure, se não estiver já implementada é dado fora vários motivos:

* A quantidade de dados para ser carregado para o Azure for inferior
* A duração da execução de compressão é mais curta, partindo do princípio que um pode utilizar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou inferior e/s latência no local
* Tamanhos de base de dados mais pequenos poderão originar menos custos para alocação de disco

Compressão de dados e de LOB funciona numa VM alojada em Azure Virtual Machines, como no local. Para obter mais detalhes sobre como verificar se a compressão já se encontra em utilização na base de dados SAP ASE existente, consulte a nota SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar instâncias de base de dados
Para sistemas SAP, o que estiver a utilizar SAP ASE como plataforma base de dados, o DBACockpit está acessível como janelas do browser incorporado numa transação DBACockpit ou Webdynpro. No entanto, todas as funcionalidades de monitorização e administrar a base de dados estão disponível a implementação de Webdynpro de apenas o DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela implementação Webdynpro do DBACockpit. Siga a nota SAP [1245200] para ativar a utilização de webdynpros e gerar as necessárias. Ao seguir as instruções nas notas de acima, também configurar Gestor de comunicação de Internet (icm) juntamente com as portas a utilizar para ligações http e https. A predefinição para http tem o seguinte aspeto:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
> 
> ICM/server_port_1 = PROT = HTTPS, porta = 443$ $, PROCTIMEOUT = 600, tempo limite = 600
> 
> 

e as ligações geradas na transação DBACockpit terá um aspeto semelhante a isto:

> https://`<fullyqualifiedhostname`>: sap/44300/bc/sap/webdynpro/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: sap/8000/bc/sap/webdynpro/dba_cockpit
> 
> 

Consoante se e como Máquina Virtual do Azure que aloja o sistema SAP estejam ligados através do site para site multilocal ou ExpressRoute (implementação de vários locais) tem de certificar-se de que esse ICM está a utilizar um nome de anfitrião totalmente qualificado que pode ser resolvido na máquina onde está a tentar abrir o DBACockpit do. Consulte a nota SAP [773830] para compreender a forma como ICM determina o nome de anfitrião totalmente qualificado, consoante os parâmetros de perfil e parâmetro de conjunto icm/host_name_full explicitamente se necessário.

Se tiver implementado a VM num cenário apenas na nuvem sem conectividade entre instalações, entre no local e o Azure, terá de definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM tem o seguinte aspeto:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Podem encontrar mais detalhes relacionados com o nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Na definição de parâmetro de perfil do SAP icm/host_name_full como o nome DNS da VM do Azure a ligação poderá ter um aspeto semelhante a:

> sap/https://mydomainlabel.westeurope.cloudapp.NET:44300/bc/sap/webdynpro/dba_cockpit
> 
> sap/http://mydomainlabel.westeurope.cloudapp.NET:8000/bc/sap/webdynpro/dba_cockpit
> 
> 

Neste caso, terá de certificar-se de que:

* Adicionar regras de entrada para o grupo de segurança de rede no portal do Azure para as portas TCP/IP utilizada para comunicar com ICM
* Adicionar regras de entrada para a configuração da Firewall do Windows para as portas TCP/IP utilizada para comunicar com o ICM

Para um automatizada importado de todas as correções disponíveis, é recomendado aplicar periodicamente a coleção de correção nota SAP aplica-se a sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar informações adicionais sobre DBA Cockpit para SAP ASE nas notas de SAP seguintes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação para SAP ASE
Quando implementar o SAP ASE no Azure, a metodologia de cópia de segurança deve ser revista. Mesmo que o sistema não é um sistema produtivo, a base de dados SAP hospedada SAP ASE deve ser feita periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança agora é menos importante no que respeita à compensação uma falha de armazenamento. A razão principal para manter um plano de cópia de segurança e restauro adequado é maior do que pode compensar erros lógica/manual, fornecendo o ponto de funcionalidades de recuperação de tempo. Por isso, o objetivo é qualquer uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. Por exemplo, foi possível transferir a partir de uma configuração de camada 2 de SAP para uma configuração de sistema de 3 camadas do mesmo sistema restaurando uma cópia de segurança.

Cópia de segurança e restaurar uma base de dados no Azure funciona da mesma forma, como no local. Consulte as notas de SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre a criação de captura de configurações e agendamento de cópias de segurança. Dependendo da sua estratégia e as suas necessidades, pode configurar base de dados e registo de informações de disco para um dos discos existentes ou adicionar um disco adicional para a cópia de segurança. Para reduzir o risco de perda de dados em caso de erro, é recomendado utilizar um disco onde não está localizado a nenhum dispositivo de base de dados.

Para além de dados e LOB compressão SAP ASE também oferece compressão de cópias de segurança. Para ocupam menos espaço com capturas de base de dados e registo se recomenda utilizar compressão de cópias de segurança. Para obter mais informações, consulte a nota SAP [1588316]. Comprimir a cópia de segurança também é fundamental para reduzir a quantidade de dados a serem transferidos, se planear transferir cópias de segurança ou os VHDs que contêm informações de cópia de segurança da Máquina Virtual do Azure no local.

Não utilize unidade D:\ como destino de informação de registo ou da base de dados.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para as cópias de segurança/restauros
Tal como em implementações bare-metal, o desempenho de cópia de segurança/restauro está dependente de quantos volumes podem ser lidas em paralelo e que o débito desses volumes poderão. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com apenas oito threads de CPU. Por conseguinte, um pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, o menor o débito global ler
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança
* Os destinos menos (Stripe diretórios, discos) para escrever a cópia de segurança, o menor o débito

Para aumentar o número de destinos ao escrever no existem duas opções que podem ser utilizado/combinado consoante as suas necessidades:

* Striping o volume de destino de cópia de segurança ao longo de vários discos montados para melhorar o débito IOPS nesse volume repartidos
* Criar uma configuração de captura no nível de SAP ASE, que utiliza mais do que um diretório de destino para escrever a captura para

Striping um volume através de vários discos montados tiver sido apresentada anteriormente neste guia. Para obter mais informações sobre como utilizar vários diretórios na configuração de captura SAP ASE, consulte a documentação no procedimento armazenado sp_config_dump, que é utilizado para criar a configuração de captura no [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação após desastre com as VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o ASE de SAP SAP Sybase replicação servidor (SRS) fornece uma solução de reserva dinâmica transfira a transferência de transações da base de dados para uma localização distantes no modo assíncrono. 

A instalação e a operação do SRS funciona bem funcionalmente uma VM alojada nos serviços de Máquina Virtual do Azure, como no local.

ASE HADR através do servidor de replicação de SAP planeado com uma versão futura. Será testado com e lançado para plataformas do Microsoft Azure, assim que está disponível.

## <a name="specifics-to-sap-ase-on-linux"></a>Informações específicas para SAP ASE no Linux
A partir do Microsoft Azure, pode migrar facilmente as aplicações SAP ASE existentes para máquinas virtuais do Azure. SAP ASE numa máquina Virtual permite-lhe reduzir o custo total de propriedade de implementação, gestão e manutenção de aplicações da empresa leque migrando facilmente estas aplicações para o Microsoft Azure. Com SAP ASE uma Máquina Virtual no Azure, os administradores e programadores podem continuar a utilizar o mesmo desenvolvimento e ferramentas de administração estão disponíveis no local.

Para implementar as VMs do Azure é importante saber os SLAs oficiais, que podem ser encontrados aqui: <https://azure.microsoft.com/support/legal/sla>

Informações de dimensionamento de SAP e uma lista de SAP certificada SKUs de VM é fornecido na nota SAP [1928533]. SAP adicional documentos de dimensionamento de máquinas virtuais do Azure podem ser encontradas aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

As instruções e recomendações in regard to a utilização do armazenamento do Azure, implementação de SAP VMs ou SAP monitorização, aplicam-se para implementações do SAP ASE em conjunto com aplicações SAP conforme indicado em toda os quatro primeiras capítulos deste documento.

As seguintes notas SAP dois incluem informações gerais sobre ASE no Linux e ASE na nuvem:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte para a versão de ASE SAP
SAP atualmente suporta SAP ASE versão 16.0 para utilização com produtos do SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou os controladores ODBC e de JDBC para ser utilizado com produtos do SAP Business Suite são fornecidas apenas através do Marketplace, serviço SAP em: <https://support.sap.com/swdc>.

Para instalações no local, não transferir as atualizações para o servidor do SAP ASE ou para os controladores JDBC e ODBC diretamente a partir de sites Sybase. Para obter informações detalhadas sobre correções de erros, o que são suportados para utilização com SAP Business Suite produtos no local e em Azure Virtual Machines consulte as notas de SAP seguintes:

* [1590719]
* [1973241]

Informações gerais sobre a execução do SAP Business Suite num SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados com o SAP ASE instalações em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implementação SAP ASE
De acordo com a descrição geral, SAP ASE executáveis devem estar localizado ou instalado para o sistema de ficheiros de raiz da VM (/sybase). Normalmente, a maioria das SAP ASE sistema e as ferramentas de bases de dados não é realmente aproveitada rígido por carga de trabalho do SAP NetWeaver. Por conseguinte, as bases de dados e ferramentas de sistema (master, model, saptools, sybmgmtdb, sybsystemdb) podem permanecer numa, bem como o sistema de ficheiros de raiz. 

Uma exceção pode ser a base de dados temporária que contém todas as tabelas de trabalho e tabelas temporárias criadas pelo ASE SAP, que, em caso de algumas ERP SAP e todas as cargas de trabalho BW, poderá necessitar de mais elevado volume de dados ou operações de e/s, volume que não é possível ajustar SO da VM original disco.

Dependendo da versão do SAPInst/SWPM utilizado para instalar o sistema, pode conter a base de dados:

* Um único tempdb SAP ASE, o que é criado ao instalar o SAP ASE
* Um tempdb SAP ASE criado ao instalar SAP ASE e um saptempdb adicional criados pela rotina de instalação do SAP
* Um tempdb SAP ASE criado ao instalar SAP ASE e um tempdb adicional que foi criada manualmente (por exemplo a seguir a nota [1752266]) para satisfazer os requisitos de tempdb específico ERP/BW

Em caso de ERP específico ou todas as cargas de trabalho BW, faz sentido, in regard to desempenho, para manter os dispositivos tempdb a tempdb adicionalmente criado (por SWPM ou manualmente) num sistema de ficheiros separado, que pode ser representado por um disco de dados do Azure individual ou um spannin RAID do Linux g vários discos de dados do Azure. Não se existir nenhum tempdb adicional, é recomendado para criar um (nota SAP [1752266]).

Para esses sistemas os seguintes passos devem ser efetuados para a tempdb criada além disso:

* Mover o diretório tempdb primeiro para o primeiro sistema de ficheiros da base de dados SAP
* Adicionar os diretórios de tempdb para cada um dos discos que contém um sistema de ficheiros da base de dados SAP

Esta configuração permite tempdb está a consumir mais espaço do que a unidade do sistema é capaz de fornecer. Como uma referência um pode verificar os tamanhos de diretório tempdb em sistemas existentes, que são executadas no local. Ou este tipo de configuração seria ativar números IOPS contra tempdb, que não pode ser fornecida com a unidade do sistema. Novamente os sistemas que estejam a executar no local podem ser utilizados para monitorizar a carga de trabalho de e/s contra tempdb.

Nunca colocar qualquer diretórios SAP ASE para /mnt ou /mnt/resource da VM. Isto também se aplica a tempdb, mesmo se os objetos mantidos a tempdb apenas são temporários porque /mnt ou /mnt/resource é um espaço de temp da VM do Azure predefinido, que não é persistente. Obter mais detalhes sobre o espaço temporário de VM do Azure podem ser encontrados na [neste artigo][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações de onde a largura de banda de e/s pode ser um fator restritivo, todas as medidas, o que reduz o IOPS poderão ajudar a Stretch Database a carga de trabalho um pode executar um cenário de IaaS, como o Azure. Por conseguinte, é vivamente recomendado para se certificar de que a compressão de SAP ASE é utilizada antes de carregar uma base de dados SAP para o Azure.

A recomendação para efetuar a compressão antes de carregar para o Azure, se não estiver já implementada é dado fora vários motivos:

* A quantidade de dados para ser carregado para o Azure for inferior
* A duração da execução de compressão é mais curta, partindo do princípio que um pode utilizar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou inferior e/s latência no local
* Tamanhos de base de dados mais pequenos poderão originar menos custos para alocação de disco

Compressão de dados e de LOB funciona numa VM alojada em Azure Virtual Machines, como no local. Para obter mais detalhes sobre como verificar se a compressão já se encontra em utilização na base de dados SAP ASE existente, consulte a nota SAP [1750510]. Para obter informações adicionais sobre a compressão de base de dados, consulte a nota SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar instâncias de base de dados
Para sistemas SAP, o que estiver a utilizar SAP ASE como plataforma base de dados, o DBACockpit está acessível como janelas do browser incorporado numa transação DBACockpit ou Webdynpro. No entanto, todas as funcionalidades de monitorização e administrar a base de dados estão disponível a implementação de Webdynpro de apenas o DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela implementação Webdynpro do DBACockpit. Siga a nota SAP [1245200] para ativar a utilização de webdynpros e gerar as necessárias. Ao seguir as instruções nas notas de acima, também configurar Gestor de comunicação de Internet (icm) juntamente com as portas a utilizar para ligações http e https. A predefinição para http tem o seguinte aspeto:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
> 
> ICM/server_port_1 = PROT = HTTPS, porta = 443$ $, PROCTIMEOUT = 600, tempo limite = 600
> 
> 

e as ligações geradas na transação DBACockpit terá um aspeto semelhante a isto:

> https://`<fullyqualifiedhostname`>: sap/44300/bc/sap/webdynpro/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: sap/8000/bc/sap/webdynpro/dba_cockpit
> 
> 

Consoante se e como Máquina Virtual do Azure que aloja o sistema SAP estejam ligados através do site para site multilocal ou ExpressRoute (implementação de vários locais) tem de certificar-se de que esse ICM está a utilizar um nome de anfitrião totalmente qualificado que pode ser resolvido na máquina onde está a tentar abrir o DBACockpit do. Consulte a nota SAP [773830] para compreender a forma como ICM determina o nome de anfitrião totalmente qualificado, consoante os parâmetros de perfil e parâmetro de conjunto icm/host_name_full explicitamente se necessário.

Se tiver implementado a VM num cenário apenas na nuvem sem conectividade entre instalações, entre no local e o Azure, terá de definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM tem o seguinte aspeto:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Podem encontrar mais detalhes relacionados com o nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Na definição de parâmetro de perfil do SAP icm/host_name_full como o nome DNS da VM do Azure a ligação poderá ter um aspeto semelhante a:

> sap/https://mydomainlabel.westeurope.cloudapp.NET:44300/bc/sap/webdynpro/dba_cockpit
> 
> sap/http://mydomainlabel.westeurope.cloudapp.NET:8000/bc/sap/webdynpro/dba_cockpit
> 
> 

Neste caso, terá de certificar-se de que:

* Adicionar regras de entrada para o grupo de segurança de rede no portal do Azure para as portas TCP/IP utilizada para comunicar com ICM
* Adicionar regras de entrada para a configuração da Firewall do Windows para as portas TCP/IP utilizada para comunicar com o ICM

Para um automatizada importado de todas as correções disponíveis, é recomendado aplicar periodicamente a coleção de correção nota SAP aplica-se a sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar informações adicionais sobre DBA Cockpit para SAP ASE nas notas de SAP seguintes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação para SAP ASE
Quando implementar o SAP ASE no Azure, a metodologia de cópia de segurança deve ser revista. Mesmo que o sistema não é um sistema produtivo, a base de dados SAP hospedada SAP ASE deve ser feita periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança agora é menos importante no que respeita à compensação uma falha de armazenamento. A razão principal para manter um plano de cópia de segurança e restauro adequado é maior do que pode compensar erros lógica/manual, fornecendo o ponto de funcionalidades de recuperação de tempo. Por isso, o objetivo é qualquer uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. Por exemplo, foi possível transferir a partir de uma configuração de camada 2 de SAP para uma configuração de sistema de 3 camadas do mesmo sistema restaurando uma cópia de segurança.

Cópia de segurança e restaurar uma base de dados no Azure funciona da mesma forma, como no local. Consulte as notas de SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre a criação de captura de configurações e agendamento de cópias de segurança. Dependendo da sua estratégia e as suas necessidades, pode configurar base de dados e registo de informações de disco para um dos discos existentes ou adicionar um disco adicional para a cópia de segurança. Para reduzir o risco de perda de dados em caso de erro que é recomendado utilizar um disco onde não está localizado a nenhum diretório/ficheiro de base de dados.

Para além de dados e LOB compressão SAP ASE também oferece compressão de cópias de segurança. Para ocupam menos espaço com capturas de base de dados e registo se recomenda utilizar compressão de cópias de segurança. Para obter mais informações, consulte a nota SAP [1588316]. Comprimir a cópia de segurança também é fundamental para reduzir a quantidade de dados a serem transferidos, se planear transferir cópias de segurança ou os VHDs que contêm informações de cópia de segurança da Máquina Virtual do Azure no local.

Não utilize a VM do Azure espaço temporário /mnt ou /mnt/resource como destino de informação de registo ou da base de dados.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para as cópias de segurança/restauros
Tal como em implementações bare-metal, o desempenho de cópia de segurança/restauro está dependente de quantos volumes podem ser lidas em paralelo e que o débito desses volumes poderão. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com apenas oito threads de CPU. Por conseguinte, um pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, o menor o débito global ler
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança
* Os destinos menos (software RAID, discos do Linux) para escrever a cópia de segurança, o menor o débito

Para aumentar o número de destinos ao escrever no existem duas opções que podem ser utilizado/combinado consoante as suas necessidades:

* Striping o volume de destino de cópia de segurança ao longo de vários discos montados para melhorar o débito IOPS nesse volume repartidos
* Criar uma configuração de captura no nível de SAP ASE, que utiliza mais do que um diretório de destino para escrever a captura para

Striping um volume através de vários discos montados tiver sido apresentada anteriormente neste guia. Para obter mais informações sobre como utilizar vários diretórios na configuração de captura SAP ASE, consulte a documentação no procedimento armazenado sp_config_dump, que é utilizado para criar a configuração de captura no [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação após desastre com as VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o ASE de SAP SAP Sybase replicação servidor (SRS) fornece uma solução de reserva dinâmica transfira a transferência de transações da base de dados para uma localização distantes no modo assíncrono. 

A instalação e a operação do SRS funciona bem funcionalmente uma VM alojada nos serviços de Máquina Virtual do Azure, como no local.

ASE HADR através do servidor de replicação de SAP não é suportada neste ponto no tempo. Pode ser testada com e lançado para plataformas do Microsoft Azure no futuro.

## <a name="specifics-to-oracle-database-on-windows"></a>Informações específicas para a base de dados Oracle no Windows
Oracle software é suportada pela Oracle para ser executada no Hyper-V do Microsoft Windows e do Azure. Para obter detalhes sobre o suporte geral do Hyper-V do Windows e o Azure, consulte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Seguintes suporte geral, é suportado, bem como do cenário específico das aplicações SAP tirar partido das bases de dados Oracle. Os detalhes são denominados nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte da versão Oracle
Versões de Oracle e versões de SO correspondentes, que são suportadas para executar o SAP Oracle em Azure Virtual Machines pode ser encontrado na nota SAP [2039619].

Informações gerais sobre a execução do SAP Business Suite no Oracle podem ser encontradas na 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle diretrizes de configuração para instalações de SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração do armazenamento
Apenas única instância Oracle utilizando discos de formatada com NTFS é suportada. Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS com base em VHDs ou discos geridos. Estes discos são montados à VM do Azure e baseiam-se no armazenamento de BLOBS de página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou discos geridos (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer tipo de unidades de rede ou partilhas remotas, como os serviços de ficheiros do Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>

são **não** suportado para ficheiros de base de dados Oracle!

Utilizar discos com base no armazenamento de BLOBS de página do Azure ou discos geridos, as instruções efetuadas neste documento no capítulo [a colocação em cache para discos de dados e VMs] [ dbms-guide-2.1] e [armazenamento do Microsoft Azure] [ dbms-guide-2.3] aplicam-se a implementações com a base de dados Oracle bem.

Tal como explicado anteriormente na parte geral do documento, quotas de débito IOPS para discos do Azure existem. As quotas exatas dependendo do tipo VM utilizadas. Pode encontrar uma lista dos tipos VM com os respetivos quotas [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure suportados, consulte a nota SAP [1928533].

Desde que a quota atual de IOPS por disco cumprir os requisitos, é possível armazenar todos os ficheiros de base de dados num único disco montado. 

Se forem necessários mais de IOPS, é vivamente que utilize agrupamentos de armazenamento de janela (apenas disponível no Windows Server 2012 e posterior) ou Windows striping para o Windows 2008 R2 para criar um dispositivo lógico grande através de vários discos montados (Consulte também o capítulo [RAID de software] [ dbms-guide-2.2] deste documento). Esta abordagem simplifica a sobrecarga administrativa para gerir o espaço em disco e evita o esforço para distribuir manualmente ficheiros entre múltiplos discos montados.

#### <a name="backup--restore"></a>Cópia de Segurança / Restauro
Para cópia de segurança / restaurar a funcionalidade, o SAP BR * as ferramentas para Oracle são suportadas da mesma forma que em sistemas operativos Windows Server padrão e o Hyper-V. O Gestor de recuperação de Oracle (RMAN) também é suportado para cópias de segurança em disco e restaurar a partir do disco.

#### <a name="high-availability"></a>Elevada Disponibilidade
Oracle Data Guard é suportado para elevada disponibilidade e fins de recuperação após desastre. Podem ser encontrados detalhes no [isto] [ virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como conjuntos de disponibilidade do Azure ou SAP monitorização aplicam-se conforme descrito nos três primeiros capítulos deste documento para implementações de VMs com a base de dados Oracle bem.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Informações específicas para a base de dados Oracle no Oracle Linux
Oracle software é suportada pela Oracle para ser executada no Hyper-V do Microsoft Windows e do Azure. Para obter detalhes sobre o suporte geral do Hyper-V do Windows e o Azure, consulte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Seguintes suporte geral, é suportado, bem como do cenário específico das aplicações SAP tirar partido das bases de dados Oracle. Os detalhes são denominados nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte da versão Oracle
Versões de Oracle e versões de SO correspondentes, que são suportadas para executar o SAP Oracle em Azure Virtual Machines pode ser encontrado na nota SAP [2039619].

Informações gerais sobre a execução do SAP Business Suite no Oracle podem ser encontradas na 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Oracle diretrizes de configuração para instalações de SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração do armazenamento
Só é suportada Oracle utilizando ext3, ext4 xfs formatadas e discos de instância única. Todos os ficheiros de base de dados devem ser armazenados nestes sistemas de ficheiros com base em VHDs ou discos geridos. Estes discos são montados à VM do Azure e baseiam-se no armazenamento de BLOBS de página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou discos geridos (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer tipo de unidades de rede ou partilhas remotas, como os serviços de ficheiros do Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>

são **não** suportado para ficheiros de base de dados Oracle!

Utilizar discos com base no armazenamento de BLOBS de página do Azure ou discos geridos, as instruções efetuadas neste documento no capítulo [a colocação em cache para discos de dados e VMs] [ dbms-guide-2.1] e [armazenamento do Microsoft Azure] [ dbms-guide-2.3] aplicam-se a implementações com a base de dados Oracle bem.

Tal como explicado anteriormente na parte geral do documento, quotas de débito IOPS para discos do Azure existem. As quotas exatas dependendo do tipo VM utilizadas. Pode encontrar uma lista dos tipos VM com os respetivos quotas [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure suportados, consulte a nota SAP [1928533]

Desde que a quota atual de IOPS por disco cumprir os requisitos, é possível armazenar todos os ficheiros de base de dados num único disco montado. 

Se forem necessários mais de IOPS, é vivamente recomendado utilizar LVM (Gestor de Volume lógica) ou MDADM para criar um grande volume lógico através de vários discos montados. Consulte também capítulo [RAID de Software] [ dbms-guide-2.2] deste documento. Esta abordagem simplifica a sobrecarga administrativa para gerir o espaço em disco e evita o esforço para distribuir manualmente ficheiros entre múltiplos discos montados.

#### <a name="backup--restore"></a>Cópia de Segurança / Restauro
Para cópia de segurança / restaurar a funcionalidade, o SAP BR * as ferramentas para Oracle são suportadas da mesma forma que em bare-metal e Hyper-V. O Gestor de recuperação de Oracle (RMAN) também é suportado para cópias de segurança em disco e restaurar a partir do disco.

#### <a name="high-availability"></a>Elevada Disponibilidade
Oracle Data Guard é suportado para elevada disponibilidade e fins de recuperação após desastre. Podem ser encontrados detalhes no [isto] [ virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como conjuntos de disponibilidade do Azure ou SAP monitorização aplicam-se conforme descrito nos três primeiros capítulos deste documento para implementações de VMs com a base de dados Oracle bem.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Informações específicas para a base de dados do SAP MaxDB no Windows
### <a name="sap-maxdb-version-support"></a>Suporte para a versão de MaxDB SAP
SAP atualmente suporta SAP MaxDB versão 7.9 para utilização com produtos SAP NetWeaver baseado no Azure. Todas as atualizações para o servidor SAP MaxDB ou os controladores ODBC e de JDBC para ser utilizado com produtos baseados em SAP NetWeaver são fornecidas apenas através do Marketplace, serviço SAP em <https://support.sap.com/swdc>.
Informações gerais sobre a execução de SAP NetWeaver no SAP MaxDB podem ser encontradas em <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Suportado tipos de versões do Microsoft Windows e a VM do Azure para SAP MaxDB DBMS
Para localizar a versão suportada do Microsoft Windows para SAP MaxDB DBMS no Azure, consulte:

* [Matriz de disponibilidade de produto do SAP (PAM)][sap-pam]
* A nota [1928533]

Recomenda-se vivamente a utilização a versão mais recente do sistema operativo Microsoft Windows, que é o Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentação de MaxDB SAP disponíveis
Pode encontrar a lista atualizada de documentação do SAP MaxDB na seguinte nota SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP MaxDB para SAP instalações em VMs do Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração de armazenamento
Melhores práticas do storage do Azure para SAP MaxDB siga as recomendações gerais mencionadas capítulo [estrutura de uma implementação de RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Como outras bases de dados SAP MaxDB também tem ficheiros de registo e de dados. No entanto, o termo correto na terminologia de SAP MaxDB é "volume" (não "ficheiro"). Por exemplo, existem SAP MaxDB volumes de dados e os volumes de registo. Não confunda estes com volumes de disco de SO. 
> 
> 

Em suma tem de:

* Se utilizar contas de armazenamento do Azure, configure a conta de armazenamento do Azure que contém os volumes de dados e de registo do SAP MaxDB (ou seja, ficheiros) como **armazenamento redundante Local (LRS)** conforme especificado no capítulo [armazenamento do Microsoft Azure][dbms-guide-2.3].
* Separe o caminho de e/s para volumes de dados SAP MaxDB (ou seja, ficheiros) do caminho de e/s para volumes de registo (ou seja, ficheiros). Isto significa que os volumes de dados SAP MaxDB (ou seja, ficheiros) têm de ser instaladas numa unidade lógica e volumes de registo do SAP MaxDB (ou seja, ficheiros) têm de ser instaladas na outra unidade lógica.
* Definir o tipo de colocação em cache adequado para cada disco, dependendo se utilizá-la para SAP MaxDB registo ou dados de volumes (ou seja, ficheiros) e, se utilizar o Azure Standard ou Premium Storage do Azure, conforme descrito em capítulo [a colocação em cache para discos de dados e VMs] [dbms-guide-2.1].
* Desde que a quota atual de IOPS por disco cumprir os requisitos, é possível armazenar todos os volumes de dados num único disco montado e também armazena todos os volumes de registo da base de dados noutro disco montado único.
* Se mais IOPS e/ou espaço forem necessário, é vivamente recomendado utilizar agrupamentos de armazenamento a janela da Microsoft (apenas disponíveis no Microsoft Windows Server 2012 e posterior) ou striping do Microsoft Windows para o Microsoft Windows 2008 R2 para criar um dispositivo lógico grande através de vários discos montados. Consulte também capítulo [RAID de Software] [ dbms-guide-2.2] deste documento. Esta abordagem simplifica a sobrecarga administrativa para gerir o espaço em disco e evita o esforço de distribuição manualmente ficheiros entre múltiplos discos montados.
* Para os requisitos mais altos de IOPS, pode utilizar Premium Storage do Azure, que está disponível no-série DS e VMs de série GS.

![Configuração de referência da VM do IaaS do Azure para SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Cópia de segurança e restauro
Quando implementar o SAP MaxDB no Azure, tem de consultar a metodologia de cópia de segurança. Mesmo que o sistema não é um sistema produtivo, a base de dados SAP hospedada SAP MaxDB deve ser feita periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança agora é menos importante em termos de proteger o sistema contra a falha de armazenamento e mais importantes falhas administrativas ou operacionais. A razão principal para manter uma cópia de segurança adequada e o plano de restauro é, de modo a que pode compensar lógico ou manual erros ao fornecer capacidades de ponto no tempo de recuperação. Por isso, o objetivo é utilizar cópias de segurança para restaurar a base de dados até um certo ponto no tempo ou utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. Por exemplo, foi possível transferir a partir de uma configuração de camada 2 de SAP para uma configuração de sistema de 3 camadas do mesmo sistema restaurando uma cópia de segurança.

Cópia de segurança e restaurar uma base de dados no Azure funciona da mesma forma que realiza sistemas no local, pelo que pode utilizar ferramentas de cópia de segurança/restauro SAP MaxDB padrão, que são descritas dos documentos de documentação SAP MaxDB listados na nota SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações de desempenho para cópia de segurança e restauro
Tal como em implementações bare-metal, cópia de segurança e restauro de desempenho está dependente de quantos volumes podem ser lidas em paralelo e o débito desses volumes. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com oito threads de CPU. Por conseguinte, um pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, o menor o débito de leitura geral
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança
* Os destinos menos (Stripe diretórios, discos) para escrever a cópia de segurança, menor o débito

Para aumentar o número de destinos ao escrever no, existem duas opções que pode utilizar, possivelmente, em combinação, consoante as suas necessidades:

* Dedicar volumes separados para cópia de segurança
* Striping o volume de destino de cópia de segurança ao longo de vários discos montados para melhorar o débito IOPS nesse volume de disco repartidos
* Ter dispositivos de disco lógico dedicado separado para:
  * SAP volumes de cópia de segurança MaxDB (ou seja, ficheiros)
  * SAP MaxDB volumes de dados (ou seja, ficheiros)
  * SAP MaxDB volumes de registo (ou seja, ficheiros)

Striping um volume através de vários discos montados foi mencionado anteriormente na capítulo [RAID de Software] [ dbms-guide-2.2] deste documento. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outros
Todos os outros tópicos gerais, como conjuntos de disponibilidade do Azure ou SAP monitorização também se aplicam, tal como descrito nos três primeiros capítulos deste documento para implementações de VMs com a base de dados SAP MaxDB.
Outras definições específicas do SAP MaxDB são transparentes para as VMs do Azure e são descritas nos documentos diferentes listados na nota SAP [767598] e nestas notas SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Informações específicas para SAP liveCache no Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache suporte da versão
Versão mínima do liveCache SAP suportada em máquinas virtuais do Azure é **SAP LC/LCAPPS 10.0 SP 25** incluindo **liveCache 7.9.08.31** e **Localizador compilação 25**, lançada para **EhP 2 para SAP SCM 7.0** e planos superiores.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Suportado tipos de versões do Microsoft Windows e a VM do Azure para SAP liveCache DBMS
Para localizar a versão suportada do Microsoft Windows para SAP liveCache no Azure, consulte:

* [Matriz de disponibilidade de produto do SAP (PAM)][sap-pam]
* A nota [1928533]

Recomenda-se vivamente a utilização a versão mais recente do sistema operativo Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache diretrizes de configuração para instalações de SAP em VMs do Azure
#### <a name="recommended-azure-vm-types"></a>Recomendado tipos VM do Azure
Como SAP liveCache é uma aplicação que executa cálculos grandes, a quantidade e velocidade de RAM e da CPU tem uma influência principais no desempenho de liveCache SAP. 

Para os tipos de VM do Azure suportados pelo SAP (nota SAP [1928533]), todos os recursos de CPU virtuais atribuídos à VM são apoiados por recursos de CPU físicos dedicados do hipervisor. Ocorre nenhum provocam um aprovisionamento (e, por conseguinte, não disputa pelos recursos da CPU).

Da mesma forma, para os tipos de instância do todas as VM do Azure suportados pelo SAP, a memória da VM é 100% mapeado para a memória física - provocam um aprovisionamento (compromisso superior), por exemplo, não é utilizado.

Esta perspetiva, recomenda vivamente a utilizar o novo tipo de série D ou VM do Azure-série DS (em combinação com o Premium Storage do Azure), que têm processadores mais rápidos de 60% a série de D. Para a maior carga de RAM e da CPU, pode utilizar série G e VMs de série GS (em combinação com o Premium Storage do Azure) com o Intel mais recente?? Xeon?? processador E5 v3 família, que tem duas vezes a memória e quatro vezes o armazenamento de unidade de estado sólido (SSDs) de série de D/DS.

#### <a name="storage-configuration"></a>Configuração de armazenamento
Como SAP liveCache baseia-se na tecnologia de SAP MaxDB, o armazenamento do Azure melhores recomendados mencionados para SAP MaxDB capítulo [configuração de armazenamento] [ dbms-guide-8.4.1] também são válidas para SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>VM do Azure dedicada para liveCache
Como SAP liveCache intensively utiliza de cálculo adequada, para utilização produtiva recomenda vivamente para implementar em dedicado Azure Máquina Virtual. 

![Dedicado VM do Azure para liveCache para caso de utilização produtivos][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Cópia de Segurança e Restauro
cópia de segurança e restauro, incluindo as considerações de desempenho, já descritas capítulos de SAP MaxDB relevantes [cópia de segurança e restaurar] [ dbms-guide-8.4.2] e [considerações de desempenho para cópia de segurança e restaurar][dbms-guide-8.4.3]. 

#### <a name="other"></a>Outros
Todos os outros tópicos gerais já estão descritos em MaxDB de SAP relevantes [isto] [ dbms-guide-8.4.4] capítulo. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Informações específicas para o servidor de conteúdo de SAP no Windows
O servidor de conteúdo de SAP é um componente separado, com base no servidor para armazenar o conteúdo como documentos Eletrónicos em diferentes formatos. O servidor de conteúdo do SAP é fornecido pelo desenvolvimento de tecnologia e deve ser entre-aplicação utilizada para quaisquer aplicações SAP. Este é instalado num sistema separado. O conteúdo típico é materiais de formação e a documentação do armazém de dados de conhecimento ou técnicas drawings provenientes de mySAP PLM sistema de gestão de documentos. 

### <a name="sap-content-server-version-support"></a>Suporte de versão do servidor de conteúdo de SAP
SAP atualmente suporta:

* **Servidor de conteúdo de SAP** com a versão **6.50 (e posterior)**
* **SAP MaxDB versão 7.9**
* **Microsoft IIS (Internet Information Server) versão 8.0 (e posterior)**

É altamente recomendado para utilizar a versão mais recente SAP do servidor de conteúdo, que no momento da escrita deste documento é **6.50 SP4**e a versão mais recente do **Microsoft IIS 8.5**. 

Verifique as versões suportadas mais recente do servidor de conteúdo de SAP e Microsoft IIS no [SAP produto disponibilidade matriz (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de Microsoft Windows e a VM do Azure de suportados para o servidor de conteúdo de SAP
Para obter a versão suportada do Windows para o servidor de conteúdo de SAP no Azure, consulte:

* [Matriz de disponibilidade de produto do SAP (PAM)][sap-pam]
* A nota [1928533]

Recomenda-se vivamente a utilização a versão mais recente do Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do servidor de conteúdo de SAP para SAP instalações em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Se configurar o servidor de conteúdo do SAP para armazenar os ficheiros na base de dados SAP MaxDB, todo o armazenamento do Azure melhores práticas recomendação mencionada para SAP MaxDB capítulo [configuração de armazenamento] [ dbms-guide-8.4.1] também são válidas para o cenário de servidor de conteúdo de SAP. 

Se configurar o servidor de conteúdo do SAP para armazenar os ficheiros no sistema de ficheiros, recomenda-se para utilizar uma unidade lógica dedicada. Utilizar espaços de armazenamento do Windows permite-lhe aumentar também o tamanho do disco lógico e débito IOPS, conforme descrito em capítulo [RAID de Software][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Localização do servidor de conteúdo de SAP
Servidor de conteúdo de SAP tem de ser implementados na mesma região do Azure e Azure VNET onde o sistema SAP é implementado. Está livre para decidir se pretende implementar componentes do servidor de conteúdo de SAP numa VM do Azure dedicado ou na mesma VM que está a executar o sistema SAP. 

![VM do Azure dedicado para o servidor de conteúdo de SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Localização do servidor de Cache SAP
O servidor de Cache do SAP é um componente baseado em servidor adicional para fornecer acesso a documentos (em cache) localmente. O servidor de Cache do SAP coloca em cache os documentos de um servidor de conteúdo do SAP. Isto é otimizar o tráfego de rede se documentos têm de ser recuperadas de mais do que uma vez a partir de localizações diferentes. A regra geral é que o servidor de Cache do SAP tem de ser fisicamente um fecho ao cliente que acede ao servidor de Cache do SAP. 

Aqui tem duas opções:

1. **Cliente é um sistema SAP de back-end** se um sistema SAP de back-end estiver configurado para aceder ao servidor de conteúdo de SAP, esse sistema SAP for um cliente. Como o sistema SAP e o servidor de conteúdo de SAP são implementados na mesma região do Azure, no mesmo datacenter do Azure, estão fisicamente próximo de si. Por conseguinte, é necessário ter um servidor de Cache de SAP dedicado. Os clientes de IU de SAP (SAP GUI ou do web browser) acedam ao sistema SAP diretamente, e o sistema SAP obtém documentos do servidor de conteúdo do SAP.
2. **Cliente é um browser da web no local** o servidor de conteúdo de SAP pode ser configurado para ser acedido diretamente pelo browser. Neste caso, um web browser em execução no local é um cliente o SAP do servidor de conteúdo. Datacenter no local e datacenter do Azure são colocadas em diferentes localizações físicas (Idealmente próximo de si). O Centro de dados no local é ligado ao Azure através de VPN de Site para Site do Azure ou do ExpressRoute. Embora ambas as opções oferecem uma ligação de rede VPN segura para o Azure, a ligação de rede de site a site não oferece um SLA de largura de banda e latência de rede entre o Centro de dados no local e datacenter do Azure. Para acelerar o acesso a documentos, pode efetuar um dos seguintes:
   1. Instalar o servidor de Cache do SAP no local, fechar para o local web browser (opção [isto] [ dbms-guide-900-sap-cache-server-on-premises] figura)
   2. Configure o ExpressRoute do Azure, que oferece uma ligação de rede dedicado de alta velocidade e baixa latência entre datacenter no local e datacenter do Azure.

![Opção de instalar o servidor de Cache do SAP no local][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Cópia de Segurança / Restauro
Se configurar o servidor de conteúdo do SAP para armazenar os ficheiros na base de dados SAP MaxDB, as considerações de desempenho e o procedimento de cópia de segurança/restauro já descritas SAP MaxDB capítulo [cópia de segurança e restaurar] [ dbms-guide-8.4.2] e capítulo [considerações de desempenho para cópia de segurança e restauro][dbms-guide-8.4.3]. 

Se configurar o servidor de conteúdo do SAP para armazenar os ficheiros no sistema de ficheiros, uma opção é executar a cópia de segurança/restauro manual da estrutura de ficheiro onde estão localizados os documentos. Semelhante ao SAP MaxDB cópia de segurança/restauro, é recomendado ter um volume de disco dedicado para o objetivo de cópia de segurança. 

#### <a name="other"></a>Outros
Outras definições específicas do servidor SAP conteúdo são transparentes para as VMs do Azure e são descritas em vários documentos e SAP notas:

* <https://Service.SAP.com/contentserver> 
* A nota [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Informações específicas para IBM DB2 para LUW no Windows
Com o Microsoft Azure, pode migrar facilmente a sua aplicação SAP existente em execução no IBM DB2 para Linux, UNIX e o Windows (LUW) para máquinas virtuais do Azure. Com SAP no IBM DB2 para LUW, os administradores e programadores podem continuar a utilizar o mesmo desenvolvimento e ferramentas de administração, que estão disponíveis no local.
Informações gerais sobre a execução do SAP Business Suite no IBM DB2 para LUW pode ser encontrado na rede de Comunidade do SAP (SCN) em <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Para obter informações adicionais e atualizações sobre SAP no DB2 para LUW no Azure, consulte a nota [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP no IBM DB2 para LUW nos serviços de Máquina Virtual do Microsoft Azure é suportada a partir do DB2 versão 10.5.

Para obter informações sobre produtos SAP suportados e tipos de VM do Azure, consulte a nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para SAP instalações em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS com base nos discos ligados diretamente. Estes discos são montados à VM do Azure e baseiam-se no armazenamento de BLOBS de página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou discos geridos (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer tipo de unidades de rede ou partilhas remotas, como os seguintes serviços de ficheiros do Azure são **não** suportado para ficheiros de base de dados: 

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>

Se estiver a utilizar discos com base no armazenamento de BLOBS de página do Azure ou discos geridos, as instruções efetuadas neste documento no capítulo [estrutura de uma implementação de RDBMS] [ dbms-guide-2] também são aplicáveis a implementações com o IBM DB2 para LUW Base de dados. 

Tal como explicado anteriormente na parte geral do documento, quotas de débito IOPS para discos no existem. As quotas exatas dependem do tipo VM utilizado. Pode encontrar uma lista dos tipos VM com os respetivos quotas [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Desde que a quota atual de IOPS por disco é suficiente, é possível armazenar todos os ficheiros de base de dados num único disco montado. 

Para um desempenho considerações também consulte o capítulo 'Segurança de dados e considerações de desempenho para os diretórios de base de dados' SAP guias de instalação.

Em alternativa, pode utilizar agrupamentos de armazenamento do Windows (apenas disponível no Windows Server 2012 e posterior) ou striping do Windows para o Windows 2008 R2, como descrito em capítulo [RAID de Software] [ dbms-guide-2.2] este documento Crie um dispositivo lógico grande através de vários discos.
Para os discos que contém os caminhos de armazenamento para os diretórios sapdata e saptmp DB2, tem de especificar um tamanho de setor de disco físico de 512 KB. Quando utilizar agrupamentos de armazenamento do Windows, tem de criar os agrupamentos de armazenamento manualmente através do interface de linha de comandos utilizando o parâmetro `-LogicalSectorSizeDefault`. Para obter mais informações, consulte <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Backup/Restauro
A funcionalidade de cópia de segurança/restauro para IBM DB2 para LUW é suportada a mesma forma que em sistemas operativos Windows Server padrão e o Hyper-V.

Tem de se certificar de que tem uma estratégia de cópia de segurança da base de dados válido no local. 

Tal como em implementações bare-metal, o desempenho de cópia de segurança/restauro depende quantos volumes podem ser lidas em paralelo e que o débito desses volumes poderão. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com apenas oito threads de CPU. Por conseguinte, um pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, o menor o débito global ler
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança
* Os destinos menos (Stripe diretórios, discos) para escrever a cópia de segurança, menor o débito

Para aumentar o número de destinos ao escrever no, duas opções podem ser utilizadas/combinado consoante as suas necessidades:

* Striping o volume de destino de cópia de segurança ao longo de vários discos para melhorar o débito IOPS nesse volume repartidos
* Utilizar mais do que um diretório de destino ao escrever a cópia de segurança

#### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre
Microsoft Cluster Server (MSCS) não é suportada.

Recuperação de desastres do DB2 elevada disponibilidade (HADR) é suportada. Se as máquinas virtuais da configuração HA tiver trabalhar resolução do nome, o programa de configuração do Azure não diferem das qualquer configuração que é efetuada no local. Não se recomenda a depender de resolução IP apenas.

Não utilize a Georreplicação para as contas de armazenamento que armazenam os discos de base de dados. Para obter mais informações, consulte o capítulo [armazenamento do Microsoft Azure] [ dbms-guide-2.3] e capítulo [elevada disponibilidade e recuperação após desastre com as VMs do Azure] [ dbms-guide-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como conjuntos de disponibilidade do Azure ou SAP monitorização aplicam-se conforme descrito nos três primeiros capítulos deste documento para implementações de VMs com IBM DB2 para LUW bem. 

Consulte também capítulo [geral SQL Server para SAP no resumo de Azure][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Informações específicas para IBM DB2 para LUW no Linux
Com o Microsoft Azure, pode migrar facilmente a sua aplicação SAP existente em execução no IBM DB2 para Linux, UNIX e o Windows (LUW) para máquinas virtuais do Azure. Com SAP no IBM DB2 para LUW, os administradores e programadores podem continuar a utilizar o mesmo desenvolvimento e ferramentas de administração, que estão disponíveis no local. Informações gerais sobre a execução do SAP Business Suite no IBM DB2 para LUW pode ser encontrado na rede de Comunidade do SAP (SCN) em <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Para obter informações adicionais e atualizações sobre SAP no DB2 para LUW no Azure, consulte a nota [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP no IBM DB2 para LUW nos serviços de Máquina Virtual do Microsoft Azure é suportada a partir do DB2 versão 10.5.

Para obter informações sobre produtos SAP suportados e tipos de VM do Azure, consulte a nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para SAP instalações em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os ficheiros de base de dados devem ser armazenados num sistema de ficheiros com base nos discos ligados diretamente. Estes discos são montados à VM do Azure e baseiam-se no armazenamento de BLOBS de página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou discos geridos (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer tipo de unidades de rede ou partilhas remotas, como os seguintes serviços de ficheiros do Azure são **não** suportado para ficheiros de base de dados:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>

Se estiver a utilizar discos com base no armazenamento de BLOBS de página do Azure, as instruções efetuadas neste documento no capítulo [estrutura de uma implementação de RDBMS] [ dbms-guide-2] também são aplicáveis a implementações com o IBM DB2 para LUW base de dados.

Tal como explicado anteriormente na parte geral do documento, quotas de débito IOPS para discos no existem. As quotas exatas dependem do tipo VM utilizado. Pode encontrar uma lista dos tipos VM com os respetivos quotas [aqui (Linux)] [ virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Desde que a quota atual de IOPS por disco é suficiente, é possível armazenar todos os ficheiros de base de dados num único disco.

Para um desempenho considerações também consulte o capítulo 'Segurança de dados e considerações de desempenho para os diretórios de base de dados' SAP guias de instalação.

Em alternativa, pode utilizar o LVM (Gestor de Volume lógica) ou MDADM conforme descrito em capítulo [RAID de Software] [ dbms-guide-2.2] deste documento para criar um dispositivo lógico grande através de vários discos.
Para os discos que contém os caminhos de armazenamento para os diretórios sapdata e saptmp DB2, tem de especificar um tamanho de setor de disco físico de 512 KB.

#### <a name="backuprestore"></a>Backup/Restauro
A funcionalidade de cópia de segurança/restauro para IBM DB2 para LUW é suportada a mesma forma que em padrão Linux instalação no local.

Tem de se certificar de que tem uma estratégia de cópia de segurança da base de dados válido no local.

Tal como em implementações bare-metal, o desempenho de cópia de segurança/restauro depende quantos volumes podem ser lidas em paralelo e que o débito desses volumes poderão. Além disso, o consumo de CPU utilizado pela cópia de segurança compressão pode reproduzir uma função significativa em VMs com apenas oito threads de CPU. Por conseguinte, um pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, o menor o débito global ler
* O menor que número de CPU threads na VM, mais grave o impacto da compressão de cópias de segurança
* Os destinos menos (Stripe diretórios, discos) para escrever a cópia de segurança, menor o débito

Para aumentar o número de destinos ao escrever no, duas opções podem ser utilizadas/combinado consoante as suas necessidades:

* Striping o volume de destino de cópia de segurança ao longo de vários discos para melhorar o débito IOPS nesse volume repartidos
* Utilizar mais do que um diretório de destino ao escrever a cópia de segurança

#### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre
Recuperação de desastres do DB2 elevada disponibilidade (HADR) é suportada. Se as máquinas virtuais da configuração HA tiver trabalhar resolução do nome, o programa de configuração do Azure não diferem das qualquer configuração que é efetuada no local. Não se recomenda a depender de resolução IP apenas.

Não utilize a Georreplicação para as contas de armazenamento que armazenam os discos de base de dados. Para obter mais informações, consulte o capítulo [armazenamento do Microsoft Azure] [ dbms-guide-2.3] e capítulo [elevada disponibilidade e recuperação após desastre com as VMs do Azure] [ dbms-guide-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como conjuntos de disponibilidade do Azure ou SAP monitorização aplicam-se conforme descrito nos três primeiros capítulos deste documento para implementações de VMs com IBM DB2 para LUW bem.

Consulte também capítulo [geral SQL Server para SAP no resumo de Azure][dbms-guide-5.8].

