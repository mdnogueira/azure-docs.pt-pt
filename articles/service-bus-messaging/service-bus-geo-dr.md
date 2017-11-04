---
title: "Recuperação de Georreplicação-desastre do Service Bus do Azure | Microsoft Docs"
description: "Como utilizar regiões geográficas a ativação pós-falha e efetuar a recuperação de desastre no Service Bus do Azure"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Recuperação de Georreplicação-desastre do Service Bus do Azure (pré-visualização)

Quando centros de dados regionais tempo de inatividade, é essencial para o processamento de dados continuar a funcionar numa região diferente ou centro de dados. Como tal, *recuperação de desastres Georreplicação* e *georreplicação* são funcionalidades importantes para qualquer empresa. Service Bus do Azure suporta a recuperação de desastres Georreplicação e a georreplicação, ao nível do espaço de nomes. 

A pré-visualização de recuperação de desastre Georreplicação atualmente só está disponível em duas regiões (**Norte Central nos** e **Sul Central dos EUA)**.

## <a name="outages-and-disasters"></a>Falhas e desastres inesperados

O [melhores práticas para insulating aplicações contra falhas de Service Bus e perante desastres](service-bus-outages-disasters.md) artigo faz uma distinção entre "falhas" e "perante desastres," que é importante ter em conta. Um *indisponibilidade* é indisponibilidade temporária do Service Bus do Azure e pode afetar alguns componentes do serviço, tal como um arquivo de mensagens ou mesmo o todo o datacenter. No entanto, depois de corrigir o problema, Service Bus fica disponível novamente. Normalmente, uma falha não irá causar a perda de mensagens ou outros dados. Um exemplo de tal indisponibilidade poderá ser uma falha de energia no Centro de dados.

