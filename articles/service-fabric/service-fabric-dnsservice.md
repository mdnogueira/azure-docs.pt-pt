---
title: "Serviço DNS de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Utilize o serviço de dns de Service Fabric para detetar micro-serviços a partir de dentro do cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS nos recursos de infraestrutura de serviço do Azure
O serviço DNS é um serviço de sistema opcional que pode ativar no seu cluster para detetar outros serviços utilizando o protocolo DNS.

Vários serviços, especialmente de serviços, podem ter um nome de URL existente, e conseguir resolvê-los utilizando o protocolo DNS padrão (em vez do protocolo serviço de nomenclatura) é desejável, particularmente em cenários de "de comparação de precisão e deslocar". O serviço DNS permite-lhe mapear os nomes de DNS para um nome de serviço e, por conseguinte, resolva endereços de IP do ponto final. 

O serviço DNS mapeia nomes de DNS para nomes de serviço, que por sua vez, são resolvidos pelo serviço de nomenclatura para devolver o ponto final do serviço. O nome DNS para o serviço é fornecido no momento da criação. 

![pontos finais de serviço][0]

## <a name="enabling-the-dns-service"></a>Ativar o serviço DNS
Primeiro tem de ativar o serviço DNS no seu cluster. Obter o modelo para o cluster que pretende implementar. Pode utilizar o [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou criar um modelo do Resource Manager. Pode ativar o serviço DNS com os seguintes passos:

1. Verifique se o `apiversion` está definido como `2017-07-01-preview` para o `Microsoft.ServiceFabric/clusters` recurso e se não estiver, atualizá-lo conforme mostrado no seguinte fragmento:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ativar agora o serviço DNS adicionando o seguinte `addonFeatures` secção após o `fabricSettings` secção conforme mostrado no seguinte fragmento: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Depois de atualizar o modelo de cluster com as alterações anteriores, aplicá-las e permitir que a atualização concluída. Depois de concluído, inicia o serviço de sistema DNS em execução no cluster denominado `fabric:/System/DnsService` na secção de serviço do sistema no Explorador do Service Fabric. 

Em alternativa, pode ativar o serviço DNS através do portal no momento da criação do cluster. O serviço DNS pode ser ativado ao marcar a caixa para `Include DNS service` no `Cluster configuration` menu conforme mostrado na captura de ecrã seguinte:

![Ativar o serviço do DNS através do portal][2]


## <a name="setting-the-dns-name-for-your-service"></a>O nome DNS para o seu serviço da definição
Assim que o DNS está em execução no seu cluster, que pode definir um nome DNS para os serviços de modo declarativo para serviços de predefinição no `ApplicationManifest.xml` ou através de comandos do Powershell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definir o nome DNS para um serviço predefinido no ApplicationManifest.xml
Abra o projeto no Visual Studio ou o seu editor favorito e abra o `ApplicationManifest.xml` ficheiro. Aceda à secção serviços predefinido e, para cada serviço, adicione o `ServiceDnsName` atributo. O exemplo seguinte mostra como definir o nome DNS do serviço de`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Assim que a aplicação é implementada, a instância de serviço no Explorador do Service Fabric mostra o nome DNS para esta instância, conforme mostrado na figura seguinte: 

![pontos finais de serviço][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>A definição do nome DNS para um serviço com o Powershell
Pode definir o nome DNS para um serviço ao criá-la utilizando o `New-ServiceFabricService` Powershell. O exemplo seguinte cria um novo serviço sem monitorização de estado com o nome DNS`service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Utilizar o DNS nos seus serviços
Se implementar mais do que um serviço, pode encontrar os pontos finais de outros serviços para comunicar com o utilizando um nome DNS. O serviço DNS só é aplicável aos serviços sem monitorização de estado, uma vez que o protocolo DNS não é possível comunicar com serviços com monitorização de estado. Para os serviços com monitorização de estado, pode utilizar o proxy inverso incorporado para chamadas http para chamar uma partição de serviço específica.

O código seguinte mostra como chamar outro serviço, o que é simplesmente uma chamada de regular http onde pode fornecer a porta e qualquer caminho opcional como parte do URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a comunicação de serviço no cluster com [ligar e comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
