---
title: "Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows através da utilização de uma partilha de ficheiros no Azure | Microsoft Docs"
description: "Saiba como uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows através da utilização de uma partilha de ficheiros no Azure."
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
ms.openlocfilehash: 8cb339c9ecffbbc711aa6ea55d6f357fe0f4cfd0
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows através da utilização de uma partilha de ficheiros no Azure

> ![Windows][Logo_Windows] Windows
>

Clustering de ativação pós-falha do Windows Server é a base de uma elevada disponibilidade SAP ASCS/SCS instalação e DBMS no Windows.

Um cluster de ativação pós-falha é um grupo de 1 + n servidores independentes (nós) que funcionam em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha de nó, o clustering de ativação pós-falha do Windows Server calcula o número de falhas que podem ocorrer e continuar a manter um bom estado de funcionamento cluster para fornecer aplicações e serviços. Pode escolher entre modos diferentes de quórum para conseguir o clustering de ativação pós-falha.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as tarefas que são descritas neste artigo, reveja este artigo:

* [Cenários para SAP NetWeaver e arquitetura de elevada disponibilidade de máquinas virtuais do Azure][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clustering de instâncias de SAP ASCS/SCS através da utilização de uma partilha de ficheiros é suportada para SAP NetWeaver 7.40 (e posterior), com o SAP Kernel 7.49 (e mais tarde).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows clustering de ativação pós-falha no Azure

Em comparação com as implementações de nuvem privada ou de bare-metal, Virtual Machines do Azure requer passos adicionais para configurar o clustering de ativação pós-falha do Windows Server. Quando criar um cluster, tem de definir vários endereços IP e nomes de anfitrião virtual para a instância do SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Resolução de nomes no Azure e o nome de anfitrião virtual de cluster

A plataforma de nuvem do Azure não oferece a opção para configurar endereços IP virtuais, tais como endereços IP flutuante. É necessário uma solução alternativa para configurar um endereço IP virtual para alcançar o recurso de cluster na nuvem. 

O serviço de Balanceador de carga do Azure fornece um *Balanceador de carga interno* para o Azure. Com o Balanceador de carga interno, os clientes conseguirem contactar o cluster através do endereço IP virtual do cluster. 

Implemente o Balanceador de carga interno no grupo de recursos que contém nós de cluster. Em seguida, configure todas as portas necessárias reencaminhamento regras utilizando a pesquisa de portas do Balanceador de carga interno. Os clientes podem ligar através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster. O Balanceador de carga interno processa o reencaminhamento de porta para o nó ativo do cluster.

![Figura 1: Clustering de ativação pós-falha do Windows Server configuração no Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 1:** configuração no Azure sem um disco partilhado de cluster de ativação pós-falha do Windows Server_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA com partilha de ficheiros

SAP desenvolvidas uma nova abordagem e uma alternativa ao cluster discos partilhados, de clustering de uma instância do SAP ASCS/SCS num cluster de ativação pós-falha do Windows. Em vez de utilizar discos partilhados de cluster, pode utilizar uma partilha de ficheiros SMB a implementação de SAP anfitrião global ficheiros.

> [!NOTE]
> Partilha de ficheiros SMB é uma alternativa à utilização de discos de cluster partilhado para clustering de instâncias de SAP ASCS/SCS.  
>

Esta arquitetura é específica das seguintes formas:

* SAP serviços centrais (com processos de estrutura e a mensagem e colocar os seus próprios ficheiros) são separados dos ficheiros de anfitrião global do SAP.
* Serviços de centrais de SAP ser executados com uma instância do SAP ASCS/SCS.
* Instância do SAP ASCS/SCS está em cluster e está acessível utilizando a \<nome de anfitrião virtual ASCS/SCS\> nome de anfitrião virtual.
* Ficheiros global SAP são colocados numa partilha de ficheiros SMB e são acedidos utilizando o \<anfitrião global SAP\> nome de anfitrião: \\ \\ &lt;anfitrião global SAP&gt;\sapmnt\\ &lt;SID&gt;\SYS\....
* A instância do SAP ASCS/SCS está instalada num disco local em ambos os nós de cluster.
* O \<nome de anfitrião virtual ASCS/SCS\> nome de rede é diferente do &lt;anfitrião global SAP&gt;.

![Figura 2: SAP ASCS/SCS HA arquitetura com partilha de ficheiros SMB][sap-ha-guide-figure-8004]

_**Figura 2:** nova SAP ASCS/SCS HA arquitetura com uma partilha de ficheiros SMB_

Pré-requisitos para uma partilha de ficheiros SMB:

* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir acesso ao Active Directory listas de controlo (ACLs) para grupos de utilizadores do Active Directory e o `computer$` objeto de computador.
* A partilha de ficheiros tem de ser ativada para HA:
    * Os discos utilizados para armazenar os ficheiros não pode ser um ponto único de falha.
    * Servidor ou o período de indisponibilidade VM não irá causar período de indisponibilidade na partilha de ficheiros.

O SAP \<SID\> função de cluster não contém discos partilhados de cluster ou um recurso de cluster de partilha de ficheiro genérico.


![Figura 3: SAP \<SID\> recursos de função para a utilização de uma partilha de ficheiros do cluster][sap-ha-guide-figure-8005]

_**Figura 3:** SAP &lt;SID&gt; recursos de função para a utilização de uma partilha de ficheiros do cluster_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Partilhas de ficheiros de escalamento horizontal com espaços de armazenamento direto no Azure como uma partilha de ficheiros SAPMNT

Pode utilizar uma partilha de ficheiros escalável para alojar e proteger os ficheiros do anfitrião global de SAP. Uma partilha de ficheiros de escalamento horizontal também oferece um serviço de partilha de ficheiros SAPMNT altamente disponível.

![Figura 4: Partilha de ficheiros de escalamento horizontal utilizada para proteger ficheiros do anfitrião global de SAP][sap-ha-guide-figure-8006]

_**Figura 4:** uma partilha de ficheiros de escalamento horizontal utilizada para proteger ficheiros do anfitrião global de SAP_

> [!IMPORTANT]
> Partilhas de ficheiros de escalamento horizontal são totalmente suportadas na nuvem do Microsoft Azure e, em ambientes no local.
>

Uma partilha de ficheiros de escalamento horizontal oferece uma partilha de ficheiros altamente disponível e escalável horizontalmente SAPMNT.

Espaços de armazenamento direto é utilizado como um disco partilhado para uma partilha de ficheiros de escalamento horizontal. Pode utilizar espaços de armazenamento direto para criar o armazenamento de elevada disponibilidade e dimensionável através de servidores com o armazenamento local. Armazenamento que é utilizado para uma partilha de ficheiros de escalamento horizontal partilhado, como para os ficheiros do anfitrião global de SAP, não é um ponto único de falha.

> [!IMPORTANT]
>Se lhe *não* plano para configurar a recuperação após desastre, recomendamos que utilize uma partilha de ficheiros de escalamento horizontal como uma solução para uma partilha de ficheiros altamente disponíveis no Azure.
>

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Pré-requisitos SAP para partilhas de ficheiros de escalamento horizontal no Azure

Para utilizar uma partilha de ficheiros de escalamento horizontal, o sistema tem de cumprir os seguintes requisitos:

* Pelo menos dois nós para uma partilha de ficheiros de escalamento horizontal do cluster.
* Cada nó tem de ter, pelo menos, dois discos locais.
* Motivo de desempenho, tem de utilizar *espelhamento resiliência*:
    * Bidirecional espelhamento para uma partilha de ficheiros de escalamento horizontal com dois nós de cluster.
    * Três vias espelhamento para uma partilha de ficheiros de escalamento horizontal connosco de cluster de três (ou mais).
* Recomendamos três (ou mais) nós de cluster para uma partilha de ficheiros de escalamento horizontal, com o espelhamento de três vias.
    Esta configuração oferece mais escalabilidade e resiliência de armazenamento mais que o programa de configuração de partilha de ficheiros de escalamento horizontal com dois nós de cluster e para espelhamento de duas vias.
* Tem de utilizar discos Premium do Azure.
* Recomendamos que utilize discos gerida do Azure.
* Recomendamos formatar volumes utilizando o sistema de ficheiros Resiliente (ReFS).
    * Para obter mais informações, consulte [SAP nota 1869038 - suporte SAP para o sistema de ficheiros ReFs] [ 1869038] e [escolher o sistema de ficheiros] [ planning-volumes-s2d-choosing-filesystem] capítulo do volumes de planeamento do artigo de espaços de armazenamento direto.
    * Certifique-se de que instala [Microsoft KB4025334 a atualização cumulativa][kb4025334].
* Pode utilizar os tamanhos de VM de Azure de série Dsv2 ou série DS.
* Para um desempenho de rede eficiente entre VMs, que é necessário para a sincronização direta de espaços de armazenamento do disco, utilize um tipo VM que tenha, pelo menos, uma largura de banda de rede "superior".
    Para obter mais informações, consulte o [série Dsv2] [ dv2-series] e [-série DS] [ ds-series] especificações.
* Recomendamos que reservar alguns capacidade não alocada no agrupamento de armazenamento. Abandonar o fileparser alguns capacidade não alocada no agrupamento de armazenamento fornece o espaço de volumes para reparar "no local" se falhar de uma unidade. Isto melhora a segurança de dados e o desempenho.  Para obter mais informações, consulte [escolher o tamanho do volume][choosing-the-size-of-volumes-s2d].
* Partilha de ficheiros de escalamento horizontal VMs do Azure tem de ser implementada no seu próprio conjunto de disponibilidade do Azure.
* Não precisa de configurar o Balanceador de carga interno do Azure para o nome de rede de partilha de ficheiros de escalamento horizontal, tal como para \<anfitrião global SAP\>. Este procedimento é efetuado o \<nome de anfitrião virtual ASCS/SCS\> da instância do SAP ASCS/SCS ou para o DBMS. Uma partilha de ficheiros de escalamento horizontal aumenta horizontalmente de forma a carga em todos os nós de cluster. \<Anfitrião global SAP\> utiliza o endereço IP local para todos os nós do cluster.


> [!IMPORTANT]
> Não é possível mudar o nome da partilha de ficheiros SAPMNT, que aponta para \<anfitrião global SAP\>. SAP suporta apenas a partilha de nome "sapmnt."

> Para obter mais informações, consulte [a nota 2492395 - pode ser alterada a sapmnt de nome de partilha?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Configure o SAP ASCS/instâncias SCS e ficheiros de escalamento horizontal partilham em dois clusters

Pode implementar instâncias de SAP ASCS/SCS um cluster, com os seus próprios SAP \<SID\> função de cluster. Neste caso, pode configurar a partilha de ficheiros de escalamento horizontal no outro cluster, com uma outra função de cluster.

> [!IMPORTANT]
>Neste cenário, a instância do SAP ASCS/SCS está configurada para aceder ao anfitrião SAP global utilizando o caminho UNC \\ \\ &lt;anfitrião global SAP&gt;\sapmnt\\&lt;SID&gt;\SYS\.
>

![Figura 5: Instância SAP ASCS/SCS e uma partilha de ficheiros de escalamento horizontal implementado em dois clusters][sap-ha-guide-figure-8007]

_**Figura 5:** implementada em dois clusters de partilha de ficheiros de uma instância do SAP ASCS/SCS e Escalamento horizontal_

> [!IMPORTANT]
> Na nuvem do Azure, defina cada cluster que é utilizado para SAP e ficheiros de escalamento horizontal partilhas tem de ser implementadas na sua própria disponibilidade do Azure. Isto garante distribuída colocação do cluster VMs em toda a infraestrutura do Azure subjacente.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Partilha de ficheiros genérico com SIOS DataKeeper como discos partilhados de cluster


> [!IMPORTANT]
> Recomendamos uma solução de partilha de ficheiros de escalamento horizontal para uma partilha de ficheiros altamente disponíveis.
>
> Se pretender também definir recuperação após desastre para a partilha de ficheiros altamente disponíveis, tem de utilizar uma partilha de ficheiro genérico e SISO DataKeeper para os discos partilhados de cluster.
>

Uma partilha de ficheiros genérico é outra opção para alcançar uma partilha de ficheiros altamente disponíveis.

Neste caso, pode utilizar uma solução SIOS de terceiros como um disco partilhado de cluster.

## <a name="next-steps"></a>Passos seguintes

* [Preparar a infraestrutura do Azure para HA do SAP através de uma partilha de ficheiros e de cluster de ativação pós-falha do Windows para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalar SAP NetWeaver HA uma partilha de ficheiros e de cluster de ativação pós-falha de Windows para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Implementar um servidor de ficheiros de escalamento horizontal espaços de armazenamento direto dois nós de armazenamento UPD no Azure][deploy-sofs-s2d-in-azure]
* [Espaços de armazenamento diretos no Windows Server 2016][s2d-in-win-2016]
* [Descrição detalhada da: Volumes em espaços de armazenamento direto][deep-dive-volumes-in-s2d]
