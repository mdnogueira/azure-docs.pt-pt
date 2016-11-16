---
title: "Criar um balanceador de carga interno com a CLI do Azure no modelo de implementação clássica | Microsoft Docs"
description: "Saiba como criar um balanceador de carga interno com a CLI do Azure no modelo de implementação clássica"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4364d3bcdffd278bef35b224a8e22062814ca490


---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Introdução à criação de um balanceador de carga interno (modo clássico) com a CLI do Azure
[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Saiba como [executar estes passos com o modelo do Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para criar um conjunto de balanceadores de carga internos para máquinas virtuais
Para criar um conjunto de balanceadores de carga internos e os servidores que irão enviar o tráfego para o mesmo, tem de efetuar o seguinte:

1. Crie uma instância Balanceamento de Carga Interno que será o ponto final do tráfego de entrada para balanceamento de carga nos servidores de um conjunto com balanceamento de carga.
2. Adicione os pontos finais correspondentes às máquinas virtuais que irão receber o tráfego de entrada.
3. Configure os servidores que irão enviar o tráfego para balanceamento de carga para enviar o tráfego para o endereço IP virtual (VIP) da instância de Balanceamento de Carga Interno.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Criação passo a passo de um balanceador de carga interno com a CLI
Este guia mostra como criar um balanceador de carga interno com base no cenário acima.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../xplat-cli-install.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo clássico, como mostrado abaixo.
   
        azure config mode asm
   
    Resultado esperado:
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Criar o ponto final e o conjunto de balanceadores de carga
O cenário assume as máquinas virtuais "DB1" e "DB2" num serviço em nuvem denominado "mytestcloud". Ambas as máquinas virtuais estão a utilizar uma rede virtual denominada "mytestvnet" com a sub-rede "subnet-1".

Este guia irá criar um conjunto de balanceadores de carga internos através da porta 1433 como porta privada e a porta 1433 como porta local.

Este é um cenário comum em que tem máquinas virtuais SQL no back-end com um balanceador de carga interno para garantir que os servidores de base de dados não são expostos diretamente através de um endereço IP público.

### <a name="step-1"></a>Passo 1
Criar um conjunto de balanceadores de carga internos com `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parâmetros utilizados:

**-r** – nome do serviço em nuvem<BR>
**-n** – nome do balanceador de carga interno<BR>
**-t** – nome da sub-rede (a mesma sub-rede das máquinas virtuais que serão adicionadas ao balanceador de carga interno)<BR>
**-a** – (opcional) adicionar um endereço IP privado estático<BR>

Veja `azure service internal-load-balancer --help` para obter mais informações.

Pode verificar as propriedades do balanceador de carga interno através do comando `azure service internal-load-balancer list` *nome do serviço em nuvem*.

Segue-se um exemplo do resultado:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Passo 2
Configure o conjunto de balanceadores de carga internos quando adicionar o primeiro ponto final. Associe o ponto final, a máquina virtual e a porta de sonda ao conjunto de balanceadores de carga internos neste passo.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parâmetros utilizados:

**-k** – porta da máquina virtual local<BR>
**-t** – porta de sonda<BR>
**-r** – protocolo de sonda<BR>
**-e** – intervalo de sonda em segundos<BR>
**-f** – intervalo do tempo limite em segundos <BR>
**-i** – nome do balanceador de carga interno <BR>

## <a name="step-3"></a>Passo 3
Verifique a configuração do balanceador de carga através de `azure vm show` *nome da máquina virtual*

    azure vm show DB1

O resultado será:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto final de ambiente de trabalho remoto para uma máquina virtual
Pode criar um ponto final de ambiente de trabalho remoto para reencaminhar o tráfego de rede de uma porta pública para uma porta local de uma máquina virtual específica através de `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Remover uma máquina virtual do balanceador de carga
Pode remover uma máquina virtual de um conjunto de balanceadores de carga internos ao eliminar o ponto final associado. Após a remoção do ponto final, a máquina virtual já não pertence ao conjunto de balanceadores de carga.

 Através do exemplo acima, pode remover o ponto final criado para a máquina virtual "DB1" do balanceador de carga interno "ilbset" com o comando `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Veja `azure vm endpoint --help` para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Configurar um modo de distribuição de balanceador de carga com a afinidade do IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


