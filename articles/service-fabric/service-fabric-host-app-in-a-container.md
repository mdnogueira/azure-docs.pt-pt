---
title: "Implementar uma aplicação .NET num contentor do Azure Service Fabric | Microsoft Docs"
description: "Informa-como uma aplicação .NET no Visual Studio num contentor Docker do pacote. Esta nova aplicação de \"container\", em seguida, é implementada para um cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: 31c1cee5ddc4c8893da729af884ae7b7b8a58093
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Implementar uma aplicação .NET num contentor do Windows Azure Service Fabric

Este tutorial mostra como implementar uma aplicação ASP.NET existente num contentor do Windows no Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um projeto de Docker no Visual Studio
> * Containerize uma aplicação existente
> * Configurar a integração contínua com o Visual Studio e VSTS

## <a name="prerequisites"></a>Pré-requisitos

1. Instalar [Docker CE para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para que possam executar contentores no Windows 10.
2. Familiarize-se com o [início rápido do Windows 10 contentores][link-container-quickstart].
3. Transferir o [Fabrikam fibra CallCenter] [ link-fabrikam-github] aplicação de exemplo.
4. Instalar [o Azure PowerShell][link-azure-powershell-install]
5. Instalar o [extensão de ferramentas de entrega contínua para Visual Studio 2017][link-visualstudio-cd-extension]
6. Criar um [subscrição do Azure] [ link-azure-subscription] e um [conta Visual Studio Team Services][link-vsts-account]. 
7. [Criar um cluster no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Criar um cluster no Azure
Aplicações de Service Fabric ser executadas num cluster, de um conjunto de ligados à rede de máquinas virtuais ou físicas. [Configurar um cluster do Service Fabric em execução no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) antes de criar e implementar a sua aplicação. Ao criar o cluster, escolha um SKU que suporta contentores em execução (por exemplo, o Windows Server 2016 Datacenter com contentores).

## <a name="containerize-the-application"></a>Containerize a aplicação

Agora que tem um cluster do Service Fabric em execução no Azure está pronto para criar e implementar uma aplicação de. Para iniciar a nossa aplicação a ser executado num contentor, precisamos de adicionar **Docker suporte** para o projeto no Visual Studio. Quando adiciona **suporte Docker** à aplicação, ocorrem duas coisas. Primeiro, um _Dockerfile_ é adicionada ao projeto. Este novo ficheiro descreve como a imagem do contentor tem de ser criada. Em seguida, segundo, um novo _compor o docker_ projeto é adicionado à solução. O novo projeto contém alguns ficheiros de compose docker. Componha de docker ficheiros podem ser utilizados para descrever como o contentor é executado.

Obter mais informações sobre como trabalhar com [ferramentas de contentor do Visual Studio][link-visualstudio-container-tools].

>[!NOTE]
>Se for a primeira vez que as imagens de contentor do Windows estiver a executar no seu computador, Docker CE tem obtenha as imagens de base para os contentores. As imagens que utilizou neste tutorial são 14 GB. Avançar e execute o seguinte comando terminal para solicitar as imagens de base:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Adicionar suporte de Docker

Abra o [FabrikamFiber.CallCenter.sln] [ link-fabrikam-github] ficheiro no Visual Studio.

Clique com botão direito do **FabrikamFiber.Web** projeto > **adicionar** > **Docker suporte**.

### <a name="add-support-for-sql"></a>Adicionar suporte para o SQL Server

Esta aplicação utiliza o SQL Server como o fornecedor de dados para que um servidor SQL é necessário para executar a aplicação. Referência a uma imagem de contentor do SQL Server no nosso ficheiro docker compose.override.yml.

No Visual Studio, abra **Explorador de soluções**, localizar **compor o docker**e abra o ficheiro **docker compose.override.yml**.

Navegue para o `services:` nó, adicionar um nó com o nome `db:` que define a entrada do SQL Server para o contentor.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Pode utilizar qualquer servidor de SQL que preferir para depuração local, desde que seja acessível a partir do seu anfitrião. No entanto, **localdb** não suporta `container -> host` comunicação.

>[!WARNING]
>Executar o SQL Server num contentor não suporta dados persistentes. Quando deixa de contentor, é apagar os dados. Utilize esta configuração para produção.

Navegue para o `fabrikamfiber.web:` nós e adicionar um nó subordinado com o nome `depends_on:`. Isto garante que o `db` serviço (o contentor do SQL Server) é iniciado antes da nossa aplicação web (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Atualizar a configuração da web

Volta a **FabrikamFiber.Web** do projeto, atualize a cadeia de ligação no **Web. config** ficheiro, para que apontem para o SQL Server no contentor.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Se pretender utilizar um diferente do SQL Server ao criar uma versão de compilação da aplicação web, adicione outra cadeia de ligação ao seu ficheiro web.release.config.

### <a name="test-your-container"></a>O contentor de teste

Prima **F5** para executar a aplicação e Depure no seu contentor.

Limite abre uma página de início definido da sua aplicação utilizando o endereço IP do contentor na rede interna de NAT (normalmente 172.x.x.x). Para saber mais sobre a depuração de aplicações nos contentores utilizando o Visual Studio 2017, consulte o artigo [neste artigo][link-debug-container].

![exemplo de fabrikam num contentor][image-web-preview]

O contentor está agora pronto para ser criada e empacotada numa aplicação de Service Fabric. Assim que tiver a imagem de contentor incorporada no seu computador, pode enviá-lo para qualquer registo de contentor e solicitar a para baixo para qualquer anfitrião para executar.

## <a name="get-the-application-ready-for-the-cloud"></a>Preparar a aplicação para a nuvem

Para preparar a aplicação para em execução no Service Fabric no Azure, é necessário concluir os dois passos:

1. Expor a porta em que queremos conseguir alcançar a nossa aplicação web no cluster de Service Fabric.
2. Forneça um produção pronto base de dados SQL para a nossa aplicação.

### <a name="expose-the-port-for-the-app"></a>Expor as portas para a aplicação
O cluster de Service Fabric que tiver configurado, tem de porta *80* aberta por predefinição no balanceador de carga do Azure, que equilibra e o tráfego de entrada para o cluster. Pode expomos nosso contentor esta porta através do nosso ficheiro docker-Compose.yml.

No Visual Studio, abra **Explorador de soluções**, localizar **compor o docker**e abra o ficheiro **docker-Compose.yml**.

Modificar o `fabrikamfiber.web:` nó, adicionar um nó subordinado com o nome `ports:`.

Adicionar uma entrada de cadeia `- "80:80"`. Este é o que o ficheiro docker-Compose.yml deve ter o seguinte aspeto:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Utilizar uma base de dados do SQL Server de produção
Quando em execução na produção, precisamos que os nossos dados persistentes na nossa base de dados. Não está actualmente nenhuma forma de garantir dados persistentes num contentor, por isso não é possível armazenar dados de produção no SQL Server num contentor.

É recomendável que utilizar uma base de dados do SQL do Azure. Para configurar e executar um servidor gerido do SQL Server no Azure, visite o [inícios rápidos de base de dados do SQL do Azure] [ link-azure-sql] artigo.

>[!NOTE]
>Lembre-se alterar as cadeias de ligação para o SQL server no **web.release.config** ficheiros o **FabrikamFiber.Web** projeto.
>
>Esta aplicação não consegue transições se nenhuma base de dados do SQL Server está acessível. Pode optar por prosseguir e implementar a aplicação não SQL Server.

## <a name="deploy-with-visual-studio-team-services"></a>Implementar com o Visual Studio Team Services

Para configurar a implementação utilizando o Visual Studio Team Services, tem de instalar o [extensão de ferramentas de entrega contínua para Visual Studio 2017][link-visualstudio-cd-extension]. Esta extensão torna mais fácil de implementar no Azure ao configurar o Visual Studio Team Services e obter a sua aplicação implementada ao cluster do Service Fabric.

Para começar, o seu código tem de estar alojado no controlo de origem. Assume os restantes desta secção **git** está a ser utilizado.

### <a name="set-up-a-vsts-repo"></a>Configurar um repositório VSTS
No canto inferior direito do Visual Studio, clique em **adicionar ao controlo de origem** > **Git** (ou qualquer opção que preferir).

![Prima o botão do controlo de origem][image-source-control]

No _equipa Explorer_ painel, prima **publicar repositório de Git**.

Selecione o nome do repositório VSTS e prima **repositório**.

![publicar o repositório no VSTS][image-publish-repo]

Agora que o seu código está sincronizado com um repositório de origem VSTS, pode configurar a integração contínua e entrega contínua.

### <a name="setup-continuous-delivery"></a>Configurar a entrega contínua

No _Explorador de soluções_, clique com botão direito do **solução** > **configurar contínua entrega**.

Selecione a subscrição do Azure.

Definir **alojar tipo** para **Service Fabric Cluster**.

Definir **anfitrião de destino** para o cluster de recursos de infraestrutura de serviço que criou na secção anterior.

Escolha um **contentor registo** para publicar o seu contentor para.

>[!TIP]
>Utilize o **editar** botão para criar um registo de contentor.

Prima **OK**.

![integração contínua de recursos de infraestrutura do serviço de configuração][image-setup-ci]
   
   Depois da configuração está concluída, o contentor é implementado no Service Fabric. Sempre push das atualizações para o repositório uma nova compilação e versão é executada.
   
   >[!NOTE]
   >Criar a tomar de imagens do contentor, aproximadamente, 15 minutos.
   >Primeira implementação para o cluster do Service Fabric faz com que as imagens de contentor do Windows Server Core base para ser transferido. A transferência demora adicionais 5-10 minutos a concluir.

Navegue para a aplicação do Centro de atendimento telefónico de Fabrikam utilizando o url do cluster: por exemplo, *http://mycluster.westeurope.cloudapp.azure.com*

Agora que tem de e implementar a solução de centro de atendimento telefónico de Fabrikam, é possível abrir o [portal do Azure] [ link-azure-portal] e ver a aplicação em execução no Service Fabric. Para experimentar a aplicação, abra um browser e avance para o URL do seu cluster do Service Fabric.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um projeto de Docker no Visual Studio
> * Containerize uma aplicação existente
> * Configurar a integração contínua com o Visual Studio e VSTS

A parte seguinte do tutorial, saiba como configurar [monitorização para o contentor](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
