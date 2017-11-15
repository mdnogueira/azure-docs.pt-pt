---
title: "Guia de protocolo as ligações híbridas do reencaminhamento do Azure | Microsoft Docs"
description: "Guia de protocolo de ligações híbridas de reencaminhamento do Azure."
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 9d015678dbd99b8d978c2c8200b36bf51cac8893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de ligações híbridas de reencaminhamento do Azure
Reencaminhamento do Azure é uma das pillars a capacidade de chave de plataforma do Service Bus do Azure. A nova *ligações híbridas* capacidade de reencaminhamento é uma evolução segura e protocolo open com base em HTTP e WebSockets. Este substitui o anterior, igualmente denominado *BizTalk Services* funcionalidade que foi criada no foundation protocolo proprietário. A integração de ligações híbridas para os serviços de aplicações do Azure irá continuar a funcionar como-é.

As ligações híbridas permite a comunicação de stream binário de bidirecionais entre duas aplicações de rede, durante os quais as partes de um ou ambos os podem residir atrás de firewalls ou dos NATs. Este artigo descreve as interações do lado do cliente com o reencaminhamento de ligações híbridas para ligação de clientes no serviço de escuta e funções do remetente e, como os serviços de escuta aceitarem novas ligações.

## <a name="interaction-model"></a>Modelo de interação
O reencaminhamento de ligações híbridas liga-se duas partes, fornecendo um ponto de encontro na nuvem do Azure que as entidades confiadoras podem detetar e ligar a partir da perspetiva da sua própria rede. Esse ponto de encontro é chamado "Ligação híbrida" neste e outra documentação, nas APIs e também no portal do Azure. O ponto final de serviço de ligações híbridas é referido como "serviço" para o resto deste artigo. O modelo de interação leans no nomenclature estabelecido pelo muitas outras APIs de rede.

Não há um serviço de escuta que primeiro indica preparação para processar ligações de entrada e, subsequentemente, aceita-las à medida que chegam. No outro lado, há um cliente de ligação que estabelece ligação para o serviço de escuta, era esperado essa ligação sejam aceites para estabelecer um caminho de comunicação bidirecional.
"Ligar", "Escuta" e "Aceitar" é mesmos termos que encontrará no socket a maioria das APIs.

Qualquer modelo de comunicação retransmitidas tem a efetuar ligações de saída para um ponto final de serviço, que faz com que o "serviço de escuta" também um "cliente" em utilização colloquial e também pode fazer com que outras sobrecargas terminologia de terceiros. Segue-se a terminologia precisa que utilizamos, por conseguinte, para as ligações híbridas:

Os programas em ambos os lados de uma ligação são denominados "clientes", uma vez que estes clientes para o serviço. O cliente que aguarda e aceita ligações é o "serviço de escuta" ou possui correspondências é denominado "função de serviço de escuta." O cliente que inicia uma nova ligação para um serviço de escuta através do serviço é chamado "remetente", ou está na "função de remetente".

### <a name="listener-interactions"></a>Interações do serviço de escuta
O serviço de escuta tem quatro interações com o serviço; todos os detalhes de transmissão são descritos neste artigo na secção de referência.

#### <a name="listen"></a>Escutar
Para indicar a preparação para o serviço que um serviço de escuta está pronto para aceitar ligações, cria uma ligação de WebSocket saída. O handshake de ligação acarreta o nome de uma ligação híbrida configurado no espaço de nomes de reencaminhamento e um token de segurança que confers "Escuta" correto esse nome.
Quando o WebSocket é aceite pelo serviço, o registo é concluído e o web estabelecida WebSocket é mantida activa como "canal de controlo" para ativar todas as interações subsequentes. O serviço permite que os serviços de escuta em simultâneo até 25 numa ligação híbrida. Se existirem dois ou mais serviços de escuta Active Directory, ligações de entrada são equilibradas entre-los pela ordem aleatória; distribuição justa não é garantida.

#### <a name="accept"></a>Aceitar
Quando um remetente abre uma nova ligação do serviço, o serviço escolhe e notifica um dos serviços de escuta de Active Directory na ligação híbrida. Esta notificação é enviada para o serviço de escuta através do canal de controlo aberto como uma mensagem JSON que contém o URL do ponto final de WebSocket que o serviço de escuta tem de ligar para aceitar a ligação.

