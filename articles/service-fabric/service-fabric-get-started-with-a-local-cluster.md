<properties
   pageTitle="Introdução à implementação e atualização de aplicações no seu cluster local | Microsoft Azure"
   description="Configurar um cluster Service Fabric local, implementar nele uma aplicação existente e atualizar essa aplicação."
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
   ms.date="06/09/2016"
   ms.author="ryanwi"/>

# Introdução à implementação e atualização de aplicações no seu cluster local
O SDK de Service Fabric de Azure inclui um ambiente de desenvolvimento local completo que pode utilizar para rapidamente começar a implementar e gerir aplicações num cluster local. Neste artigo, criará um cluster local, implementará nele uma aplicação existente e atualizará essa aplicação para uma nova versão, tudo a partir do Windows PowerShell.

> [AZURE.NOTE] Este artigo assume que já [configurou o seu ambiente de desenvolvimento](service-fabric-get-started.md).

## Criar um cluster local
Um cluster de Service Fabric representa um conjunto de recursos de hardware em que pode implementar aplicações. Normalmente, um cluster é constituído por qualquer número a partir de cinco até muitos milhares de máquinas. No entanto, o SDK de Service Fabric inclui uma configuração de cluster que pode ser executada num único computador.

É importante compreender que o cluster local de Service Fabric não é um emulador ou simulador. Executa o mesmo código de plataforma que se encontra nos clusters de várias máquinas. A única diferença é que executa numa única máquina os processos de plataforma que normalmente estão dispersos por cinco máquinas.

O SDK fornece duas formas de configurar um cluster local: um script do Windows PowerShell e a aplicação de tabuleiro de sistema do Gestor de Clusters Locais. Neste tutorial, utilizamos o script do PowerShell.

> [AZURE.NOTE] Se já criou um cluster local quando implementou uma aplicação do Visual Studio, pode ignorar esta secção.


1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de configuração de cluster a partir da pasta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    A configuração do cluster demora alguns minutos. Após a conclusão do programa de configuração, deve ver resultados com aspeto semelhante ao seguinte:

    ![Saída do programa de configuração do cluster][cluster-setup-success]

    Agora está pronto para tentar implementar uma aplicação no cluster.

