---
title: "Glossário do programador do Azure Active Directory | Microsoft Docs"
description: Uma lista dos termos de licenciamento para frequentemente utilizadas conceitos do programador do Azure Active Directory e funcionalidades.
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 6a657177087ca01f4507b12048a88850aea1a03f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-active-directory-developer-glossary"></a>Glossário de programador do Azure Active Directory
Este artigo contém definições para alguns dos principais do Azure Active Directory (AD) para programadores conceitos, que são úteis para saber mais sobre o desenvolvimento de aplicações do Azure AD.

## <a name="access-token"></a>Token de acesso
Um tipo de [token de segurança](#security-token) emitido por uma [servidor autorização](#authorization-server)e utilizado por um [aplicação cliente](#client-application) para poder aceder a um [recursos servidor protegido](#resource-server). Normalmente, sob a forma de um [JSON Web tokens (JWT)][JWT], o token embodies a autorização concedida para o cliente pelo [proprietário do recurso](#resource-owner), para um nível de acesso. O token contém todos os aplicável [afirmações](#claim) sobre o assunto, ativar a aplicação de cliente para utilizá-lo como um formulário de credenciais quando aceder a um recurso especificado. Isto também elimina a necessidade do proprietário do recurso para expor as credenciais para o cliente.

Os tokens de acesso, por vezes, são referidos como "Utilizador + aplicação" ou "Aplicação só", consoante as credenciais que está a ser representadas. Por exemplo, quando uma aplicação de cliente utiliza a:

* [Conceder de autorização de "Código de autorização"](#authorization-grant), o utilizador final autentica pela primeira vez que o proprietário do recurso, delegar a autorização para o cliente para aceder ao recurso. O cliente mais tarde, autentica ao obter o token de acesso. O token pode, por vezes, ser referido mais especificamente como um token de "Utilizador + aplicação", pois representa tanto o utilizador autorizado a aplicação de cliente e a aplicação.
* [Conceder de autorização de "Credenciais de cliente"](#authorization-grant), o cliente fornece autenticação única, a funcionar sem autenticação/autorização do-proprietário do recurso, pelo que o token, por vezes, pode ser referido como um token "Só de aplicação".

Consulte [referência de Token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-id-client-id"></a>id da aplicação (id de cliente)
Os problemas de identificador exclusivo do Azure AD para um registo de aplicação que identifica uma aplicação específica e as configurações associadas.  Este id de aplicação ([id de cliente](https://tools.ietf.org/html/rfc6749#page-15)) é utilizado quando efetuar a autenticação de pedidos e é fornecido para as bibliotecas de autenticação no tempo de desenvolvimento. O id de aplicação (id de cliente) não é um segredo. 

## <a name="application-manifest"></a>manifesto da aplicação
Uma funcionalidade fornecida pelo [portal do Azure][AZURE-portal], que produz uma representação JSON de configuração da identidade da aplicação, utilizada como um mecanismo para atualizar associado [aplicação] [ AAD-Graph-App-Entity] e [ServicePrincipal] [ AAD-Graph-Sp-Entity] entidades. Consulte [compreender o manifesto da aplicação do Azure Active Directory] [ AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>Objeto de aplicação
Quando lhe registe/atualizar uma aplicação no [portal do Azure][AZURE-portal], o portal cria/atualizações de um objeto de aplicação e um correspondente [objeto principal do serviço](#service-principal-object) para esse inquilino. O objeto de aplicação *define* a aplicação da configuração da identidade globalmente (em todos os inquilinos onde tem acesso), que fornece um modelo a partir dos quais são os objetos de principal de serviço correspondente *derivada* para utilização localmente em tempo de execução (de um inquilino específico).

Consulte [aplicação e objetos de principais de serviço] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="application-registration"></a>Registo de aplicação
Para permitir que uma aplicação integrar e delegar funções de gestão de acesso e de identidade para o Azure AD, tem de ser registado com um Azure AD [inquilino](#tenant). Ao registar a sua aplicação com o Azure AD, que está a fornecer uma configuração de identidade para a sua aplicação, permitindo que integrar com o Azure AD e utilizar as funcionalidades, tais como:

* Uma gestão robusta de Single Sign-On utilizando a gestão de identidade do Azure AD e [OpenID Connect] [ OpenIDConnect] implementação de protocolo
* Mediadas acesso [recursos protegidos](#resource-server) por [aplicações cliente](#client-application), através do Azure AD OAuth 2.0 [servidor autorização](#authorization-server) implementação
* [Arquitetura de consentimento](#consent) para gerir o acesso de cliente aos recursos protegidos, com base na autorização do proprietário de recursos.

Consulte [integrar aplicações com o Azure Active Directory] [ AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>Autenticação
O ato de um desafio um terceiros legítimos credenciais, que fornece a base para a criação de um principal de segurança a ser utilizada para identidade e controlo de acesso. Durante uma [conceder de autorização de OAuth2](#authorization-grant) por exemplo, o autenticação de terceiros estão a preencher a função do [proprietário do recurso](#resource-owner) ou [aplicação cliente](#client-application), consoante a concessão utilizada.

## <a name="authorization"></a>Autorização
O ato de uma permissão de principal de segurança autenticado para fazer algo de concessão de permissões. Existem dois cenários de utilização principal para o modelo de programação do Azure AD:

* Durante uma [conceder de autorização de OAuth2](#authorization-grant) fluxo: quando o [proprietário do recurso](#resource-owner) concede autorização para o [aplicação cliente](#client-application), permitindo que o cliente aceder aos recursos do proprietário do recurso.
* Durante o acesso a recursos pelo cliente: conforme implementado pelo [servidor recursos](#resource-server), utilizando o [afirmação](#claim) valores está presente no [token de acesso](#access-token) para tomar decisões de controlo de acesso baseado nelas.

## <a name="authorization-code"></a>Código de autorização
Um suma lived "token" fornecido para uma [aplicação cliente](#client-application) pelo [ponto final de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma do OAuth2 quatro [concede autorização](#authorization-grant). É devolvido o código para a aplicação de cliente em resposta a autenticação de um [proprietário do recurso](#resource-owner), que indica que o proprietário do recurso tem delegado autorização para aceder aos recursos do pedido. Como parte do fluxo, o código é mais tarde resgatado para um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto final de autorização
Um dos pontos finais implementados pelo [servidor autorização](#authorization-server), utilizado para interagir com o [proprietário do recurso](#resource-owner) para fornecer um [conceder de autorização](#authorization-grant) durante um OAuth2 fluxo de concessão de autorização. Consoante o fluxo de conceder de autorização utilizado, pode variar a concessão real fornecida, incluindo um [código de autorização](#authorization-code) ou [token de segurança](#security-token).

Consulte a especificação OAuth2 [tipos de conceder de autorização] [ OAuth2-AuthZ-Grant-Types] e [ponto final de autorização] [ OAuth2-AuthZ-Endpoint] secções e o [OpenIDConnect especificação] [ OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>conceder de autorização
Uma credencial que representa o [do proprietário do recurso](#resource-owner) [autorização](#authorization) para aceder os recursos protegidos, concedidos a um [aplicação cliente](#client-application). Uma aplicação cliente pode utilizar um do [conceder quatro tipos definidos pelo Framework de autorização de OAuth2] [ OAuth2-AuthZ-Grant-Types] para obter uma concessão, dependendo do tipo/requisitos do cliente: "concessão do código de autorização", "conceder credenciais de cliente", "concessão implícita" e "conceder credenciais de palavra-passe de proprietário do recurso". A credencial devolvida para o cliente encontra-se um [token de acesso](#access-token), ou um [código de autorização](#authorization-code) (trocados mais tarde para um token de acesso), dependendo do tipo de conceder de autorização utilizada.

## <a name="authorization-server"></a>servidor de autorização
Conforme definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], o servidor responsável pelo acesso de emitir tokens para o [cliente](#client-application) concluída com êxito a autenticação de [proprietário do recurso](#resource-owner) e obter a autorização. A [aplicação cliente](#client-application) interage com o servidor de autorização no tempo de execução através do respetivo [autorização](#authorization-endpoint) e [token](#token-endpoint) pontos finais, de acordo com o OAuth2 definido [concede autorização](#authorization-grant).

No caso de integração de aplicações do Azure AD, do Azure AD implementa a função de servidor de autorização para aplicações do Azure AD e Microsoft APIs do serviço, por exemplo [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>Afirmação
A [token de segurança](#security-token) contém afirmações, que fornecem asserções sobre uma entidade (tal como um [aplicação cliente](#client-application) ou [proprietário do recurso](#resource-owner)) para outra entidade (tais como o [servidor recursos](#resource-server)). As afirmações são pares nome/valor que factos sobre o assunto de token de reencaminhamento (por exemplo, o principal de segurança que foi autenticado pelo [servidor autorização](#authorization-server)). As afirmações presentes num token fornecido dependem de várias variáveis, incluindo o tipo de token, o tipo de credencial utilizado para autenticar o assunto, a configuração da aplicação, etc.

Consulte [referência de token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>aplicação de cliente
Tal como definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], protegido de uma aplicação que faz com que os pedidos de recursos em nome do [proprietário do recurso](#resource-owner). O termo "cliente" não implica as características de implementação de hardware específico (por exemplo, se a aplicação executa num servidor, um ambiente de trabalho ou outros dispositivos).  

Os pedidos de uma aplicação cliente [autorização](#authorization) de um proprietário de recursos para participar num [conceder de autorização de OAuth2](#authorization-grant) fluxo e pode aceder a APIs/dados em nome do proprietário do recurso. A estrutura de autorização de OAuth2 [define dois tipos de clientes][OAuth2-Client-Types]"Confidencial" e "público", com base na capacidade do cliente a manter a confidencialidade das respetivas credenciais. Podem implementar aplicações um [cliente web (confidencial)](#web-client) que é executada num servidor web, uma [cliente nativo (público)](#native-client) instalada num dispositivo, ou um [baseada no utilizador-agente de cliente (público)](#user-agent-based-client) que é executada no browser do dispositivo.

## <a name="consent"></a>Consentimento
O processo de um [proprietário do recurso](#resource-owner) conceder de autorização para um [aplicação cliente](#client-application), para aceder a recursos protegidos em específicos [permissões](#permissions), em nome do proprietário do recurso. Consoante as permissões solicitadas pelo cliente, um administrador ou o utilizador será pedido para pedir consentimento permitir o acesso aos respetivos dados de organização/individuais, respetivamente. Tenha em atenção num [multi-inquilino](#multi-tenant-application) cenário, a aplicação [principal de serviço](#service-principal-object) também é registada no inquilino do utilizador consenting.

## <a name="id-token"></a>Token de ID
Um [OpenID Connect] [ OpenIDConnect-ID-Token] [token de segurança](#security-token) fornecido por um [do servidor de autorização](#authorization-server) [ponto final de autorização](#authorization-endpoint), que contém [afirmações](#claim) relativas à autenticação de um utilizador final [proprietário do recurso](#resource-owner). Como um token de acesso, os tokens de ID também são representados como um assinados digitalmente [JSON Web tokens (JWT)][JWT]. Ao contrário de um token de acesso, afirmações um token de ID não é utilizado para fins de acesso a recursos e especificamente o controlo de acesso.

Consulte [referência de token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="multi-tenant-application"></a>aplicação multi-inquilino
Uma classe de aplicação que permite início de sessão e [consentimento](#consent) por utilizadores aprovisionados no qualquer Azure AD [inquilino](#tenant), incluindo inquilinos em que o cliente está registado. [O Native client](#native-client) as aplicações são multi-inquilinos por predefinição, ao passo que [cliente web](#web-client) e [recurso/API web](#resource-server) aplicações têm a capacidade de selecionar entre único ou multi-inquilino. Por outro lado, uma aplicação web registada como único-inquilino, só permita inícios de sessão das contas de utilizador aprovisionados no mesmo inquilino como aquele onde a aplicação está registada.

Consulte [como iniciar sessão em qualquer utilizador do Azure AD através do padrão de aplicação multi-inquilino] [ AAD-Multi-Tenant-Overview] para obter mais detalhes.

## <a name="native-client"></a>o Native client
Um tipo de [aplicação cliente](#client-application) que está instalado nativamente num dispositivo. Uma vez que todo o código é executado num dispositivo, considera-se um cliente "público" devido a sua incapacidade de armazenar credenciais em privado/confidentially. Consulte [de perfis e tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>Permissões
A [aplicação cliente](#client-application) obtiver acesso a um [servidor recursos](#resource-server) por declarar pedidos de permissão. Estão disponíveis dois tipos:

* "Delegado" as permissões que especificar [baseado no âmbito](#scopes) aceder ao utilizar o delegado de autorização da sessão iniciada [proprietário do recurso](#resource-owner), são apresentados para o recurso durante a execução como ["scp" afirmações](#claim) no cliente do [token de acesso](#access-token).
* Permissões de "Aplicação", que especificar [baseada em funções](#roles) aceder através credenciais/identidade da aplicação cliente, são apresentados para o recurso durante a execução como [afirmações de "funções"](#claim) no token de acesso do cliente.

Se também de superfície durante o [consentimento](#consent) processo, concedendo, o administrador ou o proprietário do recurso, a oportunidade para conceder/negar o acesso de cliente a recursos no seu inquilino.

Pedidos de permissão estão configurados nas aplicações"" / "Definições" separador o [portal do Azure][AZURE-portal], em "Necessário permissões", selecionando o pretendido "Permissões delegadas" e "Permissões de aplicação" (a última opção requer a associação na função de Administrador Global). Porque um [cliente pública](#client-application) segura não é possível manter as credenciais, apenas pode solicitar as permissões delegadas, enquanto um [cliente confidencial](#client-application) tem capacidade para solicitar permissões de delegado e de aplicações. O cliente [objeto da aplicação](#application-object) armazena as permissões declaradas no respetivo [requiredResourceAccess propriedade][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietário do recurso
Tal como definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], uma entidade com capacidade de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, é referido como um utilizador final. Por exemplo, quando um [aplicação cliente](#client-application) pretende aceder à caixa de correio do utilizador através de [Microsoft Graph API][Microsoft-Graph], necessita de permissão do proprietário do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos
Tal como definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], protegidos de um servidor que anfitriões recursos protegidos, capazes de aceitar e responder a pedidos de recursos por [aplicações cliente](#client-application) que apresente uma [token de acesso](#access-token). Também conhecido como um recurso protegido servidor ou recurso aplicação.

Um servidor de recurso expõe as APIs e impõe o acesso ao respetivos recursos protegidos através de [âmbitos](#scopes) e [funções](#roles), utilizando a estrutura de autorização do OAuth 2.0. Os exemplos incluem o Azure AD Graph API que fornece acesso aos dados de inquilino do Azure AD e as APIs do Office 365 que fornecem acesso aos dados, tais como o correio e o calendário. Estes são também acessíveis através de [Microsoft Graph API][Microsoft-Graph].  

Tal como uma aplicação de cliente, a configuração da identidade da aplicação do recurso é estabelecida através de [registo](#application-registration) no inquilino do Azure AD, fornecendo as aplicações e o objeto principal do serviço. Algumas APIs Microsoft fornecidos pelo, tais como a AD Graph API do Azure, registou previamente disponibilizadas em todos os inquilinos durante o aprovisionamento de principais de serviço.

## <a name="roles"></a>funções
Como [âmbitos](#scopes), funções fornecem uma forma de um [servidor recursos](#resource-server) para controlam o acesso aos respetivos recursos protegidos. Existem dois tipos: uma função de "utilizador" implementa o controlo de acesso baseado em funções para os utilizadores/grupos que necessitam de acesso ao recurso, enquanto que uma função de "aplicação" implementa o mesmo para [aplicações cliente](#client-application) que necessitam de acesso.

As funções são definidas pelo recurso cadeias (por exemplo "evitam o aprovador", "Só de leitura", "Directory.ReadWrite.All"), gerido no [portal do Azure] [ AZURE-portal] através do recurso [manifesto da aplicação](#application-manifest)e armazenados no recurso [appRoles propriedade][AAD-Graph-Sp-Entity]. O portal do Azure também é utilizado para atribuir utilizadores a funções de "utilizador" e configurar o cliente [permissões de aplicação](#permissions) para aceder a uma função de "aplicação".

Para ver um debate detalhado das funções de aplicação exposta pelo Azure AD Graph API, consulte [âmbitos de permissões do API Graph][AAD-Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, consulte [controlo de acesso em aplicações em nuvem com o Azure AD baseado em funções][Duyshant-Role-Blog].

## <a name="scopes"></a>âmbitos
Como [funções](#roles), âmbitos de fornecem uma forma de um [servidor recursos](#resource-server) para controlam o acesso aos respetivos recursos protegidos. Âmbitos são utilizados para implementar [baseado no âmbito] [ OAuth2-Access-Token-Scopes] o controlo de acesso, para um [aplicação cliente](#client-application) que foi fornecido acesso delegado o recurso pelo respetivo proprietário.

Âmbitos são definidos pelo recurso cadeias (por exemplo "Mail.Read", "Directory.ReadWrite.All"), geridos no [portal do Azure] [ AZURE-portal] através do recurso [manifesto da aplicação](#application-manifest)e armazenados no recurso [oauth2Permissions propriedade][AAD-Graph-Sp-Entity]. O portal do Azure também é utilizado para configurar a aplicação de cliente [permissões delegadas](#permissions) para um âmbito de acesso.

Uma convenção de nomenclatura de prática melhor, está a utilizar um formato de "resource.operation.constraint". Para ver um debate detalhado dos âmbitos exposta pelo Azure AD Graph API, consulte [âmbitos de permissões do API Graph][AAD-Graph-Perm-Scopes]. Para âmbitos expostos pelo serviços do Office 365, consulte [referência de permissões do Office 365 API][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança
Um documento assinado que contém as afirmações, tais como um OAuth2 token ou asserção SAML 2.0. Para um OAuth2 [conceder de autorização](#authorization-grant), um [token de acesso](#access-token) (OAuth2) e um [ID Token](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) são tipos de tokens de segurança, que são implementados como um [JSON Web tokens (JWT)][JWT].

## <a name="service-principal-object"></a>Objeto principal do serviço
Quando lhe registe/atualizar uma aplicação no [portal do Azure][AZURE-portal], o portal cria/atualizações ambos um [objeto da aplicação](#application-object) e um objeto principal de serviço correspondentes para esse inquilino. O objeto de aplicação *define* configuração da identidade da aplicação global (em todos os inquilinos onde a aplicação associada foi concedida acesso), e é o modelo a partir dos quais são os objetos de principal de serviço correspondente *derivada* para utilização localmente em tempo de execução (de um inquilino específico).

Consulte [aplicação e objetos de principais de serviço] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="sign-in"></a>Início de sessão
O processo de um [aplicação cliente](#client-application) iniciar autenticação de utilizador final e capturar relacionados com o estado, para fins de adquirir uma [token de segurança](#security-token) e controlo de âmbito de sessão de aplicação para esse Estado. Pode incluir artefactos, tais como informações de perfil de utilizador e informações derivam do token afirmações de estado.

A função de início de sessão de uma aplicação é normalmente utilizada para implementar o single-sign-on (SSO). -Pode também ser precedido por uma função de "inscrição", como o ponto de entrada para um utilizador final obter acesso a uma aplicação (após a primeira início de sessão). A função de inscrição é utilizada para recolher e manter o estado adicionais específico para o utilizador e pode necessitar de [consentimento do utilizador](#consent).

## <a name="sign-out"></a>fim de sessão
O processo de autenticação não associados a um utilizador final, desanexar o estado do utilizador com o [aplicação cliente](#client-application) sessão durante [início de sessão](#sign-in)

## <a name="tenant"></a>Inquilino
Uma instância de um diretório do Azure AD é referida como um inquilino do Azure AD. Fornece várias funcionalidades, incluindo:

* um serviço de registo para aplicações integradas
* autenticação de contas de utilizador e aplicações registadas
* Pontos finais REST necessários para suportar vários protocolos, incluindo o OAuth2 e SAML, incluindo o [ponto final de autorização](#authorization-endpoint), [ponto final de tokens](#token-endpoint) e o ponto final "comuns" utilizado pelo [aplicações multi-inquilino](#multi-tenant-application).

Inquilinos do Azure AD são criados/associada ao subscrições do Azure e o Office 365 durante a inscrição, fornecer serviços de identidade e gestão de acesso funcionalidades para a subscrição. Os administradores da subscrição do Azure também podem criar adicionais inquilinos do Azure AD através do portal do Azure. Consulte [como obter um inquilino do Azure Active Directory] [ AAD-How-To-Tenant] para obter detalhes sobre as várias formas poder obter acesso a um inquilino. Consulte [subscrições do Azure como estão associadas ao Azure Active Directory] [ AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre subscrições e um inquilino do Azure AD.

## <a name="token-endpoint"></a>ponto final de tokens
Um dos pontos finais implementados pelo [servidor autorização](#authorization-server) para suportar o OAuth2 [concede autorização](#authorization-grant). Consoante a concessão, podem ser utilizado para adquirir um [token de acesso](#access-token) (e de token 'atualizar' relacionados) para um [cliente](#client-application), ou [ID token](#ID-token) quando utilizado com o [OpenID Connect] [ OpenIDConnect] protocolo.

## <a name="user-agent-based-client"></a>Utilizador agente baseada em cliente
Um tipo de [aplicação cliente](#client-application) que transfere o código de um servidor web e executa dentro de um agente de utilizador (por exemplo, um web browser), tal como uma única página aplicação (SPA). Uma vez que todo o código é executado num dispositivo, considera-se um cliente "público" devido a sua incapacidade de armazenar credenciais em privado/confidentially. Consulte [de perfis e tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="user-principal"></a>principal de utilizador
A objeto principal do serviço é utilizado para representar uma instância de aplicação de forma semelhante, um objeto principal de utilizador é outro tipo de principal de segurança, que representa um utilizador. O Azure AD Graph [entidade utilizador] [ AAD-Graph-User-Entity] define o esquema para um objeto de utilizador, incluindo propriedades relacionadas com o utilizador, tais como o nome próprio e apelido, nome principal de utilizador, membro da função de diretório, etc. Isto fornece a configuração de identidade de utilizador para o Azure AD estabelecer um principal de utilizador em tempo de execução. O principal de utilizador é utilizado para representar um utilizador autenticado para Single Sign-On, gravar [consentimento](#consent) delegação, tomar decisões de controlo de acesso, etc.

## <a name="web-client"></a>cliente Web
Um tipo de [aplicação cliente](#client-application) que executa a todo o código num servidor web e capacidade para funcionar como um cliente "Confidencial" por armazenar as respetivas credenciais de forma segura no servidor. Consulte [de perfis e tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
O [Guia do programador do Azure AD] [ AAD-Dev-Guide] é a página de destino a utilizar para todos os desenvolvimento do Azure AD relacionadas com tópicos, incluindo uma descrição geral do [integração de aplicações] [ AAD-How-To-Integrate] e as noções básicas do [autenticação do Azure AD e cenários de autenticação suportados][AAD-Auth-Scenarios].  Também pode encontrar exemplos de código & tutoriais sobre como obter rapidamente e em execução no [Github](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo, incluindo pedidos de existência de novas definições ou a atualização já existentes, utilize a seguinte secção de comentários!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ../active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
