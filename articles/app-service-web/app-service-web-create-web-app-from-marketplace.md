---
title: "Criar uma aplicação Web no Azure Marketplace | Microsoft Docs"
description: "Saiba como criar uma nova aplicação Web do WordPress no Azure Marketplace utilizando o Portal do Azure."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Criar uma aplicação Web no Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

O Azure Marketplace disponibiliza uma vasta gama de Web Apps populares desenvolvidas pela Microsoft, empresas externas e iniciativas de software open source. Por exemplo, WordPress, Umbraco CMS, Drupal, etc. Estas Web Apps são criadas numa vasta gama de arquiteturas populares, tais como [PHP] neste exemplo do WordPress, [.NET], [Node.js], [Java] e [Python], entre outras. Para criar uma aplicação Web no Azure Marketplace, o único software de que necessita é o browser que utiliza para o [Portal do Azure].

Neste tutorial, ficará a saber como:

* Localizar e criar uma aplicação Web no Serviço de Aplicações do Azure com base num modelo do Azure Marketplace.
* Configure as definições do Serviço de Aplicações do Azure da nova aplicação Web.
* Inicie e faça a gestão da aplicação Web.

Para efeitos deste tutorial, irá implementar um site de blogue do WordPress a partir do Azure Marketplace. Após concluir os passos deste tutorial, o seu próprio site do WordPress estará operacional na nuvem.

![Exemplo de dashboard da aplicação Web do WordPress][WordPressDashboard1]

O site do WordPress que irá implementar neste tutorial utiliza o MySQL para a base de dados. Se, em alternativa, pretender utilizar a SQL Database para a base de dados, consulte [Projeto Nami], que também está disponível através do Azure Marketplace.

> [!NOTE]
> Para concluir este tutorial, necessita de uma conta do Microsoft Azure. Se não tiver uma conta, pode [ativar os benefícios de subscritor do Visual Studio][ activate]ou [inscrever-se numa avaliação gratuita][free trial].
> 
> Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service]. A partir daí, pode criar imediatamente uma aplicação Web de arranque de curta duração no App Service — sem necessidade de cartões de crédito e sem compromissos.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Localizar e Criar uma Aplicação Web no Serviço de Aplicações do Azure
1. Inicie sessão no [Portal do Azure].
2. Clique em **Novo**.
   
    ![Criar um novo recurso do Azure][MarketplaceStart]
3. Procure **WordPress** e clique em **WordPress**. (Se, em vez do MySQL, pretender utilizar a SQL Database, procure **Projeto Nami**.)
   
    ![Procurar o WordPress no Marketplace][MarketplaceSearch]
4. Depois de ler a descrição da aplicação WordPress, clique em **Criar**.
   
    ![Criar aplicação Web do WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configure as Definições do Serviço de Aplicações do Azure da Nova Aplicação Web
1. Depois de ser criada uma nova aplicação Web, é apresentado o painel de definições do WordPress, que irá utilizar para concluir os seguintes passos:
   
    ![Configurar definições da aplicação Web do WordPress][ConfigStart]
2. Introduza um nome para a aplicação Web na caixa **Aplicação Web**.
   
    Este nome tem de ser exclusivo no domínio azurewebsites.net porque o URL da aplicação Web será *{name}*.azurewebsites.net. Se o nome introduzido não for exclusivo, é apresentado um ponto de exclamação vermelho na caixa de texto.
   
    ![Configurar o nome da aplicação Web do WordPress][ConfigAppName]
3. Se tiver mais do que uma subscrição, escolha a que pretende utilizar.
   
    ![Configurar a subscrição da aplicação Web][ConfigSubscription]
4. Selecione um **Grupo de Recursos** ou crie um novo.
   
    Para mais informações sobre grupos de recursos, veja [Azure Resource Manager overview (Descrição Geral do Azure Resource Manager)][ResourceGroups].
   
    ![Configurar o grupo de recursos da aplicação Web][ConfigResourceGroup]
5. Selecione um **Plano do App Service/Localização** ou crie um novo.
   
    Para obter mais informações sobre os planos do App Service, consulte [Azure App Service plans overview (Descrição geral dos planos do Serviço de Aplicações do Azure)][AzureAppServicePlans].
   
    ![Configurar o plano de serviço da aplicação Web][ConfigServicePlan]
6. Clique em **Base de Dados** e, em seguida, no painel **Nova Base de Dados MySQL**, forneça os valores necessários para configurar a base de dados MySQL.
   
    a. Introduza um novo nome ou deixe o nome predefinido.
   
    b. Deixe o **Tipo de Base de Dados** definido como **Partilhado**.
   
    c. Escolha a mesma localização que escolheu para a aplicação Web.
   
    d. Escolha um escalão de preço. O **Mercúrio** – que é gratuito com ligações e espaço em disco mínimos – é perfeitamente adequado para este tutorial.
   
    e. No painel **Nova Base de Dados MySQL**, aceite os termos legais e clique em **OK**.
   
    ![Configurar as definições da base de dados da aplicação Web][ConfigDatabase]
7. No painel **WordPress**, aceite os termos legais e clique em **Criar**.
   
    ![Concluir as definições da aplicação Web e clicar em OK][ConfigFinished]
   
    Normalmente, o App Service do Azure cria a aplicação Web em menos de um minuto. Pode ver o progresso clicando no ícone de campainha na parte superior da página do portal.
   
    ![Indicador de progresso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerir a aplicação Web do WordPress
1. Quando a criação da aplicação Web estiver concluída, navegue no Portal do Azure para o grupo de recursos no qual criou a aplicação para poder ver a aplicação Web e a base de dados.
   
    O recurso adicional com o ícone de lâmpada é o [Application Insights][ApplicationInsights], que fornece serviços de monitorização para a aplicação Web.
2. No painel **Grupo de recursos**, clique na linha da aplicação Web.
   
    ![Selecionar a aplicação Web do WordPress][WordPressSelect]
3. No painel Aplicação Web, clique em **Procurar**.
   
    ![Navegar até à aplicação Web do WordPress][WordPressBrowse]
4. Se lhe for pedido que selecione o idioma para o seu blogue do WordPress, selecione o idioma pretendido e clique em **Continuar**.
   
    ![Configurar o idioma para a aplicação Web do WordPress][WordPressLanguage]
5. Na página **Bem-vindo** do WordPress, introduza as informações de configuração requeridas pelo WordPress e clique em **Instalar o WordPress**.
   
    ![Configurar as definições da aplicação Web do WordPress][WordPressConfigure]
6. Inicie sessão com as credenciais que criou na página **Bem-vindo**.  
7. A página Dashboard do site abre-se e apresenta as informações que forneceu.    
   
    ![Ver o dashboard do WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como criar e implementar uma aplicação Web de exemplo a partir do Azure Marketplace.

Para obter mais informações sobre como trabalhar com as Web Apps do App Service, consulte as ligações no lado esquerdo da página (para browsers com janelas largas) ou na parte superior da página (para browsers com janelas estreitas).

Para obter mais informações acerca do desenvolvimento de Web Apps do WordPress, consulte [Desenvolvimento do WordPress no Serviço de Aplicações do Azure][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Experimentar o App Service]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal do Azure]: https://portal.azure.com/
[Projeto Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Jan17_HO3-->


