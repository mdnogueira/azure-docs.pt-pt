<properties
    pageTitle="Criar uma aplicação Web Java no App Service do Azure | Microsoft Azure"
    description="Este tutorial mostra-lhe como implementar uma aplicação Web Java no App Service do Azure."
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# Criar uma aplicação Web Java no App Service do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar uma [aplicação Web Java no App Service do Azure] utilizando o [Portal do Azure]. O Portal do Azure é uma interface Web que pode utilizar para gerir os recursos do Azure.

> [AZURE.NOTE] Para concluir este tutorial, necessita de uma conta do Microsoft Azure. Se não tiver uma conta, pode [ativar os benefícios de subscritor do Visual Studio] ou [inscrever-se numa avaliação gratuita].
>
> Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service]. Aqui, pode criar imediatamente uma aplicação Web de arranque de curta duração no App Service — sem cartões de crédito, sem compromissos.

## Opções da aplicação Java

Existem várias formas através das quais pode configurar uma aplicação Java numa aplicação Web do App Service. 

1. Crie uma aplicação e, em seguida, configure **Definições da aplicação**.

    O App Service fornece várias versões do Tomcat e Jetty, com a configuração predefinida. Se a aplicação que irá alojar irá funcionar com uma das versões incorporadas, este método de configuração de um contentor Web é o mais fácil e é perfeito quando apenas pretende carregar um ficheiro war para um contentor Web. Para este método, pode criar uma aplicação no Portal do Azure e, em seguida, aceder ao painel **Definições da aplicação** da sua aplicação para escolher a versão do Java juntamente com o contentor Web Java pretendido. Quando utiliza este método, o contentor Web e Java são executados a partir de Programas. Os outros métodos colocam o contentor Web e potencialmente o JVM no espaço em disco. Quando utiliza este modelo, não tem acesso para editar ficheiros nesta parte do sistema de ficheiros. Isto significa que não pode efetuar ações como configurar o ficheiro *server.xml* ou colocar ficheiros de biblioteca na pasta */lib*. Para obter mais informações, consulte a secção [Criar e configurar uma aplicação Web Java](#appsettings) mais à frente neste tutorial.
    
2. Utilizar um modelo do Azure Marketplace.

    O Azure Marketplace inclui modelos que criam e configuram automaticamente as Web Apps Java com contentores Web Jetty ou Tomcat. Os contentores Web que os modelos criam são configuráveis. Para obter mais informações, consulte a secção [Utilizar um modelo Java do Azure Marketplace](#marketplace) deste tutorial.
  
3. Criar uma aplicação e, em seguida, copiar manualmente e editar ficheiros de configuração 

    Pode querer alojar uma aplicação Java personalizada que não implementa em qualquer um dos contentores Web fornecidos pelo App Service. Por exemplo:
    
    * A aplicação Java necessita de uma versão do Tomcat ou Jetty que não seja diretamente suportada pelo App Service ou fornecida na galeria.
    * A aplicação Java aceita pedidos HTTP e não implementa como um WAR num contentor Web já existente.
    * Deve configurar o contentor Web a partir do zero sozinho. 
    * Deve utilizar uma versão do Java que não seja suportada no App Service e carregá-la sozinho.

    Nestes casos, pode criar uma aplicação utilizando o Portal do Azure e, em seguida, fornecer os ficheiros de runtime adequados manualmente. Neste caso, os ficheiros serão contados face às quotas de espaço de armazenamento no seu plano do App Service. Para obter mais informações, consulte [Carregar uma aplicação Web Java personalizada para o Azure].

## <a name="portal"></a> Criar e configurar uma aplicação Web Java

Esta secção mostra como criar uma aplicação Web e configurá-la para Java utilizando o painel **Definições da aplicação** do portal.

1. Inicie sessão no [Portal do Azure].

2. Clique em **Novo > Web + Móvel > Aplicação Web**.

    ![Nova aplicação Web][newwebapp]

4. Introduza um nome para a aplicação Web na caixa **Aplicação Web**.

    Este nome tem de ser exclusivo no domínio azurewebsites.net porque o URL da aplicação Web será {name}.azurewebsites.net. Se o nome introduzido não for exclusivo, é apresentado um ponto de exclamação vermelho na caixa de texto.

5. Selecione um **Grupo de Recursos** ou crie um novo.

    Para obter mais informações acerca dos grupos de recursos, consulte [Utilizar o Portal do Azure para gerir os recursos do Azure].

6. Selecione um **Plano do App Service/Localização** ou crie um novo.

    Para obter mais informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service do Azure].

7. Clique em **Criar**.

    ![Criar Aplicação Web][newwebapp2]
 
8. Quando tiver sido criada a aplicação Web, clique em **Web Apps > {a sua aplicação Web}**.
 
    ![Selecionar Aplicação Web][selectwebapp]

9. No painel **Aplicação Web**, clique em **Definições**.

10. Clique em **Definições da aplicação**.

11. Selecione a **versão de Java** pretendida. 

12. Selecione a **versão secundária de Java** pretendida. Se selecionar **A mais recente**, a aplicação utilizará a versão secundária mais recente que está disponível no App Service para essa versão principal de Java. O item **Mais recente** é exclusivo das aplicações Java criadas a partir das **Definições da aplicação**. Se criar a sua aplicação Java a partir da galeria, tem de gerir o seu contentor e as alterações à JVM. 

12. Selecione o **Contentor Web** pretendido. Se selecionar um nome de contentor que começa com **O mais recente**, a aplicação será mantida na versão mais recente da versão principal do contentor Web que está disponível no App Service. 

    ![Versões do Contentor Web][versions]

13. Clique em **Guardar**.

    Dentro de alguns instantes, a aplicação Web ficará baseada em Java e configurada para utilizar o contentor Web que selecionou.

14. Clique em **Web Apps > {a sua nova aplicação Web}**.

15. Clique no **URL** para navegar para o novo site.

    A página Web confirma que criou uma aplicação Web baseada em Java.

## <a name="marketplace"></a> Utilizar um modelo Java do Azure Marketplace

Esta secção mostra como utilizar o Azure Marketplace para criar uma aplicação Web de Java. O mesmo fluxo geral também pode ser utilizado para criar uma aplicação API ou móvel baseada em Java. 

1. Inicie sessão no [Portal do Azure]

2. Clique em **Novo > Marketplace**.

    ![Novo Marketplace][newmarketplace]

3. Clique em **Web + Móvel**.

    Poderá ter de se deslocar para a esquerda para ver o painel **Marketplace** onde pode selecionar **Web + Móvel**.

4. Na caixa de texto de pesquisa, introduza o nome de um servidor de aplicações Java como **Apache Tomcat** ou **Jetty**, e, em seguida, prima Enter.

5. Nos resultados da pesquisa, clique no servidor de aplicações Java.

    ![Jetty Móvel Web][webmobilejetty]

6. No primeiro painel **Apache Tomcat** ou **Jetty**, clique em **Criar**.

    ![Painel do Portal Jetty][jettyblade]

7. No painel seguinte **Apache Tomcat** ou **Jetty**, introduza um nome para a aplicação Web na caixa **Aplicação Web**.

    Este nome tem de ser exclusivo no domínio azurewebsites.net porque o URL da aplicação Web será {name}.azurewebsites.net. Se o nome introduzido não for exclusivo, é apresentado um ponto de exclamação vermelho na caixa de texto.

8. Selecione um **Grupo de Recursos** ou crie um novo.

    Para obter mais informações acerca dos grupos de recursos, consulte [Utilizar o Portal do Azure para gerir os recursos do Azure].

9. Selecione um **Plano do App Service/Localização** ou crie um novo.

    Para obter mais informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service do Azure].

