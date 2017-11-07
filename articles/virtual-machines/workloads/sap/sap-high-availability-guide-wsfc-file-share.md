---
title: "Clustering SAP (A) instância SCS no Cluster de ativação pós-falha do Windows utilizando a partilha de ficheiros no Azure | Microsoft Docs"
description: "Clustering SAP (A) SCS instância no Cluster de ativação pós-falha do Windows com a partilha de ficheiros"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94d725cfb072091e57c96d3b2aca7b2e73657eef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Clustering SAP (A) SCS instância no Cluster de ativação pós-falha do Windows utilizando a partilha de ficheiros no Azure

> ![Windows][Logo_Windows] Windows
>

Clustering de ativação pós-falha do Windows Server é a base de uma elevada disponibilidade SAP ASCS/SCS instalação e DBMS no Windows.

Um cluster de ativação pós-falha é um grupo de 1 + n servidores independentes (nós) que funcionam em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha de nó, o Clustering de ativação pós-falha do Windows Server calcula o número de falhas que podem ocorrer, mantendo em simultâneo um cluster de bom estado de funcionamento para fornecer aplicações e serviços. Pode escolher entre modos diferentes de quórum para conseguir o clustering de ativação pós-falha.

## <a name="prerequisite"></a>Pré-requisito
Certifique-se de que revê estes documentos antes de iniciar com este documento:

