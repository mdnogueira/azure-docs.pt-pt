---
title: "Criar a primeira aplicação Web Java no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação Java simples."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: cephalin;robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 75e51ca45a899c6b6fa123346aa3c5860fd1600d
ms.contentlocale: pt-pt
ms.lasthandoff: 05/08/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Criar a primeira aplicação Web Java no Azure em cinco minutos

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este início rápido ajuda-o a implementar a sua primeira aplicação Web Java no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos. Quando tiver terminado este tutorial, terá uma aplicação Web simples baseada em Java a funcionar na cloud.

![Navegar para a aplicação Web](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="before-you-begin"></a>Antes de começar

Este tutorial demonstra como utilizar o IDE Eclipse para os Programadores de Java EE criarem e implementarem aplicações Web Java no Azure. Se ainda não tiver o Eclipse instalado, pode transferi-lo gratuitamente em http://www.eclipse.org/.

Para simplificar o processo de publicação de aplicações Web Java no Azure, os passos deste tutorial vão utilizar o [Azure Toolkit para Eclipse](/azure/azure-toolkit-for-eclipse). Para obter instruções de instalação do toolkit, veja [Installing the Azure Toolkit for Eclipse](/azure/azure-toolkit-for-eclipse-installation) (Instalar o Azure Toolkit para Eclipse).

> [!NOTE]
>
> Também pode utilizar o [IntelliJ IDEIA](https://www.jetbrains.com/idea/) da JetBrains para concluir os passos do tutorial. Alguns passos poderão ser ligeiramente diferentes para este ambiente de programação, embora exista também um [Azure Toolkit para IntelliJ](/azure/azure-toolkit-for-intellij), que pode utilizar para simplificar o processo de publicação para esse IDE.
>

Também vai precisar de uma subscrição do Azure para concluir os passos deste tutorial. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Criar um Projeto Web Dinâmico no Eclipse

No Eclipse, selecione o menu **Ficheiro**, em seguida, **Novo** e depois **Projeto Web Dinâmico**.

Na caixa de diálogo **Novo projeto Web Dinâmico**, nomeie o projeto **MyFirstJavaOnAzureWebApp** e selecione **Concluir**.
   
![Caixa de diálogo Projeto Web Dinâmico](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

> [!NOTE]
>
> Se tiver instalado um ambiente de runtime local, como o [Apache Tomcat](https://tomcat.apache.org/), pode especificá-lo no campo **Target runtime** (Runtime de destino).
>

Depois de o seu projeto Web dinâmico ter sido criado, adicione uma página JSP nova, ao expandir o projeto no Project Explorer (Explorador de Projetos), clicar com o botão direito do rato na pasta **WebContent** e clicar em **New** (Novo) e, por fim, em **JSP File** (Ficheiro JSP).

![Menu Novo Ficheiro JSP](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

Quando for apresentada a caixa de diálogo New JSP File (Novo Ficheiro JSP), dê o nome **index.jsp** ao ficheiro, mantenha a pasta principal como **MyFirstJavaOnAzureWebApp/WebContent** e clique em **Next** (Seguinte).

![Caixa de Diálogo Novo Ficheiro JSP](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

Na segunda página da caixa de diálogo New JSP File, dê o nome **index.jsp** ao ficheiro, mantenha a pasta principal como **MyFirstJavaOnAzureWebApp/WebContent** e clique em **Finish** (Concluir).

![Caixa de Diálogo Novo Ficheiro JSP](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-2.png)

Quando a sua página nova for aberta no Eclipse, substitua a secção `<body></body>` existente pelo código seguinte:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Guarde as alterações à página.

## <a name="publish-your-web-app-to-azure"></a>Publicar a aplicação Web no Azure

Para poder implementar a aplicação Web no Azure, vai tirar partido de várias funcionalidades proporcionadas pelo Azure Toolkit para Eclipse.

Para iniciar o processo de publicação, utilize um dos métodos seguintes:

* Clique com o botão direito do rato no Eclipse **Project Explorer** (Explorador de Projetos do Eclipse), clique em **Azure** e clique em **Publish as Azure Web App** (Publicar como Aplicação Web do Azure).

   ![Menu de Contexto Publicar como Aplicação Web do Azure](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

* Clique no ícone **Publish** (Publicar) na barra de ferramentas do Eclipse e clique em **Publish as Azure Web App** (Publicar como Aplicação Web do Azure.

   ![Menu Pendente Publicar como Aplicação Web do Azure](./media/app-service-web-get-started-java/publish-as-azure-web-app-drop-down-menu.png)

Se ainda não se tiver inscrito na sua conta do Azure, é-lhe pedido que o faça. Para fazê-lo, siga os seguintes passos:

1. Existem duas opções distintas para iniciar sessão na conta do Azure. Neste tutorial, escolha **Interactive** (Interativo).

   ![Caixa de Diálogo Início de Sessão do Azure](./media/app-service-web-get-started-java/azure-signin-dialog-box.png)

1. Introduza as suas credenciais do Azure e clique em **Sign in** (Iniciar sessão).

   ![Caixa de Diálogo Início de Sessão do Azure](./media/app-service-web-get-started-java/azure-login-dialog-box.png)

1. Escolha as suas subscrições do Azure e clique em **Selecionar**.

   ![Caixa de Diálogo Início de Sessão do Azure](./media/app-service-web-get-started-java/select-azure-subscriptions-dialog-box.png)

> [!NOTE]
>
> Estão disponíveis instruções detalhadas sobre os inícios de sessão **Interactive** (Interativo) e **Automated** (Automático) no artigo [Azure Sign In Instructions for the Azure Toolkit for Eclipse](https://go.microsoft.com/fwlink/?linkid=846174) (Instruções de Início de Sessão do Azure para o Azure Toolkit para Eclipse).
>

Depois de iniciar sessão na conta do Azure, é apresentada a caixa de diálogo **Implementar Aplicação Web**. Se estiver a publicar uma aplicação Web no Azure pela primeira vez, não deverá ver nenhum Serviços Aplicacionais listado. Se for esse o caso ou se quiser criar um Serviço de Aplicações novo, o próximo passo é criá-lo. Para tal, clique em **Criar**.

![Caixa de Diálogo Implementar Aplicação Web](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

Quando for apresentada a caixa de diálogo **Criar Serviço de Aplicações**, os dados iniciais que tem de indicar são:

* Um nome exclusivo para a aplicação Web, que se tornará no endereço DNS da mesma; por exemplo, **MyJavaWebApp** será *myjavawebapp.azurewebsites.net*.

* O contentor Web que a aplicação vai utilizar; por exemplo, **Newest Tomcat 8.5**.

* A sua subscrição do Azure.

   ![Caixa de Diálogo Criar Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

Se não tiver nenhum Plano do Serviço de Aplicações ou quiser criar um novo, terá de indicar as informações seguintes:

* Um nome exclusivo para o plano de serviço novo; este nome vai aparecer quando publicar aplicações Web no futuro com o Azure Toolkit e será listado no [Portal do Azure](https://portal.azure.com) quando estiver a gerir a sua conta.

* A localização geográfica onde o plano de serviço vai ser criado.

* O escalão de preço do plano.

   ![Criar Plano do Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-plan.png)

Em seguida, clique no separador **Grupo de recursos**. Se não tiver Grupos de Recursos ou quiser criar um novo, terá de indicar um nome exclusivo para o grupo novo; caso contrário, escolha um grupo de recursos existente a partir do menu pendente.

![Criar Plano do Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

Por último, clique no separador **JDK**. São listadas várias opções que permitem aos programadores especificar Java Developer Kits (JDKs) de terceiros ou personalizados. Contudo, para este tutorial, deverá escolher **Predefinido** e clicar em **Criar**.

![Criar Plano do Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)

O Azure Toolkit começa a criar o seu Serviço de Aplicações novo e apresenta uma caixa de diálogo de progresso enquanto decorre o processamento.

![Barra de Progresso de Criar Plano de Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

Depois de o seu serviço novo ter sido criado, a última opção que tem de escolher é se quer implementar a aplicação Web na raiz do seu Website novo. Por exemplo, se tiver um serviço de aplicações em *wingtiptoys.azurewebsites.net* e não implementar na raiz, a sua aplicação Web chamada **MyFirstJavaOnAzureWebApp** será implementada em *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Implementar Aplicação Web na Raiz](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

Depois de concluir todos os passos anteriores, clique em **implementar** para publicar a aplicação Web no Azure.

![Implementar Aplicação Web no Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

Parabéns! Implementou com êxito a sua aplicação Web no Azure! Agora, pode pré-visualizá-la no Website do Azure:

![Navegar para a aplicação Web](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="updating-your-web-app"></a>Atualizar a Aplicação Web

Depois de implementar com êxito a sua aplicação Web no Azure, a atualização da mesma é um processo muito mais simples e os passos seguintes orientam-no ao longo do processo de publicação de alterações à aplicação Web.

Em primeiro lugar, altere o código de JSP de exemplo anterior, para que o título seja substituído pela data de hoje:

```jsp
<%@ page
    language="java"
    contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"
    import="java.text.SimpleDateFormat"
    import="java.util.Date" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<% SimpleDateFormat date = new SimpleDateFormat("yyyy/MM/dd"); %>
<title><% out.println(date.format(new Date())); %></title>
</head>
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
</html>
```

Depois de guardar as suas alterações, clique com o botão direito do rato no Eclipse **Project Explorer** (Explorador de Projetos do Eclipse), clique em **Azure** e clique em **Publish as Azure Web App** (Publicar como Aplicação Web do Azure).

![Publicar Aplicação Web Atualizada](./media/app-service-web-get-started-java/publish-updated-web-app-context-menu.png)

Quando for apresentada a caixa de diálogo **Implementar Aplicação Web**, o seu serviço de aplicações anterior é listado. Para atualizar a aplicação Web, basta realçar o seu serviço de aplicações e clicar em **Implementar**, para publicar as alterações.

![Implementar Aplicação Web no Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

> [!NOTE]
>
> Se estiver a implementar a aplicação Web na raiz do serviço de aplicações, terá de assinalar novamente **Implementar na raiz** sempre que publicar as suas alterações.
>

Depois de publicar as alterações, vai reparar que o título da página muda para a data de hoje no seu browser.

![Navegar para a aplicação Web](./media/app-service-web-get-started-java/browse-web-app-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar a aplicação web, utilize o **Azure Explorer** incluído com o Azure Toolkit. Se a vista **Azure Explorer** ainda não estiver visível no Eclipse, utilize os passos seguintes para mostrá-la:

1. Clique em **Window** (Janela), clique em **Show View** (Mostrar Vista) e clique em **Other** (Outra).

   ![Menu Mostrar Vista](./media/app-service-web-get-started-java/show-azure-explorer-view-1.png)

2. Quando a caixa de diálogo **Show View** for apresentada, selecione **Azure Explorer** e clique em **OK**.

   ![Caixa de Diálogo Mostrar Vista](./media/app-service-web-get-started-java/show-azure-explorer-view-2.png)

Para eliminar a aplicação Web do Azure Explorer, tem de expandir o nó **Web Apps** (Aplicações Web), clique com o botão direito do rato e selecione **Delete** (Eliminar).

![Eliminar Aplicação Web](./media/app-service-web-get-started-java/delete-web-app-context-menu.png)

Quando lhe for pedido para eliminar a aplicação Web, clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os Azure Toolkits para IDEs de Java, veja as ligações seguintes:

* [Azure Toolkit para Eclipse (este artigo)](../azure-toolkit-for-eclipse.md)
  * [What's New in the Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse-whats-new.md) (Novidades no Azure Toolkit para Eclipse)
  * [Installing the Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse-installation.md) (Instalar o Azure Toolkit para Eclipse)
  * [Sign In Instructions for the Azure Toolkit for Eclipse](https://go.microsoft.com/fwlink/?linkid=846174) (Instruções de Início de Sessão para o Azure Toolkit para Eclipse)
* [Azure Toolkit para IntelliJ](../azure-toolkit-for-intellij.md)
  * [What's New in the Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-whats-new.md) (Novidades no Azure Toolkit para IntelliJ)
  * [Installing the Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md) (Instalar o Azure Toolkit para IntelliJ)
  * [Sign In Instructions for the Azure Toolkit for IntelliJ](https://go.microsoft.com/fwlink/?linkid=846179) (Instruções de Início de Sessão para o Azure Toolkit para IntelliJ)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure](https://azure.microsoft.com/develop/java/) e [Java Tools for Visual Studio Team Services](https://java.visualstudio.com/) (Ferramentas de Java para o Visual Studio Team Services).

