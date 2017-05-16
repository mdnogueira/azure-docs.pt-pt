---
title: "Criar a primeira aplicação Web ASP.NET no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação ASP.NET simples."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/05/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 773d616b2a5815158971d63d005fae605450ac98
ms.contentlocale: pt-pt
ms.lasthandoff: 05/08/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Criar a primeira aplicação Web ASP.NET no Azure em cinco minutos

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este Manual de Início Rápido ajuda-o a implementar a sua primeira aplicação Web ASP.NET no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos. Quando tiver terminado, terá uma aplicação Web simples a funcionar na cloud.

![Aplicação Web ASP.NET no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como utilizar o Visual Studio 2017 para criar e implementar uma aplicação Web ASP.NET no Azure. Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

No Visual Studio, crie um projeto com `Ctrl`+`Shift`+`N`.

Na caixa de diálogo **Novo Projeto**, clique em **Visual C# > Web > Aplicação Web ASP.NET (Arquitetura .NET)**.

Dê à aplicação o nome **myFirstAzureWebApp** e clique em **OK**.
   
![Caixa de diálogo Novo Projeto](./media/app-service-web-get-started-dotnet/new-project.png)

Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Neste tutorial, selecione o modelo **MVC** e confirme que a autenticação está definida como **Sem Autenticação**.
      
Clique em **OK**.

![Caixa de diálogo Novo Projeto ASP.NET](./media/app-service-web-get-started-dotnet/select-mvc-template.png)

## <a name="publish-to-azure"></a>Publicar no Azure

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **myFirstAzureWebApp** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-get-started-dotnet/solution-explorer-publish.png)

Certifique-se de que o **Serviço de Aplicações do Microsoft Azure** está selecionado e clique em **Publicar**.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

É aberta a caixa de diálogo **Criar Serviço de Aplicações**, que o ajuda a criar todos os recursos do Azure necessários para executar a sua aplicação Web ASP.NET no Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Criar Serviço de Aplicações**, clique em **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se já tiver sessão iniciada numa conta Microsoft, confirme que esta inclui a sua subscrição do Azure. Se a subscrição do Azure não estiver na conta Microsoft em que tem sessão iniciada, clique nesta para adicionar a conta correta.
   
![Iniciar sessão no Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

Depois de iniciar sessão, está pronto para criar todos os recursos de que precisa para a sua aplicação Web do Azure nesta caixa de diálogo.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Primeiro, tem de criar um _grupo de recursos_. 

> [!NOTE] 
> Um grupo de recursos é um contentor lógico em que os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento são implementados e geridos.
>
>

Junto a **Grupo de recursos**, clique em **Novo**.

Dê ao grupo de recursos o nome **myResourceGroup** e clique em **OK**.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

A sua aplicação Web do Azure também precisa de um _plano do Serviço de Aplicações_. 

> [!NOTE]
> Os planos do Serviço de Aplicações representam a coleção de recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano do Serviço de Aplicações partilham os recursos definidos pelo mesmo, o que lhe permite economizar quando alojar várias aplicações. 
>
> Os planos do Serviço de Aplicações definem:
>
> - Região (Europa do Norte, E.U.A. Leste, Sudeste Asiático)
> - Tamanho da Instância (Pequena, Média, Grande)
> - Contagem de Escalas (uma, duas, três instâncias, etc.) 
> - SKU (Gratuito, Partilhado, Básico, Standard, Premium)
>
>

Junto a **Plano do Serviço de Aplicações**, clique em **Novo**. 

Na caixa de diálogo **Configurar o Plano do Serviço de Aplicações**, configure o plano novo com as definições seguintes:

- **Plano do Serviço de Aplicações**: escreva **myAppServicePlan**. 
- **Localização**: escolha **Europa Ocidental** ou outra região que pretenda.
- **Tamanho**: escolha **Livre** ou qualquer outro [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/) que pretenda.

Clique em **OK**.

![Criar plano do App Service](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

## <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

A única coisa que falta fazer agora é dar um nome à aplicação Web. Em **Nome da Aplicação Web**, escreva um nome exclusivo para a mesma. Este nome vai ser utilizado como parte do nome DNS predefinido da sua aplicação (`<app_name>.azurewebsites.net`), pelo que tem de ser exclusivo relativamente a todas as aplicações no Azure. Posteriormente, pode mapear um nome de domínio personalizado para a aplicação antes de a expor aos utilizadores.

Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

Clique em **Criar** para começar a criar os recursos do Azure.

![Configurar o nome da aplicação Web](./media/app-service-web-get-started-dotnet/web-app-name.png)

Quando o assistente concluir a criação dos recursos do Azure, publica automaticamente a sua aplicação Web ASP.NET para o Azure pela primeira vez e, em seguida, inicia a aplicação Web do Azure publicada no seu browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/app-service-web-get-started-dotnet/published-azure-web-app.png)

O URL utiliza o nome da aplicação Web que especificou anteriormente, com o formato `http://<app_name>.azurewebsites.net`. 

Parabéns, a primeira aplicação Web ASP.NET está em execução em direto no Serviço de Aplicações do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

É muito fácil de atualizar e reimplementar no Azure. Vamos fazer uma atualização à home page.

No **Explorador de Soluções**, abra **Views\Home\Index.cshtml**.

Localize a etiqueta HTML `<div class="jumbotron">`, junto à parte de cima, e substitua toda a etiqueta pelo código seguinte:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Para reimplementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções**, e selecione **Publicar**.

Na página de publicação, clique em **Publicar**.

Quando o Visual Studio terminar, inicia a aplicação Web do Azure atualizada no browser.

![Aplicação Web ASP.NET atualizada no Azure](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao portal do Azure para ver a aplicação Web que acabou de criar. 

Para tal, inicie sessão em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

Chegou ao _painel_ da sua aplicação Web (uma página do portal que se abre na horizontal). 

Por predefinição, o painel da sua aplicação Web mostra a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.  

![Painel Serviço de Aplicações no portal do Azure](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Os separadores no lado esquerdo do painel mostram as várias páginas de configuração que pode abrir. A lista seguinte dá-lhe apenas algumas das possibilidades:

- Mapear um nome DNS personalizado
- Vincular um certificado SSL personalizado
- Configurar a implementação contínua
- Aumentar e reduzir verticalmente
- Adicionar a autenticação do utilizador

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar a sua primeira aplicação Web do Azure, pode clicar **Eliminar**, na página **Descrição geral**. No entanto, existe uma forma melhor de eliminar tudo o que criou neste guia de introdução. Na página **Descrição geral** da aplicação Web, clique no grupo de recursos para abrir o painel do mesmo. 

![Aceder ao grupo de recursos a partir do painel Serviço de Aplicações](./media/app-service-web-get-started-dotnet/access-resource-group.png)

No painel do grupo de recursos, pode ver o plano do Serviço de Aplicações e a aplicação do Serviço de Aplicações que o Visual Studio criou por si. 

Na parte superior do painel, clique em **Eliminar**. 

<!--![Delete resource group in Azure portal](./media/app-service-web-get-started-dotnet/delete-resource-group.png)-->

No painel de confirmação, escreva o nome do grupo de recursos, **myResourceGroup**, na caixa de texto, e clique em **Eliminar**, para confirmar.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como criar uma aplicação do ASP.NET no Azure com a Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Explore os scripts do PowerShell das aplicações Web de exemplo](app-service-powershell-samples.md)

