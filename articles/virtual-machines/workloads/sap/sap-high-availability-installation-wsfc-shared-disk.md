---
title: "Instalar o SAP NetWeaver HA num cluster de ativação pós-falha do Windows e disco partilhado para uma instância do SAP ASCS/SCS no Azure | Microsoft Docs"
description: "Saiba como instalar o SAP NetWeaver HA num cluster de ativação pós-falha do Windows e disco partilhado para uma instância do SAP ASCS/SCS."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 419bbdd57a391dbbf01c2110a1609cb3d0ded003
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalar o SAP NetWeaver HA num cluster de ativação pós-falha do Windows e disco partilhado para uma instância do SAP ASCS/SCS no Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

Este artigo descreve como instalar e configurar um sistema SAP de elevada disponibilidade no Azure através da utilização de um cluster de ativação pós-falha do Windows Server e o disco partilhado de cluster para uma instância do SAP ASCS/SCS de clustering.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, consulte estes documentos:

* [Guia de arquitetura: uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Preparar a infraestrutura do Azure para HA do SAP através da utilização de um cluster de ativação pós-falha do Windows e o disco partilhado para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não vamos descrevê a configuração DBMS neste artigo porque setups variam consoante o sistema DBMS que utiliza. Partimos do princípio que preocupações de elevada disponibilidade com o DBMS são resolvidas com as funcionalidades que suportam os fornecedores DBMS diferentes para o Azure. Os exemplos são AlwaysOn ou espelhamento de base de dados para o SQL Server e Oracle Data Guard para bases de dados Oracle. No cenário que utilizamos neste artigo, vamos não adicionam mais proteção para o DBMS.

Não existem não existem considerações especiais quando os diferentes serviços DBMS interagem com um cluster SAP ASCS ou a configuração de SCS no Azure.

> [!NOTE]
> Os procedimentos de instalação de sistemas do SAP NetWeaver ABAP, sistemas de Java e sistemas ABAP + Java são quase idênticos. A diferença mais significativa é que um sistema de SAP ABAP tem uma instância ASCS. O sistema de SAP Java tem uma instância SCS. O sistema SAP ABAP + Java tem uma instância ASCS e um SCS instância em execução no mesmo grupo de cluster de ativação pós-falha de Microsoft. Todas as diferenças de instalação para cada pilha de instalação do SAP NetWeaver explicitamente são mencionadas. Pode assumir que todas as outras partes são os mesmos.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instale o SAP com uma instância ASCS/SCS de elevada disponibilidade

> [!IMPORTANT]
> Lembre-se de que não coloque o ficheiro de paginação em volumes DataKeeper SIOS espelhada. DataKeeper não suporta volumes espelhados. Pode deixar o ficheiro de paginação na unidade D temporária de uma máquina virtual do Azure, que é a predefinição. Se este não estiver lá, mova o ficheiro de paginação do Windows para a unidade D da sua máquina virtual do Azure.
>
>

Instalação do SAP com uma instância ASCS/SCS de elevada disponibilidade envolve estas tarefas:

* Crie um nome de anfitrião virtual para a instância do SAP ASCS/SCS em cluster.
* Instale o SAP primeiro nó de cluster.
* Modificar o perfil SAP da instância ASCS/SCS.
* Adicione uma porta de pesquisa.
* Abra a porta de sonda de firewall do Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de anfitrião virtual para a instância do SAP ASCS/SCS em cluster

1.  No Gestor de DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS.

  > [!IMPORTANT]
  > O endereço IP que atribuir para o nome de anfitrião virtual da instância ASCS/SCS tem de ser igual ao endereço IP que atribuiu ao balanceador de carga do Azure (\<SID\>ascs - lb).  
  >
  >

  O endereço IP do nome de anfitrião virtual SAP ASCS/SCS (pr1-ascs-sap) é o mesmo que o endereço IP do Balanceador de carga do Azure (pr1-lb-ascs).

  ![Figura 1: Definir a entrada DNS para o nome virtual do cluster SAP ASCS/SCS e o endereço de TCP/IP][sap-ha-guide-figure-3046]

  _**Figura 1:** definir a entrada DNS para o nome virtual do cluster SAP ASCS/SCS e o endereço de TCP/IP_

2.  Para definir o endereço IP que é atribuído para o nome de anfitrião virtual, selecione **Gestor de DNS** > **domínio**.

  ![Figura 2: Novo nome virtual e o endereço de TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Figura 2:** para configuração de cluster do SAP ASCS/SCS de endereços novo nome virtual e TCP/IP_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalar o SAP primeiro nó de cluster

1.  Executar a primeira opção de nó de cluster no nó de cluster A. Por exemplo, num pr1-ascs-0 * anfitrião.
2.  Para manter as predefinições de portas para o Balanceador de carga interno do Azure, selecione:

  * **Sistema ABAP**: **ASCS** instância número **00**
  * **Sistema de Java**: **SCS** instância número **01**
  * **Sistema ABAP + Java**: **ASCS** instância número **00** e **SCS** instância número **01**

  Para utilizar números de instância diferente 00 para a instância de ABAP ASCS e 01 para a instância de Java SCS, em primeiro lugar, altere a Azure de carga interno balanceador predefinido balanceamento de carga regras. Para obter mais informações, consulte [alterar as regras de Balanceador de carga interno do Azure de balanceamento de carga do predefinido ASCS/SCS][sap-ha-guide-8.9].

As seguintes algumas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modificar o perfil SAP da instância ASCS/SCS

Primeiro, adicione um novo parâmetro de perfil. O parâmetro de perfil impede que as ligações entre os processos de trabalho do SAP e o servidor de colocar em fila fechar quando estão inativos há demasiado tempo. Iremos mencionar o cenário de problema de [adicionar entradas de registo em ambos os nós de cluster da instância do SAP ASCS/SCS][sap-ha-guide-8.11]. Na secção, iremos também apresenta duas alterações alguns parâmetros de ligação de TCP/IP básicos. Um segundo passo, terá de configurar o servidor de colocar em fila para enviar um `keep_alive` assinalar para que as ligações não atingiu o limiar de inatividade do Balanceador de carga interno do Azure.

Para modificar o perfil SAP da instância ASCS/SCS:

1.  Adicione este parâmetro de perfil para o perfil de instância do SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  No nosso exemplo, o caminho é:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Por exemplo, para o perfil de instância do SAP SCS e o caminho correspondente:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Para aplicar as alterações, reinicie a instância do SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adicionar uma porta de pesquisa

Utilize a funcionalidade de sonda do Balanceador de carga interno para efetuar a configuração do cluster completo funcionar com o Balanceador de carga do Azure. O Balanceador de carga interno do Azure, normalmente, distribui a carga de trabalho de entrada equitativamente entre participantes máquinas virtuais.

 No entanto, isto não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância for passiva e não pode aceitar qualquer da carga de trabalho. Uma funcionalidade de pesquisa ajuda-o ao balanceador de carga interno do Azure atribui trabalho apenas para uma instância do Active Directory. Com a funcionalidade de pesquisa, o Balanceador de carga interno pode detetar as instâncias ativas e, em seguida, apenas a instância com a carga de trabalho de destino.

Para adicionar uma porta de pesquisa:

1.  Verifique o atual **ProbePort** valor executando o seguinte comando do PowerShell:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Execute o comando a partir de dentro de uma das máquinas virtuais na configuração do cluster.

2.  Defina uma porta de pesquisa. O número de porta de sonda de predefinido é 0. No nosso exemplo, utilizamos sonda a porta 62000.

  ![Figura 3: A porta de pesquisa de configuração de cluster é 0 por predefinição][sap-ha-guide-figure-3048]

  _**Figura 3:** a porta de pesquisa de configuração de cluster predefinido é 0_

  O número de porta está definido nos modelos de SAP Azure Resource Manager. Pode atribuir o número da porta no PowerShell.

  Para definir um novo valor de ProbePort para o SAP \<SID\> recurso de cluster IP, execute o seguinte script do PowerShell para atualizar as variáveis de PowerShell para o seu ambiente:

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  Depois de colocar o SAP \<SID\> online a função de cluster, certifique-se de que **ProbePort** está definido para o novo valor.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  Depois do script é executado, lhe for pedido para reiniciar o grupo de cluster do SAP para ativar as alterações.

  ![Figura 4: Sonda a porta de cluster, depois de definir o novo valor][sap-ha-guide-figure-3049]

  _**Figura 4:** sonda a porta de cluster depois de definir o novo valor_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Abra a porta de sonda de firewall do Windows

Abra uma porta de sonda de firewall do Windows em ambos os nós de cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall do Windows. Atualize as variáveis de PowerShell para o seu ambiente.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** está definido como **62000**. Agora, pode aceder à partilha de ficheiros \\\ascsha-clsap\sapmnt dos outros anfitriões, tais como ascsha dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalar a instância de base de dados

Para instalar a instância de base de dados, siga o processo que é descrito na documentação de instalação do SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó de cluster

Para instalar o segundo cluster, siga os passos descritos no guia de instalação do SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Alterar o tipo de início da instância de serviço do Windows de ERS SAP

Alterar o tipo de início do serviço Windows do SAP ERS de **automático (início atrasado)** em ambos os nós de cluster.

![Figura 5: Altere o tipo de serviço para a instância do SAP ERS para atrasada automático][sap-ha-guide-figure-3050]

_**Figura 5:** alterar o tipo de serviço para a instância do SAP ERS a atrasada automática_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalar o servidor de principal da aplicação do SAP

Instalar a instância de servidor de aplicação principal (PAS) \<SID\>-di-0 na máquina virtual que tiver designado para alojar PAS. Não existem nenhumas dependências no Azure. Não existem não existem definições de DataKeeper específicas.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalar o servidor de aplicação adicional SAP

Instale um servidor SAP aplicação adicionais (AAS) em todas as máquinas virtuais que tiver designado para alojar uma instância de servidor de aplicações SAP. Por exemplo, num \<SID\>-di-1 para \<SID\>- di -&lt;n&gt;.

> [!NOTE]
> Isto é concluída a instalação de um sistema de SAP NetWeaver de elevada disponibilidade. Em seguida, prossiga com a ativação pós-falha de teste.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testar a ativação pós-falha de instância do SAP ASCS/SCS e replicação SIOS
É fácil testar e monitorizar uma ativação pós-falha de instância do SAP ASCS/SCS e a replicação de discos SIOS utilizando a ferramenta Gestor de clusters de ativação pós-falha e a gestão de DataKeeper SIOS e a configuração.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Instância do SAP ASCS/SCS está em execução no nó de cluster A

O grupo de cluster do SAP PR1 está em execução no nó de cluster A. Por exemplo, num pr1-ascs-0. Atribuir a unidade de disco S, partilhada que faz parte do grupo de cluster SAP PR1, para o nó de cluster A. A instância ASCS/SCS também utiliza a unidade de disco S. 

![Figura 6: Gestor de clusters de ativação pós-falha: O SAP \<SID\> grupo de cluster está em execução no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 6:** Gestor de clusters de ativação pós-falha: O SAP \<SID\> grupo de cluster está em execução no nó de cluster A_

A ferramenta de gestão de DataKeeper SIOS e configuração, pode ver que os dados de disco partilhado são replicados em sincronia da unidade de volume de origem S num nó de cluster A para a unidade do volume de destino S num nó de cluster B. Por exemplo, são replicado desde pr1-ascs-0 [10.0.0.40] pr1-ascs-1 [10.0.0.41].

![Figura 7: No SIOS DataKeeper, replicar o volume local do nó do cluster A para o nó de cluster B][sap-ha-guide-figure-5001]

_**A figura 7:** no SIOS DataKeeper, replicar o volume local a partir do nó de cluster A para o nó de cluster B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>A ativação pós-falha do nó de um nó B

1.  Escolher uma destas opções para iniciar uma ativação pós-falha do SAP \<SID\> grupo de cluster do nó do cluster A para o nó de cluster b:
  - Gestor de clusters de ativação pós-falha  
  - PowerShell do Cluster de ativação pós-falha

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Reinicie um nó de cluster no sistema de operativo convidado do Windows. Esta ação inicia uma ativação pós-falha automática do SAP \<SID\> grupo de cluster do nó de um nó B.  
3.  Reinicie o nó de cluster A partir do portal do Azure. Esta ação inicia uma ativação pós-falha automática do SAP \<SID\> grupo de cluster do nó de um nó B.  
4.  Reinicie um nó de cluster utilizando o Azure PowerShell. Esta ação inicia uma ativação pós-falha automática do SAP \<SID\> grupo de cluster do nó de um nó B.

  Após a ativação pós-falha, o SAP \<SID\> grupo de cluster está em execução no nó de cluster B. Por exemplo, que é executada pr1-ascs-1.

  ![Figura 8: Na ativação pós-falha Gestor de clusters, o SAP \<SID\> grupo de cluster está em execução no nó de cluster B][sap-ha-guide-figure-5002]

  _**Figura 8**: no Gestor de clusters de ativação pós-falha, o SAP \<SID\> grupo de cluster está em execução no nó de cluster B_

  O disco partilhado está agora montado no cluster de nó B. SIOS DataKeeper está a replicar dados do disco de volume de origem S num nó de cluster B a unidade de volume de destino S num nó de cluster A. Por exemplo, está a replicar entre pr1-ascs-1 [10.0.0.41] para pr1-ascs-0 [10.0.0.40].

  ![Figura 9: SIOS DataKeeper replica o volume local do nó do cluster B um nó de cluster][sap-ha-guide-figure-5003]

  _**Figura 9:** SIOS DataKeeper replica o volume local do nó do cluster B um nó de cluster_
