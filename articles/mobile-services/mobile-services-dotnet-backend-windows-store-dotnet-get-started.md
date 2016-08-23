<properties
    pageTitle="Introdução aos Mobile Services para aplicações universais do Windows | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para o desenvolvimento de aplicações universais do Windows em C#."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Introdução aos Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma aplicação do Windows com Aplicações Móveis](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação universal do Windows utilizando os Mobile Services do Azure. As soluções de aplicações universais do Windows incluem projetos para aplicações da Loja Windows 8.1 e da Loja do Windows Phone 8.1, bem como um projeto partilhado comum. Para obter mais informações, consulte [Criar aplicações universais do Windows direcionadas para Windows e Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza as linguagens .NET suportadas, utilizando o Visual Studio para a lógica de negócio do lado do servidor e para a gestão do serviço móvel. Para criar um serviço móvel que permita escrever a lógica de negócio do lado do servidor em JavaScript, consulte a secção Versão back-end de JavaScript deste tópico.

>[AZURE.NOTE]Este tópico mostra como criar um novo projeto de Mobile Services e aplicação Windows universal utilizando o Portal Clássico do Azure. Se utilizar o Visual Studio 2013 Atualização 3, também pode adicionar um novo projeto de Mobile Services a uma solução do Visual Studio existente. Para obter mais informações, consulte [Adicionar Mobile Services a uma aplicação existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>Para adicionar um serviço móvel a um projeto de aplicação do Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte [Adicionar Mobile Services a uma aplicação para Windows Phone existente](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started%2F).
* [Visual Studio 2013].

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar uma nova aplicação universal do Windows

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação universal do Windows que está ligada ao seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

2. No separador de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar uma nova aplicação da Loja Windows**.

    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação da Loja Windows ligada ao seu serviço móvel.

    ![Passos de início rápido dos Mobile Services](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Se ainda não o fez, transfira e instale o [Visual Studio 2013] no computador local ou numa máquina virtual.

4. Em **Transferir e executar a aplicação e o serviço localmente**, selecione um idioma para a sua aplicação da Loja Windows e, em seguida, clique em **Transferir**.

    Esta ação transfere uma solução que contém projetos para o serviço móvel e para a aplicação _Lista de tarefas_ de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Testar a aplicação contra o serviço móvel local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que se encontra no ficheiro MainPage.xaml.cs.


## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>No Projeto de código partilhado, abra o ficheiro App.xaml.cs, localize o código que cria uma instância de <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, comente o código que cria este cliente utilizando <em>localhost</em> e remova o comentário do código que cria o cliente utilizando o URL do serviço móvel remoto, que se parece com o seguinte:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

    <p>O cliente terá agora acesso ao serviço móvel publicado para o Azure.</p></li>
</ol>

## Testar a aplicação contra o serviço móvel alojado no Azure

Agora que o serviço móvel está publicado e o cliente está ligado ao serviço móvel remoto alojado no Azure, é possível executar a aplicação utilizando o Azure para o armazenamento de itens.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]


## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Adicionar Mobile Services a uma aplicação existente][Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados através dos Mobile Services.

* [Introdução à sincronização de dados offline]
  <br/>Saiba como utilizar a sincronização de dados offline para tornar a sua aplicação robusta e responsiva.

* [Adicionar autenticação à aplicação dos Mobile Services ][Introdução à autenticação]
  <br/>Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicionar notificações push à aplicação][Introdução às notificações push]
  <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.

* [Resolver problemas relacionados com um back-end .NET dos Mobile Services]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end .NET dos Mobile Services.

Para obter mais informações sobre as aplicações universais do Windows, consulte [Suportar várias plataformas de dispositivos a partir de um único serviço móvel](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->

<!-- Images. -->



<!-- URLs. -->
[Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Introdução aos dados]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Introdução à sincronização de dados offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Introdução à autenticação]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Introdução às notificações push]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK dos Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Resolver problemas relacionados com um back-end .NET dos Mobile Services]: mobile-services-dotnet-backend-how-to-troubleshoot.md



<!--HONumber=Aug16_HO1-->


