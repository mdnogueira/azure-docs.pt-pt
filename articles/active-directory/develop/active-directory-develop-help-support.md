---
title: "Opções de ajuda e suporte para programadores de identidade do Azure | Microsoft Docs"
description: "Saber como obter ajuda e suporte para perguntas relacionadas com o desenvolvimento e problemas ao criar a aplicação que se integram com identidades do Microsoft Azure (Azure Active Directory e MSA)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7c382da9bd9032b30f7c620e839c41c1756ba3f6
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="support-and-help-options-for-developers"></a>Opções de ajuda e suporte para programadores 

Independentemente se apenas a iniciar a integração com o Azure Active Directory, identidades da Microsoft ou o Microsoft Graph API, ou quando estiver a implementar uma nova funcionalidade da aplicação, há horas que precisa de obter ajuda da Comunidade ou compreender o suporta opções que tem como um programador. Este artigo ajuda-o a compreender estas opções, abaixo de um resumo:

> [!div class="checklist"]
> * Pesquisa para verificar se a sua pergunta problema tem não foram respondida pela Comunidade ou se um documentação existente para a funcionalidade está a tentar implementar já existe
> * Em alguns casos, pretender utilizar das nossas ferramentas de suporte para o ajudar a depurar específico do problema
> * Se não conseguir encontrar a resposta do que necessita, poderá querer fazer uma pergunta nos *Stack Overflow*
> * Se encontrar um problema com uma das nossas bibliotecas de autenticação, emitir uma *GitHub* problema
> * Por fim, se necessário falar com alguém, pode querer abrir um pedido de suporte


## <a name="search"></a>Pesquisa

Se tiver uma pergunta relacionados com o desenvolvimento, é possível encontrar a resposta tem de na nossa documentação, nosso [github exemplos](https://github.com/azure-samples), ou respostas para [Stack Overflow](https://www.stackoverflow.com) perguntas.

### <a name="scoped-search"></a>Pesquisa de âmbito
Para obter resultados mais rápidos, âmbito a pesquisa para Stack Overflow, documentação e nossos exemplos de código através do seguinte no seu [motor de busca favorito](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Onde *{Your os termos da procura}* está as procurar palavras-chave.
<br/>

## <a name="use-our-development-support-tools"></a>Utilizar o nosso ferramentas de suporte de desenvolvimento

|Ferramenta  |Descrição  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Colar uma tokens de acesso ou um ID de descodificar os valores e nomes de afirmações |
|[Analisador de código de erro](https://apps.dev.microsoft.com/portal/tools/errors)| Cole um código de erro que recebeu durante o início de sessão ou páginas para ver as causas possíveis e remediações de consentimento |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que permite fazer pedidos e ver respostas contra a Graph API do Microsoft|

<br/>

[![Capacidade excedida da pilha](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Publique uma pergunta para Stack Overflow

Capacidade excedida da pilha é o canal preferencial para perguntas relacionadas com o desenvolvimento - em ambos os membros da Comunidade como Microsoft equipa membros diretamente envolvidas no ajudando-o a resolver o problema.

Se não é possível localizar uma resposta para o seu problema através da pesquisa, submeter uma nova pergunta para Stack Overflow: Utilize um dos seguintes etiquetas ao fazer perguntas para ajudar a identificar, de Comunidade, em seguida, responder a sua questão num útil:

|Componente/área  |Etiquetas  |
|---------|---------|
|Biblioteca da ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Biblioteca MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN middleware  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[B2B do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[b2b do azure-ad]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Qualquer outra área relacionadas com autenticação ou autorização tópicos |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> As seguintes mensagens de Stack Overflow contenham sugestões sobre como tornar perguntas e sugestões sobre como adicionar código fonte - seguir estas diretrizes podem ajudar a aumentar as hipóteses de membros da Comunidade avaliar e responder rapidamente a sua pergunta:  
> - [Como é que posso fazer uma pergunta boa](https://stackoverflow.com/help/how-to-ask)
> - [Como criar um mínima, concluir e verificável exemplo](https://stackoverflow.com/help/mcve)

<br/>


[![Capacidade excedida da pilha](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Criar um problema no GitHub

 Se encontrar um erro ou problema relacionado com a nossa bibliotecas, emitir um problema no nosso repositórios do GitHub. Porque as nossas bibliotecas são de fonte aberta, também são livre para submeter um pedido de solicitação bem. O seguinte artigo contém uma lista de bibliotecas e os seus repositórios do GitHub:

- [ADAL, MSAL e Owin middleware](active-directory-authentication-libraries.md) bibliotecas e repositórios do GitHub

<br/>

## <a name="open-a-support-request"></a>Abra um pedido de suporte

Se precisar de comunicar com alguém, pode abrir um pedido de suporte. Se for um cliente do Azure, existem várias opções de suporte disponíveis. Para comparar planos, consulte [nesta página](https://azure.microsoft.com/support/plans/). Suporte para programadores também está disponível para clientes do Azure. Para obter informações sobre como comprar planos de suporte para programadores, consulte [nesta página](https://azure.microsoft.com/support/plans/developer/).

- Se já tiver um Azure suporta a planear, [abrir um pedido de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Se não for um cliente do Azure, também pode abrir um pedido de suporte com a Microsoft via [nosso suporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Também pode tentar [nosso agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou colocar questões.

### <a name="free-chat-support-for-a-limited-time"></a>Suporte de chat gratuita para um período de tempo limitado

Também pode utilizar o nosso suporte do chat - o que é gratuito em Partners da Microsoft para um período de tempo limitado. Se a sua empresa não é um Partner da Microsoft, pode inscrever gratuitamente e obter outras vantagens acedendo [aqui](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Após a inscrição da sua empresa, pode iniciar o pedido de chat [aqui](https://aka.ms/devchat).