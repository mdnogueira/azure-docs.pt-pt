---
title: "Como criar NSGs no modo clássico com a CLI do Azure | Microsoft Docs"
description: "Saiba como criar e implementar NSGs no modo clássico com a CLI do Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 115a1937a4c88ba2b986a40c84b1b759ed5e03b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Como criar NSGs (clássica) na CLI do Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [criar NSGs no modelo de implementação Resource Manager](virtual-networks-create-nsg-arm-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam num ambiente simple já criado com base no cenário acima. Se pretender executar os comandos, como são apresentados neste documento, criar primeiro o ambiente de teste por [criar uma VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Como criar o NSG para sub-rede do front-end
Para criar um NSG denominado chamado **NSG-front-end** com base no cenário acima, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o  **`azure config mode`**  comando para mudar para o modo clássico, como mostrado abaixo.
   
        azure config mode asm
   
    Resultado esperado:
   
        info:    New mode is asm
3. Execute o  **`azure network nsg create`**  comando para criar um NSG.
   
        azure network nsg create -l uswest -n NSG-FrontEnd
   
    Resultado esperado:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    Parâmetros:
   
   * **-l (ou --location)**. Região do Azure onde será criado o NSG de novo. Para o nosso cenário *westus*.
   * **-n (ou --name)**. Nome do novo NSG. Para o nosso cenário *NSG-front-end*.
4. Execute o  **`azure network nsg rule create`**  comando para criar uma regra que permite o acesso à porta 3389 (RDP) a partir da Internet.
   
        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   
    Resultado esperado:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
   
    Parâmetros:
   
   * **-a (ou - nsg-name)**. Nome do NSG na qual a regra será criada. Para o nosso cenário *NSG-front-end*.
   * **-n (ou --name)**. Nome da nova regra. Para o nosso cenário *rdp regra*.
   * **-c (ou - ação)**. Nível de acesso para a regra (negar ou permitir).
   * **-p (ou - protocol)**. Protocolo (Tcp, Udp ou *) para a regra.
   * **-r (ou - tipo)**. Direção de ligação (entrada ou saída).
   * **-y (ou - prioridade)**. Prioridade da regra.
   * **-f (ou - prefixo de endereço de origem)**. Prefixo de endereço de origem no CIDR ou utilizando etiquetas predefinidas.
   * **-o (ou - intervalo de portas de origem)**. Porta de origem ou intervalo de portas.
   * **-e (ou - prefixo de endereço de destino)**. Prefixo de endereço de destino no CIDR ou utilizando etiquetas predefinidas.
   * **-u (ou - intervalo de portas de destino)**. Porta de destino ou intervalo de portas.
5. Execute o  **`azure network nsg rule create`**  comando para criar uma regra que permite o acesso à porta 80 (HTTP) a partir da Internet.
   
        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
   
    Putput esperado:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK
6. Execute o  **`azure network nsg subnet add`**  comando para associar o NSG para sub-rede do front-end.
   
        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   
    Resultado esperado:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Como criar o NSG para sub-rede de back-end
Para criar um NSG denominado chamado *NSG-back-end* com base no cenário acima, siga os passos abaixo.

1. Execute o  **`azure network nsg create`**  comando para criar um NSG.
   
        azure network nsg create -l uswest -n NSG-BackEnd
   
    Resultado esperado:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    Parâmetros:
   
   * **-l (ou --location)**. Região do Azure onde será criado o NSG de novo. Para o nosso cenário *westus*.
   * **-n (ou --name)**. Nome do novo NSG. Para o nosso cenário *NSG-front-end*.
2. Execute o  **`azure network nsg rule create`**  comando para criar uma regra que permite o acesso à porta 1433 (SQL) a partir da sub-rede do front-end.
   
        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   
    Resultado esperado:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
3. Execute o  **`azure network nsg rule create`**  comando para criar uma regra que nega o acesso à Internet.
   
        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   
    Putput esperado:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK
4. Execute o  **`azure network nsg subnet add`**  comando para associar o NSG para sub-rede de back-end.
   
        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
   
    Resultado esperado:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK

