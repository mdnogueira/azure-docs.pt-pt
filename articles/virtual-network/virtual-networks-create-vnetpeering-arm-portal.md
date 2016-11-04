---
title: Criar VNet Peering através do Portal do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual com o portal do Azure no Resource Manager.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar

---
# Criar um peering de rede virtual com o portal do Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet peering com base no cenário acima com o portal do Azure, siga os passos abaixo.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Para estabelecer o VNET peering, terá de criar duas ligações, uma para cada direção, entre duas VNets. Pode criar primeiro a ligação VNET peering da VNET1 para a VNET2. No portal, clique em **Procurar** > **escolha Redes Virtuais**
   
    ![Criar o VNet peering no portal do Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. No painel Redes Virtuais, selecione VNET1, clique em Peerings e clique depois em Adicionar
   
    ![Escolher peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. No painel Adicionar Peering, atribua um nome de ligação peering LinkToVnet2, selecione a subscrição e a Rede Virtual VNET2 e clique em OK.
   
    ![Ligar à VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Uma vez criada esta ligação VNET peering, pode ver o estado da ligação da seguinte forma:
   
    ![Estado da Ligação](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Crie depois a ligação VNET peering da VNET2 para a VNET1. No painel Redes Virtuais, selecione VNET2, clique em Peerings e clique depois em Adicionar
   
    ![Estabelecer peering a partir de outra VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. No painel Adicionar Peering, atribua um nome de ligação peering LinkToVnet1, selecione a subscrição e a Rede Virtual em modo de peering e clique em OK.
   
    ![Mosaico Criar rede virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Uma vez criada esta ligação VNET peering, pode ver o estado da ligação da seguinte forma:
   
    ![Estado da ligação final](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Verifique o estado de LinkToVnet2, que mudou também para Ligado.  
   
    ![Estado da ligação final 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)
   
   > [!NOTE]
   > O VNET Peering só será estabelecido se ambas as ligações estiverem ligadas.
   > 
   > 

Existem algumas propriedades configuráveis para cada ligação:

| Opção | Descrição | Predefinição |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Se o espaço de endereços da VNet em modo de peering vai ser incluído como parte da Etiqueta Virtual_network |Sim |
| AllowForwardedTraffic |Permite que o tráfego que não provém da VNet em modo de peering seja aceite ou removido |Não |
| AllowGatewayTransit |Permite que a VNet em modo de peering utilize o seu gateway de VNet |Não |
| UseRemoteGateways |Utilize o gateway da VNet em modo de peering. A VNet em modo de peering tem de ter um gateway configurado e AllowGatewayTransit deve estar selecionado. Não pode utilizar esta opção se tiver um gateway configurado |Não |

Cada ligação no VNet peering tem um conjunto das propriedades acima indicadas. No portal, pode clicar na Ligação VNet Peering e alterar as opções disponíveis. Clique em Guardar para que a alteração produza efeitos.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Neste exemplo utilizaremos duas subscrições, A e B, e dois utilizadores, UserA e UserB, com privilégios em cada uma das subscrições, respetivamente
3. No portal, clique em Procurar, escolha Redes Virtuais. Clique na VNET e clique em Adicionar.
   
    ![Procurar Cenário 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. No painel Adicionar acesso, clique em selecionar uma função e escolha Contribuinte de Rede, clique em Adicionar Utilizadores, escreva o nome de início de sessão do UserB e clique em OK.
   
    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)
   
    Isto não é um requisito. O peering pode ser estabelecido mesmo se os utilizadores emitirem individualmente pedidos de peering para as respetivas Vnets, desde que os pedidos correspondam. A adição do utilizador com privilégios da outra VNet como utilizador na VNet local torna mais fácil a configuração no portal.
5. Inicie depois sessão no portal do Azure com o UserB, que é o utilizador com privilégios para a SubscriptionB. Siga os passos acima para adicionar o UserA como Contribuinte de Rede.
   
    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)
   
   > [!NOTE]
   > Pode terminar e iniciar sessão em ambas as sessões de utilizador no browser para garantir que a autorização é ativada com êxito.
   > 
   > 
6. Inicie sessão no portal como UserA, navegue para o painel VNET3, clique em Peering, marque a caixa de verificação “Sei o ID de recurso” e escreva o ID do recurso para a VNET5 no formato abaixo.
   
    /subscriptions/<Subscription- ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/<VNET name>
   
    ![ID do Recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Inicie sessão no portal como UserB e siga os passos acima para criar a ligação peering da VNET5 para a VNet3.
   
    ![ID do Recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. O peering será estabelecido e qualquer máquina virtual na VNet3 deverá conseguir comunicar com qualquer máquina virtual na VNet5

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primeiro passo, o VNET peering estabelece uma ligação da HubVnet para a VNET1. Tenha em atenção que a opção Permitir tráfego reencaminhado não está selecionada para a ligação.
   
    ![Peering Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Como passo seguinte, podem ser criadas ligações de peering da VNET1 para a HubVnet. Tenha em atenção que a opção Permitir tráfego reencaminhado está selecionada.
   
    ![Peering Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Uma vez estabelecido o peering, pode consultar este [artigo](virtual-network-create-udr-arm-ps.md) e definir a Rota Definida pelo Utilizador (UDR) para redirecionar o tráfego da VNet1 através de uma aplicação virtual, de modo a utilizar as respetivas capacidades. Quando especificar o endereço do Próximo Salto na rota, pode configurá-lo para o endereço IP da aplicação virtual na VNet HubVNet em modo de peering

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Para estabelecer o VNET Peering neste cenário, apenas terá de criar uma ligação, da rede virtual no Azure Resource Manager para a clássica. Ou seja, de **VNET1** para **VNET2**. No portal, clique em **Procurar** > escolha **Redes Virtuais**
3. No painel Redes virtuais, escolha **VNET1**. Clique em **Peerings** e, em seguida, clique em **Adicionar**.
4. No painel Adicionar Peering, atribua um nome à sua ligação. Aqui, é designada **LinkToVNet2**. Em Detalhes do elemento de rede, selecione **Clássica**.
5. Em seguida, selecione a subscrição e a **VNET2** da Rede Virtual do elemento de rede. Em seguida, clique em OK.
   
    ![Ligar a Vnet1 à Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
6. Assim que for criada esta ligação do VNet Peering, as duas redes virtuais são colocadas em modo de peering e poderá ver o seguinte:
   
    ![Verificar a ligação de peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## Remover VNet Peering
1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Aceda ao painel Rede Virtual, clique em Peerings, clique na Ligação que pretende remover e clique no botão Eliminar.
   
   ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Depois de remover uma ligação no VNET peering, o estado da ligação de peering passa a desligado.
   
    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. Neste estado, não é possível recriar a ligação até que o estado da ligação de peering mude para Iniciado. Recomendamos que remova ambas as ligações antes de recriar o VNET peering.

<!--HONumber=Sep16_HO3-->


