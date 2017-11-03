---
title: "Implementação de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs"
description: Saiba como implementar o software de SAP computadores virtuais Linux no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.openlocfilehash: 4c06b1c8265a12af6764124e3c753e9456a2be20
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implementação de máquinas virtuais do Azure para SAP NetWeaver
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS deployment for SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS deployment)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High availability and disaster recovery with Azure VMs)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server image from the Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure summary)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and restore)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance considerations for backup and restore)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other)
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

[deployment-guide]:deployment-guide.md (Azure Virtual Machines deployment for SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP resources)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Deploying a VM by using a custom image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Deploying a VM from the Azure Marketplace for SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Deploying a VM with a custom image for SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Moving a VM from on-premises using a non-generalized Azure VHD with SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Deployment scenarios of VMs for SAP on Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Deploying Azure PowerShell cmdlets)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download and import SAP-relevant PowerShell cmdlets)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Join a VM to an on-premises domain - Windows only)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, install, and enable the Azure VM Agent)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure the Azure Enhanced Monitoring Extension for SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Readiness check for Azure Enhanced Monitoring for SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for the Azure monitoring infrastructure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Troubleshooting Azure monitoring for SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure monitoring)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure the proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and troubleshooting for setting up end-to-end monitoring)

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

[planning-guide]:planning-guide.md (Azure Virtual Machines planning and implementation for SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (High availability and disaster recovery for SAP NetWeaver running on Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (High availability for SAP Application Servers)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Using Autostart for SAP instances)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-only - Virtual Machine deployments in Azure without dependencies on the on-premises customer network)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Deployment of single or multiple SAP VMs in Azure fully integrated with the on-premises network)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regions)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fault domains)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade domains)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure availability sets)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure virtual machines concept)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Deploying a VM with a customer specific image)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparation for moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparation for deploying a VM with a customer specific image for SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparing VMs with SAP for Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Difference between an Azure disk and an Azure image)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Uploading a VHD from on-premises to Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copying disks between Azure Storage accounts)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD structure for SAP deployments)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Setting automount for attached disks)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Single VM with SAP NetWeaver demo/training scenario)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts of Cloud-Only deployment of SAP instances)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Managed Disks)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and data disks)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Manage virtual machines by using Azure Resource Manager and PowerShell)
[virtual-machines-windows-agent-user-guide]:../../windows/agent-user-guide.md
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
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

Máquinas virtuais do Azure é a solução para organizações que precisam de recursos de computação e armazenamento no tempo mínimo e sem ciclos de aprovisionamento demorado. Pode utilizar máquinas virtuais do Azure para implementar aplicações classical, como aplicações baseadas na SAP NetWeaver, no Azure. Expanda uma aplicação a fiabilidade e disponibilidade sem recursos no local adicionais. Máquinas virtuais do Azure suporta uma conectividade entre instalações, pelo que pode integrar Virtual Machines do Azure domínios no local, nuvens privadas e horizontal do sistema SAP da sua organização.

Neste artigo, vamos abordar os passos para implementar aplicações SAP em máquinas virtuais (VMs) no Azure, incluindo as opções de implementação alternativos e resolução de problemas. Este artigo baseia-se nas informações do [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]. Também complementa a documentação de instalação do SAP e notas de SAP, que são os recursos primários para a instalação e implementação de software do SAP.

## <a name="prerequisites"></a>Pré-requisitos
Configurar uma máquina virtual do Azure para a implementação de software do SAP envolve vários passos e recursos. Antes de começar, certifique-se de que cumpre os pré-requisitos para instalar o software SAP em máquinas virtuais no Azure.

### <a name="local-computer"></a>Computador local
Para gerir o Windows ou Linux VMs, pode utilizar um script do PowerShell e o portal do Azure. Para ambas as ferramentas, precisa de um computador local com o Windows 7 ou uma versão posterior do Windows. Se pretender gerir apenas as VMs do Linux e pretender utilizar um computador com Linux para esta tarefa, pode utilizar a CLI do Azure.

