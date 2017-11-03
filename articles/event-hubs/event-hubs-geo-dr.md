---
title: "Recuperação de desastre georreplicação de Event Hubs do Azure | Microsoft Docs"
description: "Como utilizar regiões geográficas a ativação pós-falha e efetuar a recuperação de desastre em Event Hubs do Azure"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Recuperação de desastre georreplicação Event Hubs do Azure (pré-visualização)

Quando centros de dados regionais tempo de inatividade, é essencial para o processamento de dados continuar a funcionar numa região diferente ou centro de dados. Como tal, *recuperação de desastres georreplicação* e *georreplicação* são funcionalidades importantes para qualquer empresa. Os Hubs de eventos do Azure suporta a recuperação de desastres georreplicação e a georreplicação, ao nível do espaço de nomes. 

A funcionalidade de recuperação de desastre georreplicação dos Event Hubs do Azure é uma solução de recuperação após desastre. Os conceitos e fluxo de trabalho descrito neste artigo aplicam-se a cenários de desastre e não a falhas transitórias ou temporárias.

Para ver um debate detalhado da recuperação após desastre no Microsoft Azure, consulte [neste artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminologia

**O emparelhamento**: O espaço de nomes primário é referido como *Active Directory* e receber mensagens. O espaço de nomes de ativação pós-falha é *passivo* e não receber mensagens. Os metadados entre ambos são sincronizados, para que ambos perfeitamente podem aceitar mensagens sem quaisquer alterações de código da aplicação. Estabelecer a configuração da recuperação após desastre entre o Active Directory região e região passivo é conhecido como *emparelhamento* as regiões.

**Alias**: um nome para uma configuração de recuperação de desastres que configurou. O alias fornece uma cadeia de ligação da totalmente domínio nome qualificado (FQDN) estável. Aplicações utilizam esta cadeia de ligação de alias para ligar a um espaço de nomes.

**Metadados**: refere-se a nomes de hub de eventos, os grupos de consumidores, partições, unidades de débito, entidades e propriedades que estão associadas com o espaço de nomes.

## <a name="enable-geo-disaster-recovery"></a>Ativar a recuperação de desastre georreplicação

Ativar a recuperação de desastre georreplicação de Event Hubs no 3 passos: 

1. Crie um georreplicação-emparelhamento, que cria uma cadeia de ligação de alias e fornece replicação de metadados em direto. 
2. Atualize as cadeias de ligação de cliente existente para o alias que criou no passo 1.
3. Inicie a ativação pós-falha: o emparelhamento georreplicação está quebrado e o alias aponta para o espaço de nomes secundário como o novo espaço de nomes de principal.

A figura seguinte mostra este fluxo de trabalho:

![Fluxo de emparelhamento de Georreplicação][1] 

### <a name="step-1-create-a-geo-pairing"></a>Passo 1: criar um emparelhamento georreplicação

Para criar um emparelhamento entre duas regiões, terá de um espaço de nomes principal e um espaço de nomes secundário. Em seguida, crie um alias para formar o par de georreplicação. Depois dos espaços de nomes são emparelhados com um alias, os metadados periodicamente são replicados em ambos os espaços de nomes. 

O código seguinte mostra como efetuar este procedimento:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Passo 2: atualizar as cadeias de ligação de cliente existente

Assim que o emparelhamento georreplicação estiver concluído, as cadeias de ligação que apontam para os espaços de nomes primários têm de ser atualizadas para apontar para a cadeia de ligação de alias. Obter as cadeias de ligação conforme mostrado no exemplo seguinte:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Passo 3: Inicie uma ativação pós-falha

Se ocorrer um desastre, ou se optar por inicie uma ativação pós-falha para o espaço de nomes secundária, metadados e dados iniciar que fluem para o espaço de nomes secundário. Uma vez que as aplicações utilizam as cadeias de ligação de alias, não é necessária nenhuma ação adicional à medida que começa automaticamente ao ler a partir do e de escrever os event hubs no espaço de nomes secundário. 

O código seguinte mostra como acionar a ativação pós-falha:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Assim que a ativação pós-falha estiver concluída e terá dos dados presentes no espaço de nomes de principal, a extrair os dados tem de utilizar uma cadeia de ligação explícita para os event hubs no espaço de nomes primário.

### <a name="other-operations-optional"></a>Outras operações (opcionais)

Também pode interromper o emparelhamento georreplicação ou eliminar um alias, conforme mostrado no seguinte código. Tenha em atenção que ao eliminar uma cadeia de ligação de alias, tem primeiro quebrar georreplicação-paring:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Considerações para a pré-visualização pública

Tenha em atenção as seguintes considerações nesta versão:

1. A capacidade de recuperação de desastre georreplicação está disponível apenas nas regiões Norte Central-nos e Sul Central-nos. 
2. A funcionalidade só é suportada para espaços de nomes criado recentemente.
3. Para a versão de pré-visualização, apenas os metadados replicação está ativada. Não são replicados dados reais.
4. Com a versão de pré-visualização, há sem qualquer custo para ativar a funcionalidade. No entanto, o principal e os espaços de nomes secundários resultará em encargos das unidades de débito reservado.

## <a name="next-steps"></a>Passos seguintes

* O [em GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) explica um fluxo de trabalho simple que cria um emparelhamento georreplicação e inicia uma ativação pós-falha para um cenário de recuperação de desastres.
* O [referência da REST API](/rest/api/eventhub/disasterrecoveryconfigs) descreve APIs para efetuar a configuração da recuperação após desastre de Georreplicação.

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

