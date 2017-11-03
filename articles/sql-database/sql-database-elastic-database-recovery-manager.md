---
title: "Utilizando o Gestor de recuperação para corrigir problemas de mapa de partições horizontais | Microsoft Docs"
description: "Utilize a classe de RecoveryManager para resolver problemas com o ID de partição horizontal maps"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 45520ca3-6903-4b39-88ba-1d41b22da9fe
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: ddove
ms.openlocfilehash: 967a64fa41a9f9ff16ce173b76231052b22cda99
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais
O [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) classe fornece aplicações ADO.Net a capacidade de detetar e corrigir quaisquer inconsistências entre o mapa de partições horizontais global (GSM) e o mapa de partições horizontais local (LSM) num ambiente de base de dados em partição horizontal facilmente. 

O GSM e LSM controlam o mapeamento de cada base de dados num ambiente em partição horizontal. Ocasionalmente, uma quebra de ocorre entre o GSM e o LSM. Nesse caso, utilize a classe de RecoveryManager para detetar e reparar a quebra de.

A classe de RecoveryManager faz parte o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md). 

![Mapa de partições horizontais][1]

Para definições de prazo, consulte [Glossário de ferramentas de base de dados elástica](sql-database-elastic-scale-glossary.md). Para compreender como o **ShardMapManager** é utilizado para gerir os dados numa solução em partição horizontal, consulte [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Porquê utilizar o Gestor de recuperação?
Num ambiente a base de dados, há um inquilino por base de dados e muitas bases de dados por servidor. Também pode haver vários servidores no ambiente. Cada base de dados está mapeado no mapa de partições horizontais, pelo que podem ser encaminhadas chamadas para o servidor correto e a base de dados. São controlados bases de dados de acordo com um **chave de fragmentação**, e cada partição horizontal tem atribuído um **intervalo de valores chaves**. Por exemplo, uma chave de fragmentação pode representar os nomes de cliente de "D" para "f". O mapeamento de todas as partições horizontais (também conhecido como bases de dados) e os intervalos de mapeamento estão contidos no **mapa de partições horizontais global (GSM)**. Cada base de dados contém também um mapa dos intervalos contidos no ID de partição horizontal que é conhecido como o **mapa de partições horizontais local (LSM)**. Quando uma aplicação se liga a um ID de partição horizontal, o mapeamento é colocado em cache com a aplicação para recuperação rápida. O LSM é utilizado para validar os dados em cache. 

O GSM e LSM podem tornar-se dessincronizado pelos seguintes motivos:

1. A eliminação de uma partição horizontal cujo intervalo é believed para deixará de estar em utilização ou mudar o nome de um ID de partição horizontal. Eliminar um ID de partição horizontal resulta num **órfã mapeamento de partições horizontais**. Da mesma forma, uma base de dados cujo nome foi alterado pode causar um mapeamento de partições horizontais órfão. Consoante o objetivo da alteração, o ID de partição horizontal poderá ter de ser removido ou a localização de partição horizontal tem de ser atualizado. Para recuperar uma base de dados eliminada, consulte [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md).
2. Ocorre um evento de ativação pós-falha de georreplicação. Para continuar, um tem de atualizar o nome do servidor e o nome de base de dados do Gestor de mapa de partições horizontais na aplicação e, em seguida, atualize os detalhes de mapeamento de partições horizontais para todas as partições horizontais num mapa de partições horizontais. Se houver uma ativação pós-falha georreplicação, essa lógica de recuperação deve ser automatizada dentro do fluxo de trabalho de ativação pós-falha. Automatização de ações de recuperação permite uma capacidade de gestão sem incómodos para ativar a georreplicação bases de dados e evita ações humanos manuais. Para saber mais sobre as opções para recuperar uma base de dados, se existir uma falha do Centro de dados, consulte [continuidade do negócio](sql-database-business-continuity.md) e [recuperação após desastre](sql-database-disaster-recovery.md).
3. Um ID de partição horizontal ou a base de dados ShardMapManager é restaurado para um anterior ponto no tempo. Para saber mais sobre o ponto de recuperação de tempo utilizando cópias de segurança, consulte [recuperação utilizando cópias de segurança](sql-database-recovery-using-backups.md).

Para obter mais informações sobre as ferramentas do Azure SQL da base de dados elásticas da base de dados, georreplicação e restauro, consulte o seguinte: 

* [Descrição geral: Nuvem de recuperação de desastre de base de dados e de continuidade do negócio com a base de dados SQL](sql-database-business-continuity.md) 
* [Começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md)  
* [Gestão de ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Obter RecoveryManager a partir de um ShardMapManager
O primeiro passo é criar uma instância de RecoveryManager. O [GetRecoveryManager método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) devolve o Gestor de recuperação para o atual [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) instância. Para resolver eventuais inconsistências verificadas no mapa de partições horizontais, tem de obter primeiro o RecoveryManager para o mapa de partições horizontais específico. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

Neste exemplo, o RecoveryManager é inicializada do ShardMapManager. ShardMapManager que contém um ShardMap também já foi inicializado. 

Uma vez que este código de aplicação manipula o mapa de partições horizontais próprio, as credenciais utilizadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser as credenciais que têm permissões de leitura e escrita na base de dados GSM referenciada pela ligação cadeia. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir as ligações para o encaminhamento de dados dependentes. Para obter mais informações, consulte [utilizando credenciais no cliente de base de dados elástica](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Remover um ID de partição horizontal do ShardMap depois de um ID de partição horizontal é eliminado
O [DetachShard método](https://msdn.microsoft.com/library/azure/dn842083.aspx) desanexa o partições horizontais indicado do mapa de partições horizontais e elimina mapeamentos associados com o ID de partição horizontal.  

* O parâmetro de localização é a localização de partições horizontais, especificamente o nome do servidor e o nome de base de dados, das partições horizontais ser desanexado. 
* O parâmetro shardMapName é o nome do mapa de partições horizontais. Isto apenas é necessário quando várias partições horizontais maps são geridas pelo mesmo gestor de mapa de partições horizontais. Opcional. 


> [!IMPORTANT]
> Utilize esta técnica apenas se tiver determinado que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificar os dados para o intervalo que está a ser movido, pelo que é melhor incluir verificações no seu código.
>

Neste exemplo remove shards o mapa de partições horizontais. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

O mapa de partições horizontais reflete a localização de partições horizontais em GSM antes da eliminação das partições horizontais. Porque o ID de partição horizontal foi eliminada, presume-se esta foi intencional, e o intervalo de chave de fragmentação já não está em utilização. Caso contrário, pode executar o restauro de ponto no tempo. Para recuperar o ID de partição horizontal a partir de um ponto no tempo anterior. (Nesse caso, consulte a secção seguinte para detetar inconsistências de partições horizontais.) Para recuperar, consulte [ponto de recuperação de tempo](sql-database-recovery-using-backups.md).

Porque se assume que a eliminação de base de dados foi intencional, a ação de limpeza administrativo final consiste em eliminar a entrada para o ID de partição horizontal no Gestor de mapa de partições horizontais. Isto impede a aplicação de escrita inadvertidamente informações para um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detetar as diferenças de mapeamento
O [DetectMappingDifferences método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) seleciona e devolve um dos mapas de partições horizontais (locais ou global) como origem de truth e reconcilia mapeamentos em ambos os mapas de partições horizontais (GSM e LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* O *localização* Especifica o nome do servidor e o nome de base de dados. 
* O *shardMapName* parâmetro é o nome do mapa de partições horizontais. Isto apenas é necessário se várias partições horizontais maps são geridos pelo mesmo gestor de mapa de partições horizontais. Opcional. 

## <a name="to-resolve-mapping-differences"></a>Para resolver diferenças de mapeamento
O [ResolveMappingDifferences método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) seleciona um dos mapas de partições horizontais (locais ou global) como origem de truth e reconcilia mapeamentos em ambos os mapas de partições horizontais (GSM e LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O *RecoveryToken* parâmetro enumera as diferenças nos mapeamentos entre o GSM e LSM para o ID de partição horizontal específico. 
* O [MappingDifferenceResolution enumeração](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) é utilizado para indicar o método para resolver a diferença entre os mapeamentos de partições horizontais. 
* **MappingDifferenceResolution.KeepShardMapping** é recomendado que quando o LSM contém o mapeamento exata e, por conseguinte, o mapeamento do ID de partição horizontal deve ser utilizado. Isto acontece normalmente o caso se houver uma ativação pós-falha: o ID de partição horizontal agora reside num servidor novo. Uma vez que as partições horizontais primeiro tem de ser removido o GSM (utilizando o método RecoveryManager.DetachShard), um mapeamento já não existe o GSM. Por conseguinte, o LSM deve ser utilizado para restabelecer o mapeamento de partições horizontais.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar um ID de partição horizontal para o ShardMap após o restauro de um ID de partição horizontal
O [AttachShard método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) anexa o ID de partição horizontal fornecido para o mapa de partições horizontais. Em seguida, Deteta quaisquer inconsistências de mapa de partições horizontais e atualiza os mapeamentos para corresponder a partições horizontais at the point of o restauro de partições horizontais. Presume-se que a base de dados também é alterado para refletir o base de dados nome original (antes do ID de partição horizontal foi restaurada), uma vez que o restauro de ponto no tempo está predefinida para uma nova base de dados anexada com o carimbo. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* O *localização* parâmetro é o nome do servidor e o nome de base de dados, das partições horizontais que está a ser ligado. 
* O *shardMapName* parâmetro é o nome do mapa de partições horizontais. Isto apenas é necessário quando várias partições horizontais maps são geridas pelo mesmo gestor de mapa de partições horizontais. Opcional. 

Este exemplo adiciona uma partição horizontal para o mapa de partições horizontais foi recentemente restaurado a partir de um tempo de início de ponto anterior. Uma vez que o ID de partição horizontal (nomeadamente o mapeamento para o ID de partição horizontal no LSM) tiver sido recuperada, é potencialmente inconsistente com a entrada de partições horizontais no GSM. Fora este código de exemplo, o ID de partição horizontal foi recuperada e mudar o nome para o nome da base de dados original. Uma vez que foi restaurada, presume-se que o mapeamento no LSM é o mapeamento fidedigno. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizar localizações de partições horizontais após uma georreplicação-ativação pós-falha (recuperação) das partições horizontais
Se houver uma ativação pós-falha georreplicação, a base de dados secundária é efetuada escrever acessível e torna-se a nova base de dados primária. O nome do servidor e, potencialmente, a base de dados (consoante a configuração), poderá ser diferente do principal original. Por isso as entradas de mapeamento para as partições horizontais na GSM e LSM têm de ser corrigidas. Da mesma forma, se a base de dados for restaurado para um nome diferente ou localização ou para um ponto anterior no tempo, isto poderá provocar inconsistências o Maps de partições horizontais. O Gestor de mapa de partições horizontais processa a distribuição de abrir ligações à base de dados correto. Distribuição baseia-se nos dados existentes o mapa de partições horizontais e o valor da chave de fragmentação que é o destino do pedido de aplicações. Após uma georreplicação-ativação pós-falha, estas informações têm de ser atualizadas com o nome de servidor corretos, o nome de base de dados e o mapeamento de partições horizontais da base de dados recuperada. 

## <a name="best-practices"></a>Melhores práticas
Georreplicação-ativação pós-falha e recuperação são, normalmente, geridas por um administrador da nuvem da aplicação intencionalmente utilizando uma das funcionalidades de continuidade de negócio de bases de dados do Azure SQL de operações. Planeamento de continuidade do negócio requer medidas para se certificar de que as operações de negócio podem continuar sem interrupção, processos e procedimentos. Os métodos disponíveis como parte da classe RecoveryManager deve ser utilizada neste fluxo de trabalho para garantir que o GSM e LSM se mantêm atualizados com base na ação de recuperação executada. Existem cinco passos básicos para assegurar corretamente que o GSM e LSM refletem as informações corretas após um evento de ativação pós-falha. O código de aplicação para executar estes passos pode ser integrado no fluxo de trabalho e ferramentas existentes. 

1. Obter o RecoveryManager o ShardMapManager. 
2. Anular a exposição de partições horizontais antigo do mapa de partições horizontais.
3. Anexe o ID de partição horizontal novo para o mapa de partições horizontais, incluindo a nova localização de partições horizontais.
4. Deteta inconsistências no mapeamento entre o GSM e LSM. 
5. Resolva as diferenças entre o GSM e LSM, tendo em consideração o LSM. 

Neste exemplo efetua os seguintes passos:

1. Remove shards o mapa de partições horizontais refletir localizações de partições horizontais antes do evento de ativação pós-falha.
2. Anexa shards para o mapa de partições horizontais ao refletir as novas localizações de partições horizontais (o parâmetro "Configuration.SecondaryServer" é o novo nome de servidor, mas o mesmo nome de base de dados).
3. Obtém os tokens de recuperação através da deteção de diferenças de mapeamento entre o GSM e LSM para cada partição horizontal. 
4. Resolve as inconsistências, tendo em consideração o mapeamento de LSM de cada ID de partição horizontal. 
   
   ```
   var shards = smm.GetShards(); 
   foreach (shard s in shards) 
   { 
     if (s.Location.Server == Configuration.PrimaryServer) 
   
         { 
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database); 
   
          rm.DetachShard(s.Location); 
   
          rm.AttachShard(slNew); 
   
          var gs = rm.DetectMappingDifferences(slNew); 
   
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png

