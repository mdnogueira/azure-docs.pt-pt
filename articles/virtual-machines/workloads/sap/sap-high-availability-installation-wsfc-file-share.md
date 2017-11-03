---
title: "SAP NetWeaver HA instalação no Cluster de ativação pós-falha do Windows e do ficheiro partilhar para SAP (A) instância SCS no Azure | Microsoft Docs"
description: "SAP NetWeaver HA instalação no Cluster de ativação pós-falha do Windows e a partilha de ficheiros para a instância SCS SAP (A)"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec7888cfb9b0d288b5b25f580c852ee32306684c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-file-share-for-sap-ascs-instance-on-azure"></a>SAP NetWeaver HA instalação no Cluster de ativação pós-falha do Windows e a partilha de ficheiros para a instância SCS SAP (A) no Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

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

Este documento é que descrevem como instalar e configurar elevado sistema SAP disponível no Azure, com **Cluster de ativação pós-falha do Windows (WSFC)** e **escala saída partilha de ficheiros** como uma opção para clustering SAP (A) SCS instância.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que revê estes documentos antes de iniciar a instalação:

* [Guia de arquitetura - Clustering SAP (A) instância SCS no **Cluster de ativação pós-falha do Windows** utilizando **partilha de ficheiros**][sap-high-availability-guide-wsfc-file-share]

* [Preparação de infraestrutura do Azure para SAP HA utilizando **Cluster de ativação pós-falha do Windows** e **ficheiro partilhado** para a instância de SCS SAP (A)][sap-high-availability-infrastructure-wsfc-file-share]


Terá de seguir executáveis / dll a partir do SAP:
* SAP **Gestor de aprovisionamento de Software** (**SWPM**) versão da ferramenta de instalação **SPS21 (ou superior)**.
* Transferir o **NTCLUST mais recente. Kong** arquivo com recurso de cluster do novo SAP DLL. O novo cluster dlls de SAP suporta elevada disponibilidade SCS SAP (A) com partilha de ficheiros no Cluster de ativação pós-falha do Windows Server.

  Para mais formation no recurso de cluster do novo SAP DLL, consulte este blogue: [DLL de recursos do cluster de novo SAP está disponível!][sap-blog-new-sap-cluster-resource-dll]

Não podemos descrevem a configuração DBMS porque setups variam consoante o sistema DBMS que utiliza. No entanto, partimos do princípio que preocupações de elevada disponibilidade com o DBMS são resolvidas com as funcionalidades de que suportam os fornecedores DBMS diferentes para o Azure. Por exemplo, Always On ou espelhamento de base de dados para o SQL Server e Oracle Data Guard para bases de dados Oracle. No cenário que utilizamos neste artigo, vamos não adicionam mais proteção para o DBMS.

Não existem não existem considerações especiais quando os diferentes serviços DBMS interagem com este tipo de configuração em cluster do SAP ASCS/SCS no Azure.

> [!NOTE]
> Os procedimentos de instalação de sistemas do SAP NetWeaver ABAP, sistemas de Java e sistemas ABAP + Java são quase idênticos. A diferença mais significativa é que um sistema de SAP ABAP tem uma instância ASCS. O sistema de SAP Java tem uma instância SCS. O sistema SAP ABAP + Java tem uma instância ASCS e um SCS instância em execução no mesmo grupo de cluster de ativação pós-falha de Microsoft. Todas as diferenças de instalação para cada pilha de instalação do SAP NetWeaver explicitamente são mencionadas. Pode assumir que todas as outras partes são os mesmos.  
>
>

## <a name="install-ascs-instance-on-ascs-cluster"></a>Instalar instância (A) SCS (A) Cluster SCS

> [!IMPORTANT]
>
>Atualmente, uma definição de HA com uma partilha de ficheiros não é suportada pela ferramenta de instalação do SAP Gestor de aprovisionamento de Software (SWPM). Por conseguinte, algumas adoção manual é necessária para instalar um sistema SAP, por exemplo, para instalar e instância SCS SAP (A) do cluster e configurar GLOBALHOST de SAP separado.  
>
>Não há nenhuma alteração nos outros passos de instalação para instalar (e cluster) instância DBMS e servidores de aplicações SAP.
>

### <a name="install-ascs-instance-on-local-drive"></a>Instalar instância (A) SCS na unidade Local

Instalação SAP (A) SCS instância no **ambos** nós de cluster (A) SCS. Instalá-lo no **local** unidade. No nosso exemplo, escolhemos unidade local é c:\\. Pode escolher qualquer outra unidade local.  

Para instalar navegue na instalação do SAP ferramenta SWPM para:

&lt;Produto&gt; -> &lt;DBMS&gt; -> instalação -> aplicação de servidor ABAP (ou Java) distribuído-> sistema--> (A) instância SCS

> [!IMPORTANT]
>Atualmente, cenário de partilha de ficheiros ainda não é suportado pela ferramenta de instalação do SAP SWPM **não é possível utilizar** caminho de instalação:
>
>&lt;Produto&gt; -> &lt;DBMS&gt; -> instalação -> aplicação de servidor ABAP (ou Java) -> elevada disponibilidade sistema ->...
>

