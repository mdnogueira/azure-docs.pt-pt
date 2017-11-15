---
title: "Preparar a infraestrutura do Azure para SAP HA através de um cluster de ativação pós-falha do Windows e o disco partilhado para SAP ASCS/SCS | Microsoft Docs"
description: "Saiba como preparar a infraestrutura do Azure para HA do SAP através da utilização de um cluster de ativação pós-falha do Windows e o disco partilhado para uma instância do SAP ASCS/SCS."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2547d40ac39bc8188511f6682911fa302cf3e8a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Preparar a infraestrutura do Azure para HA do SAP através da utilização de um cluster de ativação pós-falha do Windows e o disco partilhado para SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Este artigo descreve os passos que efetuar para preparar a infraestrutura do Azure para instalar e configurar um sistema SAP de elevada disponibilidade num cluster de ativação pós-falha do Windows utilizando um *disco partilhado de cluster* como uma opção para clustering um Instância do SAP ASCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, consulte este artigo:

* [Guia de arquitetura: uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura da arquitetura de modelo de 1
Modelos Azure Resource Manager para SAP ajudam a simplificar a implementação dos recursos necessários.

Os modelos de três camadas no Gestor de recursos do Azure também suportam cenários de elevada disponibilidade. Por exemplo, 1 da arquitetura do modelo tem dois clusters. Cada cluster é um ponto de falha para SAP ASCS/SCS e DBMS SAP único.

Aqui é onde pode obter os modelos Azure Resource Manager para o cenário de exemplo que dita neste artigo:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Marketplace do Azure ao utilizar discos gerida do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada utilizando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura da arquitetura de modelo de 1:

- No portal do Azure, no **parâmetros** painel, no **SYSTEMAVAILABILITY** caixa, selecione **HA**.

  ![Figura 1: Definir os parâmetros do SAP elevada disponibilidade do Azure Resource Manager][sap-ha-guide-figure-3000]

_**Figura 1:** definir os parâmetros do SAP elevada disponibilidade do Azure Resource Manager_


  Criam os modelos:

  * **Máquinas virtuais**:
    * Máquinas de virtuais do servidor de aplicações SAP: \<SAPSystemSID\>- di -\<número\>
    * Máquinas virtuais de cluster ASCS/SCS: \<SAPSystemSID\>- ascs-\<número\>
    * DBMS cluster: \<SAPSystemSID\>- db-\<número\>

  * **Rede de cartões de todas as máquinas virtuais, com os endereços IP associados**:
    * \<SAPSystemSID\>- nic-di -\<número\>
    * \<SAPSystemSID\>- nic-ascs -\<número\>
    * \<SAPSystemSID\>- nic-db -\<número\>

  * **Contas de armazenamento do Azure (apenas discos não geridos)**:

  * **Grupos de disponibilidade** para:
    * Máquinas de virtuais do servidor de aplicações SAP: \<SAPSystemSID\>- avset di
    * Máquinas virtuais de cluster do SAP ASCS/SCS: \<SAPSystemSID\>- avset ascs
    * Máquinas virtuais de cluster DBMS: \<SAPSystemSID\>- avset-db

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para o endereço IP e a instância ASCS/SCS \<SAPSystemSID\>- lb ascs
    * Com todas as portas para o endereço IP e o SQL Server DBMS \<SAPSystemSID\>- lb-db

  * **Grupo de segurança de rede**: \<SAPSystemSID\>- nsg-ascs-0  
    * Com uma porta aberta externos de protocolo RDP (Remote Desktop Protocol) para o \<SAPSystemSID\>0 - ascs máquina

> [!NOTE]
> Todos os endereços IP das placas de rede e Balanceadores de carga internos do Azure são dinâmicos por predefinição. Alterá-los para endereços IP estáticos. Vamos descrever como fazê-lo mais tarde no artigo.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementar máquinas virtuais com conetividade de rede empresarial (em vários locais) para utilizar na produção
Para sistemas de produção SAP, implementar máquinas virtuais do Azure com [conectividade de rede empresarial (em vários locais)] [ planning-guide-2.2] utilizando o Gateway de VPN do Azure ou Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a instância de rede Virtual do Azure. A rede virtual e sub-rede já tiverem sido criados e preparados.
>
>

1.  No portal do Azure, no **parâmetros** painel, no **NEWOREXISTINGSUBNET** caixa, selecione **existente**.
2.  No **SUBNETID** caixa, adicione a cadeia completa do seu ID de sub-rede preparada rede Azure onde planeia implementar as máquinas virtuais do Azure.
3.  Para obter uma lista de todas as sub-redes de rede do Azure, execute este comando do PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  O **ID** campo mostra o valor para o ID de sub-rede.
4. Para obter uma lista de todos os valores de ID de sub-rede, execute este comando do PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  O ID de sub-rede tem o seguinte aspeto:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Implementar instâncias SAP apenas na nuvem para teste e demonstração
Pode implementar o sistema SAP de elevada disponibilidade de um modelo de implementação apenas de nuvem. Este tipo de implementação é principalmente útil para casos de utilização de demonstração e teste. Não é adequada para os casos de utilização de produção.

- No portal do Azure, no **parâmetros** painel, no **NEWOREXISTINGSUBNET** caixa, selecione **novo**. Deixe o **SUBNETID** campo vazio.

  O modelo de SAP Azure Resource Manager cria automaticamente a rede virtual do Azure e a sub-rede.

> [!NOTE]
> Também terá de implementar, pelo menos, uma máquina virtual dedicada para o serviço do Active Directory e DNS na mesma instância de rede Virtual do Azure. O modelo não crie estas máquinas virtuais.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura da arquitetura de modelo de 2

Pode utilizar este modelo Azure Resource Manager para SAP para o ajudar a simplificar a implementação de recursos de infraestrutura necessária para SAP da arquitetura modelo 2.

Aqui é onde pode obter os modelos Azure Resource Manager para este cenário de implementação:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Marketplace do Azure ao utilizar discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada utilizando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura da arquitetura de modelo de 3

Pode preparar a infraestrutura e configurar o SAP para o SID de várias. Por exemplo, pode adicionar uma instância adicional do SAP ASCS/SCS para um *existente* configuração de cluster. Para obter mais informações, consulte [configurar uma instância adicional do SAP ASCS/SCS para uma configuração de cluster existente criar uma configuração de várias SID SAP no Gestor de recursos do Azure][sap-ha-multi-sid-guide].

Se pretender criar um novo cluster de múltiplos SID, pode utilizar o SID de várias [modelos de início rápido no GitHub](https://github.com/Azure/azure-quickstart-templates).

Para criar um novo cluster de múltiplos SID, tem de implementar os seguintes três modelos:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de servidores de aplicações](#application-servers-template)

As secções seguintes tem mais detalhes sobre os parâmetros que tem de fornecer nos modelos e modelos.

### <a name="ASCS-SCS-template"></a>Modelo ASCS/SCS

O modelo ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de ativação pós-falha do Windows Server que aloja várias instâncias ASCS/SCS.

Para configurar o modelo de várias SID ASCS/SCS, além de [modelo de várias SID ASCS/SCS] [ sap-templates-3-tier-multisid-xscs-marketplace-image] ou [modelo de várias SID ASCS/SCS utilizando discos geridos] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], introduza valores para os seguintes parâmetros:

  - **Prefixo de recurso**: definir o prefixo de recursos, o que é utilizado para o prefixo todos os recursos que são criados durante a implementação. Porque os recursos não pertence ao sistema SAP apenas um, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo tem de ser entre três e seis carateres.
  - **Tipo de pilha**: selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Balanceador de carga do Azure tem um (ABAP ou apenas Java) ou dois (ABAP + Java) endereços IP privados por sistema SAP.
  -  **Tipo de SO**: selecione o sistema operativo das máquinas virtuais.
  -  **Contagem de sistema do SAP**: selecione o número de sistemas SAP que pretende instalar neste cluster.
  -  **Disponibilidade do sistema**: selecione **HA**.
  -  **Nome de utilizador de administrador e a palavra-passe de administrador**: criar um novo utilizador que pode ser utilizado para iniciar sessão máquina.
  -  **Nova ou existente sub-rede**: defina se pretende criar uma nova rede virtual e uma sub-rede ou utilizar uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existente**.
  -  **Id de sub-rede**: definir o ID de sub-rede para o qual as máquinas virtuais devem estar ligadas. Selecione a sub-rede da rede virtual VPN ou ExpressRoute para ligar a máquina virtual à sua rede no local. O ID, normalmente, este aspeto:

   /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}\<id de subscrição\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual\>/subnets/ \<nome de sub-rede\>

