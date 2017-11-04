---
title: "Pacote de autónoma de recursos de infraestrutura de serviço do Azure para o Windows Server | Microsoft Docs"
description: "A descrição e o conteúdo do pacote do Azure Service Fabric autónoma para o Windows Server."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik
ms.openlocfilehash: 6a6bacedde04419449a061554d5ce9ad37259a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Conteúdo do pacote de serviço Fabric autónoma para o Windows Server
No [transferido](http://go.microsoft.com/fwlink/?LinkId=730690) pacote autónoma de recursos de infraestrutura de serviço, irá encontrar os seguintes ficheiros:

| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Um script do PowerShell que cria o cluster utilizando as definições em Clusterconfig. |
| RemoveServiceFabricCluster.ps1 |Um script do PowerShell que remove um cluster utilizando as definições em Clusterconfig. |
| AddNode.ps1 |Um script do PowerShell para adicionar um nó a um existente implementada cluster da máquina atual. |
| RemoveNode.ps1 |Um script do PowerShell para remover um nó do existente implementada cluster da máquina atual. |
| CleanFabric.ps1 |Um script do PowerShell para a limpeza autónoma instalação de Service Fabric desligar a máquina atual. Instalações anteriores do MSI devem ser removidas utilizando os seus próprios uninstallers associados. |
| TestConfiguration.ps1 |Um script do PowerShell para analisar a infraestrutura conforme especificado no Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Um script do PowerShell utilizado para transferir o pacote de tempo de execução mais recente fora de banda, para cenários em que a máquina de implementação não está ligada à internet. |
| DeploymentComponentsAutoextractor.exe |Arquivo de extração que contenha os componentes de implementação utilizados pelos scripts de pacote autónomo. |
| EULA_ENU.txt |Os termos de licenciamento para a utilização do pacote do Microsoft Azure Service Fabric autónomo do Windows Server. Pode [transferir uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora. |
| Readme.txt |Uma ligação para as notas de versão e instruções de instalação básico. É um subconjunto das instruções neste documento. |
| ThirdPartyNotice.rtf |Aviso de software de terceiros que se encontra no pacote. |
| Tools\Microsoft.Azure.ServiceFabric.Windowsserver.SupportPackage.zip |StandaloneLogCollector.exe qual é executado a pedido para recolher e carregar registos de rastreio para a Microsoft para fins de suporte. |
| Tools\ServiceFabricUpdateService.zip |Uma ferramenta utilizada para ativar automaticamente a atualização de código para clusters que não têm acesso à internet. Pode encontrar mais detalhes sobre [aqui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelos** 
| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém as definições para um protegida, três nós, máquina único (ou máquina virtual) cluster de desenvolvimento, incluindo as informações para cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém as definições para um cluster não segura, multi máquina (ou máquina virtual), incluindo as informações para cada máquina no cluster. |
| ClusterConfig.Windows.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para um seguro e três nós, máquina único (ou máquina virtual) desenvolvimento cluster, incluindo as informações para cada nó no cluster. O cluster está protegido pela utilização [identidades Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para um cluster de máquina multi (ou máquina virtual) segura, utilizar a segurança do Windows, incluindo as informações para cada máquina no cluster seguro. O cluster está protegido pela utilização [identidades Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para um seguro e três nós, máquina único (ou máquina virtual) cluster de desenvolvimento, incluindo as informações para cada nó no cluster. O cluster está protegido com x509 certificados. |
| ClusterConfig.x509.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para o cluster segura, multi máquina (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster está protegido com x509 certificados. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para o cluster segura, multi máquina (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster está protegido por [contas de serviço geridas de grupo](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Exemplos de configuração de cluster
Versões mais recentes dos modelos de configuração do cluster podem ser encontradas na página do GitHub: [exemplos de configuração de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Pacote de tempo de execução independente
O pacote de tempo de execução mais recente é transferido automaticamente durante a implementação de cluster do [hiperligação Transferir - tempo de execução do serviço de recursos de infraestrutura - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relacionado
* [Criar um cluster do Azure Service Fabric autónomo](service-fabric-cluster-creation-for-windows-server.md)
* [Cenários de segurança de cluster do Service Fabric](service-fabric-windows-cluster-windows-security.md)
