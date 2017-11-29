---
title: "Interação humana e tempos limite nas funções durável - Azure"
description: "Saber como lidar com uma interação humana e tempos limite na extensão do durável funções para as funções do Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 822abf5cd09a0cd0d66441acfe4ae114c6ba73eb
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana nas funções durável - exemplo de verificação do telefone

Este exemplo demonstra como criar um [funções durável](durable-functions-overview.md) orchestration que envolve a interação humana. Sempre que uma pessoa real que está envolvida um processo automatizado, o processo tem de ser capaz de enviar notificações à pessoa e receber respostas no modo assíncrono. Deve também permitir que para a possibilidade de que a pessoa que está disponível. (Esta última parte é onde os tempos limite ficam importantes).

Este exemplo implementa um sistema de verificação do telefone baseados no SMS. Estes tipos de fluxos são frequentemente utilizados ao verificar o número de telefone de um cliente ou para autenticação multifator (MFA). Este é um exemplo de elevado desempenho, porque a implementação completa é feita utilizando algumas funções pequenas. Nenhum arquivo de dados externos, como uma base de dados, é necessário.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [instalar funções durável](durable-functions-install.md) para configurar o exemplo.
* Este artigo pressupõe que já leu o [Hello sequência](durable-functions-sequence.md) instruções de exemplo.

## <a name="scenario-overview"></a>Descrição geral do cenário

Verificação do telefone é utilizada para verificar que os utilizadores finais da sua aplicação não são spammers e sejam que diga a estão. Autenticação multifator é um caso de utilização comuns para proteger contas de utilizador contra hackers. O desafio com implementar a seus próprios verificação do telefone é que requer um **interação com monitorização de estado** com uma ser humanos. Um utilizador final é, geralmente, fornecido algum código (por exemplo, um número de 4 dígitos) e tem de responder **dentro de um período de tempo razoável**.

Comum das funções do Azure sem monitorização de estado (uma vez que são muitos outros pontos finais na nuvem em outras plataformas), pelo que estes tipos de interações irão implicar explicitamente gerir externamente na base de dados ou alguns outros persistente armazenamento de Estados. Além disso, a interação tem de ser dividida cópias de segurança em várias funções que podem ser coordenadas em conjunto. Por exemplo, precisa de, pelo menos, uma função para decidir sobre um código, algures a persistência e enviar para o telefone do utilizador. Além disso, é necessário pelo menos uma outra função para receber uma resposta do utilizador e examinar mapeá-lo novamente para a chamada de função original para o efeito de validação do código. Limite de tempo também é um aspeto importante para garantir a segurança. Isto pode obter relativamente complexo pretty rapidamente.

A complexidade deste cenário é significativamente reduzida ao utilizar as funções durável. Como verá neste exemplo, uma função do orchestrator pode gerir a interação com monitorização de estado muito facilmente e sem envolver quaisquer arquivos de dados externas. Uma vez que as funções do orchestrator são *durável*, estes fluxos interativos também são altamente fiáveis.

## <a name="configuring-twilio-integration"></a>Configurar a integração do Twilio