A *desastre* está definida como a perda permanente ou duração mais longa do barramento de serviço [unidade de escala](service-bus-architecture.md#service-bus-scale-units) ou datacenter. O Centro de dados poderá poderão não ficar disponível novamente ou pode estar inativo para horas ou dias. Exemplos de tais perante desastres são fire, sobrecarregar ou terramoto. Um desastre que torna-se permanentes poderá causar a perda de algumas mensagens ou outros dados. No entanto, na maioria dos casos não deverá existir nenhuma perda de dados e as mensagens podem ser recuperadas assim que o Centro de dados é uma cópia de segurança.

A funcionalidade de recuperação de desastre Georreplicação do Service Bus do Azure é uma solução de recuperação após desastre. Os conceitos e fluxo de trabalho descrito neste artigo aplicam-se a cenários de desastre e não a falhas transitórias ou temporárias.  

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

A funcionalidade de recuperação após desastre implementa a recuperação após desastre de metadados e baseia-se no servidor primário e um espaços de nomes de recuperação de desastre secundário. Tenha em atenção que a funcionalidade de recuperação de desastre Georreplicação está disponível para [espaços de nomes Premium](service-bus-premium-messaging.md) apenas. Não é necessário efetuar quaisquer alterações de cadeia de ligação, como a ligação é efetuada através de um alias.

Os termos seguintes são utilizados neste artigo:

-  *Alias*: A cadeia de ligação principal.

-  *Espaço de nomes do principal/secundário*: descreve os espaços de nomes que corresponde ao alias. O principal "ativo" e recebe mensagens, secundário é "passivo" e não receber mensagens. Os metadados entre ambos são sincronizados, para que ambos perfeitamente podem aceitar mensagens sem quaisquer alterações de código da aplicação.

-  *Metadados*: A representação de objetos do Service Bus do Azure. Atualmente suportamos apenas metadados.

-  *Ativação pós-falha*: O processo de ativação o espaço de nomes secundário. Tem de solicitar mensagens do seu espaço de nomes anteriormente primário assim que esta fica disponível novamente e, em seguida, elimine o espaço de nomes. Para criar outro ativação pós-falha, é possível adicionar um novo espaço de nomes secundário para o emparelhamento. Se pretender reutilizar o espaço de nomes de principal anteriores após uma ativação pós-falha, primeiro tem de remover todas as entidades existentes do espaço de nomes. Certifique-se receber todas as mensagens antes de fazê-lo.

## <a name="failover-workflow"></a>Fluxo de trabalho de ativação pós-falha

A secção seguinte é uma descrição geral de todo o processo de configuração de ativação de pós-falha inicial e como avançar partir desse ponto.

![1][]

Primeiro de configurar um site primário e um espaço de nomes secundário, então criar um emparelhamento. Este emparelhamento dá-lhe um alias que pode utilizar para estabelecer a ligação. Uma vez que utilizam um alias, não dispõe de alterar cadeias de ligação. Apenas novos espaços de nomes podem ser adicionados para o emparelhamento de ativação pós-falha. Por fim, tem de adicionar alguns lógica do acionador (por exemplo, algumas lógica de negócio que Deteta se o espaço de nomes não está disponível e inicia a ativação pós-falha). Pode procurar através de disponibilidade de espaço de nomes de [mensagem navegação](message-browsing.md) capacidade do Service Bus.

Depois de ter configurado as de monitorização e recuperação após desastre, pode examinar o processo de ativação pós-falha. Se o acionador inicia uma ativação pós-falha ou iniciar manualmente a ativação pós-falha, os dois passos são necessários:

1. Em caso de falha de outra, deve ser capaz de ativação pós-falha novamente. Por conseguinte, configurar um espaço de nomes passivo segundo e atualize o emparelhamento. 
2. Solicitar mensagens do espaço de nomes anteriormente primário depois do novo espaço de nomes está disponível. Depois disso, reutilizar ou eliminar o espaço de nomes primário antigo.

![2][]

## <a name="set-up-disaster-recovery"></a>Configurar a recuperação após desastre

Esta secção descreve como criar o seu próprio código de recuperação do Service Bus Georreplicação-desastre. Para tal, precisa de dois espaços de nomes em localizações independentes; Por exemplo,-Sul dos EUA e Norte Central-nos. O exemplo seguinte utiliza 2017 do Visual Studio.

1.  Crie um novo **consola App(.Net Framework)** projeto no Visual Studio e atribua um nome; por exemplo, **SBGeoDR**.

2.  Instale os pacotes de NuGet seguintes:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Certifique-se de que a versão do pacote NuGet newtonsoft que estiver a utilizar a versão 10.0.3.

3.  Adicione o seguinte `using` instruções para o seu código:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Modificar o `main()` método para adicionar dois espaços de nomes premium:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Ative o emparelhamento entre os dois espaços de nomes e obter o alias que mais tarde, utilizar para ligar ao seu entidades:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Configurou com êxito dois espaços de nomes emparelhados. Agora, pode criar entidades para observar a sincronização de metadados. Se pretender efetuar uma ativação pós-falha imediatamente posteriormente, deve permitir algum tempo para que os metadados para a sincronizar. Pode adicionar um tempo de suspensão pequeno, por exemplo:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

Neste momento, pode adicionar entidades através do portal ou através do Azure Resource Manager e, consulte como sincronizar. A menos que o plano para ativação pós-falha manualmente, deve criar uma aplicação que monitoriza o seu espaço de nomes principal e inicia a ativação pós-falha, se ficar indisponível. 

## <a name="initiate-a-failover"></a>Inicie uma ativação pós-falha

O código seguinte mostra como iniciar uma ativação pós-falha:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Após o acionar a ativação pós-falha, adicionar um novo espaço de nomes passivo e restabelecer o emparelhamento. O código para criar um novo emparelhamento é mostrado na secção anterior. Além disso, tem de remover as mensagens do espaço de nomes primário antigo depois de concluída a ativação pós-falha. Para obter exemplos de como receber mensagens a partir de uma fila, consulte [introdução às filas](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Como desativar a recuperação de desastres Georreplicação

O código seguinte mostra como desativar um espaço de nomes emparelhamento:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

O seguinte código elimina o alias que criou:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Passos após uma ativação pós-falha (reativação pós-falha)

Após uma ativação pós-falha, execute os seguintes dois passos:

1.  Crie um novo passivo secundário espaço de nomes. O código é mostrado na secção anterior.
2.  Remova as mensagens restantes da sua fila.

## <a name="alias-connection-string-and-test-code"></a>Código de cadeia e teste de ligação de alias

Se pretender testar o processo de ativação pós-falha, pode escrever uma aplicação de exemplo que envia mensagens para o espaço de nomes primário a utilizar o alias. Para tal, certifique-se de que obtêm a cadeia de ligação de alias de um espaço de nomes do Active Directory. Com a versão de pré-visualização atual, não há nenhuma outra interface diretamente obter a cadeia de ligação. O código de exemplo seguintes liga antes e após a ativação pós-falha:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Passos seguintes

- Consulte a recuperação de desastre Georreplicação [referência da REST API aqui](/rest/api/servicebus/disasterrecoveryconfigs).
- Executar a recuperação de desastre Georreplicação [em GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Consulte a recuperação de desastre Georreplicação [amostra que envia mensagens para um alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Para mais informações sobre mensagens do Service Bus, consulte os artigos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
