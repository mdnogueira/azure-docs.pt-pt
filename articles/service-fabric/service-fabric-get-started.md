<properties
   pageTitle="Configurar o ambiente de desenvolvimento | Microsoft Azure"
   description="Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto a criar aplicações."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/15/2016"
   ms.author="ryanwi"/>

# Preparar o ambiente de desenvolvimento
 Para compilar e executar [Aplicações do Service Fabric do Azure][1] no computador de desenvolvimento, tem de instalar o runtime, o SDK e as ferramentas. Também precisa de ativar a execução dos scripts do Windows PowerShell incluídos no SDK.

## Pré-requisitos
### Versões de sistema operativo com suporte
As seguintes versões do sistema operativo são suportadas para desenvolvimento:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] O Windows 7 inclui, por predefinição, apenas o Windows PowerShell 2.0. Para utilizar os cmdlets do PowerShell para o Service Fabric, terá de instalar o PowerShell 3.0 ou superior. Pode [transferir o Windows PowerShell 5.0][powershell5-download] no Centro de Transferências da Microsoft.

## Instalar o runtime, o SDK e as ferramentas

O Instalador de Plataforma Web oferece três configurações para desenvolvimento do Service Fabric:

- [Instalar o runtime Service Fabric, o SDK e as ferramentas do Visual Studio 2015 Update 2][full-bundle-vs2015]
- [Instalar a Pré-visualização "15" do runtime , do SDK e das ferramentas do Service Fabric para Visual Studio][full-bundle-dev15]
- [Instalar apenas o runtime e o SDK do Service Fabric (sem as ferramentas do Visual Studio)][core-sdk]


## Ativar a execução do script do PowerShell

O Service Fabric utiliza scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para implementação de aplicações do Visual Studio. Por predefinição, o Windows bloqueará a execução destes scripts. Para ativá-los, tem de modificar a política de execução do PowerShell. Abra o PowerShell como administrador e introduza o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Passos seguintes
Agora que o ambiente de desenvolvimento está configurado, pode começar a criar e executar aplicações.

- [Criar a primeira aplicação do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Saiba como implementar e gerir aplicações no cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
- [Veja exemplos de códigos do Service Fabric em GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar o cluster através do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Seguir o percurso de aprendizagem do Service Fabric para obter uma introdução abrangente da plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015-2_1 "Link VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15-2_1 "Link Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK_2_1 "Link Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Aug16_HO1-->


