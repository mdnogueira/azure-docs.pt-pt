---
title: Introdução aos Mobile Services para Xamarin.Android | Microsoft Docs
writer: craigd
description: Saiba como utilizar os Mobile Services do Azure com a sua aplicação Xamarin.Android.
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"></a>Introdução aos Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma Aplicação Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin.Android com os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel.

Se preferir ver um vídeo, o clip abaixo segue os mesmos passos deste tutorial.

Vídeo: “Introdução ao Xamarin e aos Mobile Services do Azure” com Craig Dunn, programador de Xamarin (duração: 10:05 min.)

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services/player]
> 
> 

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

Para concluir este tutorial, é necessário o XCode e o Xamarin Studio para OS X ou o Visual Studio no Windows com um MAC em rede. Pode obter instruções de instalação completas em [Configuração e Instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

O projeto de início rápido transferido contém o componente de Mobile Services do Azure para Xamarin.Android. Embora este projeto se destine ao Android 4.2 ou uma versão posterior, o SDK dos Mobile Services requer apenas o Android 2.2 ou uma versão posterior.

> [!IMPORTANT]
> Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).
> 
> 

## <a name="create-new-service"> </a>Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação Xamarin.Android
Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação Xamarin.Android que está ligada ao seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.
2. No separador de início rápido, clique em **Xamarin.Android** em **Escolher plataforma** e expanda **Criar uma nova aplicação Android**.
   
    ![][6]
   
    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação Xamarin.Android ligada ao seu serviço móvel.
   
    ![][7]
3. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.
4. Em **Transferir e executar a aplicação**, clique em **Transferir**.
   
    Esta ação transfere o projeto para a aplicação *Lista de tarefas* de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a aplicação Android
A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

1. Navegue até à localização onde guardou os ficheiros de projeto comprimidos e expanda os ficheiros no computador.
2. No Xamarin Studio ou no Visual Studio, clique em **Ficheiro** e, em seguida, em **Abrir**, navegue até aos ficheiros de exemplo descomprimidos e selecione **XamarinTodoQuickStart.Android.sln** para o abrir.
3. Prima o botão **Executar** para compilar o projeto e iniciar a aplicação. É-lhe pedido para selecionar um emulador ou um dispositivo USB ligado.
   
   > [!NOTE]
   > Para poder executar o projeto no emulador do Android, tem de definir, pelo menos, um Dispositivo Virtual Android (AVD). Utilize o Gestor de AVD para criar e gerir estes dispositivos.
   > 
   > 
4. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial* e, em seguida, clique em **Adicionar**.
   
    ![][10]
   
    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo serviço móvel e os dados são apresentados na lista.
   
   > [!NOTE]
   > Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que se encontra no ficheiro C# ToDoActivity.cs.
   > 
   > 
5. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela **Itens da Lista de Tarefas**.
   
    ![][11]
   
    Esta ação permite procurar os dados inseridos pela aplicação na tabela.
   
    ![][12]

## <a name="next-steps"> </a>Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução à sincronização de dados offline] Saiba como o guia de introdução utiliza a sincronização de dados offline para tornar a sua aplicação robusta e responsiva.
* [Introdução à autenticação] Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Introdução às notificações push] Saiba como enviar uma notificação push muito básica para a sua aplicação.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Introdução aos dados]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à sincronização de dados offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[SDK Android dos Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Portal Clássico do Azure]: https://manage.windowsazure.com/




<!--HONumber=Aug16_HO1-->


