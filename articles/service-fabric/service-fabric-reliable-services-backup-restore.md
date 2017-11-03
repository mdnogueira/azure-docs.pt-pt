---
title: "Cópia de segurança de recursos de infraestrutura de serviço e de restauro | Microsoft Docs"
description: "Documentação conceptual para cópia de segurança do serviço de recursos de infraestrutura e de restauro"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,jessebenson
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: mcoskun
ms.openlocfilehash: 8d81abec1c879ac6edbd4610dafdfd43ec7cf903
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Criar cópias de segurança e restauro Reliable Services e Reliable Actors
Azure Service Fabric é uma plataforma de elevada disponibilidade que replica o estado em vários nós para manter esta elevada disponibilidade.  Assim, mesmo se um nó no cluster falha, os serviços continuam a estar disponíveis. Apesar deste redundância incorporada no fornecida pela plataforma do pode ser suficiente para algumas, em certos casos é desejável que o serviço de cópia de segurança de dados (para um arquivo de externo).

> [!NOTE]
> É fundamental para cópia de segurança e restaurar os dados (e testar se funciona conforme esperado) pelo que pode recuperar a partir de cenários de perda de dados.
> 
> 

Por exemplo, um serviço pode querer cópias de segurança para proteger contra os seguintes cenários:

- Em caso de perda permanente de um cluster de Service Fabric completo.
- Perda permanente da maioria das réplicas de uma partição de serviço
- Erros administrativos na qual o estado acidentalmente obtém eliminado ou danificado. Por exemplo, isto pode acontecer se um administrador com privilégios suficientes indica elimina o serviço.
- Erros no serviço danificar os dados. Por exemplo, isto pode acontecer quando uma atualização de código do serviço começa a escrever dados defeituoso numa coleção fiável. Nesse caso, o código e os dados poderão ter de ser revertida para um estado anterior.
- Processamento de dados offline. Pode ser conveniente para que o processamento de dados de business intelligence, que ocorre em separado do serviço que gera dados offline.

A funcionalidade de cópia de segurança/restauro permite que os serviços incorporados na API serviços fiável para criar e restaurar cópias de segurança. As APIs de cópia de segurança fornecidas pela plataforma do permitem backup(s) do Estado de uma partição de serviço, sem operações de escrita ou de bloqueio de leitura. O restauro APIs permitir que o estado de uma partição de serviço para ser restaurado a partir de uma cópia de segurança que escolheu.

## <a name="types-of-backup"></a>Tipos de cópia de segurança
Existem duas opções de cópia de segurança: completa e Incremental.
Cópia de segurança completa é uma cópia de segurança que contém todos os dados necessários para recriar o estado da réplica: pontos de verificação e todos os registos.
Uma vez que tem os pontos de verificação e o registo, uma cópia de segurança completa pode ser restaurada por si só.

O problema com as cópias de segurança completas for quando os pontos de verificação são grandes.
Por exemplo, uma réplica que tenha de 16 GB de estado irá ter pontos de verificação que adicionam até, aproximadamente, 16 GB.
Se tivermos um objetivo de ponto de recuperação de cinco minutos, a réplica tem de ser feita a cada cinco minutos.
Sempre que efetua cópias de segurança tem de copiar 16 GB de pontos de verificação para além de 50 MB (configurável utilizando `CheckpointThresholdInMB`) visão de registos.

