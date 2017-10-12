---
title: "Implementar e atualizar os microsserviços do Azure localmente | Microsoft Docs"
description: "Saiba como configurar um cluster Service Fabric local, implementar nele uma aplicação existente e atualizar essa aplicação."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi;mikhegn
ms.openlocfilehash: 359677972c7e1fa3f7435052021ddfae5b1ed85e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introdução à implementação e atualização de aplicações no seu cluster local
O SDK de Service Fabric de Azure inclui um ambiente de desenvolvimento local completo que pode utilizar para rapidamente começar a implementar e gerir aplicações num cluster local. Neste artigo, cria um cluster local, implementa nele uma aplicação existente e atualiza essa aplicação para uma nova versão, tudo a partir do Windows PowerShell.

> [!NOTE]
> Este artigo assume que já [configurou o seu ambiente de desenvolvimento](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Criar um cluster local
Um cluster de Service Fabric representa um conjunto de recursos de hardware em que pode implementar aplicações. Normalmente, um cluster é constituído por qualquer número a partir de cinco até muitos milhares de máquinas. No entanto, o SDK de Service Fabric inclui uma configuração de cluster que pode ser executada num único computador.

É importante compreender que o cluster local de Service Fabric não é um emulador ou simulador. Executa o mesmo código de plataforma que se encontra nos clusters de várias máquinas. A única diferença é que executa numa única máquina os processos de plataforma que normalmente estão dispersos por cinco máquinas.

O SDK fornece duas formas de configurar um cluster local: um script do Windows PowerShell e a aplicação de tabuleiro de sistema do Gestor de Clusters Locais. Neste tutorial, utilizamos o script do PowerShell.

> [!NOTE]
> Se já criou um cluster local quando implementou uma aplicação do Visual Studio, pode ignorar esta secção.
> 
> 

1. Inicie uma nova janela do PowerShell como administrador.
2. Execute o script de configuração de cluster a partir da pasta SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    A configuração do cluster demora alguns minutos. Após a conclusão do programa de configuração, deverá ver resultados semelhantes ao seguinte:
   
    ![Saída do programa de configuração do cluster][cluster-setup-success]
   
    Agora está pronto para tentar implementar uma aplicação no cluster.

## <a name="deploy-an-application"></a>Implementar uma aplicação
O SDK de Service Fabric inclui um vasto conjunto de estruturas e ferramentas para programadores para a criação de aplicações. Se estiver interessado em aprender a criar aplicações no Visual Studio, consulte o artigo [Criar a primeira aplicação de Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Neste tutorial, utilizamos uma aplicação de exemplo existente (denominada WordCount) para que se possa concentrar nos aspetos de gestão da plataforma: a implementação, a monitorização e a atualização.

1. Inicie uma nova janela do PowerShell como administrador.
2. Importe o módulo de PowerShell do SDK de Service Fabric.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Crie um diretório para armazenar a aplicação que vai transferir e implementar, por exemplo C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Transfira a aplicação WordCount](http://aka.ms/servicefabric-wordcountapp) para a localização criada.  Nota: o browser Microsoft Edge guarda o ficheiro com a extensão *.zip*.  Altere a extensão do ficheiro para *.sfpkg*.
5. Ligar ao cluster local:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Crie uma nova aplicação utilizando o comando de implementação do SDK com um nome e um caminho para o pacote de aplicação.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Se tudo correr bem, deverá ver o seguinte resultado:
   
    ![Implementar uma aplicação no cluster local][deploy-app-to-local-cluster]
7. Para ver a aplicação em ação, inicie o navegador e navegue para [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Deverá ver:
   
    ![Interface do utilizador da aplicação implementada][deployed-app-ui]
   
    A aplicação de WordCount é simples. Inclui código JavaScript do lado do cliente para gerar "palavras" aleatórias de cinco carateres que posteriormente são reencaminhadas para a aplicação através da API Web ASP.NET. Um serviço com estado mantém um registo do número de palavras contadas. Criam-se partições com base no primeiro caráter da palavra. Pode localizar o código de origem para a aplicação de WordCount nos [exemplos de introdução clássicos](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    A aplicação que foi implementada contém quatro partições. Deste modo, as palavras que começam de A a G são armazenadas na primeira partição, as palavras de H a N são armazenadas na segunda partição e assim sucessivamente.

## <a name="view-application-details-and-status"></a>Ver detalhes e estado da aplicação
Agora que implementámos a aplicação, vamos ver alguns dos detalhes da aplicação no PowerShell.

1. Consulte todas as aplicações implementadas no cluster:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Se só implementou a aplicação do WordCount, verá algo parecido com:
   
    ![Consultar todas as aplicações implementadas no PowerShell][ps-getsfapp]
2. Vá para o próximo nível consultando o conjunto de serviços que estão incluídos na aplicação de WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Lista de serviços para a aplicação no PowerShell][ps-getsfsvc]
   
    A aplicação é constituída por dois serviços, o front-end Web e o serviço com estado que gere as palavras.
3. Por último, repare na lista de partições para WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Ver as partições de serviço no PowerShell][ps-getsfpartitions]
   
    O conjunto de comandos que utilizou, como todos os comandos do PowerShell de Service Fabric, está disponível para qualquer cluster a que se possa ligar, seja local ou remoto.
   
    Para uma interação mais visual com o cluster, pode utilizar a ferramenta de Service Fabric Explorer baseado na Web, acedendo a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) no navegador.
   
    ![Ver detalhes da aplicação no Service Fabric Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Para saber mais acerca do Service Fabric Explorer, consulte o artigo [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Atualizar uma aplicação
O Service Fabric fornece atualizações sem tempo de indisponibilidade através da monitorização do estado da aplicação à medida que se implementa no cluster. Efetue uma atualização da aplicação WordCount.

A nova versão da aplicação conta agora apenas as palavras que começam por uma vogal. À medida que a atualização é efetuada, vemos duas alterações de comportamento da aplicação. Em primeiro lugar, a taxa de aumento na contagem torna-se mais lenta, uma vez que estão a ser contadas menos palavras. Em segundo lugar, uma vez que a primeira partição tem duas vogais (A e E) e restantes partições contêm apenas uma, a contagem deverá finalmente deve começar a ultrapassar as outras.

1. [Transfira o pacote de WordCount versão 2](http://aka.ms/servicefabric-wordcountappv2) para a mesma localização onde transferiu o pacote versão 1.
2. Regresse à janela do PowerShell e utilize o comando de atualização do SDK para registar a nova versão do cluster. Em seguida, comece a atualizar a aplicação Fabric:/ WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Deverá ver o resultado seguinte no PowerShell, à medida que a atualização começa.
   
    ![Progresso da atualização do PowerShell][ps-appupgradeprogress]
3. Enquanto a atualização está a decorrer, será mais fácil monitorizar o estado a partir do Service Fabric Explorer. Inicie uma janela do navegador e navegue para [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda **Aplicações** na árvore à esquerda, escolha **WordCount** e, por fim, **fabric:/WordCount**. No separador Essentials, vê o estado da atualização à medida que avança nos domínios de atualização do cluster.
   
    ![Parar um nó no Service Fabric Explorer][sfx-upgradeprogress]
   
    À medida que se faz a atualização em cada domínio, são executadas verificações do estado de funcionamento para se certificar de que a aplicação está a comportar-se corretamente.
4. Se voltar a executar a consulta anterior para o conjunto de serviços que estão incluídos na aplicação fabric:/WordCount, notará que a versão do WordCountService foi alterada, mas a versão do WordCountWebService não o foi:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Consultar os serviços de aplicações após atualização][ps-getsfsvc-postupgrade]
   
    Este exemplo realça como o Service Fabric gere as atualizações de aplicações. Toca apenas o conjunto de serviços (ou pacotes de código/configuração dentro desses serviços) que tenham sido alterados, o que torna o processo de atualização mais rápido e fiável.
5. Por último, regresse ao navegador para observar o comportamento da nova versão de aplicação. Conforme esperado, a contagem avança mais lentamente e a primeira partição termina com um pouco mais de volume.
   
    ![Ver a nova versão da aplicação no navegador][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpeza
Antes de concluir, é importante lembrar-se de que o cluster local é real. As aplicações continuam a ser executadas em segundo plano até serem removidas.  Dependendo da natureza das suas aplicações, uma aplicação em execução pode consumir recursos significativos no seu computador. Tem várias opções para gerir as aplicações e o cluster:

1. Para remover uma aplicação individual e todos os seus dados, execute o comando seguinte:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Em alternativa, elimine a aplicação a partir do menu **AÇÕES** do Service Fabric Explorer ou do menu de contexto, na vista da lista de aplicações, à esquerda.
   
    ![Eliminar uma aplicação no Service Fabric Explorer][sfe-delete-application]
2. Após eliminar a aplicação do cluster, anule o registo das versões 1.0.0 e 2.0.0 do tipo de aplicação WordCount. A eliminação remove pacotes de aplicação, incluindo o código e a configuração, do armazém de imagem do cluster.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Ou, no Service Fabric Explorer, selecione **Tipo de Não Aprovisionamento** para a aplicação.
3. Para encerrar o cluster, mas manter os dados de aplicação e o rastreio, clique em **Parar Cluster Local** na aplicação de tabuleiro do sistema.
4. Para eliminar o cluster totalmente, clique em **Remover Cluster Local** na aplicação de tabuleiro do sistema. Esta opção resultará noutra implementação lenta da próxima vez que premir F5 no Visual Studio. Remova o cluster local apenas se não pretender utilizá-lo durante algum tempo ou se precisar de recuperar recursos.

## <a name="one-node-and-five-node-cluster-mode"></a>Modo do cluster de cinco nós e um nó
Ao desenvolver aplicações, irá frequentemente fazer iterações rápidas de escrita de código, depuração e alteração de código. Para ajudar a otimizar este processo, pode executar o cluster local em dois modos: um nó ou cinco nós. Ambos os modos de cluster têm as suas vantagens. O modo de cluster de cinco nós permite-lhe trabalhar com um cluster real. Pode testar cenários de ativação pós-falha, trabalhar com mais instâncias e réplicas dos seus serviços. O modo de cluster de um nó foi otimizado para implementações e registos rápidos dos serviços, com vista a ajudá-lo a validar rapidamente o código através do runtime do Service Fabric.

Os modos do cluster de um nó e de cinco nós não são um emulador ou simulador. O cluster de desenvolvimento local executa o mesmo código de plataforma que se encontra nos clusters de várias máquinas.

> [!WARNING]
> Ao alterar o modo de cluster, o cluster atual é removido do sistema e é criado um novo. Os dados armazenados no cluster são eliminados ao alterar o modo de cluster.
> 
> 

Para alterar para o modo de cluster de um nó, selecione **Trocar Modo do Cluster** no Gestor de Clusters Locais do Service Fabric.

![Alternar o modo do cluster][switch-cluster-mode]

Em alternativa, altere o modo de cluster através do PowerShell:

1. Inicie uma nova janela do PowerShell como administrador.
2. Execute o script de configuração de cluster a partir da pasta SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    A configuração do cluster demora alguns minutos. Após a conclusão do programa de configuração, deverá ver resultados semelhantes ao seguinte:
   
    ![Saída do programa de configuração do cluster][cluster-setup-success-1-node]

## <a name="next-steps"></a>Passos seguintes
* Agora que implementou e atualizou algumas aplicações pré-criadas, pode [tentar criar a sua no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Todas as ações executadas no cluster local neste artigo também podem ser realizadas num [cluster do Azure](service-fabric-cluster-creation-via-portal.md).
* A atualização que efetuámos neste artigo foi básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a capacidade e a flexibilidade das atualizações do Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
