---
title: "Publicar uma aplicação para um cluster remoto com o Visual Studio | Microsoft Docs"
description: "Saiba como publicar uma aplicação para um cluster de recursos de infraestrutura de serviço remoto utilizando o Visual Studio."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: ryanwi
ms.openlocfilehash: 60be4f285d658c7922f6a3ff620ee3b0aaaccf72
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="deploy-and-remove-applications-using-visual-studio"></a>Implementar e remover aplicações utilizando o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

A extensão do Azure Service Fabric para Visual Studio fornece uma forma fácil, repetíveis e de script para publicar uma aplicação para um cluster do Service Fabric.

## <a name="the-artifacts-required-for-publishing"></a>Os artefactos necessários para publicação
### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 implementar
Este é um script do PowerShell que utiliza um caminho de perfil de publicação como um parâmetro para publicar aplicações de Service Fabric. Uma vez que este script faz parte da sua aplicação, são bem-vindo ao modificá-la conforme for necessário para a sua aplicação.

### <a name="publish-profiles"></a>Perfis de publicação
Uma pasta no projeto de aplicação de Service Fabric denominada **PublishProfiles** contém os ficheiros XML, que armazenam informações essenciais para publicar uma aplicação, tais como:

* Parâmetros de ligação de cluster do Service Fabric
* Caminho para um ficheiro de parâmetro de aplicação
* Definições de atualização

Por predefinição, a aplicação irá incluir três perfis de publicação: Local.1Node.xml, Local.5Node.xml e Cloud.xml. Pode adicionar mais perfis, copiar e colar um dos ficheiros predefinido.

