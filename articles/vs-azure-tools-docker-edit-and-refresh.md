---
title: "A depuração de aplicações num contentor Docker local | Microsoft Docs"
description: "Saiba como modificar uma aplicação que está a executar num contentor Docker local, atualize o contentor através de Edit and atualização e definir a depuração de pontos de interrupção"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: fcd58736d8915a61683a416fb9bf3892ba7b7bd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Depurar aplicações num contentor do Docker local
## <a name="overview"></a>Descrição geral
O Visual Studio Tools para Docker fornece uma forma consistente para desenvolver e validar a sua aplicação localmente num contentor Linux Docker.
Não tem de reiniciar o contentor sempre que efetuar um código de alteração.
Este artigo ilustra como utilizar a funcionalidade 'Editar e atualizar' para iniciar uma aplicação Web do ASP.NET Core num contentor Docker local, faça as alterações necessárias e, em seguida, atualize o browser para ver essas alterações.
Este artigo também mostra como configurar pontos de interrupção de depuração.

> [!NOTE]
> Suporte de Windows contentor estará disponível numa versão futura
>
>

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas tem de estar instaladas.

* [Versão mais recente do Visual Studio](https://www.visualstudio.com/downloads/)
* [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para executar os contentores de Docker localmente, terá de um cliente de local docker.
Pode utilizar o [caixa de ferramentas do Docker](https://www.docker.com/products/docker-toolbox), que requer que o Hyper-V para ser desativado ou pode utilizar [Docker para Windows](https://www.docker.com/get-docker), que utiliza o Hyper-V e requer o Windows 10.

Se utilizar a caixa de ferramentas do Docker, terá [configurar o cliente do Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Criar uma aplicação Web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adicionar suporte de Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Editar o código e a atualização
Para iterar rapidamente as alterações, pode iniciar a aplicação num contentor e continuar a fazer alterações, visualizá-los tal como faria com IIS Express.

1. Definir a configuração de solução `Debug` e prima  **&lt;CTRL + F5 >** para compilar a imagem de docker e executá-la localmente.

    Depois da imagem do contentor foi criada e estiver em execução num contentor de Docker, Visual Studio irá iniciar a aplicação Web no browser predefinido.
    Se estiver a utilizar o browser Microsoft Edge ou tiverem erros, caso contrário, consulte o artigo [resolução de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) secção.
2. Aceda à página acerca, que é onde vamos para tornar as nossas alterações.
3. Regresse ao Visual Studio e abrir `Views\Home\About.cshtml`.
4. Adicione o seguinte conteúdo HTML no fim do ficheiro e guarde as alterações.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Quando concluir a compilação do .NET e ver estas linhas, regresse ao seu browser e atualize a página acerca a visualizar a janela de saída.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. As alterações foram aplicadas!

## <a name="4-debug-with-breakpoints"></a>4. Depuração com pontos de interrupção
Muitas vezes, as alterações terão mais inspeção, tirar partido das funcionalidades de depuração do Visual Studio.

1. Regresse ao Visual Studio e abrir`Controllers\HomeController.cs`
2. Substitua o conteúdo do método About() com o seguinte:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Definir um ponto de interrupção para a esquerda do `string message`… linha.
4. Atingiu o  **&lt;F5 >** para iniciar a depuração.
5. Navegue para a página de acerca de para o ponto de interrupção de acessos.
6. Mudar para o Visual Studio para ver o ponto de interrupção e inspecionar o valor da mensagem.

   ![][2]

## <a name="summary"></a>Resumo
Com [Visual Studio 2015 Tools para Docker](https://aka.ms/DockerToolsForVS), pode obter a produtividade de trabalhar localmente, com o realism de produção de desenvolvimento de dentro de um contentor de Docker.

## <a name="troubleshooting"></a>Resolução de problemas
[Resolução de problemas de desenvolvimento de Docker do Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mais informações sobre o Docker com o Visual Studio, Windows e do Azure
* [Docker Tools para Visual Studio](http://aka.ms/dockertoolsforvs) -desenvolver o seu código .NET Core num contentor
* [Ferramentas de docker para Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - criar e implementar os contentores de docker
* [Ferramentas de docker para Visual Studio Code](http://aka.ms/dockertoolsforvscode) -serviços de idioma para editar ficheiros de docker, com mais cenários de e2e futuras
* [Informações de contentor do Windows](http://aka.ms/containers)-informações de servidor de Nano e Windows Server
* [Serviço de contentor do Azure](https://azure.microsoft.com/services/container-service/) - [conteúdo do serviço de contentor do Azure](http://aka.ms/AzureContainerService)
* Para obter mais exemplos de como trabalhar com o Docker, consulte [trabalhar com o Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) do [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais inícios rápidos da demonstração de HealthClinic.biz, veja [Azure Developer Tools Quickstarts (Inícios Rápidos de Ferramentas de Programador do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Várias ferramentas de Docker
[Algumas ferramentas de docker excelente (blogue de blogue Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Artigos boas
[Introdução ao micro-serviços de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Apresentações
* [Blogue Lasker: VS Las Vegas 2016 - Docker e2e em direto](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introdução ao ASP.NET Core @ compilação 2016 - onde, em demonstração](https://channel9.msdn.com/Events/Build/2016/B810)
* [Desenvolver aplicações de .NET em contentores de Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
