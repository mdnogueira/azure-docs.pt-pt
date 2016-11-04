---
title: Introdução aos Mobile Services do Azure para aplicações PhoneGap/Cordova | Microsoft Docs
description: Siga este tutorial para começar a utilizar os Mobile Services do Azure para desenvolvimento de PhoneGap para iOS, Android e Windows Phone.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: ggailey777

---
# Introdução aos Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel.

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][3]

### Requisitos Adicionais
Para concluir este tutorial, é necessário o seguinte:

* Ferramentas de PhoneGap (para os projetos de Windows Phone 8, é necessário v3.2+).
* Uma conta ativa do Microsoft Azure.
* O PhoneGap suporta o desenvolvimento para várias plataformas. Para além das ferramentas de PhoneGap, tem de instalar as ferramentas para cada plataforma de destino que pretende utilizar:
  
  * Windows Phone: instale o [Visual Studio 2012 Express para Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
  * iOS: instale o [Xcode](v4.4 + necessário)
  * Android: instale as [Ferramentas de Programador Android][SDK Android]
      <br/>(O SDK dos Mobile Services para Android suporta aplicações para Android 2.2 ou uma versão posterior. É necessário o Android 4.2 ou superior para executar a aplicação de início rápido.)

## Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação PhoneGap
Nesta secção, irá criar uma nova aplicação PhoneGap que está ligada ao seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.
2. No separador de início rápido, clique em **PhoneGap** em **Escolher plataforma** e expanda **Criar uma nova aplicação PhoneGap**.
   
    ![][0]
   
    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação PhoneGap ligada ao seu serviço móvel.
   
    ![][1]
3. Se ainda não o fez, transfira e instale o PhoneGap e, pelo menos, uma das ferramentas de desenvolvimento de plataforma (Windows Phone, iOS ou Android).
4. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.
5. Em **Transferir e executar a aplicação**, clique em **Transferir**.
   
    Esta ação transfere o projeto para a aplicação *Lista de tarefas* de exemplo que está ligada ao seu serviço móvel, juntamente com o SDK de JavaScript dos Mobile Services. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a nova aplicação PhoneGap
A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

1. Navegue até à localização onde guardou os ficheiros de projeto comprimidos e expanda os ficheiros no computador.
2. Abra e execute o projeto, de acordo com as instruções abaixo para cada plataforma.
   
   * **Windows Phone 8**
     
     1. Windows Phone 8: abra o ficheiro .sln na pasta **platforms\wp8** no Visual Studio 2012 Express para Windows Phone.
     2. Prima a tecla **F5** para reconstruir o projeto e iniciar a aplicação.
        
        ![][2]
   * **iOS**
     
     1. Abra o projeto na pasta **plataforms/ios** no Xcode.
     2. Prima o botão **Executar** para criar o projeto e iniciar a aplicação no emulador do iPhone, que é a predefinição para este projeto.
        
        ![][3]
   * **Android**
     
     1. No Eclipse, clique em **Ficheiro** e, em seguida, em **Importar**, expanda **Android**, clique em **Código Android Existente para a Área de Trabalho** e, em seguida, clique em **Seguinte.**
     2. Clique em **Procurar**, navegue até à localização dos ficheiros de projeto expandidos, clique em **OK**, certifique-se de que o projeto TodoActivity está marcado e, em seguida, clique em **Concluir**. <p>Esta ação importa os ficheiros de projeto para a área de trabalho atual.</p>
     3. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador do Android.
        
         ![][4]
        
        > [!NOTE]
        > Para poder executar o projeto no emulador do Android, tem de definir, pelo menos, um Dispositivo Virtual Android (AVD). Utilize o Gestor de AVD para criar e gerir estes dispositivos.
        > 
        > 
3. Depois de iniciar a aplicação num dos emuladores móveis acima, introduza algum texto na caixa de texto e, em seguida, clique em **Adicionar**.
   
    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela **Item da Lista de Tarefas**. Os itens armazenados na tabela são devolvidos pelo serviço móvel e os dados são apresentados na lista.
   
   > [!IMPORTANT]
   > As alterações a este projeto de plataforma serão substituídas se o projeto principal for reconstruído com as ferramentas de PhoneGap. Em vez disso, efetue alterações no diretório www de raiz do projeto conforme descrito na secção abaixo.
   > 
   > 
4. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela **Item da Lista da Lista de Tarefas**.
   
    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)
   
    Esta ação permite procurar os dados inseridos pela aplicação na tabela.
   
    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)

## Efetuar atualizações da aplicação e reconstruir projetos para cada plataforma
1. Efetue alterações aos ficheiros de código no diretório “www”, que neste caso é “todolist/www”.
2. Certifique-se de que todas as ferramentas de plataforma de destino estão acessíveis no caminho do sistema.
3. Abra uma linha de comandos no diretório de raiz do projeto e execute um dos seguintes comandos específicos da plataforma:
   
   * **Windows Phone**
     
       Execute o seguinte comando a partir da linha de comandos do Programador do Visual Studio:
     
           phonegap local build wp8
   * **iOS**
     
       Abra o terminal e execute o seguinte comando:
     
           phonegap local build ios
   * **Android**
     
       Abra uma linha de comandos ou janela de terminal e execute o seguinte comando.
     
           phonegap local build android
4. Abra cada projeto no ambiente de desenvolvimento adequado conforme descrito na secção anterior.

> [!NOTE]
> Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que se encontra no ficheiro js/index.js.
> 
> 

## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* **[Adicionar autenticação à aplicação]**  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.  
* **[Adicionar notificações push à aplicação](https://msdn.microsoft.com/magazine/dn879353.aspx)**  
  Saiba como se registar e enviar notificações push para a sua aplicação.
* **[Referência Conceptual sobre Procedimentos dos Mobile Services para HTML/JavaScript](mobile-services-html-how-to-use-client-library.md)**  
  Saiba mais sobre como utilizar a biblioteca de cliente JavaScript para aceder a dados, chamar APIs personalizadas e efetuar a autenticação.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Adicionar autenticação à aplicação]: mobile-services-html-get-started-users.md
[SDK Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374



<!--HONumber=Aug16_HO1-->


