---
title: "Implementar uma aplicação Web do Umbraco no portal do Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação ASP.NET de exemplo. Veja os seus resultados de imediato."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.lasthandoff: 04/06/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Implementar uma aplicação Web do Umbraco no portal do Azure em cinco minutos

Este tutorial ajuda-o a implementar uma aplicação Web do [Umbraco](https://our.umbraco.org/) no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em minutos.

![aplicação do Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Pré-requisitos
Precisa de uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Implementar a aplicação ASP.NET
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Esta hiperligação é um atalho para configurar de imediato uma nova aplicação do Umbraco no portal do Azure.

3. Em **Nome da aplicação**, escreva o nome de uma aplicação Web. Verá uma marca de verificação verde na caixa se o nome for exclusivo no domínio `azurewebsites.net`.
   
5. Em **Grupo de Recursos**, clique em **Criar novo** para criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) e, em seguida, atribua-lhe um nome.

7. Clique em **plano do Serviço de Aplicações/Localização** > **Criar Novo**. Configure o [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) conforme indicado:

    - Em **plano do Serviço de Aplicações**, escreva o nome pretendido.
    - Em **Localização**, escolha uma localização para alojar o seu plano.
    - Clique em **Escalão de preço**, selecione **F1 Gratuito** ou outro escalão que lhe seja conveniente e, em seguida, clique em **Selecionar**.
    - Clique em **OK**.

    A configuração do CMS Umbraco deverá agora ser semelhante à seguinte captura de ecrã:

    ![Configuração em curso - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Clique em **Base de Dados SQL** > **Criar uma nova base de dados**. Configure a Base de Dados SQL, conforme indicado:

    - Em **Nome**, escreva um nome, tal como **myDB**.
    - Clique em **Escalão de preço**, selecione **F Gratuito** ou outro escalão que lhe seja conveniente e, em seguida, clique em **Selecionar**.
    - Clique em **Servidor de destino** > **Criar um novo servidor**. Configure o servidor de base de dados, conforme indicado:

        - Em **Nome do servidor**, escreva um nome de servidor. Verá uma marca de verificação verde na caixa se o nome for exclusivo no domínio `.database.windows.net`.
        - Em **Início de sessão de administrador do servidor**, escreva o nome de utilizador administrador pretendido.
        - Em **Palavra-passe** e **Confirmar palavra-passe**, escreva a palavra-passe pretendida.
        - Em Localização, selecione a mesma localização que utiliza para a aplicação Web.
        - Certifique-se de que a opção **Permitir o acesso dos serviços do Azure ao servidor** está selecionada.
        - Clique em **Selecionar**.
    
    - Clique em **Selecionar**.

13. Clique em **Definições da aplicação Web**, especifique o nome de utilizador da base de dados e a palavra-passe e clique em **OK**.

    A configuração do CMS Umbraco deverá agora ser semelhante à seguinte captura de ecrã:

    ![Configuração concluída - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Clique em **Criar**.
    
    O Azure cria agora a aplicação do Umbraco com base na sua configuração. Deverá ver a notificação **Implementação iniciada...**.

    ![Implementação efetuada com êxito - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Iniciar e gerir a aplicação Web do Umbraco

Quando o Azure concluir a implementação da aplicação, verá outra notificação.

![Implementação efetuada com êxito - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Clique na notificação. Caso não a tenha visto, pode sempre aceder à mesma ao clicar no sino de notificações (![Notificação abaixo - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Deverá ver agora o [painel](../azure-resource-manager/resource-group-portal.md#manage-resources) de gestão das suas aplicações Web (*painel*: uma página do portal que abre na horizontal).

3. Na parte superior da página Descrição geral, clique em **Procurar**.
   
    ![Procurar - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Agora, pode ver a página de **boas-vindas** do Umbraco. Configure a instalação do Umbraco e comece a utilizá-lo!

    ![Configuração do Umbraco - primeiro Umbraco no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Web ASP.NET no Serviço de Aplicações do Azure com o Visual Studio](app-service-web-get-started-dotnet.md) - Saiba como criar uma nova aplicação Web do Azure a partir do Visual Studio, utilizando qualquer um dos modelos de aplicação incluídos.
* [Implementar o código no Serviço de Aplicações do Azure](web-sites-deploy.md)- Saiba como implementar a partir de FTP ou de repositórios de controlos de origem.
* [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md) - Eleve a sua aplicação do Azure para o próximo nível. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques.