### <a name="internet-connection"></a>Ligação à Internet
Para transferir e executar as ferramentas e scripts que são necessárias para a implementação de software do SAP, tem de estar ligado à Internet. VM do Azure que está a executar o Azure avançada extensão de monitorização para SAP também tem acesso à Internet. Se a VM do Azure faz parte de uma rede virtual do Azure ou o domínio no local, certifique-se de que as definições de proxy relevantes estão definidas, conforme descrito em [configurar o proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure
Precisa de uma conta do Azure Active Directory.

### <a name="topology-and-networking"></a>Topologia e de rede
Tem de definir a topologia e a arquitetura da implementação do SAP no Azure:

* Contas de armazenamento do Azure para ser utilizado
* Rede virtual onde pretende implementar o sistema SAP
* Grupo de recursos para o qual pretende implementar o sistema SAP
* Região do Azure onde pretende implementar o sistema SAP
* Configuração de SAP (camada de dois ou três camadas)
* O número de discos de dados adicionais para ser montado para as VMs e tamanhos VM
* Configuração de correção de SAP e sistema de transporte (CTS)

Criar e configurar contas do storage do Azure (se necessário) e as redes virtuais do Azure antes de começar o processo de implementação de software do SAP. Para obter informações sobre como criar e configurar estes recursos, consulte [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Dimensionamento de SAP
Conhece as seguintes informações, para dimensionamento SAP:

* Carga de trabalho SAP prevista, por exemplo, utilizando a ferramenta de Sizer rápida SAP e o SAP aplicação desempenho Standard (SAPS) número
* Necessário consumo da CPU de recursos e de memória do sistema SAP
* Operações necessárias de (e/s) de entrada/saída por segundo
* Necessária largura de banda de rede de comunicação eventual entre VMs no Azure
* Largura de banda de rede necessária entre recursos no local e o sistema SAP implementado o Azure

### <a name="resource-groups"></a>Grupos de recursos
No Gestor de recursos do Azure, pode utilizar grupos de recursos para gerir todos os recursos de aplicação na sua subscrição do Azure. Para obter mais informações, consulte [descrição geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP
Quando estiver a configurar a implementação de software do SAP, terá dos seguintes recursos SAP:

* A nota [1928533], que tem:
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de software do SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão necessária de kernel SAP para o Windows e Linux no Microsoft Azure

* A nota [2015553] apresenta uma lista de pré-requisitos suportado de SAP SAP para implementações de software no Azure.
* A nota [2178632] tem informações detalhadas sobre todas as monitorizações métricas que relatados para SAP no Azure.
* A nota [1409604] tem a versão necessária do agente de anfitrião do SAP para o Windows no Azure.
* A nota [2191498] tem a versão necessária do agente de anfitrião do SAP para Linux no Azure.
* A nota [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota [2002167] tem informações gerais sobre Red Hat Enterprise Linux 7. x.
* A nota [2069760] tem informações gerais sobre Oracle Linux 7. x.
* A nota [1999351] tem informações adicionais de resolução de problemas para o Azure avançada extensão de monitorização para SAP.
* A nota [1597355] tem informações gerais sobre o espaço de comutação de Linux.
* [SAP na página do Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tem notícias e uma coleção de recursos úteis.
* [WIKI do SAP Comunidade](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP notas para Linux.
* Cmdlets do PowerShell de SAP específicos que fazem parte do [Azure PowerShell][azure-ps].
* Comandos de CLI do Azure de SAP específicos que fazem parte do [CLI do Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do Windows
Estes artigos Microsoft abrangem implementações SAP no Azure:

* [Azure máquinas virtuais de planeamento e implementação de SAP NetWeaver][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP NetWeaver (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implementação de software SAP em VMs do Azure
Tem várias opções para implementar as VMs e discos associados no Azure. É importante compreender as diferenças entre as opções de implementação, porque poderá demorar vários passos para preparar as suas VMs para a implementação com base no tipo de implementação que escolher.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implementar uma VM do Azure Marketplace para SAP
Pode utilizar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implementar a VM. O Marketplace oferece algumas imagens de SO padrão do Windows Server e diversas distribuições de Linux. Também pode implementar uma imagem que inclui a gestão de base de dados SKUs de sistema (DBMS), por exemplo, o Microsoft SQL Server. Para obter mais informações sobre a utilização de imagens com DBMS SKUs, consulte [implementação DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide].

O fluxograma a seguir mostra a sequência de SAP específicos de passos para implementar uma VM do Azure Marketplace:

![Fluxograma de fase de implementação da VM para sistemas SAP através da utilização de uma imagem de VM do Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal do Azure
É a forma mais fácil para criar uma nova máquina virtual com uma imagem do Azure Marketplace utilizando o portal do Azure.

1.  Aceda a <https://portal.azure.com/#create/hub>.  Ou, no menu do portal do Azure, selecione **+ novo**.
2.  Selecione **computação**e, em seguida, selecione o tipo de sistema operativo que pretende implementar. Por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2. A vista de lista predefinida não mostra que todos os sistemas operativos suportados. Selecione **ver todos os** para uma lista completa. Para mais informações sobre sistemas operativos suportados para a implementação de software do SAP, consulte a nota SAP [1928533].
3.  Na página seguinte, reveja os termos e condições.
4.  No **selecionar um modelo de implementação** caixa, selecione **Resource Manager**.
5.  Selecione **Criar**.

O assistente orienta-o definir os parâmetros necessários para criar a máquina virtual, para além de todos os recursos necessários, como as interfaces de rede e a conta de armazenamento. Algumas destes parâmetros são:

1. **Noções básicas**:
 * **Nome**: O nome do recurso (nome da máquina virtual).
 * **Tipo de disco da VM**: selecione o tipo de disco do disco de SO. Se pretender utilizar o armazenamento Premium para os discos de dados, é recomendável utilizar o Premium Storage para o disco de SO bem.
 * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: introduza o nome de utilizador e palavra-passe do utilizador que é criado durante o aprovisionamento. Para uma máquina virtual do Linux, pode introduzir a chave de Secure Shell (SSH) pública que utiliza para iniciar sessão máquina.
 * **Subscrição**: selecione a subscrição que pretende utilizar para aprovisionar a nova máquina virtual.
 * **Grupo de recursos**: O nome do grupo de recursos para a VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
 * **Localização**: onde pretende implementar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para obter mais informações, consulte [redes do Microsoft Azure] [ planning-guide-microsoft-azure-networking] no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide].
2. **Tamanho**:

     Para obter uma lista de tipos VM suportados, consulte a nota SAP [1928533]. Lembre-se de que seleciona o tipo correto da VM se pretender utilizar o Premium Storage do Azure. Nem todos os tipos VM suportam o Premium Storage. Para obter mais informações, consulte [Storage: armazenamento do Microsoft Azure e discos de dados] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Premium Storage do Azure] [ planning-guide-azure-premium-storage] no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide].

3. **Definições**:
  * **Armazenamento**
    * **Tipo de disco**: selecione o tipo de disco do disco de SO. Se pretender utilizar o armazenamento Premium para os discos de dados, é recomendável utilizar o Premium Storage para o disco de SO bem.
    * **Utilizar discos geridos**: Se pretender utilizar discos geridos, selecione Sim. Para obter mais informações sobre discos geridos, consulte o capítulo [discos geridos] [ planning-guide-managed-disks] no guia de planeamento.
    * **Conta de armazenamento**: selecione uma conta de armazenamento existente ou crie um novo. Nem todos os tipos de armazenamento funcionam para executar aplicações SAP. Para obter mais informações sobre os tipos de armazenamento, consulte [armazenamento do Microsoft Azure] [ dbms-guide-2.3] no [implementação DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide].
  * **Rede**
    * **Rede virtual** e **sub-rede**: para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
    * **Endereço IP público**: selecione o endereço IP público que pretende utilizar, ou introduza os parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também criar um grupo de segurança de rede para ajudar a proteger o acesso à máquina virtual.
    * **Grupo de segurança de rede**: para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
  * **Extensões**: pode instalar as extensões de máquina virtual adicionando-os para a implementação. Não é necessário adicionar extensões neste passo. As extensões necessárias para o suporte SAP estão instaladas mais tarde. Consulte o capítulo [configurar o Azure avançada extensão de monitorização para SAP] [ deployment-guide-4.5] neste guia.
  * **Elevada disponibilidade**: selecione um conjunto de disponibilidade, ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
  * **Monitorização**
    * **Diagnóstico de arranque**: pode selecionar **desativar** para diagnóstico de arranque.
    * **Diagnóstico de SO convidado**: pode selecionar **desativar** para monitorização do diagnóstico.

4. **Resumo**:

  Reveja as suas seleções e, em seguida, selecione **OK**.

A máquina virtual é implementada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual utilizando um modelo
Pode criar uma máquina virtual utilizando um dos modelos publicados no SAP a [repositório do GitHub modelos do início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual utilizando o [portal do Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], ou [CLI do Azure][virtual-machines-linux-tutorial].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-camada--imagem do marketplace)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - geridos discos** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e discos geridos, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-camada--imagem do marketplace)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas com várias máquinas virtuais, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - geridos discos** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três camadas com várias máquinas virtuais e discos geridos, utilize este modelo.

No portal do Azure, introduza os parâmetros seguintes para o modelo:

1. **Noções básicas**:
  * **Subscrição**: A subscrição a utilizar para implementar o modelo.
  * **Grupo de recursos**: O grupo de recurso a utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou pode selecionar um grupo de recursos existente na subscrição.
  * **Localização**: onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos é utilizada.

2. **Definições**:
  * **ID de sistema do SAP**: O ID de sistema do SAP (SID).
  * **Tipo de SO**: O sistema operativo que pretende implementar, por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2.

    A vista de lista não mostra que todos os sistemas operativos suportados. Para mais informações sobre sistemas operativos suportados para a implementação de software do SAP, consulte a nota SAP [1928533].
  * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

    O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistema.
  * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

    Selecione **HA** para uma configuração que é adequada para uma instalação de elevada disponibilidade. São criados dois servidores de base de dados e dois servidores para ABAP SAP Central serviços (ASCS).
  * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

    Para sistemas maiores, recomendamos vivamente a utilizar o Premium Storage do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte estes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Armazenamento do Microsoft Azure] [ dbms-guide-2.3] no [implementação DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]
      * [Armazenamento Premium: Armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
  * **Nome de utilizador de Admin** e **palavra-passe de administrador**: um nome de utilizador e palavra-passe.
    É criado um novo utilizador, para o início de sessão na máquina virtual.
  * **Novo ou existente sub-rede**: determina se são criados um nova rede virtual e uma sub-rede ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
  * **ID de sub-rede**: O ID da sub-rede as máquinas virtuais serão ligados. Selecione a sub-rede da sua rede privada virtual (VPN) ou a rede virtual do Azure ExpressRoute para ligar a máquina virtual à sua rede no local. O ID, normalmente, este aspeto: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}&lt;id de subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome de sub-rede >

3. **Termos e condições**:  
    Reveja e aceite os termos legais.

4.  Selecione **Compra**.

O agente da VM do Azure é implementado por predefinição, quando utilizar uma imagem do Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy
Dependendo de como a sua rede no local é configurado, poderá ter de configurar o proxy na VM. Se a VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não conseguirá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)
Se a implementação do Azure está ligada a uma instância DNS ou do Active Directory no local através de uma ligação de VPN de site para site Azure ou ExpressRoute (esta opção é denominada *em vários locais* no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]), é esperado que a VM está a associar a um domínio no local. Para obter mais informações sobre as considerações para esta tarefa, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar a monitorização
Para garantir que SAP suporta o seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização de SAP e mínimo as versões necessárias do SAP Kernel e o agente de anfitrião do SAP, nos recursos listados em [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitorização
Verificar se monitorização está a funcionar, conforme descrito em [verificações e a resolução de problemas para configurar a monitorização ponto-a-ponto][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passos de pós-implementação
Depois de criar a VM e a implementação da VM, tem de instalar os componentes de software necessárias na VM. Devido a sequência de instalação de software/implementação neste tipo de implementação da VM, o software seja instalado já deve estar disponível no Azure, na outra VM, ou como um disco que pode ser anexado. Em alternativa, considere a utilização de um cenário entre locais, na qual conectividade para o local é dado ativos (partilhas de instalação).

Depois de implementar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP na sua VM, tal como faria num ambiente no local. Para instalar o software SAP numa VM do Azure, SAP e a Microsoft recomenda que carregar e armazenar o suporte de dados de instalação do SAP no Azure VHDs ou discos geridos ou que crie uma VM do Azure que funciona como um servidor de ficheiros que tem todas as necessário SAP suporte de instalação do.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implementar uma VM com uma imagem personalizada para SAP
Porque diferentes versões de um sistema operativo ou o DBMS têm requisitos de patch diferente, as imagens que encontrar no Azure Marketplace poderão não satisfazer as suas necessidades. Em vez disso, poderá querer criar uma VM utilizando a sua própria imagem de SO/DBMS VM, que pode implementar novamente mais tarde.
Pode utilizar vários passos para criar uma imagem privada para Linux para criar um para o Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode utilizar para implementar várias máquinas virtuais, as definições do Windows (por exemplo, o SID do Windows e o nome de anfitrião) tem de ser abstracted ou generalizadas na VM no local. Pode utilizar [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) para efetuar este procedimento.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem de Linux que pode utilizar para implementar várias máquinas virtuais, algumas definições de Linux tem de ser abstracted ou generalizadas na VM no local. Pode utilizar `waagent -deprovision` para efetuar este procedimento. Para obter mais informações, consulte [capturar uma máquina virtual do Linux em execução no Azure] [ virtual-machines-linux-capture-image] e [guia de utilizador do agente Linux do Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Pode preparar e criar uma imagem personalizada e, em seguida, utilizá-la para criar várias VMs novas. Isto é descrito no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]. Configurar o conteúdo da base de dados utilizando o Gestor de aprovisionamento de Software em SAP instalar um novo sistema SAP (restaura uma cópia de segurança da base de dados a partir de um disco que está ligado à máquina virtual) ou diretamente restaurando uma cópia de segurança da base de dados do storage do Azure, se o DBMS suportar. Para obter mais informações, consulte [implementação DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]. Se já tiver instalado um sistema SAP na sua VM no local (especialmente para sistemas de duas camadas), pode adaptar as definições do sistema SAP após a implementação da VM do Azure, utilizando o procedimento de mudar o nome do sistema de mensagens em fila suportado pelo Gestor de aprovisionamento de Software para SAP (nota SAP [1619720]). Caso contrário, pode instalar o software SAP depois de implementar a VM do Azure.

O fluxograma a seguir mostra a sequência de SAP específicos de passos para implementar uma VM a partir de uma imagem personalizada:

![Fluxograma de fase de implementação da VM para sistemas SAP através da utilização de uma imagem de VM no Marketplace privada][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal do Azure
É a forma mais fácil para criar uma nova máquina virtual a partir de uma imagem de disco gerido através do portal do Azure. Para obter mais informações sobre como criar uma imagem de disco gerir, leia o artigo [capturar uma imagem gerida de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Aceda a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu do portal do Azure, selecione **imagens**.
2.  Selecione a imagem de disco gerido que pretende implementar e clique em **criar VM**

O assistente orienta-o definir os parâmetros necessários para criar a máquina virtual, para além de todos os recursos necessários, como as interfaces de rede e a conta de armazenamento. Algumas destes parâmetros são:

1. **Noções básicas**:
 * **Nome**: O nome do recurso (nome da máquina virtual).
 * **Tipo de disco da VM**: selecione o tipo de disco do disco de SO. Se pretender utilizar o armazenamento Premium para os discos de dados, é recomendável utilizar o Premium Storage para o disco de SO bem.
 * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: introduza o nome de utilizador e palavra-passe do utilizador que é criado durante o aprovisionamento. Para uma máquina virtual do Linux, pode introduzir a chave de Secure Shell (SSH) pública que utiliza para iniciar sessão máquina.
 * **Subscrição**: selecione a subscrição que pretende utilizar para aprovisionar a nova máquina virtual.
 * **Grupo de recursos**: O nome do grupo de recursos para a VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
 * **Localização**: onde pretende implementar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para obter mais informações, consulte [redes do Microsoft Azure] [ planning-guide-microsoft-azure-networking] no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide].
2. **Tamanho**:

     Para obter uma lista de tipos VM suportados, consulte a nota SAP [1928533]. Lembre-se de que seleciona o tipo correto da VM se pretender utilizar o Premium Storage do Azure. Nem todos os tipos VM suportam o Premium Storage. Para obter mais informações, consulte [Storage: armazenamento do Microsoft Azure e discos de dados] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Premium Storage do Azure] [ planning-guide-azure-premium-storage] no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide].

3. **Definições**:
  * **Armazenamento**
    * **Tipo de disco**: selecione o tipo de disco do disco de SO. Se pretender utilizar o armazenamento Premium para os discos de dados, é recomendável utilizar o Premium Storage para o disco de SO bem.
    * **Utilizar discos geridos**: Se pretender utilizar discos geridos, selecione Sim. Para obter mais informações sobre discos geridos, consulte o capítulo [discos geridos] [ planning-guide-managed-disks] no guia de planeamento.
  * **Rede**
    * **Rede virtual** e **sub-rede**: para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
    * **Endereço IP público**: selecione o endereço IP público que pretende utilizar, ou introduza os parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também criar um grupo de segurança de rede para ajudar a proteger o acesso à máquina virtual.
    * **Grupo de segurança de rede**: para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
  * **Extensões**: pode instalar as extensões de máquina virtual adicionando-os para a implementação. Não é necessário adicionar a extensão neste passo. As extensões necessárias para o suporte SAP estão instaladas mais tarde. Consulte o capítulo [configurar o Azure avançada extensão de monitorização para SAP] [ deployment-guide-4.5] neste guia.
  * **Elevada disponibilidade**: selecione um conjunto de disponibilidade, ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
  * **Monitorização**
    * **Diagnóstico de arranque**: pode selecionar **desativar** para diagnóstico de arranque.
    * **Diagnóstico de SO convidado**: pode selecionar **desativar** para monitorização do diagnóstico.

4. **Resumo**:

  Reveja as suas seleções e, em seguida, selecione **OK**.

A máquina virtual é implementada no grupo de recursos selecionado.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual utilizando um modelo
Para criar uma implementação através da utilização de uma imagem do SO privada do portal do Azure, utilize um dos seguintes modelos SAP. Estes modelos são publicados no [repositório do GitHub modelos do início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual, utilizando [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-camada-utilizador-imagem)][sap-templates-2-tier-user-image]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - imagem de disco gerido** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e uma imagem de disco gerida, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-camada-utilizador-imagem)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas com várias máquinas virtuais ou a seus próprios imagem do SO, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - imagem de disco gerido** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas com várias máquinas virtuais ou a seus próprios imagem do SO e uma imagem de disco gerida, utilize este modelo.

No portal do Azure, introduza os parâmetros seguintes para o modelo:

1. **Noções básicas**:
  * **Subscrição**: A subscrição a utilizar para implementar o modelo.
  * **Grupo de recursos**: O grupo de recurso a utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecione um grupo de recursos existente na subscrição.
  * **Localização**: onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
2. **Definições**:
  * **ID de sistema do SAP**: O ID de sistema SAP.
  * **Tipo de SO**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
  * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

    O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistema.
  * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

    Selecione **HA** para uma configuração que é adequada para uma instalação de elevada disponibilidade. São criados dois servidores de base de dados e dois servidores para ASCS.
  * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

    Para sistemas maiores, recomendamos vivamente a utilizar o Premium Storage do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Armazenamento do Microsoft Azure] [ dbms-guide-2.3] no [implementação DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]
      * [Armazenamento Premium: Armazenamento de elevado desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
  * **Imagem do utilizador URI de VHD** (modelo de imagem não gerido disco apenas): O URI do SO privado imagem VHD, por exemplo, https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Conta de armazenamento de imagem de utilizador** (modelo de imagem não gerido disco apenas): O nome da conta do storage onde está armazenada a imagem de SO privada, por exemplo, &lt;accountname > no https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (modelo de imagem gerido disco apenas): Id da imagem de disco gerido que pretende utilizar
  * **Nome de utilizador de Admin** e **palavra-passe de administrador**: O nome de utilizador e palavra-passe.

    É criado um novo utilizador, para o início de sessão na máquina virtual.
  * **Novo ou existente sub-rede**: determina se é criada uma nova rede virtual e uma sub-rede ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
  * **ID de sub-rede**: O ID de sub-rede para o qual as máquinas virtuais irão ligar à. Selecione a sub-rede da rede virtual VPN ou ExpressRoute para ligar a máquina virtual à sua rede no local. O ID, normalmente, este aspeto:

    /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}&lt;id de subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome de sub-rede >

3. **Termos e condições**:  
    Reveja e aceite os termos legais.

4.  Selecione **Compra**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalar o agente da VM (apenas Linux)
Para utilizar os modelos descritos na secção anterior, o agente Linux tem de estar instalado na imagem do utilizador ou a implementação irá falhar. Transfira e instale o agente da VM na imagem do utilizador conforme descrito em [transferir, instalar e ativar o agente da VM do Azure][deployment-guide-4.4]. Se não utilizar os modelos, também pode instalar o agente da VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)
Se a implementação do Azure está ligada a uma instância DNS ou do Active Directory no local através de uma ligação de VPN de site para site Azure ou Azure ExpressRoute (esta opção é denominada *em vários locais* no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]), é esperado que a VM está a associar a um domínio no local. Para obter mais informações sobre as considerações para este passo, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy
Dependendo de como a sua rede no local é configurado, poderá ter de configurar o proxy na VM. Se a VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não conseguirá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização
Para garantir que SAP suporta o seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização de SAP e mínimo as versões necessárias do SAP Kernel e o agente de anfitrião do SAP, nos recursos listados em [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitorização
Verificar se monitorização está a funcionar, conforme descrito em [verificações e a resolução de problemas para configurar a monitorização ponto-a-ponto][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Mover uma VM no local através da utilização de um VHD de Azure não generalizado com o SAP
Neste cenário, deve planear mover um sistema específico do SAP a partir de um ambiente no local para o Azure. Pode fazê-lo ao carregar o VHD que tenha o sistema operativo, os binários SAP e, eventualmente, os binários DBMS plus os VHDs com os ficheiros de dados e de registo do DBMS, para o Azure. Ao contrário o cenário descrito [cenário 2: implementar uma VM com uma imagem personalizada para SAP][deployment-guide-3.3], neste caso, mantenha o nome de anfitrião, o SID de SAP, e as contas de utilizador SAP na VM do Azure, porque foram configurados no ambiente no local. Não é necessário generalizar o sistema operativo. Este cenário aplica-se com mais frequência para cenários de vários locais onde parte horizontal o SAP é executado no local e parte do mesmo é executado no Azure.

Neste cenário, o agente VM é **não** automaticamente instalado durante a implementação. Porque o agente da VM e o Azure avançada extensão de monitorização para SAP são necessárias para executar o SAP NetWeaver no Azure, terá de transferir, instalar e ativar manualmente ambos os componentes depois de criar a máquina virtual.

Para obter mais informações sobre o agente da VM do Azure, consulte os seguintes recursos.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Descrição geral do agente da Máquina Virtual do Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Guia de utilizador do agente Linux do Azure][virtual-machines-linux-agent-user-guide]
>
>

- - -

O fluxograma a seguir mostra a sequência de passos para mover uma VM no local através da utilização de um VHD de Azure não generalizado:

![Fluxograma de fase de implementação da VM para sistemas SAP através da utilização de um disco VM][deployment-guide-figure-400]

Se o disco já está carregado e definido no Azure (consulte [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]), efetue as tarefas descritas as próximas secções alguns.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para criar uma implementação utilizando um disco de SO privado através do portal do Azure, utilize o modelo SAP publicado no [repositório do GitHub modelos do início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual, utilizando o PowerShell.

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap 2-camada-utilizador-disco)][sap-templates-2-tier-os-disk]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - disco geridos** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e um disco gerida, utilize este modelo.