* [Cenários para SAP NetWeaver e arquitetura de elevada disponibilidade de máquinas virtuais do Azure][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>Clustering de instâncias SCS SAP (A) com partilha de ficheiros é suportado para **SAP NetWeaver 7.40 (e superior)** produtos, com **SAP Kernel 7.49 (e superior)**.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server Clustering de ativação pós-falha no Azure

Em comparação com bare implementações de nuvem privada ou metal, Virtual Machines do Azure requer passos adicionais para configurar o Clustering de ativação pós-falha do Windows Server. Quando criar um cluster, tem de definir vários endereços IP e nomes de anfitrião virtual para a instância do SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Resolução de nomes no Azure e o nome de anfitrião Virtual de Cluster

A plataforma de nuvem do Azure não oferece a opção para configurar endereços IP virtuais, tais como endereços IP flutuante. É necessário uma solução alternativa para configurar um endereço IP virtual para alcançar o recurso de cluster na nuvem. O Azure tem um **Balanceador de carga interno** no serviço de Balanceador de carga do Azure. Com o Balanceador de carga interno, os clientes conseguirem contactar o cluster através do endereço IP virtual do cluster. Tem de implementar o Balanceador de carga interno no grupo de recursos que contém nós de cluster. Em seguida, configure todas as portas necessárias reencaminhamento regras com a pesquisa de portas do Balanceador de carga interno. Os clientes podem ligar através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster e a porta de identificadores de Balanceador de carga interno de reencaminhamento para o nó ativo do cluster.

![Figura 1: Clustering de ativação pós-falha do Windows Server configuração no Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 1:** configuração Clustering de ativação pós-falha do Windows Server no Azure sem um disco partilhado_

## <a name="sap-ascs-ha-with-file-share"></a>SAP (A) HA SCS com partilha de ficheiros

SAP desenvolvidas nova abordagem e alternativa ao cluster discos partilhados, instância de SCS SAP (A) cluster no Cluster de ativação pós-falha do Windows.

É utilizado aqui **partilha de ficheiros SMB** é uma opção para implementar **ficheiros anfitrião GLOBAL SAP**.

> [!NOTE]
>Partilha de ficheiros SMB é uma opção adicional para discos partilhados para clustering de instâncias SCS SAP (A) de cluster.  
>

O que é específico para esta arquitetura é o seguinte:

* **SAP serviços centrais (com processos de estrutura e a mensagem e a colocar em fila de ficheiro próprio) são separados dos ficheiros do anfitrião GLOBAL SAP**
* **Serviços de centrais de SAP ser executados com a instância do SCS SAP (A)**
* Instância do SAP (A) SCS está agrupada e é acessível através de virtual o nome de anfitrião **< (A) SCSVirtualHostName >**
* Ficheiros SAP globais são colocados numa partilha de ficheiros SMB e são acedidos através de <SAPGLOBALHost> nome de anfitrião \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
* A instância do SCS SAP (A) está instalada num disco local em ambos os nós de cluster
* O **< (A) SCSVirtualHostName >** nome de rede é diferente do  **&lt;SAPGLOBALHost&gt;**

![Figura 2: Novo SAP (A) SCS HA arquitetura com partilha de ficheiros SMB][sap-ha-guide-figure-8004]

_**Figura 2:** nova SAP (A) SCS HA arquitetura com partilha de ficheiros SMB_

Pré-requisitos para a partilha de ficheiros SMB:

* Protocolo SMB 3.0 (ou superior)
* Capacidade de definir a lista de controlo de acesso (ACLs) do Active Directory (AD) para **grupos de utilizadores do AD** e **$ de computador do objeto de computador**
* Partilha de ficheiros tem de ser HA ativada:
    * Utilizado para armazenar ficheiros de discos não têm de ser um ponto único de falha
    * Tem de garantir que período de indisponibilidade de VMs/servidores não está a causar o período de indisponibilidade da partilha de ficheiros

Agora, o **SAP &lt;SID&gt;**  função de cluster é que não contém discos partilhados de cluster ou recurso de cluster de partilha de ficheiros genérico.


![Figura 3: SAP < SID > função recursos do cluster quando utilizar a partilha de ficheiros][sap-ha-guide-figure-8005]

_**Figura 3:** **SAP &lt;SID&gt;**  recursos de função de cluster, ao utilizar a partilha de ficheiros_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Escalamento horizontal (SOFS) da partilha de ficheiros com direta de espaços de armazenamento (S2D) no Azure como SAPMNT partilha de ficheiros

Pode utilizar SOFS para alojar e proteger ficheiros do anfitrião GLOBAL SAP e a oferta de serviço de partilha de ficheiros SAPMNT altamente disponível.

![Figura 4: Partilha de ficheiros SOFS utilizada para proteger ficheiros do anfitrião GLOBAL SAP][sap-ha-guide-figure-8006]

_**Figura 4:** partilha de ficheiros SOFS utilizada para proteger ficheiros do anfitrião GLOBAL SAP_

> [!IMPORTANT]
>Partilha de ficheiros SOFS é totalmente suportada no Microsoft Azure na nuvem, bem como em ambientes no local.
>

**SOFS** é a oferta partilha de ficheiros SAPMNT altamente disponível e escalável horizontalmente.

**Espaços de armazenamento direto (S2D)**, é utilizado como **disco partilhado** para SOFS, permite a criação de armazenamento de elevada disponibilidade e dimensionável através de servidores com o armazenamento local. Por conseguinte, o armazenamento partilhado utilizado para SOFS, por exemplo, para os ficheiros de SAP GLOBALHOST não é um ponto único de falha (SPOF).

> [!IMPORTANT]
>Se planear a recuperação de desastres do programa de configuração, SOFS recomenda a solução para a partilha de ficheiros altamente disponíveis no Azure.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Pré-requisitos SAP para SOFS no Azure

Para o SOFS precisa de:

* Pelo menos dois nós de cluster para SOFS

* Cada nó tem de ter, pelo menos, dois discos locais

* Para o motivo de desempenho, tem de utilizar **espelhamento resiliência**:
    * **2-vias** espelhamento para SOFS com dois nós de cluster
    * **3-vias** espelhamento para SOFS connosco de cluster de três (ou mais)


* É **recomendado ter 3 (ou mais clusters) nós para SOFS com espelhamento de 3 vias**.
Esta configuração oferece mais escalabilidade e resiliência de armazenamento mais que o programa de configuração do SOFS com 2 nós de cluster e o espelhamento de 2 vias.

* Tem de utilizar **disco do Azure Premium**

* É **recomendado** utilizar **discos geridos pelo Azure**

* É **recomendado** para volumes de formato com novos **sistema de ficheiros Resiliente (ReFS)**
    * [SAP nota 1869038 - suporte SAP para o sistema de ficheiros ReFs] [1869038]
    * Consulte [escolher o sistema de ficheiros] [ planning-volumes-s2d-choosing-filesystem] capítulo de planeamento de volumes em espaços de armazenamento direto.
    * Certifique-se instalar este [MS **KB4025334** a atualização cumulativa][kb4025334].


* Pode utilizar **-série DS** ou **série Dsv2** tamanhos de VM do Azure

* Para que boa inter-o desempenho da rede VM que é necessária para a sincronização direta de espaços de armazenamento do disco, deve utilizar um tipo VM que tenha, pelo menos, um **largura de banda de rede "elevada"**.
Para obter mais detalhes, consulte [série Dsv2] [ dv2-series] e [-série DS] [ ds-series] especificação.

* É **recomendado** sair e **algumas capacidade no agrupamento de armazenamento não alocado de reserva**. Proporciona volumes de espaço para reparar "no local" depois de unidades falharem, melhorar a segurança de dados e o desempenho.

 Para obter mais detalhes, consulte [escolher o tamanho de volumes][choosing-the-size-of-volumes-s2d]


* As VMs do Azure do SOFS tem de ser implementadas no **proprietário do conjunto de disponibilidade do Azure**

* Não é necessário para configurar o Balanceador de carga interno do Azure para nome de rede de partilha de ficheiros SOFS, por exemplo, <SAPGlobalHostName>, como é efetuada para < (A) SCSVirtualHostname > da instância do SCS SAP (A) ou para o DBMS. SOFS é aumentar horizontalmente a carga em todos os nós de cluster, por isso, <SAPGlobalHostName> está a utilizar o IP local de todos os nós de cluster.


> [!IMPORTANT]
>Não é possível alterar a partilha de ficheiros SAPMNT que aponta para SAPGLOBALHOST. SAP não suporta um nome diferente de partilha como sapmnt.
>[SAP nota 2492395 - é possível alterar o sapmnt de nome de partilha?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>Configurar SAP (A) SCS instâncias e SOFS em dois Clusters

Tem a possibilidade de implementar instâncias SCS SAP (A) de um cluster, com os seus próprios SAP <SID> função de cluster. A partilha de ficheiros SOFS está configurada no cluster outra com uma outra função de cluster.

> [!IMPORTANT]
>Neste cenário, a instância SCS SAP (A) está configurada para aceder à GLOBALHost SAP utilizando o caminho UNC \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
>

![Figura 5: SAP (A) instância SCS e SOFS implementado em dois clusters][sap-ha-guide-figure-8007]

_**Figura 5:** SAP (A) instância SCS e SOFS implementado em dois clusters_

> [!IMPORTANT]
>Na nuvem do Azure, cada cluster utilizado para SAP e para o ficheiro SOFS partilhas tem de ser implementadas na sua própria Azure do conjunto de disponibilidade garantir distribuído a colocação dessas VMS do cluster em toda a infraestrutura do Azure subjacente.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>Partilha de ficheiros genérico com SIOS como discos partilhados de Cluster


> [!IMPORTANT]
>SOFS é recomendada a solução para a partilha de ficheiros altamente disponível.
>
>No entanto, se pretender definir também **recuperação após desastre** para a partilha de ficheiros altamente disponíveis, tem de utilizar partilhas de ficheiro genérico e SISO como tecnologia para discos partilhados de cluster.
>

Partilha de ficheiros genérico é outra opção para obtenção de uma partilha de ficheiros altamente disponível.

Aqui, como um cluster de disco partilhado pode utilizar 3rd solução SIOS de terceiros.

## <a name="next-steps"></a>Passos Seguintes

* [Preparação de infraestrutura do Azure para SAP HA utilizando o Cluster de ativação pós-falha do Windows e a partilha de ficheiros para a instância de SCS SAP (A)][sap-high-availability-infrastructure-wsfc-file-share]

* [SAP NetWeaver HA instalação no Cluster de ativação pós-falha do Windows e a partilha de ficheiros para a instância SCS SAP (A)][sap-high-availability-installation-wsfc-shared-disk]

* [Implementar um servidor de ficheiros de escalamento horizontal espaços de armazenamento direto dois nós de armazenamento UPD no Azure][deploy-sofs-s2d-in-azure]

* [Espaços de armazenamento diretos no Windows Server 2016][s2d-in-win-2016]

* [Descrição detalhada da: Volumes em espaços de armazenamento diretos][deep-dive-volumes-in-s2d]