![Exemplo de cópia de segurança completa.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para este problema é cópias de segurança incrementais, onde o cópia de segurança contém apenas os registos alterados desde a última cópia de segurança.

![Exemplo de cópia de segurança incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Uma vez que as cópias de segurança incrementais são apenas as alterações desde a última cópia de segurança (não inclui os pontos de verificação), têm tendência ser mais rápida, mas não pode ser restaurados por si próprios.
Para restaurar uma cópia de segurança incremental, toda a cadeia de cópia de segurança, é necessária.
Uma cadeia de cópia de segurança é uma cadeia de carateres começadas uma cópia de segurança completa de cópias de segurança e seguido por um número de cópias de segurança incrementais contíguos.

## <a name="backup-reliable-services"></a>Cópia de segurança Reliable Services
O autor do serviço tem controlo total sobre quando efetuar cópias de segurança e armazenar as cópias de segurança.

Para iniciar uma cópia de segurança, o serviço tem de invocar a função de membro herdado `BackupAsync`.  
As cópias de segurança podem ser compostas apenas por réplicas primárias e necessitam que o estado de escrita para ser concedido.

Conforme mostrado abaixo, `BackupAsync` aceita um `BackupDescription` objeto, onde um pode especificar uma cópia de segurança completa ou incremental, bem como uma função de chamada de retorno, `Func<< BackupInfo, CancellationToken, Task<bool>>>` que é invocada quando a pasta de cópia de segurança foi criada localmente e está pronta para ser movida para algumas armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Pedido para efetuar uma cópia de segurança incremental pode falhar com `FabricMissingFullBackupException`. Esta exceção indica que uma das seguintes ações está a acontecer:

- a réplica nunca realizou uma cópia de segurança completa, uma vez que tornou primária,
- Alguns dos registos desde a última cópia de segurança foi truncada ou
- réplica transmitida a `MaxAccumulatedBackupLogSizeInMB` limite.

Os utilizadores podem aumentar a probabilidade de ser capaz de fazer cópias de segurança incrementais configurando `MinLogSizeInMB` ou `TruncationThresholdFactor`.
Tenha em atenção que aumentar estes valores aumenta a por utilização de disco de réplica.
Para obter mais informações, consulte [fiável de serviços de configuração](service-fabric-reliable-services-configuration.md)

`BackupInfo`Fornece informações sobre a cópia de segurança, incluindo a localização da pasta onde o tempo de execução guardar a cópia de segurança (`BackupInfo.Directory`). A função de chamada de retorno pode mover o `BackupInfo.Directory` para um arquivo de externo ou noutra localização.  Esta função devolve também um booleano que indica se foi possível mover com êxito a pasta de cópia de segurança para a localização de destino.

O código seguinte demonstra como o `BackupCallbackAsync` método pode ser utilizado para carregar a cópia de segurança para o Storage do Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo que precede, `ExternalBackupStore` é a classe de exemplo que é utilizada para a interface com o Blob storage do Azure, e `UploadBackupFolderAsync` é o método que comprime a pasta e coloca-lo no arquivo de Blob do Azure.

Tenha em atenção que:

  - Podem existir apenas uma operação de cópia de segurança em trânsito por réplica em qualquer momento. Mais do que um `BackupAsync` chamada a uma hora irá gerar `FabricBackupInProgressException` para limitar a um de cópias de segurança em utilização.
  - Se uma réplica falhar enquanto uma cópia de segurança está em curso, a cópia de segurança não pode ter sido concluída. Assim, depois de concluir a ativação pós-falha, é responsabilidade do serviço ao reiniciar a cópia de segurança invocando `BackupAsync` conforme necessário.

## <a name="restore-reliable-services"></a>Restaurar Reliable Services
Em geral, cenários de quando poderá ter de efetuar uma operação de restauro enquadram-se uma destas categorias:

  - O serviço de perda de dados de partição. Por exemplo, o disco para duas fora de três réplicas para uma partição (incluindo a réplica primária) obtém danificado ou eliminado. A nova principal poderá ter de restaurar dados a partir de uma cópia de segurança.
  - Todo o serviço não se tenha perdido. Por exemplo, um administrador remove a todo o serviço e, consequentemente, o serviço e os dados têm de ser restaurados.
  - O serviço replicados dados de aplicação danificado (por exemplo, devido a um erro de aplicação). Neste caso, o serviço tem de ser atualizado ou reverter para remover a causa dos danos e não danificados dados tem de ser restaurados.

Enquanto várias abordagens são possíveis, que oferecemos alguns exemplos sobre como utilizar `RestoreAsync` para recuperar a partir de cenários acima.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição em Reliable Services
Neste caso, o tempo de execução seria detetar a perda de dados e invocar automaticamente o `OnDataLossAsync` API.

O autor do serviço tem de efetuar o seguinte para recuperar:

  - Substitua o método de classe base virtual `OnDataLossAsync`.
  - Localize a cópia de segurança mais recente na localização externa que contém as cópias de segurança do serviço.
  - Transferir a cópia de segurança mais recente (e descomprimir a cópia de segurança para a pasta de cópia de segurança se foi comprimida).
  - O `OnDataLossAsync` método fornece um `RestoreContext`. Chamar o `RestoreAsync` API fornecido `RestoreContext`.
  - Devolve true se o restauro foi concluído com êxito.

Segue-se uma implementação de exemplo do `OnDataLossAsync` método:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`transmitida para o `RestoreContext.RestoreAsync` chamada contém um membro chamado `BackupFolderPath`.
Ao restaurar uma única cópia de segurança completa, isto `BackupFolderPath` deve ser definido como o caminho local da pasta que contém a cópia de segurança completa.
Ao restaurar uma cópia de segurança completa e um número de cópias de segurança incrementais, `BackupFolderPath` deve ser definido como o caminho local da pasta que contém não apenas a cópia de segurança completa, mas também todas as cópias de segurança incrementais.
`RestoreAsync`chamada pode acionar `FabricMissingFullBackupException` se o `BackupFolderPath` fornecido não contém uma cópia de segurança completa.
-Também pode acionar `ArgumentException` se `BackupFolderPath` tem uma cadeia quebrada de cópias de segurança incrementais.
Por exemplo, se contém a cópia de segurança completa, o primeiro incremental e a terceira cópia de segurança incremental, mas não a segunda cópia de segurança incremental.

> [!NOTE]
> O RestorePolicy está definido como seguro por predefinição.  Isto significa que o `RestoreAsync` API irá falhar com ArgumentException se detetar que a pasta de cópia de segurança contém um estado anterior ou igual ao estado nesta réplica.  `RestorePolicy.Force`pode ser utilizada para ignorar esta verificação de segurança. Isto é especificado como parte da `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Serviço foi eliminado ou perdido
Se um serviço for removido, tem primeiro de recriar o serviço antes dos dados podem ser restaurados.  É importante criar o serviço com a mesma configuração, por ex., particionar o esquema, para que os dados podem ser restaurados de forma totalmente integrada.  Depois do serviço de cópia de segurança, a API de restauro dos dados (`OnDataLossAsync` acima) tem de ser invocado em cada partição deste serviço. Uma forma de alcançar isto é, utilizando `[FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)` em cada partição.  

A partir deste ponto, a implementação é o mesmo que o cenário acima. Cada partição tem de restaurar a mais recente relevante cópia de segurança da loja externa. Uma advertência é que o ID de partição pode agora alterou, uma vez que o tempo de execução cria partição IDs dinamicamente. Assim, o serviço tem de armazenar o nome de serviço e informações de partição adequado para identificar a cópia de segurança mais recente correta para restaurar a partir de cada partição.

> [!NOTE]
> Não é recomendado utilizar `FabricClient.ServiceManager.InvokeDataLossAsync` em cada partição para restaurar a todo o serviço, uma vez que o que poderá danificar o estado do cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados da aplicação danificado
Se a atualização da aplicação recentemente implementado tem um erro, o que poderá provocar danos de dados. Por exemplo, uma atualização da aplicação pode começar a atualizar cada registo de número de telefone num Dictionary fiável com um código de área inválido.  Neste caso, os números de telefone inválido serão replicados, uma vez que o Service Fabric não tem conhecimento de natureza dos dados que está a ser armazenadas.

A primeira coisa a fazer depois de detetar esses um erros egregious que faz com que a danos nos dados é a parar o serviço ao nível da aplicação e, se possível, atualize para a versão de código da aplicação que não tenha o erros.  No entanto, mesmo depois de corrigido o código do serviço, os dados poderão ainda estar danificados e, por conseguinte, os dados poderão ter de ser restaurados.  Nestes casos, poderá não ser suficiente restaurar a cópia de segurança mais recente, uma vez que as cópias de segurança mais recentes também poderão estar danificadas.  Assim, tem de localizar a última cópia de segurança foi feita antes dos dados obteve danificados.

Se não tem a certeza de que as cópias de segurança estão danificadas, pode implementar um novo cluster de Service Fabric e restaurar as cópias de segurança de partições afetadas, tal como o acima "Deleted ou perdido de serviço" cenário.  Para cada partição, iniciar a restaurar as cópias de segurança a partir da mais recente para o menor. Depois de encontrar uma cópia de segurança que não tenha a existência de danos, mover/eliminar todas as cópias de segurança desta partição que foram mais recentes (que essa cópia de segurança). Repita este processo para cada partição. Agora, quando `OnDataLossAsync` denomina-se na partição no cluster de produção, a última cópia de segurança encontrada no arquivo externo será um selecionado pelo processo de acima.

Agora, os passos "Deleted ou perdido de serviço" secção pode ser utilizada para restaurar o estado do serviço para o estado antes do código buggy danificado o estado.

Tenha em atenção que:

  - Quando restaurar, há a possibilidade de que a cópia de segurança seja restaurada é mais antiga do que o estado da partição antes dos dados foi perdidos. Por este motivo, deve restaurar apenas como último recurso para recuperar a maior quantidade de dados possível.
  - A cadeia que representa o caminho da pasta de cópia de segurança e os caminhos de ficheiros para a pasta de cópia de segurança pode ser superior a 255 carateres, consoante o caminho de variável FabricDataRoot e o comprimento do nome de tipo de aplicação. Isto pode causar alguns métodos de .NET, tal como `Directory.Move`, para acionar o `PathTooLongException` exceção. É uma solução para chamar diretamente APIs de kernel32, como `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Cópia de segurança e restauro Reliable Actors


Reliable Actors Framework for criada sobre Reliable Services. ActorService que aloja o actor(s) é um serviço fiável com monitorização de estado. Por conseguinte, todas as cópias de segurança e restauro funcionalidades disponíveis no Reliable Services estão também disponível para Reliable Actors (exceto os comportamentos que são específico do fornecedor de estado). Uma vez que as cópias de segurança serão efetuadas numa base por partição, Estados de todos os atores nessa partição serão efetuadas cópias de (e restauro é semelhante e irá acontecer numa base por partição). Para efetuar cópia de segurança/restauro, o proprietário do serviço deve criar uma classe de serviço de atores personalizado que derive da classe de ActorService e, em seguida, fazer cópia de segurança/restauro, semelhante a Reliable Services, tal como descrito acima nas secções anteriores.

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo)
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

Quando cria uma classe de serviço de atores personalizado, tem de registar que também ao registar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O fornecedor de estado predefinido dos Reliable Actors é `KvsActorStateProvider`. Cópia de segurança incremental não está ativada por predefinição para `KvsActorStateProvider`. Pode ativar a cópia de segurança incremental criando `KvsActorStateProvider` com a definição adequada no respetivo construtor e passou-a para o construtor ActorService conforme ilustrado no fragmento de código seguinte:

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

Depois de cópia de segurança incremental foi ativada, efetuando uma cópia de segurança incremental pode falhar com FabricMissingFullBackupException para uma das seguintes razões e terá de tomar um completo cópia de segurança antes de tirar backup(s) incrementais:

  - A réplica nunca realizou uma cópia de segurança completa, uma vez que ficou primário.
  - Alguns dos registos de registo foram truncados desde a última cópia de segurança foi feita.

Quando a cópia de segurança incremental é ativada, `KvsActorStateProvider` não utiliza a memória intermédia circular para gerir os respetivos registos de registo e periodicamente trunca-lo. Se nenhuma cópia de segurança estiver atribuída ao utilizador durante um período de 45 minutos, o sistema trunca automaticamente os registos de registo. Este intervalo pode ser configurado com a especificação `logTrunctationIntervalInMinutes` no `KvsActorStateProvider` construtor (semelhante ao ativar a cópia de segurança incremental). Também poderão obter truncados os registos de registo, se a réplica primária necessita para criar outra réplica mediante o envio de todos os respetivos dados.

Ao efetuar o restauro a partir de uma cadeia de cópia de segurança, semelhante a Reliable Services, o BackupFolderPath deve conter subdiretórios com um subdiretório com cópia de segurança completa e outros sub-diretórios que contêm incremental backup(s). A API de restauro irá gerar FabricException com a mensagem de erro apropriada se a validação da cadeia de cópia de segurança falhar. 

> [!NOTE]
> `KvsActorStateProvider`Atualmente, ignora a opção RestorePolicy.Safe. Suporte para esta funcionalidade está a ser planeado a uma versão futura.
> 

## <a name="testing-backup-and-restore"></a>Testar a cópia de segurança e restauro
É importante certificar-se de que os dados críticos está a ser efetuados e podem ser restaurados a partir de. Isto pode ser feito ao invocar o `Start-ServiceFabricPartitionDataLoss` cmdlet do PowerShell que pode induce perda de dados numa determinada partição para testar se os dados de cópia de segurança e restaurar a funcionalidade para o serviço está a funcionar conforme esperado.  Também é possível invocar a perda de dados e restaurar a partir de que o evento, bem como programaticamente.

> [!NOTE]
> Pode encontrar uma implementação de exemplo da cópia de segurança e restaurar a funcionalidade na aplicação Web de referência no GitHub. Consulte o `Inventory.Service` serviço para obter mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Os bastidores: obter mais detalhes sobre a cópia de segurança e restauro
Eis algumas obter mais detalhes sobre a cópia de segurança e restauro.

### <a name="backup"></a>Cópia de segurança
O Gestor de estado fiável fornece a capacidade de criar cópias de segurança sem bloquear qualquer leitura ou operações de escrita. Para tal, utiliza a um mecanismo de persistência de registo e ponto de verificação.  O Gestor de estado fiável demora difusa pontos de verificação (simples) em determinados pontos relieve pressão do registo de transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é denominado, o Gestor de estado fiável dá instruções ao todos os objetos fiáveis para copiar os ficheiros de ponto de verificação mais recentes para uma pasta de cópia de segurança local.  Em seguida, o Gestor de estado fiável copia todos os registos a partir do ponteiro"Iniciar" para o ponto de registo mais recente para a pasta de cópia de segurança.  Uma vez que todos os registos de registo até o registo mais recente estão incluídos na cópia de segurança e o Gestor de estado fiável preserva registo escrita antecipada, o Gestor de estado fiável garante que todas as transações que são consolidar (`CommitAsync` devolveu com êxito ) estão incluídos na cópia de segurança.

Qualquer transação que consolida após `BackupAsync` EnableNotification for chamada pode ou não estar na cópia de segurança.  Assim que tiverem sido povoada na pasta de cópia de segurança local pela plataforma do (ou seja, cópia de segurança local é concluída, o tempo de execução), é invocada chamada de retorno de cópia de segurança do serviço.  Esta chamada de retorno é responsável por mover a pasta de cópia de segurança para uma localização externa, tais como o Storage do Azure.

### <a name="restore"></a>Restauro
O Gestor de estado fiável fornece a capacidade de restaurar a partir de uma cópia de segurança utilizando o `RestoreAsync` API.  
O `RestoreAsync` método no `RestoreContext` pode ser chamado dentro de apenas o `OnDataLossAsync` método.
Bool devolvido pelo `OnDataLossAsync` indica se o serviço de restaurar o estado de uma origem externa.
Se o `OnDataLossAsync` devolve true, Service Fabric vão recriar a todos os outros réplicas deste primário. Service Fabric assegura que as réplicas que irão receber `OnDataLossAsync` chamar primeiro transição para a função primária, mas não são concedidos ler Estado ou escrever o estado.
Isto implica que para os implementadores de StatefulService `RunAsync` não será chamada até `OnDataLossAsync` for concluído com êxito.
Em seguida, `OnDataLossAsync` será invocado principal nova.
Até que um serviço concluir esta API com êxito (devolvendo true ou false) e termina a reconfiguração relevante, a API irá manter a ser chamada um de cada vez.

`RestoreAsync`primeiro ignora todos os Estados existente em que foi chamado na réplica primária.  
Em seguida, o Gestor de estado fiável cria todos os objetos fiáveis existem na pasta de cópia de segurança.  
Em seguida, os objetos fiáveis são deu instruções ao restaurar a partir os respetivos pontos de verificação na pasta de cópia de segurança.  
Por fim, o Gestor de estado fiável recupera o suas próprias estado a partir dos registos de registo na pasta de cópia de segurança e efetua a recuperação.  
Como parte do processo de recuperação, as operações a partir de "ponto de partida" com os registos de consolidação na pasta de cópia de segurança são reproduzidas para os objetos fiáveis.  
Este passo garante que o estado recuperado é consistente.

## <a name="next-steps"></a>Passos seguintes
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
  - [Notificações de serviços fiáveis](service-fabric-reliable-services-notifications.md)
  - [Configuração de serviços fiável](service-fabric-reliable-services-configuration.md)
  - [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