### <a name="remove-sapmnt-and-create-saploc-file-share"></a>Remover SAPMNT e criar a partilha de ficheiros SAPLOC

SWMP criadas partilha local SAPMNT c:\\usr\\pasta sap.

Remova a partilha de ficheiros SAPMNT em **ambos** (A) SCS os nós do cluster:

Execute o seguinte script do PowerShell:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Se a partilha SAPLOC não existir, crie uma em nós de cluster ASCS ambos.

Execute o seguinte script do PowerShell:

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-sap-global-host-on-sofs-cluster"></a>Preparar o anfitrião GLOBAL do SAP no SOFS Cluster

Neste passo, crie a partilha de ficheiros e de volume seguinte, no SOFS cluster:

* O ficheiro de SAP GLOBALHOST C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS\ estrutura no SOFS cluster partilhado (CSV) de volume

* Criar partilha de ficheiros SAPMNT

* Definir a segurança na partilha de ficheiros SAPMNT e pastas com controlo total para:
    * **&lt;DOMÍNIO&gt;\SAP_&lt;SID&gt;_GlobalAdmin** grupo de utilizadores
    * SAP (A) computador de nós de SCS Cluster **objetos &lt;domínio&gt;\ClusterNode1$ e &lt;domínio&gt;\ClusterNode2$**

Para criar o volume CSV com resiliência espelhada, tal como definido no capítulo **SAP pré-requisitos para SOFS no Azure - adicionar ligação**, executar o seguinte cmdlet do PowerShell num de nós do cluster SOFS:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Para criar SAPMNT e definir a segurança da pasta e partilha, execute o seguinte script do PowerShell num de nós do cluster SOFS:

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascs-instances-and-sap-services"></a>Instâncias SCS paragem (A) e serviços SAP

Execute os seguintes passos:
* Parar instâncias SCS SAP (A) em ambos os nós de cluster (A) SCS
* Parar serviços do Windows do SAP (A) SCS **SAP&lt;SID&gt;_&lt;InstanceNumber&gt;**  em ambos os nós de cluster

## <a name="move-sys-folder-to-sofs-cluster"></a>Mover \SYS\.... Pasta SOFS cluster

Execute os seguintes passos:
* Copiar a pasta SYS (por exemplo, C:\usr\sap\\&lt;SID&gt;\SYS) a partir de um a SCS (A) os nós do cluster para cluster SOFS, por exemplo, para C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS
* Eliminar C:\usr\sap\\&lt;SID&gt;\SYS pasta a partir de ambos os nós de cluster (A) SCS

## <a name="update-cluster-security-setting-on-sap-ascs-cluster"></a>Atualizar a definição de segurança de Cluster no Cluster SCS SAP (A)

Execute o seguinte script do PowerShell num de nós do cluster SCS SAP (A):

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for <DOMAIn>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascs-instance"></a>Criar um nome de anfitrião Virtual para a instância SCS SAP em cluster (A)

Conforme descrito em capítulo [criar um nome de anfitrião virtual para a instância em cluster do SAP ASCS/SCS] [ sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host] , criar por exemplo, o nome de rede do SCS SAP (A) cluster **pr1-ascs [10.0.6.7]**

## <a name="update-default-and-sap-ascs-instance-profile"></a>Atualizar predefinido e SAP (A) perfil de instância SCS

Terá de atualizar a predefinição e perfil de instância do SAP (A) SCS &lt;SID&gt;_(A) SCS<Nr>_  <Host> a utilizar:

* Novo nome de anfitrião virtual SCS SAP (A)

* Novo nome de anfitrião GLOBAL SAP


| Valores antigos |  |
| --- | --- |
| SAP nome de anfitrião (A) SCS = anfitrião Global SAP | ASCs-1 |
| SAP nome de perfil (A) SCS instância | PR1_ASCS00_ascs-1 |

| Novos valores |  |
| --- | --- |
| SAP nome de anfitrião (A) SCS | **PR1 ascs** |
| Anfitrião Global SAP | **sapglobal** |
| SAP nome de perfil (A) SCS instância | PR1\_ASCS00\_**pr1 ascs** |

### <a name="update-sap-default-profile"></a>Atualizar perfil predefinido de SAP


| Nome do Parâmetro | Valor do parâmetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **PR1 ascs** |
| colocar/serverhost | **PR1 ascs** |

### <a name="update-sap-ascs-instance-profile"></a>Atualizar perfil de instância do SCS SAP (A)

| Nome do Parâmetro | Valor do parâmetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\\**sapglobal**\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE) \PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Iniciar**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **PR1 ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Iniciar**_Program_03 = local$(_EN) pf=$(_PF) |
| GW/netstat_once | **0** |
| colocar/encni/set_so_keepalive  | **VERDADEIRO** |
| serviço/ha_check_node | **1** |

> [!IMPORTANT]
>Pode utilizar **atualização SAPASCSSCSProfile** cmdlet do PowerShell para automatizar a atualização de perfil
>
>Cmdlet do PowerShell suporta instância SAP ABAP ASCS e SAP SCS de Java.
>

