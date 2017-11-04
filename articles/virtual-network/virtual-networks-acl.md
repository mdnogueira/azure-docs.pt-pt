---
title: "O que é uma lista de controlo de acesso de rede do Azure?"
description: Saiba mais sobre listas de controlo de acesso no Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 9a0c85367968c9b38104012d75b1f3975be82cc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-an-endpoint-access-control-list"></a>O que é uma lista de controlo de acesso de ponto final?

> [!IMPORTANT]
> O Azure tem dois diferentes [modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que implementações mais novas utilizem o modelo de implementação Resource Manager. 

Uma lista de controlo do ponto final acesso (ACL) é uma melhoria de segurança disponível para a sua implementação do Azure. Uma ACL fornece a capacidade de forma seletiva permitir ou negar o tráfego para um ponto final da máquina virtual. Esta capacidade de filtragem de pacote fornece uma camada adicional de segurança. Pode especificar ACLs de rede para apenas os pontos finais. Não é possível especificar uma ACL para uma rede virtual ou uma sub-rede específica contidos numa rede virtual. Recomenda-se para utilizar grupos de segurança de rede (NSGs) em vez de ACLs, sempre que possível. Para saber mais sobre NSGs, consulte [descrição geral de grupo de segurança de rede](virtual-networks-nsg.md)

As ACLs podem ser configuradas através do PowerShell ou o portal do Azure. Para configurar uma ACL de rede utilizando o PowerShell, consulte [listas de controlo de acesso de gestão para pontos finais utilizando o PowerShell](virtual-networks-acl-powershell.md). Para configurar uma ACL de rede através do portal do Azure, consulte [como configurar pontos finais para uma máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Utilizar ACLs de rede, pode efetuar o seguinte:

* Seletivamente permitir ou negar o tráfego de entrada com base na sub-rede remota intervalo de endereços IPv4 para um ponto de final de entrada de máquina virtual.
* Endereços IP blacklist
* Criar múltiplas regras por ponto final de máquina virtual
* Utilizar regra ordenação para garantir que o conjunto correto de regras são aplicadas num ponto final da máquina virtual especificada (mais baixo para maior)
* Especifique uma ACL para uma endereço IPv4 de sub-rede remota específica.

Consulte o [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artigo para limites ACL.

## <a name="how-acls-work"></a>Como funcionam as ACLs
Uma ACL é um objeto que contém uma lista de regras. Quando criar uma ACL e aplicá-la para um ponto final da máquina virtual, filtragem de pacotes ocorre no nó de anfitrião da VM. Isto significa que o tráfego de endereços IP remotos é filtrado pelo nó de anfitrião para regras de ACL correspondência em vez de na VM. Isto impede que a VM gastos com os ciclos da CPU precioso filtragem de pacotes.

Quando é criada uma máquina virtual, uma predefinição ACL é colocada no local para bloquear todo o tráfego de entrada. No entanto, se um ponto final for criado para (porta 3389), em seguida, a predefinição ACL é modificada para permitir todo o tráfego de entrada para esse ponto final. Tráfego de entrada a partir de qualquer sub-rede remota, em seguida, é permitido para esse ponto final e nenhum tipo de aprovisionamento de firewall é necessário. Todas as outras portas estão bloqueadas para tráfego de entrada, a menos que são criados os pontos finais para essas portas. Por predefinição, o tráfego de saída é permitido.

**Tabela de ACL predefinido de exemplo**

| **N. º de regra** | **Sub-rede remota** | **Ponto final** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Permitir |

## <a name="permit-and-deny"></a>Permitir e negar
Seletivamente pode permitir ou negar o tráfego de rede para um ponto de final de entrada de máquina virtual através da criação de regras que especifiquem "Permitir" ou "Negar". É importante ter em atenção que por predefinição, quando é criado um ponto final, todo o tráfego é permitido para o ponto final. Por esse motivo, é importante compreender como criar regras de permitir/recusar e colocá-los pela ordem de precedência de adequado, se pretender que um controlo granular sobre o tráfego de rede que optar por permitir a alcançar o ponto final de máquina virtual.

Pontos a considerar:

1. **Não existem ACL –** por predefinição quando é criado um ponto final, permite que todos os para o ponto final.
2. **Permitir -** quando adicionar um ou mais "permitem" intervalos, são negar todos os outros intervalos por predefinição. Apenas os pacotes do intervalo IP permitido será capazes de comunicar com o ponto final de máquina virtual.
3. **Negar -** quando adicionar um ou mais "Negar" intervalos, é que permite a todos os outros intervalos de tráfego por predefinição.
4. **Combinação de permitir e negar -** pode utilizar uma combinação de "Permitir" e "Negar" quando pretende extrair um intervalo IP específico para ser permitido ou negado.

## <a name="rules-and-rule-precedence"></a>Regras e precedência da regra
ACLs de rede podem ser configuradas em pontos finais de máquina virtual específica. Por exemplo, pode especificar uma ACL de rede para um ponto final RDP criado numa máquina virtual que bloqueia para baixo de acesso para determinados de endereços IP. A tabela abaixo mostra uma forma para conceder acesso ao virtual IPs públicos (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remotos são negados. Iremos seguir um *mais baixa tem precedência* ordem da regra.

### <a name="multiple-rules"></a>Várias regras
No exemplo abaixo, se pretender permitir o acesso ao ponto final do RDP apenas a partir de dois públicos intervalos de endereços IPv4 (65.0.0.0/8 e 159.0.0.0/8), pode conseguir isto, especificando dois *permitir* regras. Neste caso, uma vez que o RDP é criada por predefinição para uma máquina virtual, poderá pretender bloquear o acesso à porta RDP com base numa sub-rede remota. O exemplo abaixo mostra uma forma para conceder acesso ao virtual IPs públicos (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remotos são negados. Isto funciona porque a ACL de rede pode ser definida para um ponto final da máquina virtual específica e o acesso é negado por predefinição.

**Exemplo – várias regras**

| **N. º de regra** | **Sub-rede remota** | **Ponto final** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Permitir |
| 200 |159.0.0.0/8 |3389 |Permitir |

### <a name="rule-order"></a>Ordem de regra
Devido a várias regras podem ser especificadas para um ponto final, tem de existir uma forma de organizar regras para determinar qual a regra tem precedência. A ordem da regra especifica precedência. Siga as ACLs de rede um *mais baixa tem precedência* ordem da regra. No exemplo abaixo, o ponto final na porta 80 seletivamente é concedido acesso a apenas determinados intervalos de endereços IP. Para configurar, temos uma regra de negação (regra \# 100) para endereços no espaço de 175.1.0.1/24. Uma segunda regra, em seguida, for especificada com precedência 200 que permite acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – precedência da regra**

| **N. º de regra** | **Sub-rede remota** | **Ponto final** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Negar |
| 200 |175.0.0.0/8 |80 |Permitir |

## <a name="network-acls-and-load-balanced-sets"></a>ACLs de rede e conjuntos com balanceamento de carga
ACLs de rede podem ser especificadas um ponto de final de conjunto com balanceamento de carga. Se for especificada uma ACL de um conjunto com balanceamento de carga, a rede ACL é aplicada a todas as máquinas virtuais em conjunto com balanceamento de carga. Por exemplo, se o balanceamento de carga é criado um conjunto com a "Porta 80" e o conjunto com balanceamento de carga contém 3 VMs, a ACL de rede criada no ponto final "Porta 80" de uma que VM será aplicado automaticamente para as outras VMs.

![ACLs de rede e conjuntos com balanceamento de carga](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Passos Seguintes
[Gerir listas de controlo de acesso para pontos finais utilizando o PowerShell](virtual-networks-acl-powershell.md)

