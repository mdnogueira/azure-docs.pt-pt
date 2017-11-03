---
title: "Azure Service Fabric com início rápido da gestão de API | Microsoft Docs"
description: "Este guia mostra como começar rapidamente API Management do Azure e o Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: 2969834713fc7c2f1a2e281a6c988158d803dc45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-quick-start"></a>Recursos de infraestrutura de serviço com início rápido da gestão de API do Azure

Este guia mostra como configurar a gestão de API do Azure com o Service Fabric e configurar a sua primeira operação de API para enviar o tráfego para serviços de back-end no Service Fabric. Para saber mais sobre os cenários de API Management do Azure com o Service Fabric, consulte o [descrição geral](service-fabric-api-management-overview.md) artigo. 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Implementar a gestão de API e recursos de infraestrutura de serviço no Azure

O primeiro passo é implementar API Management e um cluster do Service Fabric do Azure numa rede Virtual partilhado. Isto permite a gestão de API comunicar diretamente com o Service Fabric, pelo que pode efetuar a deteção do serviço, resolução de partição de serviço e reencaminhar tráfego diretamente a qualquer serviço de back-end no Service Fabric.

### <a name="topology"></a>Topologia

Este guia implementa a seguinte topologia para o Azure no qual API Management e de recursos de infraestrutura de serviço estão em sub-redes da mesma rede Virtual:

 ![Legenda de imagem][sf-apim-topology-overview]