Cópia **SAPScripts.ps1** ao local de uma unidade C:\tmp e execute o seguinte cmdlet do PowerShell:

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Figura 1: SAPScripts.ps1 saída][sap-ha-guide-figure-8012]

_**Figura 1:** SAPScripts.ps1 saída_

## <a name="update-ltsidgtadm-user-environment-variable"></a>Atualização &lt;sid&gt;adm variável de ambiente do utilizador

Atualização &lt;sid&gt;adm utilizador ambiente novo GLOBALHOST caminho UNC em nós de cluster do SCS ambos (A).
Inicie sessão como &lt;sid&gt;ferramenta de Regedit.exe de utilizador e o início do adm.
Aceda a **HKEY_CURRENT_USER** -> **ambiente** e Atualize as variáveis para o novo valor:

| Variável | Valor |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **PR1 ascs** |


## <a name="install-new-saprcdll"></a>Instale SAPRC novo. DLL

Tem de instalar uma nova versão do recurso de cluster SAP que suporta o cenário de partilha de ficheiros.

Transferir os mais recentes **NTCLUST. Kong** pacote do SAP serviço Marketplace.

Descompacte NTCLUS. Kong dos SCS (A) os nós do cluster e execute os seguintes comandos da linha de comandos para instalar o novo saprc.dll:

```
.\NTCLUST\insaprct.exe -yes -install
```

O novo saprc.dll será instalado em ambos os nós de cluster (A) SCS.

Para obter mais informações, consulte [1596496 de nota SAP - como atualizar DLLs de tipo de recurso do SAP para o Monitor de recursos de Cluster][1596496].

## <a name="create-sap-sid-cluster-group-network-name-and-ip"></a>Criar SAP <SID> grupo de Cluster, o nome de rede e o IP

Tem de criar:

* SAP &lt;SID&gt; grupo de cluster
* < (a) SCSNetworkName >
* e o endereço IP correspondente

Execute o seguinte cmdlet do PowerShell:

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create SAP ASCS instance Virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create SAP ASCS Virtual IP Address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set static IP Address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set Network DNS Name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start SAP <SID> Cluster Group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-sap-start-service-on-both-nodes"></a>Registar o serviço de início de SAP em ambos os nós

Terá de voltar a registar serviço sapstart SAP (A) SCS pint para o novo perfil e o caminho de perfil.

Tem de executar a em nós de cluster do SCS ambos (A).

Formulário de execução de comandos elevada pedido seguinte comando:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Figura 2: Reinstale o serviço SAP][sap-ha-guide-figure-8013]

_**Figura 2:** reinstale SAP serviço_

Certifica-se de que os parâmetros estão corretos e escolham **Manual** como tipo de arranque.

## <a name="stop-ascs-service"></a>Parar o serviço (A) SCS

Parar o serviço de SAP (A) SCS **SAP&lt;SID&gt;_ &lt;InstanceNumber&gt;**  no (A) SCS ambos nós do cluster.

## <a name="create-new-sap-service-and-sap-instance-resources"></a>Criar novo serviço SAP e a instância do SAP recursos

Agora tem finalizar a criação de recursos do SAP SAP&lt;SID&gt; grupo de cluster, por exemplo, terá de criar recursos:

* **SAP &lt;SID&gt; &lt;InstanceNumber&gt; serviço** e
* **SAP &lt;SID&gt; &lt;InstanceNumber&gt; instância**

Execute o seguinte cmdlet do PowerShell:

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP Instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP Instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Adicionar uma porta de pesquisa

Neste passo, está a configurar um recurso de cluster do SAP porta da sonda SAP-SID-IP utilizando o PowerShell. Executar esta configuração num de nós de cluster SAP ASCS/SCS, conforme descrito [aqui][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-ers-instance-on-both-cluster-nodes"></a>Instalar instância ERS em ambos os nós de Cluster

No próximo passo, tem de instalar a instância ERS (colocar em fila de replicação servidor) em ambos os nós de (A) SCS cluster.
A opção de instalação pode ser encontrada no menu SWPM:

&lt;Produto&gt; -> &lt;DBMS&gt; -> instalação -> sistema SAP adicionais instâncias -> **instância de servidor de replicação de colocar em fila**

## <a name="install-dbms-instance-and-sap-application-servers"></a>Instalar instância DBMS e servidores de aplicações do SAP

Finalize a instalação de sistema SAP através da instalação:
* Instância DBMS
* Servidor de aplicações SAP primário
* Servidor de aplicações SAP adicional

## <a name="next-steps"></a>Passos Seguintes

* [Instalação de uma instância SCS (A) no Cluster de ativação pós-falha sem qualquer partilhado discos - SAP oficial diretrizes para partilha de ficheiros do HA][sap-official-ha-file-share-document]:

* [Espaços de armazenamento diretos no Windows Server 2016][s2d-in-win-2016]

* [Servidor de ficheiros de escalamento horizontal para descrição geral de dados de aplicação][sofs-overview]

* [Novidades do armazenamento no Windows Server 2016][new-in-win-2016-storage]
