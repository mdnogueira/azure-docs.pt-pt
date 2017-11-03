---
title: "Cenários de autenticação para o Azure AD | Microsoft Docs"
description: "Uma descrição geral das cinco cenários mais comuns de autenticação para o Azure Active Directory (AAD)"
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: 2f9410bdaa037f1839cf7c12c3532b51be669ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-scenarios-for-azure-ad"></a>Cenários de autenticação do Azure AD
Azure Active Directory (Azure AD) simplifica a autenticação para os programadores, fornecendo identidade como uma origem de serviço, com suporte para protocolos de norma da indústria, como o OAuth 2.0 e o OpenID Connect, bem como abrir bibliotecas para plataformas diferentes para o ajudar a Inicie a codificação rapidamente. Este documento ajuda-o a compreender o várias suporta cenários do Azure AD e irá mostrar como começar a utilizar. É dividido em secções seguintes:

* [Noções básicas da autenticação no Azure AD](#basics-of-authentication-in-azure-ad)
* [Afirmações em Tokens de segurança do Azure AD](#claims-in-azure-ad-security-tokens)
* [Noções básicas de registar uma aplicação no Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Cenários e tipos de aplicações](#application-types-and-scenarios)
  
  * [Browser para a aplicação Web](#web-browser-to-web-application)
  * [Aplicação de página única (SPA)](#single-page-application-spa)
  * [Aplicação nativa a API Web](#native-application-to-web-api)
  * [Web API da aplicação Web](#web-application-to-web-api)
  * [O daemon ou a aplicação de servidor para a API Web](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Noções básicas da autenticação no Azure AD
Se não estiver familiarizado com conceitos básicos de autenticação no Azure AD, leia esta secção. Caso contrário, pode optar por ignorar até [cenários e tipos de aplicação](#application-types-and-scenarios).

Vamos, considere o cenário mais básico, onde é necessária a identidade: necessita de um utilizador num web browser autenticar a uma aplicação web. Este cenário é descrito com maior detalhe no [Browser para a aplicação Web](#web-browser-to-web-application) secção, mas do ponto de partida útil para ilustrar as capacidades do Azure AD e conceptualize como funciona o cenário. Considere o seguinte diagrama para este cenário:

![Descrição geral do início de sessão à aplicação web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Com o diagrama acima em mente, eis o que precisa de saber sobre os diversos componentes:

* Azure AD é o fornecedor de identidade, responsável por verificar a identidade de utilizadores e aplicações que existe no diretório da organização e, em última análise emitir tokens de segurança após a autenticação com êxito desses utilizadores e aplicações.
* Uma aplicação que pretende outsource autenticação para o Azure AD tem de estar registada no Azure AD, que regista e identifica de forma a aplicação no diretório.
* Os programadores podem utilizar as bibliotecas de autenticação de código aberto do Azure AD para facilitar a autenticação, processando os detalhes de protocolo por si. Consulte [bibliotecas de autenticação do Azure Active Directory](active-directory-authentication-libraries.md) para obter mais informações.

• Assim que um utilizador ser autenticado, a aplicação necessário validar o token de segurança do utilizador para se certificar de que a autenticação foi efetuada com êxito para as partes pretendidas. Os programadores podem utilizar as bibliotecas de autenticação fornecido para processar a validação de qualquer token do Azure AD, incluindo JSON Web Tokens (JWT) ou SAML 2.0. Se pretender efetuar manualmente a validação, consulte o [processador Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) documentação.

> [!IMPORTANT]
> Azure AD utiliza a criptografia de chave pública para assinar os tokens e certifique-se de que são válidas. Consulte [importantes informações sobre como assinar Rollover de chave no Azure AD](active-directory-signing-key-rollover.md) para obter mais informações sobre a lógica necessária às tem de ter na sua aplicação para se certificar de que é sempre atualizado com as chaves mais recentes.
> 
> 

• O fluxo de pedidos e respostas para o processo de autenticação é determinado pelo protocolo de autenticação que foi utilizado, tais como o OAuth 2.0, OpenID Connect, WS-Federation ou SAML 2.0. Estes protocolos são abordados mais detalhadamente no [protocolos de autenticação do Azure Active Directory](active-directory-authentication-protocols.md) tópico e nas secções abaixo.

> [!NOTE]
> Azure AD suporta o OAuth 2.0 e normas OpenID Connect que tornam mais extensas a utilização de tokens de portador, incluindo os tokens de portador representados como JWTs. Um token de portador é um token de segurança simples que concede acesso a "portador" para um recurso protegido. Este sentido, "portador" é que podem apresentar o token de terceiros. Embora um terceiros devem primeiro autenticar com o Azure AD para receber o token de portador, se não são tidas nos passos necessários para proteger o token na transmissão e o armazenamento, podem ser intercetado e utilizado por uma entidade indesejada. Enquanto algumas tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas a utilizá-los, os tokens de portador não têm este mecanismo e tem de ser transportados num canal seguro, como de segurança de camada de transporte (HTTPS). Se um token de portador é transmitido na limpar, um ataques man-in média ataque pode ser utilizado por uma entidade maliciosa para adquirir o token e utilizá-lo para um acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando armazenar ou colocação em cache os tokens de portador para utilização posterior. Certifique-se sempre de que a sua aplicação transmite e armazena os tokens de portador de forma segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).
> 
> 

Agora que tem uma descrição geral sobre as noções básicas, leia as secções abaixo para compreender como aprovisionamento funciona no Azure AD e suporta os cenários comuns do Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Afirmações em Tokens de segurança do Azure AD
Os tokens de segurança emitidos pelo Azure AD contêm afirmações ou asserções de informações sobre o assunto que tenha sido autenticado. Estas afirmações podem ser utilizadas pela aplicação de várias tarefas. Por exemplo, pode ser utilizados para validar o token, identificar o inquilino de diretório do requerente, apresentar informações de utilizador, determinar autorização do requerente e assim sucessivamente. As afirmações presentes nenhum token de segurança especificado de dependem do tipo de token, o tipo de credencial utilizada para autenticar o utilizador e a configuração da aplicação. É apresentada uma descrição breve de cada tipo de afirmação emitido pelo Azure AD na tabela abaixo. Para obter mais informações, consulte [suportado Token e tipos de afirmação](active-directory-token-and-claims.md).

| Afirmação | Descrição |
| --- | --- |
| ID da aplicação |Identifica a aplicação que está a utilizar o token. |
| Audiência |Identifica o recurso de destinatário que destina-se o token. |
| Referência de classe de contexto de autenticação de aplicação |Indica como o cliente foi autenticado (cliente público vs cliente confidencial). |
| Autenticação instantânea |Regista a data e hora quando a autenticação ocorreu. |
| Método de Autenticação |Indica como o assunto do token foi autenticado (palavra-passe, certificado, etc.). |
| Nome Próprio |Fornece o nome dado do utilizador conforme definido no Azure AD. |
| Grupos |Contém os grupos de Ids do Azure AD de objeto que o utilizador é membro. |
| Fornecedor de Identidade |Regista o fornecedor de identidade autenticado o assunto do token. |
| Emitido no |Regista o tempo no qual o token foi emitido, utilizado frequentemente para actualização token. |
| emissor |Identifica o STS que emitidos o token, bem como o inquilino do Azure AD. |
| Apelido |Fornece o apelido do utilizador conforme definido no Azure AD. |
| Nome |Fornece um valor de legível humano que identifica o assunto do token. |
| Id de objeto |Contém um identificador exclusivo, imutável do assunto no Azure AD. |
| Funções |Contém nomes amigáveis de funções da aplicação AD do Azure que tenha sido concedido ao utilizador. |
| Âmbito |Indica as permissões concedidas à aplicação de cliente. |
| Assunto |Indica o principal sobre os quais o token de asserções informações. |
| Id do inquilino |Contém um identificador exclusivo, imutável do inquilino de diretório que emitiu o token. |
| Duração do token |Define o intervalo de tempo no qual um token é válido. |
| Nome Principal de utilizador |Contém o nome principal de utilizador do assunto. |
| Versão |Contém o número de versão do token. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Noções básicas de registar uma aplicação no Azure AD
Qualquer aplicação que outsources autenticação para o Azure AD tem de estar registada num diretório. Este passo envolve do Azure AD a informar sobre a sua aplicação, incluindo o URL onde tem localizados, o URL para enviar as respostas após a autenticação, o URI para identificar a sua aplicação e muito mais. Esta informação é necessária para algumas razões chaves:

* Azure AD tem de coordenadas para comunicar com a aplicação quando o processamento de início de sessão ou trocar tokens. Estes incluem o seguinte:
  
  * URI de ID de aplicação: O identificador de uma aplicação. Este valor é enviado para o Azure AD durante a autenticação para indicar que aplicação um token para quer que o autor da chamada. Além disso, este valor está incluído no token, para que a aplicação sabe era o destino pretendido.
  * URL e o URI de redirecionamento de resposta: no caso de uma web API ou aplicação web, o URL de resposta é a localização para os quais do Azure AD irá enviar a resposta de autenticação, incluindo um token, se a autenticação foi efetuada com êxito. No caso de uma aplicação nativa, o URI de redirecionamento é um identificador exclusivo para o qual do Azure AD irá redirecionar o agente de utilizador num pedido de OAuth 2.0.
  * ID da aplicação: O ID de uma aplicação, o que é gerado pelo Azure AD quando a aplicação está registada. Quando pedir um código de autorização ou token, o ID da aplicação e a chave são enviadas para o Azure AD durante a autenticação.
  * : A chave que é enviada juntamente com um ID de aplicação durante a autenticação para o Azure AD para chamar uma API web.
* Precisa do Azure AD garantir que a aplicação tem as permissões necessárias para aceder aos seus dados de diretório, outras aplicações na sua organização e assim sucessivamente

Aprovisionamento torna-se mais clara quando compreender que existem duas categorias de aplicações que podem ser desenvolvidas e integradas com o Azure AD:

* Única aplicação do inquilino: uma aplicação de inquilino único destina-se de uma organização. Estas são, normalmente, linha de negócio (LoB) aplicações escritas por um programador de empresa. Uma aplicação de inquilino único só tem de ser acedidos por utilizadores num diretório e, como resultado, só tem de ser aprovisionados num diretório. Normalmente, estas aplicações estão registadas por um programador na organização.
* Aplicação multi-inquilino: uma aplicação multi-inquilino destina-se em muitas organizações, não apenas uma organização. Estas são normalmente software-como-um-serviço (SaaS) aplicações escritas por um fabricante independente de software (ISV). Aplicações de multi-inquilinos tem de ser aprovisionado no cada diretório onde que serão utilizados, que requer o consentimento do utilizador ou administrador registá-los. Este processo de consentimento começa quando uma aplicação registada no diretório e é dado acesso para a Graph API ou talvez outro web API. Quando um utilizador ou o administrador de uma organização diferente se inscreve para utilizar a aplicação, são apresentados com uma caixa de diálogo que mostra as permissões que a aplicação requer. O utilizador ou administrador pode, em seguida, autorizar a aplicação, o que permite o acesso de aplicação para os dados declarados e, finalmente, regista a aplicação no seu diretório. Para obter mais informações, consulte [descrição geral do Framework consentimento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Algumas considerações adicionais surgem quando desenvolver uma aplicação multi-inquilino, em vez de uma aplicação de inquilino único. Por exemplo, se estiver a efetuar a aplicação disponível a utilizadores em vários diretórios, terá de um mecanismo para determinar qual inquilino estão contidos na. Uma aplicação de inquilino único apenas tem de ter um aspeto no seu próprio diretório para um utilizador, enquanto uma aplicação multi-inquilino tem de identificar um utilizador específico de todos os diretórios no Azure AD. Para realizar esta tarefa, o Azure AD fornece um ponto final autenticação comuns em que qualquer aplicação multi-inquilino pode direcionar o início de sessão pedidos, em vez de um ponto final de inquilino específico. Este ponto final é https://login.microsoftonline.com/common para todos os diretórios no Azure AD, enquanto que um ponto final de inquilino específico poderá ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto final comum é especialmente importante a considerar quando desenvolver a sua aplicação, porque terá da lógica necessária para lidar com vários inquilinos durante o início de sessão, fim de sessão e validação de token.

Se tiver atualmente estiver a desenvolver uma aplicação de inquilino único, mas pretende disponibilizar para muitas organizações, pode facilmente efetuar alterações para a aplicação e a respetiva configuração no Azure AD para que a multi-inquilino com capacidade. Além disso, o Azure AD utiliza a mesma chave de assinatura para todos os tokens em todos os diretórios, se está a fornecer autenticação numa aplicação multi-inquilino ou único inquilino.

Cada cenário listado neste documento inclui uma secção secundárias que descreve os requisitos de aprovisionamento. Para obter informações mais aprofundadas sobre o aprovisionamento de uma aplicação no Azure AD e as diferenças entre as aplicações único e multi-inquilinos, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md) para obter mais informações. Continue a ler para compreender os cenários comuns de aplicação no Azure AD.

## <a name="application-types-and-scenarios"></a>Cenários e tipos de aplicações
Cada um dos cenários descritos neste documento pode ser desenvolvida utilizando vários idiomas e plataformas. Todos os são apoiados por amostras de código completo que estão disponíveis no nosso [exemplos de código guia](active-directory-code-samples.md), ou diretamente a partir do correspondente [repositórios do GitHub exemplo](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Além disso, se a sua aplicação precisar de uma parte específica ou um segmento de um cenário ponto-a-ponto, na maioria dos casos essa funcionalidade pode ser adicionada independentemente. Por exemplo, se tiver uma aplicação nativa que chama uma API web, pode facilmente adicionar uma aplicação web que também chama a API web. O diagrama seguinte ilustra estes cenários e tipos de aplicações, e a forma como os diferentes componentes podem ser adicionados:

![Cenários e tipos de aplicações](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estes são os cenários de principal da aplicação cinco suportados pelo Azure AD:

* [Web Browser para a aplicação Web](#web-browser-to-web-application): um utilizador tem de iniciar sessão para uma aplicação web que está protegida pelo Azure AD.
* [A única página aplicação (SPA)](#single-page-application-spa): um utilizador tem de iniciar sessão para uma aplicação de página única que esteja protegida pelo Azure AD.
* [Aplicação nativa à Web API](#native-application-to-web-api): uma aplicação nativa que é executada num telemóvel, tablet ou PC tem de autenticar um utilizador para obter recursos de uma API web que está protegida pelo Azure AD.
* [Aplicação Web API Web](#web-application-to-web-api): uma aplicação web precisa de obter recursos a partir de um APIS da web protegidas pelo Azure AD.
* [O daemon ou aplicações de servidor para a Web API](#daemon-or-server-application-to-web-api): uma aplicação do daemon ou uma aplicação de servidor sem interface de utilizador web tem de obter recursos de uma API web do protegida pelo Azure AD.

### <a name="web-browser-to-web-application"></a>Browser para a aplicação Web
Esta secção descreve uma aplicação que efetua a autenticação de um utilizador num web browser para uma aplicação web. Neste cenário, a aplicação web direciona o browser do utilizador para iniciar sessão-los para o Azure AD. Azure AD devolve uma resposta de início de sessão através do browser do utilizador, que contém as afirmações sobre o utilizador no token de segurança. Este cenário suporta início de sessão utilizando os protocolos de WS-Federation, SAML 2.0 e OpenID Connect.

#### <a name="diagram"></a>Diagrama
![Fluxo de autenticação para o browser para a aplicação web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
1. Quando um utilizador aceder a aplicações e tem de iniciar sessão, será redirecionado através de um pedido de início de sessão para o ponto final de autenticação no Azure AD.
2. O utilizador inicia sessão na página de início de sessão.
3. Se a autenticação for bem-sucedida, o Azure AD cria um token de autenticação e devolve uma resposta de início de sessão para o URL de resposta da aplicação que foi configurada no Portal do Azure. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui afirmações sobre o utilizador e o Azure AD que são necessárias para a aplicação para validar o token.
4. A aplicação valida o token utilizando uma chave pública de assinatura e informações disponíveis sobre emissor no documento de metadados de Federação para o Azure AD. Depois da aplicação valida o token, o Azure AD inicia uma nova sessão com o utilizador. Esta sessão permite ao utilizador aceder à aplicação até expirar.

#### <a name="code-samples"></a>Exemplos de Código
Consulte os exemplos de código para o Web Browser para cenários de aplicação Web. E, verifique frequentemente – adicionamos amostras novo sempre. [Web Browser para a aplicação Web](active-directory-code-samples.md#web-browser-to-web-application).

#### <a name="registering"></a>A registar
* Inquilino único: Se está a criar uma aplicação apenas para a sua organização, tem de ser registado no diretório da sua empresa utilizando o Portal do Azure.
* Multi-inquilino: Se está a criar uma aplicação que pode ser utilizada por utilizadores fora da sua organização, este tem de estar registado no diretório da sua empresa, mas também têm de estar registado no diretório de cada organização que irá utilizar a aplicação. Para disponibilizar a aplicação no seu diretório, pode incluir um processo de inscrição para os seus clientes, que permite-lhes autorizar a aplicação. Quando iniciam sessão para a sua aplicação, serão apresentadas com uma caixa de diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção de consentimento. Consoante as permissões necessárias, poderá ser necessário um administrador na outra organização para dar consentimento. Quando o utilizador ou administrador permitir, a aplicação é registada no seu diretório. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
A sessão do utilizador expira quando expira a duração do token emitido pelo Azure AD. A aplicação pode abreviar neste período de tempo, se assim o desejar, tais como assinatura de utilizadores com base nas durante um período de inatividade. Quando a sessão expira, será pedido ao utilizador para iniciar sessão novamente.

### <a name="single-page-application-spa"></a>Aplicação de página única (SPA)
Esta secção descreve a autenticação para uma aplicação de página única, que utiliza o Azure AD e a autorização implícita de OAuth 2.0 concedem a proteger o web API novamente terminar. Aplicações de página única, normalmente, são estruturadas como uma camada de apresentação de JavaScript (front-end) que é executado no browser e uma Web API back-end que é executado num servidor e implementa lógica de negócio da aplicação. Para obter mais informações sobre a concessão de autorização implícita e ajudar a decidir se é adequada para o seu cenário de aplicação, consulte [fluxo no Azure Active Directory de concessão de compreender o OAuth2 implícita](active-directory-dev-understanding-oauth2-implicit-grant.md).

Neste cenário, quando o utilizador inicia sessão, o JavaScript FrontPage final utiliza [Active Directory Authentication Library para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e a concessão de autorização implícita para obter um token de ID (id_token) do Azure AD. O token é colocado em cache e o cliente liga-lo ao pedido como o token de portador quando efetuar chamadas à sua API Web back-end, o que está protegida utilizando o middleware OWIN. 

#### <a name="diagram"></a>Diagrama
![Diagrama de aplicação de página único](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
1. O utilizador navega para a aplicação web.
2. A aplicação devolve JavaScript front-end (camada de apresentação) no browser.
3. O utilizador inicia sessão, por exemplo clicando na ligação de sessão. O browser envia um GET para o ponto final de autorização do Azure AD para pedir um token de ID. Este pedido inclui o URL de ID e a resposta da aplicação nos parâmetros de consulta.
4. Azure AD valida o URL de resposta relativamente ao URL de resposta registado que foi configurada no Portal do Azure.
5. O utilizador inicia sessão na página de início de sessão.
6. Se a autenticação for bem-sucedida, o Azure AD cria um token de ID e devolve-lo como um fragmento de URL (#) ao URL de resposta da aplicação. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui afirmações sobre o utilizador e o Azure AD que são necessárias para a aplicação para validar o token.
7. O código de cliente JavaScript em execução no browser extrai o token da resposta a utilizar a proteger as chamadas para web da aplicação que API novamente termina.
8. O browser chama web da aplicação API novamente terminar com o token de acesso no cabeçalho de autorização.

#### <a name="code-samples"></a>Exemplos de Código
Consulte os exemplos de código para cenários de aplicação de página única (SPA). Certifique-se verifique frequentemente – adicionamos amostras novo sempre. [Única (SPA) da aplicação de página](active-directory-code-samples.md#single-page-application-spa).

#### <a name="registering"></a>A registar
* Inquilino único: Se está a criar uma aplicação apenas para a sua organização, tem de ser registado no diretório da sua empresa utilizando o Portal do Azure.
* Multi-inquilino: Se está a criar uma aplicação que pode ser utilizada por utilizadores fora da sua organização, este tem de estar registado no diretório da sua empresa, mas também têm de estar registado no diretório de cada organização que irá utilizar a aplicação. Para disponibilizar a aplicação no seu diretório, pode incluir um processo de inscrição para os seus clientes, que permite-lhes autorizar a aplicação. Quando iniciam sessão para a sua aplicação, serão apresentadas com uma caixa de diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção de consentimento. Consoante as permissões necessárias, poderá ser necessário um administrador na outra organização para dar consentimento. Quando o utilizador ou administrador permitir, a aplicação é registada no seu diretório. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md).

Depois de registar a aplicação, tem de ser configurado para utilizar o protocolo de concessão implícita do OAuth 2.0. Por predefinição, este protocolo está desativado para aplicações. Para ativar o protocolo de concessão implícita OAuth2 para a sua aplicação, edite o manifesto da aplicação do Portal do Azure e defina o valor de "oauth2AllowImplicitFlow" como verdadeiro. Para obter instruções detalhadas, consulte [ativar OAuth 2.0 concessão implícita para aplicações de página única](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
Quando utilizar ADAL.js para gerir a autenticação com o Azure AD, poderá beneficiar das várias funcionalidades que o facilitam a atualizar um token de expirada, bem como obter os tokens de recursos de API web adicionais que podem ser chamados pela aplicação. Quando o utilizador autentica com êxito com o Azure AD, é estabelecida uma sessão protegida por um cookie para o utilizador entre o browser e o Azure AD. É importante ter em atenção que a sessão existe entre o utilizador e o Azure AD e não entre o utilizador e a aplicação web em execução no servidor. Quando um token expira, ADAL.js utiliza esta sessão silenciosamente obter outro token. Fazê-lo ao utilizando um iFrame oculta para enviar e receber o pedido utilizando o protocolo de concessão implícita de OAuth. ADAL.js também pode utilizar este mesmo mecanismo para obter automaticamente os tokens de acesso do Azure AD para outros recursos de API web se as chamadas de aplicação, desde que os recursos de várias origens (CORS), de partilha de suportar estes recursos estão registadas no diretório do utilizador e quaisquer necessário consentimento fornecido pelo utilizador durante o início de sessão.

### <a name="native-application-to-web-api"></a>Aplicação nativa a API Web
Esta secção descreve uma aplicação nativa que chama uma API web em nome de um utilizador. Este cenário baseia-se no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na secção 4.1 o [especificação de OAuth 2.0](http://tools.ietf.org/html/rfc6749). A aplicação nativa obtém um token de acesso para o utilizador utilizando o protocolo OAuth 2.0. Este token de acesso, em seguida, é enviado no pedido para o web API, o que permitirá posteriormente ao utilizador e devolve o recurso pretendido.

#### <a name="diagram"></a>Diagrama
![Aplicação nativa para Web API diagrama](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Fluxo de autenticação para a aplicação nativa a API
#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
Se estiver a utilizar as bibliotecas de autenticação do AD, a maioria dos detalhes de protocolo descritos abaixo é tratada por si, tais como o pop-up do browser, colocação em cache de token e processamento de atualização de tokens.

1. Utilizando um browser de pop-up, que a aplicação nativa faz um pedido para o ponto final de autorização no Azure AD. Este pedido inclui o ID da aplicação e o URI da aplicação nativa de redirecionamento, conforme apresentado no Portal do Azure e o URI de ID da aplicação para a API web. Se o utilizador ainda não já iniciou sessão, são-lhe pedido que inicie sessão novamente
2. O Azure AD autentica o utilizador. Se for uma aplicação multi-inquilino e consentimento é necessário para utilizar a aplicação, o utilizador será necessário consentimento se estes ainda não o tiver feito. Depois de conceder permissão e após a autenticação com êxito, o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento da aplicação cliente.
3. Quando o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento, a aplicação cliente deixa de interação do browser e extrai o código de autorização da resposta. Utilizar este código de autorização, a aplicação de cliente envia um pedido para ponto final token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso pretendido (URI de ID da aplicação para o Web API).
4. As informações sobre a API web e aplicações de cliente e o código de autorização são validadas pelo Azure AD. Após a validação com êxito, do Azure AD volta duas tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD devolve informações básicas sobre o utilizador, tais como os respetivos ID de inquilino e nome de apresentação
5. Através de HTTPS, a aplicação de cliente utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.
6. Quando o token de acesso expira, a aplicação cliente irá receber um erro que indica que o utilizador tem de autenticar novamente. Se a aplicação tem um token de atualização válido, pode ser utilizado para adquirir um novo token de acesso sem pedir intervenção do utilizador para iniciar sessão novamente. Se o token de atualização expirar, a aplicação terá de forma interativa autenticar o utilizador novamente.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser utilizado para aceder a vários recursos. Por exemplo, se tiver uma aplicação de cliente que tenha permissão para chamar as APIs da web dois, o token de atualização pode ser utilizado para obter o token para outro web API, bem como de acesso.
> 
> 

#### <a name="code-samples"></a>Exemplos de Código
Consulte os exemplos de código de aplicação nativa para cenários de Web API. E, verifique frequentemente – adicionamos amostras novo sempre. [Aplicação nativa para Web API](active-directory-code-samples.md#native-application-to-web-api).

#### <a name="registering"></a>A registar
* Inquilino único: Ambas o nativo aplicação e a API web tem de estar registados no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso da aplicação nativa aos respetivos recursos. A aplicação de cliente, em seguida, seleciona as permissões pretendidas no menu pendente "Permissões para outras aplicações" no Portal do Azure.
* Multi-inquilino: Em primeiro lugar, a aplicação nativa só alguma vez registado no programador ou diretório do publicador. Segundo, a aplicação nativa está configurada para indicar as permissões necessárias para ser funcional. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino fornece o consentimento para a aplicação, o que torna a mesma disponível para a respetiva organização. Algumas aplicações necessitam de permissões de nível de utilizador apenas, o que qualquer utilizador na organização pode autorizar. Outras aplicações requerem permissões ao nível do administrador, o que não é possível autorizar um utilizador na organização. Apenas um administrador da directory pode dar consentimento para aplicações que requerem este nível de permissões. Quando o utilizador ou administrador permitir, apenas a API de web é registado no seu diretório. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
Quando a aplicação nativa utiliza o código de autorização ao obter o token de acesso um JWT, também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser utilizado para autenticar novamente o utilizador sem necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

### <a name="web-application-to-web-api"></a>Web API da aplicação Web
Esta secção descreve uma aplicação web que precisa de obter recursos de uma API web. Neste cenário, existem dois tipos de identidade que a aplicação web pode utilizar para autenticar e chamar a API web: uma identidade de aplicação ou uma identidade de utilizador de delegado.

*A identidade da aplicação:* este cenário utiliza a concessão de credenciais de cliente OAuth 2.0 para efetuar a autenticação como a aplicação e aceder a API web. Quando utilizar uma identidade de aplicação, o web API só pode detetar que a aplicação web está a chamar, como o web API não receber quaisquer informações sobre o utilizador. Se a aplicação recebe informações sobre o utilizador, será enviado através do protocolo de aplicação e não está assinado pelo Azure AD. A API web confianças de entidades que a aplicação web autenticar o utilizador. Por este motivo, este padrão é chamado um subsistema fidedigno.

*Delegado a identidade do utilizador:* este cenário pode ser conseguido de duas formas: OpenID Connect e OAuth 2.0 concessão do código de autorização com um cliente confidencial. A aplicação web obtém um token de acesso para o utilizador, que comprova a web API que o utilizador autenticado com êxito para a aplicação web e de que a aplicação web foi capaz de obter uma identidade de utilizador de delegado para chamar a API web. Este token de acesso é enviado no pedido para o web API, o que permitirá posteriormente ao utilizador e devolve o recurso pretendido.

#### <a name="diagram"></a>Diagrama
![Diagrama de Web API da aplicação Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
A identidade da aplicação e os tipos de identidade do utilizador delegado são debatidos no fluxo de abaixo. A principal diferença entre eles é que a identidade do utilizador delegado tem primeiro de adquirir um código de autorização antes do utilizador pode iniciar sessão e obter acesso a web API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Concessão de credenciais de identidade da aplicação com o cliente de OAuth 2.0
1. Um utilizador tem sessão iniciado para o Azure AD na aplicação web (consulte o [Browser para a aplicação Web](#web-browser-to-web-application) acima).
2. A aplicação web tem de adquirir um token de acesso para que possa autenticar para a API web e obter o recurso pretendido. Faz um pedido para ponto final de token do Azure AD, fornecendo a credencial, o ID da aplicação e o URI de ID da aplicação de web da API.
3. Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para chamar a API web.
4. Através de HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.

##### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de utilizadores delegados com OpenID Connect
1. Um utilizador tem sessão iniciado para uma aplicação web através do Azure AD (consulte o [Browser para a aplicação Web](#web-browser-to-web-application) secção acima). Se o utilizador da aplicação web não consentiu ainda para permitir que a aplicação web chamar a API web em seu nome, o utilizador terá de consentimento. A aplicação irá apresentar as permissões requer e, se qualquer uma das seguintes permissões ao nível do administrador, um utilizador normal no diretório não será possível consentimento. Este processo de autorização apenas se aplica a aplicações de multi-inquilinos, aplicações de inquilino não único, como a aplicação já tenha as permissões necessárias. Quando o utilizador iniciou sessão, a aplicação web foi recebido um token de ID com informações sobre o utilizador, bem como um código de autorização.
2. Utilizar o código de autorização emitido pelo Azure AD, a aplicação web envia um pedido para ponto final de token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso pretendido (ID da aplicação URI para a API web).
3. O código de autorização e informações sobre a aplicação web e web API são validadas pelo Azure AD. Após a validação com êxito, do Azure AD volta duas tokens: um token de acesso JWT e um token de atualização JWT.
4. Através de HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade do utilizador delegados com a concessão do código de autorização OAuth 2.0
1. Um utilizador já tem sessão iniciado aplicação web, cujo mecanismo de autenticação é independente do Azure AD.
2. A aplicação web requer um código de autorização para adquirir um token de acesso, para que emite um pedido através do browser para o ponto final de autorização do Azure AD, fornecendo o ID da aplicação e URI de redirecionamento para a aplicação web após a autenticação com êxito. O utilizador inicia sessão com o Azure AD.
3. Se o utilizador da aplicação web não consentiu ainda para permitir que a aplicação web chamar a API web em seu nome, o utilizador terá de consentimento. A aplicação irá apresentar as permissões requer e, se qualquer uma das seguintes permissões ao nível do administrador, um utilizador normal no diretório não será possível consentimento. Este consentimento aplica-se a aplicação único e multi-inquilino.  No caso de inquilino único, um administrador pode realizar admin consentimento para consentimento em nome de utilizadores.  Isto pode ser feito utilizando o `Grant Permissions` clique no botão no [Portal do Azure](https://portal.azure.com). 
4. Depois do utilizador consentiu, a aplicação web recebe o código de autorização que necessita de adquirir um token de acesso.
5. Utilizar o código de autorização emitido pelo Azure AD, a aplicação web envia um pedido para ponto final de token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso pretendido (ID da aplicação URI para a API web).
6. O código de autorização e informações sobre a aplicação web e web API são validadas pelo Azure AD. Após a validação com êxito, do Azure AD volta duas tokens: um token de acesso JWT e um token de atualização JWT.
7. Através de HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.

#### <a name="code-samples"></a>Exemplos de Código
Consulte os exemplos de código para cenários de Web API da aplicação Web. E, verifique frequentemente – adicionamos amostras novo sempre. Web [aplicação Web API](active-directory-code-samples.md#web-application-to-web-api).

#### <a name="registering"></a>A registar
* Inquilino único: Para a identidade da aplicação e casos de identidade do delegado de utilizador, a aplicação web e a API web tem de estar registado no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso da aplicação web para os respetivos recursos. Se está a ser utilizado um tipo de identidade do delegado de utilizador, a aplicação web tem de selecionar as permissões pretendidas no menu pendente "Permissões para outras aplicações" no Portal do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.
* Multi-inquilino: Em primeiro lugar, a aplicação web está configurada para indicar as permissões necessárias para ser funcional. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino fornece o consentimento para a aplicação, o que torna a mesma disponível para a respetiva organização. Algumas aplicações necessitam de permissões de nível de utilizador apenas, o que qualquer utilizador na organização pode autorizar. Outras aplicações requerem permissões ao nível do administrador, o que não é possível autorizar um utilizador na organização. Apenas um administrador da directory pode dar consentimento para aplicações que requerem este nível de permissões. Quando o utilizador ou administrador permitir, a aplicação web e a API web estão ambos registados no seu diretório.

#### <a name="token-expiration"></a>Expiração do token
Quando a aplicação web utiliza o código de autorização ao obter o token de acesso um JWT, também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser utilizado para autenticar novamente o utilizador sem necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

### <a name="daemon-or-server-application-to-web-api"></a>O daemon ou a aplicação de servidor para a API Web
Esta secção descreve um daemon ou servidor de aplicação que precisa de obter recursos de uma API web. Existem dois cenários secundárias que se aplicam a esta secção: um daemon de que tem de chamar uma API, incorporada no tipo de concessão de credenciais de cliente OAuth 2.0; web e uma aplicação de servidor (por exemplo, uma API web) que tem de chamar uma API, incorporada na especificação de rascunho de OAuth 2.0 On-Behalf-Of web.

Para o cenário quando uma aplicação de daemon tem de chamar uma API, web é importante compreender algumas coisas. Em primeiro lugar, não é possível com uma aplicação de daemon que requer que a aplicação para que as suas próprias identidades interação do utilizador. Um exemplo de uma aplicação de daemon é uma tarefa de lote ou um serviço de sistema operativo em execução em segundo plano. Este tipo de aplicação pede um token de acesso utilizando a respetiva identidade da aplicação e apresentar o respetivo ID da aplicação, a credencial (palavra-passe ou certificado) e a aplicação URI de ID para o Azure AD. Após a autenticação com êxito, o daemon de recebe um token de acesso do Azure AD, em seguida, é utilizado para chamar a API web.

Para o cenário quando uma aplicação de servidor tem de chamar uma API, web é útil utilizar um exemplo. Imagine que um utilizador foi autenticado numa aplicação nativa e tem de chamar uma API web desta aplicação nativa. O Azure AD emite um token de acesso para chamar a API web JWT. Se a API web tem de chamar a API web a jusante outro, pode utilizar o fluxo em-nome-de delegar a identidade do utilizador e autenticar-se a API web de segundo nível.

#### <a name="diagram"></a>Diagrama
![Daemon ou a aplicação de servidor para o diagrama de Web API](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Concessão de credenciais de identidade da aplicação com o cliente de OAuth 2.0
1. Em primeiro lugar, a aplicação de servidor tem de autenticar com o Azure AD como si próprio, sem qualquer interação humana como um diálogo de início de sessão interativo. Faz um pedido para ponto final de token do Azure AD, fornecendo a credencial, o ID da aplicação e o URI de ID da aplicação.
2. Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para chamar a API web.
3. Através de HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade do utilizador delegados com a especificação de em-nome-de rascunho de OAuth 2.0
O fluxo abordado abaixo parte do princípio de que um utilizador ser autenticado num outra aplicação (por exemplo, uma aplicação nativa) e as respetivas identidades de utilizador foi utilizada para adquirir um token de acesso para a API web de camada de primeiro.

1. A aplicação nativa envia o token de acesso para a API web de camada de primeiro.
2. Primeira camada web API envia um pedido para ponto final de token do Azure AD, fornecendo o ID da aplicação e as credenciais, bem como token de acesso do utilizador. Além disso, é enviado o pedido com um on_behalf_of parâmetro que indica o web API está a pedir novos tokens para chamar uma API de web a jusante em nome do utilizador original.
3. Azure AD verifica se a API web de primeiro nível tem permissões para aceder a API web de camada de segundo e valida o pedido devolvendo um token de acesso JWT e um JWT atualizar o token para a API web de camada de primeiro.
4. Através de HTTPS, a primeira camada web chamadas à API, em seguida, a API web de segundo nível, acrescentando a cadeia do token no cabeçalho de autorização no pedido. Primeira camada web API pode continuar a chamar a API web de segundo nível, desde que o token de acesso e atualização de tokens é válidas.

#### <a name="code-samples"></a>Exemplos de Código
Consulte os exemplos de código do Daemon ou a aplicação de servidor para cenários de Web API. E, verifique frequentemente – adicionamos amostras novo sempre. [Servidor ou aplicação Daemon API Web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>A registar
* Inquilino único: Para a identidade da aplicação e casos de identidade do delegado de utilizador, a aplicação de servidor ou daemon tem de estar registada no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o daemon de ou para requerer acesso do servidor para os respetivos recursos. Se está a ser utilizado um tipo de identidade do delegado de utilizador, a aplicação de servidor tem de selecionar as permissões pretendidas no menu pendente "Permissões para outras aplicações" no Portal do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.
* Multi-inquilino: Em primeiro lugar, a aplicação do daemon ou servidor está configurada para indicar as permissões necessárias para ser funcional. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino fornece o consentimento para a aplicação, o que torna a mesma disponível para a respetiva organização. Algumas aplicações necessitam de permissões de nível de utilizador apenas, o que qualquer utilizador na organização pode autorizar. Outras aplicações requerem permissões ao nível do administrador, o que não é possível autorizar um utilizador na organização. Apenas um administrador da directory pode dar consentimento para aplicações que requerem este nível de permissões. Quando o utilizador ou administrador permitir, ambas a APIs web estão registadas no seu diretório.

#### <a name="token-expiration"></a>Expiração do token
Quando a primeira aplicação utiliza o código de autorização ao obter o token de acesso um JWT, também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser utilizado para autenticar novamente o utilizador sem pedir credenciais. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

## <a name="see-also"></a>Veja Também
[Guia para programadores do Azure Active Directory](active-directory-developers-guide.md)

[Exemplos de código do Azure Active Directory](active-directory-code-samples.md)

[Informações importantes sobre o Rollover da chave de assinatura no Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