### <a name="application-parameter-files"></a>Ficheiros de parâmetro de aplicação
Uma pasta no projeto de aplicação de Service Fabric denominada **ApplicationParameters** contém os ficheiros XML para os valores de parâmetros de manifesto de aplicação de utilizador especificado. Ficheiros de manifesto de aplicação podem ser parametrizados para que possa utilizar valores diferentes para as definições de implementação. Para saber mais sobre parameterizing a sua aplicação, consulte o artigo [gerir vários ambientes no Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Para os serviços de atores, deve criar o projeto primeiro antes de tentar editar o ficheiro num editor ou através da caixa de diálogo Publicar. Isto acontece porque parte dos ficheiros de manifesto será gerado durante a compilação.

## <a name="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box"></a>Para publicar uma aplicação utilizando a caixa de diálogo publicar aplicações de recursos de infraestrutura de serviço
Os passos seguintes demonstram como publicar uma aplicação utilizando o **publicar aplicação do serviço Fabric** fornecida as ferramentas de recursos de infraestrutura de serviço do Studio Visual de caixa de diálogo.

1. No menu de atalho do projeto de aplicação de recursos de infraestrutura de serviço, escolha **publicar...** Para ver o **publicar aplicação do serviço Fabric** caixa de diálogo.
   
    ![O * * caixa de diálogo Publicar Service Fabric aplicação * *][0]
   
    O ficheiro selecionado no **destino perfil** caixa de lista pendente é onde todas as definições, exceto **manifesto versões**, são guardadas. Pode reutilizar um perfil existente ou crie um novo escolhendo **< gerir perfis de... >** no **destino perfil** caixa de lista pendente. Ao escolher um perfil de publicação, o respetivo conteúdo são apresentados nos campos correspondentes da caixa de diálogo. Para guardar as alterações em qualquer altura, escolha o **Guardar perfil** ligação.    
2. No **ponto final de ligação** secção, especifique um local ou remoto Service Fabric da publicação ponto final de cluster. Para adicionar ou alterar o ponto final da ligação, clique em de **ponto final de ligação** na lista pendente. A lista mostra o cluster do Service Fabric disponível pontos finais da ligação para o qual pode publicar com base nas suas subscrições do Azure. Tenha em atenção que se tiver não já sessão iniciada Visual Studio, será solicitado a fazê-lo.
   
    Utilize a caixa de diálogo de seleção de cluster para escolher o conjunto de subscrições disponíveis e clusters.
   
    ![O * * caixa de diálogo Selecionar serviço Fabric Cluster * *][1]
   
   > [!NOTE]
   > Se gostaria de publicar para um ponto final arbitrário (por exemplo, um cluster de terceiros), consulte o **publicação para um ponto final de cluster arbitrários** secção abaixo.
   > 
   > 
   
    Depois de escolher um ponto final, o Visual Studio valida a ligação ao cluster do Service Fabric selecionado. Se o cluster não for seguro, Visual Studio podem ligar à mesma imediatamente. No entanto, se o cluster é seguro, terá de instalar um certificado no computador local antes de continuar. Consulte [como configurar ligações seguras](service-fabric-visualstudio-configure-secure-connections.md) para obter mais informações. Quando tiver terminado, escolha o **OK** botão. O cluster selecionado é apresentado no **publicar aplicação do serviço Fabric** caixa de diálogo.
3. No **ficheiro de parâmetros de aplicação** na lista pendente caixa, navegue para um ficheiro de parâmetros de aplicação. Um ficheiro de parâmetros de aplicação contém valores de utilizador especificado para os parâmetros no ficheiro de manifesto da aplicação. Para adicionar ou alterar um parâmetro, escolha o **editar** botão. Introduza ou altere o valor do parâmetro no **parâmetros** grelha. Quando tiver terminado, escolha o **guardar** botão.
   
    ![O * * caixa de diálogo Editar parâmetros * *][2]
4. Utilize o **atualizar a aplicação** caixa de verificação para especificar se publicar esta ação é uma atualização. Atualização publicar as ações diferem das normal publicar as ações. Consulte [atualizar de aplicação do serviço de recursos de infraestrutura](service-fabric-application-upgrade.md) para obter uma lista das diferenças. Para configurar as definições de atualização, escolha o **configurar definições de atualização** ligação. O editor de parâmetro de atualização é apresentado. Consulte [configurar a atualização de uma aplicação de Service Fabric](service-fabric-visualstudio-configure-upgrade.md) para saber mais acerca dos parâmetros de atualização.
5. Escolha o **manifesto versões...** botão para ver o **Editar versões** caixa de diálogo. Tem de atualizar a aplicação e das versões do serviço para uma atualização seja realizada. Consulte [tutorial de atualização de aplicação de Service Fabric](service-fabric-application-upgrade-tutorial.md) para saber como aplicações e versões de manifesto do serviço afetam um processo de atualização.
   
    ![O * * caixa de diálogo Editar versões * *][3]
   
    Se as versões do serviço de aplicações e utilizam o controlo de versões semântico como 1.0.0 ou valores de numérico no formato de 1.0.0.0, selecione o **atualizar automaticamente as aplicações e versões de service** opção. Quando escolher esta opção, o serviço e números de versão da aplicação são atualizados automaticamente sempre que um código de configuração, dados ou versão do pacote são atualizados. Se preferir editar manualmente as versões, desmarque a caixa de verificação para desativar esta funcionalidade.
   
   > [!NOTE]
   > Para todas as entradas de pacote apareça para um projeto de ator, crie primeiro o projeto para gerar as entradas de ficheiros Service Manifest.
   > 
   > 
6. Quando tiver terminado especificar todas as definições necessárias, escolha o **publicar** botão para publicar a aplicação para o cluster do Service Fabric selecionado. As definições que especificou são aplicadas para o processo de publicação.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicar um ponto final de cluster arbitrários (incluindo clusters de terceiros)
O Visual Studio publicação experiência está otimizado para a publicação de clusters remotos associado uma das suas subscrições do Azure. No entanto, é possível publicar pontos finais arbitrários (por exemplo, clusters de terceiros de Service Fabric) editando o XML do perfil de publicação diretamente. Como descrito acima, três perfis de publicação são fornecidos por predefinição –**Local.1Node.xml**, **Local.5Node.xml**, e **Cloud.xml**– mas é bem-vindo ao criar perfis adicionais para os diferentes ambientes. Por exemplo, pode querer criar um perfil para a publicação de clusters de terceiros, talvez com o nome **Party.xml**.

Se estiver a ligar a um cluster não segura, tudo o que necessita é o ponto de final para a ligação de cluster, tal como `partycluster1.eastus.cloudapp.azure.com:19000`. Nesse caso, o ponto final de ligação no perfil de publicação seria algo semelhante ao seguinte:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se estiver a ligar a um cluster seguro, também terá de fornecer os detalhes do certificado do arquivo local para ser utilizado para autenticação de cliente. Para obter mais detalhes, consulte [configurar ligações seguras para um cluster do Service Fabric](service-fabric-visualstudio-configure-secure-connections.md).

  Depois do perfil de publicação configurada, pode referenciá-lo na caixa de diálogo Publicar conforme mostrado abaixo.

  ![Novo perfil de publicação na caixa de diálogo Publicar][4]

  Tenha em atenção que neste caso, o novo perfil de publicação aponta para um dos ficheiros de parâmetro de aplicação predefinido. Esta ação é adequada se de que pretende publicar a mesma configuração de aplicação para um número dos ambientes. Por outro lado, em casos onde pretende que tenham configurações diferentes para cada ambiente que pretende publicar, este seria fazer sentido para criar um ficheiro de parâmetros de aplicação correspondente.

## <a name="next-steps"></a>Passos seguintes
Para saber como automatizar o processo de publicação num ambiente de integração contínua, consulte [configurar a integração contínua de Service Fabric](service-fabric-set-up-continuous-integration.md).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
