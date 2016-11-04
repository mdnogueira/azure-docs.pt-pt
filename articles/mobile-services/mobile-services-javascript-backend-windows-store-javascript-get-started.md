---
title: Introdução aos Mobile Services para aplicações JavaScript da Loja Windows | Microsoft Docs
description: Siga este tutorial para começar a utilizar os Mobile Services do Azure para desenvolvimento da Loja Windows em JavaScript.
services: mobile-services
documentationcenter: windows
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: javascript
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: glenga

---
# Introdução aos Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma aplicação do Windows com Aplicações Móveis](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).  
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação JavaScript da Loja Windows utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza JavaScript para a lógica de negócio do lado do servidor. 

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows]

## Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação da Loja Windows
Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação JavaScript da Loja Windows 8.1 para ligar ao seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.
2. No separador de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar uma nova aplicação da Loja Windows**.
3. Se ainda não o fez, transfira e instale o [Visual Studio 2013][Visual Studio 2013 Express para Windows] no computador local ou numa máquina virtual.
4. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.
5. Em **Transferir e executar a aplicação**, selecione um idioma para a sua aplicação e, em seguida, clique em **Transferir**.
   
    Esta ação transfere o projeto para a aplicação *Lista de tarefas* de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a aplicação Windows
A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

1. Navegue até à localização onde guardou os ficheiros comprimidos do projeto, expanda- os no computador e abra o ficheiro da solução no Visual Studio.
2. Prima a tecla **F5** para reconstruir o projeto e iniciar a aplicação.
3. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, em **Inserir um TodoItem** e, em seguida, clique em **Guardar**.
   
    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo servidor móvel e os dados são apresentados na segunda coluna na aplicação.
4. (Opcional) Execute novamente a aplicação e repare que os dados guardados do passo anterior são carregados a partir do serviço móvel depois de a aplicação iniciar.
5. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela **Itens da Lista de Tarefas**.
   
    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

> [!NOTE]
> Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, o qual se encontra no ficheiro default.js.
> 
> 

## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como trabalhar com o [Cliente de Mobile Services para HTML/JavaScript](mobile-services-html-how-to-use-client-library.md). 

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[SDK dos Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal Clássico do Azure]: https://manage.windowsazure.com/



<!--HONumber=Sep16_HO3-->