Para começar a trabalhar rapidamente, os modelos do Resource Manager são fornecidos para cada passo de implementação:

 - Topologia de rede:
    - [Network.JSON][network-arm]
    - [Network.Parameters.JSON][network-parameters-arm]
 - Cluster do Service Fabric:
    - [cluster.JSON][cluster-arm]
    - [cluster.Parameters.JSON][cluster-parameters-arm]
 - Gestão de API:
    - [APIM.JSON][apim-arm]
    - [APIM.Parameters.JSON][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicie sessão no Azure e selecionar a sua subscrição

Este guia utiliza [Azure PowerShell][azure-powershell]. Quando inicia uma nova sessão do PowerShell, inicie sessão na sua conta do Azure e selecionar a sua subscrição antes de executar os comandos do Azure.
 
Inicie sessão sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione a sua subscrição:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um novo grupo de recursos para a sua implementação. Atribua um nome e uma localização.

```powershell
New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Implementar a topologia de rede

É o primeiro passo para configurar a topologia de rede à qual serão implementados API Management e o cluster do Service Fabric. O [network.json] [ network-arm] modelo do Resource Manager é configurado para criar uma rede Virtual (VNET) com duas sub-redes e dois grupos de segurança de rede (NSG). 

O [network.parameters.json] [ network-parameters-arm] ficheiro de parâmetros contém os nomes das sub-redes e NSGs API Management e o Service Fabric serão implementado. Para este guia, os valores de parâmetros não precisam de ser alteradas. A utilização de modelos de API Management e o Gestor de recursos de infraestrutura de serviço estes valores, pelo que o se forem modificados aqui, tem de modificá-las nos outros modelos de Gestor de recursos em conformidade. 

 1. Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:

    - [Network.JSON][network-arm]
    - [Network.Parameters.JSON][network-parameters-arm]

 2. Utilize o seguinte comando do PowerShell para implementar os ficheiros de parâmetros e modelo do Resource Manager para a configuração de rede:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Implementar o cluster do Service Fabric

Assim que os recursos de rede tem concluído a implementação, o passo seguinte é implementar um cluster do Service Fabric para a VNET na sub-rede e NSG designado para o cluster do Service Fabric. Para este tutorial, o modelo de serviço Gestor de recursos de infraestrutura está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no passo anterior. 

O modelo de Gestor de recursos de cluster do Service Fabric está configurado para criar um cluster seguro com a segurança do certificado. O certificado é utilizado para proteger a comunicação de nó de nó para o cluster e para gerir o acesso de utilizador ao cluster do Service Fabric. API Management utiliza este certificado para o serviço Service Fabric Naming para a deteção do serviço de acesso.

Este passo necessita de ter um certificado no Cofre de chaves de segurança do cluster. Para obter mais informações sobre como configurar um cluster com o Cofre de chaves seguro, consulte [este guia de criação de um cluster no Azure utilizando o Gestor de recursos](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> Pode adicionar a autenticação do Azure Active Directory para além do certificado utilizado para acesso de cluster. Azure Active Directory é a forma recomendada para gerir o acesso de utilizador ao cluster do Service Fabric, mas não é necessário para concluir este tutorial. É necessário um certificado qualquer forma de segurança do nó para o nó de cluster e para a autenticação de API Management do Azure, que atualmente não suporta a autenticação com o Azure Active Directory para um back-end do Service Fabric.

 1. Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
 
    - [cluster.JSON][cluster-arm]
    - [cluster.Parameters.JSON][cluster-parameters-arm]

 2. Preencha os parâmetros vazios o `cluster.parameters.json` ficheiro para a sua implementação, incluindo o [informações do Cofre de chaves](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) para o seu certificado de cluster.

 3. Utilize o seguinte comando do PowerShell para implementar os ficheiros de parâmetros e modelo do Resource Manager para criar o cluster do Service Fabric:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Implementar a gestão de API

Por fim, implementar a gestão de API para a VNET na sub-rede e NSG designado para a gestão de API. Não é necessário aguardar para a implementação de cluster do Service Fabric para concluir antes de implementar a gestão de API. 

Para este tutorial, o modelo de Gestor de recursos de gestão de API está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no passo anterior. 

 1. Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
 
    - [APIM.JSON][apim-arm]
    - [APIM.Parameters.JSON][apim-parameters-arm]

 2. Preencha os parâmetros vazios o `apim.parameters.json` para a sua implementação.

 3. Utilize o seguinte comando do PowerShell para implementar os ficheiros de parâmetros e modelo do Resource Manager para a gestão de API:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Configurar a gestão de API

Depois do cluster de gestão de API e do Service Fabric são implementados, pode configurar um back-end do Service Fabric na API Management. Isto permite-lhe criar uma política de serviço de back-end que envia o tráfego para o cluster do Service Fabric.

### <a name="api-management-security"></a>Segurança da gestão de API

Para configurar o back-end do Service Fabric, terá primeiro de configurar as definições de segurança de API Management. Para configurar as definições de segurança, aceda ao seu serviço de gestão de API no portal do Azure.

#### <a name="enable-the-api-management-rest-api"></a>Ativar a gestão de API REST API

A API de REST de gestão de API está atualmente a única forma de configurar um serviço de back-end. O primeiro passo é ativar a API de REST de gestão de API e proteja-a.

 1. No serviço de API Management, selecione **API de gestão - pré-visualização** em **segurança**.
 2. Verifique o **ativar API de REST de gestão de API** caixa de verificação.
 3. Anote o URL da API de gestão – este é o URL que utilizaremos mais tarde para configurar o back-end do Service Fabric
 4. Gerar um **Token de acesso ao** ao selecionar uma data de expiração e uma chave, em seguida, clique em de **gerar** botão para parte inferior da página.
 5. Copiar o **token de acesso** e guardá-lo - iremos utilizá-lo nos passos seguintes. Tenha em atenção de que isto é diferente da chave primária e chave secundária.

#### <a name="upload-a-service-fabric-client-certificate"></a>Carregar um certificado de cliente de Service Fabric

API Management tem de autenticar com o cluster de Service Fabric para a deteção de serviço a utilizar um certificado de cliente que tenha acesso ao cluster. Simplicidade, este tutorial utiliza o mesmo certificado especificado ao criar o cluster do Service Fabric, que, por predefinição, pode ser utilizado para aceder ao seu cluster.

 1. No serviço de API Management, selecione **certificados de cliente - pré-visualização** em **segurança**.
 2. Clique em de **+ adicionar** botão
 2. Selecione o ficheiro de chave privada (. pfx) do certificado de cluster que especificou ao criar o cluster do Service Fabric, atribua um nome e fornecer a palavra-passe da chave privada.

> [!NOTE]
> Este tutorial utiliza o mesmo certificado para autenticação de cliente e de segurança do nó para o nó de cluster. Pode utilizar um certificado de cliente separado, se tiver uma configurado para aceder ao seu cluster do Service Fabric.

### <a name="configure-the-backend"></a>Configurar o back-end

Agora que a configuração da segurança de API Management, pode configurar o back-end do Service Fabric. Para o back-ends de Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço específico do Service Fabric. Isto permite que uma única política encaminhar para mais do que um serviço no cluster.

Este passo necessita do token de acesso gerados anteriormente e o thumbprint do certificado de cluster que carregou para gestão de API no passo anterior.

> [!NOTE]
> Se utilizou um certificado de cliente separado no passo anterior para a gestão de API, terá do thumbprint para o certificado de cliente para além do thumbprint do certificado de cluster neste passo.

Envie o seguinte HTTP PUT de pedido para o URL de API de gestão de API que anotou anteriormente ao ativar a API de REST de gestão de API configurar o serviço de back-end do Service Fabric. Deverá ver uma `HTTP 201 Created` resposta quando o comando for bem sucedida. Para obter mais informações sobre cada campo, consulte a API Management [documentação de referência da API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

Comando HTTP e o URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Cabeçalhos de pedido:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Corpo do pedido:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

O **url** parâmetro aqui é um nome de serviço completamente qualificado de um serviço no seu cluster de que todos os pedidos são encaminhados para por predefinição se não for especificado nenhum nome de serviço numa política de back-end. Pode utilizar um nome de serviço falsificados, tal como "fabric: / falsificados/serviço" se não pretender tem um serviço de contingência. Tenha em atenção o **url** tem de estar no formato "fabric: / aplicação/serviço" mesmo se for um serviço de contingência falsificado.

Consulte a API Management [documentação de referência da API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) para obter mais detalhes sobre cada campo.

#### <a name="example"></a>Exemplo

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementar um serviço de back-end do Service Fabric

Agora que tem o Service Fabric configurado como um back-end para a API Management, pode criar políticas de back-end para as suas APIs que enviam tráfego para os serviços do Service Fabric. Mas, primeiro precisa de um serviço em execução no Service Fabric para aceitar pedidos.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Criar um serviço do Service Fabric com um ponto final de HTTP

Para este tutorial, vamos criar um serviço básico de sem monitorização de estado ASP.NET Core fiável utilizando o modelo de projeto Web API predefinido. Esta ação cria um ponto final de HTTP para o seu serviço, o que irá expor através da API Management do Azure:

```
/api/values
```

Comece por [como configurar o ambiente de desenvolvimento para o desenvolvimento de ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Depois de configurar o ambiente de desenvolvimento, inicie o Visual Studio como administrador e criar um serviço de núcleo de ASP.NET:

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

    Isto permite que o Service Fabric especificar uma porta dinamicamente a partir do intervalo de portas a aplicação, que foi abertas através do grupo de segurança no modelo de Gestor de recursos de cluster, a permitir o tráfego flua ao mesmo de API Management.
 
 6. Prima F5 no Visual Studio para verificar a API web está disponível localmente. 

    Abrir Explorador do Service Fabric e Desagregue para uma instância específica do serviço de núcleo de ASP.NET para ver o endereço base do serviço está a escutar. Adicionar `/api/values` para a base de endereços e abra-o num browser. Esta invoca o método Get ValuesController no modelo de Web API. Devolve a resposta de predefinido que é fornecida pelo modelo, uma matriz JSON que contém duas cadeias de:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto final que irá expor através da gestão de API no Azure.

 7. Por fim, implemente a aplicação em cluster no Azure. [Com o Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), clique com o botão direito no projeto de aplicação e selecione **publicar**. Forneça o ponto final de cluster (por exemplo, `mycluster.westus.cloudapp.azure.com:19000`) para implementar a aplicação ao cluster do Service Fabric no Azure.

Um serviço sem monitorização de estado do ASP.NET Core com o nome `fabric:/ApiApplication/WebApiService` devem agora estar a executar no seu cluster do Service Fabric no Azure.

## <a name="create-an-api-operation"></a>Criar uma operação de API

Agora, está prontos para criar uma operação na API Management que clientes externos utilizam para comunicar com o ASP.NET Core sem monitorização de estado de execução do serviço no cluster de Service Fabric.

 1. Inicie sessão no portal do Azure e navegue para a implementação de serviço de API Management.
 2. No painel do serviço de API Management, selecione **APIs - Preview**
 3. Adicionar uma nova API clicando a **API em branco** caixa e preencher a caixa de diálogo:

     - **URL do serviço Web**: para o back-ends de Service Fabric, este valor de URL não é utilizado. Pode colocar qualquer valor aqui. Para este tutorial, utilize: `http://servicefabric`.
     - **Nome**: forneça um nome para a API. Para este tutorial, utilize `Service Fabric App`.
     - **Esquema de URL**: selecione HTTP, HTTPS ou ambos. Para este tutorial, utilize `both`.
     - **Sufixo do URL da API**: forneça um sufixo nossa API. Para este tutorial, utilize `myapp`.
 
 4. Assim que a API for criada, clique em **+ adicionar operação** para adicionar uma operação de API de front-end. Preencha os valores:
    
     - **URL**: selecione `GET` e forneça um caminho de URL para a API. Para este tutorial, utilize `/api/values`.
     
       Por predefinição, o caminho do URL especificado aqui é o caminho de URL é enviado para o serviço de recursos de infraestrutura do serviço de back-end. Se utilizar o mesmo caminho de URL aqui que, neste caso, o serviço utiliza `/api/values`, em seguida, a operação funciona sem modificações. Também pode especificar um caminho de URL aqui que é diferente do caminho de URL utilizado pelo seu serviço de recursos de infraestrutura do serviço de back-end, nesse caso também terá de especificar um caminho de reescrever na sua política de operação mais tarde.
     - **Nome a apresentar**: forneça um nome para a API. Para este tutorial, utilize `Values`.

## <a name="configure-a-backend-policy"></a>Configure uma política de back-end

A política de back-end vincula tudo em conjunto. Este é onde configura o serviço de recursos de infraestrutura do serviço de back-end para os quais os pedidos são encaminhados. Pode aplicar esta política para todas as operações de API. O [configuração de back-end para o Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controlos de encaminhamento do pedido: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço no Service Fabric, ou codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerados no pedido de HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição ao gerar uma chave de partição utilizando qualquer esquema de partições de Service Fabric.
 - Seleção de réplica para serviços com monitorização de estado.
 - Condições de tentativas de resolução que permitem-lhe especificar as condições para resolver a localização de serviço e enviar um pedido.

Para este tutorial, crie uma política de back-end que encaminha os pedidos diretamente para o serviço sem monitorização de estado do ASP.NET Core implementado anteriormente:

 1. Selecione e editar o **políticas de entrada** para o `Values` operação clicando no ícone editar e, em seguida, selecionando **vista de código**.
 2. No editor de código de política, adicione um `set-backend-service` política em políticas de entrada, conforme mostrado aqui e clique em de **guardar** botão:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
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

### <a name="add-the-api-to-a-product"></a>Adicione a API para um produto. 

Antes de poder chamar a API, tem de ser adicionado a um produto onde pode conceder acesso aos utilizadores. 

 1. No serviço de API Management, selecione **produtos - pré-visualização**.
 2. Por predefinição, os produtos de fornecedores dois de gestão de API: Starter e ilimitada. Selecione o produto ilimitado.
 3. Selecione APIs.
 4. Clique em de **+ adicionar** botão.
 5. Selecione o `Service Fabric App` API que criou nos passos anteriores e clique em de **selecione** botão.

### <a name="test-it"></a>Testá-lo

Agora pode tentar enviar um pedido para o serviço de back-end no Service Fabric através da gestão de API diretamente a partir do portal do Azure.

 1. No serviço de API Management, selecione **API - pré-visualização**.
 2. No `Service Fabric App` API que criou nos passos anteriores, selecionados o **teste** separador.
 3. Clique em de **enviar** botão para enviar um pedido de teste para o serviço de back-end.

## <a name="next-steps"></a>Passos seguintes

Nesta fase, deve ter uma configuração básica com o Service Fabric e gestão de API.

Este tutorial utiliza a autenticação de utilizador básico baseada em certificado para o cluster do Service Fabric para ajudá-lo a configurar rapidamente. Autenticação de utilizador mais avançadas para o cluster do Service Fabric é preferível com [autenticação do Azure Active Directory](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Em seguida, [criar e configurar definições de produto avançadas na API Management do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) para preparar a sua aplicação para o tráfego do mundo real.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png