O modelo implementa uma instância de Balanceador de carga do Azure, que suporta vários sistemas SAP:

- As instâncias ASCS estão configuradas para o número da instância 00, 10, 20...
- As instâncias do SCS estão configuradas para o número da instância 01, 11, 21...
- As instâncias de servidor de replicação de colocar em fila do ASCS (ERS) (apenas Linux) estão configuradas para o número da instância 02, 12, 22...
- As instâncias do SCS ERS (apenas Linux) estão configuradas para o número da instância 03, 13, 23...

O Balanceador de carga contém 1 VIP(s) (2 para Linux), 1 x VIP para ASCS/SCS e 1 x VIP para ERS (apenas Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Portas de SAP ASCS/SCS
A lista seguinte contém todas as regras de (em que x é o número de sistema de SAP, por exemplo, 1, 2, 3...) de balanceamento de carga:
- Portas específicas do Windows para cada sistema SAP: 445, 5985
- Portas ASCS (número de instância x0): 32 x 0, 36 x 0, -39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Portas SCS (número de instância x1): 32 x 1 33 x 1, 39 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASCS ERS portas no Linux (número de instância x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS ERS portas no Linux (número de instância x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

O Balanceador de carga está configurado para utilizar as seguintes portas de pesquisa (em que x é o número de sistema de SAP, por exemplo, 1, 2, 3...):
- Porta de sonda do Balanceador de carga internos ASCS/SCS: 620 x 0
- Porta da sonda (apenas Linux) do Balanceador de carga ERS internos: 621 x 2

### <a name="database-template"></a>Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão de base de dados relacional (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para sistemas SAP cinco, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de várias SID de base de dados, além de [modelo várias SID de base de dados] [ sap-templates-3-tier-multisid-db-marketplace-image] ou [modelo de várias SID de base de dados ao utilizar discos geridos] [ sap-templates-3-tier-multisid-db-marketplace-image-md], introduza valores para os seguintes parâmetros:

  -  **Id de sistema de SAP**: introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
  -  **Tipo de SO**: selecione o sistema operativo das máquinas virtuais.
  -  **DbType**: selecione o tipo de base de dados que pretende instalar no cluster. Selecione **SQL** se pretender instalar o Microsoft SQL Server. Selecione **HANA** se planear instalar SAP HANA nas máquinas virtuais. Certifique-se de que seleciona o tipo de sistema operativo correto. Selecione **Windows** para SQL Server, selecione uma distribuição Linux para HANA. Balanceador de carga do Azure que está ligada às máquinas virtuais está configurado para suportar o tipo de base de dados selecionada:
    * **SQL Server**: A porta de balanceamento de carga do Balanceador de carga 1433. Certifique-se de que utiliza esta porta para a sua configuração de AlwaysOn do SQL Server.
    * **HANA**: portas de balanceamento de carga do Balanceador de carga 35015 e 35017. Certifique-se de que instala o SAP HANA com o número da instância **50**.
    O Balanceador de carga utiliza a porta da sonda 62550.
  -  **Tamanho do sistema de SAP**: definir o número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistema.
  -  **Disponibilidade do sistema**: selecione **HA**.
  -  **Nome de utilizador de administrador e a palavra-passe de administrador**: criar um novo utilizador que pode ser utilizado para iniciar sessão máquina.
  -  **Id de sub-rede**: introduza o ID de sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID de sub-rede que foi criado como parte da implementação do modelo ASCS/SCS.

### <a name="application-servers-template"></a>Modelo de servidores de aplicações

O modelo de servidores de aplicações implementa dois ou mais máquinas virtuais que podem ser utilizadas como instâncias de servidor de aplicações do SAP para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para sistemas SAP cinco, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de várias SID de servidores de aplicações, além de [modelo de SID de múltiplos servidores de aplicações] [ sap-templates-3-tier-multisid-apps-marketplace-image] ou [modelo várias SID de servidores de aplicações ao utilizar discos geridos] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], introduza valores para os seguintes parâmetros:

  -  **Id de sistema de SAP**: introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
  -  **Tipo de SO**: selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema de SAP**: O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistema.
  -  **Disponibilidade do sistema**: selecione **HA**.
  -  **Nome de utilizador de administrador e a palavra-passe de administrador**: criar um novo utilizador que pode ser utilizado para iniciar sessão máquina.
  -  **Id de sub-rede**: introduza o ID de sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID de sub-rede que foi criado como parte da implementação do modelo ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Rede Virtual do Azure
No nosso exemplo, o espaço de endereços da instância Virtual Network do Azure é 10.0.0.0/16. Há uma sub-rede denominada sub-rede, com um intervalo de endereços de 10.0.0.0/24. Todos os balanceadores de carga internos e máquinas virtuais são implementados nesta rede virtual.

> [!IMPORTANT]
> Não efetue as alterações às definições de rede dentro do sistema operativo convidado. Isto inclui os endereços IP, servidores DNS e sub-rede. Configure as definições de rede no Azure. O serviço de protocolo de configuração dinâmica de anfitrião (DHCP) propaga as suas definições.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Endereços IP de DNS

Para definir o IP de DNS necessária endereços, conclua os seguintes passos:

1.  No portal do Azure, no **servidores DNS** painel, certifique-se de que a rede virtual **servidores DNS** opção estiver definida como **DNS personalizado**.
2.  Selecione as definições com base no tipo de rede que tiver. Para obter mais informações, consulte os seguintes recursos:
    * [Conectividade de rede empresarial (em vários locais)][planning-guide-2.2]: adicionar os endereços IP dos servidores DNS no local.  
    Pode expandir a servidores DNS no local para as máquinas virtuais que estão em execução no Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais do Azure em que executa o serviço DNS.
    * [Implementação apenas de nuvem][planning-guide-2.1]: implementar uma máquina virtual adicional na mesma instância de rede Virtual que funciona como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que definiu a cópia de segurança para executar o serviço DNS.

    ![Figura 2: Configurar os servidores DNS para a rede Virtual do Azure][sap-ha-guide-figure-3001]

    _**Figura 2:** configurar DNS servidores para a rede Virtual do Azure_

  > [!NOTE]
  > Se alterar os endereços IP dos servidores DNS, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração e propaguem os novos servidores DNS.
  >
  >

No nosso exemplo, o serviço DNS é instalado e configurado nestas máquinas virtuais do Windows:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor de DNS |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nomes de anfitriões e endereços IP estáticos para a instância em cluster do SAP ASCS/SCS e a instância em cluster DBMS

Para implementação no local, terá destes nomes de anfitrião reservado e endereços IP:

| Função de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| SAP ASCS/SCS primeiro cluster nome de anfitrião virtual (para gestão de cluster) |PR1-ascs-vir |10.0.0.42 |
| Nome de anfitrião virtual de instância do SAP ASCS/SCS |PR1-ascs-sap |10.0.0.43 |
| Nome de anfitrião virtual de cluster segundo do SAP DBMS (gestão de clusters) |PR1-dbms-vir |10.0.0.32 |

Quando criar o cluster, crie o anfitrião virtual nomes pr1-ascs-vir e pr1-dbms-vir e os endereços IP associados que gerem o próprio cluster. Para obter informações sobre como fazê-lo, consulte [recolher nós de cluster numa configuração de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Pode criar manualmente os outros dois nomes de anfitrião virtual, pr1-ascs-sap e pr1-dbms-sap e os endereços IP associados, no servidor DNS. A instância do SAP ASCS/SCS em cluster e a instância em cluster do DBMS utilizar estes recursos. Para obter informações sobre como fazê-lo, consulte [criar um nome de anfitrião virtual para uma instância em cluster do SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Conjunto de endereços IP estáticos para as máquinas virtuais do SAP
Depois de implementar as máquinas virtuais para utilizar no seu cluster, tem de definir os endereços IP estáticos para todas as máquinas virtuais. Opte por fazê-lo na configuração de rede Virtual do Azure e não no sistema operativo convidado.

1.  No portal do Azure, selecione **grupo de recursos** > **placa de rede** > **definições** > **endereço IP**.
2.  No **endereços IP** painel, em **atribuição**, selecione **estático**. No **endereço IP** box, introduza o endereço IP que pretende utilizar.

  > [!NOTE]
  > Se alterar o endereço IP da placa de rede, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração.  
  >
  >

  ![Figura 3: Configurar endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

  _**Figura 3:** conjunto de endereços IP estáticos para a placa de rede de cada máquina virtual_

  Repita este passo para todas as interfaces de rede, que é, todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço do Active Directory ou DNS.

No nosso exemplo, temos destas máquinas virtuais e endereços IP estáticos:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância de servidor de aplicação de SAP |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Segunda instância de servidor de aplicações SAP |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância de servidor de aplicação de SAP |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância ASCS/SCS |PR1-ascs-0 |PR1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância ASCS/SCS |PR1-ascs-1 |PR1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância DBMS |PR1-db-0 |PR1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância DBMS |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Defina um endereço IP estático para o Balanceador de carga interno do Azure

O modelo de SAP Azure Resource Manager cria um balanceador de carga interno do Azure que é utilizado para o cluster de instância do SAP ASCS/SCS e o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual de SAP ASCS/SCS é o mesmo que o endereço IP do Balanceador de carga interno SAP ASCS/SCS: pr1-lb-ascs.
> O endereço IP do nome virtual o DBMS é o mesmo que o endereço IP do Balanceador de carga interno DBMS: pr1-lb-dbms.
>
>

Para definir um endereço IP estático para o Balanceador de carga interno do Azure:

1.  A implementação inicial define o endereço IP do Balanceador de carga interno para **dinâmica**. No portal do Azure, no **endereços IP** painel, em **atribuição**, selecione **estático**.
2.  Definir o endereço IP do Balanceador de carga interno **pr1-lb-ascs** para o endereço IP do nome de anfitrião virtual da instância do SAP ASCS/SCS.
3.  Definir o endereço IP do Balanceador de carga interno **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual da instância DBMS.

  ![Figura 4: Configurar endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Figura 4:** conjunto de endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS_

No nosso exemplo, temos de dois balanceadores de carga internos do Azure que tenham estes endereços IP estáticos:

| Função do Balanceador de carga interno do Azure | Nome do Balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno de instância do SAP ASCS/SCS |PR1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno SAP DBMS |PR1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Regras de Balanceador de carga interno do Azure de balanceamento de carga na ASCS/SCS predefinido

O modelo de SAP Azure Resource Manager cria as portas que tem de:
* Uma instância de ABAP ASCS, com o número de instância predefinido 00
* Uma instância de Java SCS, com o número de instância predefinido 01

Quando instalar a instância do SAP ASCS/SCS, tem de utilizar o número de instância predefinido 00 para a instância de ABAP ASCS e o número de instância predefinido 01 para a instância do SCS de Java.

Em seguida, crie as necessária pontos finais para as portas do SAP NetWeaver de balanceamento de carga interna.

Para criar pontos finais de balanceamento de carga interna necessária, primeiro, crie estes pontos finais para as portas do SAP NetWeaver ABAP ASCS de balanceamento de carga:

| Nome de regra de balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (instância ASCS com o número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Servidor de mensagens ABAP / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Mensagem ABAP interna / *lbrule3900* |39\<InstanceNumber\> |3900 |
| O servidor de mensagens HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP iniciar o serviço ASCS HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP iniciar serviço ASCS HTTPS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Replicação de colocar em fila / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP iniciar o serviço ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP iniciar o serviço ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Gestão remota do Windows (WinRM) *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Tabela 1:** das instâncias do SAP NetWeaver ABAP ASCS os números de porta

Em seguida, crie estes pontos finais para as portas do SAP NetWeaver Java SCS de balanceamento de carga:

| Nome de regra de balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (instância SCS com o número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Servidor de gateway / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Servidor de mensagens de Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| O servidor de mensagens HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP iniciar o serviço SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP iniciar serviço SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Replicação de colocar em fila / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP iniciar o serviço ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP iniciar o serviço ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Tabela 2:** das instâncias do SAP NetWeaver Java SCS os números de porta

![Figura 5: Predefinição ASCS/SCS carregar regras de balanceamento de Balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

_**Figura 5:** regras para o Balanceador de carga interno do Azure de balanceamento de carga de predefinição ASCS/SCS_

Defina o endereço IP da carga balanceador pr1-lb-dbms para o endereço IP do nome de anfitrião virtual da instância DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Alterar a ASCS/SCS predefinido de balanceamento de carga regras para o Balanceador de carga interno do Azure

Se pretender utilizar números diferentes para as instâncias de SAP ASCS ou SCS, tem de alterar os nomes e valores das suas portas de valores predefinidos.

1.  No portal do Azure, selecione  **\<SID\>Balanceador de carga -lb ascs** > **regras de balanceamento de carga**.
2.  Todas as regras que pertencem à instância do SAP ASCS ou SCS de balanceamento de carga, altere estes valores:

  * Nome
  * Porta
  * Porta de back-end

  Por exemplo, se pretender alterar o número de instância ASCS predefinido de 00 e 31, terá de efetuar as alterações para todas as portas listadas na tabela 1.

  Eis um exemplo de uma atualização para a porta *lbrule3200*.

  ![Figura 6: Altere a ASCS/SCS predefinido de balanceamento de carga regras para o Balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

  _**Figura 6:** alterar a ASCS/SCS predefinido de balanceamento de carga regras para o Balanceador de carga interno do Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Adicionar máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático para máquinas virtuais, adicione as máquinas virtuais ao domínio.

![A figura 7: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**A figura 7:** adicionar uma máquina virtual a um domínio_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adicionar entradas de registo em ambos os nós de cluster da instância do SAP ASCS/SCS

Balanceador de carga do Azure tem um balanceador de carga interno que fechar ligações quando as ligações estão Inativas, defina durante um período de tempo (um tempo limite de inatividade). Os processos de trabalho do SAP em instâncias de caixa de diálogo Abrir ligações a colocar em fila de SAP processam assim que o primeiro colocar em fila/anular pedido tem de ser enviadas. Estas ligações normalmente permanecem estabelecidas até que o processo de trabalho ou reinicia o processo de colocar em fila. No entanto, se estiver inativa durante um período de tempo definido, o Balanceador de carga interno do Azure fecha as ligações. Esta operação não é um problema porque o processo de trabalho do SAP reestablishes a ligação para o processo de colocar em fila se já não existe. Estas atividades estão documentadas nos rastreios de Programador de processos SAP, mas que crie uma grande quantidade de conteúdo adicional nesses rastreios. É uma boa ideia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine estas alterações nos parâmetros de TCP/IP com os parâmetros de perfil SAP, descritos mais à frente do artigo.

Para adicionar entradas de registo em ambos os nós de cluster da instância do SAP ASCS/SCS, primeiro, adicione estas entradas de registo do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligar a documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** alterar o primeiro parâmetro de TCP/IP

Em seguida, adicione esta entrada de registo do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligar a documentação |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

**Tabela 4:** alterar o segundo parâmetro de TCP/IP

Para aplicar as alterações, reinicie ambos os nós de cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Configurar um cluster de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS

Configurar um cluster de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS envolve estas tarefas:

- Recolha os nós de cluster numa configuração de cluster.
- Configure um testemunho de partilha de ficheiros do cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Recolher os nós de cluster numa configuração de cluster

1.  No Assistente de funcionalidades e Adicionar função, adicione clustering para ambos os nós de cluster de ativação pós-falha.
2.  Configure o cluster de ativação pós-falha utilizando o Gestor de clusters de ativação pós-falha. No Gestor de clusters de ativação pós-falha, selecione **criar Cluster**e, em seguida, adicione apenas o nome do cluster primeiro (nó D). Não adicione o segundo nó ainda; Adicione o segundo nó num passo posterior.

  ![Figura 8: Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

  _**Figura 8:** adicione o nome do servidor ou máquina virtual do primeiro nó de cluster_

3.  Introduza o nome de rede (nome de anfitrião virtual) do cluster.

  ![Figura 9: Introduza o nome do cluster][sap-ha-guide-figure-3008]

  _**Figura 9:** introduza o nome do cluster_

4.  Depois de criar o cluster, execute um teste de validação de cluster.

  ![Figura 10: Executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

  _**Figura 10:** executar a verificação de validação de cluster_

  Pode ignorar quaisquer avisos sobre discos neste momento no processo. Irá adicionar que um testemunho de partilha de ficheiros e o SIOS discos partilhados mais tarde. Nesta fase, não precisa de preocupar com a ter um quórum.

  ![Figura 11: For encontrado nenhum disco de quórum][sap-ha-guide-figure-3010]

  _**Figura 11:** não for encontrado nenhum disco de quórum_

  ![Figura 12: Um recurso de cluster core necessita de um novo endereço IP][sap-ha-guide-figure-3011]

  _**Figura 12:** um recurso de cluster core necessita de um novo endereço IP_

5.  Altere o endereço IP, o principal de serviço de cluster. O cluster não é possível iniciar até alterar o endereço IP do serviço de cluster núcleos, porque o endereço IP do servidor aponta para um de nós de máquina virtual. Fazê-lo no **propriedades** página do recurso de IP do serviço de cluster core.

  Por exemplo, é necessário atribuir um endereço IP (no nosso exemplo, 10.0.0.42) para o cluster anfitrião virtual nome pr1-ascs-vir.

  ![Figura 13: Na caixa de diálogo de propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

  _**Figura 13:** no **propriedades** diálogo caixa, altere o endereço IP_

  ![Figura 14: Atribuir o endereço IP que está reservado para o cluster][sap-ha-guide-figure-3013]

  _**Figura 14:** atribuir o endereço IP que está reservado para o cluster_

6.  Coloque o nome de anfitrião virtual de cluster online.

  ![Figura 15: O serviço de núcleo de cluster se encontra em execução, com o endereço IP correto][sap-ha-guide-figure-3014]

  _**Figura 15:** o serviço de núcleo de cluster se encontra em execução, com o endereço IP correto_

7.  Adicione o segundo nó de cluster.

  Agora que o serviço de núcleo de cluster se encontra em execução, pode adicionar o segundo nó de cluster.

  ![Figura 16 adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

  _**Figura 16:** adicionar o segundo nó de cluster_

8.  Introduza um nome para o segundo anfitrião do nó de cluster.

  ![Figura 17: Introduza o nome de anfitrião do segundo nó de cluster][sap-ha-guide-figure-3016]

  _**Figura 17:** introduza o nome de anfitrião do segundo nó de cluster_

  > [!IMPORTANT]
  > Certifique-se ser que o **adicionar todo o armazenamento elegível ao cluster** caixa de verificação está *não* selecionado.  
  >
  >

  ![Figura 18: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

  _**Figura 18:** fazer *não* selecione a caixa de verificação_

  Pode ignorar avisos sobre o quórum e de discos. Irá definir o quórum e partilhar o disco mais tarde, conforme descrito em [instalar SIOS DataKeeper Cluster Edition para um disco de partilha de cluster do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Figura 19: Ignorar avisos sobre o quórum do disco][sap-ha-guide-figure-3018]

  _**Figura 19:** ignorar avisos sobre o quórum do disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Configurar um testemunho de partilha de ficheiros do cluster

Configurar um testemunho de partilha de ficheiros do cluster envolve estas tarefas:

- Crie uma partilha de ficheiros.
- Defina o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Criar uma partilha de ficheiros

1.  Selecione um testemunho de partilha de ficheiros em vez de um disco de quórum. SIOS DataKeeper suportar esta opção.

  Os exemplos neste artigo, é o testemunho de partilha de ficheiros no servidor DNS ou do Active Directory que está em execução no Azure. O testemunho de partilha de ficheiro é denominado domcontr-0. Porque iria tiver configurado uma ligação VPN do Azure (através do Gateway de VPN ou Azure ExpressRoute), o seu serviço do Active Directory ou o DNS está no local e não é adequado executar um testemunho de partilha de ficheiros.

  > [!NOTE]
  > Se o serviço do Active Directory ou o DNS é executado apenas no local, não configure o testemunho de partilha de ficheiros no sistema de operativo Active Directory ou o Windows de DNS que está a ser executado no local. Latência de rede entre nós de cluster em execução no Azure e o Active Directory ou o DNS no local poderá ser demasiado grande e causar problemas de conectividade. Lembre-se de que configurar o testemunho de partilha de ficheiros numa máquina virtual do Azure que está a executar o próximo nó de cluster.  
  >
  >

  A unidade de quórum tem, pelo menos, 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade de quórum.

2.  Adicione o objeto de nome de cluster.

  ![Figura 20: Atribuir as permissões na partilha para o objeto de nome de cluster][sap-ha-guide-figure-3019]

  _**Figura 20:** atribuir as permissões na partilha para o objeto de nome de cluster_

  Lembre-se de que as permissões incluem a autoridade para alterar os dados na partilha para o objeto de nome de cluster (no nosso exemplo, pr1-ascs-vir$).

3.  Para adicionar o objeto de nome do cluster à lista, selecione **adicionar**. Altere o filtro para verificar a existência de objetos de computador, para além dos mostrado na figura 22.

  ![Figura 21: Tipos de objeto de alteração para incluir computadores][sap-ha-guide-figure-3020]

  _**Figura 21:** alteração **tipos de objeto** para incluir computadores_

  ![Figura 22: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

  _**Figura 22:** selecionar o **computadores** caixa de verificação_

4.  Introduza o objeto de nome de cluster, conforme mostrado na figura 21. Porque o registo de já ter sido criado, pode alterar as permissões, conforme mostrado na figura 20.

5.  Selecione o **segurança** separador da partilha e, em seguida, defina mais detalhadas permissões para o objeto de nome de cluster.

  ![Figura 23: Definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

  _**Figura 23:** definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Definir o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha

1.  Abra a configurar o Assistente de definição de quórum.

  ![Figura 24: Iniciar o Assistente de definição de quórum de Cluster de configurar][sap-ha-guide-figure-3023]

  _**Figura 24:** iniciar o Assistente de definição de quórum de Cluster de configurar_

2.  No **selecionar opção de configuração de quórum** página, selecione **selecionar o testemunho de quórum**.

  ![Figura 25: Configurações de quórum que pode escolher entre][sap-ha-guide-figure-3024]

  _**Figura 25:** configurações de quórum, pode escolher entre_

3.  No **selecionar testemunho de quórum** página, selecione **configurar um testemunho de partilha de ficheiros**.

  ![Figura 26: Selecione o testemunho de partilha de ficheiros][sap-ha-guide-figure-3025]

  _**Figura 26:** selecionar testemunho de partilha de ficheiros_

4.  Introduza o caminho UNC para a partilha de ficheiros (no nosso exemplo, \\domcontr 0\FSW). Para ver uma lista de alterações que pode fazer, selecione **seguinte**.

  ![Figura 27: Definir a localização da partilha de ficheiros para a partilha de testemunho][sap-ha-guide-figure-3026]

  _**Figura 27:** defina a localização de partilha de ficheiros para a partilha de testemunho_

5.  Selecione as alterações pretendidas e, em seguida, selecione **seguinte**. Terá de reconfigurar com êxito a configuração do cluster, conforme mostrado na figura 28:  

  ![28 figura: Confirmação que tiver reconfigurado o cluster][sap-ha-guide-figure-3027]

  _**Figura 28:** confirmação que tiver reconfigurado o cluster_

Depois de instalar com êxito o cluster de ativação pós-falha do Windows, terá de alterar algumas limiares para que se adaptam a deteção de ativação pós-falha para condições no Azure. Os parâmetros para serem alterados estão documentados na [limiares de rede de cluster de ativação pós-falha de otimização][tuning-failover-cluster-network-thresholds]. Partindo do princípio de que as duas VMs que constituem a configuração de cluster do Windows para ASCS/SCS estão na mesma sub-rede, altere os parâmetros seguintes estes valores:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Estas definições foram testadas com os clientes e oferecem um bom comprometimento. São suficientemente resilientes, mas também fornecem ativação pós-falha que é rápida em condições de erro real com um software SAP ou num nó ou falha VM.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalar SIOS DataKeeper Cluster Edition para o disco de partilha do SAP ASCS/SCS cluster

Agora tem uma configuração de clustering de ativação pós-falha do Windows Server no Azure. Para instalar uma instância do SAP ASCS/SCS, necessita de um recurso de disco partilhado. Não é possível criar os recursos de disco partilhado que necessita no Azure. SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode utilizar para criar recursos de disco partilhado.

Instalar SIOS DataKeeper Cluster Edition para o disco de partilha de cluster do SAP ASCS/SCS envolve estas tarefas:

- Adicione o Microsoft .NET Framework 3.5.
- Instale SIOS DataKeeper.
- Configure SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adicionar o .NET Framework 3.5
.NET framework 3.5 está instalado no Windows Server 2012 R2 ou automaticamente não está ativado. Uma vez SIOS DataKeeper requer .NET ser em todos os nós em que instalar DataKeeper, tem de instalar o .NET Framework 3.5 no sistema operativo convidado de todas as máquinas virtuais no cluster.

Existem duas formas de adicionar o .NET Framework 3.5:

- Utilize a adicionar assistente funções e funcionalidades no Windows, conforme mostrado na figura 29:

  ![Figura 29: Instalar o .NET Framework 3.5 ao utilizar o Assistente de funcionalidades e para adicionar funções][sap-ha-guide-figure-3028]

  _**Figura 29:** instalar o .NET Framework 3.5 ao utilizar o Assistente de funcionalidades e para adicionar funções_

  ![Figura 30: Progresso da instalação de barra quando instalar o .NET Framework 3.5 ao utilizar o Assistente de funcionalidades e para adicionar funções][sap-ha-guide-figure-3029]

  _**Figura 30:** progresso da instalação de barra quando instalar o .NET Framework 3.5 ao utilizar o Assistente de funcionalidades e para adicionar funções_

- Utilize a ferramenta de linha de comandos dism.exe. Para este tipo de instalação, tem de aceder ao diretório SxS no suporte de dados de instalação do Windows. Numa linha de comandos elevada, introduza este comando:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalar SIOS DataKeeper

Instale SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar virtual armazenamento partilhado com SIOS DataKeeper, crie um espelho sincronizado e, em seguida, simular o armazenamento partilhado de cluster.

Antes de instalar o software SIOS, crie o utilizador de domínio DataKeeperSvc.

> [!NOTE]
> Adicione o utilizador de domínio DataKeeperSvc ao grupo de administrador Local em ambos os nós de cluster.
>
>

Para instalar SIOS DataKeeper:

1.  Instale o software SIOS em ambos os nós de cluster.

  ![Instalador SIOS][sap-ha-guide-figure-3030]

  ![Figura 31: Primeira página da instalação SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Figura 31:** primeira página da instalação SIOS DataKeeper_

2.  Na caixa de diálogo, selecione **Sim**.

  ![Figura 32: DataKeeper informa-o se um serviço será desativado][sap-ha-guide-figure-3032]

  _**Figura 32:** DataKeeper informa que um serviço vai ser desativado_

3.  Na caixa de diálogo, recomendamos que selecione **conta de domínio ou servidor**.

  ![Figura 33: Seleção de utilizador para SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Figura 33:** seleção do utilizador para SIOS DataKeeper_

4.  Introduza o nome de utilizador da conta de domínio e palavra-passe que criou para SIOS DataKeeper.

  ![Figura 34: Introduza o nome de utilizador de domínio e palavra-passe para a instalação de SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Figura 34:** introduzir o nome de utilizador de domínio e palavra-passe para a instalação de SIOS DataKeeper_

5.  Instale a chave de licença para a instância de SIOS DataKeeper, conforme mostrado na figura 35.

  ![Figura 35: Introduza a chave de licença de SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Figura 35:** introduzir a chave de licença SIOS DataKeeper_

6.  Quando lhe for solicitado, reinicie a máquina virtual.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Configurar SIOS DataKeeper

Depois de instalar SIOS DataKeeper em ambos os nós, inicie a configuração. O objetivo da configuração é ter replicação síncrona dados entre discos adicionais que estejam anexados a cada uma das máquinas virtuais.

1.  Iniciar a ferramenta de configuração e gestão DataKeeper e, em seguida, selecione **ligar servidor**.

  ![Figura 36: Ferramenta de configuração e SIOS DataKeeper gestão][sap-ha-guide-figure-3036]

  _**Figura 36:** ferramenta SIOS DataKeeper gestão e configuração_

2.  Introduza o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gestão e a configuração deve ligar e, num segundo passo, o segundo nó.

  ![Figura 37: Insira o nome ou endereço de TCP/IP do primeiro nó de gestão e a ferramenta de configuração devem ligar e um segundo passo, o segundo nó][sap-ha-guide-figure-3037]

  _**Figura 37:** inserir o nome ou endereço de TCP/IP do nó primeiro a ferramenta de gestão e a configuração deve ligar e um segundo passo, o segundo nó_

3.  Crie a tarefa de replicação entre os dois nós.

  ![Figura 38: Criar uma tarefa de replicação][sap-ha-guide-figure-3038]

  _**Figura 38:** criar uma tarefa de replicação_

  Um assistente orienta-o ao longo do processo de criação de uma tarefa de replicação.

4.  Defina o nome da tarefa de replicação.

  ![Figura 39: Definir o nome da tarefa de replicação][sap-ha-guide-figure-3039]

  _**Figura 39:** Define o nome da tarefa de replicação_

  ![Figura 40: Definir a base de dados para o nó, o que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

  _**Figura 40:** definir a base de dados para o nó, o que deve ser o nó de origem atual_

5.  Defina o nome, o endereço de TCP/IP e o volume de disco de nó de destino.

  ![Figura 41: Definir o nome, o endereço de TCP/IP e o volume de disco do nó de destino atual][sap-ha-guide-figure-3041]

  _**Figura 41:** definir o nome, o endereço de TCP/IP e o volume de disco do nó de destino atual_

6.  Defina os algoritmos de compressão. No nosso exemplo, recomendamos que comprimir o fluxo de replicação. Especialmente em situações de ressincronização, a compressão do fluxo de replicação reduz significativamente o tempo de ressincronização. Compressão utiliza os recursos de CPU e RAM de uma máquina virtual. À medida que aumenta a taxa de compressão, por isso, não o volume de recursos de CPU que são utilizados. Pode ajustar esta definição mais tarde.

7.  Outra definição que tem de verificar é se ocorre a replicação assíncrona ou síncrona. Quando protege configurações de SAP ASCS/SCS, tem de utilizar replicação síncrona.  

  ![Figura 42: Definir os detalhes de replicação][sap-ha-guide-figure-3042]

  _**Figura 42:** definir os detalhes de replicação_

8.  Defina se o volume que é replicado pela tarefa de replicação deve ser representado para uma configuração de cluster de ativação pós-falha do Windows Server como um disco partilhado. Para a configuração de SAP ASCS/SCS, selecione **Sim** para que o cluster do Windows vê o volume replicado como um disco partilhado que possa utilizar como um volume de cluster.

  ![Figura 43: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

  _**Figura 43:** selecione **Sim** para definir o volume replicado como um volume de cluster_

  Depois do volume é criado, a ferramenta de configuração e gestão DataKeeper mostra que a tarefa de replicação está ativa.

  ![Figura 44: DataKeeper espelhamento síncrono para o disco de partilha de SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

  _**Figura 44:** DataKeeper espelhamento síncrono para o SAP ASCS/SCS partilhar o disco está ativo_

  Gestor de clusters de ativação pós-falha mostra agora o disco como um disco de DataKeeper, conforme mostrado na figura 45:

  ![Figura 45: O Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicado][sap-ha-guide-figure-3045]

  _**Figura 45:** Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicado_

## <a name="next-steps"></a>Passos seguintes

* [Instalar o SAP NetWeaver HA utilizando um cluster de ativação pós-falha do Windows e o disco partilhado para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
