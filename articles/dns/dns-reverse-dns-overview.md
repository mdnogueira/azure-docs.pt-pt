---
title: "Descrição geral do DNS inverso no Azure | Microsoft Docs"
description: Saiba como inversa de DNS de funciona e como pode ser utilizado no Azure
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 70a1ad070e812951fca3d2b19da12c67f0725dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Descrição geral do DNS inversa e de suporte no Azure

Este artigo fornece uma descrição geral de como inversa de DNS de funciona e os cenários DNS inversas suportados no Azure.

## <a name="what-is-reverse-dns"></a>O que é inversa de DNS?

Registos DNS convencionais ativar um mapeamento a partir de um nome DNS (por exemplo, "www.contoso.com") para um endereço IP (por exemplo, 64.4.6.100).  O DNS inverso permite a conversão de um endereço IP (64.4.6.100) para um nome ("www.contoso.com").

Registos DNS inversas são utilizados numa variedade de situações. Por exemplo, registos DNS inversas são usados em combating spam de e-mail, verificando o remetente de uma mensagem de correio eletrónico.  Servidor de receção de correio obtém o registo DNS inverso do endereço IP do servidor de envio e verifica se o anfitrião tem autorização para enviar correio eletrónico a partir do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Funciona como inversa de DNS

Registos inversas de DNS estão alojados em especiais zonas DNS, conhecidas como zonas 'ARPA'.  Estes zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal de alojamento de domínios como "contoso.com".

Por exemplo, o DNS registos "www.contoso.com" é implementado com um registo DNS "A" com o nome "www" na zona "contoso.com".  Este registo aponta para o endereço IP correspondente, neste caso 64.4.6.100.  A pesquisa inversa se encontra implementada separadamente, utilizando um registo de 'PTR' com o nome '100' na zona '6.4.64.in-addr.arpa' (tenha em atenção que os endereços IP são invertidos em zonas ARPA.)  Este registo PTR, se tiver sido configurada corretamente, aponta para o nome "www.contoso.com".

Quando uma organização é atribuída um bloco de endereços IP, também adquirir o direito de gerir a zona ARPA correspondente. As zonas ARPA correspondente para os blocos de endereços IP utilizados pelo Azure alojadas e geridas pela Microsoft. O ISP pode alojar a zona ARPA para os seus próprios endereços IP por si ou pode permitir que a alojar a zona ARPA num serviço DNS à sua escolha, tal como o DNS do Azure.

> [!NOTE]
> Pesquisas de DNS direta e inversas de DNS de pesquisas são implementadas em hierarquias DNS separadas, paralelas. A pesquisa inversa de "www.contoso.com" está **não** alojada na zona "contoso.com", em vez está alojado na zona ARPA para o bloco de endereços IP correspondente. Zonas separadas são utilizadas para blocos de endereços IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por exemplo, ao criar uma zona inversa para registos de anfitrião para anfitriões com IPs que se encontrem no prefixo de 192.0.2.0/24, o nome da zona seria possível criar por isolar o prefixo de rede do endereço (192.0.2) e, em seguida, para inverter a ordem (2.0.192) e adicionar o sufixo `.in-addr.arpa`.

|Classe de sub-rede|Prefixo de rede  |Prefixo de rede invertidos  |Sufixo padrão  |Nome da zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegação de IPv4 Classless Inter-

Em alguns casos, o intervalo IP atribuído a uma organização é menor do que uma classe C (/ 24) intervalo. Neste caso, o intervalo de IP não se encontra num limite de zona dentro de `.in-addr.arpa` hierarquia de zona e, por conseguinte, não pode ser delegada como uma zona subordinada.

Em vez disso, é utilizado o mecanismo de diferentes para transferir o controlo de registos de pesquisa inversa individuais (PTR) para uma zona DNS dedicada. Este mecanismo delega uma zona subordinada para cada intervalo de IP, em seguida, mapeia cada endereço IP no intervalo individualmente para essa zona subordinada através de registos CNAME.

Por exemplo, suponha que uma organização é concedida o IP intervalo 192.0.2.128/26 pelo seu ISP. Isto representa 64 endereços IP, 192.0.2.128 para 192.0.2.191. Inversa de DNS para este intervalo é implementado da seguinte forma:
- A organização cria uma zona de pesquisa inversa chamada 26.2.0.192.in-arpa de 128. O prefixo ' 128-26' representa o segmento de rede atribuído à organização dentro de classe C (/ 24) intervalo.
- O ISP cria registos NS para configurar a delegação de DNS para a zona acima da zona de principal de classe C. Também cria registos CNAME na zona de pesquisa inversa principal (classe C), o mapeamento de cada endereço IP no intervalo de IP para a nova zona criada pela organização:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Em seguida, a organização gere os registos PTR individuais dentro da sua zona subordinada.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Uma pesquisa inversa para consultas de '192.0.2.129' de endereço IP para um registo PTR com o nome '129.2.0.192.in-addr.arpa'. Esta consulta é resolvido através de CNAME na zona principal para o registo PTR na zona subordinada.

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa IPv6 deve estar no seguinte formato:`<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo. Se criar uma zona inversa para registos de anfitrião para anfitriões com IPs que estiverem no 2001:db8:1000:abdc:: / 64 prefixo, seria possível criar o nome da zona ao isolar o prefixo do endereço de rede (2001:db8:abdc::). Em seguida expanda o prefixo de rede IPv6 para remover [zero compressão](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), que foi utilizado para abreviar o prefixo do endereço IPv6 (2001:0db8:abdc:0000::). Inverter a ordem, com um período como delimitador entre cada número hexadecimal no prefixo, para criar o prefixo de rede invertidos (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) e adicione o sufixo `.ip6.arpa`.


|Prefixo de rede  |Prefixo de rede expandido e invertidos |Sufixo padrão |Nome da zona inversa  |
|---------|---------|---------|---------|
|2001:DB8:abdc:::/ 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:DB8:1000:9102:::/ 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte do Azure para o DNS inversa

Azure suporta dois cenários separados, relacionado com a inversa de DNS:

**Que alojam a zona de pesquisa inversa correspondente para o bloco de endereços IP.**
O DNS do Azure pode ser utilizado para [alojar zonas de pesquisa inversa e gerir os registos PTR para cada pesquisa inversa de DNS](dns-reverse-dns-hosting.md), para IPv4 e IPv6.  O processo de criar a zona de pesquisa inversa (ARPA), configurar a delegação e configurar os registos PTR é igual para regulares zonas DNS.  São as diferenças apenas que a delegação tem de ser configurada através do seu ISP, em vez da sua entidade de registo DNS e deve ser utilizado apenas o tipo de registo PTR.

**Configure o registo DNS inverso para o endereço IP atribuído ao seu serviço do Azure.** Azure permite-lhe [configurar a pesquisa inversa de endereços IP alocados ao seu serviço do Azure](dns-reverse-dns-for-azure-services.md).  Esta pesquisa inversa é configurada pelo Azure como um registo PTR na zona ARPA correspondente.  Estes zonas ARPA, correspondente a todos os intervalos IP utilizados pelo Azure, estão alojadas pela Microsoft

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre inversa DNS, consulte [pesquisa inversa de DNS em Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [alojam a zona de pesquisa inversa para o intervalo de IP ISP atribuído no DNS do Azure](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerir registos inversas de DNS para os serviços do Azure](dns-reverse-dns-for-azure-services.md).

