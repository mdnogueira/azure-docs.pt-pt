---
title: "Configurar fiáveis micro-serviços do Azure | Microsoft Docs"
description: "Saiba mais sobre como configurar Reliable Services com monitorização de estado no Service Fabric do Azure."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 84111b37f5cdecf377442bca0b15af2092d57414
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-stateful-reliable-services"></a>Configurar os serviços fiáveis com monitorização de estado
Existem dois conjuntos de definições de configuração para serviços fiáveis. Um conjunto é global para todos os serviços fiáveis do cluster enquanto o outro conjunto é específico para um determinado serviço fiável.

## <a name="global-configuration"></a>Configuração global
A configuração do serviço fiável global é especificada no manifesto do cluster para o cluster na secção KtlLogger. Permite que a configuração da localização de registo partilhado e tamanho com os limites de memória global utilizados pelo registo. O manifesto do cluster é um único ficheiro XML que contém as definições e configurações que se aplicam a todos os nós e serviços no cluster. O ficheiro é normalmente denominado ClusterManifest.xml. Pode ver o cluster para o cluster utilizando o comando do powershell Get-ServiceFabricClusterManifest manifesto.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |Número mínimo de KB alocar no modo de kernel para o conjunto de memória do registador escrita da memória intermédia. Este conjunto de memória é utilizado para colocar em cache as informações de estado antes de escrever no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem Limite |Pode aumentar o tamanho máximo para o qual o registo escrever o conjunto de memória de memória intermédia. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a utilizar para identificar o ficheiro de registo partilhado predefinido utilizado por todos os serviços fiáveis em todos os nós no cluster que não especificam o SharedLogId na respetiva configuração específica do serviço. Se não for especificado SharedLogId, em seguida, SharedLogPath deve ser também especificado. |
| SharedLogPath |Nome de caminho completamente qualificado |"" |Especifica o caminho completamente qualificado, onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós no cluster que não especificam o SharedLogPath na respetiva configuração específica do serviço. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId deve ser também especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estaticamente para o registo partilhado. O valor tem de ser 2048 ou superior. |

No ARM do Azure ou no modelo JSON no local, o exemplo abaixo mostra como alterar o registo de transações partilhado que é criado para fazer uma cópia de quaisquer coleções fiáveis para serviços com monitorização de estado.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Secção de manifesto do cluster de programador local do exemplo
Se pretender alterar isto no seu ambiente de desenvolvimento local, tem de editar o ficheiro local clustermanifest.xml.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Observações
O registo tem um conjunto de memória atribuída da memória de kernel não paginadas que está disponível para todos os serviços fiáveis num nó para a colocação em cache dados de estado antes de a ser escritos no registo dedicado associada com a réplica de serviço fiável global. O tamanho do conjunto é controlado pelas definições de WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Especifica o tamanho inicial deste conjunto de memória e o tamanho menor para os quais pode reduzir o conjunto de memória. WriteBufferMemoryPoolMaximumInKB é o tamanho maior para o qual pode aumentar o conjunto de memória. Cada réplica fiável de serviço que é aberta pode aumentar o tamanho do conjunto de memória por um período de sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se existir mais de pedido de memória no agrupamento de memória que está disponível, pedidos de memória irão sofrer um atraso de até que a memória está disponível. Por conseguinte, se o conjunto de memória de memória intermédia de escrita é demasiado pequeno para uma configuração específica, em seguida, o desempenho poderá diminuir.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUID e a localização do registo partilhado predefinido para todos os nós do cluster. O registo partilhado predefinido é utilizado para todos os serviços fiáveis que não especificam as definições em settings.xml para o serviço específico. Para melhor desempenho, os ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado para reduzir a contenção.

SharedLogSizeInMB Especifica a quantidade de espaço em disco para prealocar para o registo partilhado predefinição em todos os nós.  SharedLogId e SharedLogPath não tem de ser especificado por ordem para SharedLogSizeInMB seja especificado.

## <a name="service-specific-configuration"></a>Configuração específicos do serviço
Pode modificar as configurações de predefinido com monitorização de estado Reliable Services utilizando o pacote de configuração (configuração) ou a implementação de serviço (código).

* **Configuração** -configuração por intermédio do pacote de configuração é conseguido ao alterar o ficheiro de Settings.xml que é gerado na raiz do pacote Microsoft Visual Studio sob a pasta de configuração para cada serviço na aplicação.
* **Código** -configuração por intermédio do código é conseguido através da criação de um ReliableStateManager utilizando um objeto de ReliableStateManagerConfiguration com o conjunto de opções apropriadas.

Por predefinição, o tempo de execução do Service Fabric do Azure analisa para nomes de secção predefinidas no ficheiro Settings.xml e consome os valores de configuração ao criar os componentes runtime subjacente.

> [!NOTE]
> Efetue **não** eliminar os nomes de secção das seguintes configurações no ficheiro Settings.xml que é gerado na solução Visual Studio, a menos que planeia configurar o seu serviço através de código.
> Mudar o nome dos nomes de pacote ou uma secção de configuração irá exigir uma alteração de código, quando configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
Configurações de segurança do replicador são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não será capazes de ver uns dos outros tráfego de replicação, garantindo que os dados que são efetuados elevados também são seguros. Por predefinição, uma secção de configuração de segurança vazio impede a segurança da replicação.

### <a name="default-section-name"></a>Nome de secção predefinido
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de replicatorSecuritySectionName ao construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador configurar replicador que é responsável por verificar o estado do serviço fiável de monitorização de estado altamente fiável ao replicar e a persistência do Estado localmente.
A configuração predefinida é gerada pelo modelo de Visual Studio e deve suffice. Esta secção aborda as configurações adicionais que estão disponíveis para otimizar o replicador.

