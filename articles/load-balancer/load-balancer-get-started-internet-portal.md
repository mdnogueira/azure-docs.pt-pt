---
title: "Criar um balanceador de carga com acesso à Internet no Resource Manager com o Portal do Azure | Microsoft Docs"
description: "Saiba como criar um balanceador de carga com acesso à Internet no Resource Manager com o Portal do Azure"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 196f9f002a1639867c210fc0dafdb783ed1e90af

---

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Criar um balanceador de carga com acesso à Internet com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [saber como criar um balanceador de carga com acesso à Internet com a implementação clássica](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Isto inclui a sequência de tarefas individuais que têm de ser feitas para criar um balanceador de carga e explicar detalhadamente o que está a ser feito para cumprir o objetivo.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>O que é preciso para criar um balanceador de carga com acesso à Internet?

Tem de criar e configurar os seguintes objetos para implementar um balanceador de carga.

* Configuração de IP front-end - contém os endereços IP públicos para o tráfego de rede recebido.
* Conjunto de endereços de back-end - contém interfaces de rede (NICs) para as máquinas virtuais receberem tráfego de rede do balanceador de carga.
* Regras de balanceamento de carga - contém as regras que mapeiam uma porta pública no balanceador de carga para a porta no conjunto de endereços de back-end.
* Regras NAT de entrada - contém as regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no conjunto de endereços de back-end.
* Sondas - contém sondas utilizadas para verificar a disponibilidade de instâncias das máquinas virtuais no conjunto de endereços de back-end.

Pode obter mais informações sobre os componentes do balanceador de carga com o Azure Resource Manager em [Suporte do Azure Resource Manager para o Load Balancer](load-balancer-arm.md).

## <a name="set-up-a-load-balancer-in-azure-portal"></a>Configurar um balanceador de carga no Portal do Azure

> [!IMPORTANT]
> Este exemplo assume que tem uma rede virtual chamada **myVNet**. Consulte [criar rede virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) para o fazer. Assume também que existe uma sub-rede em **myVNet** denominada **LB-Subnet-BE** e duas VMs denominadas **web1** e **web2** respetivamente, no mesmo conjunto de disponibilidade denominado **myAvailSet** no **myVNet**. Consulte [esta ligação](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para criar VMs.

1. Num browser, navegue para o Portal do Azure: [http://portal.azure.com](http://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. No canto superior esquerdo do ecrã, selecione **Novo** > **Rede** > **Balanceador de Carga.**
3. No painel **Criar balanceador de carga**, escreva um nome para o balanceador de carga. Aqui é designado por **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em **Endereço IP público**, crie um novo IP público denominado **myPublicIP**.
6. Em Grupo de Recursos, selecione **myRG**. Em seguida, selecione uma **Localização** adequada e clique em **OK**. O balanceador de carga irá então iniciar a implementação e irá demorar alguns minutos a concluir a implementação com êxito.

    ![A atualizar o grupo de recursos do balanceador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

1. Assim que o balanceador de carga for implementado com êxito, selecione-o a partir dos seus recursos. Em definições, selecione Conjuntos de Back-end. Escreva um nome para o conjunto de back-end. Em seguida, clique no botão **Adicionar**, na direção da parte superior do painel apresentado.
2. Clique em **Adicionar uma máquina virtual**, no painel **Adicionar conjunto de back-end**.  Selecione **Escolher um conjunto de disponibilidade** em **Conjunto de disponibilidade** e selecione **myAvailSet**. Em seguida, selecione **Escolher as máquinas virtuais** na secção Máquinas Virtuais no painel e clique em **web1** e **web2**, as duas VMs criadas para balanceamento de carga. Certifique-se de que ambas têm marcas de verificação azuis à esquerda, conforme mostrado na imagem abaixo. Em seguida, clique em **Selecionar** nesse painel, seguido de OK no painel **Escolher Máquinas virtuais** e, em seguida, **OK** no painel **Adicionar conjunto de back-end**.

    ![Adicionar ao conjunto de endereços back-end - ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Certifique-se de que as notificações na lista pendente têm uma atualização ao guardar o conjunto de back-end do balanceador de carga, além de atualizarem a interface de rede para ambas as VMs **web1** e **web2**.

## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Criar uma sonda, regra LB e regras NAT

1. Crie uma sonda de estado de funcionamento.

    Em Definições do balanceador de carga, selecione Sondas. Em seguida, clique em **Adicionar** na parte superior do painel.

    Existem duas formas de configurar uma pesquisa: HTTP ou TCP. Este exemplo mostra HTTP, mas o TCP pode ser configurado de forma semelhante.
    Atualize as informações necessárias. Conforme mencionado, o **myLoadBalancer** irá carregar o tráfego de balanceamento de carga na Porta 80. O caminho selecionado é HealthProbe.aspx, o Intervalo é 15 segundos e o Limiar de mau estado de funcionamento é 2. Assim que estiver concluído, clique em **OK** para criar a sonda.

    Faça pairar o ponteiro do rato sobre o ícone "i" para saber mais sobre estas configurações individuais e como podem ser alteradas para cumprirem os seus requisitos.

    ![Adicionar uma sonda](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Crie uma regra de balanceador de carga.

    Clique em Regras do Balanceador de carga, na secção Definições do seu balanceamento de carga. No novo painel, clique em **Adicionar**. Nomeie a sua regra. Aqui, é HTTP. Escolha a porta de front-end e a porta de Back-end. Aqui, é escolhido 80 para ambas. Escolher **LB-backend** como o conjunto de Back-end e o **HealthProbe** criado anteriormente como a Sonda. Podem ser definidas outras configurações de acordo com os requisitos. Em seguida, clique em OK para guardar a regra de balanceamento de carga.

    ![Adicionar uma regra de balanceamento de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Criar regras NAT de entrada

    Clique em Regras NAT de entrada, na secção definições do seu balanceamento de carga. No novo painel, clique em **Adicionar**. Em seguida, atribua um nome à regra NAT de entrada. Aqui é designado por **inboundNATrule1**. O destino deve ser o IP Público criado anteriormente. Selecione Personalizar, em Serviço e selecione o protocolo que pretende utilizar. Aqui está selecionado o TCP. Introduza a porta, 3441, e a Porta de destino, neste caso, 3389. em seguida, clique em OK para guardar esta regra.

    Depois da primeira regra estar criada, repita este passo para a segunda regra NAT de entrada, denominada inboundNATrule2 da porta 3442 à porta 3389 de Destino.

    ![Adicionar uma regra NAT de entrada](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Remover um Balanceador de Carga

Para eliminar um balanceador de carga, selecione o balanceador de carga que pretende remover. No painel *Balanceador de Carga*, clique em **Eliminar**, que se encontra na parte superior do painel. Em seguida, selecione **Sim** quando lhe for pedido.

## <a name="next-steps"></a>Passos seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO2-->