## Implementar uma aplicação
O SDK de Service Fabric inclui um vasto conjunto de estruturas e ferramentas para programadores para a criação de aplicações. Se estiver interessado em aprender a criar aplicações no Visual Studio, consulte o artigo [Criar a primeira aplicação de Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Neste tutorial, utilizaremos uma aplicação de exemplo existente (denominada WordCount) para que possamos concentrar-nos nos aspetos de gestão da plataforma que incluem a implementação, a monitorização e a atualização.


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

4. [Transfira a aplicação WordCount](http://aka.ms/servicefabric-wordcountapp) para a localização criada.  Nota: o browser da Microsoft Edge irá guardar o ficheiro com a extensão *.zip*.  Terá de alterar a extensão do ficheiro para *.sfpkg*.

5. Ligar ao cluster local:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Invoque o comando de implementação do SDK para criar uma nova aplicação e indique um nome e um percurso para o pacote de aplicação.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se tudo correr bem, deverá ver um resultado como o seguinte:

    ![Implementar uma aplicação no cluster local][deploy-app-to-local-cluster]

7. Para ver a aplicação em ação, inicie o navegador e navegue para [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Deverá ver algo semelhante ao seguinte:

    ![Interface do utilizador da aplicação implementada][deployed-app-ui]

    A aplicação de WordCount é muito simples. Inclui código JavaScript do lado do cliente para gerar "palavras" aleatórias de cinco carateres que posteriormente são reencaminhadas para a aplicação através da API Web ASP.NET. Um serviço com estado mantém um registo do número de palavras contadas. Criam-se partições com base no primeiro caráter da palavra. Pode localizar o código de origem para a aplicação de WordCount nos [exemplos de introdução](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    A aplicação que foi implementada contém quatro partições. Deste modo, as palavras que começam de A a G são armazenadas na primeira partição, as palavras de H a N são armazenadas na segunda partição e assim sucessivamente.

## Ver detalhes e estado da aplicação
Agora que implementámos a aplicação, vamos ver alguns dos detalhes da aplicação no PowerShell.

1. Consulte todas as aplicações implementadas no cluster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Se só implementou a aplicação do WordCount, verá algo parecido com isto:

    ![Consultar todas as aplicações implementadas no PowerShell][ps-getsfapp]

2. Vá para o próximo nível consultando o conjunto de serviços que estão incluídos na aplicação de WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Lista de serviços para a aplicação no PowerShell][ps-getsfsvc]

    Tenha em atenção que a aplicação é constituída por dois serviços - o front-end Web e o serviço com estado que gere as palavras.

3. Por último, repare na lista de partições para WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Ver as partições de serviço no PowerShell][ps-getsfpartitions]

    O conjunto de comandos que acabou de utilizar, como todos os comandos do PowerShell de Service Fabric, estão disponíveis para qualquer cluster a que poderá ligar, seja local ou remoto.

    Para uma interação mais visual com o cluster, pode utilizar a ferramenta de Service Fabric Explorer baseado na Web, acedendo a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) no navegador.

    ![Ver detalhes da aplicação no Service Fabric Explorer][sfx-service-overview]

    > [AZURE.NOTE] Para saber mais acerca do Service Fabric Explorer, consulte o artigo [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Atualizar uma aplicação
O Service Fabric fornece atualizações sem tempo de indisponibilidade através da monitorização do estado da aplicação à medida que se implementa no cluster. Vamos efetuar uma atualização simples da aplicação WordCount.

A nova versão da aplicação agora contará apenas as palavras que começam por uma vogal. À medida que a atualização se faz, vemos duas alterações de comportamento da aplicação. Em primeiro lugar, a taxa de aumento na contagem torna-se mais lenta, uma vez que estão a ser contadas menos palavras. Em segundo lugar, uma vez que a primeira partição tem duas vogais (A e E) e restantes partições contêm apenas uma, a contagem deverá finalmente deve começar a ultrapassar as outras.

1. [Transfira o pacote de WordCount v2](http://aka.ms/servicefabric-wordcountappv2) para a mesma localização onde transferiu o pacote v1.

2. Regresse à janela do PowerShell e utilize o comando de atualização do SDK para registar a nova versão do cluster. Em seguida, comece a atualizar a aplicação Fabric:/ WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Deverá ver uma saída no PowerShell, com um aspeto semelhante ao seguinte, ao começar a atualização.

    ![Progresso da atualização do PowerShell][ps-appupgradeprogress]

3. Enquanto a atualização está a decorrer, será mais fácil monitorizar o estado a partir do Service Fabric Explorer. Inicie uma janela do navegador e navegue para [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda **Aplicações** na árvore à esquerda, escolha **WordCount** e, por fim, **fabric:/WordCount**. No separador Essentials, verá o estado da atualização à medida que avança nos domínios de atualização do cluster.

    ![Parar um nó no Service Fabric Explorer][sfx-upgradeprogress]

    À medida que se faz a atualização em cada domínio, são executadas verificações do estado de funcionamento para se certificar de que a aplicação está a comportar-se corretamente.

4. Se voltar a executar a consulta anterior para o conjunto de serviços que estão incluídos na aplicação fabric:/WordCount, notará que enquanto a versão do WordCountService foi alterada, a versão do WordCountWebService não o foi:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Consultar os serviços de aplicações após atualização][ps-getsfsvc-postupgrade]

    Isto realça como o Service Fabric gere as atualizações de aplicações. Toca apenas o conjunto de serviços (ou pacotes de código/configuração dentro desses serviços) que tenham sido alterados, o que torna o processo de atualização mais rápido e fiável.

5. Por último, regresse ao navegador para observar o comportamento da nova versão de aplicação. Conforme esperado, a contagem avança mais lentamente e a primeira partição termina com um pouco mais de volume.

    ![Ver a nova versão da aplicação no navegador][deployed-app-ui-v2]

## Limpeza

Antes de concluir, é importante lembrar-se de que o cluster local é real. As aplicações continuarão a ser executadas em segundo plano até serem removidas.  Dependendo da natureza das suas aplicações, uma aplicação em execução pode consumir recursos significativos no seu computador. Tem várias opções para lidar com isto:

1. Para remover uma aplicação individual e todos os seus dados, execute o seguinte:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Ou utilize a ação **Eliminar aplicação** no Service Fabric Explorer com o menu **AÇÕES** ou com o menu de contexto, na vista da lista de aplicações, no painel da esquerda.

    ![Eliminar uma aplicação no Service Fabric Explorer][sfe-delete-application]

2. Depois de eliminar a aplicação do cluster, pode, em seguida, anular o registo das versões 1.0.0 e 2.0.0 do tipo de aplicação WordCount. Esta ação remove pacotes de aplicação, incluindo o código e a configuração, do armazém de imagem do cluster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Ou, no Service Fabric Explorer, selecione **Tipo de Não Aprovisionamento** para a aplicação.

3. Para encerrar o cluster, mas manter os dados de aplicação e o rastreio, clique em **Parar Cluster Local** na aplicação de tabuleiro do sistema.

4. Para eliminar o cluster totalmente, clique em **Remover Cluster Local** na aplicação de tabuleiro do sistema. Tenha em atenção que esta opção resultará noutra implementação lenta da próxima vez que premir F5 no Visual Studio. Utilize esta opção apenas se não pretender utilizar o cluster local durante algum tempo ou se precisar de recuperar recursos.

## Passos seguintes
- Agora que implementou e atualizou algumas aplicações pré-criadas, pode [tentar criar a sua no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas as ações executadas no cluster local neste artigo também podem ser efetuadas num [cluster do Azure](service-fabric-cluster-creation-via-portal.md).
- A atualização que efetuámos neste artigo foi muito básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a capacidade e a flexibilidade das atualizações do Service Fabric.

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



<!--HONumber=Aug16_HO1-->


