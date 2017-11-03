---
title: Recursos de modelo de API Management do Azure | Microsoft Docs
description: "Saiba mais sobre os tipos de recursos disponíveis para utilização em modelos de portal do programador na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-resources"></a>Recursos de modelo de API Management do Azure
Gestão de API do Azure fornece os seguintes tipos de recursos para utilização no programador modelos portais.  
  
-   [Recursos de cadeia](#strings)  
  
-   [Recursos de glifo](#glyphs)  
  
##  <a name="strings"></a>Recursos de cadeia  
 A API Management fornece um conjunto abrangente de recursos de cadeias para utilização no portal do programador. Estes recursos estão localizados em todos os idiomas suportados pelo API Management. O conjunto de modelos predefinidos utilizará estes recursos, cabeçalhos, etiquetas e quaisquer cadeias de constantes que são apresentadas no portal do programador. Para utilizar um recurso de cadeia no seus modelos, fornece o prefixo de cadeia de recurso seguido do nome de cadeia, conforme mostrado no exemplo seguinte.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo seguinte é a partir do modelo da lista de produto e apresenta **produtos** na parte superior da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Consulte as tabelas seguintes para os recursos de cadeias disponíveis para utilização em modelos de portal do programador. Utilize o nome de tabela como o prefixo para os recursos da cadeia nessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentação](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Pré-visualização de aplicações|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Captura de ecrã|  
|WebApplicationsScreenshotsHeader|Capturas de ecrã|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Tem a certeza de que pretende remover a aplicação?|  
|WebDevelopersAppNotPublished|Não foi publicado|  
|WebDevelopersAppNotSubminted|Não foi submetido|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Nome|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registar a aplicação|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Submeter|  
|WebDevelopersYourApplicationsHeader|Suas aplicações|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicações|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Nome|Texto|  
|----------|----------|  
|NoItemsToDisplay|Não foram encontrados resultados.|  
|GeneralExceptionMessage|Algo não está correto. Isto pode dever uma falha temporária ou um erro. Tente novamente.|  
|GeneralJsonExceptionMessage|Algo não está correto. Isto pode dever uma falha temporária ou um erro. ., Recarregar a página e tente novamente.|  
|ConfirmationMessageUnsavedChanges|Existem algumas alterações não guardadas. Tem a certeza de que pretende cancelar e eliminar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Pedido de corpo HTTP demasiado grande.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|GeneralExceptionMessage|Algo não está correto. Isto pode dever uma falha temporária ou um erro. Tente novamente.|  
|NoItemsToDisplay|Não existem itens a apresentar.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|última|  
|PagerButtonLabelNext|Seguinte|  
|PagerButtonLabelPrevious|Prev|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|A palavra-passe é demasiado curta|  
|EmailAsPassword|Não utilize o seu correio eletrónico como a palavra-passe|  
|PasswordSameAsUserName|A palavra-passe não pode conter o seu nome de utilizador|  
|PasswordTwoCharacterClasses|Utilizar classes de carateres diferentes|  
|PasswordTooManyRepetitions|Demasiados repetições|  
|PasswordSequenceFound|A palavra-passe contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|A carregar...|  
|TablePlaceholderNothingToDisplay|Não há nenhum dado para o período selecionado e o âmbito|  
|ButtonLabelClose|Fechar|  
  
###  <a name="Documentation"></a>Documentação  
  
|Nome|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho inválido '{0}'|  
|WebDocumentationInvalidRequestErrorMessage|URL de pedido inválido|  
|TextboxLabelAccessToken|Token de acesso *|  
|DropdownOptionPrimaryKeyFormat|Primário-{0}|  
|DropdownOptionSecondaryKeyFormat|Secundário-{0}|  
|WebDocumentationSubscriptionKeyText|A chave de subscrição|  
|WebDocumentationTemplatesAddHeaders|Adicionar cabeçalhos HTTP necessários|  
|WebDocumentationTemplatesBasicAuthSample|Exemplo de autorização básicas|  
|WebDocumentationTemplatesCurlForBasicAuth|para utilização de autorização básicas: – utilizador {username}: {palavra-passe}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique os valores dos parâmetros de caminho (mostrados como {...}), a chave de subscrição e os valores de parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique a chave de subscrição|  
|WebDocumentationTemplatesJavaApache|Este exemplo utiliza o cliente Apache HTTP de componentes de HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especifique os valores de parâmetros opcionais, conforme necessário|  
|WebDocumentationTemplatesPhpPackage|Este exemplo utiliza o pacote de HTTP_Request2. (para obter mais informações: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especifique os valores dos parâmetros de caminho (mostrados como {...}) e corpo do pedido, se for necessário|  
|WebDocumentationTemplatesRequestBody|Especifique o corpo do pedido|  
|WebDocumentationTemplatesRequiredParams|Especifique valores para os seguintes parâmetros obrigatórios|  
|WebDocumentationTemplatesValuesForPath|Especifique os valores dos parâmetros de caminho (mostrados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto final de autorização é utilizado para interagir com o proprietário do recurso e obter uma concessão de autorização.|  
|OAuth2AuthorizationEndpointName|ponto final de autorização|  
|OAuth2TokenEndpointDescription|Ponto final do token é utilizado pelo cliente para obter um token de acesso ao apresentar o respetivo conceder de autorização ou atualizar o token.|  
|OAuth2TokenEndpointName|ponto final de tokens|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo ao instruir agente o proprietário de recursos de utilizador para o ponto final de autorização.  O cliente inclui o respetivo identificador de cliente, o âmbito de pedido, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente de utilizador novamente depois de acesso é concedido (ou negado).     </p\> < p\> o servidor de autorização autentica o proprietário do recurso (através do agente de utilizador) e estabelece se o proprietário do recurso concede ou nega o pedido de acesso do cliente.     </p\> < p\> pressuposto de que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador para o cliente utilizando o redirecionamento de URI fornecido anterior (no pedido ou durante registrati de cliente no).  O URI de redirecionamento inclui um código de autorização e de qualquer local estado fornecido pelo cliente anteriormente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o utilizador nega o pedido de acesso de se o pedido é inválido, o cliente será informado utilizando os seguintes parâmetros adicionados para o redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> a aplicação cliente terá de enviar o utilizador para o ponto final de autorização para iniciar o processo de OAuth.          O ponto final de autorização, o utilizador efetua a autenticação e, em seguida, atribui ou nega o acesso à aplicação.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> pressuposto de que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador para o cliente utilizando o redirecionamento de URI fornecido anterior (no pedido ou durante o registo de cliente).  O URI de redirecionamento inclui um código de autorização e de qualquer local estado fornecido pelo cliente anteriormente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> o cliente pede um token de acesso ao servidor de autorização ' ponto final de tokens s, incluindo o código de autorização recebido no passo anterior.  Quando efetua o pedido, o cliente efetua a autenticação com o servidor de autorização.  O cliente inclui o redirecionamento que URI utilizado para obter o código de autorização para verificação. </p\> < p\> o servidor de autorização autentica o cliente, valida o código de autorização e garante que o redirecionamento URI recebido corresponde ao URI utilizado para redirecionar o cliente no passo (C).  Se forem válidas, o servidor de autorização responde novamente com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> se a autenticação de cliente de pedido falha ou é inválida, o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido para ponto final do token, enviando os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de carateres do UTF-8 no HTTP pedem de corpo de entidade. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> o servidor de autorização emite um token de acesso e o token de atualização opcional e construções a resposta ao adicionar os seguintes parâmetros para o corpo de entidade da resposta HTTP com um código de estado (OK) 200. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> o cliente efetua a autenticação com o servidor de autorização e pedir um token de acesso de ponto final do token. </p\> < p\> o servidor de autorização autentica o cliente e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se o pedido falha na autenticação de cliente ou é inválido o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido para ponto final do token ao adicionar os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de carateres do UTF-8 no HTTP pedem de corpo de entidade. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se o pedido de token de acesso é válidos e autorizado, o servidor de autorização emite um token de acesso e o token de atualização opcional e construções a resposta ao adicionar os seguintes parâmetros para o corpo de entidade da resposta HTTP com um 200  Código de estado (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo ao instruir o proprietário do recurso ' s-agente de utilizador para o ponto final de autorização.  O cliente inclui o respetivo identificador de cliente, o âmbito de pedido, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente de utilizador novamente depois de acesso é concedido (ou negado). </p\> < p\> o servidor de autorização autentica o proprietário do recurso (através do agente de utilizador) e estabelece se o proprietário do recurso concede ou nega o cliente "pedido de acesso de s. </p\> < p\> pressuposto de que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador para o cliente utilizando o redirecionamento de URI fornecido anteriormente.  O URI de redirecionamento inclui o token de acesso de fragmento URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o proprietário do recurso nega o pedido de acesso ou se falhar o pedido por motivos que não seja um em falta ou inválido URI de redirecionamento, o servidor de autorização informa o cliente ao adicionar os seguintes parâmetros para o o componente de fragmento o redirecionamento URI utilizando o formato "application/x-www-form-urlencoded" f. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> a aplicação cliente terá de enviar o utilizador para o ponto final de autorização para iniciar o processo de OAuth.      O ponto final de autorização, o utilizador efetua a autenticação e, em seguida, atribui ou nega o acesso à aplicação. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> se o proprietário do recurso concede o pedido de acesso, o servidor de autorização emite um token de acesso e disponibiliza-o cliente ao adicionar os seguintes parâmetros para o componente de fragmento da utilizar o "application/x-www URI de redirecionamento formato de - de forma - urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Fluxo de código de autorização está otimizado para clientes com capacidade de manter a confidencialidade das suas credenciais (por exemplo, web server aplicações implementadas utilizando o PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão do código de autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Fluxo de credenciais de cliente é adequado em casos em que o cliente (aplicação) está a solicitar acesso a recursos protegidos sob o controlo dele. O cliente é considerado como um proprietário de recursos, pelo que é necessária qualquer interação do utilizador final.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de credenciais do cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Fluxo implícito está otimizado para clientes sem capacidade de manter a confidencialidade das respetivas credenciais conhecido operar um URI de redirecionamento específico. Normalmente, estes clientes são implementados num browser utilizando uma linguagem de script, como JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Concessão implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Fluxo de credenciais de palavra-passe de proprietário de recursos é adequado em casos em que o proprietário do recurso tem uma relação de fidedignidade com o cliente (aplicação), como o sistema operativo do dispositivo ou uma aplicação altamente privilegiada. Este fluxo é adequado para clientes com capacidade de obter o recurso de credenciais do proprietário (nome de utilizador e palavra-passe, normalmente, utilizando uma forma interativa).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concessão de credenciais de palavra-passe de proprietário do recurso|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> o proprietário do recurso fornece ao cliente com o nome de utilizador e palavra-passe. </p\> < p\> o cliente pede um token de acesso ao servidor de autorização ' ponto final de tokens s, incluindo as credenciais que recebeu o proprietário do recurso.  Quando efetua o pedido, o cliente efetua a autenticação com o servidor de autorização. </p\> < p\> o servidor de autorização autentica o cliente valida as credenciais de proprietário do recurso e se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se o pedido falha na autenticação de cliente ou é inválido o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido para ponto final do token ao adicionar os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de carateres do UTF-8 no HTTP pedem de corpo de entidade. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se o pedido de token de acesso é válidos e autorizado, o servidor de autorização emite um token de acesso e o token de atualização opcional e construções a resposta ao adicionar os seguintes parâmetros para o corpo de entidade da resposta HTTP com um 20 Código de estado (OK) 0. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Pedido de token de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|NECESSÁRIO. Identificador do cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO se o cliente não está a autenticar com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|NECESSÁRIO. O identificador de cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|NECESSÁRIO. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Legível por humanos texto ASCII a fornecer informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Legível por humanos texto ASCII a fornecer informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Legível por humanos texto ASCII a fornecer informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Legível por humanos texto ASCII a fornecer informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Legível por humanos texto ASCII a fornecer informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|NECESSÁRIO. Um código de erro ASCII único seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|NECESSÁRIO. Um código de erro ASCII único seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|NECESSÁRIO. Um código de erro ASCII único seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|NECESSÁRIO. Um código de erro ASCII único seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|NECESSÁRIO. Um código de erro ASCII único seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO. Valor deve ser definido como "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|NECESSÁRIO. Valor deve ser definido como "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|NECESSÁRIO. Valor deve ser definido como "palavra-passe".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|NECESSÁRIO. A palavra-passe de proprietário de recursos.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O URI do ponto final do redirecionamento tem de ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO se o parâmetro de "redirect_uri" foi incluído no pedido de autorização e os respetivos valores têm de ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCIONAL. O URI do ponto final do redirecionamento tem de ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter os tokens de acesso de novo.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter os tokens de acesso de novo.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter os tokens de acesso de novo.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|NECESSÁRIO. Valor deve ser definido como "código".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|NECESSÁRIO. Valor tem de ser definido para "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, é necessário.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, é necessário.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, é necessário.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, é necessário.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|NECESSÁRIO se o parâmetro de "state" estava presente no pedido de autorização de cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco utilizado pelo cliente para manter o estado entre o pedido e a chamada de retorno.  O servidor de autorização inclui este valor quando redirecionar o agente de utilizador para o cliente.  O parâmetro deve ser utilizado para impedir a falsificação de pedidos entre sites.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|NECESSÁRIO se o parâmetro de "state" estava presente no pedido de autorização de cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|NECESSÁRIO se o parâmetro de "state" estava presente no pedido de autorização de cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco utilizado pelo cliente para manter o estado entre o pedido e a chamada de retorno.  O servidor de autorização inclui este valor quando redirecionar o agente de utilizador para o cliente.  O parâmetro deve ser utilizado para impedir a falsificação de pedidos entre sites.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|NECESSÁRIO se o parâmetro de "state" estava presente no pedido de autorização de cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NECESSÁRIO. O tipo de token emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|NECESSÁRIO. O nome de utilizador de proprietário do recurso.|  
|OAuth2UnsupportedTokenType|Tipo de token '{0}' não é supporetd.|  
|OAuth2InvalidState|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|Código de autorização|  
|OAuth2GrantType_Implicit|Implícita|  
|OAuth2GrantType_ClientCredentials|Credenciais do cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Palavra-passe de proprietário de recursos|  
|WebDocumentation302Code|Foram encontradas 302|  
|WebDocumentation400Code|400 (pedido incorreto)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso através de {0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma ligação HTTP para o servidor de autorização ou ter sido fechado inesperadamente.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ocorreu um erro inesperado.|  
|AuthorizationServerCommunicationException|Ocorreu a exceção de comunicação do servidor de autorização. Contacte o administrador.|  
|TextblockSubscriptionKeyHeaderDescription|Chave de subscrição que fornece acesso a esta API. Foi encontrado no seu < uma href ='/ Programador '\>perfil < /a\>.|  
|TextblockOAuthHeaderDescription|O token de acesso de OAuth 2.0 obtidos a partir de < posso\>{0} </i\>. Conceder tipos suportados: < i\>{1} </i\>.|  
|TextblockContentTypeHeaderDescription|Tipo de suporte de dados do corpo enviado para a API.|  
|ErrorMessageApiNotAccessible|A API está a tentar chamar não está acessível neste momento. Contacte o fabricante de API < uma href = "/ emite"\>aqui < /a\>.|  
|ErrorMessageApiTimedout|A API está a tentar chamar está a demorar mais que o habitual para voltar a resposta. Contacte o fabricante de API < uma href = "/ emite"\>aqui < /a\>.|  
|BadRequestParameterExpected|"o parâmetro '{0}' é esperado"|  
|TooltipTextDoubleClickToSelectAll|Faça duplo clique para selecionar todos os.|  
|TooltipTextHideRevealSecret|Mostrar/ocultar|  
|ButtonLinkOpenConsole|Experimente|  
|SectionHeadingRequestBody|Corpo do pedido|  
|SectionHeadingRequestParameters|Parâmetros do pedido|  
|SectionHeadingRequestUrl|URL do pedido|  
|SectionHeadingResponse|Resposta|  
|SectionHeadingRequestHeaders|Cabeçalhos de pedido|  
|FormLabelSubtextOptional|Opcional|  
|SectionHeadingCodeSamples|Exemplos de código|  
|TextblockOpenidConnectHeaderDescription|Token de id OpenID Connect obtidos a partir de < posso\>{0} </i\>. Conceder tipos suportados: < i\>{1} </i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Nome|Texto|  
|----------|----------|  
|LinkLabelBack|fazer uma cópia|  
|LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|Envie-num e-mail|  
|PageTitleError|Lamentamos, mas ocorreu um problema que serve a página pedida|  
|TextblockPotentialCauseIntermittentIssue|Isto pode ser um problema de acesso de dados intermitente que se encontre removido.|  
|TextblockPotentialCauseOldLink|A ligação que poderá ter clicado pode ser antigo e não deixe a apontar para a localização correta.|  
|TextblockPotentialCauseTechnicalProblem|Pode existir um problema técnico do nosso lado.|  
|TextblockPotentialSolutionRefresh|Tente atualizar a página.|  
|TextblockPotentialSolutionStartOver|Começar através do nosso {0}.|  
|TextblockPotentialSolutionTryAgain|Aceda {0} e repita a ação efetuada novamente.|  
|TextReportProblem|{0} que descrevem o que aconteceu de errado e iremos abordar o-lo logo que possível.|  
|TitlePotentialCause|Causas possíveis|  
|TitlePotentialSolution|É possivelmente apenas um problema temporário, algumas coisas a experimentar|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Problemas|  
|WebIssuesNoActiveSubscriptions|Ter não existem subscrições ativas. Terá de subscrever um produto comunicar um problema.|  
|WebIssuesNotSignin|Não tem sessão iniciada. Volte a {0} para comunicar um problema ou após um comentário.|  
|WebIssuesReportIssueButton|Problema de relatório|  
|WebIssuesSignIn|iniciar sessão|  
|WebIssuesStatusReportedBy|Estado: {0} &#124; Comunicado pelo {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Nome|Texto|  
|----------|----------|  
|LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|Envie-num e-mail|  
|PageTitleNotFound|Lamentamos, mas não foi possível encontrar a página que procura|  
|TextblockPotentialCauseMisspelledUrl|Pode ter mal escritos o URL se tiver digitado-lo na.|  
|TextblockPotentialCauseOldLink|A ligação que poderá ter clicado pode ser antigo e não deixe a apontar para a localização correta.|  
|TextblockPotentialSolutionRetype|Tente retyping o URL.|  
|TextblockPotentialSolutionStartOver|Começar através do nosso {0}.|  
|TextReportProblem|{0} que descrevem o que aconteceu de errado e iremos abordar o-lo logo que possível.|  
|TitlePotentialCause|Causas possíveis|  
|TitlePotentialSolution|Potencial solução|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebProductsAgreement|Ao subscrever {0} produto, concordo com os `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Termos de Utilização|  
|WebProductsSubscribeButton|Subscrever|  
|WebProductsUsageLimitsHeader|Limites de utilização|  
|WebProductsYouAreNotSubscribed|Tem subscritos deste produto.|  
|WebProductsYouRequestedSubscription|Pediu subscrição deste produto.|  
|ErrorYouNeedtoAgreeWithLegalTerms|Tem de aceitar os termos de utilização para poder continuar.|  
|ButtonLabelAddSubscription|Adicionar subscrição|  
|LinkLabelChangeSubscriptionName|alterar|  
|ButtonLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Tem {0} subscrições para este produto:|  
|TextblockSingleSubscriptionsCount|Tem {0} subscrição para este produto:|  
|TextblockSingleApisCount|Este produto contém {0} API:|  
|TextblockMultipleApisCount|Este produto contém {0} APIs:|  
|TextblockHeaderSubscribe|Subscrever o produto|  
|TextblockSubscriptionDescription|Uma nova subscrição será criada da seguinte forma:|  
|TextblockSubscriptionLimitReached|Foi atingido o limite de subscrições.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Nome|Texto|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Início de sessão está desativado por administradores de momento.|  
|TextboxExternalIdentitiesSigninInvitation|Em alternativa, inicie sessão com|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Inicie sessão com:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Nome|Texto|  
|----------|----------|  
|PrincipalNotFound|Não é possível encontrar o principal ou a assinatura é inválida|  
|ErrorSsoAuthenticationFailed|Falha na autenticação de SSO|  
|ErrorSsoAuthenticationFailedDetailed|Não é possível verificar a assinatura ou um token inválido fornecido.|  
|ErrorSsoTokenInvalid|Token SSO é inválido|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail '{0}' já foi registada|  
|ValidationErrorSpecificEmailInvalid|O e-mail '{0}' é inválido|  
|ValidationErrorPasswordInvalid|Palavra-passe é inválida. Corrija os erros e tente novamente.|  
|PropertyTooShort|{0} é demasiado curto|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de correio eletrónico inválido.|  
|ValidationMessageNewPasswordConfirmationRequired|Confirmar nova palavra-passe|  
|ValidationErrorPasswordConfirmationRequired|Confirme a palavra-passe está vazia|  
|WebAuthenticationEmailChangeNotice|O e-mail de confirmação de alteração está a forma de {0}. Siga as instruções dentro da mesma para confirmar o seu novo endereço de correio eletrónico. Se o e-mail não chegam a sua pasta a receber nos próximos alguns minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationEmailChangeNoticeHeader|O pedido de alteração de correio eletrónico foi processado com êxito|  
|WebAuthenticationEmailChangeNoticeTitle|Pedido de alteração de correio eletrónico|  
|WebAuthenticationEmailHasBeenRevertedNotice|Correio eletrónico já existe. Pedido foi revertido|  
|ValidationErrorEmailAlreadyExists|Correio eletrónico já existe|  
|ValidationErrorEmailInvalid|Endereço de correio eletrónico inválido|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|O e-mail é necessário.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|WebAuthenticationFieldLengthErrorMessage|{0} tem de ser um comprimento máximo de {1}|  
|TextboxLabelEmailFirstName|Nome próprio|  
|ValidationErrorFirstNameRequired|É necessário o nome próprio.|  
|ValidationErrorFirstNameInvalid|Nome do próprio inválido|  
|NoticeInvalidInvitationToken|Tenha em atenção que as ligações de confirmação são válidas para só de 48 horas. Se não estiver ainda durante este período de tempo, certifique-se que a ligação está correta. Se a ligação tiver expirado, em seguida, repita a ação que está a tentar para confirmar.|  
|NoticeHeaderInvalidInvitationToken|Token inválido convite|  
|NoticeTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Apelido inválido|  
|TextboxLabelEmailLastName|Apelido|  
|ValidationErrorLastNameRequired|É necessário um apelido.|  
|WebAuthenticationLinkExpiredNotice|Ligação de confirmação enviada expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A ligação de reposição de palavra-passe é inválida ou expirada.|  
|WebAuthenticationLinkExpiredNoticeTitle|Ligação enviada|  
|WebAuthenticationNewPasswordLabel|Nova palavra-passe|  
|ValidationMessageNewPasswordRequired|Nova palavra-passe é obrigatório.|  
|TextboxLabelNotificationsSenderEmail|Correio eletrónico do remetente de notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|A palavra-passe foi atualizada com êxito|  
|WebAuthenticationPasswordChangedNoticeTitle|Palavra-passe atualizado|  
|WebAuthenticationPasswordCompareErrorMessage|As palavras-passe não correspondem|  
|WebAuthenticationPasswordConfirmLabel|Confirmar palavra-passe|  
|ValidationErrorPasswordInvalidDetailed|Palavra-passe é demasiado fraco.|  
|WebAuthenticationPasswordLabel|Palavra-passe|  
|ValidationErrorPasswordRequired|É necessária a palavra-passe.|  
|WebAuthenticationPasswordResetSendNotice|E-mail de confirmação de palavra-passe de alteração se a forma de {0}. Siga as instruções no e-mail para continuar o processo de alteração de palavra-passe.|  
|WebAuthenticationPasswordResetSendNoticeHeader|O pedido de reposição de palavra-passe foi processado com êxito|  
|WebAuthenticationPasswordResetSendNoticeTitle|Pedido de reposição da palavra-passe|  
|WebAuthenticationRequestNotFoundNotice|Não foi encontrado um pedido|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Correio eletrónico do remetente de notificações está vazio|  
|WebAuthenticationSigninPasswordLabel|Confirme a alteração ao introduzir uma palavra-passe|  
|WebAuthenticationSignupConfirmNotice|O e-mail de confirmação de registo está a forma de {0}. < br /\> siga as instruções no e-mail para ativar a conta. < br /\> se o e-mail não chegam a sua pasta a receber nos próximos alguns minutos, verifique o eletrónico de lixo pasta de correio eletrónico.|  
|WebAuthenticationSignupConfirmNoticeHeader|A conta foi criada com êxito|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|E-mail de confirmação de registo foi enviado novamente|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|Token está vazio|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um utilizador com este e-mail já está registado no sistema. Se esqueceu a sua palavra-passe, tente restaurá-lo ou contactar a nossa equipa de suporte.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Utilizador já registado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já foi registada|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelChangeAccountInfo|Alterar informações de conta|  
|ButtonLabelCloseAccount|Conta fechar|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto introduzido não corresponde ao texto na imagem. Tente novamente.|  
|ValidationErrorCredentialsInvalid|E-mail ou palavra-passe é inválida. Corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|O pedido não é válido|  
|WebAuthenticationUserIsNotConfirm|Verifique se o seu registo antes de tentar iniciar sessão.|  
|WebAuthenticationInvalidEmailFormated|O e-mail é inválido: {0}|  
|WebAuthenticationUserNotFound|Utilizador não encontrado|  
|WebAuthenticationTenantNotRegistered|A conta pertence a um inquilino do Azure Active Directory que não está autorizado a aceder a este portal.|  
|WebAuthenticationAuthenticationFailed|Falha na autenticação.|  
|WebAuthenticationGooglePlusNotEnabled|Falha na autenticação. Se autorizar a aplicação, em seguida,. Contacte o administrador para se certificar de que a autenticação de Google está configurado corretamente.|  
|ValidationErrorAllowedTenantIsRequired|É necessário o inquilino permitido|  
|ValidationErrorTenantIsNotValid|O inquilino do Azure Active Directory '{0}' não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Inicie sessão com a sua conta {0}|  
|WebAuthenticationUserLimitNotice|Este serviço foi atingido o número máximo de utilizadores permitidos. . `<a href="mailto:{0}"\>contact the administrator</a\>` Para atualizar o seu serviço e volte a ativar o registo de utilizador.|  
|WebAuthenticationUserLimitNoticeHeader|Registo de utilizador desativado|  
|WebAuthenticationUserLimitNoticeTitle|Registo de utilizador desativado|  
|WebAuthenticationUserRegistrationDisabledNotice|Registo de utilizadores foi desativado pelo administrador. Inicie sessão com o fornecedor de identidade externas.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registo de utilizador desativado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registo de utilizador desativado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes, pode concluir a criação da sua conta é necessário verificar o seu endereço de correio eletrónico. Enviámos uma mensagem de correio eletrónico para {0}. Siga as instruções no interior de correio electrónico para ativar a sua conta. Se o correio eletrónico não chegam nos próximos alguns minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontrámos uma conta holograma para o endereço de correio electrónico {0}. Para concluir a criação da sua conta, que temos de verificar o seu endereço de correio eletrónico. Enviámos uma mensagem de correio eletrónico para {0}. Siga as instruções no interior de correio electrónico para ativar a sua conta. Se o correio eletrónico não chegam nos próximos alguns minutos, verifique a pasta de e-mail de lixo|  
|WebAuthenticationSignupConfirmationAlmostDone|Quase concluído|  
|WebAuthenticationSignupConfirmationEmailSent|Enviámos uma mensagem de correio eletrónico para {0}. Siga as instruções no interior de correio electrónico para ativar a sua conta. Se o correio eletrónico não chegam nos próximos alguns minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationEmailSentNotificationMessage|Correio eletrónico enviado com êxito para {0}|  
|WebAuthenticationNoAadTenantConfigured|Nenhum inquilino do Azure Active Directory configurado para o serviço.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Concordo com os `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Reveja`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Termos de Utilização|  
|ValidationMessageConsentNotAccepted|Tem de aceitar os termos de utilização para poder continuar.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|Esqueceu-se da sua palavra-passe?|  
|WebAuthenticationIfAdministrator|Se for um administrador tem de iniciar sessão `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Não num membro ainda? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Lembre-se de que me são neste computador|  
|WebAuthenticationSigininWithPassword|Inicie sessão com o nome de utilizador e palavra-passe|  
|WebAuthenticationSigninTitle|Iniciar sessão|  
|WebAuthenticationSignUpNow|Registe-se já|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleSignup|Inscrever-se|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de gestão de API|  
|WebAuthenticationSigninNow|Inicie sessão agora|  
|ButtonLabelSignup|Inscrever-se|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Nome|Texto|  
|----------|----------|  
|SubscriptionCancelConfirmation|Tem a certeza de que pretende cancelar esta subscrição?|  
|SubscriptionRenewConfirmation|Tem a certeza de que pretende renovar esta subscrição?|  
|WebDevelopersManageSubscriptions|Gerir subscrições|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscription|Renovar|  
|WebDevelopersSubscriptionReqested|Pedido em {0}|  
|WebDevelopersSubscriptionRequestedState|Pedido|  
|WebDevelopersSubscriptionTableNameHeader|Nome|  
|WebDevelopersSubscriptionTableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|Suas subscrições|  
|SubscriptionPropertyLabelRequestedDate|Pedido em|  
|SubscriptionPropertyLabelStartedDate|Foi iniciado em|  
|PageTitleRenameSubscription|Mudar o nome de subscrição|  
|SubscriptionPropertyLabelName|Nome da subscrição|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Nome|Texto|  
|----------|----------|  
|SectionHeadingCloseAccount|Está à procura de fechar a sua conta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Ocultar|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegenerated|Tem a certeza de que pretende regenerar a chave deste?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelUpdateProfile|Atualizar perfil|  
|PageTitleUpdateProfile|Atualizar informações de conta|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informações de conta|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelCloseAccount|Conta fechar|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Nome próprio|  
|TextboxLabelEmailLastName|Apelido|  
|TextboxLabelNotificationsSenderEmail|Correio eletrónico do remetente de notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|SubscriptionStateActive|Ativa|  
|SubscriptionStateCancelled|Foi cancelada|  
|SubscriptionStateExpired|Expirou|  
|SubscriptionStateRejected|Rejeitados|  
|SubscriptionStateRequested|Pedido|  
|SubscriptionStateSuspended|Suspenso|  
|DefaultSubscriptionNameTemplate|{0} (predefinição)|  
|SubscriptionNameTemplate|Acesso de programador #{0}|  
|TextboxLabelSubscriptionName|Nome da subscrição|  
|ValidationMessageSubscriptionNameRequired|Nome da subscrição não pode estar vazio.|  
|ApiManagementUserLimitReached|Este serviço foi atingido o número máximo de utilizadores permitidos. Atualize para um escalão de preço superior.|  
  
##  <a name="glyphs"></a>Recursos de glifo  
 Modelos de portais de Programador de API Management, podem utilizar os glifos de [Glyphicons de arranque](http://getbootstrap.com/components/#glyphicons). Este conjunto de glifos inclui mais de 250 glyphs no formato de tipo de letra a partir de [Glyphicon](http://glyphicons.com/) Halflings definido. Para utilizar um glifo neste conjunto, utilize a seguinte sintaxe.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [Glyphicons de arranque](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).