O URL pode e tem de ser utilizado diretamente pelo serviço de escuta sem qualquer trabalho adicional.
As informações codificadas só são válidas durante um curto período de tempo, essencialmente, desde que o remetente está disposto a aguardar para a ligação ser estabelecida ponto-a-ponto, mas, até um máximo de 30 segundos. O URL só pode ser utilizado para a tentativa de uma ligação com êxito. Assim que a ligação de WebSocket com o URL de encontro é estabelecida, toda a atividade adicional neste WebSocket é retransmitida de e para o remetente, sem qualquer intervenção ou a interpretação pelo serviço.

#### <a name="renew"></a>Renovar
O token de segurança que tem de ser utilizado para registar o serviço de escuta e manter o canal de controlo pode expirar enquanto o serviço de escuta está ativo. A expiração do token não afeta as ligações em curso, mas fazer com que o canal de controlo ser removido pelo serviço, ou logo após o momento de expiração. A operação "renovar" é uma mensagem JSON que o serviço de escuta pode enviar para substituir o token associado com o canal de controlo, para que o canal de controlo pode ser mantido por períodos prolongados.

#### <a name="ping"></a>Ping
Se o canal de controlo permanece inativo durante muito tempo, intermediários na forma, tais como a carga balanceadores ou os NATs podem remover a ligação de TCP. A operação de "ping" evita que enviando uma pequena quantidade de dados no canal que relembra todas as pessoas a rota de rede que a ligação se destinar a ser alive, e também serve como um teste de "dinâmicos" para o serviço de escuta. Se o ping falhar, o canal de controlo deve ser considerado inutilizável e o serviço de escuta deve voltar a ligar.

### <a name="sender-interaction"></a>Interação de remetente
O remetente tem apenas uma única interação com o serviço: para estabelecer a ligação.

#### <a name="connect"></a>Ligar
A operação "ligar" abre um WebSocket no serviço, fornecendo o nome da ligação híbrida e (opcionalmente, mas necessário por predefinição) token de segurança conferring permissão "Enviar" na cadeia de consulta. O serviço, em seguida, interage com o serviço de escuta da forma descrito anteriormente, e o serviço de escuta cria uma ligação de encontro que está associada este WebSocket. Depois de ter sido aceite o WebSocket, todas as interações adicionais em que WebSocket são com um serviço de escuta ligado.

### <a name="interaction-summary"></a>Interação resumo
O resultado deste modelo de interação é que o cliente do remetente vem fora do handshake com um WebSocket "Limpar", que está ligado a um serviço de escuta e que não necessita de nenhum mais preambles ou preparação. Este modelo permite praticamente qualquer implementação de cliente WebSocket existente prontamente tirar partido do serviço de ligações híbridas, fornecendo um URL construído corretamente na respetiva camada de cliente WebSocket.

A ligação de encontro WebSocket que o serviço de escuta obtém através da interação aceitar também está limpa e pode ser entregar para qualquer implementação de servidor de WebSocket existente com alguns abstração extra mínima que distingue entre operações de "aceitar" nos serviços de escuta do respetivo framework rede local e as operações de remoto "aceitar" ligações híbridas.

## <a name="protocol-reference"></a>Referência do protocolo

Esta secção descreve os detalhes das interações de protocolo descritos anteriormente.

Todas as ligações de WebSocket que são efetuadas na porta 443 como uma atualização do 1.1 de HTTPS, o que é normalmente abstracted por alguns framework WebSocket ou API. A descrição aqui é mantida implementação independente, sem sugerindo uma arquitetura específica.

### <a name="listener-protocol"></a>Protocolo de serviço de escuta
O protocolo serviço de escuta é constituído por dois gestos de ligação e três operações de mensagem.

#### <a name="listener-control-channel-connection"></a>Ligação de canal de controlo do serviço de escuta
O canal de controlo é aberto com criação de uma ligação de WebSocket para:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

O `namespace-address` é o nome de domínio completamente qualificado do espaço de nomes do reencaminhamento do Azure que aloja a ligação híbrida, normalmente com a forma `{myname}.servicebus.windows.net`.

