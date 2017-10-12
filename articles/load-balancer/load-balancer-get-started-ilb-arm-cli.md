---
title: "Criar um balanceador de carga interno – CLI do Azure | Microsoft Docs"
description: Saiba como criar um balanceador de carga interno com a CLI do Azure no Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 83cf027d95018de61ea906268d8f24700203e0c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Criar um balanceador de carga interno com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Resource Manager, o que é recomendado pela Microsoft para a maioria das novas implementações em vez do [modelo de implementação clássica](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Implementar a solução com a CLI do Azure

Os passos seguintes mostram como criar um balanceador de carga com acesso à Internet com o Azure Resource Manager com CLI. Com o Azure Resource Manager, cada recurso é criado e configurado individualmente e, em seguida, colocado em conjunto para criar um recurso.

Tem de criar e configurar os seguintes objetos para implementar um balanceador de carga:

* **Configuração de IP front-end**: contém os endereços IP públicos para o tráfego de rede recebido
* **Conjunto de endereços de back-end**: contém interfaces de rede (NICs) para as máquinas virtuais receberem tráfego de rede do balanceador de carga
* **Regras de balanceamento de carga**: contém as regras que mapeiam uma porta pública no balanceador de carga para uma porta no conjunto de endereços de back-end
* **Regras NAT de entrada**: contém as regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no conjunto de endereços de back-end
* **Sondas**: contém sondas utilizadas para verificar a disponibilidade de instâncias das máquinas virtuais no conjunto de endereços de back-end

Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar a CLI para utilizar o Resource Manager

1. Se nunca tiver utilizado a CLI do Azure, veja [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md). Siga as instruções até ao ponto onde seleciona a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo:

    ```azurecli
    azure config mode arm
    ```

    Resultado esperado:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Criar um balanceador de carga interno passo a passo

1. Inicie sessão no Azure.

    ```azurecli
    azure login
    ```

    Quando lhe for pedido, introduza as suas credenciais do Azure.

2. Altere as ferramentas de comando para o modo Azure Resource Manager.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos no Azure Resource Manager estão associados a um grupo de recursos. Se ainda não o tiver feito, crie um grupo de recursos.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Criar um conjunto de balanceadores de carga internos

1. Criar um balanceador de carga interno

    No cenário seguinte, é criado um grupo de recursos com o nome nrprg na região E.U.A. Leste.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > Todos os recursos para um balanceador de carga interno, como redes virtuais e sub-redes da rede virtual, devem estar no mesmo grupo de recursos e na mesma região.

2. Crie um endereço IP de front-end para o balanceador de carga interno.

    O endereço IP que utilizar tem de estar dentro do intervalo de sub-rede da rede virtual.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Crie o conjunto de endereços de back-end.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    Depois de definir um endereço IP de front-end e um conjunto de endereços de back-end, pode criar regras de balanceador de carga, regras NAT de entrada e sondas do estado de funcionamento personalizadas.

4. Crie uma regra de balanceador de carga para o balanceador de carga interno.

    Ao seguir os passos anteriores, o comando cria uma regra de balanceador de carga para escutar a porta 1433 no conjunto de front-end e envia o tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end, também através da porta 1433.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Crie regras NAT de entrada.

    As regras NAT de entrada são utilizadas para criar pontos finais num balanceador de carga que aceda a uma instância de máquina virtual específica. Os passos anteriores criaram duas regras NAT para o ambiente de trabalho remoto.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Crie sondas de estado de funcionamento para o balanceador de carga.

    Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > A plataforma Microsoft Azure utiliza um endereço IPv4 estático encaminhável publicamente para uma grande variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por nenhuma firewall, porque pode causar comportamentos inesperados.
    > No que respeita ao balanceamento de carga interno do Azure, este endereço IP é utilizado pelas sondas de monitorização do balanceador de carga para determinar o estado de funcionamento das máquinas virtuais num conjunto com balanceamento de carga. Se for utilizado um grupo de segurança de rede para restringir o tráfego para as máquinas virtuais do Azure num conjunto com balanceamento de carga interno ou se for aplicado a uma sub-rede de rede virtual, certifique-se de que é adicionada uma regra de segurança de rede ao permitir tráfego a partir de 168.63.129.16.

## <a name="create-nics"></a>Criar NICs

Tem de criar NICs (ou modificar os existentes) e associá-los a regras NAT, regras de balanceador de carga e sondas.

1. Crie um NIC com o nome *lb-nic1-be* e associe-o à regra NAT *rdp1* e ao conjunto de endereços de back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    Resultado esperado:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Crie um NIC com o nome *lb-nic2-be* e associe-o à regra NAT *rdp2* e ao conjunto de endereços de back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Crie uma máquina virtual com o nome *DB1* e associe-a ao NIC com o nome *lb-nic1-be*. Foi criada uma conta de armazenamento denominada *web1nrp* antes de executar o seguinte comando:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > As VMs num balanceador de carga têm de estar no mesmo conjunto de disponibilidade. Utilize `azure availset create` para criar um conjunto de disponibilidade.

4. Crie uma máquina virtual (VM) com o nome *DB2* e associe-a ao NIC com o nome *lb-nic2-be*. Foi criada uma conta de armazenamento denominada *web1nrp* antes de executar o seguinte comando.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Eliminar um balanceador de carga

Para remover um balanceador de carga, utilize o seguinte comando:

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Passos seguintes

[Configurar um modo de distribuição de balanceador de carga com a afinidade do IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

