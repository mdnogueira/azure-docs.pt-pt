---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Crie um cluster autónomo de desenvolvimento com três nós em execução no mesmo computador. Depois de concluir esta configuração, estará pronto a criar um cluster de várias máquinas."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Crie o primeiro cluster autónomo do Service Fabric
Pode criar um cluster autónomo do Service Fabric em quaisquer máquinas virtuais ou computadores com o Windows Server 2012 R2 ou o Windows Server 2016, no local ou na cloud. Este guia de introdução ajuda-o a criar um cluster autónomo de desenvolvimento em apenas alguns minutos.  Quando tiver terminado, tem um cluster com três nós em execução num computador único onde pode implementar aplicações.

## <a name="before-you-begin"></a>Antes de começar
O Service Fabric fornece um pacote de configuração para criar clusters autónomos do Service Fabric.  [Transferir o pacote de configuração](http://go.microsoft.com/fwlink/?LinkId=730690).  Deszipe o pacote de configuração para uma pasta no computador ou na máquina virtual em que estiver a configurar o cluster de desenvolvimento.  Os conteúdos do pacote de configuração estão descritos em detalhe [aqui](service-fabric-cluster-standalone-package-contents.md).

O administrador do cluster que irá implementar e configurar o cluster tem de ter privilégios de administrador no computador. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="validate-the-environment"></a>Validar o ambiente
O script *TestConfiguration.ps1* no pacote autónomo é utilizado como um analisador de melhores práticas para validar se um cluster pode ser implementado num determinado ambiente. A [preparação da implementação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e os requisitos do ambiente. Execute o script para verificar se pode criar o cluster de desenvolvimento:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Criar o cluster
São instalados vários ficheiros de configuração de cluster de exemplo com o pacote de configuração. O *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster inseguro com três nós em execução num único computador.  Outros ficheiros de configuração descrevem clusters únicos ou de várias máquinas protegidas com certificados X.509 ou de segurança do Windows.  Não precisa de modificar qualquer uma das definições de configuração predefinida para este tutorial, mas examine o ficheiro de configuração e familiarize-se com as definições.  A secção **nós** descreve os três nós do cluster: nome, endereço IP, [tipo de nó, domínio de falha e o domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A secção **propriedades** define a [segurança, o nível de fiabilidade, a recolha de diagnóstico e os tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

Este cluster não é seguro.  Qualquer pessoa pode ligar de forma anónima e efetuar operações de gestão, para que os clusters de produção sejam sempre protegidos ao utilizar certificados X.509 ou segurança do Windows.  A segurança só está configurada no momento de criação do cluster e não é possível ativá-la depois de o cluster ser criado.  Leia [Secure a cluster (Proteger um cluster)](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.  

Para criar o cluster de desenvolvimento com três nós, execute o script *CreateServiceFabricCluster.ps1* a partir de uma sessão do PowerShell do administrador:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

O pacote do runtime do Service Fabric é transferido e instalado automaticamente no momento da criação do cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster
O cluster de desenvolvimento com três nós está agora em execução. O módulo do ServiceFabric PowerShell é instalado com o runtime.  Pode verificar que o cluster está em execução a partir do mesmo computador ou de um computador remoto com o runtime do Service Fabric.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma ligação ao cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Consulte [Connect to a secure cluster (Ligar a um cluster seguro)](service-fabric-connect-to-secure-cluster.md) para outros exemplos de ligação a um cluster. Depois de ligar ao cluster, utilize o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para apresentar uma lista de nós no cluster e informações de estado para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualize o cluster com o explorador do Service Fabric
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o seu cluster e gerir aplicações.  O Service Fabric Explorer é um serviço que é executado no cluster, que poderá aceder através de um browser, ao navegar para [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

O dashboard do cluster fornece uma descrição geral do cluster, incluindo um resumo de aplicações e do estado de funcionamento do nó. A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Remover o cluster
Para remover um cluster, execute o script *RemoveServiceFabricCluster.ps1* do PowerShell a partir da pasta do pacote e passe no caminho para o ficheiro de configuração do JSON. Opcionalmente, pode especificar uma localização para o registo da eliminação.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Para remover o tempo de execução do Service Fabric do computador, execute o seguinte script do PowerShell a partir da pasta do pacote.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Passos seguintes
Agora que configurou um cluster autónomo de desenvolvimento, experimente os artigos seguintes:
* [Configure um cluster autónomo de várias máquinas](service-fabric-cluster-creation-for-windows-server.md) e ative a segurança.
* [Implementar aplicações com o Powershell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
