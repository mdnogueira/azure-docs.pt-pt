---
title: "Atualização do App Service Fabric com o PowerShell | Microsoft Docs"
description: "Este artigo explica a experiência de implementação de uma aplicação de Service Fabric, alterar o código e disponibilizando uma atualização através do PowerShell."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 54ca664a29ed8c6337bb27fe1fa17276e480c911
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Atualização da aplicação de Service Fabric com o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Mais frequentemente utilizadas e a abordagem recomendada de atualização é a atualização sem interrupção monitorizada.  Azure Service Fabric monitoriza o estado de funcionamento da aplicação a ser atualizado com base num conjunto de políticas de estado de funcionamento. Depois de um domínio de atualização (UD) estiver atualizado, o Service Fabric avalia o estado de funcionamento da aplicação e avança para o domínio de atualização seguinte ou falha da atualização, consoante as políticas de estado de funcionamento.

Uma atualização da aplicação monitorizada pode ser efetuada utilizando o nativo ou gerido APIs, PowerShell ou REST. Para obter instruções sobre como executar uma atualização com o Visual Studio, consulte [atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com atualizações graduais do Service Fabric monitorizado, o administrador da aplicação pode configurar a política de avaliação do Estado de funcionamento que utiliza o Service Fabric para determinar se a aplicação está em bom estada. Além disso, o administrador pode configurar a ação a executar quando a avaliação de estado de funcionamento falha (por exemplo, efetuar uma reversão automática.) Esta secção explica uma atualização monitorizada para um dos exemplos de SDK que utiliza o PowerShell. A seguinte Microsoft Virtual Academy vídeo também explica-lhe uma atualização da aplicação:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Criar e implementar o exemplo de objetos Visual
Criar e publicar a aplicação ao clicar no projeto de aplicação, **VisualObjectsApplication,** e selecionando a **publicar** comando.  Para obter mais informações, consulte [tutorial de atualização de aplicação de Service Fabric](service-fabric-application-upgrade-tutorial.md).  Em alternativa, pode utilizar o PowerShell para implementar a sua aplicação.

> [!NOTE]
> Antes de qualquer um dos comandos Service Fabric pode ser utilizado no PowerShell, terá primeiro de ligar ao cluster utilizando o `Connect-ServiceFabricCluster` cmdlet. Da mesma forma, é assumido que o Cluster tiver já sido configurado no seu computador local. Consulte o artigo sobre [como configurar o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
> 
> 

Depois de compilar o projeto no Visual Studio, pode utilizar o comando do PowerShell [cópia ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) para copiar o pacote de aplicação para o arquivo de imagens. Se pretende verificar o pacote de aplicação localmente, utilize o [teste ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet. O passo seguinte consiste em registar a aplicação para o tempo de execução do Service Fabric utilizando o [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet. O passo seguinte é para iniciar uma instância da aplicação utilizando o [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet.  Estes três passos são análogos a utilizar o **implementar** item de menu no Visual Studio.  Depois de concluído o aprovisionamento, deve limpar o pacote de aplicação copiado do arquivo de imagens para reduzir os recursos consumidos.  Se já não é necessário um tipo de aplicação, deve ser anular o registo para a mesma razão. Consulte [implementar e remover aplicações utilizando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) para obter mais informações.

Agora, pode utilizar [Service Fabric Explorer para ver o cluster e a aplicação](service-fabric-visualizing-your-cluster.md). A aplicação tem um serviço web que pode ser utilizado no Internet Explorer, escrevendo [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na barra de endereço.  Deverá ver alguns objetos visual flutuante mover à volta, no ecrã.  Além disso, pode utilizar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) para verificar o estado da aplicação.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar o exemplo de objetos Visual
Poderá reparar que com a versão que tenha sido implementada no passo 1, os objetos visual não rodar. Vamos atualizar esta aplicação para um onde os objetos visual rodar também.

Selecione o projeto de VisualObjects.ActorService na solução de VisualObjects e abra o ficheiro StatefulVisualObjectActor.cs. Esse ficheiro, navegue para o método `MoveObject`, comente `this.State.Move()`e anule os comentários `this.State.Move(true)`. Esta alteração roda os objetos depois do serviço está atualizado.

Também é necessário atualizar o *ServiceManifest.xml* ficheiro (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualização do *CodePackage* e a versão de service 2.0 e as linhas correspondentes a *ServiceManifest.xml* ficheiro.
Pode utilizar o Visual Studio *editar ficheiros de manifesto* opção depois de a com o botão direito na solução de efetuar as alterações do ficheiro de manifesto.

Depois das alterações serem efetuadas, o manifesto deverá ter um aspeto semelhante ao seguinte (partes realçadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora a *ApplicationManifest.xml* ficheiro (localizado no **VisualObjects** projeto no **VisualObjects** solução) é atualizado para a versão 2.0 do **VisualObjects.ActorService** projeto. Além disso, a versão da aplicação é atualizada para 2.0.0.0 do 1.0.0.0. O *ApplicationManifest.xml* deverá existir o seguinte fragmento:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, compilar o projeto selecionando apenas o **ActorService** projeto e, em seguida, clicar e selecionando a **criar** opção no Visual Studio. Se selecionar **reconstruir todas**, deve atualizar as versões para todos os projetos, uma vez que o código seria foram alterados. Em seguida, vamos do pacote de aplicação atualizada ao clicar no ***VisualObjectsApplication***, selecionando o Menu de recursos de infraestrutura de serviço e escolher **pacote**. Esta ação cria um pacote de aplicações que pode ser implementado.  A aplicação atualizada está pronta para ser implementada.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3: Atualização parâmetros e opte por utilizar políticas de estado de funcionamento
Familiarize-se com o [parâmetros de atualização da aplicação](service-fabric-application-upgrade-parameters.md) e [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critério de estado de funcionamento aplicada. Nestas instruções, o critério de avaliação de estado de funcionamento do serviço é predefinido (e recomendado) valores, o que significa que todas as instâncias e serviços devem ser *bom* após a atualização.  

No entanto, vamos aumentar o *HealthCheckStableDuration* a 60 segundos (para que os serviços estão em bom Estados de, pelo menos, 20 segundos antes de continua a atualização para o domínio de atualização seguinte).  Vamos também configurar a *UpgradeDomainTimeout* ser 1200 segundos e o *UpgradeTimeout* ser 3000 segundos.

Por fim, vamos também configurar a *UpgradeFailureAction* a reversão. Esta opção necessita de Service Fabric reverter a aplicação para a versão anterior se encontrar problemas durante a atualização. Assim, quando iniciar a atualização (no passo 4), estão especificados os seguintes parâmetros:

FailureAction = reversão

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar a aplicação para atualização
A aplicação está agora incorporado e pronto a ser atualizado. Se abrir uma janela do PowerShell como um administrador e escreva [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), este deve informá-lo de que é o tipo de aplicação 1.0.0.0 do **VisualObjects** foi implementado.  

O pacote de aplicação é armazenado no seguinte caminho relativo onde descomprimidos que o SDK de Service Fabric - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Deve encontrar uma pasta de "Pacote" nesse diretório, onde o pacote de aplicação está armazenado. Verifique os carimbos para se certificar de que está a compilação mais recente (poderá ter de modificar os caminhos adequadamente bem).

Agora vamos copiar o pacote de aplicação atualizado para o arquivo de imagens de recursos de infraestrutura serviço (onde os pacotes de aplicações são armazenados por Service Fabric). O parâmetro *ApplicationPackagePathInImageStore* informa o Service Fabric, onde pode encontrar o pacote de aplicações. Iremos colocados a aplicação atualizada "VisualObjects\_V2" com o seguinte comando (poderá ter de modificar os caminhos novamente adequadamente).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

O passo seguinte consiste em registar esta aplicação com o Service Fabric, o que pode ser efetuada utilizando o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não for bem-sucedida, é provável que precisa de uma reconstrução de todos os serviços. Tal como mencionado no passo 2, poderá ter de atualizar, bem como a sua versão do serviço Web.

Recomenda-se que remova o pacote de aplicações após a aplicação for registada com êxito.  A eliminação de pacotes de aplicação da loja de imagem liberta recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho da aplicação.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização da aplicação
Agora, iremos está tudo pronto para iniciar a atualização da aplicação utilizando o [início ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome da aplicação é o mesmo, tal como foi descrito no *ApplicationManifest.xml* ficheiro. O Service Fabric utiliza este nome para identificar que a aplicação está a obter atualizada. Se definir os tempos limite ser demasiado curto, poderá encontrar uma mensagem de falha que indica o problema. Consulte a secção de resolução de problemas, ou aumentar os tempos limite.

Agora, como atualização continua a aplicação, pode monitorizar com o Service Fabric Explorer, ou utilizando o [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) comando do PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Dentro de alguns minutos, o estado de que recebeu utilizando o comando PowerShell anterior, devem especificar que todos os domínios de atualização foram atualizados (concluir). E deverá considerar de que os objetos na janela do browser visual foram iniciados rotação dos!

Pode experimentar atualizar da versão 2 para a versão 3 ou a partir da versão 2 para a versão 1 como um exercício. Mover da versão 2, a versão 1 também é considerado uma atualização. Reproduzir com tempos limite e políticas de estado de funcionamento para tornar-se familiarizado com os mesmos. Quando estiver a implementar um cluster do Azure, os parâmetros tem de estar definidas corretamente. É boa definir os tempos limite moderadamente.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Faça as atualizações de aplicações compatíveis aprender a utilizar [serialização dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [avançadas tópicos](service-fabric-application-upgrade-advanced.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [as atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).

