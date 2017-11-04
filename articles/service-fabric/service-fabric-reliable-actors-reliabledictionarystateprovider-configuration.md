---
title: "Alterar as definições de ReliableDictionaryActorStateProvider no Azure micro-serviços | Microsoft Docs"
description: "Saiba mais sobre a configuração de atores com monitorização de estado do Azure Service Fabric do tipo ReliableDictionaryActorStateProvider."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: 
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 5dcd1b4f5a070e9a09b6f8338928d93d10227d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurar Reliable Actors – ReliableDictionaryActorStateProvider
Pode modificar a configuração predefinida ReliableDictionaryActorStateProvider alterando o ficheiro de settings.xml gerado na raiz do pacote Visual Studio sob a pasta de configuração para os atores especificado.

O tempo de execução do Service Fabric do Azure analisa para nomes de secção predefinidas no ficheiro settings.xml e consome os valores de configuração ao criar os componentes runtime subjacente.

> [!NOTE]
> Efetue **não** eliminar ou modificar os nomes de secção das seguintes configurações no ficheiro settings.xml que é gerado na solução Visual Studio.
> 
> 

Existem também definições globais que afetam a configuração de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração global
A configuração global é especificada no manifesto do cluster para o cluster na secção KtlLogger. Permite que a configuração da localização de registo partilhado e tamanho com os limites de memória global utilizados pelo registo. Tenha em atenção que as alterações no manifesto do cluster afetam todos os serviços que utilizam ReliableDictionaryActorStateProvider e reliable services com monitorização de estado.

O manifesto do cluster é um único ficheiro XML que contém as definições e configurações que se aplicam a todos os nós e serviços no cluster. O ficheiro é normalmente denominado ClusterManifest.xml. Pode ver o cluster para o cluster utilizando o comando do powershell Get-ServiceFabricClusterManifest manifesto.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |Número mínimo de KB alocar no modo de kernel para o conjunto de memória do registador escrita da memória intermédia. Este conjunto de memória é utilizado para colocar em cache as informações de estado antes de escrever no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem Limite |Pode aumentar o tamanho máximo para o qual o registo escrever o conjunto de memória de memória intermédia. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a utilizar para identificar o ficheiro de registo partilhado predefinido utilizado por todos os serviços fiáveis em todos os nós no cluster que não especificam o SharedLogId na respetiva configuração específica do serviço. Se não for especificado SharedLogId, em seguida, SharedLogPath deve ser também especificado. |
| SharedLogPath |Nome de caminho completamente qualificado |"" |Especifica o caminho completamente qualificado, onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós no cluster que não especificam o SharedLogPath na respetiva configuração específica do serviço. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId deve ser também especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estaticamente para o registo partilhado. O valor tem de ser 2048 ou superior. |

### <a name="sample-cluster-manifest-section"></a>Secção de manifesto do cluster de exemplo
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Observações
O registo tem um conjunto de memória atribuída da memória de kernel não paginadas que está disponível para todos os serviços fiáveis num nó para a colocação em cache dados de estado antes de a ser escritos no registo dedicado associada com a réplica de serviço fiável global. O tamanho do conjunto é controlado pelas definições de WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Especifica o tamanho inicial deste conjunto de memória e o tamanho menor para os quais pode reduzir o conjunto de memória. WriteBufferMemoryPoolMaximumInKB é o tamanho maior para o qual pode aumentar o conjunto de memória. Cada réplica fiável de serviço que é aberta pode aumentar o tamanho do conjunto de memória por um período de sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se existir mais de pedido de memória no agrupamento de memória que está disponível, pedidos de memória irão sofrer um atraso de até que a memória está disponível. Por conseguinte, se o conjunto de memória de memória intermédia de escrita é demasiado pequeno para uma configuração específica, em seguida, o desempenho poderá diminuir.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUID e a localização do registo partilhado predefinido para todos os nós do cluster. O registo partilhado predefinido é utilizado para todos os serviços fiáveis que não especificam as definições em settings.xml para o serviço específico. Para melhor desempenho, os ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado para reduzir a contenção.

SharedLogSizeInMB Especifica a quantidade de espaço em disco para prealocar para o registo partilhado predefinição em todos os nós.  SharedLogId e SharedLogPath não tem de ser especificado por ordem para SharedLogSizeInMB seja especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
Configurações de segurança do replicador são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver uns dos outros tráfego de replicação, garantindo que os dados que são efetuados elevados também são seguros.
Por predefinição, uma secção de configuração de segurança vazio impede a segurança da replicação.

