---
title: Configurar o ambiente de desenvolvimento | Microsoft Docs
description: "Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto a criar aplicações."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/15/2016
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: d9050347434a22fa79f5e751c676f77bf025d176
ms.openlocfilehash: e2919b07b8fc6e9dff6823cb6995f52106612b8a


---
# <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Para compilar e executar [aplicações do Service Fabric do Azure][1] no computador de desenvolvimento, tem de instalar o runtime, o SDK e as ferramentas. Também precisa de ativar a execução dos scripts do Windows PowerShell incluídos no SDK.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operativo com suporte
As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> O Windows 7 inclui, por predefinição, apenas o Windows PowerShell 2.0. Os cmdlets do PowerShell do Service Fabric necessitam do PowerShell 3.0 ou superior. Pode [transferir o Windows PowerShell 5.0][powershell5-download] no Centro de Transferências da Microsoft.
> 
> 

## <a name="install-the-sdk-and-tools"></a>Instalar o SDK e as ferramentas
### <a name="to-use-visual-studio-2017-rc"></a>Utilizar o Visual Studio 2017 RC
As Ferramentas do Service Fabric fazem parte da carga de trabalho de Desenvolvimento e de Gestão do Azure no Visual Studio 2017 RC. Deve ativar esta carga de trabalho como parte da instalação do Visual Studio.
Para além disso, deve instalar o Microsoft Azure Service Fabric SDK, utilizando o Instalador de Plataforma Web.

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Utilizar Visual Studio 2015 (requer a Atualização 2 do Visual Studio 2015 ou posterior)
Para o Visual Studio 2015, as ferramentas do Service Fabric são instaladas juntamente com o SDK, utilizando o Instalador de Plataforma Web:

* [Instalar o SDK e as Ferramentas do Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Apenas instalação do SDK
Se apenas precisa do SDK, pode instalar este pacote:
* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

> [!WARNING]
> Os clientes comunicaram erros durante a instalação ao utilizar estas ligações de início, ou quando estas ligações eram utilizadas no browser do Chrome. Estes são problemas conhecidos no Instalador da Plataforma Web que estão a ser resolvidos.  Como solução, tente o seguinte:
>- Inicie as ligações acima nos browsers do Internet Explorer ou do Edge, ou
>- Inicie o Instalador de Plataforma Web a partir do menu Iniciar, procure o "Service Fabric" e instale o SDK
> 
> Pedimos desculpa pelo inconveniente. 

As versões atuais são:
* SDK do Service Fabric 2.4.145
* Runtime do Service Fabric 5.4.145
* Ferramentas do Visual Studio 2015 1.4.41209

Para obter uma lista das versões suportadas, consulte [Service Fabric support (Suporte do Service Fabric)](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>Ativar a execução do script do PowerShell
O Service Fabric utiliza scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para implementação de aplicações do Visual Studio. Por predefinição, o Windows bloqueará a execução destes scripts. Para ativá-los, tem de modificar a política de execução do PowerShell. Abra o PowerShell como administrador e introduza o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Passos seguintes
Agora que o ambiente de desenvolvimento está configurado, pode começar a criar e executar aplicações.

* [Criar a primeira aplicação do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Saiba como implementar e gerir aplicações no cluster local](service-fabric-get-started-with-a-local-cluster.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Veja exemplos de códigos do Service Fabric em GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster através do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Seguir o percurso de aprendizagem do Service Fabric para obter uma introdução abrangente da plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ligação VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ligação Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ligação Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Dec16_HO5-->


