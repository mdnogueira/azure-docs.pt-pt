---
title: Azure Service Fabric proxy inverso | Microsoft Docs
description: "Utilize proxy inverso do Service Fabric para comunicação com micro-serviços a partir de dentro e fora do cluster."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: bharatn
ms.openlocfilehash: 3168a8129e2e73d7ab1de547679aabd10d8f7112
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy inverso no Azure Service Fabric
Proxy inverso incorporado no Service Fabric do Azure ajuda-o em execução num cluster de Service Fabric micro-serviços detetar e comunicar com outros serviços que tenham pontos finais de http.

## <a name="microservices-communication-model"></a>Modelo de comunicação micro-serviços
Micro-serviços no Service Fabric executam num subconjunto de nós no cluster e podem migrar entre os nós por vários motivos. Como resultado, os pontos finais para micro-serviços podem alterar dinamicamente. Para detetar e comunicar com outros serviços em cluster, microsserviço deve seguir os seguintes passos:

1. Resolva a localização de serviço através do serviço de nomenclatura.
2. Liga ao serviço.
3. Moldar os passos anteriores num ciclo que implementa a resolução de serviço e repita as políticas para aplicar em falhas de ligação

Para obter mais informações, consulte [Connect e comunicar com serviços](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicar através do proxy inverso
Proxy inverso é um serviço que é executada em cada nó e processa a resolução de ponto final, tentativas automáticas e outras falhas de ligação em nome de serviços do cliente. Proxy inverso pode ser configurado para aplicar políticas de vários como que processa os pedidos de serviços do cliente. Utilizar um proxy inverso permite que o serviço de cliente para utilizar as bibliotecas de comunicação do lado do cliente HTTP e não requerem resolução especial e repetir lógica no serviço. 

Proxy inverso expõe um ou mais pontos finais no nó local para os serviços de cliente a utilizar para enviar pedidos para outros serviços.

![Comunicação interna][1]

> **Plataformas suportadas**
>
> Proxy inverso no Service Fabric atualmente suporta as seguintes plataformas
> * *Cluster do Windows*: Windows 8 e posterior ou Windows Server 2012 e posterior
> * *Cluster do Linux*: Proxy inverso não está atualmente disponível para clusters do Linux

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atingir micro-serviços a partir de fora do cluster
O modelo de comunicação externo predefinido para micro-serviços é um modelo de recusa onde cada serviço não pode ser acedido diretamente a partir de clientes externos. [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md), que é um limite de rede entre micro-serviços e clientes externos, efetua a tradução de endereços de rede e encaminha pedidos externos IP:port interna de pontos finais. Para tornar o ponto final de um microsserviço diretamente acessível aos clientes externos, primeiro tem de configurar o Balanceador de carga para encaminhar o tráfego para cada porta que o serviço utiliza no cluster. Além disso, a maioria das micro-serviços, especialmente micro-serviços com monitorização de estado, não em direto em todos os nós do cluster. Podem mover os micro-serviços entre nós de ativação pós-falha. Nestes casos, o Balanceador de carga eficazmente não é possível determinar a localização do nó de destino das réplicas para a qual deve reencaminhar tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Atingir micro-serviços através do proxy inverso partir de fora do cluster
Em vez de configurar a porta de um serviço individual no balanceador de carga, pode configurar apenas a porta do proxy inverso no balanceador de carga. Esta configuração permite que os clientes fora do cluster a aceder aos serviços dentro do cluster através do proxy inverso sem configuração adicional.

![Comunicação externa][0]

> [!WARNING]
> Quando configura a porta do proxy inverso no balanceador de carga, todos os micro-serviços no cluster que expõem um ponto final de HTTP são endereçáveis a partir de fora do cluster.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato de URI para endereçamento serviços através do proxy inverso
O proxy inverso utiliza um formato (URI identifier) de recurso uniform específico para identificar a partição de serviço ao qual o pedido de entrada deve ser reencaminhado:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** do proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Reencaminhamento de HTTPS, consulte [ligar a um serviço seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md) assim que tiver a configuração de proxy inverso para escutar HTTPS.
* **Nome de domínio completamente qualificado (FQDN) do cluster | IP interno:** para clientes externos, pode configurar o proxy inverso para que seja acessível através de domínio do cluster, tais como mycluster.eastus.cloudapp.azure.com. Por predefinição, o proxy inverso é executada em cada nó. Para o tráfego interno, o proxy reverso pode ser contactado no localhost ou IP qualquer nó interno, como 10.0.0.1.
* **Porta:** esta é a porta, tais como 19081, foi especificada para o proxy inverso.
* **ServiceInstanceName:** este é o nome completamente qualificado da instância de serviço implementado que está a tentar aceder sem o "fabric: /" esquema. Por exemplo, para alcançar o *fabric: / myapp/myservice/* serviço, teria de utilizar *myapp/myservice*.

    O nome de instância de serviço é maiúsculas e minúsculas. Utilizar uma maiúsculas e minúsculas diferentes para o nome de instância de serviço no URL, faz com que os pedidos falhar com 404 (não for encontrado).
* **Caminho de sufixo:** este é o caminho de URL real, tais como *myapi/valores/Adicionar/3*, para o serviço que pretende ligar.
* **PartitionKey:** para um serviço particionado, esta é a chave de partição calculada da partição que pretende aceder. Tenha em atenção que isto *não* a GUID de ID de partição. Este parâmetro não é necessário para serviços que utilizam o esquema de partição singleton.
* **PartitionKind:** este é o esquema de partição de serviço. Isto pode ser 'Int64Range' ou 'Com o nome'. Este parâmetro não é necessário para serviços que utilizam o esquema de partição singleton.
* **ListenerName** os pontos finais do serviço de têm o formato {"Pontos finais": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}}. Quando o serviço expõe vários pontos finais, esta identifica o ponto final que o pedido de cliente deve ser reencaminhado para. Isto pode ser omitido se o serviço tem apenas um serviço de escuta.
* **TargetReplicaSelector** esta ação Especifica a forma como a réplica de destino ou a instância deve ser selecionada.
  * Quando o serviço de destino está com monitorização de estado, o TargetReplicaSelector pode ser um dos seguintes: 'PrimaryReplica', 'RandomSecondaryReplica' ou 'RandomReplica'. Quando este parâmetro não for especificado, a predefinição é 'PrimaryReplica'.
  * Quando o serviço de destino está sem monitorização de estado, o proxy inverso escolhe uma instância da partição de serviço para reencaminhar o pedido para aleatória.
* **Tempo limite:** Isto especifica o tempo limite para o pedido HTTP criado pelo proxy inverso para o serviço em nome do pedido do cliente. O valor predefinido é de 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de utilização
Por exemplo, vamos o *fabric: / MyApp/MyService* serviço que abre um serviço de escuta HTTP no seguinte URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Seguem-se os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço utiliza singleton a criação de partições de esquema, o *PartitionKey* e *PartitionKind* parâmetros de cadeia de consulta não são necessários e o serviço pode ser contactado através do gateway como:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente:`http://localhost:19081/MyApp/MyService`

Se o serviço utiliza o esquema de partições Uniform Int64, o *PartitionKey* e *PartitionKind* parâmetros de cadeia de consulta tem de ser utilizados para aceder a uma partição do serviço:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para aceder os recursos que expõe o serviço, basta colocar o caminho do recurso depois do nome de serviço no URL:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway, em seguida, irá reencaminhar estes pedidos para o URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para a partilha de porta de serviços
Gateway de aplicação do Azure tenta resolver novamente um endereço de serviço e repita o pedido quando não é possível aceder um serviço. Esta é uma vantagem principal de Gateway de aplicação porque o código de cliente não precisa de implementar a suas próprias resolução de serviço e resolver o ciclo.

Geralmente, quando não é possível aceder um serviço, a instância de serviço ou a réplica foi movido para um nó diferente como parte do respetivo ciclo de vida normal. Quando isto acontecer, o Gateway de aplicação pode receber um erro de ligação de rede que indica que um ponto final já não está aberto no endereço originalmente resolvido.

No entanto, as réplicas ou instâncias de serviço, pode partilhar um processo de anfitrião e também podem partilhar uma porta quando alojada por um servidor web com base em http.sys, incluindo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener de núcleo de ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nesta situação, é provável que o servidor web está disponível no processo de anfitrião e responder a pedidos, mas a instância de serviço resolvido ou a réplica já não está disponível no anfitrião. Neste caso, o gateway irá receber uma resposta de HTTP 404 do servidor web. Assim, um HTTP 404 tem dois significados distintos:

- Caso #1: O endereço de serviço está correto, mas o recurso que o utilizador solicitou não existe.
- Caso #2: O endereço do serviço está incorreto e o recurso que o utilizador solicitou pode existir num nó diferente.

O primeiro caso é um HTTP 404 normal, que é considerado um erro de utilizador. No entanto, no segundo caso, o utilizador solicitou um recurso que existe. Gateway de aplicação não foi possível localizá-la porque moveu o próprio serviço. Gateway de aplicação tem de resolver o endereço novamente e repita o pedido.

Gateway de aplicação, por isso, precisa de uma forma para distinguir entre estas duas casos. Para se certificar de que distinção, é necessária uma sugestão do servidor.

* Por predefinição, o Gateway de aplicação é caso #2 e tenta resolver e emitir o pedido novamente.
* Para indicar caso #1 para o Gateway de aplicação, o serviço deverá devolver o cabeçalho de resposta HTTP seguinte:

  `X-ServiceFabric : ResourceNotFound`

Este cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal em que o recurso pedido não existe e Gateway de aplicação não irá tentar resolver o endereço de serviço novo.

## <a name="setup-and-configuration"></a>Instalação e configuração

### <a name="enable-reverse-proxy-via-azure-portal"></a>Ativar o proxy inverso através do portal do Azure

Portal do Azure fornece uma opção para ativar o proxy inverso ao criar um novo cluster de Service Fabric.
Em **cluster do Service Fabric criar**, passo 2: configuração de Cluster, configuração de tipo de nó, selecione a caixa de verificação "Ativar o proxy inverso".
Para configurar o proxy inverso seguro, o certificado SSL pode ser especificado no passo 3: segurança, configurar definições de segurança do cluster, selecione a caixa de verificação "Incluem um certificado SSL para o proxy inverso" e introduza os detalhes do certificado.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Ativar o proxy inverso através de modelos Azure Resource Manager

Pode utilizar o [modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para ativar o proxy inverso no Service Fabric para o cluster.

Consulte [configurar HTTPS Proxy inverso num cluster seguro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) para o Azure Resource Manager amostras de modelo para configurar segura inverso proxy com um rollover de certificado do certificado e processamento.

Em primeiro lugar, pode obter o modelo para o cluster que pretende implementar. Pode utilizar os modelos de exemplo ou criar um modelo personalizado do Gestor de recursos. Em seguida, pode ativar o proxy reverso utilizando os seguintes passos:

1. Definir uma porta para o proxy reverso no [secção parâmetros](../azure-resource-manager/resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos no nodetype o **Cluster** [secção de tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md).

    A porta é identificada pelo nome do parâmetro, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Para resolver o proxy reverso de fora do cluster do Azure, defina as regras de Balanceador de carga do Azure para a porta que especificou no passo 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar certificados SSL na porta para o proxy inverso, adicionar o certificado para o ***reverseProxyCertificate*** propriedade no **Cluster** [secção de tipo de recurso](../resource-group-authoring-templates.md) .

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suporte de um certificado de proxy inverso que é diferente do certificado de cluster
 Se o certificado de proxy inverso é diferente do certificado que protege o cluster, em seguida, o certificado especificado anteriormente deve ser instalado na máquina virtual e adicionado à lista de controlo de acesso (ACL) para que o Service Fabric pode aceder ao mesmo. Isto pode ser feito **virtualMachineScaleSets** [secção de tipo de recurso](../resource-group-authoring-templates.md). Para instalação, adicione esse certificado para o osProfile. A secção de extensão do modelo pode atualizar o certificado na ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Quando utilizar certificados que sejam diferentes do certificado de cluster para ativar o proxy inverso num cluster existente, instalar o certificado de proxy inverso e atualize a ACL no cluster antes de ativar o proxy inverso. Concluir o [modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) implementação utilizando as definições mencionadas anteriormente antes de iniciar uma implementação para ativar o proxy inverso nos passos 1 a 4.

## <a name="next-steps"></a>Passos seguintes
* Ver um exemplo de comunicação HTTP entre serviços num [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Reencaminhamento para o serviço HTTP seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Chamadas de procedimento remoto com o sistema de interação remota Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que utiliza OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação de WCF utilizando Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* Para opções de configuração de proxy inverso adicionais, consulte a secção ApplicationGateway/Http [definições de cluster do Service Fabric personalizar](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