No portal do Azure, introduza os parâmetros seguintes para o modelo:

1. **Noções básicas**:
  * **Subscrição**: A subscrição a utilizar para implementar o modelo.
  * **Grupo de recursos**: O grupo de recurso a utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecione um grupo de recursos existente na subscrição.
  * **Localização**: onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
2. **Definições**:
  * **ID de sistema do SAP**: O ID de sistema SAP.
  * **Tipo de SO**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
  * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

    O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistema.
  * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

    Para sistemas maiores, recomendamos vivamente a utilizar o Premium Storage do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Armazenamento do Microsoft Azure] [ dbms-guide-2.3] no [implementação DBMS de Máquina Virtual do Azure para SAP NetWeaver][dbms-guide]
      * [Armazenamento Premium: Armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
  * **URI do VHD do disco de SO** (apenas modelo de disco não gerido): O URI do disco de SO privado, por exemplo, https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Id de disco gerido de disco do SO** (apenas modelo de disco gerido): O Id de disco de SO de disco gerida, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Novo ou existente sub-rede**: determina se são criados um nova rede virtual e uma sub-rede ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
  * **ID de sub-rede**: O ID de sub-rede para o qual as máquinas virtuais irão ligar à. Selecione a sub-rede da rede virtual VPN ou Azure ExpressRoute para ligar a máquina virtual à sua rede no local. O ID, normalmente, este aspeto:

    /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}&lt;id de subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome de sub-rede >

