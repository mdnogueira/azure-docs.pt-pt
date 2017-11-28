---
title: "Melhores práticas para a sincronização de dados de SQL do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba mais sobre as melhores práticas para configurar e executar a sincronização de dados de SQL do Azure (pré-visualização)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 1c8ad4b318d52b5cb6af284b3304cfa7ad35522b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Melhores práticas para a sincronização de dados do SQL Server (pré-visualização) 

Este artigo descreve as melhores práticas para a sincronização de dados SQL do Azure (pré-visualização).

Para obter uma descrição geral da sincronização de dados do SQL Server (pré-visualização), consulte [sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Segurança e fiabilidade

### <a name="client-agent"></a>Agente do cliente

-   Instale o agente de cliente utilizando a conta com menos privilégios de utilizador que tenha acesso de serviço de rede.  
-   Instale o agente de cliente num computador que não seja de computador do SQL Server no local.  
-   Não registe uma base de dados no local com mais do que um agente.    
    -   Evitar esta situação, mesmo se estiver a sincronizar a tabelas diferentes para grupos de sincronização diferentes.  
    -   Registar uma base de dados no local com múltiplos desafios de utilizadores constitui de agentes de cliente quando elimina um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de base de dados com menos privilégios necessários

-   **Para a configuração de sincronização**. Criar/Alter Table; Alterar a base de dados; Criar o procedimento; Selecione / Alter esquema; Crie o tipo definido pelo utilizador.

-   **Para sincronização em curso**. Selecione / inserir / atualizar / eliminar em tabelas que estão selecionadas para sincronização e sincronizar metadados e o controlo de tabelas; Uma permissão de execução em procedimentos armazenados criados pelo serviço; A permissão execute em tipos de tabela definido pelo utilizador.

-   **Para o desaprovisionamento**. ALTER na peça de tabelas de sincronização; Selecione / eliminar em tabelas de metadados da sincronização; Controlar a sincronização de tipos definidos pelo utilizador, procedimentos armazenados e tabelas de controlo.

Base de dados SQL do Azure suporta apenas um único conjunto de credenciais. Para realizar estas tarefas dentro desta restrição, considere as seguintes opções:

-   Alterar as credenciais para diferentes fases (por exemplo, *credentials1* para a configuração e *credentials2* para em curso).  
-   Altere a permissão das credenciais (ou seja, altere a permissão depois de configurar a sincronização).

## <a name="setup"></a>Configurar

### <a name="database-considerations-and-constraints"></a>Considerações de base de dados e as restrições

#### <a name="sql-database-instance-size"></a>Tamanho da instância de base de dados SQL

Quando cria uma nova instância de base de dados do SQL Server, defina o tamanho máximo que sempre é maior do que a base de dados que implementa. Se não definir o tamanho máximo para maior do que a base de dados implementado, a sincronização falha. Embora a sincronização de dados do SQL Server (pré-visualização) não oferecem o aumento automático, pode executar o `ALTER DATABASE` comando para aumentar o tamanho da base de dados depois de terem sido criadas. Certifique-se de que permanecem dentro dos limites de tamanho do instância de base de dados SQL.

> [!IMPORTANT]
> Sincronização de dados do SQL Server (pré-visualização) armazena os metadados adicionais com cada base de dados. Certifique-se de que tem em conta estes metadados ao calcular o espaço necessário. A quantidade de adicionar a sobrecarga está relacionado com a largura das tabelas (por exemplo, as tabelas estreito requerem mais overhead) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a>Considerações de tabela e restrições

#### <a name="selecting-tables"></a>Selecionar tabelas

Não tem de incluir todas as tabelas que se encontrem numa base de dados num grupo de sincronização. As tabelas que incluir no grupo de sincronização afetam os custos e eficiência. Inclua tabelas e as tabelas são dependentes, num grupo de sincronização apenas se as necessidades de negócio requerem.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela num grupo de sincronização tem de ter uma chave primária. O serviço de sincronização de dados do SQL Server (pré-visualização) não é possível sincronizar uma tabela que não tem uma chave primária.

Antes de utilizar a sincronização de dados do SQL Server (pré-visualização) na produção, teste o desempenho da sincronização iniciais e contínuas.

### <a name="provisioning-destination-databases"></a>Aprovisionamento de bases de dados de destino

Pré-visualização de sincronização de dados do SQL Server (pré-visualização) fornece autoprovisioning de base de dados básica.

Esta secção descreve as limitações de aprovisionamento na sincronização de dados do SQL Server (pré-visualização).

#### <a name="autoprovisioning-limitations"></a>Limitações de Autoprovisioning

Sincronização de dados do SQL Server (pré-visualização) tem as seguintes limitações no autoprovisioning:

-   Selecione apenas as colunas que são criadas na tabela de destino.  
    Todas as colunas que não fazem parte do grupo de sincronização não estão aprovisionadas nas tabelas de destino.
-   Os índices são criados apenas para colunas selecionadas.  
    Se o índice de tabela de origem tem colunas que não fazem parte do grupo de sincronização, os índices não são aprovisionados nas tabelas de destino.  
-   Os índices em colunas do tipo XML não são aprovisionados.  
-   Restrições de verificação não são aprovisionadas.  
-   Não são aprovisionados existentes acionadores em tabelas de origem.  
-   As vistas e procedimentos armazenados não são criados na base de dados de destino.

#### <a name="recommendations"></a>Recomendações

-   Utilize a capacidade de autoprovisioning de sincronização de dados do SQL Server (pré-visualização) apenas quando está a tentar terminar o serviço.  
-   Para produção, Aprovisione o esquema de base de dados.

### <a name="locate-hub"></a>Onde localizar a base de dados do hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário empresarial para a nuvem

Para minimizar a latência, manter a base de dados do hub próximo de concentração maior de tráfego de base de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para a nuvem

-   Quando todas as bases de dados num grupo de sincronização estão no Centro de dados de um, o hub deve estar localizado no mesmo datacenter. Esta configuração reduz a latência e o custo de transferência de dados entre centros de dados.
-   Quando as bases de dados num grupo de sincronização estão em vários datacenters, hub deve estar localizado no mesmo centro de dados como a maioria das bases de dados e tráfego de base de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplicam-se as diretrizes anteriores a configurações de grupo de sincronização complexos, tais como as que são uma combinação de cenários de empresa para a nuvem e nuvem para a nuvem.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Evitar lenta e dispendiosa sincronização inicial

Nesta secção, vamos discutir a sincronização inicial de um grupo de sincronização. Saiba como ajudar a evitar uma sincronização inicial de demorar mais tempo e a ser mais dispendioso do que o necessário.

#### <a name="how-initial-sync-works"></a>Funciona como inicial de sincronização

Quando cria um grupo de sincronização, começar a utilizar dados na base de dados apenas um. Se tiver dados nas bases de dados de vários, sincronização de dados do SQL Server (pré-visualização) trata cada linha como um conflito que tem de ser resolvidos. Esta resolução de conflitos faz com que a sincronização inicial ir lentamente. Se tiver dados em várias bases de dados, a sincronização inicial pode demorar entre vários dias e vários meses, dependendo do tamanho de base de dados.

Se as bases de dados estiverem em datacenters diferentes, cada linha tem de se entre centros de dados diferentes. Isto aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se for possível, começar a utilizar dados em apenas uma das bases de dados do grupo de sincronização.

### <a name="design-to-avoid-synchronization-loops"></a>Conceção para evitar os ciclos de sincronização

Um ciclo de sincronização ocorre quando existem referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração de uma base de dados é ter e forma circular replicada através de bases de dados no grupo de sincronização.   

Certifique-se de que evitar os ciclos de sincronização, pois causar degradação do desempenho e poderá aumentar significativamente os custos.

### <a name="handling-changes-that-fail-to-propagate"></a>Alterações que não obedeçam a propagação

#### <a name="reasons-that-changes-fail-to-propagate"></a>Por motivos que não obedeçam a alterações à propagação

As alterações poderão falhar a propagação de um dos seguintes motivos:

-   Incompatibilidade de esquema/datatype.
-   A inserir nulo nas colunas não nulas.
-   Violação de restrições de chave externa.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando não propagar as alterações?

-   Sincronizar mostra de grupo que faz parte de um **aviso** estado.
-   Os detalhes são apresentados no Visualizador de registo de IU do portal.
-   Se o problema foi resolvido não 45 dias, a base de dados fica desatualizada.

> [!NOTE]
> Estas alterações nunca se propagarem. A única forma de recuperar este cenário é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitorize o sincronização grupo e a base de dados de estado de funcionamento regularmente através da interface de portal e do registo.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitar Desatualizadas bases de dados e grupos de sincronização

Um grupo de sincronização ou uma base de dados num grupo de sincronização pode ficar desatualizada. Quando o estado de um grupo de sincronização é **Desatualizadas**, deixa de funcionar. Quando o estado de uma base de dados é **Desatualizadas**, dados poderão perder-se. É melhor evitar este cenário em vez de tentar recuperar.

#### <a name="avoid-out-of-date-databases"></a>Evitar bases de dados desatualizados

Estado de uma base de dados está definido como **Desatualizadas** quando foi offline de 45 dias ou mais. Para evitar um **Desatualizadas** estado numa base de dados, certifique-se de que nenhuma das bases de dados está offline para 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar a grupos de sincronização desatualizados

Estado de um grupo de sincronização estiver definido como **Desatualizadas** quando qualquer alteração no grupo de sincronização a falhar a ser propagada para o resto do grupo de sincronização de 45 dias ou mais. Para evitar um **Desatualizadas** estado a um grupo de sincronização, verificar regularmente o registo de histórico do grupo de sincronização. Certifique-se de que todos os conflitos são resolvidos e que as alterações sejam propagadas de com êxito em toda as bases de dados do grupo de sincronização.

Um grupo de sincronização poderão falhar para se candidatar a uma alteração de um destes motivos:

-   Incompatibilidade de esquema entre as tabelas.
-   Incompatibilidade de dados entre as tabelas.
-   A inserir uma linha com um valor nulo numa coluna que não permite valores nulos.
-   Atualizar uma linha com um valor que viola uma restrição de chave externa.

Para impedir que os grupos de sincronização Desatualizadas:

-   Atualize o esquema para permitir que os valores que estão contidos nas linhas com falhas.
-   Atualize os valores de chaves externos para incluir os valores que estão contidos nas linhas com falhas.
-   Atualize os valores de dados na linha falhado para que sejam compatíveis com o esquema ou chaves externas numa base de dados de destino.

### <a name="avoid-deprovisioning-issues"></a>Evitar problemas de desaprovisionamento

Em algumas circunstâncias, a anulação do registo uma base de dados com um agente do cliente pode causar a sincronização a falhar.

#### <a name="scenario"></a>Cenário

1. Grupo de sincronização A foi criado utilizando uma instância de base de dados do SQL Server e no local do SQL Server da base de dados, que está associado ao agente local 1.
2. A mesma base de dados no local está registado no agente local 2 (este agente não está associado a nenhum grupo de sincronização).
3. A anulação do registo da base de dados no local do agente local 2 remove o controlo e tabelas de metadados para sincronizar grupo A para a base de dados no local.
4. Sincronização de grupo uma falha de operações, com o erro: "a operação atual não foi possível concluir porque a base de dados não está aprovisionada para sincronização ou não dispõe de permissões para as tabelas de configuração de sincronização."

#### <a name="solution"></a>Solução

Para evitar este cenário, não registe uma base de dados com mais do que um agente.

Para recuperar este cenário:

1. Remova a base de dados de cada grupo de sincronização que pertence.  
2. Adicione a base de dados novamente em cada grupo de sincronização que removeu a.  
3. Implemente a cada grupo de sincronização afetado (esta ação Aprovisiona a base de dados).  

### <a name="modifying-your-sync-group"></a>Modificar um grupo de sincronização

Não tente remover uma base de dados de um grupo de sincronização e, em seguida, edite o grupo de sincronização sem primeiro uma implementação de alterações.

Em vez disso, remova primeiro uma base de dados de um grupo de sincronização. Em seguida, implementar a alteração e aguarde desaprovisionamento para concluir. Quando desaprovisionamento estiver concluída, pode editar o grupo de sincronização e implementar as alterações.

Se tentar remover uma base de dados e, em seguida, editar um grupo de sincronização sem primeiro uma implementação de alterações, um ou a outra operação falha. Interface do portal pode ficar inconsistente. Se isto acontecer, atualize a página para restaurar o estado correto.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a sincronização de dados do SQL Server (pré-visualização), consulte:

-   [Sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md)
-   [Configurar a sincronização de dados de SQL do Azure (pré-visualização)](sql-database-get-started-sql-data-sync.md)
-   [Monitor SQL do Azure de sincronização de dados (pré-visualização) com a análise de registos do OMS](sql-database-sync-monitor-oms.md)
-   [Resolver problemas com a sincronização de dados de SQL do Azure (pré-visualização)](sql-database-troubleshoot-data-sync.md)  
-   Conclua os exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server (pré-visualização):  
    -   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Transferir a documentação da API de REST de sincronização de dados do SQL Server (pré-visualização)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados SQL](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
