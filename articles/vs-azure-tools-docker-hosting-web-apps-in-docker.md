---
title: "Implementar um contentor de Docker do ASP.NET Core Linux num anfitrião remoto do Docker | Microsoft Docs"
description: "Saiba como utilizar o Visual Studio Tools para Docker para implementar uma aplicação web do ASP.NET Core para um contentor de Docker em execução numa VM do Azure Docker anfitrião Linux"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Implementar um contentor do ASP.NET para um anfitrião remoto do Docker
## <a name="overview"></a>Descrição geral
Docker é um motor de contentor simples, semelhante em alguns aspetos a uma máquina virtual, que pode utilizar para o anfitrião de aplicações e serviços.
Este tutorial explica-lhe utilizar o [Visual Studio Tools para Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) extensão para implementar uma aplicação ASP.NET Core para um anfitrião de Docker no Azure através do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, é necessário o seguinte:

* Crie uma VM de anfitriões de Docker de Azure conforme descrito em [como utilizar o docker máquina com o Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Instale a versão mais recente do [Visual Studio](https://www.visualstudio.com/downloads/)
* Transferir o [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
* Instalar [Docker para Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Criar uma aplicação web do ASP.NET Core
Os seguintes passos guiá-lo através da criação de uma aplicação ASP.NET Core básica que vai ser utilizada neste tutorial.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adicionar suporte de Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Utilizar o Script do PowerShell DockerTask.ps1
1. Abra uma linha de comandos do PowerShell para o diretório de raiz do projeto. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Validar remoto anfitrião está em execução. Deverá ver o estado = em execução 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. A aplicação através de compilação-parâmetro de compilação
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Executar a aplicação, utilizando o parâmetro-execução
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Após a conclusão do docker, deverá ver resultados semelhantes ao seguinte:
   
   ![Ver a sua aplicação][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
