---
title: "Implementar a sua primeira aplicação Web Java no Azure em cinco minutos (CLI 2.0 [Pré-visualização]) | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação de exemplo. Comece a programar verdadeiramente em pouco tempo e a ver resultados imediatos."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: af27369b0ae8de0ece6da38a78b434e595fbfc4e


---
# <a name="deploy-your-first-java-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Implementar a sua primeira aplicação Web Java no Azure em cinco minutos (CLI 2.0 [Pré-visualização])

> [!div class="op_single_selector"]
> * [Primeiro site HTML](app-service-web-get-started-html-cli-nodejs.md)
> * [Primeira aplicação .NET](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Primeira aplicação PHP](app-service-web-get-started-php-cli-nodejs.md)
> * [Primeira aplicação Node.js](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Primeira aplicação Python](app-service-web-get-started-python-cli-nodejs.md)
> * [Primeira aplicação Java](app-service-web-get-started-java.md)
> 
> 

Este tutorial ajuda-o a implementar uma aplicação Web Java simples no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar o Serviço de Aplicações para criar aplicações, [back-ends de aplicações móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicações API](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

* Criar uma aplicação Web no Serviço de Aplicações do Azure.
* Implementar uma aplicação Java de exemplo.
* Ver o seu código em execução em direto na produção.

## <a name="prerequisites"></a>Pré-requisitos
* Obter um cliente FTP/FTPS, como o [FileZilla](https://filezilla-project.org/).
* Obter uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

<a name="create"></a>

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.
2. No menu à esquerda, clique em **Novo** > **Web + Móvel** > **Aplicação Web**.
   
    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)
3. No painel de criação da aplicação, utilize as seguintes definições para a sua nova aplicação:
   
   * **Nome da aplicação**: escreva um nome exclusivo.
   * **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.
   * **Plano do Serviço de Aplicações/Localização**: clique nele para configurar e, em seguida, clique em **Criar Novo** para definir o nome, a localização e o escalão de preço do plano do Serviço de Aplicações. Pode utilizar o escalão de preço **Gratuito**.
     
     Quando tiver terminado, o painel de criação da aplicação deverá este aspeto:
     
     ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)
4. Clique em **Criar** na parte inferior. Pode clicar no ícone de **Notificação** na parte superior para ver o progresso.
   
    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)
5. Quando a implementação estiver concluída, deverá ver esta mensagem de notificação. Clique na mensagem para abrir o painel da implementação.
   
    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)
6. No painel **Implementação concluída com êxito**, clique na ligação **Recurso** para abrir o painel da sua nova aplicação Web.
   
    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## <a name="deploy-a-java-app-to-your-web-app"></a>Implementar uma aplicação Java na aplicação Web
Agora, vamos implementar uma aplicação Java no Azure através de FTPS.

1. No painel da aplicação Web, desloque-se até às **Definições da aplicação** ou procure-as e, em seguida, clique na opção. 
   
    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)
2. Em **Versão Java**, selecione **Java 8** e clique em **Guardar**.
   
    ![](./media/app-service-web-get-started-languages/set-java.png)
   
    Quando receber a notificação **Definições da aplicação Web atualizadas com êxito**, navegue para http://*&lt;nomedaaplicação>*.azurewebsites.net para ver o servlet JSP predefinido em ação.
3. Novamente no painel da aplicação Web, desloque-se para baixo até às **Credenciais de implementação** ou procure-as e, em seguida, clique na opção.
4. Defina as suas credenciais de implementação e clique em **Guardar**.
5. Novamente no painel da aplicação Web, clique em **Descrição geral**. Junto do **Nome de utilizador de FTP/Implementação** e do **Nome do anfitrião FTPS**, clique no botão **Copiar** para copiar esses valores.
   
    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)
   
    Agora, está pronto para implementar a sua aplicação Java com FTPS.
6. No cliente FTP/FTPS, inicie sessão no servidor de FTP da sua aplicação Web do Azure com os valores que copiou no último passo. Utilize a palavra-passe de implementação que criou anteriormente.
   
    A seguinte captura de ecrã mostra como iniciar sessão com o FileZilla.
   
    ![](./media/app-service-web-get-started-languages/filezilla-login.png)
   
    Poderá ver avisos de segurança para o certificado SSL não reconhecido pelo Azure. Inicie sessão e continue.
7. Clique [nesta ligação](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) para transferir o ficheiro WAR para o computador local.
8. No cliente FTP/FTPS, navegue até **/site/wwwroot/webapps** no site remoto e arraste o ficheiro WAR transferido do computador local para esse diretório remoto.
   
    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)
   
    Clique em **OK** para substituir o ficheiro no Azure.
   
   > [!NOTE]
   > Em conformidade com o comportamento predefinido do Tomcat, o nome de ficheiro **ROOT.war** em /site/wwwroot/webapps dá-lhe a aplicação Web de raiz (http://*&lt;nomedaaplicação>*.azurewebsites.net) e o nome de ficheiro ***&lt;qualquernome>*.war** dá-lhe uma aplicação Web nomeada (http://*&lt;nomedaaplicação>*.azurewebsites.net/*&lt;qualquernome>*).
   > 
   > 

Já está! A aplicação Java já está em execução em direto no Azure. No seu browser, navegue para http://*&lt;nomeaplic>*.azurewebsites.net para vê-lo em ação. 

## <a name="make-updates-to-your-app"></a>Efetuar atualizações à sua aplicação
Sempre que precisar de fazer uma atualização, basta carregar o novo ficheiro WAR para o mesmo diretório remoto com o seu cliente FTP/FTPS.

## <a name="next-steps"></a>Passos seguintes
[Crie uma aplicação Web Java a partir de um modelo no Azure Marketplace](web-sites-java-get-started.md#marketplace). Pode obter o seu próprio contentor Tomcat totalmente personalizável e obter a IU de Gestor que já conhece. 

Depure a sua aplicação Web do Azure diretamente no [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) ou [Eclipse](app-service-web-debug-java-web-app-in-eclipse.md).

Em alternativa, faça mais com a sua primeira aplicação Web. Por exemplo:

* Experimente [outras formas de implementar o seu código no Azure](web-sites-deploy.md). 
* Eleve a sua aplicação do Azure ao nível seguinte. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques. Consulte [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md).




<!--HONumber=Feb17_HO3-->


