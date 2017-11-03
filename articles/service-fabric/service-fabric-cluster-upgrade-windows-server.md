---
title: "Atualizar um cluster do Azure Service Fabric autónomo no Windows Server | Microsoft Docs"
description: "Atualize o código de Service Fabric do Azure e/ou a configuração que executa um cluster do Service Fabric autónomo, incluindo a definir o modo de atualização do cluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: c95c1827d0433dcb61eace34e7a905a5610c7781
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Atualize o cluster do Azure Service Fabric autónomo no Windows Server 
> [!div class="op_single_selector"]
> * [Cluster do Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autónomo](service-fabric-cluster-upgrade-windows-server.md)
>
>

Para qualquer sistema moderno, a capacidade de atualização é importante para o sucesso de longo prazo do produto. Um cluster do Service Fabric do Azure é um recurso que é proprietário. Este artigo descreve como garantir que o cluster executa sempre as versões suportadas do código de Service Fabric e configurações.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de Service Fabric que é executado no seu cluster
Para configurar o cluster para transferir as atualizações do Service Fabric quando a Microsoft disponibiliza uma nova versão, definir a configuração do cluster fabricClusterAutoupgradeEnabled *verdadeiro*. Para selecionar uma versão suportada do Service Fabric que pretende que o cluster na, defina a configuração do cluster fabricClusterAutoupgradeEnabled como *falso*.

