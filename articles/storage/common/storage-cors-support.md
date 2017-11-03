---
title: "Recursos de várias origens (CORS) de partilha suporte | Microsoft Docs"
description: "Saiba como ativar o suporte de CORS para os serviços de armazenamento do Microsoft Azure."
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure de partilha
A partir da versão 2013-08-15, os serviços de armazenamento do Azure suportam a partilha de recursos de várias origens (CORS) para os serviços Blob, tabela, fila e ficheiro. CORS é uma funcionalidade HTTP que permite uma aplicação web em execução com um domínio aceder a recursos noutro domínio. Browsers da Web implementam uma restrição de segurança denominada [política da mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy) impede que uma página web a partir de APIs chamadas num domínio diferente; CORS fornece uma forma segura para permitir que um domínio (o domínio de origem) chamar as APIs noutro domínio. Consulte o [especificação de CORS](http://www.w3.org/TR/cors/) para obter detalhes sobre CORS.

Pode definir as regras CORS individualmente para cada um dos serviços de armazenamento, chamando [definir propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades de serviço fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir propriedades de serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx). Depois de definir as regras CORS para o serviço, um pedido autenticado corretamente efetuado contra o serviço de outro domínio será avaliado para determinar se é permitido, de acordo com as regras que especificou.

> [!NOTE]
> Tenha em atenção que CORS não é um mecanismo de autenticação. Qualquer pedido efetuado em relação a um recurso de armazenamento, quando ativar a CORS tem de ter uma assinatura de uma autenticação adequada ou têm de ser efetuado em relação a um recurso público.
> 
> 

## <a name="understanding-cors-requests"></a>Noções sobre pedidos CORS
Um pedido de CORS de um domínio de origem pode ser composto por duas pedidos separados:

* Um pedido prévia, que consulta as restrições de CORS impostas pelo serviço. É necessário o pedido de verificação prévia, a menos que o método de pedido é um [método simples](http://www.w3.org/TR/cors/), que significa que GET, HEAD ou POST.
* O pedido real, realizado no recurso pretendido.

### <a name="preflight-request"></a>Pedido de verificação prévia
As consultas de pedido de verificação prévia as restrições de CORS que foram estabelecidas pelo proprietário da conta para o serviço de armazenamento. O web browser (ou outros agentes de utilizador) envia um pedido de opções que inclui a cabeçalhos de pedido, o domínio de método e origem. O serviço de armazenamento avalia a operação pretendida com base num conjunto de regras CORS que especificar os domínios de origem, os métodos de pedido e cabeçalhos de pedido podem ser especificados num pedido real contra um recurso de armazenamento previamente configurado.

Se CORS está ativada para o serviço e existe uma regra CORS que corresponde ao pedido de verificação prévia, o serviço responde com o código de estado 200 (OK) e inclui os cabeçalhos de controlo de acesso necessários na resposta.

Se CORS não está ativada para o serviço ou o pedido de verificação prévia não corresponde a nenhuma regra CORS, o serviço irá responder com o código de estado 403 (proibido).

Se o pedido de opções não contém os cabeçalhos CORS necessários (os cabeçalhos de origem e de acesso-controlo-pedido-Method), o serviço irá responder com o código de estado 400 (pedido incorreto).

Tenha em atenção que um pedido de verificação prévia é avaliado contra o serviço (Blob, fila e tabela) e não com o recurso pedido. O proprietário da conta tem de ter ativada a CORS como parte das propriedades de serviço da conta na ordem para o pedido seja bem sucedida.

### <a name="actual-request"></a>Pedido real
Depois do pedido de verificação prévia é aceite e a resposta é devolvida, o browser irá emitir o pedido real contra o recurso de armazenamento. O browser irá negar o real imediatamente pedido se o pedido de verificação prévia foi rejeitado.

O pedido real é tratado como normal pedido contra o serviço de armazenamento. A presença do cabeçalho de origem indica que o pedido é um pedido CORS e o serviço irá verificar as regras CORS correspondentes. Se for encontrada uma correspondência, os cabeçalhos de controlo de acesso são adicionados à resposta e enviados de volta para o cliente. Se não for encontrada uma correspondência, os cabeçalhos de controlo de acesso CORS não são devolvidos.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Ativar o CORS para os serviços de armazenamento do Azure
As regras CORS são definidas ao nível de serviço, por isso terá de ativar ou desativar a CORS para cada serviço (Blob, fila e tabela) em separado. Por predefinição, a CORS está desativada para cada serviço. Para ativar a CORS, tem de definir as propriedades de serviço apropriado utilizando a versão 2013-08-15 ou posterior e adicione as regras CORS para as propriedades do serviço. Para obter detalhes sobre como ativar ou desativar a CORS para um serviço e como configurar as regras CORS, consulte [definir propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades de serviço fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir propriedades de serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx).

Eis um exemplo de uma única regra CORS, especificado através de uma operação definir propriedades de serviço:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Cada elemento incluído na regra CORS é descrito abaixo:

* **AllowedOrigins**: os domínios de origem que têm permissão para efetuar um pedido contra o serviço de armazenamento através de CORS. O domínio de origem é o domínio a partir do qual o pedido tem origem. Tenha em atenção que a origem tem de ser uma correspondência exata de maiúsculas e minúsculas com a origem que envia a idade do utilizador para o serviço. Também pode utilizar o caráter universal ' *' para permitir que todos os domínios de origem fazer pedidos através de CORS. No exemplo acima, os domínios [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) possa fazer pedidos contra o serviço utilizando a CORS.
* **AllowedMethods**: os métodos (verbos de pedido HTTP) que pode utilizar o domínio de origem para um pedido CORS. No exemplo acima, são permitidos apenas pedidos PUT e GET.
* **AllowedHeaders**: os cabeçalhos de pedido que pode especificar o domínio de origem do pedido de CORS. No exemplo acima, todos os cabeçalhos de metadados a partir x-ms-meta-data, x-ms-meta-destino e x-ms-meta-abc são permitidos. Tenha em atenção que o caráter universal ' *' indica que o início qualquer cabeçalho com o prefixo especificado é permitido.
* **ExposedHeaders**: os cabeçalhos de resposta que podem ser enviados em resposta ao pedido de CORS e expostos pelo browser para o emissor de pedido. No exemplo acima, o browser é instruído para expor a partir qualquer cabeçalho x-ms-meta.
* **MaxAgeInSeconds**: O tempo máximo que um browser deve colocar em cache as opções de verificação prévias de pedido.

Os serviços de armazenamento do Azure suportam a especificação de cabeçalhos com prefixo para ambos os **AllowedHeaders** e **ExposedHeaders** elementos. Para permitir uma categoria dos cabeçalhos, pode especificar um prefixo comuns para dessa categoria. Por exemplo, especificar *x-ms-meta** como um cabeçalho com prefixo estabelece uma regra que corresponde a todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações aplicam-se para as regras CORS:

* Pode especificar até cinco CORS regras por serviço de armazenamento (Blob, tabela e fila).
* O tamanho máximo de todas as definições CORS da regras no pedido, excluindo as etiquetas XML, não deve ter mais de 2 KB.
* O comprimento de um cabeçalho permitido, cabeçalho exposto ou origem permitida não deve exceder os 256 carateres.
* Cabeçalhos permitidos e expostos cabeçalhos podem ser:
  * Cabeçalhos literais, onde o nome de cabeçalho exato for fornecido, tais como **x-ms-meta-processados**. Um máximo de 64 cabeçalhos literais pode ser especificado no pedido.
  * Cabeçalhos, onde um prefixo do cabeçalho é fornecido, tais como o prefixo **x-ms-meta-data***. Especificar um prefixo desta forma permite ou expõe qualquer cabeçalho que começa com o prefixo especificado. Um máximo de dois cabeçalhos com prefixo pode ser especificado no pedido.
* Os métodos (ou verbos HTTP) especificado no **AllowedMethods** elemento tem de estar em conformidade com os métodos suportados pelo serviço de armazenamento do Azure APIs. Métodos suportados são DELETE, GET, HEAD, intercalação, POST, as opções e PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Compreender a lógica de avaliação da regra CORS
Quando um serviço de armazenamento recebe um pedido de verificação prévia ou real, avalia esse pedido com base nas regras CORS que tem estabelecida para o serviço através da operação definir propriedades de serviço apropriado. As regras CORS são avaliadas por ordem em que foram definidas no corpo do pedido da operação definir propriedades de serviço.

As regras CORS são avaliadas da seguinte forma:

1. Em primeiro lugar, o domínio de origem do pedido é verificado com domínios listados para o **AllowedOrigins** elemento. Se o domínio de origem está incluído na lista ou todos os domínios têm permissão para ter o caráter universal ' *', em seguida, as regras continua de avaliação. Se o domínio de origem não estiver incluído, o pedido falha.
2. Em seguida, o método (ou o verbo HTTP) do pedido é verificado com os métodos apresentados no **AllowedMethods** elemento. Se o método está incluído na lista, avaliação de regras continua; caso contrário, em seguida, falha o pedido.
3. Se o pedido corresponde a uma regra no respetivo domínio de origem e o respetivo método, essa regra está selecionada para processar que o pedido e não existem regras adicionais serão avaliadas. Antes do pedido terá êxito, no entanto, quaisquer cabeçalhos especificados no pedido são verificados relativamente contra os cabeçalhos listados no **AllowedHeaders** elemento. Se os cabeçalhos enviados não corresponderem os cabeçalhos permitidos, falha o pedido.

Uma vez que as regras são processadas pela ordem que estão presentes no corpo do pedido, recomendadas as melhores práticas que especificou as regras mais restritivas no que respeita à origens primeiro na lista, para que estes são avaliadas primeiro. Especifique as regras que são menos restritivas – por exemplo, uma regra para permitir todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo-avaliação de regras de CORS
O exemplo seguinte mostra um corpo do pedido parcial de uma operação definir as regras CORS para os serviços de armazenamento. Consulte [definir propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades de serviço fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir propriedades de serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre como construir o pedido.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Em seguida, considere os seguintes pedidos CORS:

| Pedir |  |  | Resposta |  |
| --- | --- | --- | --- | --- |
| **Método** |**Origem** |**Cabeçalhos de pedido** |**Correspondência de regra** |**Resultado** |
| **COLOCAR** |http://www.contoso.com |x-ms-BLOBs-tipo de conteúdo |Primeira regra |Êxito |
| **INTRODUÇÃO** |http://www.contoso.com |x-ms-BLOBs-tipo de conteúdo |Segunda regra |Êxito |
| **INTRODUÇÃO** |http://www.contoso.com |x-ms-client-request-id |Segunda regra |Falha |

O primeiro pedido corresponde da primeira regra – o domínio de origem corresponde as origens permitidas, o método corresponde os métodos permitidos e o cabeçalho corresponde os cabeçalhos permitidos – e, por isso, é concluída com êxito.

O segundo pedido não corresponde a primeira regra porque o método não coincide com os métodos permitidos. No entanto, corresponde a segunda regra, pelo que será efetuada com êxito.

O terceiro pedido corresponde a segunda regra no respetivo domínio de origem e o método, pelo que não existem regras adicionais são avaliadas. No entanto, o *cabeçalho x-ms-client-request-id* não é permitido pela segunda regra, para que o pedido falha, apesar do facto de que a semântica de regra de terceira seria ter permissão para ter êxito.

> [!NOTE]
> Embora este exemplo mostra uma regra menos restritiva antes de um mais restritivo, em geral a melhor prática é listar as regras mais restritivas primeiro.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Compreender a forma como o cabeçalho Vary está definido
O *Vary* cabeçalho é um cabeçalho HTTP/1.1 padrão constituída por um conjunto de campos de cabeçalho de pedido aconselhe processo o agente de browser ou o utilizador sobre os critérios que foram selecionados pelo servidor para processar o pedido. O *Vary* cabeçalho é essencialmente utilizado para a colocação em cache por proxies, browsers e CDNs, que utilizá-la para determinar a forma como a resposta deve ser colocada em cache. Para obter mais informações, consulte a especificação para o [cabeçalho Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o browser ou de outro utilizador agente coloca em cache a resposta de um pedido CORS, o domínio de origem é colocado em cache, como a origem permitida. Quando um segundo domínio emite o pedido mesmo para um recurso de armazenamento enquanto a cache está ativa, o agente de utilizador obtém o domínio de origem em cache. Segundo domínio não corresponde ao domínio em cache, pelo que o pedido falha quando caso contrário será bem sucedida. Em certos casos, o Storage do Azure define o cabeçalho Vary para **origem** ao instruir o agente de utilizador para enviar o pedido CORS subsequente para o serviço quando o domínio requerente difere de acordo com a origem da cache.

Conjuntos de armazenamento do Azure a *Vary* cabeçalho para **origem** para os pedidos GET/HEAD reais nos seguintes casos:

* Quando a origem do pedido corresponde exatamente a origem permitida definida por uma regra CORS. Para ser uma correspondência exata, a regra CORS não pode incluir um caráter universal ' * ' carateres.
* Não existe nenhuma regra correspondente a origem do pedido, mas CORS está ativada para o serviço de armazenamento.

No caso em que um pedido GET/HEAD corresponde a uma regra CORS que permite que todas as origens, a resposta indica que todas as origens são permitidas e a cache do agente de utilizador irá permitir que os pedidos subsequentes de qualquer domínio de origem enquanto a cache está ativa.

Tenha em atenção que para pedidos a utilizar métodos diferentes de GET/HEAD, os serviços de armazenamento não definirá o cabeçalho Vary, uma vez que as respostas para estes métodos não estão em cache por agentes de utilizador.

A tabela seguinte indica armazenamento como do Azure irá responder a pedidos GET/HEAD com base em cenários de mencionadas anteriormente:

| Pedir | Definição de conta e o resultado da avaliação da regra |  |  | Resposta |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Cabeçalho de origem presente no pedido** |**CORS regra (s) especificado para este serviço** |**Existe uma regra correspondente que permite que todas as origens (*)** |**Existe uma regra correspondente para a correspondência exata de origem** |**Resposta inclui o cabeçalho Vary definido como origem** |**Resposta inclui acesso controlo-permitido-origens: "*"** |**Resposta inclui acesso-controlo-expostos-cabeçalhos** |
| Não |Não |Não |Não |Não |Não |Não |
| Não |Sim |Não |Não |Sim |Não |Não |
| Não |Sim |Sim |Não |Não |Sim |Sim |
| Sim |Não |Não |Não |Não |Não |Não |
| Sim |Sim |Não |Sim |Sim |Não |Sim |
| Sim |Sim |Não |Não |Sim |Não |Não |
| Sim |Sim |Sim |Não |Não |Sim |Sim |

## <a name="billing-for-cors-requests"></a>Faturação para pedidos CORS
Os pedidos com êxito prévias são faturadas se tiver ativado o CORS para qualquer um dos serviços de armazenamento para a sua conta (chamando [definir propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades de serviço fila](https://msdn.microsoft.com/library/hh452232.aspx), ou [definir propriedades de serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar os custos, considere o **MaxAgeInSeconds** elemento no seu CORS regras para um valor grande para que o agente de utilizador coloca em cache o pedido.

Pedidos de verificação prévias sem não serão cobrados.

## <a name="next-steps"></a>Passos seguintes
[Definir as propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir as propriedades do serviço fila](https://msdn.microsoft.com/library/hh452232.aspx)

[Definir as propriedades do serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificação de partilha de recursos de várias origens de W3C](http://www.w3.org/TR/cors/)

