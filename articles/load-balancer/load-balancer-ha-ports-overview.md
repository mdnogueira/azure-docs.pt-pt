---
title: "Descrição geral de portas de elevada disponibilidade no Azure | Microsoft Docs"
description: Saiba mais sobre num Balanceador de carga interno de balanceamento de carga de portas de elevada disponibilidade.
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>Descrição geral de portas de elevada disponibilidade

Azure carregar balanceador padrão ajuda-o a carregar os fluxos TCP e UDP de balanceamento em todas as portas em simultâneo, quando estiver a utilizar um balanceador de carga interno. 

>[!NOTE]
> A funcionalidade de portas de elevada disponibilidade (HA) está disponível com o padrão de Balanceador de carga e está atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade não pode ter o mesmo nível de disponibilidade e fiabilidade como funcionalidades que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Inscrever-se a pré-visualização padrão de Balanceador de carga utilizar portas HA com recursos padrão de Balanceador de carga. Siga as instruções de inscrição para o Balanceador de carga [padrão pré-visualização](https://aka.ms/lbpreview#preview-sign-up) bem.

Uma regra de portas do HA é uma variante de uma regra, configurada num interno carregar balanceador padrão de balanceamento de carga. Pode simplificar a utilização do Balanceador de carga, fornecendo uma única regra de balanceamento de carga todos os TCP e UDP fluxos que chegam em todas as portas de um interno carregar balanceador padrão. A decisão de balanceamento de carga é efetuada por fluxo. Isto baseia-se na seguinte ligação de cinco cadeias de identificação: endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo.

A funcionalidade de portas do HA ajuda-o com cenários importantes, tais como de elevada disponibilidade e dimensionamento de aplicações virtuais de rede (NVA) dentro de redes virtuais. Também pode ajudar a quando um grande número de portas tem de ser com balanceamento de carga. 

A funcionalidade de portas do HA está configurada quando definir as portas front-end e back-end **0**e o protocolo para **todos os**. O recurso de Balanceador de carga interno, em seguida, equilibra a todos os fluxos de TCP e UDP, independentemente do número de porta.

## <a name="why-use-ha-ports"></a>Porquê utilizar portas HA?

### <a name="nva"></a>Dispositivos de rede virtuais

Pode utilizar NVAs para proteger a sua carga de trabalho do Azure a partir de vários tipos de ameaças de segurança. Quando são utilizadas NVAs nestes cenários, têm de ser fiável e altamente disponível e tem ampliar para a pedido.

Pode alcançar estes objetivos, simplesmente ao adicionar instâncias NVA para o conjunto de back-end de Balanceador de carga interno do Azure e configurar uma regra de Balanceador de carga de portas do HA.

HA portas oferecem várias vantagens para NVA HA cenários:
- Ativação pós-falha rápida para bom estado de funcionamento instâncias, com por instância de sondas de estado de funcionamento
- Desempenho superior com escalável para  *n* -instâncias ativas
- *N*-Active Directory e ativa-passiva cenários
- Elimina a necessidade de soluções de complexas, como o Apache ZooKeeper nós para a monitorização de aplicações

O diagrama seguinte apresenta uma implementação de concentrador hub-and-spoke de rede virtual. A spokes realizar imposição de túnel tráfego para a rede virtual do hub e através de NVA, antes de abandonar o fileparser o espaço fidedigno. Os NVAs estejam atrás de um interno carga balanceador padrão com uma configuração de portas do HA. Todo o tráfego pode ser processado e reencaminhado em conformidade.

![Diagrama de rede virtual de concentrador hub-and-spoke, com NVAs implementada no modo de HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se estiver a utilizar NVAs, confirme com o respetivo fornecedor como utilizar melhor HA portas e os cenários que são suportados.

### <a name="load-balancing-large-numbers-of-ports"></a>Grande número de portas de balanceamento de carga

Também pode utilizar as portas HA para aplicações que requerem balanceamento de carga de grandes quantidades de portas. Pode simplificar a estes cenários, utilizando um interno [padrão de Balanceador de carga](https://aka.ms/lbpreview) com portas HA. Uma única regra de balanceamento de carga substitui várias regras, um para cada porta de balanceamento de carga individual.

## <a name="region-availability"></a>Disponibilidade de região

A funcionalidade de portas do HA está disponível no [mesmas regiões como padrão de Balanceador de carga](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Pré-visualização inscrição

Participar na pré-visualização da funcionalidade de portas HA no padrão de Balanceador de carga, registe a sua subscrição para obter acesso. Pode utilizar o Azure CLI 2.0 ou o PowerShell.

>[!NOTE]
>Para utilizar esta funcionalidade, tem também inscreva Balanceador de carga [pré-visualização padrão](https://aka.ms/lbpreview#preview-sign-up), além da funcionalidade de portas do HA. Registo pode demorar até uma hora.

### <a name="sign-up-by-using-azure-cli-20"></a>Inscrever-se utilizando o Azure CLI 2.0

1. A funcionalidade com o fornecedor do registo:
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos a concluir. Pode verificar o estado da operação com o seguinte comando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    A operação for concluída com êxito quando o estado de registo de funcionalidade devolve **registada**, conforme mostrado aqui:
   
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
    
### <a name="sign-up-by-using-powershell"></a>Inscrever-se utilizando o PowerShell

1. A funcionalidade com o fornecedor do registo:
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos a concluir. Pode verificar o estado da operação com o seguinte comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    A operação for concluída com êxito quando o estado de registo de funcionalidade devolve **registada**, conforme mostrado aqui:
   
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

Seguem-se as configurações suportadas ou exceções para a funcionalidade de portas do HA:

- Uma única configuração de IP Front-end pode ter uma única regra de Balanceador de carga DSR com portas HA ou pode ter uma regra de Balanceador de carga não DSR único com portas HA. Não é possível ter ambos.
- Uma configuração de IP da interface de rede única só pode ter um não-DSR com portas HA a regra de Balanceador de carga. Não é possível configurar quaisquer outras regras para este ipconfig.
- Uma configuração de IP da interface de rede única pode ter um ou mais DSR HA portas, as regras de Balanceador de carga fornecido todas as respetivas configurações de IP Front-end são exclusivas.
- Se todas a regras de balanceamento de carga forem HA portas (apenas DSR), podem coexistir duas (ou mais) regras de Balanceador de carga que apontam para o mesmo conjunto de back-end. O mesmo se aplica se todas as regras são não-HA portas (DSR e não DSR). Se existir uma combinação de portas HA e regras de não HA portas, no entanto, dois dessas carga balanceamento regras não podem coexistir.
- A funcionalidade de portas do HA não está disponível para IPv6.
- Fluxo symmetry para cenários NVA é suportado com apenas um único NIC. Consulte a descrição e diagrama para [aplicações virtuais de rede](#nva). 



## <a name="next-steps"></a>Passos seguintes

- [Configurar portas HA num padrão de Balanceador de interno de carga](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre a pré-visualização padrão de Balanceador de carga](https://aka.ms/lbpreview)