### <a name="section-name"></a>Nome de secção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador são utilizadas para configurar o replicador que é responsável por verificar o estado do fornecedor de estado de Ator altamente fiável ao replicar e a persistência do Estado localmente.
A configuração predefinida é gerada pelo modelo de Visual Studio e deve suffice. Esta secção aborda as configurações adicionais que estão disponíveis para otimizar o replicador.

### <a name="section-name"></a>Nome de secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador na aguarda secundária depois de receber uma operação antes de enviar fazer uma cópia de uma confirmação para o site primário. Quaisquer outras confirmações sejam enviados para operações de processamento dentro deste intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/D |Sem predefinição - parâmetro necessário |Definir o endereço IP e a porta que o replicador principal/secundário irá utilizar para comunicar com outros os replicadores na réplica. Isto deverá referenciar um ponto de final de recursos TCP no manifesto de serviço. Consulte [recursos do serviço do manifesto](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto final no manifesto do serviço. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é libertada cópias de segurança depois do replicador primário recebe uma confirmação dos secundários replicadores. Este valor tem de ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada cópias de segurança depois de efetuar o seu estado de elevada disponibilidade através de persistência. Este valor tem de ser superior a 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |200 |Quantidade de espaço no ficheiro de registo após o qual o estado é checkpointed. |
| MaxRecordSizeInKB |KB |1024 |Tamanho de registo maior que o replicador pode escrever no registo. Este valor tem de ser um múltiplo de 4 e superior a 16. |
| OptimizeLogForLowerDiskUsage |Valor booleano |VERDADEIRO |Quando verdadeiro, o registo está configurado para que o ficheiro de registo dedicado a réplica é criado utilizando um ficheiro disperso NTFS. Isto reduz a utilização do espaço em disco real para o ficheiro. Se for FALSO, o ficheiro é criado com alocações fixas, que fornecem que o melhor desempenho de escrita. |
| SharedLogId |GUID |"" |Especifica um guid exclusivo a utilizar para identificar o ficheiro de registo partilhados utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogId for especificado, em seguida, SharedLogPath deve ser também especificado. |
| SharedLogPath |Nome de caminho completamente qualificado |"" |Especifica o caminho completamente qualificado, onde o ficheiro de registo partilhado para esta réplica será criado. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId deve ser também especificado. |

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
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

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço em disco que pode utilizar o replicador para armazenar informações de estado no ficheiro de registo dedicado a réplica. Aumentar este para um valor superior à predefinição de pode resultar em tempos mais rápidos de reconfiguração quando uma nova réplica é adicionada ao conjunto. Isto acontece porque a transferência de estado parcial ocorre devido à disponibilidade do histórico mais operações no registo. Potencialmente, isto pode aumentar o tempo de recuperação de uma réplica após uma falha.

Se definir OptimizeForLowerDiskUsage como true, espaço do ficheiro de registo será excessiva aprovisionado para que as réplicas Active Directory podem armazenar mais informações de Estado nos respetivos ficheiros de registo, enquanto réplicas Inativas irão utilizar menos espaço em disco. Isto torna possível alojar réplicas mais de um nó. Se definir OptimizeForLowerDiskUsage como false, as informações de estado são escritas mais depressa para os ficheiros de registo.

A definição de MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser escrito pelo replicador para o ficheiro de registo. Na maioria dos casos, o tamanho de 1024-KB registo predefinido é o ideal. No entanto, se o serviço está a causar maior itens de dados como parte das informações de estado, este valor poderá ter de ser aumentada. Não há pouca benefício tomar MaxRecordSizeInKB inferior a 1024, como registos de menores utilizam apenas o espaço necessário para o registo mais pequeno. Esperamos que este valor deverá ser necessário alterar apenas em casos raros.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para tornar um serviço a utilizar um registo partilhado separado do registo partilhado predefinido para o nó. Para melhor eficiência, como muitos serviços possível devem especificar o mesmo registo partilhado. Ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado, para reduzir a contenção de movimento head. Esperamos que estes valores deverá ser necessário alterar apenas em casos raros.

