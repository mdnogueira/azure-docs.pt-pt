---
title: "Serviços de domínio do Azure AD: Diretrizes de redes | Microsoft Docs"
description: "Considerações sobre o funcionamento em rede para serviços de domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 5f9236c5cf660be00db6e09d61df617b64d978e9
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerações sobre o funcionamento em rede para serviços de domínio do Azure AD
## <a name="how-to-select-an-azure-virtual-network"></a>Como selecionar uma Azure virtual network
As seguintes diretrizes ajudam a selecionar uma rede virtual para utilizar com os serviços de domínio do Azure AD.

### <a name="type-of-azure-virtual-network"></a>Tipo de rede virtual do Azure
* **Redes virtuais do Gestor de recursos**: serviços de domínio do Azure AD podem ser ativados nas redes virtuais criadas com o Azure Resource Manager.
* Não é possível ativar os serviços de domínio do Azure AD numa rede virtual do Azure clássico.
* Pode ligar outros redes virtuais à rede virtual em que os serviços de domínio do Azure AD é ativado. Para obter mais informações, consulte o [conectividade de rede](active-directory-ds-networking.md#network-connectivity) secção.
* **Redes virtuais regionais**: Se planeia utilizar uma rede virtual existente, certifique-se de que é uma rede virtual regional.

  * Não é possível utilizar redes virtuais que utilizam o mecanismo de grupos de afinidade legado com os Serviços de Domínio do Azure AD.
  * Utilizar os serviços de domínio do Azure AD, [migrar redes virtuais legadas para redes virtuais regionais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

### <a name="azure-region-for-the-virtual-network"></a>Região do Azure para a rede virtual
* Os serviços de domínio do Azure AD domínio gerido é implementado na mesma região que a rede virtual do Azure, optar por ativar o serviço no.
* Selecione uma rede virtual numa região do Azure suportada pelos serviços de domínio do Azure AD.
* Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.

### <a name="requirements-for-the-virtual-network"></a>Requisitos para a rede virtual
* **Proximidade para as cargas de trabalho do Azure**: selecione a rede virtual que atualmente aloja/irá alojar máquinas virtuais que necessitam de aceder aos serviços de domínio do Azure AD. Também pode optar por ligar redes virtuais, se as cargas de trabalho são implementadas numa rede virtual diferente domínio gerido.
* **Servidores DNS personalizados/traga a sua própria**: Certifique-se de que existem não existem servidores DNS personalizados configurados para a rede virtual. Um exemplo de um servidor DNS personalizado é uma instância do DNS do Windows Server em execução numa VM do Windows que tenha implementado na rede virtual. Serviços de domínio do AD do Azure se integra com todos os servidores DNS personalizados implementados na rede virtual.
* **Domínios existentes com o mesmo nome de domínio**: Certifique-se de que não dispõe de um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, tenta ativar um domínio gerido dos serviços de domínio do Azure AD com o mesmo nome de domínio (que é 'contoso.com') na rede virtual. Detetar uma falha ao tentar ativar os serviços de domínio do Azure AD. Esta falha é devido a conflitos de nome para o nome de domínio na rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

> [!WARNING]
> Não é possível mover os serviços de domínio a uma rede virtual diferente depois de ter ativado o serviço.
>
>

## <a name="network-security-groups-and-subnet-design"></a>Grupos de segurança de rede e a estrutura de sub-rede
A [grupo de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de lista de controlo de acesso (ACL) que permitem ou negam o tráfego de rede para as instâncias de VM numa rede Virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede. Além disso, o tráfego para uma VM individual pode ser restringido adicional ao associar um NSG diretamente a essa VM.

![Estrutura de sub-rede recomendada](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="guidelines-for-choosing-a-subnet"></a>Diretrizes para escolher uma sub-rede
* Implementar os serviços de domínio do Azure AD para um **separar sub-rede dedicado** dentro da sua rede virtual do Azure.
* Não se aplicam os NSGs para a sub-rede dedicada para o seu domínio gerido. Se tem de aplicar NSGs para a sub-rede dedicada, certifique-se de **não bloquear as portas necessárias para o serviço e gerir o seu domínio**.
* Restringe o número de endereços IP disponíveis na sub-rede dedicada para o seu domínio gerido excessivamente. Esta restrição impede que o serviço de disponibilizar dois controladores de domínio para o seu domínio gerido.
* **Não ativar os serviços de domínio do Azure AD da sub-rede de gateway** da sua rede virtual.

> [!WARNING]
> Quando associa um NSG com uma sub-rede na qual dos serviços de domínio do Azure AD é ativado, poderá interromper capacidade da Microsoft para o serviço e gerir o domínio. Além disso, a sincronização entre o seu inquilino do Azure AD e o seu domínio gerido é interrompida. **O SLA não é aplicável a implementações em que um NSG foi aplicado que blocos de serviços de domínio do Azure AD da atualização e gerir o seu domínio.**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Portas necessárias para os serviços de domínio do Azure AD
As seguintes portas são necessárias para os serviços de domínio do Azure AD para o serviço e manter o seu domínio gerido. Certifique-se de que estas portas não são bloqueadas para a sub-rede em que tiver ativado o seu domínio gerido.

| Número de porta | Objetivo |
| --- | --- |
| 443 |Sincronização com o seu inquilino do Azure AD |
| 3389 |Gestão do seu domínio |
| 5986 |Gestão do seu domínio |
| 636 |Proteger o acesso de LDAP (LDAPS) ao seu domínio gerido |

Porta 5986 é utilizada para efetuar tarefas de gestão a utilizar a comunicação remota do PowerShell no seu domínio gerido. Os controladores de domínio para o seu domínio gerido não normalmente escutar nesta porta. O serviço abre-se esta porta nos controladores de domínio geridos apenas quando uma operação de gestão ou de manutenção tem de ser efetuada para o domínio gerido. Assim que a operação for concluída, o serviço será encerrado a esta porta nos controladores de domínio gerido.

Porta 3389 é utilizada para ligações de ambiente de trabalho remotas para o seu domínio gerido. Isto também porta permanece amplamente desativada no seu domínio gerido. O serviço permite esta porta apenas se é necessário ligar ao seu domínio gerido para resolução de problemas, iniciados em resposta a um pedido de serviço que iniciar. Este mecanismo não é utilizado numa base contínua, uma vez que as tarefas de monitorização e gestão são executadas utilizando a comunicação remota do PowerShell. Esta porta é utilizada apenas no evento raro que é necessário para ligar remotamente ao seu domínio gerido para resolução de problemas avançada. A porta está fechada, assim que a operação de resolução de problemas está concluída.


### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exemplo de NSG para redes virtuais com os serviços de domínio do Azure AD
A tabela seguinte ilustra um exemplo NSG, pode configurar uma rede virtual com um domínio gerido dos serviços de domínio do Azure AD. Esta regra permite o tráfego de entrada sobre as portas necessárias para garantir que o seu domínio gerido permanece corrigido, atualizar e pode ser monitorizado pela Microsoft. A regra predefinida do 'DenyAll' aplica-se a todos os outro o tráfego de entrada da internet.

Além disso, o NSG também ilustra a bloquear o acesso LDAP seguro através da internet. Ignore esta regra se não tiver ativado acesso LDAP seguro ao seu domínio gerido através da internet. O NSG contém um conjunto de regras que permitem acesso de entrada de LDAPS através da porta TCP 636 apenas a partir de um conjunto especificado de endereços IP. A regra NSG para permitir o acesso LDAPS através da internet a partir de endereços IP especificados tem uma prioridade mais alta do que a regra de DenyAll NSG.

![Exemplo NSG para proteger o acesso a LDAPS através da internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Obter mais informações** - [criar um grupo de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Conectividade de rede
Um domínio gerido dos serviços de domínio do Azure AD pode ser ativado apenas dentro de uma única rede virtual no Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Cenários para ligar redes do Azure
Ligar redes virtuais do Azure para utilizar o domínio gerido em qualquer um dos seguintes cenários de implementação:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Utilizar o domínio gerido em mais do que uma rede virtual do Azure
Pode ligar outros redes virtuais do Azure para a Azure virtual network na qual que tiver ativado os serviços de domínio do Azure AD. Esta ligação de peering VPN/VNet permite-lhe utilizar o domínio gerido com as cargas de trabalho implementadas nas outras redes virtuais.

![Conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utilizar o domínio gerido numa rede virtual baseada no Resource Manager
Pode ligar uma rede virtual baseada no Resource Manager para a rede virtual clássica do Azure em que tiver ativado os serviços de domínio do Azure AD. Esta ligação permite-lhe utilizar o domínio gerido com as cargas de trabalho implementadas na rede virtual baseada no Resource Manager.

![Gestor de recursos para conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opções de ligação de rede
* **Peering de rede de ligações VNet a VNet com virtual**: peering de rede Virtual é um mecanismo que liga as duas redes virtuais na mesma região através da rede principal do Azure. Uma vez executado o peering, as duas redes virtuais aparecem como uma única para todos os fins de conetividade. Vão continuar a ser geridas como recursos separados, mas as máquinas virtuais nestas redes virtuais podem comunicar diretamente entre si através de endereços IP privados.

    ![Utilização de peering de conectividade de rede virtual](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Obter mais informações - virtuais peering de rede](../virtual-network/virtual-network-peering-overview.md)
    
* **Ligações VNet a VNet através de ligações de VPN de site para site**: ligar uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação uma rede virtual a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

    ![Conectividade de rede virtual com Gateway de VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Obter mais informações - ligar redes virtuais utilizando o gateway de VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Peering de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
* [Configurar uma ligação VNet a VNet para o modelo de implementação clássica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupos de segurança de rede do Azure](../virtual-network/virtual-networks-nsg.md)
* [Criar um grupo de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
