---
title: "Descrição geral de elevada disponibilidade portas no Azure | Microsoft Docs"
description: Saiba mais sobre num Balanceador de carga interno de balanceamento de carga de portas de elevada disponibilidade
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>Descrição geral de elevada disponibilidade portas (pré-visualização)

Azure carregar balanceador padrão introduz uma nova capacidade de carregar os fluxos TCP e UDP de balanceamento em todas as portas em simultâneo ao utilizar um balanceador de carga interno. 

>[!NOTE]
> Funcionalidade de elevada disponibilidade portas está disponível com o padrão de Balanceador de carga e atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade poderá não ter o mesmo nível de disponibilidade e fiabilidade oferecido na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). É necessário inscrever-se para a pré-visualização de padrão de Balanceador de carga utilizar portas HA com recursos padrão de Balanceador de carga. Siga as instruções de inscrição para além do Balanceador de carga [padrão pré-visualização](https://aka.ms/lbpreview#preview-sign-up) bem.

Uma regra de portas HA é uma variante de uma configurado num interno carregar balanceador padrão de regra de balanceamento de carga.  Cenários são simplificados, fornecendo uma única regra LB para equilibrar a carga todos os TCP e UDP fluxos que chegam em todas as portas de um front-end padrão de Balanceador de carga interno. A decisão de balanceamento de carga é efetuada por fluxo com base em cinco-cadeias de identificação de endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo.

HA portas permite cenários importantes, tais como de elevada disponibilidade e dimensionamento para a rede Virtual aparelhos (NVA) dentro de redes virtuais, bem como outros cenários em que tem de ser um grande número de portas com balanceamento de carga. 

HA portas está configurado definindo o front-end e back-end portas **0** e o protocolo para **todos os**.  O recurso de Balanceador de carga interno agora equilibra a todos os fluxos TCP e UDP independentemente do número de porta.

## <a name="why-use-ha-ports"></a>Porquê utilizar portas HA

### <a name="nva"></a>Dispositivos de rede Virtual

Pode utilizar aplicações virtuais de rede (NVA) para proteger a sua carga de trabalho do Azure a partir de vários tipos de ameaças de segurança. Quando são utilizadas NVA nestes cenários, têm de ser fiável, elevada disponibilidade e escalável para a pedido.

Pode alcançar estes objetivos no seu cenário ao simplesmente adicionar instâncias NVA para o conjunto de back-end de Balanceador de carga interno do Azure e configurar uma regra de HA portas Balanceador de carga.

HA portas oferecem várias vantagens para NVA HA cenários:
- a efetuar a ativação pós-falha instâncias bom estado de funcionamento com por instância de sondas de estado de funcionamento
- desempenho superior com escalável para instâncias de n-Active Directory
- cenários de n-ativo e ativa-passiva
- Elimina a necessidade de soluções complexas, como os nós de Zookeeper para monitorização de aplicações

O exemplo seguinte apresenta uma implementação de rede virtual de concentrador hub-and-spoke com a imposição de spokes realizar túnel tráfego para a rede virtual do hub e através de NVA antes de abandonar o fileparser o espaço fidedigno. Os NVAs estejam atrás de um interno carga balanceador padrão com a configuração de portas HA.  Todo o tráfego é possível processar e reencaminhar em conformidade. 

![ha portas de exemplo](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1 - rede virtual de Concentrador Hub-and-spoke com NVAs implementada no modo de HA

Se estiver a utilizar dispositivos de rede Virtual, verifique se o com o respetivo fornecedor como utilizar melhor HA portas e os cenários que são suportados.

### <a name="load-balancing-large-numbers-of-ports"></a>Grande número de portas de balanceamento de carga

Também pode utilizar as portas HA para cenários de aplicações que necessitam de carga balanicng de grandes quantidades de portas. Estes cenários podem ser simplificados utilizando um interno [padrão de Balanceador de carga](https://aka.ms/lbpreview) com HA portas em que uma única regra de balanceamento de carga substitui várias regras, um para cada porta de balanceamento de carga individual.

## <a name="region-availability"></a>Disponibilidade de região

HA portas está disponível no [mesmas regiões como padrão de Balanceador de carga](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Pré-visualização inscrição

Participar na pré-visualização da funcionalidade de portas HA no padrão de Balanceador de carga, registe a sua subscrição para obter acesso utilizando 2.0 do Azure CLI ou PowerShell.  Siga estes três passos:

>[!NOTE]
>Para utilizar esta funcionalidade, tem também de inscrição para o Balanceador de carga [pré-visualização padrão](https://aka.ms/lbpreview#preview-sign-up) para além das portas HA. Registo de pré-visualizações de portas HA ou padrão de Balanceador de carga pode demorar uma hora.

### <a name="sign-up-using-azure-cli-20"></a>Inscrever-se utilizar o Azure CLI 2.0

1. A funcionalidade com o fornecedor de registo
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos a concluir.  Pode verificar o estado da operação com o seguinte comando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    . Avance para o passo 3, quando o estado de registo de funcionalidade devolve registada como mostrado abaixo:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Conclua a inscrição de pré-visualização ao registar novamente a sua subscrição com o fornecedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Inscrever-se com o PowerShell

1. A funcionalidade com o fornecedor de registo
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos a concluir.  Pode verificar o estado da operação com o seguinte comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    . Avance para o passo 3, quando o estado de registo de funcionalidade devolve registada como mostrado abaixo:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Conclua a inscrição de pré-visualização ao registar novamente a sua subscrição com o fornecedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Limitações

Seguem-se as configurações suportadas ou exceções para HA portas:

- Uma configuração de IP de front-end única pode ter uma única regra de Balanceador de carga DSR com portas HA ou pode ter uma regra de Balanceador de carga não DSR único com portas HA. Não é possível ter ambos.
- Uma configuração de IP da Interface de rede única só pode ter um não-DSR com portas HA a regra de Balanceador de carga. Não existem outras regras podem ser configuradas para este ipconfig.
- Uma única configuração de IP da Interface de rede pode ter um ou mais regras de Balanceador de carga DSR HA portas, fornecido todas as respetivas configurações de IP de front-end respetivos são exclusivas.
- Se o balanceamento de carga todas as regras são portas HA (apenas DSR) ou, todas as regras são não - HA portas (DSR & não DSR), regras de Balanceador de carga de dois (ou mais) que aponta para a mesma pode de conjunto de back-end coexistem. Não podem coexistir duas essas carga regras de balanceamento se existir uma combinação de regras HA portas e não - HA portas.
- HA portas não está disponível para IPv6.
- Fluxo symmetry para cenários NVA é suportado com apenas o NIC único. Consulte a descrição e diagrama para [aparelhos de rede Virtual](#nva). 



## <a name="next-steps"></a>Passos seguintes

- [Configurar portas HA num padrão de Balanceador de interno de carga](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre a pré-visualização padrão de Balanceador de carga](https://aka.ms/lbpreview)

