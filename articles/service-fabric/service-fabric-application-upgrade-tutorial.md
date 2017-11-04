---
title: "Tutorial de atualização de aplicação de Service Fabric | Microsoft Docs"
description: "Este artigo explica a experiência de implementação de uma aplicação de Service Fabric, alterar o código e disponibilizando uma atualização através do Visual Studio."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 940440688ec770a4aeb932b574bd6be173f494d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de atualização de aplicação de Service Fabric com o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric simplifica o processo de atualização de aplicações em nuvem, garantindo que apenas os serviços alterados sejam atualizados e que o estado de funcionamento da aplicação é monitorizado em todo o processo de atualização. É também automaticamente reverte a aplicação para a versão anterior depois de identificar problemas. As atualizações de aplicações de Service Fabric são *período de indisponibilidade de Zero*, dado que a aplicação possa ser atualizada sem período de indisponibilidade. Este tutorial abrange como concluir uma atualização sem interrupção do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passo 1: Criar e publicar o exemplo de objetos Visual
Em primeiro lugar, transfira o [objetos Visual](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplicação a partir do GitHub. Em seguida, criar e publicar a aplicação ao clicar no projeto de aplicação, **VisualObjects**e selecionar o **publicar** comando no item de menu de Service Fabric.

![Menu de contexto para uma aplicação de Service Fabric][image1]

Selecionar **publicar** aparece um pop-up, e pode definir o **destino perfil** para **PublishProfiles\Local.xml**. A janela deve o seguinte aspeto antes de clicar em **publicar**.

![Publicar uma aplicação de Service Fabric][image2]

Agora, pode clicar em **publicar** na caixa de diálogo. Pode utilizar [Service Fabric Explorer para ver o cluster e a aplicação](service-fabric-visualizing-your-cluster.md). A aplicação de objetos Visual tem um serviço web que pode aceder à escrevendo [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) na barra de endereço do seu browser.  Deverá ver 10 objetos visual flutuante mover à volta, no ecrã.

**Nota:** se implementar `Cloud.xml` perfil (Azure Service Fabric), a aplicação, em seguida, deve estar disponível em **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Certifique-se de que tem `8081/TCP` configurado no balanceador de carga (localizar o Balanceador de carga no mesmo grupo de recursos que a instância do Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar o exemplo de objetos Visual
Poderá reparar que com a versão que tenha sido implementada no passo 1, os objetos visual não rodar. Vamos atualizar esta aplicação para um onde os objetos visual rodar também.

Selecione o projeto de VisualObjects.ActorService na solução de VisualObjects e abra o **VisualObjectActor.cs** ficheiro. Esse ficheiro, vá para o método `MoveObject`, comente `visualObject.Move(false)`e anule os comentários `visualObject.Move(true)`. Esta alteração de código roda os objetos depois do serviço está atualizado.  **Agora pode compilar a solução (não a reconstrução)**, que cria os projetos de modificação. Se selecionar *reconstruir todas*, é necessário atualizar as versões para todos os projetos.

É também necessário versão nossa aplicação. Para efetuar as alterações de versão, depois, clique com botão direito no **VisualObjects** projeto, pode utilizar o Visual Studio **Editar versões manifesto** opção. A seleção desta opção aparece a caixa de diálogo para versões de edição da seguinte forma:

![Caixa de diálogo de controlo de versões][image3]

Atualize as versões para os projetos modificados e os respetivos pacotes de código, juntamente com a aplicação para versão 2.0.0. Depois das alterações serem efetuadas, o manifesto deverá ter um aspeto semelhante ao seguinte (partes negrito mostram as alterações):

![Versões de atualização][image4]

As ferramentas do Visual Studio, podem fazer rollups automática das versões após selecionar **atualizar automaticamente as aplicações e versões de service**. Se utilizar [SemVer](http://www.semver.org), tem de atualizar o código de e/ou configuração pacote versão autónoma se essa opção está selecionada.

Guardar as alterações e verificar agora o **atualizar a aplicação** caixa.

## <a name="step-3--upgrade-your-application"></a>Passo 3: Atualizar a sua aplicação
Familiarize-se com o [parâmetros de atualização da aplicação](service-fabric-application-upgrade-parameters.md) e [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critério de estado de funcionamento que pode ser aplicado. Nestas instruções, o critério de avaliação de estado de funcionamento do serviço está definido para a predefinição (modo de não monitorizado). Pode configurar estas definições, selecionando **configurar definições de atualização** e, em seguida, modificar os parâmetros conforme pretendido.

Agora vamos está tudo pronto para iniciar a atualização da aplicação, selecionando **publicar**. Esta opção atualiza a aplicação para a versão 2.0.0, na qual rodar os objetos. Service Fabric atualiza um domínio de atualização num momento (alguns objetos estão atualizados em primeiro lugar, seguidos por outras pessoas) e o serviço permanece acessível durante a atualização. Acesso ao serviço pode ser verificado através do seu cliente (browser).  

Agora, como atualização continua a aplicação, pode monitorizá-lo com o Service Fabric Explorer, utilizando o **atualizações em curso** separador em aplicações.

Dentro de alguns minutos, todos os domínios de atualização devem ser atualizados (concluir) e a janela de saída do Visual Studio também deve indicar que a conclusão da atualização. E deverá considerar que *todos os* os objetos visual na janela do browser estão agora rodar!

Experimente alterar as versões, poderá ser útil e mudança de versão 2.0.0 para versão 3.0.0 como um exercício, ou mesmo a partir da versão 2.0.0 voltar à versão 1.0.0. Reproduzir com tempos limite e políticas de estado de funcionamento para tornar-se familiarizado com os mesmos. Quando implementar um cluster do Azure, por oposição a um cluster local, os parâmetros utilizados poderão ter de ser diferentes. Recomendamos que defina os tempos limite moderadamente.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação através do PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

Controlar a forma como a aplicação é atualizada utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Faça as atualizações de aplicações compatíveis aprender a utilizar [serialização dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [avançadas tópicos](service-fabric-application-upgrade-advanced.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [as atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
