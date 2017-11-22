---
title: FAQ de DNS do Azure | Microsoft Docs
description: Perguntas mais frequentes sobre o DNS do Azure
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>FAQ de DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O sistema de nomes de domínio ou DNS, é responsável por traduzir (ou resolver) um nome de Web site ou serviço para o endereço IP. O DNS do Azure é um serviço de alojamento de domínios DNS, fornecer a resolução do nome utilizando a infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Domínios DNS no DNS do Azure estão alojados numa rede de global do Azure dos servidores de nome DNS. Utilizamos Anycast de rede para que cada consulta DNS é respondida pelo servidor DNS disponível mais próximo. Isto fornece desempenho rápido e elevada disponibilidade para o seu domínio.

O serviço DNS do Azure baseia-se no Azure Resource Manager. Como tal, beneficia das funcionalidades do Gestor de recursos, tais como o controlo de acesso baseado em funções, os registos de auditoria e bloqueio de recurso. Os domínios e registos podem ser geridos através do portal do Azure, os cmdlets do PowerShell do Azure e a CLI do Azure de várias plataformas. As aplicações que requerem gestão automática de DNS podem integrar com o serviço através da REST API e SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custo DNS do Azure?

O modelo de faturação do DNS do Azure é com base no número de zonas DNS alojadas no DNS do Azure e o número de consultas DNS que recebem. Descontos são fornecidos com base na utilização.