### <a name="default-section-name"></a>Nome de secção predefinido
ReplicatorConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de replicatorSettingsSectionName ao construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador na aguarda secundária depois de receber uma operação antes de enviar fazer uma cópia de uma confirmação para o site primário. Quaisquer outras confirmações sejam enviados para operações de processamento dentro deste intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/D |Sem predefinição - parâmetro necessário |Definir o endereço IP e a porta que o replicador principal/secundário irá utilizar para comunicar com outros os replicadores na réplica. Isto deverá referenciar um ponto de final de recursos TCP no manifesto de serviço. Consulte [recursos do serviço do manifesto](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto final no manifesto do serviço. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é libertada cópias de segurança depois do replicador primário recebe uma confirmação dos secundários replicadores. Este valor tem de ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada cópias de segurança depois de efetuar o seu estado de elevada disponibilidade através de persistência. Este valor tem de ser superior a 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |50 |Quantidade de espaço no ficheiro de registo após o qual o estado é checkpointed. |
| MaxRecordSizeInKB |KB |1024 |Tamanho de registo maior que o replicador pode escrever no registo. Este valor tem de ser um múltiplo de 4 e superior a 16. |
| MinLogSizeInMB |MB |0 (sistema de determinado) |Tamanho mínimo do registo transacional. O registo não será permitido para truncar para um tamanho inferior esta definição. 0 indica que o replicador determinará o tamanho mínimo de registo. Aumento deste valor aumenta a possibilidade de fazer cópias parciais e cópias de segurança incrementais desde possibilidades de registos de registo relevantes fossem truncados é lowered. |
| TruncationThresholdFactor |Fator |2 |Determina em que o tamanho do registo, será acionada truncagem. Limiar de truncagem é determinado pelo MinLogSizeInMB multiplicado pelo TruncationThresholdFactor. TruncationThresholdFactor tem de ser superior a 1. MinLogSizeInMB * TruncationThresholdFactor deve ser inferior a MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Fator |4 |Determina em que o tamanho do registo, a réplica começarão a ser limitado. Limiar de limitação (em MB) é determinado pelo número máximo ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Limiar de limitação (em MB) tem de ser superior ao limiar de truncagem (em MB). Limiar de truncagem (em MB) tem de ser inferior ao MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Máx. acumulados tamanho (em MB) de cópia de segurança registos numa cadeia de cópia de segurança de registo especificado. Um pedidos de cópia de segurança incremental falharão se a cópia de segurança incremental irá gerar um registo de cópia de segurança que faria com que os registos de cópia de segurança acumulados desde a relevante cópia de segurança completa ser maior do que este tamanho. Nestes casos, é pedido ao utilizador para efetuar uma cópia de segurança completa. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a utilizar para identificar o ficheiro de registo partilhados utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogId for especificado, em seguida, SharedLogPath deve ser também especificado. |
| SharedLogPath |Nome de caminho completamente qualificado |"" |Especifica o caminho completamente qualificado, onde o ficheiro de registo partilhado para esta réplica será criado. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId deve ser também especificado. |
| SlowApiMonitoringDuration |Segundos |300 |Define o intervalo de monitorização para chamadas de API geridos. Exemplo: o utilizador forneceu a função de chamada de retorno de cópia de segurança. Após ter passado o intervalo, será enviado um relatório de estado de funcionamento de aviso para o Gestor de estado de funcionamento. |

### <a name="sample-configuration-via-code"></a>Configuração de exemplo através de código
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Observações
BatchAcknowledgementInterval controla a latência de replicação. Um valor de '0' resulta numa menor latência possível, ao custo de débito (como mais mensagens de confirmação tem de ser enviadas e processadas, cada que contém menos confirmações).
Quanto maior for o valor BatchAcknowledgementInterval, quanto maior for o geral débito de replicação, ao custo de latência de operação superior. Isto traduz-se diretamente para a latência de consolidações de transações.

O valor para CheckpointThresholdInMB controla a quantidade de espaço em disco que pode utilizar o replicador para armazenar informações de estado no ficheiro de registo dedicado a réplica. Aumentar este para um valor superior à predefinição de pode resultar em tempos mais rápidos de reconfiguração quando uma nova réplica é adicionada ao conjunto. Isto acontece porque a transferência de estado parcial ocorre devido à disponibilidade do histórico mais operações no registo. Potencialmente, isto pode aumentar o tempo de recuperação de uma réplica após uma falha.

A definição de MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser escrito pelo replicador para o ficheiro de registo. Na maioria dos casos, o tamanho de 1024-KB registo predefinido é o ideal. No entanto, se o serviço está a causar maior itens de dados como parte das informações de estado, este valor poderá ter de ser aumentada. Não há pouca benefício tomar MaxRecordSizeInKB inferior a 1024, como registos de menores utilizam apenas o espaço necessário para o registo mais pequeno. Esperamos que este valor deverá ser necessário alterar em casos raros apenas.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para tornar um serviço a utilizar um registo partilhado separado do registo partilhado predefinido para o nó. Para melhor eficiência, como muitos serviços possível devem especificar o mesmo registo partilhado. Ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado para reduzir a contenção de movimento head. Esperamos que este valor deverá ser necessário alterar em casos raros apenas.

## <a name="next-steps"></a>Passos seguintes
* [Depurar a aplicação de Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)
* [Referência para programadores para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

