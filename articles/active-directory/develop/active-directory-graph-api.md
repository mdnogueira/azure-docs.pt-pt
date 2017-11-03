---
title: Azure Active Directory Graph API | Microsoft Docs
description: "Um guia de descrição geral e o início rápido para a Graph API que permite o acesso programático para o Azure AD através de pontos finais da REST API."
services: active-directory
documentationcenter: 
author: viv-liu
manager: mbaldwin
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 79bb96171c00fa62f24113800c3824856febe728
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-graph-api"></a>Graph API do Azure Active Directory
> [!IMPORTANT]
> Recomendamos vivamente que utilize o [Microsoft Graph](https://graph.microsoft.io/) em vez da Graph API do Azure AD para aceder aos recursos do Azure Active Directory. Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Existe um número muito limitado de cenários nos quais a Graph API do Azure AD ainda poderá ser adequada. Para obter mais informações, veja a mensagem do blogue [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph ou Graph do Azure AD), no Office Dev Center.
> 
> 

O Active Directory Graph API do Azure fornece acesso programático para o Azure AD através de pontos finais da REST API. As aplicações podem utilizar a Graph API para efetuar a criar, ler, atualizar e eliminar operações (CRUD) em dados de diretório e objetos. Por exemplo, a Graph API suporta as seguintes operações comuns para um objeto de utilizador:

* Criar um novo utilizador num diretório
* Obter propriedades de detalhado de um utilizador, tais como os respetivos grupos
* Atualizar propriedades de um utilizador, tais como a respetiva localização e o número de telefone, ou altere a palavra-passe
* Verifique a associação a grupos do utilizador para o acesso baseado em funções
* Desativar a conta de utilizador ou eliminá-lo completamente

Para além dos objetos de utilizador, pode efetuar operações similares noutros objetos, tais como grupos e aplicações. Para chamar a Graph API num diretório, a aplicação tem de ser registada com o Azure AD e configurada para permitir o acesso ao diretório. Normalmente, isto é conseguido através de um fluxo de consentimento do utilizador ou administrador.

Para começar a utilizar o Active Directory Graph API do Azure, consulte o [guia de introdução do API Graph](active-directory-graph-api-quickstart.md), ou ver o [interativa documentação de referência da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funcionalidades
A Graph API fornece as seguintes funcionalidades:

* **Pontos finais da API REST**: A Graph API é um serviço RESTful composta por pontos finais que são acedidos através de pedidos de HTTP padrão. A Graph API suporta tipos de conteúdo XML ou Javascript Object Notation (JSON) para os pedidos e respostas. Para obter mais informações, consulte [referência da API do REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticação com o Azure AD**: todos os pedidos para a Graph API tem de ser autenticado, acrescentando um Token Web JSON (JWT) no cabeçalho de autorização do pedido. Este token é adquirida ao efetuar um pedido para ponto final de token do Azure AD e fornecer credenciais válidas. Pode utilizar o fluxo de credenciais de cliente OAuth 2.0 ou o código de autorização conceder fluxo adquirir um token para chamar o gráfico. Para obter mais informações, [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorização baseada em funções (RBAC)**: grupos de segurança são utilizados para efetuar o RBAC na Graph API. Por exemplo, se pretende determinar se um utilizador tem acesso a um recurso específico, a aplicação pode chamar o [Verifique a associação de grupo (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) operação, que devolve true ou false.
* **Consulta diferencial**: Se pretender verificar a existência de alterações num diretório entre dois períodos de tempo sem ter de efetuar consultas frequentes para a Graph API, pode efetuar um pedido de consulta diferencial. Este tipo de pedido irá devolver apenas as alterações efetuadas entre o pedido de consulta diferencial anterior e o pedido atual. Para obter mais informações, consulte [do Azure AD Graph API diferencial consulta](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensões de diretórios**: Se estiver a desenvolver uma aplicação que necessita para ler ou escrever propriedades exclusivas para objetos de diretório, pode registar e utilizar valores de extensão com a Graph API. Por exemplo, se a sua aplicação precisar de uma propriedade de ID de Skype para cada utilizador, pode registar a nova propriedade no diretório e estará disponível em todos os objetos de utilizador. Para obter mais informações, consulte [extensões do Azure AD Graph API do esquema](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegidos por âmbitos de permissões**: AAD Graph API expõe âmbitos de permissões que permitem o acesso secure/autorizado aos dados do AAD e suportam uma variedade de tipos de aplicação de cliente, incluindo:
  
  * aqueles com uma interface de utilizador que recebem delegado acesso aos dados através de autorização do utilizador com sessão iniciada (delegado)
  * as que utilizam aplicações-definem o controlo de acesso baseado em funções, tais como clientes de serviço/daemon (funções de aplicação)
    
    Ambos delegados e âmbitos de permissões de função de aplicação representam um privilégio exposto pela Graph API e podem ser pedidos por aplicações de cliente através de permissões de registo de aplicação [funcionalidades no portal do Azure](https://portal.azure.com). Os clientes podem verificar se a âmbitos de permissões concedidos aos mesmos inspecionando a afirmação de âmbito ("scp") foi recebida no token de acesso para as permissões delegadas e as funções ("funções") de afirmação para permissões de função de aplicação. Saiba mais sobre [do Azure AD Graph API permissão âmbitos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

## <a name="scenarios"></a>Cenários
A Graph API permite vários cenários de aplicação. Os cenários seguintes são as mais comuns:

* **Linha de negócio (único inquilino) aplicação**: neste cenário, um programador de enterprise funciona de uma organização que tem uma subscrição do Office 365. O programador está a criar uma aplicação web que interage com o Azure AD para efetuar tarefas de tais atribuir uma licença a um utilizador. Esta tarefa requer acesso para a Graph API, pelo que regista o programador, o único inquilino aplicação no Azure AD e configura de leitura e escrita permissões para a Graph API. Em seguida, a aplicação está configurada para utilizar as suas próprias credenciais ou de utilizador atualmente início de sessão para adquirir um token para chamar a API de gráfico.
* **Software como uma aplicação de serviço (multi-inquilino)**: neste cenário, um fabricante independente de software (ISV) está a desenvolver aplicações web do multi-inquilino alojado que fornece funcionalidades de gestão de utilizadores de outras organizações que utilizam o Azure AD. Estas funcionalidades requerem acesso a objetos de diretório e, por isso, a aplicação tem de chamar Graph API. O programador regista a aplicação no Azure AD, configura-o para requerer a leitura e escrita permissões para a Graph API e, em seguida, ativa o acesso externo para que as outras organizações podem autorizar a utilizar a aplicação no seu diretório. Quando um utilizador na outra organização efetua a autenticação para a aplicação pela primeira vez, é apresentada uma caixa de diálogo de consentimento com as permissões que a aplicação está a solicitar.  Os conceder consentimento, em seguida, irá dar-a aplicação pediu permissões para a Graph API no diretório do utilizador. Para obter mais informações sobre a arquitetura de consentimento, consulte [descrição geral do Framework consentimento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Veja Também
[Guia de introdução ao Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Documentação de REST de gráfico do AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guia para programadores do Azure Active Directory](active-directory-developers-guide.md)

