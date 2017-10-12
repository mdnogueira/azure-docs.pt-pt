---
title: "Descrição geral de delegação do DNS do Azure | Microsoft Docs"
description: "Compreenda como alterar a delegação de domínios e utilizar servidores de nomes de DNS do Azure para fornecer o alojamento de domínios."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.openlocfilehash: 31a500502a4d3e729ecb79929ed6c1de156d6259
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegação de zonas DNS com o DNS do Azure

O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Para que as consultas do DNS de um domínio alcancem o DNS do Azure, o domínio tem de ser delegado ao DNS do Azure a partir do domínio principal. Tenha em atenção que o DNS do Azure não é a entidade de registo de domínios. Este artigo explica como funciona a delegação de domínios e como delegar domínios ao DNS do Azure.

## <a name="how-dns-delegation-works"></a>Como funciona a delegação ao DNS

### <a name="domains-and-zones"></a>Domínios e zonas

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes domínios de nível superior, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”.  E assim sucessivamente. Os domínios na hierarquia do DNS estão alojados com recurso a zonas DNS separadas. Estas zonas são distribuídas globalmente, alojadas por servidores de nomes DNS em todo o mundo.

**Zona DNS** - um domínio é um nome exclusivo no Sistema de Nomes de Domínio, como, por exemplo, “contoso.com”. Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um Web site).

**Entidade de registo de domínios** - uma entidade de registo de domínios é uma empresa que pode fornecer nomes de domínios de Internet. Esta entidade irá verificar se o domínio da Internet que pretende utilizar está disponível e permite-lhe que o compre. Assim que o nome de domínio estiver registado, será o proprietário legal do mesmo. Se já tiver um domínio da Internet, irá utilizar a entidade de registo de domínios atual para delegar ao DNS do Azure.

Para saber mais informações sobre quem é o proprietário de um determinado nome de domínio ou para obter informações sobre como comprar um domínio, consulte [Gestão de domínios da Internet no Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

* Um servidor DNS *autoritativo* aloja zonas DNS. Responde a consultas DNS para os registos apenas dessas zonas.
* Um servidor DNS *recursivo* não aloja zonas DNS. Responde a todas as consultas DNS ao chamar servidores DNS autoritativos para recolher os dados de que necessita.

O Azure DNS fornece um serviço DNS autoritativo.  Não fornece um serviço DNS recursivo. Os Serviços Cloud e as VMs no Azure são automaticamente configurados para utilizar um serviço DNS recursivo, que é fornecido separadamente como parte da infraestrutura do Azure. Para obter informações sobre como alterar estas definições de DNS, veja [Name Resolution in Azure (Resolução de Nomes no Azure)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Normalmente, os clientes DNS em PCs ou em dispositivos móveis chamam um servidor DNS recursivo para efetuar quaisquer consultas de DNS que as aplicações de cliente necessitem.

Quando um servidor DNS recursivo recebe uma consulta para um registo DNS, tal como “www.contoso.com”, tem primeiro de localizar o nome do servidor que aloja a zona do domínio “contoso.com”. Para tal, começa pelos servidores de nomes de raiz e, a partir daí, localiza os servidores de nomes que alojam a zona “com”. Em seguida, consulta os servidores de nomes “com” para localizar os servidores de nomes que alojam a zona “contoso.com”.  Finalmente, é capaz de consultar estes servidores de nome para “www.contoso.com”.

Este procedimento é denominado “resolver o nome DNS”. Em termos mais rigorosos, a resolução de DNS inclui passos adicionais, tais como o seguimento de CNAMEs, mas que não são importantes para compreender como funciona a delegação do DNS.

Como é que uma zona principal “aponta” para os servidores de nomes de uma zona subordinada? Consegue fazê-lo ao utilizar um tipo especial de registo DNS chamado registo NS (NS significa servidor de nomes, “name server” em inglês). Por exemplo, a zona raiz contém registos NS para “com” e mostra os servidores de nomes para a zona “com”. Por sua vez, a zona “com” contém registos NS para “contoso.com”, que mostra os servidores de nomes para a zona “contoso.com”. Configurar os registos NS para uma zona subordinada numa zona principal é denominado delegar o domínio.

A imagem seguinte mostra um exemplo de consulta DNS. As contoso.net e partners.contoso.net são zonas DNS do Azure.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. O cliente pede `www.partners.contoso.net` a partir do respetivo servidor DNS local.
1. O servidor DNS local não tem o registo, por isso faz um pedido ao respetivo servidor de nomes de raiz.
1. O servidor de nomes de raiz não tem o registo, mas sabe o endereço do servidor de nomes `.net`, e fornece esse endereço ao servidor DNS
1. O DNS envia o pedido ao servidor de nomes `.net`, não tem o registo, mas não sabe o endereço do servidor de nomes contoso.net. Neste caso, é uma zona DNS alojada no DNS do Azure.
1. A zona `contoso.net` não tem o registo, mas sabe o servidor de nomes para `partners.contoso.net` e responde com o mesmo. Neste caso, é uma zona DNS alojada no DNS do Azure.
1. O servidor DNS solicita o endereço IP do `partners.contoso.net` a partir da zona `partners.contoso.net`. Contém o registo A e responde com o endereço IP.
1. O servidor DNS fornece o endereço IP ao cliente
1. O cliente liga-se ao site `www.partners.contoso.net`.

Cada delegação, na verdade, tem duas cópias dos registos NS; uma na zona principal a apontar para a subordinada e outra na própria zona subordinada. A zona “contoso.net” contém os registos NS para “contoso.net” (além dos registos NS em “net”). Estes são designados registos NS autoritativos e residem no vértice da zona subordinada.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [delegar o domínio no DNS do Azure](dns-delegate-domain-azure-dns.md)

