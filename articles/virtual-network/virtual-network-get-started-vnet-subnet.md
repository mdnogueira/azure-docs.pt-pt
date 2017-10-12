---
title: Criar a sua primeira Rede Virtual do Azure | Microsoft Docs
description: "Saiba como criar uma Rede Virtual do Azure (VNet), ligar duas máquinas virtuais (VM) à VNet e ligar às VMs."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.openlocfilehash: e653764d7cb514d50b44fadd0cc5963dd404d99e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-virtual-network"></a>Criar a primeira rede virtual

Saiba como criar uma rede virtual (VNet) com duas sub-redes, criar duas máquinas virtuais (VM) e ligar cada VM a uma das sub-redes, conforme mostrado na imagem seguinte:

![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. Pode controlar as definições da sua rede do Azure e definir blocos de endereços DHCP, definições de DNS, políticas de segurança e encaminhamento. Para saber mais sobre os conceitos de VNet, veja o artigo [Descrição geral da Rede Virtual](virtual-networks-overview.md). Conclua os seguintes passos para criar os recursos mostrados na imagem:

1. [Criar uma VNet com duas sub-redes](#create-vnet)
2. [Criar duas VMs, cada uma delas com uma interface de rede (NIC)](#create-vms) e associar um grupo de segurança de rede (NSG) a cada NIC
3. [Ligar de e para as VMs](#connect-to-from-vms)
4. [Elimine todos os recursos](#delete-resources). Haverão custos para alguns dos recursos criados neste exercício, enquanto estiverem aprovisionados. Para minimizar os custos, depois de concluir o exercício, certifique-se de que conclui os passos nesta secção para eliminar os recursos que criou.

Ficará com uma compreensão básica de como pode utilizar uma VNet após concluir os passos neste artigo. Os passos seguintes são fornecidos para que possa saber mais sobre como utilizar VNets num nível mais profundo.

## <a name="create-vnet"></a>Criar uma rede virtual com duas sub-redes

Para criar uma rede virtual com duas sub-redes, conclua os passos que se seguem. Sub-redes diferentes são normalmente utilizadas para controlar o fluxo de tráfego entre sub-redes.

1. Inicie sessão no [Portal do Azure](<https://portal.azure.com>). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free). 
2. No painel **Favoritos** do portal, clique em **Novo**.
3. No painel **Novo**, clique em **Redes**. No painel **Redes**, clique em **Rede virtual**, conforme mostrado na imagem seguinte:

    ![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  No painel **Rede Virtual**, deixe *Resource Manager* selecionado como o modelo de implementação e clique em **Criar**.
5.  No **painel Criar rede virtual** apresentado, introduza os seguinte valores e clique em **Criar**:

    |**Definição**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyVNet*|O nome tem de ser exclusivo dentro do grupo de recursos.|
    |**Espaço de endereços**|*10.0.0.0/16*|Pode especificar qualquer espaço de endereços pretendido na notação CIDR.|
    |**Nome da sub-rede**|*Front-end*|O nome da sub-rede tem de ser exclusivo dentro da rede virtual.|
    |**Intervalo de endereços da sub-rede**|*10.0.0.0/24*| O intervalo que especificar tem de existir dentro do espaço de endereços que definiu para a rede virtual.|
    |**Subscrição**|*[A sua subscrição]*|Selecione uma subscrição na qual criar a VNet. Existe uma VNet dentro de uma subscrição única.|
    |**Grupo de recursos**|**Criar novo:** *MyRG*|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Localização**|*E.U.A. Oeste*| Normalmente, é selecionada a localização que estiver mais próxima da sua localização física.|

    A VNet demora alguns segundos a ser criada. Depois de estar criada, pode ver o dashboard do portal do Azure.

6. Com a rede virtual criada, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique na rede virtual **MyVNet** no painel **Todos os recursos**. Se a subscrição que selecionou já tiver vários recursos, pode introduzir *MyVNet* na caixa **Filtrar por nome...** para aceder facilmente à VNet.
7. O painel **MyVNet** abre-se e apresenta informações sobre a VNet, conforme mostrado na imagem seguinte:

    ![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Conforme mostrado na imagem anterior, clique em **Sub-redes** para apresentar uma lista das sub-redes dentro da VNet. A única sub-rede que existe é **Front-end**, a sub-rede que criou no passo 5.
9. No painel MyVNet - Sub-redes, clique em **+ Sub-rede** para criar uma sub-rede com as seguintes informações e clique em **OK** para criar a sub-rede:

    |**Definição**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*Back-end*|O nome tem de ser exclusivo dentro da rede virtual.|
    |**Intervalo de endereços**|*10.0.1.0/24*|O intervalo que especificar tem de existir dentro do espaço de endereços que definiu para a rede virtual.|
    |**Grupo de segurança de rede** e **Tabela de rota**|*Nenhum* (predefinição)|Os grupos de segurança de rede (NSG) serão abordados posteriormente neste artigo. Para saber mais sobre as rotas definidas pelo utilizador, veja o artigo [Rotas definidas pelo utilizador](virtual-networks-udr-overview.md).|

10. Depois de a nova sub-rede ser adicionada à VNet, pode fechar o painel **MyVNet – Sub-redes** e fechar o painel **Todos os recursos**.

## <a name="create-vms"></a>Criar máquinas virtuais

Com a VNet e as sub-redes criadas, pode criar as VMs. Neste exercício, ambas as VMs utilizam o sistema operativo Windows Server, embora possam ser executadas em qualquer sistema operativo suportado pelo Azure, incluindo várias distribuições do Linux.

### <a name="create-web-server-vm"></a>Criar a VM do servidor Web

Para criar a VM do servidor Web, execute os seguintes passos:

1. No painel de favoritos do portal do Azure, clique em **Novo**, **Computação** e, em seguida, em **Windows Server 2016 Datacenter**.
2. No painel **Windows Server 2016 Datacenter**, clique em **Criar**.
3. No painel **Básico** que é apresentado, introduza ou selecione os seguintes valores e clique em **OK**:

    |**Definição**| **Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyWebServer*|Esta VM funciona como um servidor Web ao qual os recursos de Internet ligam.|
    |**Tipo de disco da VM**|*SSD*|
    |**Nome de utilizador**|*A sua escolha*|
    |**Palavra-passe e Confirmar palavra-passe**|*A sua escolha*|
    | **Subscrição**|*<Your subscription>*|A subscrição tem de ser a mesma subscrição que selecionou no passo 5 da secção [Criar uma rede virtual com duas sub-redes](#create-vnet) deste artigo. A VNet à qual ligar uma VM tem de existir na mesma subscrição da VM.|
    |**Grupo de recursos**|**Utilizar existente:** selecione *MyRG*|Embora estejamos a utilizar o mesmo grupo de recursos, tal como fizemos para a VNet, os recursos não têm de existir no mesmo grupo de recursos.|
    |**Localização**|*E.U.A. Oeste*|A localização tem de ser a mesma localização que especificou no passo 5 da secção [Criar uma rede virtual com duas sub-redes](#create-vnet) deste artigo. As VMs e as VNets às quais as sub-redes ligam têm de existir na mesma localização.|

4. No painel **Escolher um tamanho**, clique em *DS1_V2 Standard* e clique em **Selecionar**. Veja o artigo [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter uma lista de todos os tamanhos de VM do Windows suportados pelo Azure.
5. No painel **Definições**, introduza ou selecione os seguintes valores e clique em **OK**:

    |**Definição**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Armazenamento: utilizar discos geridos**|*Sim*||
    |**Rede virtual**| Selecionar *MyVNet*|Pode selecionar qualquer VNet que exista na mesma localização da VM que está a criar. Para saber mais sobre VNets e sub-redes, veja o artigo [Rede Virtual](virtual-networks-overview.md).|
    |**Sub-rede**|Selecionar *front-end*|Pode selecionar qualquer sub-rede que exista dentro da VNet.|
    |**Endereço IP público**|Aceitar a predefinição|Um endereço IP público permite-lhe ligar à VM a partir da Internet. Para saber mais sobre endereços IP públicos, veja o artigo [Endereços IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Grupo de segurança de rede (firewall)**|Aceitar a predefinição|Clique no NSG **(novo) MyWebServer-nsg** predefinido que o portal criou, para ver as respetivas definições. No painel **Criar grupo de segurança de rede** que se abre, repare que tem uma regra de entrada que permite tráfego de TCP/3389 (RDP) de qualquer endereço IP de origem.|
    |**Todos os outros valores**|Aceitar todas as predefinições|Para saber mais sobre as restantes definições, veja o artigo [Sobre as VMs](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    Os grupos de segurança de rede (NSG) permitem-lhe criar regras de entrada/saída para o tipo de tráfego de rede que pode circular de e para a VM. Por predefinição, todo o tráfego de entrada para a VM é negado. Pode adicionar regras de entrada adicionais para TCP/80 (HTTP) e TCP/443 (HTTPS) para um servidor Web de produção. Não existe nenhuma regra para o tráfego de saída porque, por predefinição, todo o tráfego de saída é permitido. Pode adicionar/remover regras para controlar o tráfego de acordo com as suas políticas. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre os NSGs.

6.  No painel **Resumo**, reveja as definições e clique em **OK** para criar a VM. Um mosaico de estado é apresentado no dashboard do portal, à medida que a VM é criada. A criação pode demorar alguns minutos. Não terá de aguardar que esta seja concluída. Pode continuar para o passo seguinte enquanto a VM está a ser criada.

### <a name="create-database-server-vm"></a>Criar a VM do servidor de bases de dados

Para criar a VM do servidor de bases de dados, execute os seguintes passos:

1.  No painel Favoritos, clique em **Novo**, **Computação** e, em seguida, em **Windows Server 2016 Datacenter**.
2.  No painel **Windows Server 2016 Datacenter**, clique em **Criar**.
3.  No **painel Básico**, introduza ou selecione os seguintes valores e clique em **OK**:

    |**Definição**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyDBServer*|Esta VM funciona como um servidor de bases de dados ao qual o servidor Web liga, mas ao qual a Internet não consegue ligar.|
    |**Tipo de disco da VM**|*SSD*||
    |**Nome de utilizador**|A sua escolha||
    |**Palavra-passe e Confirmar palavra-passe**|A sua escolha||
    |**Subscrição**|<Your subscription>|A subscrição tem de ser a mesma subscrição que selecionou no Passo 5 da secção [Criar uma rede virtual com duas sub-redes](#create-vnet) deste artigo.|
    |**Grupo de recursos**|**Utilizar existente:** selecione *MyRG*|Embora estejamos a utilizar o mesmo grupo de recursos, tal como fizemos para a VNet, os recursos não têm de existir no mesmo grupo de recursos.|
    |**Localização**|*E.U.A. Oeste*|A localização tem de ser a mesma localização que especificou no passo 5 da secção [Criar uma rede virtual com duas sub-redes](#create-vnet) deste artigo.|

4.  No painel **Escolher um tamanho**, clique em *DS1_V2 Standard* e clique em **Selecionar**.
5.  No painel **Definições**, introduza ou selecione os seguintes valores e clique em **OK**:

    |**Definição**|**Valor**|**Detalhes**|
    |----|----|---|
    |**Armazenamento: utilizar discos geridos**|*Sim*||
    |**Rede virtual**|Selecionar *MyVNet*|Pode selecionar qualquer VNet que exista na mesma localização da VM que está a criar.|
    |**Sub-rede**|Selecione *Back-end* ao clicar na caixa **Sub-rede** e, em seguida, selecione **Back-end** no painel **Escolher uma sub-rede**|Pode selecionar qualquer sub-rede que exista dentro da VNet.|
    |**Endereço IP público**|Nenhum – clique no endereço predefinido e, em seguida, clique em **Nenhum** no painel **Escolher endereço IP público**|Sem um endereço IP público, só pode ligar à VM a partir de outra VM ligada à mesma VNet. Não é possível ligar à mesma diretamente da Internet.|
    |**Grupo de segurança de rede (firewall)**|Aceitar a predefinição| Como no NSG predefinido criado para a VM MyWebServer, este NSG também tem a mesma regra de entrada predefinida. Pode adicionar uma regra de entrada adicional para TCP/1433 (MS SQL) para um servidor de bases de dados. Não existe nenhuma regra para o tráfego de saída porque, por predefinição, todo o tráfego de saída é permitido. Pode adicionar/remover regras para controlar o tráfego de acordo com as suas políticas.|
    |**Todos os outros valores**|Aceitar todas as predefinições||

6.  No painel **Resumo**, reveja as definições e clique em **OK** para criar a VM. Um mosaico de estado é apresentado no dashboard do portal, à medida que a VM é criada. A criação pode demorar alguns minutos. Não terá de aguardar que esta seja concluída. Pode continuar para o passo seguinte enquanto a VM está a ser criada.

## <a name="review"></a>Rever recursos

Se tiver criado uma VNet e duas VMs, o portal do Azure terá criado vários recursos adicionais por si no grupo de recursos MyRG. Reveja o conteúdo do grupo de recursos MyRG, concluindo os seguintes passos:

1. No painel **Favoritos**, clique em **Mais serviços**.
2. No painel **Mais serviços**, escreva *Grupos de recursos* na caixa com a palavra *Filtro*. Clique em **Grupos de recursos** quando o vir na lista filtrada.
3. No painel **Grupos de recursos**, clique no grupo de recursos *MyRG*. Se tiver muitos grupos de recursos na sua subscrição, pode escrever *MyRG* na caixa que contém o texto *Filtrar por nome...* para aceder rapidamente ao grupo de recursos MyRG.
4.  No painel **MyRG**, pode ver que o grupo de recursos contém 12 recursos, conforme mostrado na imagem seguinte:

    ![Conteúdos do grupo de recursos](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Para saber mais sobre VMs, discos e contas de armazenamento, veja os artigos de descrição geral [Máquina Virtual](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Disco](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json), e [Conta de armazenamento](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pode ver os dois NSGs predefinidos que o portal criou por si. Também pode ver que o portal criou dois recursos de interface de rede (NIC). Uma NIC permite a uma VM ligar a outros recursos através da VNet. Veja o artigo [NIC](virtual-network-network-interface.md) para saber mais sobre NICs. O portal também criou um recurso de endereço IP público. Os endereços IP públicos são uma definição para um recurso de endereço IP público. Para saber mais sobre endereços IP públicos, veja o artigo [Endereços IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Ligar às VMs

Com a sua VNet e duas VMs criadas, pode agora ligar às VMs, concluindo os passos nas secções seguintes:

### <a name="connect-from-internet"></a>Ligar à VM do servidor Web a partir da Internet

Para ligar à VM do servidor Web a partir da Internet, conclua os seguintes passos:

1. No portal, abra o grupo de recursos MyRG, concluindo os passos na secção [Rever recursos](#review) deste artigo.
2. No painel **MyRG**, clique na VM **MyWebServer**.
3. No painel **MyWebServer**, clique em **Ligar**, conforme mostrado na imagem seguinte:

    ![Ligar à VM do servidor Web](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Permita que o browser transfira o ficheiro *MyWebServer.rdp* e abra-o.
5. Se receber uma caixa de diálogo informando-o de que não é possível verificar o publicador da ligação remota, clique em **Ligar**.
6. Ao introduzir as suas credenciais, certifique-se de que inicia sessão com o nome de utilizador e a palavra-passe que especificou no passo 3 da secção [Criar a VM do servidor Web](#create-web-server-vm) deste artigo. Se a caixa **Segurança do Windows** que é apresentada não listar as credenciais corretas, poderá ter de clicar em **Mais opções** e **Utilizar uma conta diferente** para poder especificar o nome de utilizador e a palavra-passe corretos. Clique em **OK** para ligar à VM.
7. Se receber uma caixa **Ligação de Ambiente de Trabalho Remoto** informando-o de que não é possível verificar a identidade do computador remoto, clique em **Sim**.
8. Está agora ligado à VM MyWebServer a partir da Internet. Deixe a ligação de ambiente de trabalho remoto aberta para concluir os passos na secção seguinte.

A ligação remota é estabelecida para o endereço IP público atribuído ao recurso de endereço IP público que o portal criou no passo 5 da secção [Criar uma rede virtual com duas sub-redes](#create-vnet) deste artigo. A ligação é permitida porque a regra predefinida criada no NSG **MyWebServer-nsg** permitiu a entrada de TCP/3389 (RDP) para a VM a partir de qualquer endereço IP de origem. Se tentar ligar à VM através de qualquer outra porta, a ligação falha, a menos que adicione regras de entrada adicionais para o NSG, permitindo as portas adicionais.

>[!NOTE]
>Se adicionar regras de entrada adicionais para o NSG, certifique-se de que as mesmas portas estão abertas na firewall do Windows, ou a ligação irá falhar.
>

### <a name="connect-to-internet"></a>Ligar à Internet a partir da VM do servidor Web

Para ligar a saída para a Internet a partir da VM do servidor Web, conclua os seguintes passos:

1. Se ainda não tiver uma ligação remota aberta para a VM MyWebServer, estabeleça uma ligação remota à VM, concluindo os passos na secção [Ligar à VM do servidor Web a partir da Internet](#connect-from-internet) deste artigo.
2. No ambiente de trabalho do Windows, abra o Internet Explorer. Na caixa de diálogo **Configurar o Internet Explorer 11**, clique em **Não utilizar definições recomendadas** e clique em **OK**. É recomendado aceitar as definições recomendadas para um servidor de produção.
3. Na barra de endereço do Internet Explorer, introduza [bing.com](http:www.bing.com). Se receber uma caixa de diálogo do Internet Explorer, clique em **Adicionar** e, em seguida, em **Adicionar** na caixa de diálogo **Sites fidedignos**, e clique em **Fechar**. Repita este processo para quaisquer outras caixas de diálogo do Internet Explorer.
4. Na página de pesquisa do Bing, introduza *whatsmyipaddress* e clique no botão da lupa. O Bing devolve o endereço IP público atribuído ao recurso de endereço IP público criado pelo portal quando criou a VM. Se examinar as definições do recurso **MyWebServer-ip**, pode ver o mesmo endereço IP atribuído ao recurso de endereço IP público, conforme mostrado na imagem que se segue. No entanto, o endereço IP atribuído à sua VM é diferente.

    ![Ligar à VM do servidor Web](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Deixe a ligação de ambiente de trabalho remoto aberta para concluir os passos na secção seguinte.

Consegue ligar à Internet a partir da VM porque toda a conectividade de saída da VM é permitida por predefinição. Pode limitar a conectividade de saída ao adicionar regras de adição para o NSG aplicadas à NIC, para a sub-rede à qual a NIC está ligada, ou ambos.

Se a VM for colocada no estado parado (desalocada) através do portal, o endereço IP público pode mudar. Se necessitar que o endereço IP público nunca mude, pode utilizar o método de alocação estático para o endereço IP, em vez do método de alocação dinâmico (que é a predefinição). Para saber mais sobre as diferenças entre os métodos de alocação, veja o artigo [Tipos e métodos de alocação de endereços IP](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Ligar à VM do servidor de bases de dados a partir da VM do servidor Web

Para ligar à VM do servidor de bases de dados a partir da VM do servidor Web, conclua os seguintes passos:

1. Se ainda não tiver uma ligação remota aberta para a VM MyWebServer, estabeleça uma ligação remota à VM, concluindo os passos na secção [Ligar à VM do servidor Web a partir da Internet](#connect-from-internet) deste artigo.
2. Clique no botão Iniciar no canto inferior esquerdo do ambiente de trabalho do Windows e comece a escrever *ambiente de trabalho remoto*. Quando a lista de menu Iniciar apresentar **Ligação ao Ambiente de Trabalho Remoto**, clique na mesma.
3. Na caixa de diálogo **Ligação ao Ambiente de Trabalho Remoto**, introduza *MyDBServer* para o nome do computador e clique em **Ligar**.
4. Introduza o nome de utilizador e as palavras-passe que introduziu no passo 3 da secção [Criar a VM do servidor de bases de dados](#create-database-server-vm) deste artigo e clique em **OK**.
5. Se receber uma caixa de diálogo informando-o de que não é possível verificar a identidade do computador remoto, clique em **Sim**.
6. Deixe a ligação de ambiente de trabalho remoto para os dois servidores aberta para concluir os passos na secção seguinte.

É possível estabelecer a ligação à VM do servidor de bases de dados a partir da VM do servidor Web pelos seguintes motivos:

- As ligações de entrada de TCP/3389 estão ativadas para qualquer IP de origem no NSG predefinido criado no passo 5 da secção [Criar a VM do servidor de bases de dados](#create-database-server-vm) deste artigo.
- Iniciou a ligação a partir da VM do servidor Web, que está ligada à mesma VNet da VM do servidor de bases de dados. Para ligar a uma VM que não tem um endereço IP público atribuído, tem de ligar a partir de outra VM ligada à mesma VNet, mesmo se a VM estiver ligada a uma sub-rede diferente.
- Apesar de as VMs estarem ligadas a sub-redes diferentes, o Azure cria rotas predefinidas que permitem a conectividade entre sub-redes. No entanto, pode substituir as rotas predefinidas ao criar a sua própria rota. Veja o artigo [Rotas definidas pelo utilizador](virtual-networks-udr-overview.md) para saber mais sobre o encaminhamento do Azure.

Se tentar iniciar uma ligação remota à VM do servidor de bases de dados a partir da Internet, como fez na secção [Ligar à VM do servidor Web a partir da Internet](#connect-from-internet) deste artigo, verá que a opção **Ligar** está a cinzento. A ligação está a cinzento porque não existe nenhum endereço IP público atribuído à VM, pelo que não são possíveis ligações de entrada da mesma a partir da Internet.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Ligar à Internet a partir da VM do servidor de bases de dados

Ligue a saída à Internet a partir da VM do servidor de bases de dados, concluindo os seguintes passos:

1. Se ainda não tiver aberto uma ligação remota à VM MyDBServer a partir da VM MyWebServer, conclua os passos na secção [Ligar à VM do servidor de bases de dados a partir da VM do servidor Web](#webserver-to-dbserver) deste artigo.
2. No ambiente de trabalho do Windows, na VM MyDBServer, abra o Internet Explorer e responda às caixas de diálogo como fez nos passos 2 e 3 da secção [Ligar à Internet a partir da VM do servidor Web](#connect-to-internet) deste artigo.
3. Na barra de endereço, introduza [bing.com](http:www.bing.com).
4. Clique em **Adicionar** na caixa de diálogo do Internet Explorer que é apresentada e, em seguida, em **Adicionar** e, em seguida, em **Fechar** na caixa de diálogo de sites **Fidedignos**. Conclua estes passos em quaisquer caixas de diálogo adicionais apresentadas.
5. Na página de pesquisa do Bing, introduza *whatsmyipaddress* e clique no botão da lupa. O Bing devolve o endereço IP público atualmente atribuído à VM pela infraestrutura do Azure. 6. Feche o ambiente de trabalho remoto para a VM MyDBServer a partir da VM MyWebServer e, em seguida, feche a ligação remota à VM MyWebServer.

A ligação de saída à Internet é permitida porque todo o tráfego de saída é permitido por predefinição, mesmo se um recurso de endereço IP público não estiver atribuído à VM MyDBServer. Todas as VMs, por predefinição, são capazes de estabelecer uma ligação de saída à Internet, com ou sem um recurso de endereço IP público atribuído à VM. No entanto, não é possível ligar ao endereço IP público a partir da Internet, como era possível para a VM MyWebServer, que tem um recurso de endereço IP público atribuído.

## <a name="delete-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, conclua os seguintes passos:

1. Para ver o grupo de recursos MyRG criado neste artigo, conclua os passos 1-3 na secção [Rever recursos](#review) deste artigo. Mais uma vez, reveja os recursos do grupo de recursos. Se criou o grupo de recursos MyRG, conforme os passos anteriores, verá os 12 recursos mostrados na imagem do passo 4.
2. No painel MyRG, clique no botão **Eliminar**.
3. O portal requer que escreva o nome do grupo de recursos para confirmar que pretende eliminá-lo. Se vir recursos além dos recursos mostrados no passo 4 da secção [Rever recursos](#review) deste artigo, clique em **Cancelar**. Se vir apenas 12 recursos criados como parte deste artigo, escreva *MyRG* para o nome do grupo de recursos e clique em **Eliminar**. A eliminação de um grupo de recursos elimina todos os recursos dentro do grupo de recursos. Por conseguinte, confirme os conteúdos de um grupo de recursos antes de o eliminar. O portal elimina todos os recursos contidos dentro do grupo de recursos e, em seguida, elimina o próprio grupo de recursos. Este processo demora vários minutos.

## <a name="next-steps"></a>Passos seguintes

Neste exercício, criou uma VNet e duas VMs. Especificou algumas definições personalizadas durante a criação da VM e aceitou várias predefinições. Recomendamos que leia os artigos seguintes, antes de implementar VNets e VMs de produção, para garantir que compreende todas as definições disponíveis:

- [Redes virtuais](virtual-networks-overview.md)
- [Endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Interfaces de rede](virtual-network-network-interface.md)
- [Grupos de segurança de rede](virtual-networks-nsg.md)
- [Máquinas virtuais](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
