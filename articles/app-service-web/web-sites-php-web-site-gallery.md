---
title: "Criar uma aplicação Web WordPress no Serviço de Aplicações do Azure | Microsoft Docs"
description: "Saiba como criar uma nova aplicação Web do Azure para um blogue do WordPress através do Portal do Azure."
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 193ae094-0d7c-4749-a09b-ff4b1240149e
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: hero-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 83ef925a8268873d09c4a1611a1f2341acd92ba7


---
# <a name="create-a-wordpress-web-app-in-azure-app-service"></a>Criar uma aplicação Web WordPress no App Service do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como implementar um site do blogue do WordPress a partir do Azure Marketplace.

Após concluir os passos deste tutorial, o seu próprio site do blogue do WordPress estará operacional na nuvem.

![Site do WordPress](./media/web-sites-php-web-site-gallery/wpdashboard.png)

Irá aprender:

* Como localizar um modelo de aplicação no Azure Marketplace.
* Como criar uma aplicação Web no App Service do Azure com base no modelo.
* Como configurar as definições do App Service do Azure para a nova aplicação Web e a base de dados.

O Azure Marketplace disponibiliza uma vasta gama de Web Apps populares desenvolvidas pela Microsoft, empresas externas e iniciativas de software open source. As Web Apps são criadas numa vasta gama de arquiteturas populares, tais como [PHP](/develop/nodejs/) neste exemplo do WordPress, [.NET](/develop/net/), [Node.js](/develop/nodejs/), [Java](/develop/java/) e [Python](/develop/python/), entre outras. Para criar uma aplicação Web no Azure Marketplace, o único software de que necessita é o browser que utiliza para o [Portal do Azure](https://portal.azure.com/). 

O site do WordPress que implementa neste tutorial utiliza o MySQL para a base de dados. Se em vez disso, pretender utilizar a SQL Database para a base de dados, consulte o artigo [Projeto Nami](http://projectnami.org/). O **Projeto Nami** também está disponível através do Marketplace.

> [!NOTE]
> Para concluir este tutorial, necessita de uma conta do Microsoft Azure. Se não tiver uma conta, pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/). Aqui, pode criar imediatamente uma aplicação Web de arranque de curta duração no App Service — sem cartões de crédito e sem compromissos.
> 
> 

## <a name="select-wordpress-and-configure-for-azure-app-service"></a>Selecionar o WordPress e configurar para o App Service do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Novo**.
   
    ![Criar Novo][5]
3. Procure **WordPress** e clique em **WordPress**. Se, em vez do MySQL, pretender utilizar a SQL Database, pesquise **Projeto Nami**.
   
    ![WordPress na lista][7]
4. Depois de ler a descrição da aplicação WordPress, clique em **Criar**.
   
    ![Criar](./media/web-sites-php-web-site-gallery/create.png)
5. Introduza um nome para a aplicação Web na caixa **Aplicação Web**.
   
    Este nome tem de ser exclusivo no domínio azurewebsites.net porque o URL da aplicação Web será {name}.azurewebsites.net. Se o nome introduzido não for exclusivo, é apresentado um ponto de exclamação vermelho na caixa de texto.
6. Se tiver mais do que uma subscrição, escolha a que pretende utilizar. 
7. Selecione um **Grupo de Recursos** ou crie um novo.
   
    Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
8. Selecione um **Plano do App Service/Localização** ou crie um novo.
   
    Para obter mais informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)    
9. Clique em **Base de Dados** e, em seguida, no painel **Nova Base de Dados MySQL**, forneça os valores necessários para configurar a base de dados MySQL.
   
    a. Introduza um novo nome ou deixe o nome predefinido.
   
    b. Deixe o **Tipo de Base de Dados** definido como **Partilhado**.
   
    c. Escolha a mesma localização que escolheu para a aplicação Web.
   
    d. Escolha um escalão de preço. O (gratuito com ligações e espaço em disco mínimos) é adequado para este tutorial.
10. No painel **Nova Base de Dados MySQL**, clique em **OK**. 
11. No painel **WordPress**, aceite os termos legais e clique em **Criar**. 
    
     ![Configurar aplicação Web](./media/web-sites-php-web-site-gallery/configure.png)
    
     Normalmente, o App Service do Azure cria a aplicação Web em menos de um minuto. Pode ver o progresso clicando no ícone de campainha na parte superior da página do portal.
    
     ![Indicador de progresso](./media/web-sites-php-web-site-gallery/progress.png)

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerir a aplicação Web do WordPress
1. Quando a criação da aplicação Web estiver concluída, navegue no Portal do Azure para o grupo de recursos no qual criou a aplicação para poder ver a aplicação Web e a base de dados.
   
    O recurso adicional com o ícone de lâmpada é o [Application Insights](/services/application-insights/), que fornece serviços de monitorização para a aplicação Web.
2. No painel **Grupo de recursos**, clique na linha da aplicação Web.
   
    ![Configurar aplicação Web](./media/web-sites-php-web-site-gallery/resourcegroup.png)
3. No painel Aplicação Web, clique em **Procurar**.
   
    ![URL do site][browse]
4. Na página **Bem-vindo** do WordPress, introduza as informações de configuração requeridas pelo WordPress e clique em **Instalar o WordPress**.
   
    ![Configurar WordPress](./media/web-sites-php-web-site-gallery/wpconfigure.png)
5. Inicie sessão com as credenciais que criou na página **Bem-vindo**.  
6. Abre-se a página do Dashboard do seu site.    
   
    ![Site do WordPress](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## <a name="next-steps"></a>Passos seguintes
Aprendeu a criar e implementar uma aplicação Web PHP a partir da galeria. Para obter mais informações sobre como utilizar o PHP no Azure, consulte o [Centro de Programadores do PHP](/develop/php/).

Para obter mais informações sobre como trabalhar com as Web Apps do App Service, consulte as ligações no lado esquerdo da página (para browsers com janelas largas) ou na parte superior da página (para browsers com janelas estreitas). 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte [App Service do Azure e o respetivo impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[browse]: ./media/web-sites-php-web-site-gallery/browse-web.png



<!--HONumber=Jan17_HO3-->


