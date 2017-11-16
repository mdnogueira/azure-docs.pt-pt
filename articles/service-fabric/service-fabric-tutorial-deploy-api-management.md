---
title: "Integrar os recursos de infraestrutura de serviço do Azure com a gestão de API | Microsoft Docs"
description: "Saiba como começar rapidamente API Management do Azure e o Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: ryanwi
ms.openlocfilehash: 97bcf312621ec0fed28e26179d4c4aa101a8a92d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Implementar a gestão de API com o Service Fabric
Este tutorial é parte três de uma série.  Implementar a gestão de API do Azure com o Service Fabric é um cenário avançado.  Gestão de API é útil quando tem de publicar APIs com um vasto conjunto de regras de encaminhamento para os serviços de recursos de infraestrutura do serviço de back-end. Aplicações em nuvem, normalmente, precisam de um gateway de front-end para fornecer um único ponto de entrada para os utilizadores, dispositivos ou outras aplicações. No Service Fabric, um gateway pode ser qualquer serviço sem estado concebido para a entrada de tráfego, como uma aplicação APP.NET núcleos, os Event Hubs, IoT Hub ou API Management do Azure. 

Este tutorial mostra como configurar [API Management do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para encaminhar o tráfego para um serviço de back-end no Service Fabric.  Quando tiver terminado, ter implementado a API Management para uma VNET, configurada uma operação de API para enviar o tráfego para serviços sem monitorização de estado de back-end. Para saber mais sobre os cenários de API Management do Azure com o Service Fabric, consulte o [descrição geral](service-fabric-api-management-overview.md) artigo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar a gestão de API
> * Configurar a gestão de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API para um produto

Este tutorial série, a saber como:
> [!div class="checklist"]
> * Criar segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure através de um modelo
> * [Um cluster de entrada ou saída de escala](/service-fabric-tutorial-scale-cluster.md)
> * Implementar a gestão de API com o Service Fabric

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Criar segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure
- Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instalar [Visual Studio 2017](http://www.visualstudio.com) e **programação do Azure**, **desenvolvimento ASP.NET e web**, e **desenvolvimento de plataformas cruzadas .NET Core**cargas de trabalho.  Em seguida, configure um [ambiente de desenvolvimento de .NET](service-fabric-get-started.md).
- Se implementar um cluster do Linux, configurar um ambiente de desenvolvimento Java no [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instalar o [Service Fabric CLI](service-fabric-cli.md). 

## <a name="network-topology"></a>Topologia de rede
Agora que tem um segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure, implementar a gestão de API para a rede virtual (VNET) na sub-rede e NSG designado para a gestão de API. Para este tutorial, o modelo de Gestor de recursos de gestão de API está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no anterior [tutorial de cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [tutorial do Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md). Este tutorial implementa a seguinte topologia para o Azure no qual API Management e de recursos de infraestrutura de serviço estão em sub-redes da mesma rede Virtual:

 ![Legenda de imagem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicie sessão no Azure e selecionar a sua subscrição
Inicie sessão na sua conta do Azure selecione a sua subscrição antes de executar os comandos do Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementar um serviço de back-end do Service Fabric

Antes de configurar a gestão de API para encaminhar o tráfego para um serviço de back-end do Service Fabric, primeiro precisa de um serviço em execução para aceitar pedidos.  Se criou anteriormente um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), implementar um serviço de .NET Service Fabric.  Se criou anteriormente um [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md), implementar um serviço de recursos de infraestrutura de serviço de Java.

### <a name="deploy-a-net-service-fabric-service"></a>Implementar um serviço de recursos de infraestrutura de serviço de .NET

Para este tutorial, crie um serviço básico de sem monitorização de estado ASP.NET Core fiável utilizando o modelo de projeto Web API predefinido. Esta ação cria um ponto final de HTTP para o seu serviço, que expõe através da API Management do Azure.

Inicie o Visual Studio como administrador e criar um serviço de núcleo de ASP.NET:

 1. No Visual Studio, selecione o ficheiro -> novo projeto.
 2. Selecione o modelo de aplicação de recursos de infraestrutura de serviço em nuvem e nome **"ApiApplication"**.
 3. Selecione o modelo de serviço de núcleo de ASP.NET e nomeie o projeto **"WebApiService"**.
 4. Selecione o modelo de projeto Web API ASP.NET Core 1.1.
 5. Uma vez criado o projeto, abra `PackageRoot\ServiceManifest.xml` e remova o `Port` atributo da configuração do recurso de ponto final:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    A porta a remover permite que os recursos de infraestrutura de serviço especificar uma porta dinamicamente a partir do intervalo de portas de aplicação, aberto através do grupo de segurança no modelo de Gestor de recursos de cluster, a permitir o tráfego flua ao mesmo de API Management.
 
 6. Prima F5 no Visual Studio para verificar a API web está disponível localmente. 

    Abrir Explorador do Service Fabric e Desagregue para uma instância específica do serviço de núcleo de ASP.NET para ver o endereço base do serviço está a escutar. Adicionar `/api/values` para a base de endereços e abra-o num browser, que invoca o método Get ValuesController no modelo de Web API. Devolve a resposta de predefinido que é fornecida pelo modelo, uma matriz JSON que contém duas cadeias de:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto final que expõe através da gestão de API no Azure.

 7. Por fim, implemente a aplicação em cluster no Azure. No Visual Studio, clique com o botão direito no projeto de aplicação e selecione **publicar**. Forneça o ponto final de cluster (por exemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implementar a aplicação ao cluster do Service Fabric no Azure.

Um serviço sem monitorização de estado do ASP.NET Core com o nome `fabric:/ApiApplication/WebApiService` devem agora estar a executar no seu cluster do Service Fabric no Azure.

### <a name="create-a-java-service-fabric-service"></a>Criar um serviço de Java Service Fabric
Para este tutorial implementar um servidor de web básica, que echoes mensagens fazer uma cópia ao utilizador. A aplicação de exemplo do servidor de eco contém um ponto final de HTTP para o seu serviço, que expõe através da API Management do Azure.

1. Os exemplos de introdução ao obter Java de clone.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Editar *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Atualize o ponto final para que o serviço de escuta na porta 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Guardar *ServiceManifest.xml*, em seguida, criar a aplicação de EchoServer1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Implemente a aplicação para o cluster.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   Um serviço sem monitorização de estado de Java com o nome `fabric:/EchoServerApplication/EchoServerService` devem agora estar a executar no seu cluster do Service Fabric no Azure.

5. Abra um browser e escreva no http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage, deverá ver "[versão 1.0] Olá, mundo!!!" apresentado.

## <a name="download-and-understand-the-resource-manager-template"></a>Transferir e compreender o modelo do Resource Manager
Transfira e guarde o Gestor de recursos seguinte ficheiro de modelo e os parâmetros:
 
- [APIM.JSON][apim-arm]
- [APIM.Parameters.JSON][apim-parameters-arm]

As secções seguintes descrevem os recursos que está a ser definidos pelo *apim.json* modelo. Para obter mais informações, siga as ligações a documentação de referência de modelo dentro de cada secção. Configuráveis parâmetros definidos no *apim.parameters.json* ficheiro de parâmetros que estão definidos neste artigo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) descreve a instância do serviço de gestão de API: nome, SKU ou camada, localização do grupo de recursos, informações do publicador e rede virtual.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura a segurança de API Management. API Management tem de autenticar com o cluster de Service Fabric para a deteção de serviço a utilizar um certificado de cliente que tenha acesso ao cluster. Este tutorial utiliza o mesmo certificado que especificou anteriormente ao criar o [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, por predefinição pode ser utilizado para aceder ao seu cluster. 

Este tutorial utiliza o mesmo certificado para autenticação de cliente e de segurança do nó para o nó de cluster. Pode utilizar um certificado de cliente separado, se tiver uma configurado para aceder ao seu cluster do Service Fabric. Forneça o **nome**, **palavra-passe**, e **dados** (cadeia com codificação base 64) do ficheiro de chave privada (. pfx) do certificado de cluster que especificou ao criar o Cluster do Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) descreve o serviço de back-end que o tráfego seja reencaminhado para. 

Para o back-ends de Service Fabric, o cluster do Service Fabric é o back-end em vez de um serviço específico do Service Fabric. Isto permite que uma única política encaminhar para mais do que um serviço no cluster. O **url** campo aqui é um nome de serviço completamente qualificado de um serviço no seu cluster de que todos os pedidos são encaminhados para por predefinição se não for especificado nenhum nome de serviço numa política de back-end. Pode utilizar um nome de serviço falsificados, tal como "fabric: / falsificados/serviço" se não pretender tem um serviço de contingência. **resourceId** Especifica o ponto final de gestão de cluster.  **clientCertificateThumbprint** e **serverCertificateThumbprints** identificar certificados utilizados para autenticar com o cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) cria um produto. Na API Management do Azure, um produto contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Depois de um produto é publicado, os programadores podem subscrever o produto e começar a utilizar as APIs do produto. 

Introduza um descritivo **displayName** e **Descrição** para o produto. Para este tutorial, é necessária uma subscrição mas não é aprovação de subscrição por um administrador.  Este produto **estado** "publicada" e está visível para os subscritores. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) cria uma API. Uma API na API Management representa um conjunto de operações que podem ser invocados por aplicações cliente. Depois das operações são adicionadas, a API é adicionada um produto e pode ser publicada. Depois de uma API for publicada, pode ser subscrito e utilizado pelos programadores.

- **displayName** pode ser qualquer nome para a API. Para este tutorial, utilize "Recursos de infraestrutura do serviço de aplicações".
- **nome** fornece um nome único e descritivo para a API, tais como "aplicação de recursos de infraestrutura de serviço". Será apresentado no portais do programador e o publicador. 
- **serviceUrl** referencia o serviço HTTP implementar a API. API de gestão reencaminha os pedidos para este endereço. Para o back-ends de Service Fabric, este valor de URL não é utilizado. Pode colocar qualquer valor aqui. Para este tutorial, por exemplo "http://servicefabric". 
- **caminho** é anexado ao URL de base para o serviço de gestão de API. A base de URL é comum para todos os APIs alojadas por uma instância de serviço de API Management. Gestão de API distingue APIs pelo respetivo sufixo e, por conseguinte, o sufixo tem de ser exclusivo para cada API para um determinado publicador. 
- **protocolos** determinar quais protocolos podem ser utilizados para aceder à API do. Para este tutorial, lista **http** e **https**.
- **caminho** é um sufixo para a API. Para este tutorial, utilize "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) antes de uma API na API Management podem ser utilizado, operações tem de ser adicionadas à API.  Clientes externos utilizam uma operação para comunicar com o serviço sem monitorização de estado do ASP.NET Core em execução no cluster de Service Fabric.

Para adicionar uma operação de API de front-end, preencha os valores:

- **displayName** e **Descrição** descrevem a operação. Para este tutorial, utilize "Valores".
- **método** Especifica o verbo HTTP.  Para este tutorial, especifique **obter**.
- **urlTemplate** é anexado ao URL de base da API e identifica uma única operação de HTTP.  Para este tutorial, utilize `/api/values` se tiver adicionado o serviço de back-end do .NET ou `getMessage` se tiver adicionado o serviço de back-end de Java.  Por predefinição, o caminho do URL especificado aqui é o caminho de URL é enviado para o serviço de recursos de infraestrutura do serviço de back-end. Se utilizar o mesmo caminho de URL aqui que utiliza o seu serviço, tal como "/ api/valores", em seguida, a operação funciona sem modificações. Também pode especificar um caminho de URL aqui que é diferente do caminho de URL utilizado pelo seu back-end serviço do Service Fabric, caso em que também tem de especificar um caminho de reescrever na sua política de operação mais tarde.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) cria uma política de back-end, vincula tudo em conjunto. Este é onde configura o serviço de recursos de infraestrutura do serviço de back-end para os quais os pedidos são encaminhados. Pode aplicar esta política para todas as operações de API.  Para obter mais informações, consulte [descrição geral das políticas](/azure/api-management/api-management-howto-policies). 

O [configuração de back-end para o Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controlos de encaminhamento do pedido: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço no Service Fabric, ou codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerados no pedido de HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição ao gerar uma chave de partição utilizando qualquer esquema de partições de Service Fabric.
 - Seleção de réplica para serviços com monitorização de estado.
 - Condições de tentativas de resolução que permitem-lhe especificar as condições para resolver a localização de serviço e enviar um pedido.

**policyContent** é Json escape conteúdo XML da política.  Para este tutorial, crie uma política de back-end para pedidos de rota diretamente para o serviço de sem monitorização de estado .NET ou Java implementada anteriormente. Adicionar um `set-backend-service` política em políticas de entrada.  Substitua o "nome do serviço" `fabric:/ApiApplication/WebApiService` se implementou o serviço de back-end .NET, anteriormente ou `fabric:/EchoServerApplication/EchoServerService` se implementou o serviço de Java.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Para um conjunto completo de atributos de política de back-end do Service Fabric, consulte o [documentação de back-end de API Management](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Configurar parâmetros e implementar a gestão de API
Preencha os seguintes parâmetros vazios no *apim.parameters.json* para a sua implementação. 

|Parâmetro|Valor|
|---|---|
|apimInstanceName|SF apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Programador|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|CertificatePassword|% de q6D7nN6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;cadeia com codificação base 64&gt;|
|url_path|/ api/valores|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.Azure.com:19080|
|inbound_policy|&lt;Cadeia XML&gt;|

*certificatePassword* e *serviceFabricCertificateThumbprint* tem de coincidir com o certificado de cluster utilizado para configurar o cluster.  

*serviceFabricCertificate* é o certificado como uma cadeia com codificação base 64, que pode ser gerada utilizando o seguinte script:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

No *inbound_policy*, substitua "nome do serviço" `fabric:/ApiApplication/WebApiService` se implementou o serviço de back-end .NET, anteriormente ou `fabric:/EchoServerApplication/EchoServerService` se implementou o serviço de Java.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Utilize o seguinte script para implementar os ficheiros de parâmetros e modelo do Resource Manager para a gestão de API:

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Testá-lo

Agora pode tentar enviar um pedido para o serviço de back-end no Service Fabric através da API de gestão diretamente a partir de [portal do Azure](https://portal.azure.com).

 1. No serviço de API Management, selecione **API**.
 2. No **do App Service Fabric** API que criou nos passos anteriores, selecionados o **teste** separador e, em seguida, o **valores** operação.
 3. Clique em de **enviar** botão para enviar um pedido de teste para o serviço de back-end.  Deverá ver uma resposta HTTP semelhante a:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é constituído por outros recursos do Azure, além do próprio recurso do cluster. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Inicie sessão no Azure e selecione o ID de subscrição com o qual pretende remover o cluster.  Pode encontrar o ID de subscrição ao iniciar sessão a [portal do Azure](http://portal.azure.com). Eliminar o grupo de recursos e todos os recursos de cluster utilizando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="conclusion"></a>Conclusão
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar a gestão de API
> * Configurar a gestão de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API para um produto

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