3. **Termos e condições**:  
    Reveja e aceite os termos legais.

4.  Selecione **Compra**.

#### <a name="install-the-vm-agent"></a>Instalar o agente da VM
Para utilizar os modelos descritos na secção anterior, o agente da VM tem de estar instalado no disco do SO ou a implementação irá falhar. Transfira e instale o agente da VM na VM, conforme descrito em [transferir, instalar e ativar o agente da VM do Azure][deployment-guide-4.4].

Se não utilizar os modelos descritos na secção anterior, também pode instalar o agente da VM posteriormente.

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)
Se a implementação do Azure está ligada a uma instância DNS ou do Active Directory no local através de uma ligação de VPN de site para site Azure ou ExpressRoute (esta opção é denominada *em vários locais* no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide]), é esperado que a VM está a associar a um domínio no local. Para obter mais informações sobre as considerações para esta tarefa, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy
Dependendo de como a sua rede no local é configurado, poderá ter de configurar o proxy na VM. Se a VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não conseguirá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização
Para garantir que SAP suporta o seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização de SAP e mínimo as versões necessárias do SAP Kernel e o agente de anfitrião do SAP, nos recursos listados em [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitorização
Verificar se monitorização está a funcionar, conforme descrito em [verificações e a resolução de problemas para configurar a monitorização ponto-a-ponto][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Atualizar a configuração de monitorização para SAP
Atualize a configuração de monitorização de SAP em qualquer um dos seguintes cenários:
* A equipa da Microsoft/SAP conjunta expande as capacidades de monitorização e os pedidos de contadores mais ou menos.
* Microsoft disponibiliza uma nova versão da infraestrutura do Azure subjacente que fornece os dados de monitorização e o Azure avançada extensão de monitorização para SAP tem de ser adaptada a essas alterações descritos.
* Montar discos de dados adicionais para a VM do Azure ou remover um disco de dados. Neste cenário, atualize a coleção de dados relacionados com o armazenamento. Alterar a configuração, adicionando ou eliminando pontos finais ou através da atribuição de endereços IP para uma VM não afeta a configuração de monitorização.
* Altere o tamanho da VM do Azure, por exemplo, do tamanho A5 para quaisquer outro tamanho da VM.
* Adicionar novo interfaces de rede para a VM do Azure.

Para atualizar as definições de monitorização, atualizar a infraestrutura de monitorização, seguindo os passos no [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas de implementação de software do SAP
Esta secção descreve em pormenor os passos para efetuar tarefas específicas no processo de configuração e implementação.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar os cmdlets do PowerShell do Azure
1.  Aceda a [transfere do Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  Em **ferramentas da linha de comandos**, em **PowerShell**, selecione **Windows instalar**.
3.  Na caixa de diálogo Gestor de transferência da Microsoft, para o ficheiro transferido (por exemplo, WindowsAzurePowershellGet.3f.3f.3fnew.exe), selecione **executar**.
4.  Para executar o instalador de plataforma Web da Microsoft (Microsoft Web PI), selecione **Sim**.
5.  Uma página que parece que este é apresentado:

  ![Página de instalação para os cmdlets do PowerShell do Azure][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Selecione **instalar**e, em seguida, aceite os termos de licenciamento de Software Microsoft.
7.  O PowerShell está instalado. Selecione **concluir** para fechar o Assistente de instalação.

Verifique frequentemente para atualizações para os cmdlets do PowerShell, que normalmente são atualizados mensalmente. A forma mais fácil para procurar atualizações para se efetuar os passos de instalação anterior, até a página de instalação mostrado no passo 5. O número de data e a versão de lançamento dos cmdlets está incluído na página mostrada no passo 5. Salvo indicação em contrário no nota SAP [1928533] ou nota SAP [2015553], recomendamos que trabalhe com a versão mais recente dos cmdlets do PowerShell do Azure.

Para verificar a versão dos cmdlets do PowerShell do Azure que estão instalados no seu computador, execute este comando do PowerShell:
```powershell
(Get-Module AzureRm.Compute).Version
```
O resultado tem o seguinte aspeto:

![Resultado da verificação da versão do cmdlet do PowerShell do Azure][deployment-guide-figure-600]
<a name="figure-6"></a>

Se a versão de cmdlet do Azure instalada no seu computador é a versão atual, a primeira página do Assistente de instalação indica que adicionando **(instalada)** para o título do produto (consulte a seguinte captura de ecrã). Os cmdlets do PowerShell Azure estão atualizados. Para fechar o Assistente de instalação, selecione **saída**.

![Página de instalação para os cmdlets do Azure PowerShell com a indicação de que a versão mais recente dos cmdlets do PowerShell do Azure estão instalados][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementar a CLI do Azure
1.  Aceda a [transfere do Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  Em **ferramentas da linha de comandos**, em **interface de linha de comandos do Azure**, selecione o **instalar** ligação para o seu sistema operativo.
3.  Na caixa de diálogo Gestor de transferência da Microsoft, para o ficheiro transferido (por exemplo, WindowsAzureXPlatCLI.3f.3f.3fnew.exe), selecione **executar**.
4.  Para executar o instalador de plataforma Web da Microsoft (Microsoft Web PI), selecione **Sim**.
5.  Uma página que parece que este é apresentado:

  ![Página de instalação para os cmdlets do PowerShell do Azure][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Selecione **instalar**e, em seguida, aceite os termos de licenciamento de Software Microsoft.
7.  CLI do Azure está instalado. Selecione **concluir** para fechar o Assistente de instalação.

Verifique frequentemente para atualizações para a CLI do Azure, o que normalmente é atualizada mensalmente. A forma mais fácil para procurar atualizações para se efetuar os passos de instalação anterior, até a página de instalação mostrado no passo 5.

Para verificar a versão da CLI do Azure que está instalado no seu computador, execute este comando:
```
azure --version
```

O resultado tem o seguinte aspeto:

![Resultado da verificação da versão do CLI do Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Associar uma VM a um domínio no local (apenas Windows)
Se implementar SAP VMs num cenário em vários locais, onde são expandidas do Active Directory no local e o DNS no Azure, é esperado que as VMs estão associação a um domínio no local. Os passos detalhados que efetuar para associar uma VM a um domínio no local e o software adicional necessário para ser um membro de um domínio no local, varia pelo cliente. Normalmente, para associar uma VM a um domínio no local, terá de instalar o software adicional, como o software antimalware e o software de cópia de segurança ou de monitorização.

Neste cenário, terá também de certificar-se de que, se as definições de proxy da Internet são forçadas quando uma VM associado um domínio no seu ambiente, o Windows conta do sistema Local (S-1-5-18) na VM do convidado tem as mesmas definições de proxy. A opção mais fácil é forçar o proxy através da utilização de um domínio de política de grupo, aplica-se a sistemas no domínio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Transferir, instalar e ativar o agente da VM do Azure
Para máquinas virtuais que sejam implementadas a partir de uma imagem do SO que não seja generalizada (por exemplo, uma imagem que não têm origem na ferramenta de preparação de sistema do Windows ou o sysprep,), tem de transferir, instalar e ativar o agente da VM do Azure manualmente.

Se implementar uma VM do Azure Marketplace, este passo não é necessário. Imagens do Azure Marketplace já tem o agente da VM do Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Transferir o agente da VM do Azure:
  1.  Transferir o [pacote instalador do agente da VM do Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  Armazene o pacote MSI do agente de VM localmente num servidor ou computador pessoal.
2.  Instale o agente da VM do Azure:
  1.  Liga à VM do Azure implementadas utilizando o protocolo RDP (Remote Desktop Protocol).
  2.  Abra uma janela do Explorador do Windows na VM e selecione o diretório de destino para o ficheiro MSI do agente de VM.
  3.  Arraste o ficheiro MSI de instalador do agente de VM do Azure do seu computador local/servidor para o diretório de destino do agente de VM na VM.
  4.  Faça duplo clique em ficheiro MSI da VM.
3.  Para VMs que estão associadas a domínios no local, certifique-se de que as definições de proxy de Internet eventual também se aplicam à conta de sistema Local do Windows (S-1-5-18) na VM, conforme descrito em [configurar o proxy][deployment-guide-configure-proxy]. O agente da VM é executado neste contexto e tem de ser capaz de ligar ao Azure.

Não é necessária nenhuma interação do utilizador para atualizar o agente da VM do Azure. O agente da VM é atualizado automaticamente e não requer um reinício VM.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Utilize os seguintes comandos para instalar o agente da VM para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) ou Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Se o agente já estiver instalado, ao atualizar o agente Linux do Azure, efetue os passos descritos no [atualizar o agente Linux do Azure numa VM para a versão mais recente a partir do GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar o proxy
Os passos que efetuar para configurar o proxy no Windows são diferentes da forma de que configurar o proxy no Linux.

#### <a name="windows"></a>Windows
As definições de proxy tem de ser corretamente configuradas para a conta de sistema Local aceder à Internet. Se as definições de proxy não estão definidas pela política de grupo, pode configurar as definições para a conta de sistema Local.

1. Aceda a **iniciar**, introduza **gpedit.msc**e, em seguida, selecione **Enter**.
2. Selecione **configuração do computador** > **modelos administrativos** > **componentes do Windows** > **do Internet Explorer**. Certifique-se de que a definição **tornar proxy definições por computador (em vez de por utilizador)** está desativada ou não está configurado.
3. No **painel de controlo**, aceda a **Centro de partilha de rede e** > **opções da Internet**.
4. No **ligações** separador, selecione o **definições de LAN** botão.
5. Limpar o **detetar automaticamente as definições** caixa de verificação.
6. Selecione o **utilizar um servidor proxy para a sua LAN** caixa de verificação e, em seguida, introduza o endereço de proxy e a porta.
7. Selecione o **avançadas** botão.
8. No **exceções** box, introduza o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux
Configurar o proxy correto no ficheiro de configuração do Microsoft Azure agente convidado, que está localizado em \\etc\\waagent.conf.

Defina os seguintes parâmetros:

1.  **Anfitrião de proxy HTTP**. Por exemplo, defina-o como **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **Porta de proxy HTTP**. Por exemplo, defina-o como **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Reinicie o agente.

  ```
  sudo service waagent restart
  ```

As definições de proxy no \\etc\\waagent.conf também se aplicam às extensões VM necessárias. Se pretender utilizar repositórios do Azure, certifique-se de que o tráfego para estes repositórios não vai através da sua intranet local. Se tiver criado rotas definidas pelo utilizador para ativar a imposição do túnel, certifique-se de que adicione uma rota que encaminha o tráfego para os repositórios diretamente à Internet e não através da ligação de VPN de site para site.

* **SLES**

  Também terá de adicionar rotas para os endereços IP listadas em \\etc\\regionserverclnt.cfg. A figura seguinte mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Também terá de adicionar rotas para os endereços IP dos anfitriões listados no \\etc\\yum.repos.d\\balanceadores de carga rhui. Por exemplo, veja a ilustração anterior.

* **Oracle Linux**

  Não existem nenhum repositórios para Oracle Linux no Azure. Terá de configurar os seus próprios repositórios para Oracle Linux ou utilizar repositórios do públicos.

Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [rotas definidas pelo utilizador e reencaminhamento IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar a extensão de monitorização avançada do Azure para SAP
Quando já preparou a VM conforme descrito em [cenários de implementação de VMs para SAP no Azure][deployment-guide-3], o agente da VM do Azure está instalado na máquina virtual. O passo seguinte é implementar o Azure avançada extensão de monitorização para SAP, que está disponível no repositório de extensões do Azure no global do Azure dos centros de dados. Para obter mais informações, consulte [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver][planning-guide-9.1].

Pode utilizar o PowerShell ou a CLI do Azure para instalar e configurar o Azure avançada extensão de monitorização para SAP. Para instalar a extensão num Windows ou VM com Linux através da utilização de um computador Windows, consulte [Azure PowerShell][deployment-guide-4.5.1]. Para instalar a extensão numa VM com Linux através da utilização de um ambiente de trabalho do Linux, consulte [CLI do Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>O Azure PowerShell para Linux e VMs do Windows
Para instalar o Azure avançada extensão de monitorização para SAP através do PowerShell:

1. Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Azure. Para obter mais informações, consulte [cmdlets de implementação do Azure PowerShell][deployment-guide-4.1].  
2. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista dos ambientes disponíveis, execute `commandlet Get-AzureRmEnvironment`. Se pretender utilizar o global Azure, o seu ambiente é **AzureCloud**. Para o Azure na China, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de introduzir os seus dados de conta e identificar a máquina virtual do Azure, o script implementa as extensões necessárias e permite que as funcionalidades necessárias. Isto pode demorar vários minutos.
Para obter mais informações sobre `Set-AzureRmVMAEMExtension`, consulte [conjunto AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![A execução com êxito do SAP específicos do Azure cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

O `Set-AzureRmVMAEMExtension` configuração todos os passos para configurar o anfitrião de monitorização para SAP.

O resultado do script inclui as seguintes informações:

* Confirmação de que a monitorização para o disco de SO e todos os discos de dados adicionais foi configurada.
* As mensagens de dois confirme a configuração das métricas do Storage para uma conta de armazenamento específico.
* Uma linha de saída fornece o estado da atualização real da configuração da monitorização.
* Outra linha do resultado confirma que a configuração foi implementada ou atualizada.
* A última linha do resultado é meramente informativa. Mostra as opções para testar a configuração de monitorização.
* Para verificar se todos os passos do Azure avançada monitorização foi executados com êxito e que a infraestrutura do Azure fornece os dados necessários, continuar com a verificação de disponibilidade para o Azure avançada extensão de monitorização para SAP, conforme descrito em [verificação de disponibilidade para avançada monitorização do Azure para SAP][deployment-guide-5.1].
* Aguarde 15-30 minutos para o Azure Diagnostics recolher os dados relevantes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI do Azure para VMs com Linux
Para instalar o Azure avançada extensão de monitorização para SAP utilizando a CLI do Azure:

1. Instalar a CLI do Azure 1.0, conforme descrito em [instalar a CLI do Azure 1.0][azure-cli].
2. Inicie sessão com a sua conta do Azure:

  ```
  azure login
  ```

3. Mudar para modo Azure Resource Manager:

  ```
  azure config mode arm
  ```

4. Ative a monitorização avançada do Azure:

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Certifique-se de que a extensão de monitorização de avançada para Azure está ativa na VM do Linux do Azure. Verifique se o ficheiro \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existe. Se existir, numa linha de comandos, execute este comando para apresentar as informações recolhidas pelo Monitor avançada do Azure:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

O resultado tem o seguinte aspeto:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
???
???
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e a resolução de problemas para a monitorização ponto a ponto
Depois de ter implementado a VM do Azure e configurar a infraestrutura de monitorização do Azure relevante, verifique se todos os componentes da extensão de monitorização de avançada para Azure estão a funcionar conforme esperado.

Execute a verificação de disponibilidade para o Azure avançada extensão de monitorização para SAP, conforme descrito em [verificação de disponibilidade para o Azure avançada extensão de monitorização para SAP][deployment-guide-5.1]. Se todos os resultados da verificação de preparação estão positivos e todos os contadores de desempenho relevantes aparecem OK, a monitorização do Azure tiver sido configurada com êxito. Pode continuar com a instalação do agente do anfitrião SAP, conforme descrito nas notas de SAP no [recursos SAP][deployment-guide-2.2]. Se a verificação de disponibilidade indica que os contadores estão em falta, execute a verificação de estado de funcionamento da infraestrutura de monitorização do Azure, conforme descrito em [verificação de estado de funcionamento de configuração da infraestrutura de monitorização do Azure][deployment-guide-5.2]. Para obter mais opções de resolução de problemas, consulte [monitorização do Azure de resolução de problemas para SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de disponibilidade para o Azure avançada extensão de monitorização para SAP
Esta verificação certifica-se de que todas as métricas de desempenho que são apresentados no interior da sua aplicação de SAP são fornecidas pela infraestrutura de monitorização do Azure subjacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Execute a verificação de disponibilidade numa VM do Windows

1.  Inicie sessão na máquina virtual do Azure (utilizar uma conta de administrador não é necessário).
2.  Abra uma janela de linha de comandos.
3.  Na linha de comandos, altere o diretório para a pasta de instalação do Azure avançada de monitorização da extensão para SAP: c:\\pacotes\\plug-ins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versão >\\drop

  O *versão* no caminho para a extensão de monitorização podem variar. Se vir pastas para várias versões da extensão de monitorização na pasta de instalação, verifique a configuração do serviço AzureEnhancedMonitoring Windows e, em seguida, mude para a pasta indicada como *caminho executável*.

  ![Propriedades de serviço com o Azure avançada extensão de monitorização para SAP][deployment-guide-figure-1000]

4.  Na linha de comandos, execute **azperflib.exe** sem quaisquer parâmetros.

  > [!NOTE]
  > Azperflib.exe é executado em ciclo e atualiza os contadores recolhidos 60 em 60 segundos. Para terminar o ciclo, feche a janela de linha de comandos.
  >
  >

Se a extensão de monitorização de avançada para Azure não está instalada ou o serviço de AzureEnhancedMonitoring não está em execução, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implementar a extensão, consulte [infraestruturas de monitorização do Azure de resolução de problemas para SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Verifique a saída do azperflib.exe
Saída de Azperflib.exe mostra que todos os preenchido contadores de desempenho do Azure para SAP. Na parte inferior da lista de contadores recolhidos, um indicador de estado de funcionamento e o resumo Mostrar o estado de monitorização do Azure.

![Resultado da verificação de estado de funcionamento executando azperflib.exe, que indica que existem sem problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verificar o resultado devolvido para o **total contadores** resultado, o que é comunicado como vazio e para **o estado de funcionamento**, mostrado na figura anterior.

Interpretar os valores resultantes da seguinte forma:

| Valores de resultado Azperflib.exe | Monitorização de estado de funcionamento do Azure |
| --- | --- |
| **Chamadas à API - não disponível** | Os contadores que não estão disponíveis poderão não é aplicável a configuração da máquina virtual, ou de erros. Consulte **o estado de funcionamento**. |
| **Total de contadores - vazio** |Os seguintes dois contadores de armazenamento do Azure podem estar vazios: <ul><li>Armazenamento ler Op latência mseg de servidor</li><li>Armazenamento ler Op latência E2E MS</li></ul>Todos os outros contadores tem de ter valores. |
| **Estado de funcionamento** |Se apenas OK devolver o estado Mostrar **OK**. |
| **Diagnóstico** |Obter informações detalhadas sobre o estado de funcionamento. |

Se o **o estado de funcionamento** valor não é **OK**, siga as instruções em [verificação de estado de funcionamento de configuração da infraestrutura de monitorização do Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Execute a verificação de preparação uma VM com Linux

1.  Liga a máquina de Virtual do Azure através de SSH.

2.  Verifique a saída da extensão de monitorização de avançada para Azure.

  a.  Execute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Era esperado o resultado**: lista de devolve dos contadores de desempenho. O ficheiro não deve estar vazio.

 b. Execute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Era esperado o resultado**: uma linha de devolve onde o erro é **nenhum**, por exemplo, **3; config; Erro; 0; 0; Nenhum; 0 1456416792; tst-servercs;**

  c. Execute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

    **Era esperado o resultado**: devolve como vazio ou não existe.

Se a verificação anterior não foi bem sucedida, execute estes verificações adicionais:

1.  Certifique-se de que o waagent está instalado e ativado.

  a.  Execute `sudo ls -al /var/lib/waagent/`

      **Era esperado o resultado**: indica o conteúdo do diretório waagent.

  b.  Execute `ps -ax | grep waagent`

   **Era esperado o resultado**: apresenta uma entrada semelhante a:`python /usr/sbin/waagent -daemon`

3.   Certifique-se de que a extensão de monitorização de avançada para Azure está instalado e em execução.

  a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

    **Era esperado o resultado**: indica o conteúdo do diretório da extensão de monitorização avançada da Azure.

  b. Execute `ps -ax | grep AzureEnhanced`

     **Era esperado o resultado**: apresenta uma entrada semelhante a:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

3. Instalar o agente do anfitrião SAP, conforme descrito na nota SAP [1031096]e verificar o resultado do `saposcol`.

  a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

  b.  Execute `dump ccm`

  c.  Verifique se o **Virtualization_Configuration\Enhanced monitorização acesso** métrica é **verdadeiro**.

Se já tiver um servidor de aplicações do SAP NetWeaver ABAP instalado, abra a transação ST06 e verificar se a monitorização avançada está ativada.

Se qualquer uma destas verificações falhar e para obter informações detalhadas sobre como implementar novamente a extensão, consulte [infraestruturas de monitorização do Azure de resolução de problemas para SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de estado de funcionamento para a configuração de infraestrutura de monitorização do Azure
Se alguns da monitorização de dados não for entregue a corretamente conforme indicado pelo teste descrito [verificação de disponibilidade para avançada monitorização do Azure para SAP][deployment-guide-5.1], execute o `Test-AzureRmVMAEMExtension` cmdlet para verificar se o Azure monitorização de infraestrutura e a monitorização de extensão para SAP estão configuradas corretamente.

1.  Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Azure, conforme descrito em [cmdlets de implementação do Azure PowerShell][deployment-guide-4.1].
2.  Execute o seguinte cmdlet do PowerShell. Para obter uma lista dos ambientes disponíveis, execute o cmdlet `Get-AzureRmEnvironment`. Para utilizar global do Azure, selecione o **AzureCloud** ambiente. Para o Azure na China, selecione **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Login-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Introduza os seus dados de conta e identificar a máquina virtual do Azure.

  ![Página de entrada do SAP específicos do Azure cmdlet VMConfigForSAP_GUI de teste][deployment-guide-figure-1200]

4. O script testa a configuração da máquina virtual que selecionou.

  ![Resultado do teste concluída com êxito de infraestruturas de monitorização do Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que todos os resultados da verificação de estado de funcionamento **OK**. Se não for apresentado algumas verificações **OK**, execute o cmdlet de atualização, conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5]. Aguarde 15 minutos e repita as verificações de descrito em [verificação de disponibilidade para avançada monitorização do Azure para SAP] [ deployment-guide-5.1] e [verificação de estado de funcionamento de configuração da infraestrutura de monitorização do Azure][deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, consulte o artigo [infraestruturas de monitorização do Azure de resolução de problemas para SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Resolução de problemas de infraestrutura de monitorização do Azure para SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Contadores de desempenho do Azure não apresentada em todas as
O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho no Azure. Se o serviço não foi instalado corretamente ou não está em execução na sua VM, a não haver métricas de desempenho podem ser recolhidas.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão de monitorização de avançada para Azure está vazio

###### <a name="issue"></a>Problema
O diretório de instalação c:\\pacotes\\plug-ins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versão >\\largar está vazio.

###### <a name="solution"></a>Solução
A extensão não está instalada. Determine se se trata de um problema de proxy (como descrito anteriormente). Poderá ter de reiniciar a máquina ou execute novamente o `Set-AzureRmVMAEMExtension` script de configuração.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Serviço para monitorização avançada do Azure não existe

###### <a name="issue"></a>Problema
O serviço AzureEnhancedMonitoring Windows não existe.

Saída de Azperflib.exe emite um erro:

![A execução do azperflib.exe indica que o serviço de extensão de monitorização de avançada para do Azure para SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução
Se o serviço não existir, o Azure avançada extensão de monitorização para SAP não foi instalado corretamente. Reimplementar a extensão, utilizando os passos descritos para o seu cenário de implementação no [cenários de implementação de VMs para SAP no Azure][deployment-guide-3].

Depois de implementar a extensão, depois de uma hora, verifique novamente se os contadores de desempenho do Azure são fornecidos na VM do Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Serviço para monitorização avançada do Azure existe, mas não consegue iniciar

###### <a name="issue"></a>Problema
O serviço AzureEnhancedMonitoring Windows existe e está ativado, mas não consegue iniciar. Para obter mais informações, consulte o registo de eventos de aplicações.

###### <a name="solution"></a>Solução
A configuração está incorreta. Reinicie a extensão de monitorização para a VM, conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Faltam alguns contadores de desempenho do Azure
O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho no Azure. O serviço obtém dados de várias origens. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidos a partir do diagnóstico do Azure. Os contadores de armazenamento são utilizados a partir do seu registo ao nível da subscrição de armazenamento.

Se a resolução de problemas ao utilizar a nota SAP [1999351] não resolver o problema, execute novamente o `Set-AzureRmVMAEMExtension` script de configuração. Poderá ter de aguardar uma hora, porque os contadores de análise ou diagnósticos de armazenamento não podem ser criados imediatamente após estão ativadas. Se o problema persistir, abra uma mensagem de suporte de cliente SAP no componente BC OP-NT AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Contadores de desempenho do Azure não apresentada em todas as
Métricas de desempenho no Azure são recolhidas por um daemon. Se o daemon de mensagens em fila não está em execução, não haver métricas de desempenho podem ser recolhidas.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão avançada monitorização do Azure está vazio

###### <a name="issue"></a>Problema
O diretório \\var\\lib\\waagent\\ não tem um subdiretório para a extensão avançada monitorização do Azure.

###### <a name="solution"></a>Solução
A extensão não está instalada. Determine se se trata de um problema de proxy (como descrito anteriormente). Poderá ter de reiniciar a máquina e/ou execute novamente o `Set-AzureRmVMAEMExtension` script de configuração.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Faltam alguns contadores de desempenho do Azure
Métricas de desempenho no Azure são recolhidas por um daemon, que obtém dados de várias origens. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidos a partir do diagnóstico do Azure. Os contadores de armazenamento provenientes os registos na sua subscrição de armazenamento.

Para obter uma lista completa e atualizada dos problemas conhecidos, consulte a nota SAP [1999351], que tem informações adicionais de resolução de problemas de avançada monitorização do Azure para SAP.

Se a resolução de problemas ao utilizar a nota SAP [1999351] não resolver o problema, execute novamente o `Set-AzureRmVMAEMExtension` script de configuração conforme descrito em [configurar o Azure avançada extensão de monitorização para SAP][deployment-guide-4.5]. Poderá ter de aguardar por uma hora, porque os contadores de análise ou diagnósticos de armazenamento não podem ser criados imediatamente após estão ativadas. Se o problema persistir, abra uma mensagem de suporte de cliente SAP no componente BC OP-NT AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.
