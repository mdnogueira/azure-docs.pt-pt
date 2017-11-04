---
title: "Modelo de aplicação de Service Fabric do Azure | Microsoft Docs"
description: "Como modelo e descrevem as aplicações e serviços no Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: e30482427b88eb3e58d39075c7f0734664b79aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>Modelar uma aplicação no Service Fabric
Este artigo fornece uma descrição geral do modelo de aplicação de Service Fabric do Azure e como definir uma aplicação e do serviço através de ficheiros de manifesto.

## <a name="understand-the-application-model"></a>Compreender o modelo de aplicação
Uma aplicação é uma coleção de serviços constituintes que efetuar determinada uma função ou funções. Um serviço executa uma função completa e autónoma e pode iniciar e executar independentemente outros serviços.  Um serviço é composto por código, configuração e dados. Para cada serviço, código consiste dos binários do executável, configuração consiste em definições de serviço que podem ser carregadas em tempo de execução e dados constituem em dados estáticos arbitrários a ser consumidos pelo serviço. Cada componente neste modelo de aplicação hierárquica pode ser atualizado e com a versão independentemente.

![O modelo de aplicação de Service Fabric][appmodel-diagram]

Um tipo de aplicação é uma categorização de uma aplicação e é composta por um pacote de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter configurações e definições diferentes, mas a funcionalidade principal permanece igual. As instâncias de um serviço são variações de configuração de serviço diferentes do mesmo tipo de serviço.  

Classes (ou "tipos") de aplicações e serviços são descritas através de ficheiros XML (os manifestos da aplicação e manifestos de serviço).  Os manifestos são os modelos em relação às quais as aplicações podem ser instanciadas do cluster arquivo de imagens. A definição de esquema para o ficheiro ServiceManifest.xml e ApplicationManifest.xml é instalada com o SDK de Service Fabric e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

O código para instâncias de aplicações diferentes run processos separados, mesmo quando alojadas pelo mesmo nó de Service Fabric. Além disso, o ciclo de vida de cada instância da aplicação pode ser gerido (por exemplo, atualizado) independentemente. O diagrama seguinte mostra como tipos de aplicação são compostas por tipos de serviço, que por sua vez, são compostos de código, configuração e pacotes de dados. Para simplificar o diagrama, apenas o código/configuração/dados de pacotes para `ServiceType4` são apresentados, apesar de cada tipo de serviço inclui alguns ou todos os os tipos de pacotes.

![Tipos de aplicação de Service Fabric e os tipos de serviço][cluster-imagestore-apptypes]

Dois ficheiros de manifesto diferentes são utilizados para descrever aplicações e serviços: o manifesto de serviço e o manifesto da aplicação. Manifestos descritos detalhadamente nas secções seguintes.

Pode ser ativo no cluster de uma ou mais instâncias de um tipo de serviço. Por exemplo, instâncias de serviço com estado ou réplicas, atingir elevada fiabilidade através da replicação de estado entre réplicas localizadas em diferentes nós do cluster. Replicação essencialmente fornece redundância estar disponível, mesmo se falha um nó num cluster do serviço. A [particionada serviço](service-fabric-concepts-partitioning.md) mais divide o respetivo estado (e padrões de acesso para esse Estado) em nós do cluster.

O diagrama seguinte mostra a relação entre as aplicações e instâncias de serviço, partições e réplicas.

![As partições e réplicas dentro de um serviço][cluster-application-instances]

> [!TIP]
> Pode ver o esquema de aplicações num cluster utilizando a ferramenta de Service Fabric Explorer disponível na http://&lt;yourclusteraddress&gt;: 19080/Explorer. Para obter mais informações, consulte [visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 

## <a name="describe-a-service"></a>Descrevem um serviço
O manifesto do serviço forma declarativa define o tipo de serviço e a versão. Especifica os metadados do serviço como o tipo de serviço, propriedades de estado de funcionamento, métricas de balanceamento de carga, os binários de serviço e os ficheiros de configuração.  Colocar de outra forma, descreve os pacotes de código, configuração e dados que compõem um pacote de serviço para suportar um ou mais tipos de serviço. Eis um manifesto do serviço de exemplo simples:

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

**Versão** atributos são cadeias não estruturadas e não analisada pelo sistema. Versão atributos são utilizados para versão de cada componente para atualizações.

**ServiceTypes** declara que tipos de serviço são suportados pelo **CodePackages** neste manifesto. Quando um serviço é instanciado contra um dos seguintes tipos de serviço, são ativados todos os pacotes de código declarados no manifesto do executando os respetivos pontos de entrada. Espera-se os processos resultantes para registar os tipos de serviço suportados em tempo de execução. Tipos de serviço são declarados no nível do manifesto e não o nível de pacote do código. Por isso, quando existem vários pacotes de código, estes são todas ativadas sempre que o sistema procura qualquer um dos tipos de serviço declarado.

