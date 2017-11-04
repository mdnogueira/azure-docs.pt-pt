---
title: "Configurar endereços IP privados para as VMs (clássica) - CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como configurar endereços IP privados para máquinas virtuais (clássicas) utilizando a interface de linha de comandos do Azure (CLI) 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed0fe2fea20671063395b9ff089599853278989d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>Configurar endereços IP privados para uma máquina virtual (clássica) utilizando a CLI do Azure 1.0

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo de implementação Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Os comandos da CLI do Azure de exemplo abaixo esperam num ambiente simple que já criado. Se pretender executar os comandos, como são apresentados neste documento, criar primeiro o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático quando criar uma VM
Para criar uma nova VM com o nome *DNS01* num novo serviço em nuvem com o nome *TestService* com base no cenário acima, siga estes passos:

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o **criar o serviço do azure** comando para criar o serviço em nuvem.
   
        azure service create TestService --location uscentral
   
    Resultado esperado:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Execute o **azure criar a vm** comando para criar a VM. Tenha em atenção o valor para um endereço IP privado estático. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Resultado esperado:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (ou --location)**. Região do Azure onde será criada a VM. Para o nosso cenário *centralus*.
   * **-n (ou nome de vm –)**. Nome da VM ser criada.
   * **-w (ou ---nome de rede virtual)**. Nome da VNet onde será criada a VM. 
   * **-S (ou - ip estático)**. Endereço de IP privado estático para a VM.
   * **TestService**. Nome do serviço de nuvem onde será criada a VM.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagem utilizada para criar a VM.
   * **adminuser**. Administrador local para a VM do Windows.
   * **AdminP@ssw0rd**. Palavra-passe de administrador local para a VM do Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter privadas informações endereços IP estáticos para uma VM
Para ver as informações de endereço IP privadas estáticas para a VM criada com o script acima, execute o seguinte comando da CLI do Azure e observe o valor para *rede StaticIP*:

    azure vm static-ip show DNS01

Resultado esperado:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM
Para remover o endereço IP privado estático adicionadas para a VM com o script acima, execute o seguinte comando da CLI do Azure:

    azure vm static-ip remove DNS01

Resultado esperado:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Como adicionar um IP privado estático para uma VM existente
Para adicionar um estático privado de endereços IP para a VM criada ao utilizar o script acima, runt tados os seguintes comandos:

    azure vm static-ip set DNS01 192.168.1.101

Resultado esperado:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [instância ao nível do IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte o [reservado APIs REST do IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

