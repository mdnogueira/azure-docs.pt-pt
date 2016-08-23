<properties
    pageTitle="Implementar uma aplicação ASP.NET no 	App Service do Azure com o Visual Studio | Microsoft Azure"
    description="Saiba como implementar um projeto Web do ASP.NET numa nova aplicação Web no App Service do Azure, utilizando o Visual Studio."
    services="app-service\web"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/22/2016"
    ms.author="rachelap"/>

# Implementar uma aplicação Web ASP.NET no App Service do Azure com o Visual Studio

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## Descrição geral

Este tutorial mostra como implementar uma aplicação Web ASP.NET numa [aplicação Web no App Service do Azure do Azure](app-service-web-overview.md) utilizando o Visual Studio 2015.

O tutorial parte do princípio que é um programador do ASP.NET sem experiência prévia com a utilização do Azure. Quando tiver terminado, terá uma aplicação Web simples em execução na nuvem.

Irá aprender:

* Como criar uma nova aplicação Web do App Service enquanto cria um novo projeto Web no Visual Studio.
* Como implementar um projeto Web a uma aplicação Web do App Service através do Visual Studio.

O diagrama ilustra o que fazer no tutorial.

![Diagrama de criação e implementação do Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

No final do tutorial, uma secção [Resolução de problemas](#troubleshooting) dá ideias sobre o que fazer se algo não funcionar e uma secção [Passos seguintes](#next-steps) fornece ligações para outros tutoriais que aprofundam como utilizar o App Service do Azure.

Uma vez que este é um tutorial de introdução, o projeto Web que mostra como implementar é simples e não utiliza uma base de dados, autenticação nem autorização. Para obter ligações para tópicos de implementação mais avançados, consulte o artigo [Como implementar uma aplicação Web do Azure](web-sites-deploy.md).

Além do tempo necessário para instalar o Azure SDK para o .NET, este tutorial irá demorar cerca de 10 a 15 minutos para concluir.

## Pré-requisitos

* O tutorial assume que já trabalhou com ASP.NET MVC e o Visual Studio. Se precisar de uma introdução, consulte o artigo [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started).

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar as vantagens de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

    Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Aqui, pode criar uma aplicação de arranque de curta duração no App Service — sem cartões de crédito, sem compromissos.

## <a name="setupdevenv"></a>Configurar o ambiente de desenvolvimento

O tutorial foi escrito para o Visual Studio 2015 com o [Azure SDK para .NET](../dotnet-sdk.md) 2.9 ou posterior. 

* [Transfira o Azure SDK mais recente para o Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). O SDK instala o Visual Studio 2015, se ainda não o tiver.

    >[AZURE.NOTE] Dependendo do número de dependências SDK que já possui no seu computador, instalar o SDK pode demorar muito tempo, de alguns minutos a meia hora ou mais.

Se tiver o Visual Studio 2013 e preferir utilizá-lo, pode [transferir o Azure SDK mais recente para o Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alguns ecrãs podem parecer diferentes das ilustrações.

## Configurar um novo projeto Web

O próximo passo é criar um projeto Web no Visual Studio e uma aplicação Web no App Service do Azure. Nesta secção do tutorial, configura o novo projeto Web. 

1. Abra o Visual Studio 2015.

2. Clique em **Ficheiro > Novo > Projeto**.

3. Na caixa de diálogo **Novo Projeto**, clique em **Visual C# > Web > Aplicação Web ASP.NET**.

3. Certifique-se de que o **.NET Framework 4.5.2** é selecionado como o framework de destino.

4.  O [Azure Application Insights](../application-insights/app-insights-overview.md) monitoriza a sua aplicação Web quanto à disponibilidade, ao desempenho e á utilização. A caixa de verificação **Adicionar Application Insights ao Projeto** está selecionada por predefinição da primeira vez que criar um projeto Web depois de instalar o Visual Studio. Desmarque a caixa de verificação se estiver selecionada, mas não pretender experimentar o Application Insights.

4. Dê um nome à aplicação **OmeuExemplo** e, em seguida, clique em **OK**.

    ![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. Na caixa de diálogo **Novo Projeto de ASP.NET**, selecione o modelo **MVC** modelo e, em seguida, clique em **Alterar Autenticação**.

    Para este tutorial, implementa um projeto Web de ASP.NET MVC. Se pretender obter informações sobre como implementar um projeto Web API do ASP.NET, consulte a secção [Passos seguintes](#next-steps). 

    ![Caixa de diálogo Novo Projeto ASP.NET](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

    ![Sem Autenticação](./media/web-sites-dotnet-get-started/GS13noauth.png)

    Para este tutorial de introdução, está a implementar uma aplicação simples que não utiliza o início de sessão do utilizador.

5. Na secção **Microsoft Azure** da caixa de diálogo **Novo Projeto ASP.NET**, certifique-se de que **Anfitrião na nuvem** está selecionado e que **Serviços de Aplicações** está selecionado na lista pendente.

    ![Caixa de diálogo Novo Projeto ASP.NET](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

    Estas definições direcionam o Visual Studio para criar uma aplicação Web do Azure para o seu projeto Web.

6. Clique em **OK**

## Configurar recursos do Azure para uma nova aplicação Web

Agora, indique ao Visual Studio os recursos do Azure que pretende que criar.

5. Na caixa de diálogo **Criar Serviços de Aplicações**, clique em **Adicionar uma conta** e, em seguida, inicie sessão no Azure com o ID e palavra-passe da conta que utiliza para gerir a sua subscrição Azure.

    ![Iniciar sessão no Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

    Se já iniciou sessão anteriormente no mesmo computador, poderá não ver o botão **Adicionar uma conta**. Nesse caso, pode ignorar este passo ou poderá ter de reintroduzir as suas credenciais.
 
3. Introduza um **Nome da Aplicação Web** que seja exclusivo no domínio *azurewebsites.net*. Por exemplo, pode atribuir o nome OmeuExemplo com números para a direita para o tornar único, tal como OmeuExemplo810. Se um nome da Web predefinido for criado para si, será exclusivo e pode utilizá-lo.

    Se alguém já tiver utilizado o nome que introduziu, verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde e terá de introduzir um nome diferente.

    O URL da aplicação é este nome mais *.azurewebsites.net*. Por exemplo, se o nome for `MyExample810`, o URL será `myexample810.azurewebsites.net`.

    Também pode utilizar um domínio personalizado com uma aplicação Web do Azure. Para obter mais informações, consulte o artigo [Configurar um nome de domínio personalizado no App Service do Azure](web-sites-custom-domain-name.md).

6. Clique no botão **Novo** junto à caixa **Grupo de Recursos** e, em seguida, introduza "OmeuExemplo" ou outro nome, se preferir. 

    ![Caixa de diálogo Criar App Service](./media/web-sites-dotnet-get-started/rgcreate.png)

    Um grupo de recursos é uma coleção de recursos do Azure, tais como Web Apps, bases de dados e VMs. Para um tutorial, geralmente, é melhor criar um novo grupo de recursos porque torna mais fácil eliminar num único passo todos os recursos do Azure que criar para o tutorial. Para obter mais informações, consulte o artigo [Descrição geral do Azure Resource Manager](../resource-group-overview.md).

4. Clique no botão **Novo** junto ao menu pendente **Plano do App Service**.

    ![Caixa de diálogo Criar App Service](./media/web-sites-dotnet-get-started/createasplan.png)

    É apresentada a caixa de diálogo **Configurar Plano do App Services**.

    ![Caixa de diálogo Configurar App Service](./media/web-sites-dotnet-get-started/configasp.png)

    Nos passos seguintes, pode configurar um plano do App Service para o novo grupo de recursos. Um plano do App Service especifica os recursos de computação em que é executada a aplicação Web. Por exemplo, se escolher o escalão gratuito, a aplicação API é executada em VMs partilhadas, enquanto para alguns escalões pagos, é executada em VMs dedicadas. Para obter mais informações, consulte o artigo [Descrição geral dos planos de Serviços de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na caixa de diálogo **Configurar Plano do App Service**, introduza "OmeuPlanoDeExemplo" ou outro nome, se preferir.

5. Na lista pendente **Localização**, escolha a localização que esteja mais perto de si.

    Esta definição especifica em que datacenter do Azure a sua aplicação será executada. Para este tutorial, pode selecionar qualquer região pois não terá uma diferença percetível. Porém, para uma aplicação de produção, o servidor deve estar o mais perto possível dos clientes que estão a aceder ao mesmo para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. No menu pendente **Tamanho** pendente, clique **Gratuito**.

    Para este tutorial, o escalão de preço gratuito irá fornecer o desempenho suficiente.

6. Na caixa de diálogo **Configurar Plano do App Service**, clique em **OK**.

7. Na caixa de diálogo **Criar App Service**, clique em **Criar**.

## O Visual Studio cria o projeto e a aplicação Web

Num curto período de tempo, normalmente, menos do que um minuto, o Visual Studio cria o projeto Web e a aplicação Web.  

A janela **Explorador de Soluções** mostra os ficheiros e as pastas no projeto novo.

![Explorador de Soluções](./media/web-sites-dotnet-get-started/solutionexplorer.png)

A janela **Atividade do App Service do Azure** mostra que a aplicação Web foi criada.

![Aplicação Web criada na janela de Atividade do App Service do Azure](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

A janela **Cloud Explorer** permite-lhe ver e gerir recursos do Azure, incluindo a nova aplicação Web que acabou de criar.

![Aplicação Web criada no Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)
    
## Implementar o projeto Web na aplicação Web do Azure

Nesta secção, é possível implementar o projeto Web na aplicação Web.

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

    ![Clicar em Publicar no menu do Visual Studio](./media/web-sites-dotnet-get-started/choosepublish.png)

    Em alguns segundos, é apresentado o assistente **Publicar Web**. O Assistente abre num *perfil de publicação* que tem definições para implementar o projeto Web na nova aplicação Web.

    O perfil de publicação inclui um nome de utilizador e a palavra-passe para a implementação.  Estas credenciais foram geradas para si e não tem de as introduzir. A palavra-passe está encriptada num ficheiro oculto específico do utilizador na pasta `Properties\PublishProfiles`.
 
8. No separador **Ligação** do assistente **Publicar Web**, clique em **Seguinte**.

    ![Clicar em Seguinte no separador Ligação do assistente Publicar Web](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

    Segue-se o separador **Definições**. Aqui, pode alterar a configuração de compilação para implementar uma versão de depuração para a [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). O separador também oferece várias [Opções de Publicação do Ficheiro](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2).

10. No separador **Definições**, clique em **Seguinte**.

    ![Separador Definições do assistente Publicar Web](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

    O separador **Pré-visualização** vem a seguir. Aqui, tem uma oportunidade para ver os ficheiros que vão ser copiados do seu projeto para a aplicação API. Quando estiver a implementar um projeto para uma aplicação API que já tenha implementado anteriormente, apenas os ficheiros alterados são copiados. Se pretender ver uma lista daquilo que será copiado, pode clicar no botão **Iniciar Pré-visualização**.

11. No separador **Pré-visualização**, clique em **Publicar**.

    ![Separador Pré-visualização do assistente Publicar Web](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

    Quando clica em **Publicar**, o Visual Studio inicia o processo de copiar os ficheiros para o servidor do Azure. Este processo poderá demorar um ou dois minutos.

    As janelas de **Resultados** e de **Atividade do App Service do Azure** mostram que ações de implementação foram executadas e reportam a conclusão com êxito da implementação.

    ![Janela de Resultados do Studio Visual a reportar uma implementação efetuada com êxito](./media/web-sites-dotnet-get-started/PublishOutput.png)

    Após a implementação com êxito, o browser predefinido abre automaticamente no URL da aplicação Web implementada e a aplicação que criou está agora a ser executada na nuvem. O URL na barra de endereço do browser mostra que a aplicação Web é carregada a partir da Internet.

    ![Aplicação Web em execução no Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

    > [AZURE.TIP] Pode ativar a barra de ferramentas **Publicação com Um Clique na Web** para uma implementação rápida. Clique em **Ver > Barras de Ferramentas** e, em seguida, selecione **Publicação com Um Clique na Web**. Pode utilizar a barra de ferramentas para selecionar um perfil, clicar num botão para publicar ou clicar num botão para abrir o assistente **Publicar Web**.
    > ![Barra de Ferramentas Publicação com Um Clique na Web](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Resolução de problemas

Se tiver um problema à medida que revê este tutorial, certifique-se de que está a utilizar a versão mais recente do Azure SDK para o .NET. A forma mais fácil para o fazer é [transferir o Azure SDK para o Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Se tiver a versão atual instalada, o Instalador de Plataforma Web permite-lhe saber que não é necessária qualquer instalação.

Caso se encontre numa rede empresarial e esteja a tentar implementar no App Service do Azure através de uma firewall, certifique-se de que as portas 443 e 8172 estão abertas para Web Deploy. Se não for possível abrir essas portas, consulte a secção Passos seguintes para obter outras opções de implementação.

Depois de ter a aplicação Web do ASP.NET em execução no App Service do Azure, pode querer saber mais sobre as funcionalidades do Visual Studio que simplificam a resolução de problemas. Para obter informações sobre o registo, a depuração remota e muito mais, consulte o artigo [Resolução de problemas com as Web Apps do Azure no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Passos seguintes

Neste tutorial, aprendeu a criar uma aplicação Web simples e a implementá-la numa aplicação Web do Azure. Eis alguns tópicos relacionados e recursos para aprender mais sobre o App Service do Azure:

* Monitorizar e gerir a sua aplicação Web no [Portal do Azure](https://portal.azure.com/). 

    Para obter mais informações, consulte [uma descrição geral do Portal do Azure](/services/management-portal/) e [Configurar Web Apps no App Servicedo Azure](web-sites-configure.md).

* Implementar um projeto Web existente para uma nova aplicação Web, utilizando o Visual Studio

    Clique com o botão direito do rato no projeto **Explorador de Soluções** e, em seguida, clique em **Publicar**. Escolha **App Service do Microsoft Azure** como o destino da publicação e, em seguida, clique em **Novo**. Deste modo, as caixas de diálogo serão as mesmas que viu neste tutorial.

* Implementar um projeto Web a partir do controlo de código fonte

    Para obter informações sobre a [automatização da implementação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) a partir de um [sistema de controlo de origem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte o artigo [Introdução às Web Apps no App Service do Azure](app-service-web-get-started.md) e [Como implementar uma aplicação Web no Azure](web-sites-deploy.md).

* Implementar uma API Web do ASP.NET para uma aplicação API no App Service do Azure

    Aprendeu a criar uma instância do App Service do Azure que se destina principalmente a alojar um Web site. O App Service também oferece funcionalidades para o alojamento de APIs Web, tal como suporte de CORS e o suporte de metadados da API para a geração de código do cliente. Pode utilizar funcionalidades da API na aplicação Web, mas se pretender principalmente alojar uma API numa instância do App Service, uma **aplicação API** seria uma melhor opção. Para obter mais informações, consulte o artigo [Introdução às API Apps e ao ASP.NET no App Service do Azure](../app-service-api/app-service-api-dotnet-get-started.md). 

* Adicionar um nome de domínio personalizado e SSL

    Para obter informações sobre como utilizar o SSL e o seu próprio domínio (por exemplo, www.contoso.com em vez de contoso.azurewebsites.net), consulte os seguintes recursos:

    * [Configurar um nome de domínio personalizado no App Service do Azure](web-sites-custom-domain-name.md)
    * [Ativar HTTPS para um Web site do Azure](web-sites-configure-ssl-certificate.md)

* Elimine o grupo de recursos que contém a aplicação Web e quaisquer recursos relacionados do Azure quando tiver terminado com os mesmos.

    Para obter informações sobre como trabalhar com grupos de recursos no Portal do Azure, veja [Implementar recursos com modelos do Resource Manager e com o portal do Azure](../resource-group-template-deploy-portal.md).   

*   Para obter mais exemplos de criação de uma Aplicação Web ASP.NET no Serviço de Aplicações, veja [Create and deploy an ASP.NET web app in Azure App Service (Criar e implementar uma aplicação Web ASP.NET no Serviço de Aplicações do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) e [Create and deploy a mobile app in Azure App Service (Criar e implementar uma aplicação móvel no Serviço de Aplicações do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-a-mobile-app-in-Azure-App-Service) da [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Para obter mais inícios rápidos a partir da demonstração de HealthClinic.biz, veja [Inícios Rápidos de Ferramentas de Programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).



<!--HONumber=Aug16_HO1-->


