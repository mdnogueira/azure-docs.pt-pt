---
title: "Início rápido para o Azure AD Graph API | Microsoft Docs"
description: "O Active Directory Graph API do Azure fornece acesso programático para o Azure AD através de pontos finais da API de REST de OData. As aplicações podem utilizar a Graph API para efetuar a criar, ler, atualizar e eliminar operações (CRUD) em dados de diretório e objetos."
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mbaldwin
editor: 
tags: 
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: fad5c315a247673b7a2ad52b4a78b49c567a997a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Início rápido para o Azure AD Graph API
A Graph API do Azure Active Directory (AD) fornece acesso programático para o Azure AD através de pontos finais da API de REST de OData. As aplicações podem utilizar a Graph API para efetuar a criar, ler, atualizar e eliminar operações (CRUD) em dados de diretório e objetos. Por exemplo, pode utilizar a Graph API para criar um novo utilizador, ver ou atualizar propriedades do utilizador, altere a palavra-passe do utilizador, selecione a associação a grupos de acesso baseado em funções, desative ou elimine o utilizador. Para obter mais informações sobre as funcionalidades de Graph API e cenários de aplicações, consulte [AD Graph API do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [pré-requisitos do Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Recomendamos vivamente que utilize o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da Graph API do Azure AD para aceder aos recursos do Azure Active Directory. Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Existe um número muito limitado de cenários nos quais a Graph API do Azure AD ainda poderá ser adequada. Para obter mais informações, veja a mensagem do blogue [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph ou Graph do Azure AD), no Office Dev Center.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Como construir um URL de API do gráfico
No Graph API, para aceder aos dados de diretório e objetos (por outras palavras, recursos ou entidades) relativamente ao qual pretende efetuar as operações CRUD, pode utilizar os URLs com base no protocolo Open Data (OData). Os URLs utilizados no Graph API consistem em quatro partes principais: raiz, o identificador de inquilino, o caminho do recurso e opções de cadeia de consulta do serviço: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Executar o exemplo do seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Serviço raiz**: AD Graph API do Azure, a raiz do serviço é sempre https://graph.windows.net.
* **Identificador de inquilino**: Esta secção pode ser um nome de domínio (registada) verificado, no exemplo anterior, contoso.com. Pode também ser um ID de objeto do inquilino ou "myorganization" ou "me" alias. Para obter mais informações, consulte [endereçamento entidades e operações na Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
* **Caminho do recurso**: Esta secção de um URL identifica o recurso a Interagiu com (utilizadores, grupos, um utilizador específico, ou um grupo específico, etc.) No exemplo acima, é os nível superior "grupos" para o endereço que conjunto de recursos. Também pode resolver uma entidade específica, por exemplo "utilizadores / {objectId}" ou "utilizadores/userPrincipalName".
* **Parâmetros de consulta**: um ponto de interrogação (?) separa a secção de caminho de recurso da secção de parâmetros de consulta. O parâmetro de consulta "api-version" é necessário em todos os pedidos da Graph API. A Graph API também suporta as seguintes opções de consulta de OData: **$filter**, **$orderby**, **$expand**, **$top**, e **$format**. As seguintes opções de consulta não são atualmente suportadas: **$count**, **$inlinecount**, e **$skip**. Para obter mais informações, consulte [suportadas consultas, filtros e as opções de paginação no Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versões de API do gráfico
Especifique a versão de um pedido de Graph API no parâmetro de consulta "api-version". Para versão 1,5 e posterior, utilize um valor de versão numérica; API-version = 1.6. Para versões anteriores, utilize uma cadeia de data que respeita o formato AAAA-MM-DD; Por exemplo, a api-version = 2013-11-08. Para funcionalidades de pré-visualização, utilize a cadeia "beta"; Por exemplo, a api-version = beta. Para obter mais informações sobre as diferenças entre versões da Graph API, consulte [controlo de versões do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados da Graph API
Para devolver o ficheiro de metadados da Graph API, adicione o segmento de "$metadata" após o identificador de inquilino no URL. por exemplo, o seguinte URL devolve metadados para uma empresa de demonstração: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Pode introduzir este URL na barra de endereço de um browser para ver os metadados. O documento de metadados CSDL devolvido descreve as entidades e tipos complexos, as respetivas propriedades e as funções e ações expostas pela versão do Graph API pedida. O parâmetro api-version a omissão devolve metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns
[Azure AD Graph API consultas comuns](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) apresenta uma lista de consultas comuns que podem ser utilizadas com o Azure AD Graph, incluindo consultas que podem ser utilizadas para aceder a recursos de nível superior no seu diretório e as consultas para executar operações no seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` devolve da empresa informações para directory contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` apresenta uma lista de todos os objetos de utilizador no diretório contoso.com.

## <a name="using-the-graph-explorer"></a>Utilizar o Explorador do gráfico
Pode utilizar o Explorador do gráfico para a Azure AD Graph API para consultar os dados de diretório, como criar a sua aplicação.

Segue-se a saída verá se atualizasse navegue para o Explorador do gráfico, iniciar sessão e introduza `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para apresentar todos os utilizadores com sessão iniciada do diretório do utilizador:

![Explorador de api de gráfico do Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Explorador do gráfico**: para carregar a ferramenta, navegue até à [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Clique em **início de sessão** e inicie sessão com as suas credenciais de conta do Azure AD para executar o Explorador de gráfico no seu inquilino. Se executar o Explorador de gráfico contra o seu inquilino, utilizador ou o administrador tem de consentimento durante o início de sessão. Se tiver uma subscrição do Office 365, terá automaticamente um inquilino do Azure AD. As credenciais que utiliza para iniciar sessão Office 365 são, na verdade, contas do Azure AD, e pode utilizar estas credenciais com o Explorador de gráfico.

**Executar uma consulta**: ao executar uma consulta, escreva a sua consulta na caixa de texto de pedido e clique em **obter** ou clique em de **introduza** chave. Os resultados são apresentados na caixa de resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` apresenta uma lista de todos os objetos de grupo com sessão iniciada do diretório do utilizador.

Tenha em atenção as seguintes funcionalidades e limitações do Explorador do gráfico:

* Define a capacidade de conclusão automática no recurso. Para ver esta funcionalidade, clique na caixa de texto de pedido (onde aparece o URL da empresa). Pode selecionar um recurso definido na lista pendente.
* Suporta "me" e "myorganization" endereçamento aliases. Por exemplo, pode utilizar `https://graph.windows.net/me?api-version=1.6` para devolver o objeto de utilizador do utilizador com sessão iniciada ou `https://graph.windows.net/myorganization/users?api-version=1.6` para devolver todos os utilizadores no diretório atual.
* Uma secção de cabeçalhos de resposta. Pode ser utilizada nesta secção para ajudar a resolver problemas que ocorrem durante a execução de consultas.
* Um visualizador JSON para a resposta com capacidades de expansão e fechar.
* Não existe suporte para apresentar uma fotografia em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Utilizar o Fiddler para escrever para o diretório
Para efeitos deste guia de início rápido, pode utilizar o depurador de Web Fiddler para prática efetuar 'escrever' operações no diretório do Azure AD. Para obter mais informações e para instalar o Fiddler, consulte [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

No exemplo abaixo, utilizar o Fiddler Web depurador para criar um novo grupo de segurança 'MyTestGroup' no diretório do Azure AD.

**Obter um token de acesso**: para aceder ao Azure AD Graph, os clientes são necessárias para autenticar com êxito para o Azure AD pela primeira vez. Para obter mais informações, consulte [cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).

**Componha e executar uma consulta**: execute os seguintes passos:

1. Abra o Fiddler Web depurador e mudar para o **compositor** separador.
2. Uma vez que pretende criar um novo grupo de segurança, selecione **Post** como o método HTTP a partir do menu pendente. Para obter mais informações sobre operações e permissões num objeto de grupo, consulte [grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dentro de [referência da API do REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo junto a **Post**, escreva o seguinte como o URL do pedido: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.
   
   > [!NOTE]
   > Tem de substituir mytenantdomain com o nome de domínio do seu diretório do Azure AD.
   > 
   > 
4. No campo diretamente abaixo pendente Post, escreva o seguinte:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitui o &lt;seu token de acesso&gt; com o token de acesso para o diretório do Azure AD.
   > 
   > 
5. No **corpo do pedido** campo, escreva o seguinte:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Para obter mais informações sobre como criar grupos, consulte [criar grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre entidades do Azure AD e tipos que sejam expostos pelo gráfico e informações sobre as operações que podem ser efetuadas nos mesmos com o gráfico, consulte [referência da API do REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [AD Graph API do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Saiba mais sobre [do Azure AD Graph API permissão âmbitos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

