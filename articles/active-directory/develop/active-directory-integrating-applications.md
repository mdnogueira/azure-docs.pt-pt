---
title: "Integrar aplicações com o Azure Active Directory"
description: "Como adicionar, atualizar ou remover uma aplicação no Azure Active Directory (Azure AD)."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 8a5eab88e10b330bf4da88c01d24a11e95277439
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrar aplicações com o Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Programadores empresariais e fornecedores de software-como-um-serviço (SaaS) podem desenvolver serviços em nuvem comerciais ou aplicações de linha de negócio, que podem ser integradas com o Azure Active Directory (Azure AD) para fornecer o início de sessão seguro e autorização para os respetivos serviços. Para integrar uma aplicação ou serviço com o Azure AD, um programador tem primeiro de registar a aplicação do Azure AD.

Este artigo mostra como adicionar, atualizar ou remover o registo de uma aplicação no Azure AD. Pode obter informações sobre os diferentes tipos de aplicações que podem ser integrados com o Azure AD, como configurar as suas aplicações para aceder a outros recursos, tais como as APIs web e muito mais.

Para saber mais sobre os dois objetos do Azure AD que representa uma aplicação registada e a relação entre eles, consulte o artigo [objectos da aplicação e objetos de principais de serviço](active-directory-application-objects.md); para saber mais sobre as diretrizes de imagem corporativa deve utilizar quando desenvolver aplicações com o Azure Active Directory, consulte [diretrizes de imagem corporativa para aplicações integradas](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Adicionar uma aplicação
Qualquer aplicação que pretende utilizar as capacidades do Azure AD primeiro tem de estar registada num inquilino do Azure AD. Este processo de registo envolve dá ao Azure AD obter detalhes sobre a aplicação, tais como o URL onde tem localizados, o URL para enviar as respostas depois de um utilizador é autenticado, o URI que identifique a aplicação e assim sucessivamente.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Para registar uma nova aplicação no portal do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**e clique em **novo registo de aplicação**.

   ![Registar uma nova aplicação](./media/active-directory-integrating-applications/add-app-registration.png)

4. Quando o **criar** é apresentada a página, introduza as informações de registo da aplicação: 

  - **Nome:** introduza um nome de aplicação significativo
  - **Tipo de aplicação:** 
    - Selecione "Nativo" para [aplicações cliente](active-directory-dev-glossary.md#client-application) que são instaladas localmente num dispositivo. Esta definição é utilizada para público OAuth [clientes nativos](active-directory-dev-glossary.md#native-client).
    - Selecione "aplicação Web / API" para [aplicações cliente](active-directory-dev-glossary.md#client-application) e [aplicações de recursos/API](active-directory-dev-glossary.md#resource-server) que são instaladas num servidor seguro. Esta definição é utilizada para OAuth confidencial [web clientes](active-directory-dev-glossary.md#web-client) públicas e [utilizador agente-os clientes baseados em](active-directory-dev-glossary.md#user-agent-based-client). A mesma aplicação também pode expor um cliente e de recursos/API.
  - **URL de início de sessão:** para "aplicação Web / API" aplicações, forneça o URL de base da sua aplicação. Por exemplo, `http://localhost:31544` poderá ser o URL de uma aplicação web em execução no seu computador local. Os utilizadores seriam utilizar este URL para iniciar sessão a uma aplicação de cliente web. 
  - **URI de redirecionamento:** para aplicações "Nativas", forneça o URI utilizado pelo Azure AD para devolver respostas token. Introduza um valor específico da aplicação, por exemplo`http://MyFirstAADApp`

   ![Registar uma nova aplicação – criar](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Se gostaria de exemplos específicos de aplicações web ou aplicações nativas, consulte o nosso [inícios Rápidos](active-directory-developers-guide.md#get-started).

5. Quando terminar, clique em **criar**. Azure AD atribui um ID exclusivo da aplicação para a aplicação e, está a ser direcionado para a página de registo principal da sua aplicação. Dependendo se a aplicação é um web ou a aplicação nativa, são fornecidas diferentes opções para adicionar capacidades adicionais à sua aplicação. Consulte a secção seguinte para uma descrição geral de consentimento e detalhes sobre como ativar funcionalidades de configuração adicionais no registo da aplicação (credenciais, as permissões, ativar início de sessão para os utilizadores de outros inquilinos).

  > [!NOTE]
  > Por predefinição, a aplicação registada recentemente está configurada para permitir **apenas** utilizadores a partir do mesmo inquilino para iniciar sessão sua aplicação.
  > 
  > 

## <a name="updating-an-application"></a>Atualizar uma aplicação
Assim que a aplicação foi registada com o Azure AD, poderá ter de ser atualizado para fornecer acesso a web APIs, disponibilizada no outras organizações e muito mais. Esta secção descreve várias formas, na qual pode configurar ainda mais a sua aplicação. Primeiro vamos começar com uma descrição geral do framework consentimento, que é importante compreender quando criar aplicações que precisem de ser utilizado por outros utilizadores ou aplicações.

### <a name="overview-of-the-consent-framework"></a>Descrição geral do framework consentimento

A estrutura de consentimento do Azure AD torna mais fácil desenvolver web do multi-inquilino e aplicações de cliente nativo, incluindo aplicações de várias camadas. Estas aplicações permitam início de sessão por contas de utilizador de um inquilino do Azure AD, diferente em que a aplicação está registada. Pode também precisam de acesso web APIs, tais como o Microsoft Graph API (para aceder ao Azure Active Directory, o Intune e serviços do Office 365) e APIs de outros serviços da Microsoft, além das suas próprias APIs da web. A arquitetura baseia-se um utilizador ou um administrador dar consentimento para uma aplicação que lhe pede para ser registado no seu diretório, o que poderá envolver o método aceder aos dados de diretório.

Por exemplo, se precisar de uma aplicação de cliente web ler informações de calendário sobre o utilizador a partir do Office 365, esse utilizador é necessário para autorizar a aplicação de cliente pela primeira vez. Depois de consentimento é determinado, a aplicação de cliente será capaz de ligar para o Microsoft Graph API em nome do utilizador e utilize as informações de calendário, conforme necessário. O [Microsoft Graph API](https://graph.microsoft.io) fornece acesso aos dados do Office 365 (por exemplo, calendários e mensagens do Exchange, sites e apresenta uma lista do SharePoint, documentos do OneDrive, blocos de notas do OneNote, tarefas a partir da Planner, livros do Excel, etc.), bem como os utilizadores e grupos do Azure AD e outros objetos de dados da cloud services da Microsoft mais. 

A estrutura de consentimento baseia OAuth 2.0 e o respetivos vários fluxos, tais como conceder credenciais de cliente e de concessão do código de autorização, com clientes públicos ou confidenciais. Ao utilizar o OAuth 2.0, do Azure AD torna possível criar vários tipos de aplicações de cliente, tal como um telemóvel, tablet, servidor ou uma aplicação web e obter acesso aos recursos necessários.

Para obter mais informações sobre como utilizar a estrutura de consentimento com OAuth2.0 concede de autorização, consulte [autorizar o acesso a aplicações web utilizando o OAuth 2.0 e o Azure AD](active-directory-protocols-oauth-code.md) e[cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md). Para obter informações sobre como obter acesso autorizado ao Office 365 através do Microsoft Graph, consulte [autenticação da aplicação com o Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exemplo de experiência de consentimento

Os passos seguintes mostram como o consentimento experiência funciona para o programador da aplicação e o utilizador.

1. Partem do princípio de que tem uma aplicação de cliente web que necessita para pedir permissões específicas para aceder a uma recurso/API. Irá aprender a fazer esta configuração na secção seguinte, mas essencialmente o portal do Azure é utilizado para declarar pedidos de permissão no momento da configuração. Como outras definições de configuração que se tornem parte do registo do AAD da aplicação:
   
  ![Permissões para outras aplicações](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Considere que foram atualizadas as permissões da sua aplicação, a aplicação está em execução e um utilizador está prestes a utilizá-la pela primeira vez. Primeiro a aplicação tem de obter um código de autorização do Azure AD `/authorize` ponto final. O código de autorização, em seguida, pode ser utilizado para adquirir um novo acesso e atualizar o token.

3. Se o utilizador já não for autenticado, Azure do AD `/authorize` pede ao ponto final de início de sessão.
   
  ![Administrador ou utilizador de início de sessão para o Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Depois do utilizador tem sessão iniciada, do Azure AD irá determinar se o utilizador tem de ser apresentada uma página de consentimento. Esta determinação baseia-se no se o utilizador (ou o administrador da respetiva organização) já concedeu consentimento a aplicação. Se já não tenha sido concedido consentimento, o Azure AD pede ao utilizador consentimento e apresenta as permissões necessárias, tem de funcionar. O conjunto de permissões que são apresentadas na caixa de diálogo de consentimento corresponder aos perfis selecionados nas permissões delegadas no portal do Azure.
   
  ![Experiência de consentimento do utilizador](./media/active-directory-integrating-applications/consent.png)

5. Depois do utilizador concede consentimento, é devolvido um código de autorização da aplicação, o que é resgatadas para adquirir um token de acesso e atualizar o token. Para obter mais informações sobre este fluxo, consulte o [aplicação a secção de API em cenários de autenticação da web para o Azure AD web](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Como administrador, pode também autorizar para as permissões delegadas de uma aplicação em nome de todos os utilizadores no seu inquilino. Consentimento administrativo impede a caixa de diálogo de consentimento de apresentação para cada utilizador no inquilino e é efetuado a sua aplicação página no [portal do Azure](https://portal.azure.com). Do **definições** página para a sua aplicação, clique em **permissões obrigatórias** e clique em de **conceder permissões de** botão. 

  ![Conceder permissões de administrador explícita consentimento](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Conceder explícita consentimento utilizando o **conceder permissões** botão é atualmente necessário para aplicações de página única (SPA) que utilizam ADAL.js. Caso contrário, a aplicação falha quando o token de acesso é solicitado.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Configurar uma aplicação de cliente para aceder a APIs web
Por ordem para uma aplicação de cliente web confidencial conseguir a participar num fluxo de concessão de autorização que necessita de autenticação (e obter um token de acesso), tem de estabelecer credenciais seguras. O método de autenticação predefinido suportado pelo portal do Azure é o ID de cliente + chave secreta. Esta secção aborda os passos de configuração necessários para fornecer a chave secreta credenciais do seu cliente.

Além disso, antes de um cliente pode aceder a uma API web do expostas por uma aplicação de recursos (por exemplo, o Microsoft Graph API), a arquitetura de consentimento garante o cliente obtém a concessão de permissão necessária, com base nas permissões pedidas. Por predefinição, todas as aplicações podem escolher permissões "Windows Azure Active Directory" (Graph API) e "Windows API de gestão de serviços do Azure." O [permissão de "início de sessão e perfil de utilizador de leitura" Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) também está selecionada por predefinição. Se o cliente está a ser registado num inquilino com contas de subscrever o Office 365, APIs da Web e permissões para o Exchange Online e SharePoint estão disponíveis para seleção. Pode selecionar [dois tipos de permissões](active-directory-dev-glossary.md#permissions) para cada pretendido web API:

- Permissões de aplicação: A aplicação de cliente precisa de aceder à API web diretamente como propriamente dito (nenhum contexto de utilizador). Este tipo de permissão requer consentimento de administrador e também não está disponível para aplicações cliente nativo.

- As permissões delegadas: A aplicação de cliente precisa de aceder à web API que o utilizador com sessão iniciada, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a menos que a permissão requer consentimento de administrador. 

  > [!NOTE]
  > A adição de uma delegado permissão para uma aplicação automaticamente conceda consentimento para os utilizadores no inquilino, tal como era no portal clássico do Azure. Utilizadores manualmente consentimento para que as permissões delegadas adicionadas no tempo de execução, a menos que o administrador clica o **conceder permissões** no botão do **permissões obrigatórias** secção a página de aplicação no portal do Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Para adicionar as credenciais de aplicação ou permissões para aceder a APIs web
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/active-directory-integrating-applications/update-app-registration.png)

4. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação. Para adicionar uma chave secreta para credenciais da sua aplicação web:
  - Clique em de **chaves** secção no **definições** página.  
  - Adicione uma descrição para a sua chave.
  - Selecione um ou duração de dois anos.
  - Clique em **Guardar**. A coluna mais à direita irá conter o valor da chave, depois de guardar as alterações de configuração. **Certifique-se de copiar a chave** para utilização no código da aplicação de cliente, porque não está acessível uma vez sair desta página.

  ![Atualizar o registo de uma aplicação - chaves](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Para adicionar permissões para aceder a recursos APIs a partir do seu cliente
  - Clique em de **permissões obrigatórias** secção no **definições** página. 
  - Clique em de **adicionar** botão.
  - Clique em **selecionar um API** para selecionar o tipo de recursos que pretende que escolha.
  - Procure a lista das APIs disponíveis ou utilize a caixa de pesquisa para selecionar de entre as aplicações de recursos disponíveis no seu diretório que expõem uma API web. Clique no recurso que está interessado, em seguida, clique em **selecione**.
  - Está a ser direcionado para o **ativar o acesso ao** página. Selecione as permissões de aplicação e/ou necessita de permissões delegadas a aplicação quando aceder à API.
   
  ![Atualizar o registo de uma aplicação - permissões api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Atualizar o registo de uma aplicação - as permissões de permissões](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Quando terminar, clique no **selecione** botão no **ativar o acesso ao** página, o **feito** botão no **acesso à API adicionar** página. É encaminhado para o **as permissões necessárias** página, onde o novo recurso é adicionado à lista de APIs.

  > [!NOTE]
  > Ao clicar no **feito** botão também automaticamente define as permissões para a sua aplicação no seu diretório com base nas permissões para outras aplicações que configurou.  Pode ver as permissões ao observar a aplicação **definições** página.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recursos para expor as APIs web

Pode desenvolver uma API web e disponibilizá-lo para aplicações cliente resultariam da exposição acesso [âmbitos](active-directory-dev-glossary.md#scopes) e [funções](active-directory-dev-glossary.md#roles). É disponibilizada uma API web corretamente configurada à semelhança de outro Microsoft APIs web, incluindo a Graph API e as APIs do Office 365. Âmbitos de acesso e as funções são expostas através da sua [manifesto da aplicação](active-directory-dev-glossary.md#application-manifest), que é um ficheiro JSON que representa a configuração da identidade da aplicação. 

A secção seguinte mostra como para expor os âmbitos de acesso, modificando o manifesto da aplicação de recursos.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Adicionar âmbitos de acesso à sua aplicação de recursos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.

3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/active-directory-integrating-applications/update-app-registration.png)

4. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação. Mudar para o **editar manifesto** página, ao clicar em **manifesto** da página de registo da aplicação. Abre o editor de manifesto baseada na web, permitindo-lhe **editar** o manifesto no portal. Opcionalmente, pode clicar em **transferir** e editar localmente, em seguida, utilizar **carregar** para voltar a aplicar à sua aplicação.

5. Neste exemplo, expomos um novo âmbito chamado `Employees.Read.All` na nossa recurso/API, adicionando o seguinte elemento JSON para o `oauth2Permissions` coleção. Existente `user_impersonation` âmbito é fornecido por predefinição durante o registo. `user_impersonation`permite que uma aplicação de cliente pedir permissão para aceder ao recurso, com a identidade do utilizador com sessão iniciada. Certifique-se de adicionar a vírgula após existente `user_impersonation` analisar o elemento e alterar os valores de propriedade de acordo com as necessidades do seu recurso. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > O valor de "id" tem de ser gerado utilizando uma ferramenta de geração de GUID como [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) ou através de programação. Representa um identificador exclusivo para o âmbito como expostos através da web API. Depois de um cliente está corretamente configurado com permissões para aceder à web API, este é emitido um token de acesso OAuth2.0 pelo Azure AD. Quando o cliente, chama a API web, apresenta o token de acesso que tenha o âmbito (scp) conjunto de afirmações para as permissões de pedido no respetivo registo de aplicação.
  >
  > Pode expor os âmbitos adicionais mais tarde conforme necessários. Considere-se de que a API web poderá expor associados uma variedade de diferentes funções de vários âmbitos. O recurso pode controlar o acesso para a API web em tempo de execução, através da avaliação de âmbito (`scp`) claim(s) no token de acesso de OAuth 2.0 recebido.
  > 

6. Quando terminar, clique em **guardar**. Agora a API web está configurada para utilização por outras aplicações no seu diretório.  

  ![Atualizar o registo de uma aplicação](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verifique se o web que API está exposta a outras aplicações no seu inquilino
1. Aceda ao seu Azure AD de inquilino, clique em **registos de aplicação** novamente, em seguida, localizar/clique a aplicação de cliente que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/active-directory-integrating-applications/update-app-registration.png)

2. Repita o passo 5 tal como fez na [configurar uma aplicação de cliente para aceder a web APIs](#configure-a-client-application-to-access-web-apis). Quando chegar ao **selecionar um API** passo, procure o seu recurso ao introduzir o respetivo nome de aplicação no campo de pesquisa e, em **selecione**. 

3. No **ativar o acesso ao** página deverá ver o novo âmbito, disponível para pedidos de permissão de cliente.

  ![Novas permissões são mostradas](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Obter mais informações sobre o manifesto da aplicação

O manifesto da aplicação, na verdade, serve como um mecanismo para atualizar a entidade de aplicação, que define todos os atributos da configuração de identidade de uma aplicação do Azure AD, incluindo os âmbitos de acesso da API discutimos. Para obter mais informações sobre a entidade de aplicação e o respetivo esquema, consulte o [documentação de entidade de aplicação de API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completa sobre os membros de entidade de aplicação utilizada para especificar permissões para a API, incluindo:  

- O membro appRoles, que é uma coleção de [função de aplicação](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entidades, utilizadas para definir [permissões de aplicação](active-directory-dev-glossary.md#permissions) para uma API web. 
- O membro oauth2Permissions, que é uma coleção de [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entidades, utilizadas para definir [permissões delegadas](active-directory-dev-glossary.md#permissions) para uma API web.

Para obter mais informações sobre a aplicação manifesto conceitos em geral, consulte [compreender o manifesto da aplicação do Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Aceder a Graph do Azure e o Office 365 através de APIs de Microsoft Graph  

Conforme mencionado anteriormente, além de expor/aceder a APIs para as suas próprias aplicações, pode registar a aplicação de cliente de acesso a APIs expostas pela recursos Microsoft. A Microsoft Graph API, referido como "Microsoft Graph" na lista de recursos/API no portal, está disponível para todas as aplicações que são registadas com o Azure AD. Se estiver a registar a aplicação cliente de um inquilino que contém as contas que tenham efetuado a inscrição para uma subscrição do Office 365, também pode aceder os âmbitos expostos pelos vários recursos do Office 365.

Para ver um debate completado em âmbitos exposta pelo Microsoft Graph API, consulte o [âmbitos de permissões | Conceitos do Microsoft Graph API](https://graph.microsoft.io/docs/authorization/permission_scopes) artigo.

> [!NOTE]
> Devido a uma limitação atual, as aplicações de cliente nativo só é possível chamar para a Azure AD Graph API se utilizarem a permissão "Aceder ao diretório da sua organização". Esta restrição não se aplica para aplicações web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configurar aplicações multi-inquilinos

Ao registar uma aplicação no Azure AD, poderá pretender que a aplicação para ser acedido apenas por utilizadores na sua organização. Em alternativa, pode também pretender que a aplicação para ser acessíveis aos utilizadores em organizações externas. Estes tipos de duas aplicação são denominados aplicações multi-inquilinos e um único inquilino. Esta secção descreve como modificar a configuração de uma aplicação de inquilino único para tornar uma aplicação multi-inquilino.

É importante ter em conta as diferenças entre uma aplicação multi-inquilino e um único inquilino:  

- Uma aplicação de inquilino único destina-se a utilização de uma organização. Normalmente, é uma aplicação de (LoB) de linha de negócio escrita por um programador de empresa. Uma aplicação de inquilino único só pode ser acedida pelos utilizadores com contas no mesmo inquilino como o registo de aplicação. Como resultado, só tem de ser aprovisionada num diretório.
- Uma aplicação multi-inquilino destina-se em muitas organizações. Conhecido como uma aplicação de web do software-como-um-serviço (SaaS), que é normalmente escrito por um fabricante independente de software (ISV). Aplicações de multi-inquilinos têm de ser aprovisionadas em cada inquilino onde os utilizadores precisam de acesso. Para inquilinos diferente daquela onde a aplicação está registada, consentimento do utilizador ou administrador é necessário para registá-los. Tenha em atenção que as aplicações de cliente nativo estão multi-inquilinos por predefinição como estejam instaladas no dispositivo o proprietário de recursos. Consulte precedente [descrição geral do framework consentimento](#overview-of-the-consent-framework) secção para obter detalhes sobre a arquitetura de consentimento.

Efetuar um multi-inquilino de aplicação requer ambas as alterações de registo de aplicações, bem como as alterações para a própria aplicação da web. As secções seguintes abrangem ambos.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Alterar o registo de aplicação para suportar o multi-inquilino

Se estiver a escrever uma aplicação que pretende disponibilizar aos seus clientes ou parceiros fora da sua organização, terá de atualizar a definição de aplicação no portal do Azure.

> [!IMPORTANT]
> O Azure AD requer o URI de ID de aplicação de aplicações de multi-inquilinos para ser globalmente exclusivo. O URI de ID de aplicação é uma das formas que uma aplicação for identificada nas mensagens de protocolo. Para uma aplicação de inquilino único, é suficiente para o URI de ID de aplicação a ser exclusivos dentro desse inquilino. Para uma aplicação multi-inquilino, tem de ser globalmente exclusivo para que o Azure AD pode encontrar a aplicação em todos os inquilinos. Exclusividade global é imposta, exigindo que o URI de ID de aplicação ter um nome de anfitrião que corresponde a um domínio verificado do inquilino do Azure AD. Por exemplo, se o nome do seu inquilino for contoso.onmicrosoft.com um URI de ID de aplicação válido seria https://contoso.onmicrosoft.com/myapp. Se o seu inquilino tem um domínio verificado do contoso.com, em seguida, um URI de ID de aplicação válido seria também https://contoso.com/myapp. Se o URI de ID de aplicação não seguir este padrão, a definição de uma aplicação como falha de multi-inquilino.
> 

Para conceder a utilizadores externos a capacidade de aceder à aplicação: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação.
4. Do **definições** página, clique em **propriedades** e altere o **tenanted de várias** mudar para **Sim**.

Depois de efetuar as alterações, utilizadores e os administradores de outras organizações conseguem conceder os seus utilizadores a capacidade para iniciar sessão sua aplicação, permitindo que a aplicação para aceder a recursos protegidos pelo respetivo inquilino.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Alterar a aplicação para suportar o multi-inquilino

Suporte para aplicações de multi-inquilinos depende de descontos elevados a estrutura de consentimento do Azure AD. Consentimento é o mecanismo que permite ao utilizador a partir de outro inquilino, para conceder o acesso de aplicações a recursos protegidos por inquilino do utilizador. Esta experiência é referida como "consentimento do utilizador".

Também pode oferecer a sua aplicação web:

- A capacidade para os administradores "inscrever pela minha empresa". Esta experiência, referida como "admin consentimento", fornece um administrador a capacidade de conceder consentimento em nome *todos os utilizadores* na respetiva organização. Apenas um utilizador que efetua a autenticação com uma conta que pertence à função de Administrador Global pode fornecer consentimento admin; outras pessoas recebem um erro.

- Uma experiência de inscrição para utilizadores. É esperado que o utilizador é fornecido um botão "inscrição", que irá redirecionar o browser OAuth2.0 de AD Azure `/authorize` endpoint ou um OpenID Connect `/userinfo` ponto final. Estes pontos finais permitir que a aplicação obter informações sobre o novo utilizador inspecionando a id_token. Após a fase de inscrição é apresentada ao utilizador com um pedido de consentimento, semelhante ao apresentado no [descrição geral do framework consentimento](#overview-of-the-consent-framework) secção.

Para obter mais informações sobre as alterações de aplicação necessários para suportar várias-tenanted acesso e experiências de início de sessão-na/sessão-up, consulte:

- [Como iniciar sessão de qualquer utilizador do Azure Active Directory (AD) utilizando o padrão de aplicação multi-inquilino](active-directory-devhowto-multi-tenant-overview.md)
- A lista de [exemplos de código de multi-inquilino](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Guia de introdução: Adicionar imagem corporativa à sua página de início de sessão no Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Ativar o OAuth 2.0 implícita conceder para aplicações de página única

Única página aplicação (SPAs) são normalmente estruturada com um JavaScript pesado front-end que é executado no browser, que chama API web a aplicação back-end para efetuar a respetiva lógica de negócio. Para SPAs alojados no Azure AD, utilize concessão implícita do OAuth 2.0 para autenticar o utilizador com o Azure AD e obter um token que pode utilizar para proteger as chamadas de cliente de JavaScript da aplicação para a API web de back-end. 

Depois do utilizador concedeu consentimento, este mesmo protocolo de autenticação pode ser utilizado para obter os tokens para proteger chamadas entre o cliente e outro web configurados para a aplicação de recursos de API. Para obter mais informações sobre a concessão de autorização implícita e ajudar a decidir se é adequada para o seu cenário de aplicação, consulte [fluxo no Azure Active Directory de concessão de compreender o OAuth2 implícita](active-directory-dev-understanding-oauth2-implicit-grant.md).

Por predefinição, a concessão implícita de OAuth 2.0 está desativado para aplicações. Pode ativar a concessão implícita do OAuth 2.0 para a sua aplicação através da definição de `oauth2AllowImplicitFlow` valor na respetiva [manifesto da aplicação](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para ativar a concessão implícita de OAuth 2.0

> [!NOTE]
> Para obter detalhes sobre como editar o manifesto da aplicação, lembre-se de que primeiro consultar a secção anterior, [configurar uma aplicação de recursos para expor APIs web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação.
4. Mudar para o **editar manifesto** página, ao clicar em **manifesto** da página de registo da aplicação. Abre o editor de manifesto baseada na web, permitindo-lhe **editar** o manifesto no portal. Localize e defina o valor de "oauth2AllowImplicitFlow" como "true". Por predefinição, está definido como "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Guarde o manifesto atualizado. Depois de guardar, a API web está agora configurada para utilizar a concessão implícita do OAuth 2.0 para autenticar os utilizadores.

## <a name="removing-an-application"></a>Remover uma aplicação
Esta secção descreve como remover o registo de uma aplicação de inquilino do Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Remover uma aplicação criada pela sua organização
As aplicações que registou-se a sua organização mostram sob o filtro "As minhas aplicações" na página do seu inquilino principal "registos de aplicação". Estas aplicações são aqueles que registou manualmente através do portal do Azure, ou de forma programática através do PowerShell ou a Graph API. Mais especificamente, são representados por ambos os um objeto de aplicação e um Principal de serviço no seu inquilino. Para obter mais informações, consulte [objectos da aplicação e objetos de principais de serviço](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para remover uma aplicação de inquilino único do seu diretório
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação.
4. Na página de registo principal da aplicação, clique em **eliminar**.
5. Clique em **Sim** na mensagem de confirmação.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Para remover uma aplicação multi-inquilino do seu diretório raiz
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se o fornecem de conta de acesso a mais do que um, clique na sua conta no canto superior direito e definir a sessão do portal para o Azure AD pretendido de inquilino.
3. No painel de navegação esquerdo, clique o **do Azure Active Directory** de serviço, clique em **registos de aplicação**, em seguida, localizar/clique a aplicação que pretende configurar. É direcionado para a página de registo principal da aplicação, o qual abre o **definições** página para a aplicação.
4. Do **definições** página, escolha **propriedades** e altere o **tenanted de várias** mudar para **não**, para alterar primeiro a aplicação inquilino único, em seguida, clique em **guardar**. Objetos de principal de serviço da aplicação permaneçam na inquilinos de todas as organizações que já tem sido autorizado ao mesmo.
5. Clique em de **eliminar** botão da página de registo principal da aplicação.
6. Clique em **Sim** na mensagem de confirmação.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Remover uma aplicação de multi-inquilino autorizada por outra organização
Um subconjunto das aplicações que mostram sob o filtro "Todas as aplicações" (excluindo a registos de "As minhas aplicações") na página "Registos de aplicação" principal do seu inquilino, são aplicações de multi-inquilinos. Em termos técnicos, estas aplicações de multi-inquilinos são de outro inquilino em foram registadas no seu inquilino durante o processo de consentimento. Estes erro são mais especificamente, representados por apenas um objeto principal do serviço no seu inquilino, a com nenhum objeto de aplicação correspondente. Para obter mais informações sobre as diferenças entre a aplicação e objetos de principal de serviço, consulte [aplicação e objetos de principal de serviço no Azure AD](active-directory-application-objects.md).

Para remover o acesso de uma aplicação multi-inquilino para o seu diretório (depois de ter concedido consentimento), o administrador da empresa tem de remover o respetivo principal de serviço. O administrador tem de ter acesso de administrador global e pode remover através do portal do Azure a [Cmdlets do Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como funciona a autenticação no Azure AD, consulte [cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).
- Consulte o [diretrizes de imagem corporativa para aplicações integradas](active-directory-branding-guidelines.md) para dicas sobre visual orientações para a sua aplicação.
- Para obter mais informações sobre a relação entre objetos de aplicação e um Principal de serviço de uma aplicação, consulte [objectos da aplicação e objetos de principais de serviço](active-directory-application-objects.md).
- Para mais informações sobre a função desempenhada por manifesto de aplicação, consulte o artigo [compreender o manifesto da aplicação do Azure Active Directory](active-directory-application-manifest.md)
- Consulte o [Glossário de programador do Azure AD](active-directory-dev-glossary.md) para definições de alguns dos conceitos de programador do Azure Active Directory (AD) principal.
- Visite o [guia para programadores do Active Directory](active-directory-developers-guide.md) para uma descrição geral de todos os conteúdos relacionados com o programador.