As opções de parâmetros de cadeia de consulta são os seguintes.

| Parâmetro | Necessário | Descrição |
| --- | --- | --- |
| `sb-hc-action` |Sim |Para a função de serviço de escuta do parâmetro tem de ser **sb-hc-action = escuta** |
| `{path}` |Sim |O caminho de espaço de nomes com codificação URL da ligação híbrida pré-configurada para registar este serviço de escuta no. É acrescentada esta expressão para o fixo `$hc/` parte do caminho. |
| `sb-hc-token` |Sim\* |O serviço de escuta tem de fornecer um válido, com codificação URL Service Bus partilhado acesso Token para o espaço de nomes ou da ligação híbrida que confers o **escutar** à direita. |
| `sb-hc-id` |Não |Este ID fornecido pelo cliente opcional que permite que o rastreio de diagnóstico ponto-a-ponto. |

Se a ligação de WebSocket falhar devido ao caminho da ligação híbrida não a ser registado, ou um token inválido ou em falta ou alguns outros erros, os comentários de erro é fornecido com o modelo de comentários de estado de HTTP 1.1 regular. A descrição de estado contém um controlo-id de erro que pode ser comunicado ao pessoal de suporte do Azure:

| Código | Erro | Descrição |
| --- | --- | --- |
| 404 |Não foi encontrado |O caminho da ligação híbrida é inválido ou o URL de base tem um formato incorreto. |
| 401 |Não autorizado |O token de segurança está em falta ou com formato incorreto ou é inválido. |
| 403 |Proibido |O token de segurança não é válido para este caminho para esta ação. |
| 500 |Erro interno |Ocorreu um erro no serviço. |

Se a ligação de WebSocket é intencionalmente encerrada pelo serviço depois de este foi inicialmente configurada, o motivo para fazê-lo pelo que é comunicado utilizando um código de erro de protocolo de WebSocket adequado, juntamente com uma mensagem de erro descritivo também inclui um ID de controlo. O serviço irá não encerrar o canal de controlo sem encontrar uma condição de erro. Qualquer encerramento correto é controlado de cliente.

| Estado de WS | Descrição |
| --- | --- |
| 1001 |O caminho da ligação híbrida foi eliminado ou desativado. |
| 1008 |O token de segurança expirou, pelo que a política de autorização é violada. |
| 1011 |Ocorreu um erro no serviço. |

### <a name="accept-handshake"></a>Aceitar handshake
A notificação "aceitar" é enviada pelo serviço para o serviço de escuta, através do canal de controlo anteriormente estabelecido como uma mensagem JSON num intervalo de texto de WebSocket. Não há nenhuma resposta a esta mensagem.

A mensagem contém um objeto JSON com o nome "aceitar", que define as propriedades seguintes neste momento:

* **endereço** – a cadeia de URL a utilizar para estabelecer o WebSocket para o serviço para aceitar uma ligação recebida.
* **ID** – o identificador exclusivo para esta ligação. Se o ID de foi fornecido pelo cliente de remetente, que é o valor do remetente fornecido, caso contrário, é um valor de gerada pelo sistema.
* **connectHeaders** – todos os cabeçalhos HTTP que foi fornecidos para o ponto final de reencaminhamento pelo remetente, que também inclui o protocolo de WebSocket seg e os cabeçalhos de extensões de WebSocket de seg.

