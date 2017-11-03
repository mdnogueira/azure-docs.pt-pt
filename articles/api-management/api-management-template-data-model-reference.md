---
title: "Referência de modelo de dados do modelo de API Management do Azure | Microsoft Docs"
description: "Saiba mais sobre as entidade e tipo representações para itens comuns utilizados em modelos de dados para os modelos de portal do programador na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 72936a4d38f809934ddea74e5ae4a6029450a97c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência de modelo de dados do modelo de API Management do Azure
Este tópico descreve as representações de entidade e tipo para itens comuns utilizados em modelos de dados para os modelos de portal do programador na API Management do Azure.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [Resumo de API](#APISummary)  
-   [Aplicação](#Application)  
-   [Anexo](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Comentário](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Pedido de HTTP](#HTTPRequest)  
-   [Resposta de HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item de menu de operação](#MenuItem)  
-   [Paginação](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Produto](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Subscrição](#Subscription)  
-   [Resumo de subscrição](#SubscriptionSummary)  
-   [Informações da conta de utilizador](#UserAccountInfo)  
-   [Início de sessão de utilizador](#UseSignIn)  
-   [Inscrição do utilizador](#UserSignUp)  
  
##  <a name="API"></a>API  
 O `API` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|ID|Cadeia|Identificador de recurso. Identifica exclusivamente a API numa instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato de `apis/{id}` onde `{id}` é um identificador de API. Esta propriedade é só de leitura.|  
|nome|Cadeia|Nome da API. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|descrição|Cadeia|Descrição da API. Não deve estar vazio. Pode incluir a formatação tags HTML. Comprimento máximo é de 1000 carateres.|  
|serviceUrl|Cadeia|URL absoluto do serviço de back-end implementar esta API.|  
|Caminho|Cadeia|URL relativo identificar exclusivamente esta API e todos os respetivos caminhos recursos a instância do serviço de API Management. É acrescentado a para o API base URL de ponto final especificado durante a criação de instância de serviço para formar um URL público para esta API.|  
|protocolos|matriz de número|Descreve em protocolos de quais é possível invocar as operações desta API. Valores permitidos são `1 - http` e `2 - https`, ou ambos.|  
|authenticationSettings|[Definições de autenticação de servidor de autorização](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Coleção de definições de autenticação incluídos nesta API.|  
|subscriptionKeyParameterNames|objeto|Propriedade opcional que pode ser utilizada para especificar nomes personalizados para os parâmetros de consulta e/ou de cabeçalho que contém a chave de subscrição. Quando esta propriedade está presente tem de conter, pelo menos, uma das duas propriedades seguintes.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Resumo de API  
 O `API summary` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|ID|Cadeia|Identificador de recurso. Identifica exclusivamente a API numa instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato de `apis/{id}` onde `{id}` é um identificador de API. Esta propriedade é só de leitura.|  
|nome|Cadeia|Nome da API. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|descrição|Cadeia|Descrição da API. Não deve estar vazio. Pode incluir a formatação tags HTML. Comprimento máximo é de 1000 carateres.|  
  
##  <a name="Application"></a>Aplicação  
 O `application` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|O identificador exclusivo da aplicação.|  
|Título|Cadeia|O título da aplicação.|  
|Descrição|Cadeia|A descrição da aplicação.|  
|Url|URI|O URI da aplicação.|  
|Versão|Cadeia|Informações de versão para a aplicação.|  
|Requisitos|Cadeia|Uma descrição dos requisitos para a aplicação.|  
|Estado|Número|O estado atual da aplicação.<br /><br /> -0 - registado<br /><br /> -1 - submetido<br /><br /> -2 - publicado<br /><br /> -3 - rejeitados<br /><br /> -4 - não publicado|  
|RegistrationDate|DateTime|A data e hora que a aplicação foi registada.|  
|CategoryId|Número|A categoria da aplicação (financeiros, entretenimento, etc.)|  
|DeveloperId|Cadeia|O identificador exclusivo do programador que submetido a aplicação.|  
|Anexos|Coleção de [anexo](#Attachment) entidades.|Os anexos para a aplicação, tais como capturas de ecrã ou ícones.|  
|Ícone|[Anexo](#Attachment)|O ícone da para a aplicação.|  
  
##  <a name="Attachment"></a>Anexo  
 O `attachment` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|UniqueId|Cadeia|O identificador exclusivo para o anexo.|  
|Url|Cadeia|O URL do recurso.|  
|Tipo|Cadeia|O tipo de anexo.|  
|ContentType|Cadeia|O tipo de suporte de dados do anexo.|  
  
##  <a name="Sample"></a>Exemplo de código  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Título|Cadeia|O nome da operação.|  
|fragmento|Cadeia|Esta propriedade foi preterida e não deve ser utilizada.|  
|Pincel|Cadeia|Que modelo a utilizar quando se apresenta o código de exemplo de cores da sintaxe de código. Valores permitidos são `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, e `csharp`.|  
|modelo|Cadeia|O nome deste modelo de exemplo de código.|  
|Corpo|Cadeia|Um marcador de posição para a parte de exemplo de código de fragmento.|  
|Método|Cadeia|O método HTTP da operação.|  
|Esquema|Cadeia|O protocolo a utilizar para o pedido de operação.|  
|Caminho|Cadeia|O caminho da operação.|  
|consulta|Cadeia|Exemplo de cadeia de consulta com parâmetros definidos.|  
|anfitrião|Cadeia|O URL do gateway de serviço de API Management para a API que contém esta operação.|  
|Cabeçalhos|Coleção de [cabeçalho](#Header) entidades.|Cabeçalhos para esta operação.|  
|parâmetros|Coleção de [parâmetro](#Parameter) entidades.|Parâmetros que são definidos para esta operação.|  
  
##  <a name="Comment"></a>Comentário  
 O `API` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Número|O id do comentário.|  
|CommentText|Cadeia|O corpo do comentário. Pode incluir HTML.|  
|DeveloperCompany|Cadeia|O nome da empresa do programador.|  
|PostedOn|DateTime|A data e hora que o comentário foi publicado.|  
  
##  <a name="Issue"></a>Problema  
 O `issue` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|O identificador exclusivo para o problema.|  
|ApiID|Cadeia|O id para a API para o qual foi comunicado este problema.|  
|Título|Cadeia|Título do problema.|  
|Descrição|Cadeia|Descrição do problema.|  
|SubscriptionDeveloperName|Cadeia|Nome próprio do programador que forneceram o problema.|  
|IssueState|Cadeia|O estado atual do problema. Os valores possíveis são Proposed, aberto, fechado.|  
|ReportedOn|DateTime|A data e hora que foi comunicado o problema.|  
|Comentários|Coleção de [comentário](#Comment) entidades.|Comentários sobre este problema.|  
|Anexos|Coleção de [anexo](api-management-template-data-model-reference.md#Attachment) entidades.|Os anexos para o problema.|  
|Serviços|Coleção de [API](#API) entidades.|As APIs subscrito pelo utilizador que registada o problema.|  
  
##  <a name="Filtering"></a>Filtragem  
 O `filtering` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Padrão|Cadeia|O termo de pesquisa atual; ou `null` se não houver nenhuma termo de pesquisa.|  
|Marcador de posição|Cadeia|O texto a apresentar na caixa de pesquisa quando não existe nenhum termo de pesquisa especificado.|  
  
##  <a name="Header"></a>Cabeçalho  
 Esta secção descreve o `parameter` representação.  
  
|Propriedade|Descrição|Tipo|  
|--------------|-----------------|----------|  
|nome|Cadeia|Nome do parâmetro.|  
|descrição|Cadeia|Descrição de parâmetro.|  
|valor|Cadeia|Valor do cabeçalho.|  
|TypeName|Cadeia|Tipo de dados do valor do cabeçalho.|  
|Opções|Cadeia|Opções.|  
|Necessário|Valor booleano|Indica se o cabeçalho é necessário.|  
|Só de leitura|Valor booleano|Indica se o cabeçalho é só de leitura.|  
  
##  <a name="HTTPRequest"></a>Pedido de HTTP  
 Esta secção descreve o `request` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|descrição|Cadeia|Descrição de pedido de operação.|  
|Cabeçalhos|matriz de [cabeçalho](#Header) entidades.|Cabeçalhos de pedido.|  
|parâmetros|matriz de [parâmetro](#Parameter)|Coleção de parâmetros de operação de pedido.|  
|representações|matriz de [representação](#Representation)|Coleção de representações de pedido de operação.|  
  
##  <a name="HTTPResponse"></a>Resposta de HTTP  
 Esta secção descreve o `response` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|statusCode|Número inteiro positivo|Código de estado de resposta de operação.|  
|descrição|Cadeia|Descrição de resposta da operação.|  
|representações|matriz de [representação](#Representation)|Coleção de representações de resposta da operação.|  
  
##  <a name="Operation"></a>Operação  
 O `operation` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|ID|Cadeia|Identificador de recurso. Identifica exclusivamente a operação na instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato de `apis/{aid}/operations/{id}` onde `{aid}` é um identificador de API e `{id}` é um identificador da operação. Esta propriedade é só de leitura.|  
|nome|Cadeia|Nome da operação. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|descrição|Cadeia|Descrição da operação. Não deve estar vazio. Pode incluir a formatação tags HTML. Comprimento máximo é de 1000 carateres.|  
|Esquema|Cadeia|Descreve em protocolos de quais é possível invocar as operações desta API. Valores permitidos são `http`, `https`, ou ambos `http` e `https`.|  
|uriTemplate|Cadeia|Modelo de URL relativo identificar o recurso de destino para esta operação. Pode incluir parâmetros. Exemplo:`customers/{cid}/orders/{oid}/?date={date}`|  
|anfitrião|Cadeia|O URL de gateway de gestão de API que aloja a API.|  
|httpMethod|Cadeia|Método de operação de HTTP.|  
|Pedido|[Pedido de HTTP](#HTTPRequest)|Uma entidade que contém detalhes do pedido.|  
|respostas|matriz de [resposta de HTTP](#HTTPResponse)|Matriz da operação [resposta de HTTP](#HTTPResponse) entidades.|  
  
##  <a name="Menu"></a>Menu de operação  
 O `operation menu` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|ApiId|Cadeia|O id da API atual.|  
|CurrentOperationId|Cadeia|O id da operação atual.|  
|Ação|Cadeia|O tipo de menu.|  
|MenuItems|Coleção de [item de menu operação](#MenuItem) entidades.|As operações da API atual.|  
  
##  <a name="MenuItem"></a>Item de menu de operação  
 O `operation menu item` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|O id da operação.|  
|Título|Cadeia|A descrição da operação.|  
|httpMethod|Cadeia|O método de Http da operação.|  
  
##  <a name="Paging"></a>Paginação  
 O `paging` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Página|Número|O número de página atual.|  
|PageSize|Número|O máximo de resultados a apresentar numa única página.|  
|TotalItemCount|Número|O número de itens para apresentar.|  
|Mostrartudo|Valor booleano|Indica se deve mo todos os resultados numa única página.|  
|Que PageCount|Número|O número de páginas de resultados.|  
  
##  <a name="Parameter"></a>Parâmetro  
 Esta secção descreve o `parameter` representação.  
  
|Propriedade|Descrição|Tipo|  
|--------------|-----------------|----------|  
|nome|Cadeia|Nome do parâmetro.|  
|descrição|Cadeia|Descrição de parâmetro.|  
|valor|Cadeia|Valor do parâmetro.|  
|Opções|Matriz da cadeia|Valores definidos para valores de parâmetro de consulta.|  
|Necessário|Valor booleano|Especifica se é necessário o parâmetro ou não.|  
|tipo|Número|Indica se este parâmetro é um parâmetro de caminho (1) ou um parâmetro de cadeia de consulta (2).|  
|TypeName|Cadeia|Tipo de parâmetro.|  
  
##  <a name="Product"></a>Produto  
 O `product` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|Identificador de recurso. Identifica exclusivamente o produto dentro a instância atual do serviço de API Management. O valor é um URL relativo válido no formato de `products/{pid}` onde `{pid}` é um identificador de produto. Esta propriedade é só de leitura.|  
|Título|Cadeia|Nome do produto. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|Descrição|Cadeia|Descrição do produto. Não deve estar vazio. Pode incluir a formatação tags HTML. Comprimento máximo é de 1000 carateres.|  
|Termos|Cadeia|Produto termos de utilização. Os programadores tentar subscrever o produto serão apresentados e têm de aceitar estes termos antes de poderem concluir o processo de subscrição.|  
|ProductState|Número|Especifica se o produto está publicado ou não. Produtos publicados são Detetáveis pelos programadores no portal de programador. Produtos publicada não estão visíveis apenas para administradores.<br /><br /> Os valores permitidos para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|Valor booleano|Especifica se um utilizador pode ter várias subscrições para este produto ao mesmo tempo.|  
|MultipleSubscriptionsCount|Número|O número de subscrições para este produto, o utilizador atual.|  
  
##  <a name="Provider"></a>Fornecedor  
 O `provider` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Propriedades|dicionário de cadeia|Propriedades para este fornecedor de autenticação.|  
|authenticationType|Cadeia|O tipo de fornecedor. (Azure Active Directory, Facebook início de sessão, a conta do Google, Twitter do Microsoft Account).|  
|Legenda|Cadeia|Nome a apresentar do fornecedor.|  
  
##  <a name="Representation"></a>Representação  
 Esta secção descreve um `representation`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|ContentType|Cadeia|Especifica um tipo de conteúdo registado ou personalizado para este representação, por exemplo, `application/xml`.|  
|exemplo|Cadeia|Um exemplo da representação.|  
  
##  <a name="Subscription"></a>Subscrição  
 O `subscription` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|Identificador de recurso. Identifica de forma exclusiva a subscrição dentro da instância atual do serviço de API Management. O valor é um URL relativo válido no formato de `subscriptions/{sid}` onde `{sid}` é um identificador de subscrição. Esta propriedade é só de leitura.|  
|ProductId|Cadeia|O identificador de recurso de produto do produto subscrito. O valor é um URL relativo válido no formato de `products/{pid}` onde `{pid}` é um identificador de produto.|  
|ProductTitle|Cadeia|Nome do produto. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|ProductDescription|Cadeia|Descrição do produto. Não deve estar vazio. Pode incluir a formatação tags HTML. Comprimento máximo é de 1000 carateres.|  
|ProductDetailsUrl|Cadeia|URL relativo os detalhes do produto.|  
|state|Cadeia|O estado da subscrição. Estados possíveis são:<br /><br /> - `0 - suspended`– a subscrição está bloqueada e o subscritor não é possível chamar as APIs do produto.<br /><br /> - `1 - active`– a subscrição está ativa.<br /><br /> - `2 - expired`– a subscrição atingiu a respetiva data de expiração e foi desativada.<br /><br /> - `3 - submitted`– o pedido de subscrição foi efetuado pelo programador, mas ainda não foi aprovado ou rejeitado.<br /><br /> - `4 - rejected`– o pedido de subscrição foi negado por um administrador.<br /><br /> - `5 - cancelled`– a subscrição foi cancelada pelo administrador ou programador.|  
|displayName|Cadeia|Nome a apresentar da subscrição.|  
|CreatedDate|DateTime|A data a subscrição foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|Valor booleano|Indica se a subscrição pode ser cancelada pelo utilizador atual.|  
|IsAwaitingApproval|Valor booleano|Indica se a subscrição está a aguardar aprovação.|  
|StartDate|DateTime|A data de início para a subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|DateTime|A data de expiração da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|DateTime|A data de notificação da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|Cadeia|A chave primária da subscrição. Comprimento máximo é 256 carateres.|  
|secondaryKey|Cadeia|A chave secundária da subscrição. Comprimento máximo é 256 carateres.|  
|CanBeRenewed|Valor booleano|Indica se a subscrição pode ser renovada até o utilizador atual.|  
|HasExpired|Valor booleano|Se a subscrição expirou.|  
|IsRejected|Valor booleano|Indica se o pedido de subscrição foi negado.|  
|cancelUrl|Cadeia|O Url relativo para cancelar a subscrição.|  
|RenewUrl|Cadeia|O Url relativo para renovar a subscrição.|  
  
##  <a name="SubscriptionSummary"></a>Resumo de subscrição  
 O `subscription summary` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Id|Cadeia|Identificador de recurso. Identifica de forma exclusiva a subscrição dentro da instância atual do serviço de API Management. O valor é um URL relativo válido no formato de `subscriptions/{sid}` onde `{sid}` é um identificador de subscrição. Esta propriedade é só de leitura.|  
|displayName|Cadeia|O nome a apresentar da subscrição|  
  
##  <a name="UserAccountInfo"></a>Informações da conta de utilizador  
 O `user account info` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Nome próprio|Cadeia|Nome próprio. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|Apelido|Cadeia|Apelido. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|E-mail|Cadeia|Endereço de correio eletrónico. Não deve estar vazio e tem de ser exclusivos numa instância de serviço. Comprimento máximo é 254 carateres.|  
|Palavra-passe|Cadeia|Palavra-passe da conta de utilizador.|  
|NameIdentifier|Cadeia|Identificador de conta, o mesmo que o e-mail do utilizador.|  
|ProviderName|Cadeia|Nome do fornecedor de autenticação.|  
|IsBasicAccount|Valor booleano|TRUE se esta conta foi registada com o correio eletrónico e palavra-passe; FALSO se a conta foi registada utilizando um fornecedor.|  
  
##  <a name="UseSignIn"></a>Início de sessão de utilizador  
 O `user sign in` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|E-mail|Cadeia|Endereço de correio eletrónico. Não deve estar vazio e tem de ser exclusivos numa instância de serviço. Comprimento máximo é 254 carateres.|  
|Palavra-passe|Cadeia|Palavra-passe da conta de utilizador.|  
|ReturnUrl|Cadeia|O URL da página onde o utilizador clica no início de sessão.|  
|RememberMe|Valor booleano|Indica se deve guardar informações do utilizador atual.|  
|RegistrationEnabled|Valor booleano|Indica se o registo está ativado.|  
|DelegationEnabled|Valor booleano|Indica se o início de sessão delegado está ativada.|  
|DelegationUrl|Cadeia|O delegado de sessão no url, se estiver ativada.|  
|SsoSignUpUrl|Cadeia|O início de sessão único URL para o utilizador, se estiver presente.|  
|AuxServiceUrl|Cadeia|Se o utilizador atual é um administrador, esta é uma ligação à instância de serviço no Portal clássico do Azure.|  
|Fornecedores|Coleção de [fornecedor](#Provider) entidades|Os fornecedores de autenticação para este utilizador.|  
|UserRegistrationTerms|Cadeia|Termos de licenciamento que um utilizador tem de aceitar antes de iniciar sessão.|  
|UserRegistrationTermsEnabled|Valor booleano|Indica se os termos estão ativados.|  
  
##  <a name="UserSignUp"></a>Inscrição do utilizador  
 O `user sign up` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|PasswordConfirm|Valor booleano|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up)controlo de inscrição.|  
|Palavra-passe|Cadeia|Palavra-passe da conta de utilizador.|  
|PasswordVerdictLevel|Número|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up)controlo de inscrição.|  
|UserRegistrationTerms|Cadeia|Termos de licenciamento que um utilizador tem de aceitar antes de iniciar sessão.|  
|UserRegistrationTermsOptions|Número|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up)controlo de inscrição.|  
|ConsentAccepted|Valor booleano|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up)controlo de inscrição.|  
|E-mail|Cadeia|Endereço de correio eletrónico. Não deve estar vazio e tem de ser exclusivos numa instância de serviço. Comprimento máximo é 254 carateres.|  
|Nome próprio|Cadeia|Nome próprio. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|Apelido|Cadeia|Apelido. Não deve estar vazio. Comprimento máximo é 100 carateres.|  
|UserData|Cadeia|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up) controlo.|  
|NameIdentifier|Cadeia|Valor utilizado pelo [inscrição](api-management-page-controls.md#sign-up)controlo de inscrição.|  
|ProviderName|Cadeia|Nome do fornecedor de autenticação.|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).
