---
title: "Expressões de política de gestão de API do Azure | Microsoft Docs"
description: "Saiba mais sobre as expressões de política na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 96455dcdcf2eb90c836675c73c83c0320524fdac
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="api-management-policy-expressions"></a>Expressões de política de gestão de API
Sintaxe de expressões de política é c# 6.0. Cada expressão tem acesso ao implicitamente fornecido [contexto](api-management-policy-expressions.md#ContextVariables) variável e um permitido [subconjunto](api-management-policy-expressions.md#CLRTypes) dos tipos de .NET Framework.  
  
> [!NOTE]
>  Para obter mais informações sobre as expressões de política, consulte o [expressões de política](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/) vídeo.  
>   
>  Para demonstrações de configuração de políticas utilizando expressões de política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Este vídeo contém as demonstrações de expressão de política seguinte.  
>   
>  -   10:30 - Consulte como aplicar a política ao nível da API para fornecer informações de contexto para o serviço de back-end utilizando a [defina o parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e [cabeçalho de HTTP definir](api-management-transformation-policies.md#SetHTTPheader) políticas. Às 12:10 há uma demonstração da chamada de uma operação no portal do programador, onde poderá ver estas políticas no trabalho.  
> -   13:50 - ver como utilizar o [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) política para pré-autorizar o acesso às operações de baseadas em afirmações de token. Reencaminhe rápido para 15:00 para ver as políticas configuradas no editor de política e, em seguida, para 18:50 para uma demonstração de chamar uma operação a partir do portal do programador com e sem o token de autorização necessário.  
> -   21:00 - ver como utilizar um [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) rastreio para ver como são avaliadas as políticas e os resultados das avaliações.  
> -   25:25 - ver como utilizar expressões de política com o [aproveitar a cache](api-management-caching-policies.md#GetFromCache) e [arquivo para colocar em cache](api-management-caching-policies.md#StoreToCache) políticas para configurar a resposta da API Management colocação em cache duração que corresponda a resposta a colocação em cache de back-end do serviço conforme especificado pelo serviço de cópia `Cache-Control` diretiva.  
> -   34:30 - Consulte como efetuar a filtragem de conteúdo através da remoção de elementos de dados da resposta recebeu do serviço de back-end utilizando a [controlar o fluxo](api-management-advanced-policies.md#choose) e [definir corpo](api-management-transformation-policies.md#SetBody) políticas. Iniciar o 31:50 ver uma descrição geral do [a escuro Sky previsão API](https://developer.forecast.io/) utilizado para esta demonstração.  
> -   Para transferir as declarações de política utilizadas neste vídeo, consulte o [api management-samples/políticas](https://github.com/Azure/api-management-samples/tree/master/policies) repositório do github.  
  
  
##  <a name="Syntax"></a>Sintaxe  
 As expressões de instrução única estão incluídas no `@(expression)`, onde `expression` uma declaração de expressão do c# corretamente formado.  
  
 Expressões com múltiplas instruções estão incluídas no `@{expression}`. Todos os caminhos de código dentro de expressões com múltiplas instruções tem de terminar com um `return` instrução.  
  
##  <a name="PolicyExpressionsExamples"></a>Exemplos  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a>Utilização  
 As expressões podem ser utilizadas como valores de atributo ou valores de texto em qualquer um da API Management [políticas](api-management-policies.md), a menos que a referência de política especifica caso contrário.  
  
> [!IMPORTANT]
>  Tenha em atenção que ao utilizar expressões de política, há apenas limitada verificação das expressões de política quando a política estiver definida. Uma vez que as expressões são executadas durante a execução no pipeline de entrada ou saído pelo gateway, quaisquer exceções de tempo de execução geradas pelas expressões de política resultará num erro de tempo de execução na chamada à API.  
  
##  <a name="CLRTypes"></a>Tipos de .NET framework permitidos em expressões de política  
 A tabela seguinte lista os tipos de .NET Framework e os respetivos membros que são permitidos em expressões de política.  
  
|Tipo CLR|Métodos suportados|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JArray|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JConstructor|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JContainer|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JObject|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JProperty|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JRaw|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JToken|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JTokenType|Todos os métodos são suportados|  
|Newtonsoft.Json.Linq.JValue|Todos os métodos são suportados|  
|System.Collections.Generic.IReadOnlyCollection < T\>|Todos|  
|System.Collections.Generic.IReadOnlyDictionary < TKey, TValue >|Todos|  
|System.Collections.Generic.ISet < TKey, TValue >|Todos|  
|System.Collections.Generic.KeyValuePair < TKey, TValue >|Chave, o valor|  
|System.Collections.Generic.List < TKey, TValue >|Todos|  
|System.Collections.Generic.Queue < TKey, TValue >|Todos|  
|System.Collections.Generic.Stack < TKey, TValue >|Todos|  
|System.Convert|Todos|  
|DateTime|Todos|  
|System.DateTimeKind|UTC|  
|System.DateTimeOffset|Todos|  
|System|Todos|  
|System.Double|Todos|  
|GUID|Todos|  
|System.IEnumerable < T\>|Todos|  
|System.IEnumerator < T\>|Todos|  
|System.Int16|Todos|  
|System. Int32|Todos|  
|System. Int64|Todos|  
|System.Linq.Enumerable < T\>|Todos os métodos são suportados|  
|Math|Todos|  
|System.MidpointRounding|Todos|  
|System. Nullable < T\>|Todos|  
|System.Random|Todos|  
|System.SByte|Todos|  
|Cryptography. HMACSHA384|Todos|  
|Cryptography. HMACSHA512|Todos|  
|System.Security.Cryptography.HashAlgorithm|Todos|  
|System.Security.Cryptography.HMAC|Todos|  
|System.Security.Cryptography.HMACMD5|Todos|  
|System.Security.Cryptography.HMACSHA1|Todos|  
|System.Security.Cryptography.HMACSHA256|Todos|  
|System.Security.Cryptography.KeyedHashAlgorithm|Todos|  
|System.Security.Cryptography.MD5|Todos|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Todos|  
|System.Security.Cryptography.SHA1|Todos|  
|System.Security.Cryptography.SHA1Managed|Todos|  
|System.Security.Cryptography.SHA256|Todos|  
|System.Security.Cryptography.SHA256Managed|Todos|  
|System.Security.Cryptography.SHA384|Todos|  
|System.Security.Cryptography.SHA384Managed|Todos|  
|System.Security.Cryptography.SHA512|Todos|  
|System.Security.Cryptography.SHA512Managed|Todos|  
|System.Single|Todos|  
|String|Todos|  
|System.StringSplitOptions|Todos|  
|System.Text.Encoding|Todos|  
|System.Text.RegularExpressions.Capture|Valor de índice, comprimento,|  
|System.Text.RegularExpressions.CaptureCollection|Contagem de Item|  
|System.Text.RegularExpressions.Group|Capturas de êxito|  
|System.Text.RegularExpressions.GroupCollection|Contagem de Item|  
|System.Text.RegularExpressions.Match|Resultado vazio, grupos,|  
|RegularExpressions|. ctor, IsMatch, corresponde a, corresponde, substituir|  
|System.Text.RegularExpressions.RegexOptions|Compilado, IgnoreCase, IgnorePatternWhitespace, múltiplas linhas, None, RightToLeft, Singleline|  
|System.TimeSpan|Todos|  
|System.Tuple|Todos|  
|System.UInt16|Todos|  
|System.UInt32|Todos|  
|UInt64|Todos|  
|URI|Todos|  
|System.Xml.Linq.Extensions|Todos os métodos são suportados|  
|System.Xml.Linq.XAttribute|Todos os métodos são suportados|  
|System.Xml.Linq.XCData|Todos os métodos são suportados|  
|System.Xml.Linq.XComment|Todos os métodos são suportados|  
|System.Xml.Linq.XContainer|Todos os métodos são suportados|  
|System.Xml.Linq.XDeclaration|Todos os métodos são suportados|  
|System.Xml.Linq.XDocument|Todos os métodos são suportados|  
|System.Xml.Linq.XDocumentType|Todos os métodos são suportados|  
|System.Xml.Linq.XElement|Todos os métodos são suportados|  
|System.Xml.Linq.XName|Todos os métodos são suportados|  
|System.Xml.Linq.XNamespace|Todos os métodos são suportados|  
|System.Xml.Linq.XNode|Todos os métodos são suportados|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Todos os métodos são suportados|  
|System.Xml.Linq.XNodeEqualityComparer|Todos os métodos são suportados|  
|System.Xml.Linq.XObject|Todos os métodos são suportados|  
|System.Xml.Linq.XProcessingInstruction|Todos os métodos são suportados|  
|System.Xml.Linq.XText|Todos os métodos são suportados|  
|System.Xml.XmlNodeType|Todos|  
  
##  <a name="ContextVariables"></a>Variável de contexto  
 Uma variável com o nome `context` está implicitamente disponível em cada política [expressão](api-management-policy-expressions.md#Syntax). Os seus membros fornecem informações relevantes para o `\request`. Todas as `context` membros são só de leitura.  
  
|Variável de contexto|Permitidos métodos, propriedades e valores de parâmetros|  
|----------------------|-------------------------------------------------------|  
|Contexto|API: IApi<br /><br /> Implementação<br /><br /> LastError<br /><br /> Operação<br /><br /> Produto<br /><br /> Pedir<br /><br /> RequestId: Guid<br /><br /> Resposta<br /><br /> Subscrição<br /><br /> Rastreio: bool<br /><br /> Utilizador<br /><br /> Variáveis: IReadOnlyDictionary < cadeia, objecto ><br /><br /> void Trace(message: string)|  
|contexto. API|ID: cadeia<br /><br /> Nome: cadeia<br /><br /> Caminho: cadeia<br /><br /> ServiceUrl: IUrl|  
|contexto. Implementação|Região: cadeia<br /><br /> ServiceName: cadeia<br /><br /> Certificados: IReadOnlyDictionary < cadeia, X509Certificate2 >|  
|contexto. LastError|Origem: cadeia<br /><br /> Razão: cadeia<br /><br /> Mensagem: cadeia<br /><br /> Âmbito: cadeia<br /><br /> Secção: cadeia<br /><br /> Caminho: cadeia<br /><br /> PolicyId: cadeia<br /><br /> Para obter mais informações sobre o contexto. LastError, consulte [processamento de erros](api-management-error-handling-policies.md).|  
|contexto. Operação|ID: cadeia<br /><br /> Método: cadeia<br /><br /> Nome: cadeia<br /><br /> UrlTemplate: cadeia|  
|contexto. Produto|APIs: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable < IGroup\><br /><br /> ID: cadeia<br /><br /> Nome: cadeia<br /><br /> Estado: enumeração ProductState {NotPublished, publicada}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|contexto. Pedido|Corpo: IMessageBody<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Cabeçalhos: IReadOnlyDictionary < string, string [] ><br /><br /> IpAddress: cadeia<br /><br /> MatchedParameters: IReadOnlyDictionary < cadeia, cadeia ><br /><br /> Método: cadeia<br /><br /> OriginalUrl:IUrl<br /><br /> URL: IUrl|  
|contexto de cadeia. Request.Headers.GetValueOrDefault (headerName: cadeia, defaultValue: cadeia)|headerName: cadeia<br /><br /> defaultValue: cadeia<br /><br /> Os valores de cabeçalho de pedido de separados por vírgulas de Devolve ou `defaultValue` se não for encontrado o cabeçalho.|  
|contexto. Resposta|Corpo: IMessageBody<br /><br /> Cabeçalhos: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: cadeia|  
|contexto de cadeia. Response.Headers.GetValueOrDefault (headerName: cadeia, defaultValue: cadeia)|headerName: cadeia<br /><br /> defaultValue: cadeia<br /><br /> Devolve os valores de cabeçalho de resposta de separados por vírgulas ou `defaultValue` se não for encontrado o cabeçalho.|  
|contexto. Subscrição|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: cadeia<br /><br /> Chave: cadeia<br /><br /> Nome: cadeia<br /><br /> PrimaryKey: cadeia<br /><br /> SecondaryKey: cadeia<br /><br /> StartDate: DateTime?|  
|contexto. Utilizador|E-mail: cadeia<br /><br /> Nome próprio: cadeia<br /><br /> Grupos: IEnumerable < IGroup\><br /><br /> ID: cadeia<br /><br /> Identidades: IEnumerable < IUserIdentity\><br /><br /> Apelido: cadeia<br /><br /> Nota: cadeia<br /><br /> RegistrationDate: DateTime|  
|IApi|ID: cadeia<br /><br /> Nome: cadeia<br /><br /> Caminho: cadeia<br /><br /> Protocolos: IEnumerable < cadeia\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|ID: cadeia<br /><br /> Nome: cadeia|  
|IMessageBody|Como < T\>(preserveContent: bool = false): onde t: Temporary cadeia, JObject JToken, JArray, XNode, XElement, XDocument<br /><br /> O `context.Request.Body.As<T>` e `context.Response.Body.As<T>` métodos utilizados para ler um pedido e resposta corpos de mensagem num tipo especificado `T`. Por predefinição, o método utiliza o fluxo do corpo de mensagem original e reneders-la disponível depois de devolve. Para evitar que fazendo com que o método funcionar numa cópia do fluxo do corpo, defina o `preserveContent` parâmetro `true`. Aceda [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|  
|IUrl|Anfitrião: cadeia<br /><br /> Caminho: cadeia<br /><br /> Porta: int<br /><br /> Consulta: IReadOnlyDictionary < string, string [] ><br /><br /> QueryString: cadeia<br /><br /> Esquema: cadeia|  
|IUserIdentity|ID: cadeia<br /><br /> Fornecedor: cadeia|  
|ISubscriptionKeyParameterNames|Cabeçalho: cadeia<br /><br /> Consulta: cadeia|  
|cadeia IUrl.Query.GetValueOrDefault (queryParameterName: cadeia, defaultValue: cadeia)|queryParameterName: cadeia<br /><br /> defaultValue: cadeia<br /><br /> Os valores de parâmetros de consulta valores separados por vírgulas de Devolve ou `defaultValue` se o parâmetro não for encontrado.|  
|Contexto de T. Variables.GetValueOrDefault < T\>(variableName: cadeia, defaultValue: T)|variableName: cadeia<br /><br /> defaultValue: T<br /><br /> Devolve o valor da variável convertido no tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Este método emite uma exceção se o tipo especificado não corresponde ao tipo real da variável devolvido.|  
|BasicAuthCredentials AsBasic(input: this string)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contém um valor de cabeçalho do pedido de autorização de autenticação básica de HTTP válido, o método devolve um objeto do tipo `BasicAuthCredentials`; caso contrário, o método devolve um valor nulo.|  
|bool TryParseBasic (entrada: Esta cadeia, o resultado: saída BasicAuthCredentials)|entrada: cadeia<br /><br /> resultado: saída BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contém um valor de cabeçalho do pedido de autorização de autenticação básica de HTTP válido, o método devolve `true` e o parâmetro de resultado contém um valor de tipo `BasicAuthCredentials`; caso contrário, devolve o método `false`.|  
|BasicAuthCredentials|Palavra-passe: cadeia<br /><br /> UserId: cadeia|  
|Jwt AsJwt(input: this string)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contém um valor de token válido JWT, o método devolve um objeto do tipo `Jwt`; caso contrário, devolve o método `null`.|  
|bool TryParseJwt (entrada: Esta cadeia, o resultado: saída Jwt)|entrada: cadeia<br /><br /> resultado: saída Jwt<br /><br /> Se o parâmetro de entrada contém um valor de token válido JWT, o método devolve `true` e o parâmetro de resultado contém um valor de tipo `Jwt`; caso contrário, devolve o método `false`.|  
|Jwt|Algoritmo: cadeia<br /><br /> Audiência: IEnumerable < cadeia\><br /><br /> Afirmações: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: cadeia<br /><br /> Emissor: cadeia<br /><br /> NotBefore: DateTime?<br /><br /> Requerente: cadeia<br /><br /> Tipo: cadeia|  
|cadeia Jwt.Claims.GetValueOrDefault (claimName: cadeia, defaultValue: cadeia)|claimName: cadeia<br /><br /> defaultValue: cadeia<br /><br /> Devolve os valores de afirmação separados por vírgulas ou `defaultValue` se não for encontrado o cabeçalho.|
|byte [] encriptar (entrada: esta byte [], alg: cadeia, a chave: byte [], iv:byte[])|entrada - texto simples para ser encriptada<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto não encriptado.|
|byte [] encriptar (entrada: esta byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto simples para ser encriptada<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto não encriptado.|
|byte [] encriptar (entrada: esta byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, chave: byte [], iv:byte[])|entrada - texto simples para ser encriptada<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto não encriptado.|
|byte [] desencriptar (entrada: esta byte [], alg: cadeia, a chave: byte [], iv:byte[])|entrada - cyphertext a desencriptar<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto simples.|
|byte [] desencriptar (entrada: esta byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - cyphertext a desencriptar<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto simples.|
|byte [] desencriptar (entrada: esta byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, chave: byte [], iv:byte[])|entrada cyphertext - entrada - a desencriptar<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto simples.|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  