10. Clique em **Criar**.

    ![Portal de Criação Jetty][jettyportalcreate2]

    Num curto período de tempo, normalmente, menos de um minuto, o Azure acaba de criar a nova aplicação Web.

11. Clique em **Web Apps > {a sua nova aplicação Web}**.

12. Clique no **URL** para navegar para o novo site.

    ![URL Jetty][jettyurl]

    O Tomcat é fornecido com um conjunto predefinido de páginas. Se optou por Tomcat, verá uma página semelhante ao seguinte exemplo.

    ![Aplicação Web com Apache Tomcat][tomcat]

    Se optou por Jetty, verá uma página semelhante ao seguinte exemplo. O Jetty não tem um conjunto de páginas predefinido, como tal, o mesmo JSP que é utilizado para um site Java vazio é reutilizado aqui.

    ![Aplicação Web utilizando o Jetty][jetty]

Agora que criou a aplicação Web com um contentor aplicacional, consulte a secção [Passos seguintes](#next-steps) para obter informações sobre como carregar a aplicação na aplicação Web.

## Passos seguintes

Neste momento, tem um servidor de aplicações Java em execução na sua aplicação Web no App Service do Azure. Para implementar o seu próprio código na aplicação Web, consulte o artigo [Adicionar uma aplicação ou página Web à sua aplicação Web Java].

Para obter mais informações sobre a programação de aplicações Java no Azure, consulte o [Centro de Programadores do Java].

<!-- URL List -->

[Adicionar uma aplicação ou página Web à sua aplicação Web Java]: ./web-sites-java-add-app.md
[Descrição geral dos planos do App Service do Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal do Azure]: https://portal.azure.com/
[ativar os benefícios de subscritor do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[inscrever-se numa avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Experimentar o App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[aplicação Web Java no App Service do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de Programadores do Java]: /develop/java/
[Utilizar o Portal do Azure para gerir os recursos do Azure]: ../azure-portal/resource-group-portal.md
[Carregar uma aplicação Web Java personalizada para o Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=ago16_HO4-->


