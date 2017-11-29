---
title: "Operações de API de REST dos serviços de armazenamento do Azure, incluindo a autenticação ao chamar | Microsoft Docs"
description: "Chamar operações de API de REST dos serviços de armazenamento do Azure, incluindo a autenticação"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/27/2017
ms.author: robinsh
ms.openlocfilehash: 73921f7fd4de65513f647db92b737a79f1043182
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="using-the-azure-storage-rest-api"></a>Utilizar o armazenamento do Azure REST API

Este artigo mostra-lhe como utilizar as APIs REST do Blob Storage serviço e como se autenticar a chamada para o serviço. Que é escrito do ponto de vista de alguém que conhece nada REST e nenhum ideia como efetuar uma chamada REST, mas é um programador. Vamos observar a documentação de referência para uma chamada REST e como convertê-lo numa chamada REST real – os campos aceda onde? Depois de aprender a configurar uma chamada REST, pode tirar partido estes conhecimentos para utilizar qualquer uma das outras APIs armazenamento serviço REST.

## <a name="prerequisites"></a>Pré-requisitos 

A aplicação apresenta uma lista de contentores no armazenamento de BLOBs para uma conta de armazenamento. Para experimentar o código neste artigo, terá dos seguintes itens: 

* Instalar [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho seguinte:
    - Desenvolvimento do Azure

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta do storage para fins gerais. Se não tiver quaisquer contas de armazenamento, pode criar um utilizando o [portal do Azure](https://portal.azure.com), [PowerShell](storage-quickstart-create-storage-account-powershell.md), ou [CLI do Azure](storage-quickstart-create-storage-account-cli.md).

* O exemplo neste artigo mostra como listar os contentores na conta de armazenamento. Para ver um resultado, adicione alguns contentores de armazenamento na conta de armazenamento de BLOBs, antes de começar.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação de exemplo é uma aplicação de consola escrita em c#.

Utilize [git](https://git-scm.com/) para transferir uma cópia da aplicação para o ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clones o repositório para a pasta de local git. Para abrir a solução do Visual Studio, procure a pasta storage-dotnet-rest-api-with-auth, abra-o e faça duplo clique no StorageRestApiAuth.sln. 

## <a name="why-do-i-need-to-know-rest"></a>Por que motivo é necessário saber REST?

Saber como utilizar REST é um skill útil. A equipa de produto do Azure com frequência versões novas funcionalidades. Muitas vezes, as novas funcionalidades estão acessíveis através da interface REST, mas ainda não foram anexadas através de **todos os** as bibliotecas de cliente de armazenamento ou a IU (por exemplo, o portal do Azure). Se pretender utilizar o mais recente e melhor sempre, learning REST é um requisito. Além disso, se pretender escrever a sua própria biblioteca de interagir com o Storage do Azure ou se pretender aceder ao armazenamento do Azure com uma linguagem de programação que não tenha uma biblioteca de cliente SDK ou armazenamento, pode utilizar a API REST.

## <a name="what-is-rest"></a>O que é REST?

Meios REST *transferência de estado representativo*. Para uma definição específica, consulte [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

Basicamente, REST é uma arquitetura que pode utilizar quando chamar APIs ou tornar disponíveis para ser chamado de APIs. É independente da que está a acontecer em ambos os lados e chama o outro software que está a ser utilizado ao enviar ou receber o resto. Pode escrever uma aplicação que é executado no Mac, Windows, Linux, um telemóvel Android ou tablet, iPhone, iPod ou web site e utilizar a API REST do mesmo em todas essas plataformas. Dados podem ser passados na e/ou enviados quando a API REST é chamada. A API REST não está interessado do que plataforma é denominado – que é importante é a informação transmitido no pedido e os dados fornecidos na resposta.

## <a name="heres-the-plan"></a>Eis o plano

O projeto de exemplo lista os contentores na conta de armazenamento. Assim que compreender a forma como as informações na documentação da REST API está correlacionada com o código real, outras chamadas REST são mais fáceis de descobrir. 

Se observar o [API de REST do serviço Blob](/rest/api/storageservices/fileservices/Blob-Service-REST-API), ver todas as operações que pode efetuar no armazenamento de Blobs. As bibliotecas de cliente de armazenamento são wrappers em torno as APIs REST – tornam mais fácil para si para aceder ao armazenamento sem utilizar as APIs REST diretamente. Mas, conforme indicado acima, por vezes, que pretende utilizar a API REST, em vez de uma biblioteca de clientes do storage.

## <a name="rest-api-reference-list-containers-api"></a>Referência da API REST: API de contentores de lista

Vamos ver a página de referência de API REST para o [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) operação para compreender a em que alguns dos campos provenientes no pedido e resposta na secção seguinte com o código.

**Método de pedido**: obter. Este verbo é o método HTTP que especifica como uma propriedade do objeto pedido. Outros valores para que este verbo incluem HEAD, PUT e DELETE, consoante a API está a chamar.

**URI de pedido**: https://myaccount.blob.core.windows.net/?comp=list isto é criado a partir do ponto final de conta do blob storage `http://myaccount.blob.core.windows.net` e a cadeia de recurso `/?comp=list`.

[Parâmetros URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): existem parâmetros de consulta adicionais que pode utilizar ao chamar ListContainers. Alguns destes parâmetros são *tempo limite* para a chamada de (em segundos) e *prefixo*, que é utilizada para filtragem.

Outro parâmetro útil é *maxresults:* se estiverem disponíveis mais contentores que este valor, o corpo da resposta irá conter um *NextMarker* elemento que indica o contentor seguinte para devolver no próximo pedido. Para utilizar esta funcionalidade, forneça o *NextMarker* valor como o *marcador* parâmetro no URI de quando efetua o pedido seguinte. Quando utilizar esta funcionalidade, é semelhante a paginação percorrer os resultados. 

Para utilizar parâmetros adicionais, acrescentá-los para a cadeia de recurso com o valor, tal como neste exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Cabeçalhos de pedido](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** esta secção lista os cabeçalhos de pedido necessários e opcionais. Três dos cabeçalhos de são necessárias: um *autorização* cabeçalho, *x-ms-data* (contém a hora UTC para o pedido), e *x-ms-version* (Especifica a versão do RESTANTE API a utilizar). Incluindo *x-ms-client-request-id* nos cabeçalhos é opcional – pode definir o valor para este campo para qualquer coisa; é escrito nos registos de análise de armazenamento quando o registo está ativado.

[Corpo do pedido](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** não há nenhum corpo do pedido para ListContainers. Corpo do pedido é utilizado em todas as operações PUT ao carregar os blobs, bem como SetContainerAccessPolicy, que lhe permite enviar uma lista de XML de políticas de acesso armazenada a aplicar. Políticas de acesso armazenada são debatidas no artigo [através de acesso partilhado assinaturas (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Código de estado de resposta](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** Tells de qualquer precisa de saber os códigos de estado. Neste exemplo, um código de estado HTTP de 200 está ok. Para obter uma lista completa dos códigos de estado HTTP, consulte [definições de código de estado](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Para ver os códigos de erro específicos para as APIs REST do Storage, consulte [códigos de erro comuns API de REST](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** incluem *tipo de conteúdo*; *x-ms-request-id* (o id do pedido é transmitida, se aplicável); *x-ms-version* (indica a versão do serviço Blob utilizado) e o *data* (UTC, indica o tempo que o pedido foi efetuado).

[Corpo da resposta](/rest/api/storageservices/fileservices/List-Containers2#response-body): Este campo é uma estrutura XML, fornecendo os dados solicitados. Neste exemplo, a resposta é uma lista de contentores e as respetivas propriedades.

## <a name="creating-the-rest-request"></a>Criar o pedido REST

Algumas notas antes de iniciar – para segurança quando em execução na produção, utilize sempre HTTPS em vez de HTTP. Para efeitos neste exercício, deve utilizar HTTP, para que possa visualizar os dados de pedido e resposta. Para ver as informações de pedido e resposta de chamadas REST real, pode transferir [Fiddler](http://www.telerik.com/fiddler) ou uma aplicação semelhante. A solução do Visual Studio, o nome da conta de armazenamento e a chave são codificado na classe e o método ListContainersAsyncREST transmite o nome da conta de armazenamento e a chave da conta de armazenamento para os métodos que são utilizados para criar os vários componentes do pedido REST . Uma aplicação do mundo real, o nome da conta de armazenamento e a chave deverá residirem num ficheiro de configuração, as variáveis de ambiente, ou obtidos a partir de um cofre de chaves do Azure.

No nosso projeto de exemplo, o código para criar o cabeçalho de autorização está a ser uma classe separada, com a ideia que foi demorar a classe toda e adicioná-lo à sua própria solução e utilizá-lo "tal como"está. O código de cabeçalho de autorização funciona para a maioria das chamadas da REST API para o armazenamento do Azure.

Para criar o pedido, o que é um objeto de HttpRequestMessage, aceda à ListContainersAsyncREST em Program.cs. Os passos para criar o pedido são: 

* Crie o URI para ser utilizada para chamar o serviço. 
* Crie o objeto de HttpRequestMessage e defina o payload. O payload é nulo para ListContainersAsyncREST porque que não está a transmitir nada no.
* Adicione os cabeçalhos de pedido x-ms-data e x-ms-version.
* Obtenha o cabeçalho authorization e adicioná-lo.

Algumas informações básicas que precisa de: 

*  Para ListContainers, o **método** é `GET`. Este valor é definido ao instanciar o pedido. 
*  O **recursos** é a parte da consulta de URI que indica que está a ser chamada API, pelo que é o valor `/?comp=list`. Conforme indicado anteriormente, o recurso está na página de documentação de referência que mostra as informações sobre o [ListContainers API](/rest/api/storageservices/fileservices/List-Containers2).
*  O URI é construído através da criação do ponto final de serviço Blob para essa conta de armazenamento e concatenar o recurso. O valor para **URI do pedido** termina a ser `http://contosorest.blob.core.windows.net/?comp=list`.
*  Para ListContainers, **requestBody** é nula e não existem nenhum extra **cabeçalhos**.

APIs de diferentes podem ter outros parâmetros para passar como *ifMatch*. Um exemplo de onde pode utilizar ifMatch é ao chamar PutBlob. Nesse caso, defina ifMatch uma ETag e atualiza apenas o blob se eTag que fornecer corresponde ao eTag atual no blob. Se alguém tiver atualizado o blob desde a obter a eTag, as suas alterações não serão substituídas. 

Em primeiro lugar, defina o `uri` e `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, instanciar o pedido, definir o método para `GET` e fornecer o URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de pedido x-ms-data e x-ms-version. Este local com o código é também onde adiciona quaisquer cabeçalhos de pedido adicionais necessários para a chamada. Neste exemplo, não existem nenhum cabeçalhos adicionais. Um exemplo de uma API que passa nos cabeçalhos adicionais é SetContainerACL. Para o Blob storage, adiciona um cabeçalho chamado "x-ms-BLOBs--acesso público" e o valor para o nível de acesso.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-04-17");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Chamar o método que cria o cabeçalho authorization e adicioná-lo para os cabeçalhos do pedido. Verá como criar o cabeçalho de autorização mais tarde no artigo. O nome do método é GetAuthorizationHeader, que pode ver este fragmento de código:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Neste momento, `httpRequestMessage` contém o pedido REST completo com os cabeçalhos de autorização. 

## <a name="call-the-rest-api-with-the-request"></a>Chamada da API de REST com o pedido

Agora que tem o pedido, pode chamar SendAsync enviar o pedido REST. SendAsync chama a API e recebe a resposta de volta. Examine a resposta StatusCode (200 é OK), em seguida, analisar a resposta. Neste caso, obter uma lista XML de contentores. Vamos ver o código para chamar o método GetRESTRequest para criar o pedido, executar o pedido e, em seguida, examine a resposta para a lista de contentores.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Se executar um sniffer de rede, tais como [Fiddler](https://www.telerik.com/fiddler) quando efetuar a chamada para SendAsync, pode ver as informações de pedido e resposta. Vamos ver. O nome da conta de armazenamento é *contosorest*.

**Pedido:**

```
GET /?comp=list HTTP/1.1
```

**Cabeçalhos de pedido:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Cabeçalhos de resposta e de código de estado devolvido após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 04-17
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corpo da resposta (XML):** para ListContainers mostra a lista de contentores e as respetivas propriedades.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Agora que sabe como criar o pedido, chamar o serviço e analisar os resultados, vamos ver como criar o cabeçalho de autorização. Criar essa cabeçalho é complexa, mas boas notícias que assim que tiver o código de funcionar, funciona para todas as APIs de REST do serviço de armazenamento.

## <a name="creating-the-authorization-header"></a>Criar o cabeçalho de autorização

Há um artigo que explica concecionais (nenhum código) como efetuar [autenticação para os serviços de armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Vamos distill esse artigo para baixo para exatamente é necessário e mostrar o código.

Em primeiro lugar, utilize uma autenticação de chave partilhada. O formato de cabeçalho de autorização tem o seguinte aspeto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

O campo de assinatura é uma base em Hash Message Authentication Code (HMAC) criado no pedido e calculado utilizando o algoritmo SHA256, em seguida, codificado com codificação Base64. Obteve que? (Bloquear no mesmo, ainda não o mesmo teve o word *canonizado* ainda.)

Este fragmento de código mostra o formato da cadeia de assinatura de chave partilhada:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

A maioria destes campos raramente é utilizada. Para o Blob storage, especifique VERBO, md5, o comprimento do conteúdo, Canonizado cabeçalhos e Canonizado recursos. Pode deixar as outras em branco (mas colocar no `\n` para que confie que estejam em branco).

Quais são CanonicalizedHeaders e CanonicalizedResource? Boa pergunta. Na verdade, o que faz canonizado média? Microsoft Word, mesmo não reconhece-la como uma palavra. Eis o que [Wikipedia indica sobre a colocação em forma canónica](http://en.wikipedia.org/wiki/Canonicalization): *no ciência de computador, colocação em forma canónica (por vezes, uniformização ou normalização) é um processo de conversão de dados que tenha mais do que uma possível representação num formulário "padrão", "normal" ou canónico.* Enunciar no normal, isto significa que a lista de itens (tais como cabeçalhos no caso de cabeçalhos Canonizado) e padronizá-los para um formato necessário. Basicamente, Microsoft decidir um formato e tem de corresponder ao mesmo.

Vamos começar a utilizar esses dois campos canonicalized, pois são necessários para criar o cabeçalho de autorização.

**Canonizado cabeçalhos**

Para criar este valor, obter os cabeçalhos que comece com "x - ms-" e classificá-los, em seguida, formatou-los para uma cadeia de `[key:value\n]` instâncias, concatenadas numa cadeia. Neste exemplo, os cabeçalhos canonicalized este aspeto: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-04-17\n
```

Eis o código utilizado para criar essa saída:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Recurso canonizado**

Esta parte da cadeia de assinatura representa a conta de armazenamento visada por pedido. Lembre-se de que o URI do pedido é `<http://contosorest.blob.core.windows.net/?comp=list>`, com o nome da conta real (`contosorest` neste caso). Neste exemplo, este é devolvido:

```
/contosorest/\ncomp:list
```

Se tiver parâmetros de consulta, isto inclui os bem. Eis o código, o qual também processa parâmetros de consulta adicionais e parâmetros de consulta com vários valores. Lembre-se de que está a criar este código para funciona para todas as APIs REST, pelo que pretende incluir todas as possibilidades, mesmo que o método ListContainers não necessita de todos eles.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Agora que são definidas as cadeias de canonicalized, vamos ver como criar o cabeçalho de autorização em si. Começar através da criação de uma cadeia de assinatura de mensagem no formato de StringToSign apresentada anteriormente neste artigo. Este conceito é mais fácil para explicam a utilização de comentários no código, por isso, aqui é, o método final que devolve o cabeçalho de autorização:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Quando executar este código, o MessageSignature resultante tem este aspeto:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-04-17\n/contosorest/\ncomp:list
```

Segue-se o valor final para AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O AuthorizationHeader é o último cabeçalho colocado nos cabeçalhos do pedido antes de publicar a resposta.

Que inclui tudo o que precisa de saber, juntamente com o código, para a juntar-se de uma classe que pode utilizar para criar um pedido para ser utilizado para chamar as APIs de REST de serviços de armazenamento.

## <a name="how-about-another-example"></a>Como vai outro exemplo? 

Vamos ver como alterar o código para chamar ListBlobs contentor *contentor-1*. Isto é quase idêntico o código para listar os contentores, as diferenças apenas a ser o URI e como analisar a resposta. 

Se observar a documentação de referência para [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), encontrará que é o método *obter* e o RequestURI é:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Na ListContainersAsyncREST, altere o código que define o URI para a API para ListBlobs. O nome do contentor é **contentor-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Em seguida, onde para processar a resposta, altere o código para procurar blobs em vez de contentores.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando executar este exemplo, pode obter resultados como o seguinte:

**Cabeçalhos canonizados:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-04-17\n
```

**Canonizado recursos:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-04-17\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Os seguintes valores são de [Fiddler](http://www.telerik.com/fiddler):

**Pedido:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Cabeçalhos de pedido:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-04-17
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Cabeçalhos de resposta e de código de estado devolvido após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-04-17
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corpo da resposta (XML):** resposta XML este mostra a lista de blobs e as respetivas propriedades. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Resumo

Neste artigo, aprendeu a efetuar um pedido para o API REST para obter uma lista de contentores ou uma lista de blobs num contentor de armazenamento de Blobs. Também aprendeu como criar a assinatura de autorização para a chamada de REST API, como utilizá-la no pedido REST e como examinar a resposta.

## <a name="next-steps"></a>Passos seguintes

* [API de REST do serviço blob](/rest/api/storageservices/blob-service-rest-api)
* [API de REST do serviço de ficheiro](/rest/api/storageservices/file-service-rest-api)
* [API de REST do serviço fila](/rest/api/storageservices/queue-service-rest-api)