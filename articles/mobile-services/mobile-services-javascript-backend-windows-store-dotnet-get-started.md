<properties
    pageTitle="Introdução aos Mobile Services para aplicações da loja Windows (C#) | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para desenvolvimento da Loja Windows em C#."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="get-started-article" 
    ms.date="07/21/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Introdução aos Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma Aplicação do Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação universal do Windows utilizando os Mobile Services do Azure. As soluções de aplicações universais do Windows incluem projetos para aplicações da Loja Windows 8.1 e da Loja do Windows Phone 8.1, bem como um projeto partilhado comum. Para obter mais informações, consulte [Criar aplicações universais do Windows direcionadas para Windows e Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza JavaScript para a lógica de negócio do lado do servidor. Para criar um serviço móvel que permita escrever a lógica de negócio do lado do servidor nas linguagens .NET suportadas utilizando o Visual Studio, consulte a versão back-end de JavaScript deste tópico.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows]

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação universal do Windows

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação universal do Windows ou modificar um projeto de aplicação da Loja Windows ou para Windows Phone existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação universal do Windows que está ligada ao seu serviço móvel.

1.  No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.


2. No separador de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar uma nova aplicação da Loja Windows**.

    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação da Loja Windows ligada ao seu serviço móvel.

    ![Passos de início rápido dos Mobile Services](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Se ainda não o fez, transfira e instale o [Visual Studio 2013 Express para Windows] no computador local ou numa máquina virtual.

4. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.

5. Em **Transferir e executar a aplicação**, selecione um idioma para a sua aplicação e, em seguida, clique em **Transferir**.

    Esta ação transfere o projeto para a aplicação *Lista de tarefas* de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a aplicação Windows

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que se encontra no ficheiro MainPage.xaml.cs.

## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução à sincronização de dados offline] Saiba como utilizar a sincronização de dados offline para tornar a sua aplicação robusta e responsiva.

* [Adicionar autenticação à aplicação dos Mobile Services ][Introdução à autenticação]  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicionar notificações push à aplicação][Introdução às notificações push]  
  Saiba como enviar uma notificação push muito básica para a sua aplicação.

* [Como utilizar a biblioteca de cliente .NET](mobile-services-dotnet-how-to-use-client-library.md)  
 Saiba como consultar o serviço móvel, trabalhar com dados e aceder a APIs personalizadas.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Introdução à autenticação]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Introdução às notificações push]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[SDK dos Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal Clássico do Azure]: https://manage.windowsazure.com/
 


<!--HONumber=Aug16_HO1-->


