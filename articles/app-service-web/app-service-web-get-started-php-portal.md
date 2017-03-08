---
title: "Implementar uma aplicação do WordPress no portal do Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação do WordPress. Veja os seus resultados de imediato."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Implementar uma aplicação do WordPress no portal do Azure em cinco minutos

Este tutorial mostra como implementar a sua primeira aplicação Web do [WordPress](https://wordpress.org/) no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em minutos.

![Site do WordPress](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Pré-requisitos
Precisa de uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Implementar a aplicação do WordPress
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Esta hiperligação é um atalho para configurar de imediato uma nova aplicação do WordPress no portal do Azure.

3. Em **Nome da aplicação**, escreva o nome de uma aplicação Web. Verá uma marca de verificação verde na caixa se o nome for exclusivo no domínio `azurewebsites.net`.
   
5. Em **Grupo de Recursos**, clique em **Criar novo** para criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) e, em seguida, atribua-lhe um nome.

6. Em **Fornecedor de Base de Dados**, selecione **CleaDB**.

7. Clique em **plano do Serviço de Aplicações/Localização** > **Criar Novo**. Configure o [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) conforme indicado:

    - Em **plano do Serviço de Aplicações**, escreva o nome pretendido.
    - Em **Localização**, escolha uma localização para alojar o seu plano.
    - Clique em **Escalão de preço**, selecione **F1 Gratuito** ou outro escalão que lhe seja conveniente e, em seguida, clique em **Selecionar**.
    - Clique em **OK**.

8. Clique em **Base de Dados** > **Cria Novo**. Configure a Base de Dados SQL, conforme indicado:

    - Em **Nome da Base de Dados**, escreva um nome de base de dados. 
    - Em **Localização**, escolha a mesma localização do plano do Serviço de Aplicações.
    - Clique em **Escalão de preço**, selecione **Mercúrio** ou outro escalão que lhe seja conveniente e, em seguida, clique em **Selecionar**.
    - Selecione **Termos Legais** e clique em **Comprar**.
    - Clique em **OK**.

9. Clique em **Criar**.

    O Azure cria agora a aplicação do WordPress com base na sua configuração. Deverá ver a notificação **Implementação iniciada...**.

    ![Implementação iniciada - primeiro WordPress no Serviço de Aplicações do Azure](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerir a aplicação Web do WordPress

Quando o Azure concluir a implementação da aplicação, verá outra notificação.

![Implementação efetuada com êxito - primeiro WordPress no Serviço de Aplicações do Azure](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Clique na notificação. Caso não a tenha visto, pode sempre aceder à mesma ao clicar no sino de notificações (![sino Notificações - primeiro WordPress no Serviço de Aplicações do Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Deverá ver agora o [painel](../azure-resource-manager/resource-group-portal.md#manage-resources) de gestão das suas aplicações Web (*painel*: uma página do portal que abre na horizontal).

3. Na parte superior da página Descrição geral, clique em **Procurar**.
   
    ![Procurar - primeiro WordPress no Serviço de Aplicações do Azure](./media/app-service-web-get-started-php-portal/browse.png)

    Agora, pode ver a página de **boas-vindas** do WordPress. Configure a instalação do WordPress e comece a utilizá-lo!

    ![Configuração do WordPress - primeiro WordPress no Serviço de Aplicações do Azure](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Passos seguintes
* [Criar, configurar e implementar uma aplicação Web Laravel no Azure](app-service-web-php-get-started.md) - Saiba quais são as competências básicas de que necessita para executar qualquer aplicação Web PHP no Azure, tais como:

    * Criar e configurar aplicações no Azure a partir do PowerShell/Bash.
    * Definir a versão do PHP.
    * Utilizar um ficheiro de arranque que não se encontra no diretório de aplicações de raiz.
    * Ativar a automatização do Compositor.
    * Aceder a variáveis específicas do ambiente.
    * Resolver erros comuns.

* [Implementar o código no Serviço de Aplicações do Azure](web-sites-deploy.md)- Saiba como implementar a partir de FTP ou de repositórios de controlos de origem.
* [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md) - Eleve a sua aplicação do Azure para o próximo nível. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques.

