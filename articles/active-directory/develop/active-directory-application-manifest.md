---
title: "Compreender o manifesto da aplicação do Azure Active Directory | Microsoft Docs"
description: "Cobertura de detalhado de manifesto da aplicação do Azure Active Directory, que representa a configuração da identidade de uma aplicação num inquilino do Azure AD e é utilizada para facilitar a OAuth autorização, experiência de consentimento e muito mais."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Compreender o manifesto da aplicação do Azure Active Directory
As aplicações que se integram com o Azure Active Directory (AD) tem de ser registadas com um inquilino do Azure AD, fornecendo uma configuração de identidade persistente para a aplicação. Esta configuração é consulted em runtime, ativar cenários que permitem que uma aplicação outsource e Mediador de autenticação/autorização através do Azure AD. Para mais informações sobre o modelo de aplicação do Azure AD, consulte o [adicionar, atualizar e remover uma aplicação] [ ADD-UPD-RMV-APP] artigo.

## <a name="updating-an-applications-identity-configuration"></a>Atualizar a configuração da identidade de uma aplicação
Existem várias opções de, na verdade, disponível para atualizar as propriedades na configuração da identidade de uma aplicação, que variam no capacidades e graus de dificuldade em, incluindo o seguinte:

* O  **[do portal do Azure] [ AZURE-PORTAL] interface de utilizador Web** permite-lhe atualizar as propriedades de uma aplicação mais comuns. Esta é a forma de propensas ao erro mais rápida e pelo menos, de atualizar propriedades da sua aplicação, mas não fornecer acesso total para todas as propriedades, como os dois métodos.
* Para cenários mais avançados, onde tem de atualizar as propriedades que não estão expostas no portal clássico do Azure, pode modificar o **manifesto da aplicação**. Este é o objetivo deste artigo e é abordada mais detalhadamente a iniciar na secção seguinte.
* Também é possível **escrever uma aplicação que utiliza o [Graph API] [ GRAPH-API]**  para atualizar a sua aplicação, o que requer o maior esforço. Isto pode ser uma boa opção embora, se estiver a escrever o software de gestão ou tem de atualizar propriedades da aplicação regularmente de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Com o manifesto da aplicação para atualizar a configuração da identidade de uma aplicação
Através de [portal do Azure][AZURE-PORTAL], pode gerir a configuração da identidade da aplicação, atualizando o manifesto de aplicação utilizando o editor de manifesto inline. Também pode transferir e carregar o manifesto da aplicação como um ficheiro JSON. Nenhum ficheiro real é armazenado no diretório. O manifesto da aplicação é simplesmente uma HTTP GET operação na entidade de aplicação do Azure AD Graph API e o carregamento é uma operação HTTP PATCH na entidade de aplicação.

Como resultado, para compreender o formato e propriedades de manifesto da aplicação, é necessário fazer referência a Graph API [entidade aplicação] [ APPLICATION-ENTITY] documentação. Exemplos de atualizações que podem ser efetuadas apesar de carregamento de manifesto de aplicação:

* **Declarar âmbitos de permissões (oauth2Permissions)** expostas pela sua API web. Consulte o tópico "Exposição de APIs para outras aplicações Web" no [integrar aplicações com o Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] para obter informações sobre como implementar a representação de utilizadores utilizar o oauth2Permissions delegado âmbito de permissão. Como mencionado anteriormente, as propriedades de entidade de aplicação estão documentadas na Graph API [entidade e o tipo complexo] [ APPLICATION-ENTITY] artigo de referência, incluindo a propriedade de oauth2Permissions que é uma colecção do tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Declarar as funções da aplicação (appRoles) expostas pela sua aplicação**. A propriedade de appRoles a entidade de aplicação é uma colecção do tipo [função de aplicação][APPLICATION-ENTITY-APP-ROLE]. Consulte o [controlo de acesso em aplicações em nuvem com o Azure AD baseado em funções] [ RBAC-CLOUD-APPS-AZUREAD] artigo para obter um exemplo de implementação.
* **Declarar cliente conhecido aplicações (knownClientApplications)**, que permitem-lhe associar logicamente o consentimento das aplicações de cliente especificado para o recurso/API web.
* **Pedido do Azure AD para emitir afirmações de associações de grupo** para o utilizador com sessão iniciada (groupMembershipClaims).  Também pode ser configurada para emitir afirmações sobre associações de funções de diretório do utilizador. Consulte o [autorização em aplicações na nuvem através de grupos do AD] [ AAD-GROUPS-FOR-AUTHORIZATION] artigo para obter um exemplo de implementação.
* **Permitir que a aplicação suportar a concessão implícita do OAuth 2.0** fluxos (oauth2AllowImplicitFlow). Este tipo de fluxo de concessão é utilizado com páginas web de JavaScript incorporado ou aplicações de página única (SPA). Para obter mais informações sobre a concessão de autorização implícita, consulte [fluxo no Azure Active Directory de concessão de compreender o OAuth2 implícita][IMPLICIT-GRANT].
* **Ativar a utilização de X509 certificados como a chave secreta** (keyCredentials). Consulte o [criar aplicações de serviço e o daemon no Office 365] [ O365-SERVICE-DAEMON-APPS] e [guia para programadores para autenticação com a API do Azure Resource Manager] [ DEV-GUIDE-TO-AUTH-WITH-ARM] artigos para obter exemplos de implementação.
* **Adicionar um novo URI de ID de aplicação** para a sua aplicação (identifierURIs[]). Os URIs de ID de aplicação são utilizados para identificar de forma exclusiva uma aplicação no seu inquilino do Azure AD (ou em vários inquilinos do Azure AD, para cenários de multi-inquilinos quando qualificado através de domínio personalizado verificado). São utilizados quando pedir permissões para uma aplicação de recursos ou adquirir um token de acesso para uma aplicação de recursos. Quando atualizar este elemento, a mesma atualização é efetuada à coleção de [] servicePrincipalNames de um principal de serviço correspondentes, que se encontra no inquilino inicial da aplicação.

