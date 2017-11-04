---
title: "Configurar endereços IP para uma interface de rede do Azure | Microsoft Docs"
description: "Saiba como adicionar, alterar e remova endereços IP públicos e privados para uma interface de rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 254d5d43f0f665f64ddfe276fe31702f66f16758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Adicionar, alterar ou remover os endereços IP para uma interface de rede do Azure

Saiba como adicionar, alterar e remova endereços IP públicos e privados para uma interface de rede. Endereços de IP privado atribuídos a uma interface de rede ativar uma máquina virtual para comunicar com outros recursos numa Azure virtual network e redes ligadas. Um endereço IP privado também permite a comunicação de saída à Internet através de um endereço IP imprevisível. A [endereço IP público](virtual-network-public-ip-address.md) atribuídos a uma rede interface permite comunicação de entrada para uma máquina virtual através da Internet. O endereço também permite a comunicação de saída da máquina virtual à Internet através de um endereço IP previsível. Para obter mais informações, consulte [Noções sobre ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Se precisar de criar, alterar ou eliminar uma interface de rede, leia o [gerir uma interface de rede](virtual-network-network-interface.md) artigo. Se precisar de interfaces de rede para adicionar ou remover interfaces de rede a partir de uma máquina virtual, leia o [interfaces de rede de adicionar ou remover](virtual-network-network-interface-vm.md) artigo. 


## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Reveja o [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo para saber mais sobre os limites de endereços IP públicos e privados.
- Inicie sessão para o Azure [portal](https://portal.azure.com), interface de linha de comandos (CLI) do Azure ou o Azure PowerShell com uma conta do Azure. Se ainda não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente dos mini-comandos de Azure PowerShell instaladas. Para obter ajuda para comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente da CLI do Azure instalados. Para obter ajuda para comandos da CLI, escreva `az <command> --help`. Em vez de instalar a CLI e respetivos pré-requisitos, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem **> _** na parte superior da parte a [portal](https://portal.azure.com).

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Pode adicionar como muitas [privada](#private) e [pública](#public) [IPv4](#ipv4) endereços conforme necessário para uma interface de rede, dentro dos limites indicados no [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo. Não é possível utilizar o portal para adicionar um endereço IPv6 para uma interface de rede existentes (embora, pode utilizar o portal para adicionar um endereço IPv6 privado para uma interface de rede, ao criar a interface de rede). Pode utilizar o PowerShell ou a CLI para adicionar um endereço IPv6 privado para uma [configuração de IP secundária](#secondary) (desde que não existem nenhum configurações de IP secundárias existentes) para uma interface de rede existente que não esteja anexada a uma máquina virtual. Não é possível utilizar qualquer ferramenta para adicionar um endereço IPv6 público para uma interface de rede. Consulte [IPv6](#ipv6) para obter detalhes sobre a utilização de endereços IPv6. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja atribuídas (no mínimo) permissões para a função de contribuinte de rede para a sua subscrição. Leia o [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artigo para obter mais informações sobre a atribuição de funções e permissões a contas.
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **interfaces de rede** painel apresentado, clique em que pretende adicionar um endereço IPv4 para a interface de rede.
4. Clique em **configurações de IP** no **definições** secção do painel para a interface de rede que selecionou.
5. Clique em **+ adicionar** no painel que abre para configurações de IP.
6. Especifique o seguinte, em seguida, clique em **OK** para fechar o **configuração de IP adicionar** painel:

    |Definição|Necessário?|Detalhes|
    |---|---|---|
    |Nome|Sim|Tem de ser exclusivo para a interface de rede|
    |Tipo|Sim|Uma vez que estiver a adicionar uma configuração de IP para uma interface de rede existente e cada interface de rede tem de ter um [primário](#primary) é a única opção de configuração de IP, **secundário**.|
    |Método de atribuição de endereço IP privado|Sim|[**Dinâmica** ](#dynamic) endereços podem alterar se a máquina virtual for reiniciada após ter sido no estado parado (desalocado). Azure atribui um endereço disponível a partir do espaço de endereços da sub-rede que a interface de rede está ligada à. [**Estático** ](#static) endereços não são lançados até que a interface de rede é eliminada. Especifique um endereço IP do intervalo de espaço de endereço sub-rede que não está atualmente a ser utilizado por outra configuração de IP.|
    |Endereço IP público|Não|**Desativado:** nenhum recurso de endereço IP público está atualmente associado à configuração de IP. **Ativado:** selecione um endereço IPv4 de IP público existente ou crie um novo. Para saber como criar um endereço IP público, leia o [endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address) artigo.|
7. Adicionar manualmente secundários endereços IP privados para o sistema operativo da máquina virtual, efetuando as instruções de [atribuir vários endereços IP para sistemas operativos de máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config) artigo. Consulte [privada](#private) endereços IP para considerações especiais antes de adicionar manualmente os endereços IP para um sistema de operativo da máquina virtual. Não adicione quaisquer endereços IP públicos para o sistema operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[Criar nic de rede AZ ip-config](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[AzureRmNetworkInterfaceIpConfig adicionar](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Alterar definições do endereço IP

Pode precisar de alterar o método de atribuição de um endereço IPv4, alterar o endereço IPv4 estático, ou alterar o endereço IP público atribuído a uma interface de rede. Se estiver a alterar o endereço IPv4 privado de uma configuração de IP secundária associada uma interface de rede secundárias numa máquina virtual (Saiba mais sobre [interfaces de rede principal e secundário](virtual-network-network-interface-vm.md#about)), coloque a máquina virtual no estado parado (desalocado) antes de concluir os seguintes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja atribuídas (no mínimo) permissões para a função de contribuinte de rede para a sua subscrição. Leia o [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artigo para obter mais informações sobre a atribuição de funções e permissões a contas.
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **interfaces de rede** painel apresentado, clique em que pretende ver ou alterar as definições de endereço IP para a interface de rede.
4. Clique em **configurações de IP** no **definições** secção do painel para a interface de rede que selecionou.
5. Clique em configuração de IP que pretende modificar a partir da lista no painel que abre para configurações de IP.
6. Alterar as definições, conforme pretendido, utilizando as informações sobre as definições no passo 6 do [adicionar uma configuração de IP](#create-ip-config) secção deste artigo. Clique em **guardar** para fechar o painel para a configuração de IP é alterado.

>[!NOTE]
>Se a interface de rede principal tem várias configurações de IP e altere o endereço IP privado de configuração de IP primária, tem de reatribuir manualmente os endereços IP primários e secundários para a interface de rede no Windows (não é necessário para Linux) . Para atribuir manualmente os endereços IP a uma interface de rede dentro de um sistema operativo, leia o [atribuir vários endereços IP às máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config) artigo. Consulte [privada](#private) endereços IP para considerações especiais antes de adicionar manualmente os endereços IP para um sistema de operativo da máquina virtual. Não adicione quaisquer endereços IP públicos para o sistema operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[atualização de configuração de ip da nic de rede de AZ](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Conjunto AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Remova endereços IP

Pode remover [privada](#private) e [pública](#public) endereços IP da interface de rede, mas uma interface de rede tem sempre de ter pelo menos um endereço IPv4 privado atribuído.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja atribuídas (no mínimo) permissões para a função de contribuinte de rede para a sua subscrição. Leia o [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artigo para obter mais informações sobre a atribuição de funções e permissões a contas.
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **interfaces de rede** painel que aparece, clique em que a interface de rede que pretende remover o IP, endereços de.
4. Clique em **configurações de IP** no **definições** secção do painel para a interface de rede que selecionou.
5. Com o botão direito um [secundário](#secondary) configuração de IP (não é possível eliminar o [primário](#primary) configuração) que pretende eliminar, clique em **eliminar**, em seguida, clique em **Sim** para confirmar a eliminação. Se a configuração tinha um recurso de endereço IP público associado ao mesmo, o recurso é desassociado da configuração de IP, mas o recurso não é eliminado.
6. Fechar o **configurações de IP** painel.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[eliminação de configuração de ip do AZ rede nic](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remover AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>Configurações de IP

[Privada](#private) e (opcionalmente) [pública](#public) endereços IP são atribuídos a uma ou mais configurações de IP atribuídas a uma interface de rede. Existem dois tipos de configurações de IP:

### <a name="primary"></a>Primária

Cada interface de rede é atribuído uma configuração de IP primária. Uma configuração de IP primária:

- Tem um [privada](#private) [IPv4](#ipv4) endereço atribuído à mesma. Não é possível atribuir uma privada [IPv6](#ipv6) endereço para uma configuração de IP primária.
- Também pode ter um [pública](#public) endereço de IPv4 atribuído ao mesmo. Não é possível atribuir um endereço IPv6 público para uma configuração de IP primária ou secundária. No entanto, pode atribuir um endereço IPv6 público para um balanceador de carga do Azure, pode carregar equilibrar o tráfego para o endereço de IPv6 privado de uma máquina virtual. Para obter mais informações, consulte [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Secundária

Para além de uma configuração de IP primária, uma interface de rede pode ter zero ou mais secundárias configurações de IP atribuídas ao mesmo. Uma configuração de IP secundária:

- Tem de ter um endereço IPv4 ou IPv6 privado atribuído. Se o endereço IPv6, a interface de rede só pode ter uma configuração de IP secundária. Se o endereço IPv4, a interface de rede pode ter várias configurações de IP secundárias atribuídas. Para obter mais informações sobre quantas endereços de IPv4 privados e públicos podem ser atribuídos a uma interface de rede, consulte o [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo.  
- Pode também ter um endereço IPv4 público atribuídas ao mesmo, se o endereço IP privado é IPv4. Se o endereço IP privado IPv6, não é possível atribuir um endereço IPv4 ou IPv6 público à configuração de IP. Atribuir vários endereços IP a uma interface de rede é útil em cenários tais como:
    - Alojar vários Web Sites ou serviços com diferentes endereços IP e certificados SSL num único servidor.
    - Uma máquina virtual que servem como uma aplicação virtual de rede, tais como uma firewall ou Balanceador de carga.
    - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer uma das interfaces de rede para um conjunto de back-end de Balanceador de carga do Azure. No passado, apenas o endereço IPv4 principal para a interface de rede principal foi possível adicionar a um conjunto de back-end. Para saber mais sobre como o balanceamento de carga múltiplas configurações de IPv4, consulte o [várias configurações de IP de balanceamento de carga](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. 
    - A capacidade de carregar o balanceamento de um endereço de IPv6 atribuído a uma interface de rede. Para obter mais informações sobre como balancear a um endereço IPv6 privado, consulte o [equilibrar os endereços IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.


## <a name="address-types"></a>Tipos de endereço

Pode atribuir os seguintes tipos de endereços IP a um [configuração de IP](#ip-configurations):

### <a name="private"></a>Privado

Privada [IPv4](#ipv4) endereços ativar uma máquina virtual para comunicar com outros recursos numa rede virtual ou outras redes ligadas. Uma máquina virtual não é possível comunicar entrada para, nem pode a máquina virtual comunicar saída com privado [IPv6](#ipv6) endereço, com uma exceção. Uma máquina virtual podem comunicar com o Balanceador de carga do Azure utilizando um endereço IPv6. Para obter mais informações, consulte [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Por predefinição, os servidores Azure DHCP atribuir o endereço IPv4 privado para o [configuração de IP primária](#primary) da interface de rede para a interface de rede dentro do sistema de operativo da máquina virtual. Se necessário, manualmente nunca deverá definir o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual. 

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de uma interface de rede no sistema de operativo de uma máquina virtual nunca diferente o endereço IPv4 privado atribuído à configuração de IP primária da interface de rede principal ligado a uma máquina virtual no Azure, poderá perder a conectividade à máquina virtual.

Existem cenários onde é necessário definir o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual manualmente. Por exemplo, tem de definir manualmente os endereços IP primários e secundários de um sistema operativo Windows quando adicionar vários endereços IP a uma máquina virtual do Azure. Para uma máquina virtual do Linux, apenas poderá ter de definir manualmente os endereços IP secundários. Consulte [endereços IP de adicionar a um sistema de operativo VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter mais detalhes. Quando definir o endereço IP no sistema operativo manualmente, é recomendado que atribua sempre os endereços para a configuração de IP para uma interface de rede utilizando o método de atribuição de estático (em vez de dinâmica). Atribuir o endereço utilizando o método estático assegura que o endereço não é alterado no Azure. Se alguma vez precisar de alterar o endereço atribuído a uma configuração de IP, é recomendado que tem:

1. Certifique-se de que a máquina virtual está a receber um endereço de servidores DHCP do Azure, altere a atribuição do endereço IP para DHCP no sistema operativo e reinicie a máquina virtual.
2. Parar (desalocar) a máquina virtual.
3. Altere o endereço IP para a configuração de IP no Azure.
4. Inicie a máquina virtual.
5. [Configurar manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operativo (e também o endereço IP primário no Windows) para corresponder ao que definiu no Azure.
 
Ao seguir os passos anteriores, o endereço IP privado atribuído à interface de rede no Azure e no sistema de operativo de uma máquina virtual, se alteram. Para controlar as máquinas virtuais na sua subscrição que definiu manualmente endereços IP dentro de um sistema operativo, considere adicionar um Azure [tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) às máquinas virtuais. Poderá utilizar "atribuição de endereços IP: estático", por exemplo. Desta forma, pode localizar facilmente as máquinas virtuais na sua subscrição que definiu manualmente do endereço IP no sistema operativo.

Para além de ativarem uma máquina virtual para comunicar com outros recursos dentro dos mesmos, ou ligados redes virtuais, um endereço IP privado também permite que uma máquina virtual para comunicar a saída à Internet. Ligações de saída são convertido pelo Azure para um endereço IP público imprevisível endereço de rede de origem. Para obter mais informações acerca da conetividade de Internet de saída do Azure, leia o [conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. Não é possível comunica entrada para o endereço IP privado de uma máquina virtual através da Internet.

### <a name="public"></a>Público

Endereços IP públicos ativar a conetividade de entrada para uma máquina virtual através da Internet. Ligações de saída à Internet, utilize um endereço IP previsível. Consulte [Noções sobre ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais detalhes. Pode atribuir um endereço IP público para uma configuração de IP, mas não são necessários para. Se não atribuir um endereço IP público para uma máquina virtual, ainda pode comunicar saída à Internet utilizando o respetivo endereço IP privado. Para saber mais sobre os endereços IP públicos, leia o [endereço IP público](virtual-network-public-ip-address.md) artigo.

Existem limites para o número de endereços IP públicos e privados que pode atribuir a uma interface de rede. Para obter mais informações, leia o [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo.

> [!NOTE]
> Azure traduz endereço IP privado de uma máquina virtual para um endereço IP público. Como resultado, o sistema operativo não tem conhecimento do quaisquer endereços IP públicos atribuídos ao mesmo, pelo que não é necessário atribuir alguma vez manualmente um endereço IP público no sistema operativo.

## <a name="assignment-methods"></a>Métodos de atribuição

Endereços IP públicos e privados atribuídos, utilizando os seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmica

IPv4 privada dinâmico e IPv6 (opcionalmente) endereços são atribuídos por predefinição. Podem alterar a endereços dinâmicos se a máquina virtual é colocada em estado de parada (desalocado), em seguida, iniciada. Se não quiser endereços IPv4 para alterar o de vida da máquina virtual, atribua os endereços utilizando o método estático. Só é possível atribuir um endereço IPv6 privado utilizando o método de atribuição dinâmico. Não é possível atribuir um endereço IPv6 público para uma configuração de IP utilizando um dos métodos.

### <a name="static"></a>Estático

Endereços atribuídos utilizando o método estático não alterar até que uma máquina virtual é eliminada. Atribuir manualmente um IPv4 privado estático endereço para uma configuração de IP do espaço de endereço para a sub-rede a interface de rede está a ser. (Opcionalmente) pode atribuir um endereço de IPv4 estático público ou privado para uma configuração de IP. Não é possível atribuir o endereço IPv6 estático público ou privado para uma configuração de IP. Para saber mais sobre como o Azure atribui os endereços IPv4 públicos estáticos, consulte o [endereço IP público](virtual-network-public-ip-address.md) artigo.

## <a name="ip-address-versions"></a>Versões de endereços IP

Pode especificar as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada interface de rede tem de ter um [primário](#primary) configuração de IP com um atribuído [privada](#private) [IPv4](#ipv4) endereço. Pode adicionar um ou mais [secundário](#secondary) configurações de IP com IPv4 privado e (opcionalmente) IPv4 [pública](#public) endereço IP.

### <a name="ipv6"></a>IPv6

Pode atribuir privada zero ou um [IPv6](#ipv6) endereço para uma configuração de IP secundária de uma interface de rede. A interface de rede não pode ter quaisquer configurações de IP secundárias existentes. Não é possível adicionar uma configuração de IP com um endereço IPv6 através do portal. Utilize o PowerShell ou a CLI para adicionar uma configuração de IP com um endereço IPv6 privado para uma interface de rede existente. A interface de rede não pode ser ligada a uma VM existente.

> [!NOTE]
> Apesar de poder criar uma interface de rede com um endereço IPv6 através do portal, não é possível adicionar uma interface de rede existente para uma máquina virtual nova ou existente, utilizando o portal. Utilizar o PowerShell ou o 2.0 CLI do Azure para criar uma interface de rede com um endereço IPv6 privado, em seguida, anexar a interface de rede, ao criar uma máquina virtual. Não é possível anexar uma interface de rede com um endereço IPv6 privado atribuído a uma máquina virtual existente. Não é possível adicionar um endereço IPv6 privado para uma configuração de IP para qualquer interface de rede ligado a uma máquina virtual utilizando ferramentas (portal, CLI ou o PowerShell).

Não é possível atribuir um endereço IPv6 público para uma configuração de IP primária ou secundária.

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU básico ou padrão.  Para obter mais detalhes sobre as diferenças SKU, consulte [gerir endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual com diferentes configurações de IP, leia os artigos seguintes:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC único com vários endereços de IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC único com um endereço IPv6 privado (atrás de um balanceador de carga do Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