#### <a name="accept-message"></a>Aceitar a mensagem

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",  
        "connectHeaders" : {                                         
            "Host" : "...",                                                
            "Sec-WebSocket-Protocol" : "...",                              
            "Sec-WebSocket-Extensions" : "..."                             
        }                                                            
     }                                                         
}
```

O URL de endereço fornecido na mensagem JSON é utilizado pelo serviço de escuta para estabelecer o WebSocket para aceitar ou rejeitar o socket do remetente.

#### <a name="accepting-the-socket"></a>Ao aceitar o socket
Para aceitar, o serviço de escuta estabelece uma ligação de WebSocket para o endereço fornecido.

Se a mensagem "aceitar" acarreta um `Sec-WebSocket-Protocol` cabeçalho, é esperado que o serviço de escuta só aceita o WebSocket se suporta esse protocolo. Além disso, define o cabeçalho como o WebSocket é estabelecido.

O mesmo se aplica para o `Sec-WebSocket-Extensions` cabeçalho. Se a estrutura suporta uma extensão, defini-lo deve o cabeçalho de resposta de lado do servidor de necessários `Sec-WebSocket-Extensions` handshake para a extensão.

O URL tem de ser utilizado como-é para estabelecer o socket de aceitar, mas contém os seguintes parâmetros:

| Parâmetro | Necessário | Descrição |
| --- | --- | --- |
| `sb-hc-action` |Sim |Para aceitar um socket, o parâmetro tem de ser`sb-hc-action=accept` |
| `{path}` |Sim |(consulte o parágrafo seguinte) |
| `sb-hc-id` |Não |Ver descrição anterior **id**. |

`{path}`é o caminho de espaço de nomes com codificação URL da ligação híbrida pré-configurada em que pretende registar este serviço de escuta. É acrescentada esta expressão para o fixo `$hc/` parte do caminho. 

O `path` expressão pode ser expandida com um sufixo e uma expressão de cadeia de consulta que se segue o nome registado após uma barra separating. Isto permite que o cliente do remetente passar os argumentos de distribuição para o serviço de escuta aceitar quando não é possível incluir cabeçalhos de HTTP. As expectativas são de que a arquitetura do serviço de escuta analisa a parte do caminho fixo e o nome do caminho de registado e faz com que o resto, possivelmente sem quaisquer argumentos de cadeia de consulta como prefixo `sb-`, disponível para a aplicação para decidir se pretende aceitar a ligação.

Para obter mais informações, consulte a secção "Remetente protocolo".

Se houver um erro, o serviço pode responder a forma:

| Código | Erro | Descrição |
| --- | --- | --- |
| 403 |Proibido |O URL não é válido. |
| 500 |Erro interno |Ocorreu um erro no serviço |

Depois da ligação for estabelecida, o servidor encerra o WebSocket quando o remetente WebSocket encerrado para baixo, ou com o estado do seguinte:

| Estado de WS | Descrição |
| --- | --- |
| 1001 |O cliente do remetente será encerrado a ligação. |
| 1001 |O caminho da ligação híbrida foi eliminado ou desativado. |
| 1008 |O token de segurança expirou, pelo que a política de autorização é violada. |
| 1011 |Ocorreu um erro no serviço. |

#### <a name="rejecting-the-socket"></a>Rejeitar o socket
Rejeitar o socket após analisando a mensagem "aceitar" necessita de um handshake semelhante para o código de estado e a descrição de Estado comunicar o motivo para a rejeição possam circular para o remetente.

A escolha de design do protocolo está a utilizar um handshake de WebSocket (que foi concebido para terminar com um Estado de erro definido), para que as implementações de cliente do serviço de escuta pode continuar a depender de um cliente WebSocket e não precisa de utilizar um extra, bare cliente HTTP.

Rejeitar o socket, o cliente utiliza o endereço URI da mensagem de "aceitar" e acrescenta dois parâmetros de cadeia de consulta, da seguinte forma:

| Param | Necessário | Descrição |
| --- | --- | --- |
| statusCode |Sim |Código de estado HTTP numérico. |
| StatusDescription |Sim |Motivo legível humano a rejeição. |

O URI resultante, em seguida, é utilizado para estabelecer uma ligação de WebSocket.

Quando concluir corretamente, este handshake intencionalmente ocorre uma falha com um código de erro HTTP 410, desde que foi estabelecida não WebSocket. Se houver algum problema, os códigos seguintes descrevem o erro:

| Código | Erro | Descrição |
| --- | --- | --- |
| 403 |Proibido |O URL não é válido. |
| 500 |Erro interno |Ocorreu um erro no serviço. |

### <a name="listener-token-renewal"></a>Renovação de token de serviço de escuta
Quando o token de serviço de escuta está prestes a expirar, pode substituir esta enviando uma mensagem de moldura de texto para o serviço através do canal de controlo estabelecida. A mensagem contém um objeto JSON chamado `renewToken`, que define a seguinte propriedade neste momento:

* **token** – um token de acesso de partilhado de barramento de serviço válido, com codificação URL para o espaço de nomes ou da ligação híbrida que confers o **escutar** à direita.

#### <a name="renewtoken-message"></a>mensagem de renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Se a falha de validação de token, o acesso é negado e o serviço em nuvem se fechar o canal de controlo de WebSocket com um erro. Caso contrário, não há nenhuma resposta.

| Estado de WS | Descrição |
| --- | --- |
| 1008 |O token de segurança expirou, pelo que a política de autorização é violada. |

## <a name="sender-protocol"></a>Protocolo de remetente
O protocolo de remetente é efetivamente idêntico para a forma como um serviço de escuta é estabelecido.
O objetivo é transparência máxima para o WebSocket ponto-a-ponto. O endereço para ligar ao é igual para o serviço de escuta, mas "action" é diferente e o token tem uma permissão diferentes:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

O *endereço de espaço de nomes* é o nome de domínio completamente qualificado do espaço de nomes do reencaminhamento do Azure que aloja a ligação híbrida, normalmente com a forma `{myname}.servicebus.windows.net`.

O pedido pode conter arbitrários Extras os cabeçalhos de HTTP, incluindo aqueles definido pela aplicação. Todos os cabeçalhos fornecidos fluir para o serviço de escuta e pode ser encontrados no `connectHeader` objeto do **aceitar** mensagem de controlo.

As opções de parâmetros de cadeia de consulta são os seguintes:

| Param | Necessário? | Descrição |
| --- | --- | --- |
| `sb-hc-action` |Sim |Para a função de remetente, o parâmetro tem de ser `action=connect`. |
| `{path}` |Sim |(consulte o parágrafo seguinte) |
| `sb-hc-token` |Sim\* |O serviço de escuta tem de fornecer um válido, com codificação URL Service Bus partilhado acesso Token para o espaço de nomes ou da ligação híbrida que confers o **enviar** à direita. |
| `sb-hc-id` |Não |Um ID opcional que permite o rastreio de diagnóstico ponto a ponto e é disponibilizado para o serviço de escuta durante o handshake de aceitar. |

O `{path}` é o caminho de espaço de nomes com codificação URL da ligação híbrida pré-configurada em que pretende registar este serviço de escuta. O `path` expressão pode ser expandida com um sufixo e uma expressão de cadeia de consulta para obter mais comunicar. Se a ligação híbrida estiver registada no caminho `hyco`, a `path` expressão pode ser `hyco/suffix?param=value&...` seguido os parâmetros de cadeia de consulta definidos aqui. Uma expressão completa, em seguida, pode ser da seguinte forma:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

O `path` expressão é transferida para o serviço de escuta no endereço URI contido na mensagem de controlo "aceitar".

Se a ligação de WebSocket falhar devido ao caminho da ligação híbrida não registada, um token inválido ou em falta ou alguns outros erros, os comentários de erro é fornecido com o modelo de comentários de estado de HTTP 1.1 regular. A descrição de estado contém um erro de controlo ID que pode ser comunicada ao pessoal de suporte do Azure:

| Código | Erro | Descrição |
| --- | --- | --- |
| 404 |Não foi encontrado |O caminho da ligação híbrida é inválido ou o URL de base tem um formato incorreto. |
| 401 |Não autorizado |O token de segurança está em falta ou com formato incorreto ou é inválido. |
| 403 |Proibido |O token de segurança não é válido para este caminho e para esta ação. |
| 500 |Erro interno |Ocorreu um erro no serviço. |

Se a ligação de WebSocket foi intencionalmente encerrada pelo serviço depois de ter sido definido inicialmente cópias de segurança, o motivo para fazê-lo pelo que é comunicado utilizando um código de erro de protocolo de WebSocket adequado, juntamente com uma mensagem de erro descritivo também inclui um ID de controlo.

| Estado de WS | Descrição |
| --- | --- |
| 1000 |O serviço de escuta encerrar o socket. |
| 1001 |O caminho da ligação híbrida foi eliminado ou desativado. |
| 1008 |O token de segurança expirou, pelo que a política de autorização é violada. |
| 1011 |Ocorreu um erro no serviço. |

## <a name="next-steps"></a>Passos seguintes
* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

