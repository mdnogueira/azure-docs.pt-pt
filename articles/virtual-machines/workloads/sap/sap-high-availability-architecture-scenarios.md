---
title: "Arquitetura de elevada disponibilidade de máquinas virtuais do Azure e cenários para SAP NetWeaver | Microsoft Docs"
description: "Arquitetura de elevada disponibilidade e cenários para SAP NetWeaver em Azure Virtual Machines"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31f3765d807882e65a247819a5999c191f9e7ac5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arquitetura de elevada disponibilidade e cenários para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Definições terminológicas

**Elevada disponibilidade**: refere-se a um conjunto de tecnologias que minimizar as interrupções de IT, fornecendo a continuidade do negócio de serviços de TI através de componentes redundantes, com tolerância a falhas ou protegidos de ativação pós-falha no interior do *mesmo*Centro de dados. No nosso caso, o Centro de dados reside dentro de uma região do Azure.

**Recuperação após desastre**: também faz referência a minimizar de interrupção de serviços IT e os respetivos recuperação, mas across *vários* centros de dados que possam ser centenas de quilómetros um do outro. No nosso caso, os centros de dados podem residir em várias regiões do Azure na mesma região geopolítica ou em localizações como estabelecido por si, como um cliente.


## <a name="overview-of-high-availability"></a>Descrição geral da elevada disponibilidade
SAP elevada disponibilidade no Azure pode ser separada em três tipos:

* **Elevada disponibilidade da infraestrutura do Azure**: 

    Por exemplo, elevada disponibilidade pode incluir (VMs), rede, ou armazenamento e computação suas vantagens para aumentar a disponibilidade de aplicações SAP.

* **Utilizar a infraestrutura do Azure VM reiniciar para alcançar *maior disponibilidade* das aplicações SAP**: 

    Se optar por não utilizar funcionalidades, tais como Clustering de ativação pós-falha de servidor ao Windows ' (WSFC) ou Pacemaker no Linux, é utilizado o reinício da VM do Azure. Sistemas SAP contra períodos de indisponibilidade planeado e imprevisto a infraestrutura de servidor físico do Azure e a plataforma do Azure de subjacente geral que protege.

* **Disponibilidade elevada da aplicação do SAP**: 

    Para alcançar completa SAP sistema elevada disponibilidade, terá de proteger todos os componentes de sistema críticos de SAP. Por exemplo:
    * Servidores de aplicações SAP redundantes.
    * Componentes exclusivos. Um exemplo pode ser um ponto único de componente de falha (SPOF), por exemplo, uma instância do SAP ASCS/SCS ou um sistema de gestão de base de dados (DBMS).

SAP elevada disponibilidade no Azure é diferente do SAP de elevada disponibilidade no ambiente no local físico ou virtual. O documento seguinte [SAP NetWeaver elevada disponibilidade e continuidade do negócio nos ambientes virtuais com o VMware e o Hyper-V no Microsoft Windows] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] descreve padrão SAP elevada disponibilidade configurações em ambientes virtualizados no Windows.

