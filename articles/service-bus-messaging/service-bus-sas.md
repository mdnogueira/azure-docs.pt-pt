---
title: "Autenticação de Service Bus do Azure com assinaturas de acesso partilhado | Microsoft Docs"
description: "Descrição geral da autenticação de barramento de serviço através da descrição geral de assinaturas de acesso partilhado, detalhes sobre autenticação de SAS com o Service Bus do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Autenticação de Service Bus com assinaturas de acesso partilhado

*Assinaturas de acesso partilhado* (SAS) são o mecanismo de principal de segurança de mensagens do Service Bus. Este artigo aborda SAS, como funcionam e como utilizá-los de forma agnóstico plataforma.

Autenticação de SAS permite que as aplicações autenticar para o Service Bus, utilizando uma chave de acesso configurada no espaço de nomes ou na entidade Serviço de mensagens (fila ou um tópico) que estão associados direitos específicos. Em seguida, pode utilizar esta chave para gerar um token SAS que os clientes por sua vez podem utilizar para autenticar para o Service Bus.

Suporte de autenticação SAS é incluído no SDK do Azure versão 2.0 e posterior.

## <a name="overview-of-sas"></a>Descrição geral da SAS

Assinaturas de acesso partilhado são um mecanismo de autenticação com base nos hashes segurados SHA-256 ou URI. SAS é um mecanismo extremamente poderoso que é utilizado por todos os serviços do Service Bus. Em utilização real, SAS tem dois componentes: um *partilhado a política de acesso* e um *assinatura de acesso partilhado* (, frequentemente, designado uma *token*).

Autenticação de SAS no Service Bus envolve a configuração de uma chave criptográfica com direitos associados num recurso de Service Bus. Os clientes de acesso a recursos do Service Bus afirmações através da apresentação de um token SAS. Este token é composta pela ser consultado de URI do recurso e uma expiração assinada com a chave configurada.