Este exemplo envolve a utilização de [Twilio](https://www.twilio.com/) serviço para enviar mensagens SMS para um telemóvel. As funções do Azure já tem suporte para Twilio através de [Twilio enlace](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), e este exemplo utiliza essa funcionalidade.

A primeira coisa que precisa é uma conta do Twilio. Pode criar um livre no https://www.twilio.com/try-twilio. Assim que tiver uma conta, adicione os seguintes três **as definições de aplicação** à sua aplicação de função.

| Nome da definição de aplicação | Descrição de valor |
| - | - |
| **TwilioAccountSid**  | O SID para a sua conta do Twilio |
| **TwilioAuthToken**   | O token de autenticação para a sua conta do Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta do Twilio. Isto é utilizado para enviar mensagens SMS. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

As secções seguintes explicam o código que são utilizados para programação portal do Azure e configuração. O código para o desenvolvimento de Visual Studio é apresentado no final do artigo.
 
## <a name="the-sms-verification-orchestration"></a>A orquestração de verificação do SMS

O **E4_SmsPhoneVerification** função utiliza a norma *function.json* para funções do orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Eis o código que implementa a função:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

Depois de iniciada, esta função do orchestrator faz o seguinte:

1. Obtém um número de telefone para o qual será *enviar* a notificação de SMS.
2. Chamadas **E4_SendSmsChallenge** envie uma mensagem SMS para o utilizador e devolve novamente o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável esse acionadores 90 segundos da hora atual.
4. Em paralelo com o temporizador, aguarda que um **SmsChallengeResponse** eventos do utilizador.

O utilizador recebe uma mensagem SMS com um código de quatro dígitos. Têm de 90 segundos para enviar esse mesmo código de 4 dígitos volta para a instância de função do orchestrator para concluir o processo de verificação. Se submeter o código de errado, recebem um tenta três adicionais obtê-lo à direita (dentro da mesma janela segundo 90).

> [!NOTE]
> Podem não ser óbvios em primeiro lugar, mas esta orchestrator função é completamente determinística. Isto acontece porque o `CurrentUtcDateTime` propriedade é utilizada para calcular a hora de expiração do temporizador, e esta propriedade devolve o mesmo valor em cada reprodução neste momento no código do orchestrator. Isto é importante para se certificar de que o mesmo `winner` resultados a partir de cada chamada repetida para `Task.WhenAny`.

> [!WARNING]
> É importante [Cancelar temporizadores utilizando um CancellationTokenSource](durable-functions-timers.md) se que já não necessita da expirar, como no exemplo acima quando é aceite a uma resposta de desafio.

## <a name="send-the-sms-message"></a>Enviar a mensagem SMS

O **E4_SendSmsChallenge** função utiliza o enlace do Twilio para enviar a mensagem SMS com o código de 4 dígitos para o utilizador final. O *function.json* é definidos do seguinte modo:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

E Eis o código que gera o código de desafio de 4 dígitos e envia a mensagem SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

Isto **E4_SendSmsChallenge** função apenas obtém chamada uma vez, mesmo que o processo de falhas ou obtém reproduzido. Esta é uma boa vez que não pretende que o utilizador final obter várias mensagens SMS. O `challengeCode` devolver o valor é automaticamente persistente, para a função do orchestrator confie sempre que é o código correto.

## <a name="run-the-sample"></a>Executar o exemplo

Utilizar as funções acionadas por HTTP incluídas no exemplo, pode começar a orquestração enviando o seguinte pedido de HTTP POST.

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```
```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A função do orchestrator recebe o número de telefone fornecido e envia-imediatamente uma mensagem SMS com um código de verificação de 4 dígitos gerado aleatoriamente &mdash; por exemplo, *2168*. A função, em seguida, aguarda 90 segundos para uma resposta.

Responder com o código, pode utilizar `RaiseEventAsync` dentro de outra função ou da invocação de **sendEventUrl** webhook HTTP POST referenciada na resposta 202 acima, substituindo `{eventName}` com o nome do evento, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se enviar esta antes do temporizador expira, o orchestration estiver concluída e a `output` campo está definido como `true`, que indica que uma verificação com êxito.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar o temporizador expirar, ou se introduzir o código de errado quatro vezes, pode consultar o estado e ver um `false` orchestration a função de saída, que indica que falharam na verificação do telefone.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis o orchestration como um único c# ficheiro um projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Passos seguintes

Este exemplo tem demonstrou algumas das funcionalidades avançadas de funções durável, nomeadamente `WaitForExternalEvent` e `CreateTimer`. Que viu a forma como estas podem ser conjugadas com `Task.WaitAny` para implementar um sistema de tempo limite fiável, muitas vezes, é útil para interagir com pessoas reais. Pode saber mais sobre como utilizar funções duráveis através da leitura de uma série de artigos que oferecem aprofundada cobertura de tópicos específicos.

> [!div class="nextstepaction"]
> [Vá para o artigo primeiro da série](durable-functions-bindings.md)