O manifesto da aplicação também fornece uma boa forma de controlar o estado do registo da aplicação. Porque se encontra disponível no formato JSON, pode ser verificada a representação de ficheiro para o controlo de origem, juntamente com o código fonte da sua aplicação.

## <a name="step-by-step-example"></a>Passo exemplo passo
Agora permite-percorrer os passos necessários para atualizar a configuração da identidade da aplicação através do manifesto da aplicação. Vamos dar destaque um dos exemplos anteriores, que mostra como um novo âmbito de permissão de declarar uma aplicação do recurso:

1. Inicie sessão no [Portal do Azure][AZURE-PORTAL].
2. Depois de ter autenticado, escolha o seu inquilino do Azure AD ao selecioná-la a partir do canto superior direito da página.
3. Selecione **do Azure Active Directory** extensão do painel de navegação esquerdo e clique em **registos de aplicação**.
4. Localize a aplicação que pretende atualizar na lista e clique na mesma.
5. Na página da aplicação, clique em **manifesto** para abrir o editor de manifesto inline. 
6. Pode editar diretamente o manifesto através deste editor. Tenha em atenção que o manifesto segue-se o esquema para o [entidade aplicação] [ APPLICATION-ENTITY] conforme foi mencionado anteriormente: por exemplo, partindo do princípio de que pretende implementar/expõe uma nova permissão chamada "Employees.Read.All" na nossa aplicação de recursos (API), seria simplesmente adicionar um novo/segundo elemento à coleção oauth2Permissions, ie:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
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
        ],
   
    A entrada tem de ser exclusiva e, por conseguinte, tem de gerar um novo globalmente ID exclusivo (GUID) para o `"id"` propriedade. Neste caso, porque especificamos `"type": "User"`, esta permissão pode ser autorizado por qualquer conta autenticada pelo Azure AD de inquilino no qual a aplicação de recursos/API está registada. Esta ação garante o cliente permissão de aplicação para aceder ao mesmo em nome da conta. As cadeias de nome de apresentação e descrição são utilizadas durante consentimento e para apresentar no portal do Azure.
6. Quando tiver terminado o manifesto de atualização, clique em **guardar** para guardar o manifesto.  
   
Agora que o manifesto é guardado, pode dar um registado acesso de aplicação de cliente para a nova permissão adicionámos acima. Este tempo, pode utilizar a IU da Web do portal do Azure em vez de editar o manifesto da aplicação de cliente:  

1. Pela primeira vez, vá para o **definições** painel da aplicação de cliente para o qual pretende adicionar acesso para a nova API, clique em **permissões obrigatórias** e escolha **selecionar um API**.
2. Em seguida, será apresentada com a lista de aplicações (APIs) de recursos registado no inquilino. Clique na aplicação de recursos para selecioná-lo ou escreva o nome da aplicação a caixa de pesquisa. Quando tiver a aplicação, clique em **selecione**.  
3. Isto leva-o para o **selecionar permissões** página, que mostrará a lista de permissões de aplicação e permissões delegadas disponível para a aplicação de recursos. Selecione a permissão de novo para adicionar à lista de pedido do cliente de permissões. Esta nova permissão será armazenado na configuração da identidade da aplicação cliente, na propriedade de coleção "requiredResourceAccess".


E já está. Agora, as aplicações serão executadas utilizando a nova configuração de identidade.

## <a name="next-steps"></a>Passos seguintes
* Para obter mais detalhes sobre a relação entre objetos de aplicação e um Principal de serviço de uma aplicação, consulte [aplicação e objetos de principal de serviço no Azure AD][AAD-APP-OBJECTS].
* Consulte o [Glossário de programador do Azure AD] [ AAD-DEVELOPER-GLOSSARY] para definições de alguns dos conceitos de programador do Azure Active Directory (AD) principal.

Utilize a secção de comentários abaixo para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