Pode configurar regras de autorização de assinatura de acesso partilhado no Service Bus [reencaminha](service-bus-fundamentals-hybrid-solutions.md#relays), [filas](service-bus-fundamentals-hybrid-solutions.md#queues), e [tópicos](service-bus-fundamentals-hybrid-solutions.md#topics).

Autenticação de SAS utiliza os seguintes elementos:

* [Regra de autorização de acesso partilhada](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): A 256 bits primária chave criptográfica em Base64 representação, uma chave secundária opcional e um nome de chave e direitos associados (uma coleção de *escutar*, *enviar*, ou *gerir* direitos).
* [Assinatura de acesso partilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token: gerado utilizando o HMAC-SHA256 de uma cadeia de recurso, constituída pelo URI do recurso que é acedido e uma expiração, com a chave criptográfica. A assinatura e outros elementos descritos nas secções seguintes são formatados numa cadeia para formar o token SAS.

## <a name="shared-access-policy"></a>Política de acesso partilhado

Um mais importante para compreender sobre SAS é que começa com uma política. Para cada política, que opte por utilizar três informações: **nome**, **âmbito**, e **permissões**. O **nome** apenas que; é um nome exclusivo nesse âmbito. O âmbito é suficientemente fácil: é o URI do recurso em questão. Para um espaço de nomes do Service Bus, o âmbito é o nome de domínio completamente qualificado (FQDN), tal como `https://<yournamespace>.servicebus.windows.net/`.

As permissões disponíveis para uma política dispensam explicações amplamente:

* Enviar
* Escutar
* Gerir

Depois de criar a política, é-lhe atribuído um *chave primária* e um *chave secundária*. Estes são chaves criptograficamente seguras. Não perca-los ou fuga-los - estejam sempre disponíveis no [portal do Azure][Azure portal]. Pode utilizar qualquer uma das chaves geradas e pode voltar a gerá-los em qualquer altura. No entanto, se voltar a gerar ou alterar a chave primária na política, serão invalidadas qualquer assinaturas de acesso partilhado criado a partir do mesmo.

Quando cria um espaço de nomes do Service Bus, é automaticamente criada uma política para o espaço de nomes todo chamado **RootManageSharedAccessKey**, e esta política tem todas as permissões. Não inicie sessão como **raiz**, por isso, não utilize esta política, a menos que exista um realmente bom motivo. Pode criar políticas adicionais no **configurar** separador para o espaço de nomes no portal. É importante ter em atenção que um nível de árvore único no Service Bus (espaço de nomes, fila, etc.) só pode ter até 12 políticas anexadas ao mesmo.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração de autenticação da assinatura de acesso partilhado
Pode configurar o [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regra em espaços de nomes do Service Bus, filas ou tópicos. Configurar um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) um barramento de serviço subscrição atualmente não é suportada, mas pode utilizar regras configuradas num espaço de nomes ou tópico para proteger o acesso às subscrições. Para um exemplo de trabalho que ilustra a este procedimento, consulte o [autenticação a utilizar acesso assinatura partilhado (SAS) com as subscrições do Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exemplo.

Um máximo de 12 dessas regras pode ser configurado num espaço de nomes de barramento de serviço, fila ou tópico. Regras que são configuradas num espaço de nomes do Service Bus são aplicadas a todas as entidades nesse espaço de nomes.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Este figura a *manageRuleNS*, *sendRuleNS*, e *listenRuleNS* regras de autorização são aplicadas a fila Q1 e tópico T1, enquanto *listenRuleQ* e *sendRuleQ* aplicam-se apenas a fila Q1 e *sendRuleT* aplica-se apenas ao tópico T1.

Os parâmetros de chaves de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) são os seguintes:

| Parâmetro | Descrição |
| --- | --- |
| *KeyName* |Uma cadeia que descreve a regra de autorização. |
| *PrimaryKey* |Uma com codificação base64 256 bits chave primária para assinatura e a validar o token SAS. |
| *SecondaryKey* |Uma com codificação base64 256 bits chave secundária para a assinatura e a validar o token SAS. |
| *AccessRights* |Uma lista de direitos de acesso concedido pela regra de autorização. Estes direitos podem ser qualquer coleção de direitos de escuta, enviar e gerir. |

Quando um espaço de nomes do Service Bus é aprovisionado, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), com [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) definido como **RootManageSharedAccessKey**, é criada por predefinição.

## <a name="generate-a-shared-access-signature-token"></a>Gerar uma assinatura de acesso partilhado (token)

A política propriamente dito não é o token de acesso para o Service Bus. É o objeto a partir dos quais é gerado o token de acesso - utilizando o a chave primária ou secundária. Qualquer cliente que tem acesso às chaves de assinatura especificada na regra de autorização de acesso partilhado pode gerar o token SAS. O token é gerado por uma cadeia no seguinte formato de composição cuidadosamente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Onde `signature-string` é o hash SHA-256 do âmbito do token (**âmbito** conforme descrito na secção anterior) com um CRLF anexado e uma hora de expiração (em segundos desde a época: `00:00:00 UTC` no 1 de Janeiro de 1970). 

> [!NOTE]
> Para evitar uma hora de expiração do token curto, é recomendado que codificar o valor de tempo de expiração como, pelo menos, um número inteiro sem sinal de 32 bits ou, preferencialmente, um número inteiro longa (64 bits).  
> 
> 

O hash semelhante para o seguinte código pseudo e devolve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Os valores hash não estão no **SharedAccessSignature** para que o destinatário pode calcular o hash com os mesmos parâmetros, para assegurar que devolve o mesmo resultado de cadeia. O URI Especifica o âmbito e o nome da chave identifica a política para ser utilizado para calcular o hash. Isto é importante de um ponto de vista de segurança. Se a assinatura não corresponde à qual calcula o destinatário (Service Bus), o acesso é negado. Neste momento pode ter a certeza de que o remetente tinha acesso para a chave e deve ser concedido especificados na política de direitos.

Tenha em atenção que deve utilizar o URI do recurso codificado para esta operação. O URI do recurso é o URI do recurso de barramento de serviço ao qual foi reclamado acesso completo. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`; ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

A regra de autorização de acesso partilhado utilizada para a assinatura tem de ser configurada na entidade especificada por este URI ou por um dos respetivos pais hierárquicas. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos sob o `<resourceURI>` utilizado no `signature-string`.

O [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) a SAS token refere-se à **keyName** da regra de autorização de acesso partilhado utilizada para gerar o token.

O *resourceURI URL codificados* tem de ser o mesmo que o URI utilizado em cadeia a sessão durante o cálculo da assinatura. Deve ser [codificado por cento](https://msdn.microsoft.com/library/4fkewx0t.aspx).

É recomendado que periodicamente regenerar as chaves utilizadas no [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto. As aplicações, geralmente, devem utilizar o [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) para gerar um token SAS. Quando regenerar as chaves, deve substituir o [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) com o principal anterior da chave e gerar uma nova chave como a nova chave primária. Isto permite-lhe continuar a utilização de tokens de autorização que foram emitidos com a chave primária antiga e que não ainda expiraram.

Se uma chave fica comprometida e tem de revogar as chaves, pode voltar a gerar ambas as [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) e o [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), substituindo-los com chaves novas. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Como utilizar a autenticação de assinatura de acesso partilhado com o Service Bus

Os seguintes cenários incluem a configuração das regras de autorização, a geração de autorização de cliente e os tokens SAS.

Para um completo amostra de trabalho de uma aplicação de Service Bus que ilustra a autorização de SAS de configuração e utilizações, consulte [autenticação da assinatura de acesso partilhado com o Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Um exemplo relacionado que ilustra a utilização de regras de autorização de SAS configuradas nos espaços de nomes ou tópicos para proteger as subscrições do Service Bus está disponível aqui: [autenticação a utilizar acesso assinatura partilhado (SAS) com as subscrições do Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Regras de autorização de acesso partilhado de acesso de um espaço de nomes

As operações na raiz do espaço de nomes de barramento de serviço requerem a autenticação de certificados. Tem de carregar um certificado de gestão para a sua subscrição do Azure. Para carregar um certificado de gestão, siga os passos [aqui](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate), utilizando o [portal do Azure][Azure portal]. Para obter mais informações sobre certificados de gestão do Azure, consulte o [descrição geral de certificados do Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

O ponto final para aceder às regras de autorização de acesso partilhado no espaço de nomes de barramento de serviço é o seguinte:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Para criar um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto num espaço de nomes do Service Bus, executar uma operação POST neste ponto final com as informações de regras serializadas como JSON ou XML. Por exemplo:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Da mesma forma, utilize uma operação GET no ponto final para as regras de autorização configuradas no espaço de nomes de leitura.

Para atualizar ou eliminar uma regra de autorização específico, utilize o seguinte ponto final:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Regras de autorização de acesso partilhado de acesso numa entidade

Pode aceder a um [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto configurado numa fila do Service Bus ou um tópico através de [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) coleção no correspondente [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

O código seguinte mostra como adicionar regras de autorização de uma fila.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Utilize a autorização de assinatura de acesso partilhado

As aplicações que utilizam o SDK .NET do Azure com a bibliotecas .NET de barramento de serviço podem utilizar a autorização de SAS através do [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) classe. O código seguinte ilustra a utilização do fornecedor de tokens para enviar mensagens para uma fila do Service Bus.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

As aplicações podem também utilizar SAS para autenticação através da utilização de uma cadeia de ligação de SAS em métodos que aceitam cadeias de ligação.

Tenha em atenção que, para utilizar a autorização de SAS com reencaminhamentos do Service Bus, pode utilizar as chaves SAS configuradas no espaço de nomes de barramento de serviço. Se criar explicitamente um reencaminhamento no espaço de nomes ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) com um [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) de objeto, pode definir as regras SAS apenas para esse reencaminhamento. Para utilizar a autorização de SAS com subscrições do Service Bus, pode utilizar as chaves SAS configuradas num espaço de nomes de barramento de serviço ou um tópico.

## <a name="use-the-shared-access-signature-at-http-level"></a>Utilize a assinatura de acesso partilhado (a nível HTTP)

Agora que sabe como criar assinaturas de acesso partilhado para todas as entidades de Service Bus, está pronto para executar um HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Lembre-se de que isto funciona para todos os fins. Pode criar SAS para uma fila, tópico ou de subscrição. 

Se receber um remetente ou cliente um token SAS, não tem a chave diretamente e não é possível reverter o hash para obtê-lo. Como tal, tem controlo sobre o que pode aceder e como período de tempo. Um mais importante lembrar-se é que, se alterar a chave primária na política, as assinaturas de acesso partilhado criado a partir do mesmo serão invalidadas.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Utilize a assinatura de acesso partilhado (nível AMQP)

Na secção anterior, vimos como utilizar o token SAS com um pedido POST de HTTP para enviar dados para o Service Bus. Como souber, pode aceder ao Service Bus com o Advanced mensagem colocação protocolo (AMQP) que é o protocolo preferencial a utilizar por motivos de desempenho, em vários cenários. A utilização de token SAS com AMQP é descrita no documento [AMQP Claim-Based segurança versão 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está no trabalho rascunho desde 2013 mas bem suportados pelo Azure hoje.

Antes de começar a enviar dados para o Service Bus, o publicador tem de enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido com o nome **$cbs** (pode vê-lo como uma fila "especial" utilizada pelo serviço de adquirir e validar os tokens de SAS). O publicador tem de especificar o **ReplyTo** campo dentro da mensagem AMQP; este é o nó no qual o serviço responde ao publicador com o resultado da validação do token (um padrão de pedido/resposta simples entre o publicador e o serviço). Este nó de resposta é criado "no momento," falando sobre "criação dinâmica de nó remoto", conforme descrito pela especificação AMQP 1.0. Após verificar que o token SAS é válido, o fabricante pode avançar e começar a enviar dados para o serviço.

Os passos seguintes mostram como enviar o token SAS com a utilização do protocolo AMQP o [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) biblioteca. Isto é útil se não é possível utilizar o SDK do Service Bus oficial (por exemplo, num WinRT, .net Compact Framework, o .net Micro Framework e Mono) desenvolver no C\#. Obviamente, esta biblioteca é útil para ajudar a compreender como afirmações com base na segurança funciona ao nível do AMQP, conforme mostrado como funciona a nível de HTTP (com um pedido POST de HTTP e o token SAS enviados dentro do cabeçalho "Authorization"). Se não precisar desses conhecimentos profundo sobre AMQP, pode utilizar o SDK oficial do Service Bus com o .net aplicações Framework, que irão fazer por si.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

O `PutCbsToken()` método recebe o *ligação* (instância de classe de ligação AMQP conforme fornecida pelo [biblioteca AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) que representa a ligação de TCP para o serviço e o *sasToken* token de parâmetro que é a SAS para enviar. 

> [!NOTE]
> É importante que a ligação é criada com **mecanismo de autenticação SASL definido para externo** (e não o predefinido simples com o nome de utilizador e palavra-passe utilizada quando não precisa de enviar o token SAS).
> 
> 

Em seguida, o publicador cria duas ligações AMQP para enviar o token SAS e receber a resposta (o resultado da validação de token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e informações mais do que uma simple mensagem. O token SAS é o corpo da mensagem (utilizando o respetivo construtor). O **"ReplyTo"** propriedade está definida como o nome do nó para receber o resultado da validação da ligação de recetor (pode alterar o nome se pretender, e será criado dinamicamente pelo serviço). As últimas três propriedades da aplicação/personalizada são utilizadas pelo serviço para indicar que tipo de operação tem de executar. Tal como descrito pela especificação de rascunho CBS, têm de ser o **nome da operação** ("put-token"), o **tipo de token** (neste caso, um "servicebus.windows.net:sastoken") e o **"nome" a audiência** ao qual o token aplica-se (a entidade completa).

Depois de enviar o token SAS na ligação de remetente, o publicador tem de ler a resposta da ligação do recetor. A resposta é uma simple mensagem AMQP com uma propriedade de aplicação com o nome **"código de estado"** que pode conter os mesmos valores como um código de estado HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para operações de Service Bus

A tabela seguinte mostra os direitos de acesso necessários para várias operações de recursos do Service Bus.

| Operação | Afirmação necessária | Âmbito de afirmação |
| --- | --- | --- |
| **Espaço de nomes** | | |
| Configurar regra de autorização de um espaço de nomes |Gerir |Qualquer endereço de espaço de nomes |
| **Registo do serviço** | | |
| Enumerar privada políticas |Gerir |Qualquer endereço de espaço de nomes |
| Começar a escutar num espaço de nomes |Escutar |Qualquer endereço de espaço de nomes |
| Enviar mensagens para um serviço de escuta um espaço de nomes |Enviar |Qualquer endereço de espaço de nomes |
| **Fila** | | |
| Criar uma fila |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar uma fila |Gerir |Qualquer endereço de fila válida |
| Enumerar as filas |Gerir |$ Recursos/filas |
| Obter a descrição da fila |Gerir |Qualquer endereço de fila válida |
| Configurar a regra de autorização para uma fila |Gerir |Qualquer endereço de fila válida |
| Enviar em para a fila |Enviar |Qualquer endereço de fila válida |
| Receber mensagens a partir de uma fila |Escutar |Qualquer endereço de fila válida |
| Abandonar ou concluir mensagens depois de receber a mensagem no modo de bloqueio peek |Escutar |Qualquer endereço de fila válida |
| Diferir uma mensagem para obtenção posterior |Escutar |Qualquer endereço de fila válida |
| Uma mensagem de mensagens não entregues |Escutar |Qualquer endereço de fila válida |
| Obter o Estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válida |
| Definir o Estado associado uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válida |
| **Tópico** | | |
| Criar um tópico |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar um tópico |Gerir |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerir |$ Recursos/tópicos |
| Obter a descrição de tópico |Gerir |Qualquer endereço de tópico válido |
| Configurar a regra de autorização para um tópico |Gerir |Qualquer endereço de tópico válido |
| Enviar para o tópico |Enviar |Qualquer endereço de tópico válido |
| **Subscrição** | | |
| Criar uma subscrição |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar a subscrição |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar subscrições |Gerir |../myTopic/subscrições |
| Obter a descrição da subscrição |Gerir |../myTopic/subscriptions/mySubscription |
| Abandonar ou concluir mensagens depois de receber a mensagem no modo de bloqueio peek |Escutar |../myTopic/subscriptions/mySubscription |
| Diferir uma mensagem para obtenção posterior |Escutar |../myTopic/subscriptions/mySubscription |
| Uma mensagem de mensagens não entregues |Escutar |../myTopic/subscriptions/mySubscription |
| Obter o Estado associado a uma sessão de tópico |Escutar |../myTopic/subscriptions/mySubscription |
| Definir o Estado associado uma sessão de tópico |Escutar |../myTopic/subscriptions/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Eliminar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar regras |Gerir ou escutar |../myTopic/subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com