> [!NOTE]
> Certifique-se de que o cluster é sempre executada uma versão suportada do Service Fabric. Quando a Microsoft announces a versão de uma nova versão de Service Fabric, a versão anterior está marcada para fim de suporte após um mínimo de 60 dias a contar da data em que o anúncio. Novos lançamentos sejam anunciados [no blogue de equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolher nesse momento.
>
>

Pode atualizar o cluster para a nova versão apenas se estiver a utilizar uma configuração de nó de estilo de produção, onde cada nó de Service Fabric está alocada numa máquina virtual ou físico separado. Se tiver um cluster de desenvolvimento, em que mais do que um nó de Service Fabric é numa única máquina física ou virtual, tem de recriar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o cluster para a versão mais recente ou uma versão suportada do Service Fabric. Um fluxo de trabalho é para os clusters que tem conectividade para transferir a versão mais recente automaticamente. Outro fluxo de trabalho é para os clusters que não tem conectividade para transferir a versão mais recente de Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar clusters tem conectividade para transferir o código e a configuração mais recente
Utilize estes passos para atualizar o cluster para uma versão suportada, se os nós do cluster tiverem conectividade à internet para o [Microsoft Download Center](http://download.microsoft.com).

Para os clusters que tem conetividade para o [Microsoft Download Center](http://download.microsoft.com), Microsoft verifica periodicamente a disponibilidade de novas versões de Service Fabric.

Quando estiver disponível uma nova versão de Service Fabric, o pacote é transferido localmente para o cluster e aprovisionado para atualização. Além disso, para informar o cliente desta nova versão, o sistema mostra um aviso de estado de funcionamento de explícita de cluster que é semelhante ao seguinte:

"A versão atual do cluster [versão #] o suporte termina [date]."

Depois do cluster está a executar a versão mais recente, o aviso passa ausente.

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização de cluster
Quando vir o aviso de estado de funcionamento do cluster, efetue o seguinte:

1. Ligar ao cluster a partir de qualquer computador que tenha acesso de administrador para todas as máquinas que estão listados como nós do cluster. A máquina em que este script é executado no não tem de ser parte do cluster.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obter a lista de versões de Service Fabric que pode atualizar para o.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Deve obter um resultado semelhante a isto:

    ![Obter versões de Service Fabric][getfabversions]
3. Iniciar uma atualização do cluster para uma versão disponível utilizando o [início ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) comando do Windows PowerShell.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Para monitorizar o progresso da atualização, pode utilizar o Explorador do Service Fabric ou execute o seguinte comando do PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de estado de funcionamento do cluster não são cumpridas, a atualização é revertida. Para especificar políticas de estado de funcionamento personalizado para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [início ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização ao seguir os mesmos passos conforme descrito anteriormente.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar clusters que tenham *sem conectividade* para transferir o código e a configuração mais recente
Utilize estes passos para atualizar o cluster para uma versão suportada, se os nós do cluster não tem ligação à internet para o [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Se estiver a executar um cluster que não está ligado à internet, terá de monitorizar o blogue da equipa de Service Fabric para saber mais sobre os novos lançamentos. O sistema não mostra um aviso de estado de funcionamento de cluster para o alertar das versões de novo.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Auto aprovisionamento vs. de aprovisionamento manual
Para ativar a transferência automática e o registo para a versão mais recente do código, configure o serviço de atualização do serviço de recursos de infraestrutura. Para obter instruções, consulte Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt dentro de [autónomo pacote](service-fabric-cluster-standalone-package-contents.md).
Para o processo manual, siga estas instruções.

Modifique a sua configuração de cluster para definir a propriedade seguinte *falso* antes de iniciar uma atualização da configuração:

        "fabricClusterAutoupgradeEnabled": false,

Para detalhes de utilização, consulte o [comando do PowerShell de início ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Certifique-se atualizar clusterConfigurationVersion no seu JSON antes de iniciar a atualização da configuração.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização de cluster

1. Execute Get-ServiceFabricClusterUpgrade a partir de um de nós no cluster e tenha em atenção o TargetCodeVersion.

2. Execute o seguinte a partir de uma máquina ligada à internet para listar todas as versões de atualização compatível com a versão atual e transferir o pacote correspondente de ligações de transferência associados:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Ligar ao cluster a partir de qualquer computador que tenha acesso de administrador para todas as máquinas que estão listados como nós do cluster. A máquina em que este script é executado no não tem de ser parte do cluster.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Copie o pacote transferido para o arquivo de imagens do cluster.

5. Registe o pacote copiado.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Inicie uma atualização de cluster para uma versão disponível.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pode monitorizar o progresso da atualização no Service Fabric Explorer, ou pode executar o seguinte comando do PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de estado de funcionamento do cluster não são cumpridas, a atualização é revertida. Para especificar políticas de estado de funcionamento personalizado para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [início ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização ao seguir os mesmos passos conforme descrito anteriormente.


## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração de cluster
Antes de iniciar a atualização da configuração, pode testar a configuração do cluster novo JSON, executando o seguinte script do PowerShell no pacote autónomo:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Ou utilize este script:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Algumas configurações não podem ser atualizado, como pontos finais, nome do cluster, IP de nó, etc. A nova configuração de cluster JSON é testada relativamente o um antigo e emite erros na janela do PowerShell, se existir um problema.

Para atualizar a atualização da configuração de cluster, execute Start-ServiceFabricClusterConfigurationUpgrade. A atualização da configuração é o domínio de atualização processado por domínio de atualização.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Atualização de configuração do certificado de cluster  
Um certificado de cluster é utilizado para autenticação entre nós de cluster. O rollover de certificado deve ser efetuado com cuidado extra porque falha bloqueia a comunicação entre nós de cluster.

Tecnicamente, são suportadas quatro opções:  

* Atualização do certificado único: O caminho de atualização é o certificado de um site (primário) -> B do certificado (primário) -> certificado C (primário) ->...

* Atualização do certificado duplo: O caminho de atualização é um site (primário) de certificado do certificado de um site (primário) -> e B (secundário) -> B do certificado (primário) -> B do certificado (principal) e C (secundário) -> certificado C (primário) ->...

* Atualização do tipo de certificado: baseado na Thumbprint do certificado <> - certificado com base em CommonName configuração. Por exemplo, o Thumbprint do certificado (principal) e Thumbprint B (secundário)-C. CommonName do certificado >

* Atualização de thumbprint do emissor do certificado: O caminho de atualização é certificado CN = A, IssuerThumbprint = IT1 (principal) -> CN do certificado = A, IssuerThumbprint = IT1, IT2 (principal) -> CN do certificado = A, IssuerThumbprint = IT2 (principal).


## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas [definições de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [reduzir e ampliar o seu cluster](service-fabric-cluster-scale-up-down.md).
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
