---
title: "Gerir vários ambientes no Service Fabric | Microsoft Docs"
description: "Aplicações de Service Fabric podem ser executadas em clusters que o intervalo de tamanho de uma máquina para milhares de máquinas. Em alguns casos, irá querer configurar a sua aplicação para esses ambientes variadas. Este artigo abrange como definir parâmetros da aplicação diferente por ambiente."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Gerir parâmetros de aplicação para vários ambientes
Pode criar clusters do Service Fabric do Azure utilizando qualquer parte de um para muitos milhares de máquinas. Enquanto os binários da aplicação podem ser executada sem modificação entre este largo espetro de ambientes, muitas vezes pretende configurar a aplicação de forma diferente, dependendo do número de máquinas que estiver a implementar.

Como um exemplo simples, considere `InstanceCount` para um serviço sem estado. Quando estiver a executar as aplicações no Azure, que geralmente pretende defina este parâmetro para o valor especial de "-1". Esta configuração assegura que o serviço está em execução em cada nó do cluster (ou todos os nós no tipo de nó, se tiver definido uma restrição de posicionamento). No entanto, esta configuração não é adequada para um cluster único máquina, uma vez que não pode ter vários processos de escuta no ponto final da mesmo num único computador. Em vez disso, normalmente definidas `InstanceCount` para "1".

## <a name="specifying-environment-specific-parameters"></a>Especificar os parâmetros de específico do ambiente
A solução para este problema de configuração é um conjunto de serviços de parametrizada predefinido e os ficheiros de parâmetro de aplicação que preencha os valores de parâmetro para um determinado ambiente. Serviços de predefinido e parâmetros de aplicação estão configurados em manifestos de aplicação e serviços. A definição de esquema para os ficheiros ServiceManifest.xml e ApplicationManifest.xml é instalada com o SDK de Service Fabric e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Serviços de predefinido
Aplicações de Service Fabric são constituídas por uma coleção de instâncias de serviço. Embora seja possível criar uma aplicação em branco e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria das aplicações têm um conjunto de serviços de núcleos que deve sempre ser criado quando a aplicação é instanciada. Estes são denominados "serviços predefinido". Estes são especificados no manifesto da aplicação, com marcadores de posição para a configuração de per ambiente incluído entre parênteses Retos:

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Cada um dos parâmetros com nome tem de ser definida no elemento de parâmetros de manifesto da aplicação:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

O atributo DefaultValue Especifica o valor a ser utilizado na ausência de um parâmetro de mais específicos para um determinado ambiente.

> [!NOTE]
> Nem todos os parâmetros de instância de serviço são adequados para a configuração de per ambiente. No exemplo acima, os valores de LowKey e HighKey para o esquema de partições do serviço explicitamente são definidos para todas as instâncias do serviço, uma vez que o intervalo de partição é uma função do domínio de dados, não o ambiente.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Definições de configuração do serviço por ambiente
O [modelo de aplicação de Service Fabric](service-fabric-application-model.md) permite que os serviços incluir pacotes de configuração que contêm pares chave-valor personalizados que são legíveis em tempo de execução. Os valores destas definições também podem ser diferenciados por ambiente especificando um `ConfigOverride` no manifesto da aplicação.

Suponha que tem a seguinte definição no ficheiro Config\Settings.xml para o `Stateful1` serviço:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Para substituir este valor para um par de aplicação/ambiente específico, crie um `ConfigOverride` quando importa o manifesto de serviço no manifesto da aplicação.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Este parâmetro, em seguida, pode ser configurado pelo ambiente, conforme mostrado acima. Pode fazê-lo por declará-lo na secção de parâmetros de manifesto da aplicação e especificando valores de específico do ambiente nos ficheiros de parâmetro de aplicação.

> [!NOTE]
> No caso de definições de configuração de serviço, existem três locais onde o valor de uma chave pode ser definido: o pacote de configuração de serviço, o manifesto da aplicação e o ficheiro de parâmetros de aplicação. Recursos de infraestrutura de serviço irá sempre escolher ficheiro da aplicação parâmetro primeiro (se especificada), em seguida, o manifesto da aplicação e, finalmente, o pacote de configuração.
> 
> 

### <a name="setting-and-using-environment-variables"></a>A definição e utilizar variáveis de ambiente 
Pode especificar e definir variáveis de ambiente no ficheiro ServiceManifest.xml e, em seguida, substituir no ficheiro ApplicationManifest.xml numa base por instância.
O exemplo abaixo mostra duas variáveis de ambiente, uma com um valor definido e o outro é substituído. Pode utilizar os parâmetros de aplicação para definir valores de variáveis de ambiente da mesma forma que estes foram utilizados para substituições de configuração.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Para substituir as variáveis de ambiente de ApplicationManifest.xml, façam referência ao pacote de código no ServiceManifest com o `EnvironmentOverrides` elemento.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 Uma vez criada a instância de serviço com o nome pode aceder a variáveis de ambiente a partir do código. Por exemplo, em c# que pode fazer o seguinte

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Variáveis de ambiente do Service Fabric
As variáveis de ambiente definidas para cada instância de serviço criada Service Fabric. A lista completa de variáveis de ambiente abaixo, onde aqueles em negrito são aqueles que irá utilizar no seu serviço, o outro que está a ser utilizado pelo tempo de execução do Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **TypeEndpoint Fabric_Endpoint_ [YourServiceName]**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

O belows de código mostra como listar as variáveis de ambiente do Service Fabric
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Seguem-se exemplos de variáveis de ambiente de um tipo de aplicação chamado `GuestExe.Application` com um tipo de serviço chamado `FrontEndService` quando executado no seu computador de desenvolvimento local.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = código**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = node_2**

### <a name="application-parameter-files"></a>Ficheiros de parâmetro de aplicação
O projeto de aplicação de Service Fabric pode incluir um ou mais ficheiros de parâmetro de aplicação. Cada um deles define dos valores específicos para os parâmetros que estão definidos no manifesto da aplicação:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Por predefinição, uma nova aplicação inclui três ficheiros de parâmetro de aplicação, com o nome Local.1Node.xml, Local.5Node.xml e Cloud.xml:

![Ficheiros de parâmetro de aplicação no Explorador de soluções][app-parameters-solution-explorer]

Para criar um ficheiro de parâmetros, basta copiar e colar existente e atribua um nome novo.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificação de específico do ambiente de parâmetros durante a implementação
No momento da implementação, tem de escolher o ficheiro de parâmetros adequados para aplicar com a sua aplicação. Pode fazê-lo através da caixa de diálogo Publicar no Visual Studio ou através do PowerShell.

### <a name="deploy-from-visual-studio"></a>Implementar a partir do Visual Studio
Pode escolher entre a lista de ficheiros de parâmetros disponíveis quando publicar a aplicação no Visual Studio.

![Escolha um ficheiro de parâmetros na caixa de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implementar a partir do PowerShell
O `Deploy-FabricApplication.ps1` script do PowerShell incluído no modelo de projeto de aplicação aceita um perfil de publicação como um parâmetro e o PublishProfile contém uma referência para o ficheiro de parâmetros de aplicação.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos conceitos principais que são debatidos neste tópico, consulte o [descrição geral técnica do Service Fabric](service-fabric-technical-overview.md). Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