Para obter mais informações, consulte o [DNS do Azure, página de preços](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual é o SLA do DNS do Azure?

Garantimos que os pedidos de DNS válidos receberão resposta de pelo menos um servidor de nomes DNS do Azure 99,99% das vezes, pelo menos.

Para obter mais informações, consulte o [página SLA de DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma 'zona DNS'? É o mesmo que um domínio de DNS? 

Um domínio é um nome exclusivo no sistema de nome de domínio, por exemplo, "contoso.com".

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio "contoso.com" pode conter vários registos DNS, tais como "mail.contoso.com" (para um servidor de correio) e "www.contoso.com" (para um web site). Estes seriam ser alojadas na zona DNS "contoso.com".

É um nome de domínio *apenas um nome*, enquanto que uma zona DNS é um recurso de dados que contém os registos DNS para um nome de domínio. O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Também fornece servidores de nome DNS para responder às consultas DNS da Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Necessito de comprar um nome de domínio de DNS para utilizar o DNS do Azure? 

Não necessariamente.

Não necessita de comprar um domínio para alojar uma zona DNS no DNS do Azure. Pode criar uma zona DNS a qualquer momento sem ter um nome de domínio. Consultas DNS para esta zona só irão resolver se este é direcionado para os servidores de nome de DNS do Azure atribuídos à zona.

Terá de adquirir o nome de domínio se de que pretende associar a sua zona DNS para a hierarquia DNS global – Isto permite consultas DNS a partir de qualquer lugar do mundo, para localizar a sua zona DNS e de ser respondidas com os registos DNS.

## <a name="azure-dns-features"></a>Funcionalidades DNS do Azure

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>DNS do Azure suporta a ativação de pós-falha de tráfego com base no DNS, encaminhamento ou o ponto final?

Ativação pós-falha de encaminhamento e o ponto final de tráfego com base no DNS são fornecidos pelo Gestor de tráfego do Azure. Este é um serviço do Azure separado que pode ser utilizado em conjunto com o DNS do Azure. Para obter mais informações, consulte o [descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só suporta o alojamento de domínios DNS 'estáticos', em que cada consulta DNS para um determinado registo DNS recebe sempre a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>DNS do Azure suporta o registo de nome de domínio?

Não. O DNS do Azure não suporta atualmente a compra de nomes de domínio. Se pretende adquirir domínios, terá de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser alojados no DNS do Azure para a gestão de registos DNS. Consulte [delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais detalhes.

Esta é uma funcionalidade que estamos a controlar no nosso registo de segurança. Pode utilizar o nosso site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>DNS do Azure suporta domínios 'private'?
Suporte para domínios 'private' é implementado utilizando privadas zonas DNS.  Esta funcionalidade está atualmente disponível como uma pré-visualização.  Zonas DNS privada são geridas utilizando as ferramentas do mesmas como zonas de DNS do Azure para a internet, mas apenas estão resolvíveis nas redes virtuais especificadas.  Consulte o [descrição geral](private-dns-overview.md) para obter mais detalhes.

Para obter informações sobre outras opções de DNS internas no Azure, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>DNS do Azure suporta DNSSEC?

Não. O DNS do Azure não suporta atualmente DNSSEC.

Esta é uma funcionalidade que estamos a controlar no nosso registo de segurança. Pode utilizar o nosso site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>DNS do Azure suporta transferências de zona (transações AXFR/IXFR)?

Não. O DNS do Azure não suporta atualmente transferências de zona. Zonas DNS podem ser [importado para o DNS do Azure utilizando a CLI do Azure](dns-import-export.md). Registos DNS, em seguida, podem ser geridos através de [portal de gestão do DNS do Azure](dns-operations-recordsets-portal.md), nosso [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md), ou [Ferramenta CLI](dns-operations-recordsets-cli.md).

Esta é uma funcionalidade que estamos a controlar no nosso registo de segurança. Pode utilizar o nosso site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>DNS do Azure suporta URL redireciona?

Não. Serviços do URL de redirecionamento não são um serviço de DNS - funcionam ao nível do HTTP em vez do nível DNS. Alguns fornecedores DNS para agrupar um URL de redirecionamento serviço como parte da sua oferta geral. Isto não é atualmente suportado pelo DNS do Azure.

Esta funcionalidade é controlada no nosso registo de segurança. Pode utilizar o nosso site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Utilizar o DNS do Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Posso coalojar um domínio DNS do Azure e o outro fornecedor DNS a utilizar?

Sim. O DNS do Azure suporta domínios de alojamento conjunta com outros serviços DNS.

Para tal, tem de modificar os registos NS para o domínio (o controlo que Fornecedores recebem DNS consulta para o domínio) para que apontem para os servidores de nomes de ambos os fornecedores. Estes registos NS têm de ser modificado em 3 locais: no DNS do Azure, no outro fornecedor e na zona principal (normalmente configuradas através de entidade de registo de nome de domínio). Para obter mais informações sobre delegação de DNS, consulte [delegação de domínio DNS](dns-domain-delegation.md).

Além disso, tem de garantir que os registos DNS para o domínio são sincronizados entre os dois fornecedores DNS. O DNS do Azure não suporta atualmente transferências de zona DNS. Tem de sincronizar os registos DNS utilizando o [portal de gestão do DNS do Azure](dns-operations-recordsets-portal.md), nosso [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md), ou [Ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>É necessário delegar o meu domínio para todas as 4 servidores de nome de DNS do Azure?

Sim. O DNS do Azure atribui 4 servidores de nomes para cada zona DNS, para uma maior resiliência e isolamento de falhas. Para se qualificar para o SLA de DNS do Azure, terá de delegar o seu domínio para todos os servidores de nome 4.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de utilização para o DNS do Azure?

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre os grupos de recursos ou entre subscrições?

Sim. Zonas DNS podem ser movidas entre grupos de recursos ou entre subscrições.

Não há nenhum impacto sobre em consultas DNS quando move uma zona DNS. Os servidores de nomes atribuídos à zona permaneçam a mesma e consultas DNS são processadas como habitualmente durante todo.

Para obter mais informações e instruções sobre como mover zonas DNS, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo demora para que as alterações DNS em vigor?

Novas zonas DNS e registos DNS são normalmente refletidos em servidores de nome DNS do Azure rapidamente, dentro de alguns segundos.

As alterações para registos DNS existentes poderá demorar um pouco mais, mas ainda devem ser refletidas nos servidores de nomes DNS do Azure dentro de 60 segundos. Neste caso, a DNS a colocação em cache fora do DNS do Azure (por clientes DNS e resoluções de recursivo DNS) também pode afetar o tempo decorrido para uma alteração DNS ser eficaz. Esta duração da cache pode ser controlada através da propriedade Time-To-Live (TTL) de cada conjunto de registos.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como pode proteger os meus zonas DNS contra eliminação acidental?

O DNS do Azure é gerido com o Azure Resource Manager e vantagens do controlo de acesso funcionalidades que o Azure Resource Manager fornece. Controlo de acesso baseado em funções pode ser utilizado para controlar quais os utilizadores tem de leitura ou de acesso de escrita para zonas DNS e conjuntos de registos. Bloqueios de recursos podem ser aplicados para impedir a modificação acidental ou eliminação de zonas DNS e conjuntos de registos.

Para obter mais informações, consulte [Protecting zonas de DNS e registos](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como configurar os registos SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Resolver Nameservers de DNS do Azure através de IPv6? 

Sim. Nameservers de DNS do Azure são pilha dupla (ter endereços IPv4 e IPv6). Para localizar o endereço IPv6 de nameservers o DNS do Azure atribuídos à sua zona DNS, pode utilizar uma ferramenta como nslookup (por exemplo, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Como definir a segurança de nome de domínio um internacional (IDN) no DNS do Azure?

Nomes de domínio internacional (IDNs) funcionam com codificação cada através do nome DNS '[punycode](https://en.wikipedia.org/wiki/Punycode)'. Consultas DNS são efetuadas utilizando estes nomes com codificação punycode.

Pode configurar os nomes de domínio internacional (IDNs) no DNS do Azure convertendo primeiro o nome da zona ou nome do conjunto de registos para punycode. O DNS do Azure não suporta a conversão incorporada do punycode atualmente.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o DNS do Azure](dns-overview.md)
<br>
[Saiba mais sobre como utilizar o DNS do Azure para domínios privados](private-dns-overview.md)
<br>
[Saiba mais sobre os registos e zonas DNS](dns-zones-records.md)
<br>
[Introdução ao DNS do Azure](dns-getstarted-portal.md)