**SetupEntryPoint** é um ponto de entrada com privilégios que é executada com as mesmas credenciais de Service Fabric (normalmente o *LocalSystem* conta) antes de qualquer outro ponto de entrada. O executável especificado por **EntryPoint** é, geralmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita que o anfitrião do serviço seja executado com privilégios elevados para períodos de tempo prolongados. O executável especificado por **EntryPoint** for executado após **SetupEntryPoint** sai com sucesso. Se alguma vez, o processo termina ou falhas, o processo de resultante é monitorizado e reiniciado (a partir do novo **SetupEntryPoint**).  

Cenários típicos de utilização **SetupEntryPoint** tem quando executar um executável antes do início do serviço ou efetuar uma operação com privilégios elevados. Por exemplo:

* Configurar e inicializar as variáveis de ambiente que tem o executável do serviço. Não é limitado a apenas executáveis escritos através de modelos de programação do Service Fabric. Por exemplo, npm.exe tem algumas variáveis de ambiente configurados para implementar uma aplicação node.js.
* Configurar o controlo de acesso através da instalação de certificados de segurança.

Para obter mais detalhes sobre como configurar o **SetupEntryPoint** consulte [Configure a política para um ponto de entrada de configuração de serviço](service-fabric-application-runas-security.md)

**EnvironmentVariables** fornece uma lista de variáveis de ambiente que estão definidas para este pacote do código. Environmentment variáveis podem ser substituídas no `ApplicationManifest.xml` para fornecer valores diferentes para instâncias de serviço diferente. 

**DataPackage** declara uma pasta, com o nome de **nome** atributo, que contém dados estáticos arbitrários a ser consumidos pelo processo no tempo de execução.

**ConfigPackage** declara uma pasta, com o nome de **nome** atributo, que contém um *Settings.xml* ficheiro. O ficheiro de definições contém secções par definido pelo utilizador, o valor de chave de definições de que lê o processo de volta ao tempo de execução. Durante uma atualização, se apenas a **ConfigPackage** **versão** tiver sido alterado, em seguida, o processo em execução não for reiniciado. Em vez disso, uma chamada de retorno notifica o processo que as definições de configuração foram alteradas pelo que pode ser recarregadas dinamicamente. Eis um exemplo *Settings.xml* ficheiro:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> Um manifesto de serviço pode conter vários código, configuração e pacotes de dados. Cada um desses pode ter versão independentemente.
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Descrevem uma aplicação
O manifesto da aplicação forma declarativa descreve o tipo de aplicação e a versão. Especifica os metadados de composição de serviço, tais como nomes estáveis, criação de partições de esquema, fator de contagem/replicação de instância, política de segurança/isolamento, restrições de posicionamento, substituições de configuração e tipos de serviço que constituem. Também estão descritos os domínios de balanceamento de carga para o qual a aplicação está colocada.

Assim, um manifesto de aplicação descreve elementos ao nível da aplicação e referencia um ou mais manifestos de serviço para compor um tipo de aplicação. Eis um manifesto de aplicação de exemplo simples:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

Como manifestos de serviço, **versão** atributos são cadeias não estruturadas e não são analisados pelo sistema. Atributos de versão são também utilizada para a versão de cada componente atualizações.

**ServiceManifestImport** contém referências a manifestos de serviço que compõem este tipo de aplicação. Manifestos de serviço importado determinam que tipos de serviço são válidos dentro deste tipo de aplicação. Dentro do ServiceManifestImport, substituir valores de configuração em Settings.xml e ambiente variáveis nos ficheiros de ServiceManifest.xml. 


**DefaultServices** declara instâncias de serviço que são criadas automaticamente sempre que uma aplicação com instâncias criada em relação a este tipo de aplicação. Os serviços de predefinição são apenas para efeitos práticos e comportar-se como serviços normais no que cada respeita depois de terem sido criadas. Estes são atualizados juntamente com quaisquer outros serviços na instância da aplicação e podem ser removidos bem.

> [!NOTE]
> Um manifesto de aplicação pode conter várias importações de manifesto do serviço e os serviços de predefinição. Cada importação de manifesto do serviço pode ser com versão independentemente.
> 
> 

Para saber como manter diferente da aplicação e os parâmetros do serviço para ambientes individuais, consulte [gerir parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Passos seguintes
[Uma aplicação do pacote](service-fabric-package-apps.md) e torná-lo pronto para implementar.

[Implementar e remover aplicações] [ 10] descreve como utilizar o PowerShell para gerir instâncias da aplicação.

[Gerir parâmetros de aplicação para vários ambientes] [ 11] descreve como configurar os parâmetros e variáveis de ambiente para instâncias de aplicações diferentes.

[Configurar políticas de segurança para a sua aplicação] [ 12] descreve como executar os serviços de políticas de segurança para restringir o acesso.

[Aplicação aloja modelos] [ 13] descrevem a relação entre réplicas (ou instâncias) de um serviço implementado e o processo de anfitrião do serviço.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
[13]: service-fabric-hosting-model.md
