---
title: "Configurar definições da aplicação de função do Azure | Microsoft Docs"
description: "Saiba como configurar as definições de aplicação de função do Azure."
services: 
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: glenga
ms.openlocfilehash: a6cfcd939cb0f21d01fe849ef04619ec9c1c972a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Como gerir uma aplicação de função no portal do Azure 

As funções do Azure, uma aplicação de função fornece o contexto de execução para as suas funções individuais. Comportamentos de aplicação de função se aplicam a todas as funções alojadas por uma aplicação de função especificada. Este tópico descreve como configurar e gerir as suas aplicações de função no portal do Azure.

Para começar, vá para o [portal do Azure](http://portal.azure.com) e inicie sessão na sua conta do Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar a sua aplicação de função, consulte a seguinte página:

![Descrição geral da aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="manage-app-service-settings"></a>Separador de definições de aplicação de função

![Função descrição geral da aplicação no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

O **definições** separador é onde pode atualizar a versão de tempo de execução de funções utilizada pela sua aplicação de função. Também é onde irá gerir as chaves de anfitrião utilizadas para restringir o acesso HTTP para todas as funções alojadas pela aplicação de função.

As funções suporta o alojamento de consumo e planos de alojamento do serviço de aplicações. Para obter mais informações, consulte [escolher o plano de serviço correto para as funções do Azure](functions-scale.md). Para melhor previsão no plano de consumo, funções permite-lhe limitar a utilização de plataforma, definindo uma quota de utilização diária, em gigabytes segundos. Assim que a quota de utilização diária for atingida, a aplicação de função está parada. Uma aplicação de função parada devido a atingir a quota de gastos pode ser ativada novamente a partir do contexto do mesmo como estabelecer diariamente gastos quota. Consulte o [das funções do Azure a página de preços](http://azure.microsoft.com/pricing/details/functions/) para obter detalhes sobre faturação.   

## <a name="platform-features-tab"></a>Separador de funcionalidades de plataforma

![Separador de funcionalidades de plataforma de aplicação de função.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplicações de função executados na e são mantidas, pela plataforma do App Service do Azure. Como tal, as aplicações de função tem acesso a maioria das funcionalidades de plataforma de alojamento na web principal do Azure. O **funcionalidades da plataforma** separador é onde aceder as muitas funcionalidades da plataforma do serviço de aplicações que pode utilizar nas suas aplicações de função. 

> [!NOTE]
> Nem todas as funcionalidades do serviço de aplicações estão disponíveis quando uma aplicação de função é executado no consumo de plano de alojamento.

O resto deste tópico centra-se as seguintes funcionalidades de serviço de aplicações no portal do Azure, que são úteis para as funções:

+ [Editor de serviço de aplicações](#editor)
+ [Definições da aplicação](#settings) 
+ [Console](#console)
+ [Ferramentas Avançadas (Kudu)](#kudu)
+ [Opções de implementação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)
+ [Definição da API](#swagger)

Para obter mais informações sobre como trabalhar com definições de serviço de aplicações, consulte [configurar definições de serviço de aplicações do Azure](../app-service/web-sites-configure.md).

### <a name="editor"></a>Editor de serviço de aplicações

| | |
|-|-|
| ![Aplicação de função de editor do serviço de aplicações.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | O editor de serviço de aplicações é um editor no portal avançado que pode utilizar para modificar os ficheiros de configuração JSON e ficheiros de código igual. Escolha esta opção inicia um separador de browser separados com um editor de básico. Isto permite-lhe integrar com o repositório de Git, executar e depurar código e modificar as definições de aplicação de função. Este editor fornece um ambiente de desenvolvimento avançado para as suas funções comparado com o painel de aplicação de função predefinidos.    |

![O editor de serviço de aplicações](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Definições da aplicação

| | |
|-|-|
| ![Definições de aplicação de aplicação de função.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | O serviço de aplicações **definições da aplicação** painel é onde pode configurar e gerir versões framework, depuração remota, as definições de aplicação e as cadeias de ligação. Quando integrar a sua aplicação de função com outros serviços de terceiros e do Azure, pode modificar essas definições aqui. |

![Configurar as definições da aplicação](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Consola do

| | |
|-|-|
| ![Consola de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | A consola no portal é uma ferramenta de programador ideal quando que preferir interagir com a sua aplicação de função na linha de comandos. Comandos comuns incluem o diretório e criação de ficheiros e navegação, bem como executar scripts e ficheiros batch. |

![Consola de aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Ferramentas Avançadas (Kudu)

| | |
|-|-|
| ![Aplicação de função Kudu no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | As ferramentas avançadas para o serviço de aplicações (também conhecido como Kudu) fornecem acesso a funcionalidades avançadas de administrativas da sua aplicação de função. Do Kudu, gerir as informações do sistema, as definições de aplicação, as variáveis de ambiente, as extensões de site, os cabeçalhos de HTTP e variáveis de servidor. Também pode iniciar **Kudu** ao navegar para o ponto final SCM para a sua aplicação de função, como`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurar o Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opções de implementação

| | |
|-|-|
| ![Opções de implementação de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | As funções permite-lhe desenvolver o seu código de função no seu computador local. Em seguida, pode carregar o projeto de aplicação de função local para o Azure. Para além de carregamento FTP tradicional, funções permite-lhe implementar a aplicação de função utilizando soluções de integração contínua popular, como o GitHub, VSTS, Dropbox, Bitbucket e outras pessoas. Para obter mais informações, consulte [a implementação contínua para as funções do Azure](functions-continuous-deployment.md). Para carregar manualmente a utilizar FTP ou de local Git, terá também [configurar as suas credenciais de implementação](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplicação de função CORS no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para impedir a execução de código malicioso nos seus serviços, o serviço de aplicações bloqueia chamadas às suas aplicações de função a partir de origens externas. As funções suporta recursos de várias origens (CORS) para permitem-lhe definir uma "lista branca" de origens permitidas dos quais as suas funções podem aceitar pedidos remotos de partilha.  |

![Configurar a aplicação de função CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticação

| | |
|-|-|
| ![Autenticação da aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando as funções de utilizam um acionador HTTP, pode exigir chamadas para ser autenticada pela primeira vez. Serviço de aplicações suporta a autenticação do Azure Active Directory e inicie sessão com fornecedores de redes sociais, como o Facebook, a Microsoft e o Twitter. Para obter detalhes sobre a configuração de fornecedores de autenticação específicas, consulte [descrição geral da autenticação do App Service do Azure](../app-service/app-service-authentication-overview.md). |

![Configurar a autenticação para uma aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definição da API

| | |
|-|-|
| ![API de aplicação de função swagger de definição no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | As funções suporta Swagger para permitir que os clientes consumir mais facilmente as funções acionadas por HTTP. Para obter mais informações sobre como criar definições de API com Swagger, visite [introdução às API Apps e Swagger no Azure](../app-service/app-service-web-tutorial-rest-api.md). Também pode utilizar os Proxies de funções para definir uma único superfície de API para várias funções. Para obter mais informações, consulte [trabalhar com os Proxies de funções do Azure](functions-proxies.md). |

![Configurar a API da aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passos seguintes

+ [Configurar as definições do App Service do Azure](../app-service/web-sites-configure.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)