Não há nenhuma configuração de elevada disponibilidade integradas sapinst SAP para Linux que estejam para Windows. Para informações sobre SAP elevada disponibilidade no local do Linux, consulte [informações do parceiro de elevada disponibilidade][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Elevada disponibilidade da infraestrutura do Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA para máquinas virtuais de instância única

Não há atualmente um SLA de VM única de 99,9% com o armazenamento premium. Para obter uma ideia sobre o que poderá ser a disponibilidade de uma única VM, pode criar o produto dos disponíveis vários [contratos de nível de serviço do Azure][azure-sla].

A base para o cálculo é 30 dias, por mês ou 43.200 minutos. Por exemplo, um período de indisponibilidade 0,05% corresponde à opção 21.6 minutos. Normalmente, a disponibilidade dos serviços de vários é calculada da seguinte forma:

(Serviço de disponibilidade #1/100) * (serviço de disponibilidade #2/100) * (serviço de disponibilidade #3/100) \*...

Por exemplo:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 ou um disponibilidade global da 99.75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Várias instâncias de máquinas virtuais no mesmo conjunto de disponibilidade
Todas as máquinas virtuais que têm duas ou mais instâncias implementadas da mesma *conjunto de disponibilidade*, garantimos que tenha conectividade de máquinas virtuais para, pelo menos, uma instância, pelo menos, de 99,95% do tempo.

Quando duas ou mais VMs fazem parte do mesmo conjunto de disponibilidade, cada máquina virtual no conjunto de disponibilidade é atribuída um *domínio de atualização* e um *domínio de falhas* pela plataforma do Azure subjacente.

* **Domínios de atualização** garantir que não são reiniciadas, várias VMs em simultâneo durante a manutenção planeada de uma infraestrutura do Azure. Apenas uma VM for reiniciada cada vez.

* **Domínios de falha** garantir que as VMs implementadas em componentes de hardware que não partilham um comutador de rede de origem e de energia comum. Quando os servidores, um comutador de rede ou uma origem de energia sofrer um período de indisponibilidade não planeado, apenas uma VM é afetada.

Para obter mais informações, consulte [gerir a disponibilidade das virtual machines Windows no Azure][azure-virtual-machines-manage-availability].

Um conjunto de disponibilidade é utilizado para alcançar a elevada disponibilidade de:

* Servidores de aplicações SAP redundantes.  
* Clusters com dois ou mais nós (VMs, por exemplo) que proteger SPOFs como uma instância do SAP ASCS/SCS ou um DBMS.

### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Manutenção planeada e imprevista de máquinas virtuais

Dois tipos de eventos de plataforma do Azure podem afetar a disponibilidade das máquinas virtuais:

* **A manutenção planeada** eventos são atualizações periódicas efetuadas pela Microsoft para a plataforma subjacente do Azure. As atualizações de melhorar a fiabilidade geral, desempenho e segurança da infraestrutura da plataforma que executam as máquinas virtuais no.

* **Manutenção não planeada** eventos ocorrem quando o hardware ou a infraestrutura física subjacente a máquina virtual falhou de alguma forma. Poderão incluir falhas de rede local, falhas de disco local ou outras falhas de nível de bastidor. Quando é detetada uma falha de tal, a plataforma do Azure migra automaticamente a máquina virtual do servidor físico mau estado de funcionamento que aloja a máquina virtual para um servidor físico em bom estado. Esses eventos são raros, mas também poderão fazer com que a máquina virtual reiniciar.

Para obter mais informações, consulte [gerir a disponibilidade das virtual machines Windows no Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundância do armazenamento do Azure
Os dados na sua conta de armazenamento estão sempre replicados para garantir durabilidade e elevada disponibilidade, a cumprir o SLA de armazenamento do Azure, mesmo face a falhas de hardware transitórias.

Porque o armazenamento do Azure mantém três imagens dos dados, por predefinição, a utilização de RAID 5 ou RAID 1 entre múltiplos discos do Azure é desnecessária.

Para obter mais informações, consulte [replicação de armazenamento do Azure][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Managed Disks do Azure
Discos geridos é um novo tipo de recurso no Azure Resource Manager que pode ser utilizado em vez de discos rígidos virtuais (VHDs) que estão armazenados em contas do storage do Azure. Discos geridos alinham automaticamente com o conjunto de disponibilidade da máquina virtual que estão ligados a. Podem aumentam a disponibilidade de máquina virtual e os serviços que estão em execução no mesmo.

Para obter mais informações, consulte [descrição geral do Azure gerida discos][azure-storage-managed-disks-overview].

Recomendamos que utilize discos geridos porque estes simplificam a implementação e gestão das suas máquinas virtuais.

SAP atualmente suporta apenas os discos premium gerido. Para obter mais informações, consulte SAP nota [1928533].

## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>A utilização de elevada disponibilidade de infraestrutura do Azure para alcançar *maior disponibilidade* das aplicações SAP

Se optar por não utilizar funcionalidades como WSFC ou Pacemaker no Linux (atualmente suportado apenas para o SUSE Linux Enterprise Server [SLES] 12 e posterior), é utilizado o reinício da VM do Azure. Sistemas SAP contra períodos de indisponibilidade planeado e imprevisto a infraestrutura de servidor físico do Azure e a plataforma do Azure de subjacente geral que protege.

Para obter mais informações sobre esta abordagem, consulte [infraestrutura do Azure utilizam VM reiniciar para alcançar a elevada disponibilidade do sistema SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Elevada disponibilidade de aplicações de SAP em IaaS do Azure

Para alcançar completa SAP sistema elevada disponibilidade, terá de proteger todos os componentes de sistema críticos de SAP. Por exemplo:
  * Servidores de aplicações SAP redundantes.
  * Componentes exclusivos. Um exemplo pode ser um ponto único de componente de falha (SPOF), por exemplo, uma instância do SAP ASCS/SCS ou um sistema de gestão de base de dados (DBMS).

As secções seguintes abordam como atingir elevada disponibilidade para todos os três componentes mais importantes SAP sistema.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Arquitetura de elevada disponibilidade para servidores de aplicações SAP

> Esta secção aplica-se a:
>
> ![Windows][Logo_Windows] Windows e ![Linux][Logo_Linux] Linux
>

Normalmente, não precisa de uma solução de elevada disponibilidade específica para as instâncias de servidor e a caixa de diálogo de aplicação do SAP. Alcançar a elevada disponibilidade por redundância e configurar várias instâncias de caixa de diálogo em várias instâncias de máquinas virtuais do Azure. Deve ter, pelo menos, duas instâncias de aplicações SAP instaladas em duas instâncias de máquinas virtuais do Azure.

![Figura 1: Servidor de aplicações de elevada disponibilidade SAP][sap-ha-guide-figure-2000]

_**Figura 1:** servidor de aplicações SAP de elevada disponibilidade_

Tem de colocar todas as máquinas virtuais que definir de instâncias do servidor de aplicações do anfitrião SAP no mesmo de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de atualização.  
    Um domínio de atualização garante que as máquinas virtuais não são atualizadas ao mesmo tempo durante o período de indisponibilidade de manutenção planeada.

    A funcionalidade básica, o qual baseia-se a atualização diferentes e domínios de falhas dentro de uma unidade de escala do Azure, já foi introduzida no [domínios de atualização] [ planning-guide-3.2.2] secção.

* Todas as máquinas virtuais fazem parte do mesmo domínio de falhas.  
    Um domínio de falhas assegura que as máquinas virtuais são implementadas para que não existe nenhum ponto único de falha afeta a disponibilidade de todas as máquinas virtuais.

O número de domínios de atualização e falhas que podem ser utilizados por um conjunto dentro de uma unidade de escala do Azure de disponibilidade do Azure é finito. Se manter a adicionar VMs a um conjunto de disponibilidade único, dois ou mais VMs, eventualmente, irão surgir no mesmo domínio de falhas ou atualização.

Se implementar algumas instâncias de servidor de aplicações SAP no respetivos VMs dedicadas, partindo do princípio que temos cinco domínios de atualização, a imagem seguinte emerges. O número real de máximo de domínios de atualização e falhas dentro de um conjunto de disponibilidade poderá alterar no futuro:

![Figura 2: Elevada disponibilidade dos servidores de aplicações SAP num conjunto de disponibilidade do Azure][planning-guide-figure-3000]
_**figura 2:** elevada disponibilidade dos servidores de aplicações SAP num conjunto de disponibilidade do Azure_

Para obter mais informações, consulte [gerir a disponibilidade das virtual machines Windows no Azure][azure-virtual-machines-manage-availability].

Para obter mais informações, consulte o [conjuntos de disponibilidade do Azure] [ planning-guide-3.2.3] secção das máquinas virtuais do Azure, planeamento e implementação para SAP NetWeaver documento.

**Não gerido apenas discos:** porque a conta de armazenamento do Azure é um potencial ponto único de falha, é importante que tenha pelo menos duas contas de armazenamento do Azure, em que são distribuídas, pelo menos, duas máquinas virtuais. Uma configuração ideal, os discos de cada máquina virtual que está a executar uma instância de caixa de diálogo SAP seriam implementados numa conta de armazenamento diferente.

> [!IMPORTANT]
> Recomendamos vivamente que utiliza o Azure geridos discos para as instalações de elevada disponibilidade do SAP. Porque discos geridos alinham automaticamente com o conjunto de disponibilidade da máquina virtual que estão ligados a, podem aumentam a disponibilidade de máquina virtual e os serviços que estão em execução no mesmo.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Arquitetura de elevada disponibilidade para uma instância do SAP ASCS/SCS no Windows

> ![Windows][Logo_Windows] Windows
>

Pode utilizar uma solução WSFC para proteger a instância do SAP ASCS/SCS. A solução tem dois variantes:

* **A instância do SAP ASCS/SCS de cluster utilizando discos partilhados em cluster**: Para mais informações sobre esta arquitetura, consulte [Cluster uma instância do SAP ASCS/SCS num cluster de ativação pós-falha do Windows, utilizando um disco partilhado de cluster] [ sap-high-availability-guide-wsfc-shared-disk].   

* **A instância do SAP ASCS/SCS de cluster utilizando a partilha de ficheiros**: Para mais informações sobre esta arquitetura, consulte [Cluster uma instância do SAP ASCS/SCS num cluster de ativação pós-falha do Windows, utilizando a partilha de ficheiros] [ sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Arquitetura de elevada disponibilidade para uma instância do SAP ASCS/SCS no Linux

> ![Linux][Logo_Linux] Linux
>
Para obter mais informações sobre o clustering de instância do SAP ASCS/SCS utilizando o framework de cluster SLES, consulte [elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações SAP] [ sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Configuração de várias SID do SAP NetWeaver para uma instância do SAP ASCS/SCS em cluster

> ![Windows][Logo_Windows] Windows
>
> Atualmente, várias SID só é suportado com WSFC. Várias SID é suportado com a partilha de ficheiros e de disco partilhado.
>
Para obter mais informações sobre a arquitetura de elevada disponibilidade de várias SID, consulte:

* [SAP ASCS/SCS instância elevada disponibilidade de várias SID para Clustering de ativação pós-falha do Windows Server e a partilha de ficheiros][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS instância elevada disponibilidade de várias SID para Clustering de ativação pós-falha do Windows Server e o disco partilhado][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Instância DBMS de elevada disponibilidade

O DBMS também é um único ponto de contacto num sistema SAP. Terá de protegê-lo utilizando uma solução de elevada disponibilidade. A figura seguinte mostra uma solução de elevada disponibilidade do AlwaysOn do SQL Server no Azure, com Clustering de ativação pós-falha do Windows Server e do Azure interno Balanceador de carga. SQL Server AlwaysOn replica os ficheiros de dados e de registo DBMS utilizando a sua própria replicação DBMS. Neste caso, não precisa de disco partilhado de cluster, o que simplifica a configuração completa.

![Figura 3: Exemplo de um DBMS de SAP de elevada disponibilidade, com o SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 3:** exemplo de um DBMS de SAP de elevada disponibilidade, com o SQL Server AlwaysOn_

Para obter mais informações sobre o clustering do SQL Server DBMS no Azure utilizando o modelo de implementação Azure Resource Manager, consulte estes artigos:

* [Configurar um grupo de Disponibilidade AlwaysOn nas virtual machines do Azure manualmente utilizando o Gestor de recursos][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configurar um balanceador de carga interno do Azure para um grupo de Disponibilidade AlwaysOn no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para obter mais informações sobre o clustering de SAP HANA DBMS no Azure utilizando o modelo de implementação Azure Resource Manager, consulte [elevada disponibilidade de SAP HANA em máquinas de virtuais (VMs) do Azure][sap-hana-ha].
