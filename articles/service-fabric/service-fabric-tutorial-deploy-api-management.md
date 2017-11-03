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
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: d98d2823c19f24a2d9040f7959bd5189bd6bcc16
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Implementar a gestão de API com o Service Fabric
Este tutorial é parte dois de uma série. Este tutorial mostra como configurar [API Management do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para encaminhar o tráfego para um serviço de back-end no Service Fabric.  Quando tiver terminado, ter implementado a API Management para uma VNET, configurada uma operação de API para enviar o tráfego para serviços sem monitorização de estado de back-end. Para saber mais sobre os cenários de API Management do Azure com o Service Fabric, consulte o [descrição geral](service-fabric-api-management-overview.md) artigo.

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
> * Implementar a gestão de API com o Service Fabric

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Criar segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure
- Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instalar [Visual Studio 2017](http://www.visualstudio.com) e **programação do Azure**, **desenvolvimento ASP.NET e web**, e **desenvolvimento de plataformas cruzadas .NET Core**cargas de trabalho.  Em seguida, configure um [ambiente de desenvolvimento de .NET](service-fabric-get-started.md).
- Se implementar um cluster do Linux, configurar um ambiente de desenvolvimento Java no [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instalar o [Service Fabric CLI](service-fabric-cli.md). 

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

## <a name="deploy-api-management"></a>Implementar a gestão de API
Aplicações em nuvem, normalmente, precisam de um gateway de front-end para fornecer um único ponto de entrada para os utilizadores, dispositivos ou outras aplicações. No Service Fabric, um gateway pode ser qualquer serviço sem monitorização de estado como uma aplicação ASP.NET Core ou outro serviço concebida para a entrada de tráfego, como os Event Hubs, o IoT Hub ou API Management do Azure. Este tutorial é uma introdução à utilização de API Management do Azure como um gateway para as aplicações de Service Fabric. Gestão de API integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um vasto conjunto de regras de encaminhamento para os serviços de recursos de infraestrutura do serviço de back-end. 

Agora que tem um segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure, implementar a gestão de API para a rede virtual (VNET) na sub-rede e NSG designado para a gestão de API. Para este tutorial, o modelo de Gestor de recursos de gestão de API está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no anterior [tutorial de cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [tutorial do Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
 
- [APIM.JSON][apim-arm]
- [APIM.Parameters.JSON][apim-parameters-arm]

Preencha os parâmetros vazios o `apim.parameters.json` para a sua implementação.

Utilize o seguinte script para implementar os ficheiros de parâmetros e modelo do Resource Manager para a gestão de API:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Configurar a gestão de API

Depois do cluster de gestão de API e do Service Fabric são implementados, pode configurar as definições de segurança e um back-end do Service Fabric na API Management. Isto permite-lhe criar uma política de serviço de back-end que envia o tráfego para o cluster do Service Fabric.

### <a name="configure-api-management-security"></a>Configurar a segurança da gestão de API

Para configurar o back-end do Service Fabric, terá primeiro de configurar as definições de segurança de API Management. Para configurar as definições de segurança, aceda ao seu serviço de gestão de API no portal do Azure.

#### <a name="enable-the-api-management-rest-api"></a>Ativar a gestão de API REST API

A API de REST de gestão de API está atualmente a única forma de configurar um serviço de back-end. O primeiro passo é ativar a API de REST de gestão de API e proteja-a.

 1. No serviço de API Management, selecione **API de gestão** em **segurança**.
 2. Verifique o **ativar API de REST de gestão de API** caixa de verificação.
 3. Tenha em atenção o **URL da API de gestão**, que iremos utilizar mais tarde para configurar o back-end do Service Fabric.
 4. Gerar um **Token de acesso** ao selecionar uma data de expiração e uma chave, em seguida, clique em de **gerar** botão para parte inferior da página.
 5. Copiar o **token de acesso** e guardá-lo.  Utilizamos o token de acesso nos passos seguintes. Tenha em atenção de que isto é diferente da chave primária e chave secundária.

#### <a name="upload-a-service-fabric-client-certificate"></a>Carregar um certificado de cliente de Service Fabric

API Management tem de autenticar com o cluster de Service Fabric para a deteção de serviço a utilizar um certificado de cliente que tenha acesso ao cluster. Simplicidade, este tutorial utiliza o mesmo certificado que especificou anteriormente ao criar o [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, por predefinição pode ser utilizado para aceder ao seu cluster.

 1. No serviço de API Management, selecione **certificados de cliente** em **segurança**.
 2. Clique em de **+ adicionar** botão.
 2. Selecione o ficheiro de chave privada (. pfx) do certificado de cluster que especificou ao criar o cluster do Service Fabric, atribua um nome e fornecer a palavra-passe da chave privada.

> [!NOTE]
> Este tutorial utiliza o mesmo certificado para autenticação de cliente e de segurança do nó para o nó de cluster. Pode utilizar um certificado de cliente separado, se tiver uma configurado para aceder ao seu cluster do Service Fabric.

### <a name="configure-the-backend"></a>Configurar o back-end

Agora que a configuração da segurança de API Management, pode configurar o back-end do Service Fabric. Para o back-ends de Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço específico do Service Fabric. Isto permite que uma única política encaminhar para mais do que um serviço no cluster.

Este passo necessita do token de acesso gerados anteriormente e o thumbprint para o seu certificado de cluster que carregou anteriormente para a API Management.

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

O **url** parâmetro aqui é um nome de serviço completamente qualificado de um serviço no seu cluster de que todos os pedidos são encaminhados para por predefinição se não for especificado nenhum nome de serviço numa política de back-end. Pode utilizar um nome de serviço falsificados, tal como "fabric: / falsificados/serviço" se não pretender tem um serviço de contingência.

Consulte a API Management [documentação de referência da API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) para obter mais detalhes sobre cada campo.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementar um serviço de back-end do Service Fabric

Agora que tem o Service Fabric configurado como um back-end para a API Management, pode criar políticas de back-end para as suas APIs que enviam tráfego para os serviços do Service Fabric. Mas, primeiro precisa de um serviço em execução no Service Fabric para aceitar pedidos.  Se criou anteriormente um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), implementar um serviço de .NET Service Fabric.  Se criou anteriormente um [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md), implementar um serviço de recursos de infraestrutura de serviço de Java.

### <a name="deploy-a-net-service-fabric-service"></a>Implementar um serviço de recursos de infraestrutura de serviço de .NET

Para este tutorial, vamos criar um serviço básico de sem monitorização de estado ASP.NET Core fiável utilizando o modelo de projeto Web API predefinido. Esta ação cria um ponto final de HTTP para o seu serviço, que expõe através da API Management do Azure:

```
/api/values
```

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

    Isto permite que o Service Fabric especificar uma porta dinamicamente a partir do intervalo de portas a aplicação, que foi abertas através do grupo de segurança no modelo de Gestor de recursos de cluster, a permitir o tráfego flua ao mesmo de API Management.
 
 6. Prima F5 no Visual Studio para verificar a API web está disponível localmente. 

    Abrir Explorador do Service Fabric e Desagregue para uma instância específica do serviço de núcleo de ASP.NET para ver o endereço base do serviço está a escutar. Adicionar `/api/values` para a base de endereços e abra-o num browser. Esta invoca o método Get ValuesController no modelo de Web API. Devolve a resposta de predefinido que é fornecida pelo modelo, uma matriz JSON que contém duas cadeias de:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto final que expõe através da gestão de API no Azure.

 7. Por fim, implemente a aplicação em cluster no Azure. [Com o Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), clique com o botão direito no projeto de aplicação e selecione **publicar**. Forneça o ponto final de cluster (por exemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implementar a aplicação ao cluster do Service Fabric no Azure.

Um serviço sem monitorização de estado do ASP.NET Core com o nome `fabric:/ApiApplication/WebApiService` devem agora estar a executar no seu cluster do Service Fabric no Azure.

### <a name="create-a-java-service-fabric-service"></a>Criar um serviço de Java Service Fabric
Para este tutorial, iremos implementar um servidor de web básica que echoes mensagens novamente para o utilizador. A aplicação de exemplo do servidor de eco contém um ponto final de HTTP para o seu serviço, que expõe através da API Management do Azure.

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


## <a name="create-an-api-operation"></a>Criar uma operação de API

Agora, está prontos para criar uma operação na API Management que clientes externos utilizam para comunicar com o ASP.NET Core sem monitorização de estado de execução do serviço no cluster de Service Fabric.

1. Inicie sessão no portal do Azure e navegue para a implementação de serviço de API Management.
2. No painel do serviço de API Management, selecione **APIs**
3. Adicionar uma nova API clicando **+ API**, em seguida, **API em branco** caixa e preencher a caixa de diálogo:

    - **URL do serviço Web**: para o back-ends de Service Fabric, este valor de URL não é utilizado. Pode colocar qualquer valor aqui. Para este tutorial, utilize: "http://servicefabric".
    - **Nome a apresentar**: forneça um nome para a API. Para este tutorial, utilize "Recursos de infraestrutura do serviço de aplicações".
    - **Nome**: introduza "aplicação de recursos de infraestrutura de serviço".
    - **Esquema de URL**: selecione **HTTP**, **HTTPS**, ou **ambos**. Para este tutorial, utilize **ambos**.
    - **Sufixo do URL da API**: forneça um sufixo nossa API. Para este tutorial, utilize "myapp".
 
4. Selecione **do App Service Fabric** da lista de APIs e clique em **+ adicionar operação** para adicionar uma operação de API de front-end. Preencha os valores:
    
    - **URL**: selecione **obter** e forneça um caminho de URL para a API. Para este tutorial, utilize "valores/api /".  Por predefinição, o caminho do URL especificado aqui é o caminho de URL é enviado para o serviço de recursos de infraestrutura do serviço de back-end. Se utilizar o mesmo caminho de URL aqui que utiliza o seu serviço, neste caso, "/ api/valores", em seguida, a operação funciona sem modificações. Também pode especificar um caminho de URL aqui que é diferente do caminho de URL utilizado pelo seu back-end serviço do Service Fabric, caso em que também tem de especificar um caminho de reescrever na sua política de operação mais tarde.
    - **Nome a apresentar**: forneça um nome para a API. Para este tutorial, utilize "Valores".

5. Clique em **Guardar**.

## <a name="configure-a-backend-policy"></a>Configure uma política de back-end

A política de back-end vincula tudo em conjunto. Este é onde configura o serviço de recursos de infraestrutura do serviço de back-end para os quais os pedidos são encaminhados. Pode aplicar esta política para todas as operações de API. O [configuração de back-end para o Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controlos de encaminhamento do pedido: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço no Service Fabric, ou codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerados no pedido de HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição ao gerar uma chave de partição utilizando qualquer esquema de partições de Service Fabric.
 - Seleção de réplica para serviços com monitorização de estado.
 - Condições de tentativas de resolução que permitem-lhe especificar as condições para resolver a localização de serviço e enviar um pedido.

Para este tutorial, crie uma política de back-end que encaminha os pedidos diretamente para o serviço sem monitorização de estado do ASP.NET Core implementado anteriormente:

 1. Selecione e editar o **políticas de entrada** para a operação de valores ao clicar no ícone editar e, em seguida, selecionando **vista de código**.
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

### <a name="add-the-api-to-a-product"></a>Adicionar a API para um produto 

Antes de poder chamar a API, tem de ser adicionado a um produto onde pode conceder acesso aos utilizadores. 

 1. No serviço de API Management, selecione **produtos**.
 2. Por predefinição, os produtos de fornecedores dois de gestão de API: Starter e ilimitada. Selecione o produto ilimitado.
 3. Selecione **+ adicionar APIs**.
 4. Selecione o `Service Fabric App` API que criou nos passos anteriores e clique em de **selecione** botão.

### <a name="test-it"></a>Testá-lo

Agora pode tentar enviar um pedido para o serviço de back-end no Service Fabric através da gestão de API diretamente a partir do portal do Azure.

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
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes
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