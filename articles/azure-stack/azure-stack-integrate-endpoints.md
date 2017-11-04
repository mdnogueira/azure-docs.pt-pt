---
title: "Azure pilha integração do Centro de dados - publicar pontos finais"
description: Saiba como publicar pontos finais de pilha do Azure no seu centro de dados
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/18/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 0d15252079b62f6a74a1279309fb9b1b3ed5711e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure pilha integração do Centro de dados - publicar pontos finais

*Aplica-se a: Azure pilha integrado sistemas*

Pilha do Azure configura vários pontos finais (VIPs - endereços IP virtuais) para as respetivas funções de infraestrutura. Estes VIPs são atribuídos de conjunto de endereços IP público. Cada VIP está protegido com uma lista de controlo de acesso (ACL) na camada de rede definidas por software. As ACLs também são utilizadas em todos os comutadores físicos (TORs e BMC) para proteger ainda mais a solução. Uma entrada DNS é criada para cada ponto final na zona DNS externa que foi especificada no momento da implementação.


O diagrama da arquitetura seguinte mostra as camadas de rede diferente e ACLs:

![Diagrama da arquitetura](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

Os VIPs de infraestrutura que são necessários para publicação do Azure pilha pontos finais para redes externas são listadas na seguinte tabela. A lista mostra cada ponto final, a porta necessária e o protocolo. Pontos finais necessários para fornecedores de recursos adicionais, como o fornecedor de recursos do SQL e outros, são abordados na documentação de implementação do fornecedor de recursos específico.

A infra-estrutura interna VIPs não estão listadas porque não forem necessários para publicação pilha do Azure.

> [!NOTE]
> Utilizador VIPs são dinâmicos, definidas pelos próprios utilizadores com nenhum controlo pela operadora de rede de pilha do Azure.


|Ponto final (VIP)|Um registo de anfitrião de DNS|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|Portal (administrador)|`Adminportal.[Region].[External FQDN]`|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|O Azure Resource Manager (administrador)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Portal (utilizador)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|O Azure Resource Manager (utilizador)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Graph|`Graph.[Region].[External FQDN]`|HTTPS|443|
|Lista de revogação de certificados|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP E UDP|53|
|Cofre de chaves (utilizador)|`*.vault.[Region].[External FQDN]`|TCP|443|
|Cofre de chaves (administrador)|`*.adminvault.[Region].[External FQDN]`|TCP|443|
|Fila de armazenamento|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Tabela de armazenamento|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Blob de armazenamento|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídos)

Pilha do Azure suporta apenas servidores de proxy transparente. Numa implementação em que um uplinks de proxy transparentes para um servidor proxy tradicionais, tem de permitir os URLs e portas seguintes para comunicação de saída:


|Objetivo|URL|Protocolo|Portas|
|---------|---------|---------|---------|
|Identidade|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|Sindicação do Marketplace|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|Patch & atualização|`https://*.azureedge.net`|HTTPS|443|
|Registo|`https://management.azure.com`|HTTPS|443|
|Utilização|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>Publicação de firewall

Portas listadas na secção anterior aplicam-se a comunicação de entrada quando a publicação de serviços de pilha do Azure através de uma firewall existente.

Recomendamos que utilize um dispositivo de firewall para o ajudar a proteger pilha do Azure. No entanto, não é um requisito rigoroso. Embora as firewalls podem ajudar com coisas como distribuídas denial of service (DDoS distribuídos), e e ataques inspeção de conteúdo, pode também ficarem um estrangulamento do débito para serviços de armazenamento do Azure como blobs, tabelas e filas.

Com base no modelo de identidade (Azure AD ou AD FS), pode ou não pode ser necessário para publicar o ponto final do AD FS. Se for utilizado um modo de implementação de desligado, tem de publicar o ponto final do AD FS. (Para obter mais informações, consulte o tópico de identidade de integração do Centro de dados).

O Azure Resource Manager (administrador), o portal de administrador e a pontos finais do Cofre de chaves (administrador) não exige necessariamente de publicação externa. Depende do cenário. Por exemplo, como um fornecedor de serviços, poderá limitar a superfície de ataque e administrar apenas a pilha de Azure a partir de dentro da sua rede e não a partir da Internet.

Para uma organização empresarial, a rede externa pode ser a rede empresarial existente. Neste cenário, é necessário publicar os pontos finais para operar pilha do Azure a partir da rede empresarial.

## <a name="edge-firewall-scenario"></a>Cenário de firewall de limite

Numa implementação edge, pilha do Azure é implementada diretamente atrás o router de limite (fornecido pelo ISP), com ou sem uma firewall existentes à frente dele.

![Diagrama da arquitetura de uma implementação de limite de pilha do Azure](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

Normalmente, os endereços IP encaminháveis públicos especificados para o conjunto VIP público no momento da implementação numa implementação edge. Este cenário permite que um utilizador experimentar a experiência de nuvem automática controlada completa como uma pública na nuvem, como o Azure.

### <a name="using-nat"></a>Utilização de NAT

Apesar de não é recomendada devido a sobrecarga, pode utilizar a tradução de endereços de rede (NAT) para pontos finais de publicação. Para publicação de ponto final que é totalmente controlada pelos utilizadores, é necessário uma regra NAT por utilizador VIP que contém todas as portas de que um utilizador pode utilizar.

Outra consideração é que o Azure não suporta a configuração de um túnel VPN para um ponto final com NAT num cenário de nuvem híbrida com o Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Cenário de firewall de rede intranet/Enterprise/perímetro

Numa implementação empresarial/intranet/perímetro, a pilha do Azure é implementada para além de uma firewall segundo, que é tipicamente a parte de uma rede de perímetro (também conhecida como DMZ).

![Cenário de firewall de pilha do Azure](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Se os endereços IP encaminháveis públicos foram especificados para o conjunto VIP público da pilha do Azure, estes endereços logicamente pertencem à rede de perímetro e requerem regras de publicação na firewall do principal.

### <a name="using-nat"></a>Utilização de NAT

Se os endereços IP encaminháveis não público são utilizados para o conjunto VIP público da pilha do Azure, NAT é utilizada na firewall secundária para publicar os pontos finais de pilha do Azure. Neste cenário, terá de configurar as regras de publicação na firewall do primária para além do limite e na firewall secundária. Se pretender utilizar NAT, considere os seguintes pontos:

- NAT sobrecarga adiciona ao gerir regras de firewall porque dos utilizadores para controlar os seus próprios pontos finais e as suas próprias regras de publicação na pilha de rede (SDN) definidas por software. Os utilizadores tem de contactar o operador de pilha do Azure para obter os VIPs publicadas e para atualizar a lista de portas.
- Embora a utilização NAT limita a experiência de utilizador, fornece controlo total para o operador através de pedidos de publicação.
- Para cenários de nuvem híbrida com o Azure, considere que o Azure não suporta a configuração de um túnel VPN para um ponto final com NAT.


## <a name="next-steps"></a>Passos seguintes

[Integração do Centro de dados do Azure pilha - segurança](azure-stack-integrate-security.md)