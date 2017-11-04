---
title: "Alterar as definições de KVSActorStateProvider no Azure micro-serviços | Microsoft Docs"
description: "Saiba mais sobre a configuração de atores com monitorização de estado do Azure Service Fabric do tipo KVSActorStateProvider."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: d3424aa7a8e0f6011bbef4aa61274c1f598f5c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurar Reliable Actors – KVSActorStateProvider
Pode modificar a configuração predefinida KVSActorStateProvider alterando o ficheiro de settings.xml que é gerado na raiz do pacote Microsoft Visual Studio sob a pasta de configuração para os atores especificado.

O tempo de execução do Service Fabric do Azure analisa para nomes de secção predefinidas no ficheiro settings.xml e consome os valores de configuração ao criar os componentes runtime subjacente.

> [!NOTE]
> Efetue **não** eliminar ou modificar os nomes de secção das seguintes configurações no ficheiro settings.xml que é gerado na solução Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
Configurações de segurança do replicador são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver uns dos outros tráfego de replicação, garantindo que os dados que são efetuados elevados também são seguros.
Por predefinição, uma secção de configuração de segurança vazio impede a segurança da replicação.

### <a name="section-name"></a>Nome de secção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador configurar replicador que é responsável por verificar o estado do fornecedor de estado de Ator altamente fiável.
A configuração predefinida é gerada pelo modelo de Visual Studio e deve suffice. Esta secção aborda as configurações adicionais que estão disponíveis para otimizar o replicador.

### <a name="section-name"></a>Nome de secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador na aguarda secundária depois de receber uma operação antes de enviar fazer uma cópia de uma confirmação para o site primário. Quaisquer outras confirmações sejam enviados para operações de processamento dentro deste intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/D |Sem predefinição - parâmetro necessário |Definir o endereço IP e a porta que o replicador principal/secundário irá utilizar para comunicar com outros os replicadores na réplica. Isto deverá referenciar um ponto de final de recursos TCP no manifesto de serviço. Consulte [recursos do serviço do manifesto](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto final no manifesto de serviço. |
| RetryInterval |Segundos |5 |Período de tempo após o qual o replicador novamente transmite uma mensagem se receber uma confirmação de uma operação. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |1024 |Número máximo de operações na fila principal. Uma operação é libertada cópias de segurança depois do replicador primário recebe uma confirmação dos secundários replicadores. Este valor tem de ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação é libertada cópias de segurança depois de efetuar o seu estado de elevada disponibilidade através de persistência. Este valor tem de ser superior a 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração do arquivo
Configurações de armazenamento são utilizadas para configurar o arquivo local que é utilizado para manter o estado de que está a ser replicado.
A configuração predefinida é gerada pelo modelo de Visual Studio e deve suffice. Esta secção aborda as configurações adicionais que estão disponíveis para otimizar o armazenamento local.

### <a name="section-name"></a>Nome de secção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |milissegundos |200 |Definir o máximo de criação de batches de intervalo de consolidações de arquivo local durável. |
| MaxVerPages |Número de páginas |16384 |O número máximo de páginas de versão no local armazena a base de dados. Determina o número máximo de transações pendentes. |

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>Observações
O parâmetro BatchAcknowledgementInterval controla a latência de replicação. Um valor de '0' resulta numa menor latência possível, ao custo de débito (como mais mensagens de confirmação tem de ser enviadas e processadas, cada que contém menos confirmações).
Quanto maior for o valor BatchAcknowledgementInterval, quanto maior for o geral débito de replicação, ao custo de latência de operação superior. Isto traduz-se diretamente para a latência de consolidações de transações.

