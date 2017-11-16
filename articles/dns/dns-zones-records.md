---
title: "Zonas DNS e registos de descrição geral - DNS do Azure | Microsoft Docs"
description: "Descrição geral do suporte para alojamento de zonas DNS e os registos no DNS do Microsoft Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
ms.openlocfilehash: 00f6309114039db23a1d22f1eb70076b842dadca
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-dns-zones-and-records"></a>Descrição geral dos registos e zonas DNS

Esta página explica os conceitos chave dos domínios, zonas DNS e registos DNS e os conjuntos de registos e como são suportadas no DNS do Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”. Os domínios na hierarquia do DNS são distribuídos globalmente, alojadas por servidores de nome DNS em todo o mundo.

Uma entidade de registo de nome de domínio é uma organização que permite-lhe comprar um nome de domínio, como "contoso.com".  A compra de um nome de domínio dá-lhe o direito de controlo da hierarquia DNS em que o nome, por exemplo, permitindo-lhe direcionar o nome "www.contoso.com" para o web site da sua empresa. A entidade de registo pode alojar o domínio no seus próprio servidores de nomes em seu nome ou permitem-lhe especificar os servidores de nome alternativo.

O DNS do Azure fornece uma infraestrutura de servidor de nome globalmente distribuída, a elevada disponibilidade, o que pode utilizar para alojar o seu domínio. Por que aloja os seus domínios no DNS do Azure, pode gerir os registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como outros serviços do Azure.

O DNS do Azure não suporta atualmente a compra de nomes de domínio. Se pretende adquirir um nome de domínio, terá de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser alojados no DNS do Azure para a gestão de registos DNS. Consulte [delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registos DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-live

O limite de tempo, ou TTL, especifica o tempo que cada registo é colocado em cache por clientes antes de voltar a ser consultado. No exemplo acima, o TTL é de 3600 segundos ou 1 hora.

No DNS do Azure, o TTL é especificado para o conjunto de registos, não para cada registo de, pelo que é utilizado o mesmo valor em todos os registos nesse conjunto de registos.  Pode especificar qualquer valor TTL entre 1 e 2,147,483,647 segundos.

### <a name="wildcard-records"></a>Registos de carateres universais

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registos de carateres universais são devolvidos em resposta a qualquer consulta com um nome correspondente (a menos que exista uma correspondência mais próxima de um conjunto de registos não universais). O DNS do Azure suporta conjuntos de registos de carateres universais para todos os tipos de registos, exceto NS e SOA.

Para criar um conjunto de registos de carateres universais, utilize o nome do conjunto de registos '\*'. Em alternativa, também pode utilizar um nome com '\*'como respetivo mais à esquerda etiqueta, por exemplo,'\*. foo ".

### <a name="caa-records"></a>Registos de CAA

Registos de CAA permitir que os proprietários de domínio especificar as autoridades de certificação (ACs) estão autorizadas a emitir certificados para os domínios. Isto permite que as ACs evitar incorrectamente emitir certificados em algumas circunstâncias. Registos de CAA tem três propriedades:
* **Sinalizadores**: Este é um número inteiro entre 0 e 255, utilizada para representar o sinalizador crítico que têm um significado especial pelo [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Etiqueta**: uma cadeia de carateres ASCII que pode ser um dos seguintes:
    * **problema**: Utilize esta opção se pretender especificar ACs que são permitidos para emitir certificados (todos os tipos de)
    * **issuewild**: Utilize esta opção se pretender especificar ACs que são permitidos para emitir certificados (apenas para certificados de caráter universal)
    * **iodef**: Especifique um endereço de e-mail ou nome de anfitrião para que as ACs podem notificar para pedidos de problema do certificado não autorizado
* **Valor**: o valor da etiqueta específica escolhido

### <a name="cname-records"></a>Registos CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um registo CNAME com o nome relativo "www" e um registo com o nome relativo "www" ao mesmo tempo.

Porque vértice da zona (nome = ' @') contém sempre o NS e SOA conjuntos de registos que foram criadas quando a zona foi criada, não é possível criar um registo CNAME definido no vértice da zona.

Estas restrições derivam das normas DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registos NS

Conjunto no vértice da zona de registos dos NS (nome ' @') é criado automaticamente com cada zona DNS e é eliminado automaticamente quando a zona é eliminada (não é possível eliminá-lo em separado).

Este conjunto de registos contém os nomes dos servidores de nome DNS do Azure atribuídos à zona. Pode adicionar servidores para este registo NS definidas, para suportar os domínios de alojamento conjunta com mais do que um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes de DNS do Azure pré-preenchidos. 

Tenha em atenção que isto aplica-se apenas ao registo NS definido no vértice da zona. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser criados, modificados e eliminados sem restrição.

### <a name="soa-records"></a>Registos SOA

Um conjunto de registos SOA é criado automaticamente no vértice da cada zona (nome = ' @') e é eliminado automaticamente quando a zona é eliminada.  Registos SOA não podem ser criados ou eliminados separadamente.

Pode modificar todas as propriedades do registo SOA, exceto para a propriedade de "anfitrião", que está pré-configurada para indicar o nome do servidor primário de nome fornecido pelo DNS do Azure.

### <a name="spf-records"></a>Registos SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registos SRV

[Os registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados por vários serviços para especificar localizações de servidor. Quando especificar um registo SRV no DNS do Azure:

* O *serviço* e *protocolo* devem ser especificadas como parte do nome do conjunto de registos, o prefixo carateres de sublinhado.  Por exemplo, '\_sip.\_ TCP.name'.  Para um registo no vértice da zona, é necessário especificar ' @' no nome do registo, basta utilizar o serviço e o protocolo, por exemplo '\_sip.\_ TCP'.
* O *prioridade*, *ponderação*, *porta*, e *destino* são especificadas como parâmetros de cada registo no conjunto de registos.

### <a name="txt-records"></a>Registos TXT

Registos TXT são utilizados para mapear os nomes de domínio para cadeias de texto arbitrários. São utilizados em várias aplicações, em particular relacionadas com a configuração de e-mail, tal como o [remetente política Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e [DomainKeys identificado correio (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

As normas DNS permite que um único registo TXT para conter vários cadeias, cada um dos quais poderá até 254 carateres de comprimento. Em que são utilizadas vários cadeias, estão concatenados pelos clientes e tratadas como uma cadeia única.

Quando chamar a API de REST de DNS do Azure, terá de especificar cada cadeia TXT separadamente.  Quando utilizar o portal do Azure, PowerShell ou o CLI interfaces deve especificar uma cadeia único por registo, que é automaticamente dividido segmentos de caráter 254 se necessário.

As cadeias de vários num registo de DNS não devem ser confundidas com os vários registos TXT num conjunto de registos TXT.  Um conjunto de registos TXT pode conter vários registos, *cada um dos quais* pode conter vários cadeias.  O DNS do Azure suporta um comprimento de cadeia total de até 1024 carateres em cada registo TXT definido (em todos os registos combinados).

## <a name="tags-and-metadata"></a>As etiquetas e metadados

### <a name="tags"></a>Etiquetas

As etiquetas são uma lista de pares nome-valor e são utilizadas pelo Azure Resource Manager para recursos de etiquetas.  O Azure Resource Manager utiliza as etiquetas para ativar as vistas filtradas a fatura do Azure e também lhe permite definir uma política no qual as etiquetas são necessárias. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

O DNS do Azure suporta utilizando tags de Azure Resource Manager em recursos de zona DNS.  Não suporta etiquetas em conjuntos de registos de DNS, embora como alternativa 'metadata' é suportada no registo DNS define conforme explicado abaixo.

### <a name="metadata"></a>Metadados

Como alternativa para etiquetas do conjunto de registos, o DNS do Azure suporta anotar os conjuntos de registos com 'metadata'.  Semelhante ao etiquetas, metadados permite-lhe associar pares nome-valor de cada conjunto de registos.  Isto pode ser útil, por exemplo, para o registo a finalidade de cada conjunto de registos.  Ao contrário das etiquetas, os metadados não podem ser utilizado para fornecer uma vista filtrada da sua fatura do Azure e não podem ser especificado numa política do Azure Resource Manager.

## <a name="etags"></a>Etags são

Suponhamos duas pessoas ou dois processos tentam modificar um registo DNS ao mesmo tempo. Qual delas wins? E o winner sabe que que tem substituído alterações criadas por outra pessoa?

O DNS do Azure utiliza Etags para processar alterações em simultâneo ao mesmo recurso em segurança. Os etags são são separados dos [do Azure Resource Manager 'Etiquetas'](#tags). Cada recurso DNS (zona ou conjunto de registos) tem uma Etag associada à mesma. Sempre que é obtido um recurso, a Etag também é obtido. Ao atualizar um recurso, pode optar por transmitir volta a Etag pelo DNS do Azure pode verificar que o Etag correspondências de servidor. Uma vez que cada atualização para um recurso resulta num Etag a ser regenerada, um erro de correspondência de Etag indica que tiver ocorrido uma alteração em simultâneo. Os etags são também podem ser utilizados ao criar um novo recurso para se certificar de que o recurso já existe.

Por predefinição, o PowerShell de DNS do Azure utiliza Etags para bloquear as alterações em simultâneo para zonas e conjuntos de registos. O opcional *-substituir* parâmetro pode ser utilizado para suprimir verificações Etag, nesse caso simultâneas quaisquer alterações ocorridas serão substituídas.

Ao nível da API de REST de DNS do Azure, Etags são especificadas utilizando os cabeçalhos de HTTP.  Respetivo comportamento é fornecido na tabela seguinte:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhuma |PUT sempre é concluída com êxito (verificações de Etag) |
| IF-match<etag> |PUT apenas for bem sucedida se o recurso existe e Etag corresponde |
| IF-match * |PUT apenas for bem sucedida se o recurso existe |
| IF-none-match * |PUT apenas for bem sucedida se o recurso não existe |


## <a name="limits"></a>Limites

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passos seguintes

* Para começar a utilizar o DNS do Azure, saiba como [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [criar registos DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, saiba como [importar e exportar um ficheiro de zona DNS](dns-import-export.md).
