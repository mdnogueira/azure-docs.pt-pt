---
title: "Melhores práticas para a sincronização de dados SQL do Azure | Microsoft Docs"
description: "Obter as melhores práticas para configurar e executar a sincronização de dados SQL do Azure"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: d23bd186300d451186dd4f3644290cb4178417a2
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Melhores práticas para a sincronização de dados do SQL Server (pré-visualização) 

Este artigo descreve as melhores práticas para a sincronização de dados do SQL Server (pré-visualização).

Para obter uma descrição geral da sincronização de dados do SQL Server, consulte [sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Segurança e fiabilidade

### <a name="client-agent"></a>Agente do cliente

-   Instale o agente de cliente com a conta de privilégios, pelo menos, acesso de serviço de rede.

-   Recomenda-se o agente do cliente é instalado em separado um computador do computador do SQL Server no local.

-   Não é registe uma base de dados no local com mais do que um agente.

-   Mesmo que a sincronizar a tabelas diferentes para grupos de sincronização diferentes.

-   Registar uma base de dados no local com múltiplos desafios de utilizadores constitui de agentes de cliente aquando da eliminação de um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privilege"></a>Contas de base de dados com menos privilégios necessários

-   **Para a configuração de sincronização**. Criar/Alter Table Alter da base de dados, a criar o procedimento, selecionar / alterar o esquema, criar o tipo definido pelo utilizador.

-   **Para sincronização em curso**. Selecione / inserir / atualizar / eliminar em tabelas selecionadas para sincronização e sincronizar metadados e tabelas de controlo, a permissão executar em procedimentos armazenados criados pelo serviço, permissão executar em tipos de tabela definido pelo utilizador.

-   **Para anular o aprovisionamento**. Falha de ALTER na parte de tabelas da sincronização, selecione / eliminar em tabelas de metadados de sincronização, o controlo da sincronização de controlo de controlo sincronização tabelas, procedimentos armazenados e tipos definidos pelo utilizador.

**Como pode utilizar estas informações quando existe apenas uma única credencial para uma base de dados no grupo de sincronização?**

-   Alterar as credenciais para diferentes fases (por exemplo, credential1 para a configuração e credential2 para em curso).

-   Altere a permissão das credenciais (ou seja, altere a permissão depois de configurar a sincronização).

## <a name="locate-hub"></a>Onde localizar a base de dados do Hub

### <a name="enterprise-to-cloud-scenario"></a>Cenário empresarial para a nuvem

Para minimizar a latência, manter a base de dados do hub próximo de concentração maior de tráfego de base de dados do grupo de sincronização.

### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para a nuvem

-   Quando todas as bases de dados num grupo de sincronização estão no Centro de dados de um, o hub deve estar localizado no mesmo centro de dados. Esta configuração reduz a latência e o custo de transferência de dados entre centros de dados.

-   Quando as bases de dados num grupo de sincronização estão em múltiplos centros de dados, o hub deve estar localizado no mesmo centro de dados, tal como a maioria das bases de dados e tráfego de base de dados.

### <a name="mixed-scenarios"></a>Cenários mistos

Aplicam-se as diretrizes anteriores para configurações mais complexas de grupo de sincronização.

## <a name="database-considerations-and-constraints"></a>Considerações de base de dados e as restrições

### <a name="sql-database-instance-size"></a>Tamanho da instância de base de dados SQL

Quando cria uma nova instância de base de dados do SQL Server, defina o tamanho máximo que sempre é maior do que a base de dados que implementa. Se definir o tamanho máximo maior do que a base de dados implementado, a sincronização falhar. Embora não haja nenhum crescimento automático - pode efetuar ALTER DATABASE para aumentar o tamanho da base de dados depois de terem sido criadas. Certifique-se de que permanecem dentro dos limites de tamanho do instância de base de dados SQL.

> [!IMPORTANT]
> Sincronização de dados do SQL Server armazena os metadados adicionais com cada base de dados. Lembre-se de que conta para estes metadados ao calcular o espaço necessário. A quantidade de adicionar a sobrecarga é regida pela largura das tabelas (por exemplo, as tabelas estreito requerem mais overhead) e a quantidade de tráfego.

## <a name="table-considerations-and-constraints"></a>Considerações de tabela e restrições

### <a name="selecting-tables"></a>Selecionar tabelas

Nem todas as tabelas numa base de dados têm de ser um [grupo de sincronização](#sync-group). A seleção dos que tabelas para incluir no grupo de sincronização e de que pretende excluir (ou incluir num grupo de sincronização diferentes) pode afetar os custos e eficiência. Inclua apenas nessas tabelas de um grupo de sincronização que necessidades da empresa a pedido as tabelas após a que são dependentes.

### <a name="primary-keys"></a>Chaves primárias

Cada tabela num grupo de sincronização tem de ter uma chave primária. O serviço de sincronização de dados do SQL Server (pré-visualização) não é possível sincronizar nenhuma tabela que não tem uma chave primária.

Antes de a disponibilizar para produção, teste o desempenho da sincronização iniciais e contínuas.

## <a name="provisioning-destination-databases"></a>Aprovisionamento de bases de dados de destino

Pré-visualização de sincronização de dados do SQL Server (pré-visualização) fornece o aprovisionamento automático de base de dados básica.

Esta secção descreve as limitações de sincronização de dados do SQL Server do aprovisionamento (pré-visualização).

### <a name="auto-provisioning-limitations"></a>Auto limitações de aprovisionamento

Seguem-se as limitações de aprovisionamento automático de sincronização de dados do SQL Server (pré-visualização).

-   Apenas as colunas selecionadas são criadas na tabela de destino.
Assim, se algumas colunas não fazem parte do grupo de sincronização dessas colunas não são aprovisionadas nas tabelas de destino.

-   Os índices são criados apenas para as colunas selecionadas.
Se o índice de tabela de origem tem colunas que não fazem parte do grupo de sincronização os índices não são aprovisionados nas tabelas de destino.

-   Os índices em colunas do tipo XML não são aprovisionados.

-   Restrições de verificação não são aprovisionadas.

-   Não são aprovisionados existentes acionadores em tabelas de origem.

-   As vistas e procedimentos armazenados não estão criados na base de dados de destino.

### <a name="recommendations"></a>Recomendações

-   Utilize a capacidade de aprovisionamento automático apenas para o serviço a tentar.

-   Para produção, deve aprovisionar o esquema de base de dados.

## <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Evitar uma sincronização inicial lenta e dispendiosa

Esta secção descreve a sincronização inicial de um grupo de sincronização e o que pode fazer para evitar uma sincronização inicial demorar mais tempo do que o necessário e custos mais do que devia.

### <a name="how-initial-synchronization-works"></a>Funciona como inicial de sincronização

Quando cria um grupo de sincronização, começar a utilizar dados na base de dados apenas um. Se tiver dados nas bases de dados de vários, sincronização de dados do SQL Server (pré-visualização) trata cada linha como um conflito que tem de resolução. Esta resolução de conflitos faz com que a sincronização inicial ir lentamente, colocar vários dias para vários meses, dependendo do tamanho de base de dados.

Além disso, se as bases de dados estiverem em datacenters diferentes, os custos da sincronização inicial são superiores ao necessário, uma vez que cada linha tem de se deslocar entre centros de dados diferentes.

### <a name="recommendation"></a>Recomendação

Sempre que possível começar a utilizar dados em apenas uma das bases de dados do grupo de sincronização.

## <a name="design-to-avoid-synchronization-loops"></a>Conceção para evitar os ciclos de sincronização

Um ciclo de sincronização resulta quando existem referências circulares dentro de um grupo de sincronização para que cada alteração de uma base de dados é replicada através de bases de dados no grupo de sincronização de forma circular e ter. Pretender evitar os ciclos de sincronização, tal como degradar o desempenho e pode aumentar significativamente os custos.

## <a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitar Desatualizadas bases de dados e grupos de sincronização

Um grupo de sincronização ou uma base de dados dentro de um grupo de sincronização pode tornar-se desatualizados. Quando o estado de um grupo de sincronização é "desatualizado", deixa de funcionar. Quando o estado de uma base de dados é "desatualizado", os dados podem ser perdidos. É melhor evitar nestas situações, em vez de ter que recuperar dos mesmos.

### <a name="avoid-out-of-date-databases"></a>Evitar bases de dados desatualizados

Estado de uma base de dados está definido como desatualizado quando tiver sido offline de 45 dias ou mais. Evite o estado desatualizado numa base de dados, garantindo que nenhuma das bases de dados está offline para 45 dias ou mais.

### <a name="avoid-out-of-date-sync-groups"></a>Evitar a grupos de sincronização desatualizados

Estado de um grupo de sincronização está definido como desatualizado quando qualquer alteração num grupo de sincronização não é possível propagar no resto do grupo de sincronização de 45 dias ou mais. Evite o estado desatualizado sobre um grupo de sincronização ao verificar regularmente o registo de histórico do grupo de sincronização. Certifique-se de que são resolvidos os conflitos de todas as e as alterações propagadas com êxito em toda as bases de dados do grupo de sincronização.

Um grupo de sincronização poderão falhar aplicar uma alteração de motivos incluem:

-   Incompatibilidade de esquema entre as tabelas.

-   Incompatibilidade de dados entre as tabelas.

-   A inserir uma linha com um valor nulo numa coluna que não permite valores nulos.

-   Atualizar uma linha com um valor que viola uma restrição de chave externa.

Pode impedir que os grupos de sincronização Desatualizadas por:

-   Atualize o esquema para permitir que os valores contidos nas linhas com falhas.

-   Atualize os valores de chaves externos para incluir os valores contidos nas linhas com falhas.

-   Atualize os valores de dados na linha da falha para ser compatível com o esquema ou chaves externas numa base de dados de destino.

## <a name="avoid-deprovisioning-issues"></a>Evitar problemas de desaprovisionamento

Em determinadas circunstâncias, a anulação do registo uma base de dados com um agente do cliente pode causar sincronizações falhar.

### <a name="scenario"></a>Cenário

1. Grupo de sincronização A foi criado com uma instância de base de dados do SQL Server e no local do SQL Server da base de dados, que está associado ao agente local 1.

2. A mesma base de dados no local está registado no agente local 2 (este agente não está associado a nenhum grupo de sincronização).

3. A anulação do registo da base de dados no local do agente local 2 remove as tabelas de controlo/metadados para o grupo de sincronização A para a base de dados no local.

4. Agora, as operações de um grupo sincronização falharam com o seguinte erro-"a operação atual não foi possível concluir porque a base de dados não está aprovisionada para sincronização ou não dispõe de permissões para as tabelas de configuração de sincronização."

### <a name="solution"></a>Solução

Evite a situação inteiramente registando nunca uma base de dados com mais do que um agente.

Para recuperar desta situação:

1. Remova a base de dados de cada grupo de sincronização pertence.

2. Adicione a base de dados novamente em cada grupo de sincronização que apenas removeu a.

3. Implemente a cada grupo de sincronização afetado (que Aprovisiona a base de dados).

## <a name="handling-changes-that-fail-to-propagate"></a>Processamento de alterações que não obedeçam a propagação

### <a name="reasons-that-changes-fail-to-propagate"></a>Por motivos que não obedeçam a alterações à propagação

Podem efetuar alterações a propagação por muitos motivos. Algumas causas seria:

-   Incompatibilidade de esquema/Datatype.

-   A tentar inserir nulo nas colunas não nulas.

-   Violação de restrições de chave externa.

### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando não propagar as alterações?

-   Grupo de sincronização mostra faz parte de um Estado de aviso.

-   Os detalhes são no Visualizador de início de sessão do Portal da IU.

-   Se o problema foi resolvido não 45 dias, a base de dados fica desatualizada.

> [!NOTE]
> Estas alterações nunca se propagarem. A única forma de recuperar é recriar o grupo de sincronização.

### <a name="recommendation"></a>Recomendação

Monitorize o estado de funcionamento do grupo de sincronização e a base de dados regularmente através da interface de portal e do registo.

## <a name="modifying-your-sync-group"></a>Modificar um grupo de sincronização

Não tente remover uma base de dados de um grupo de sincronização e, em seguida, edite o grupo de sincronização sem primeiro uma implementação de alterações.

Em primeiro lugar, remova uma base de dados de um grupo de sincronização. Em seguida, implementar a alteração e aguarde aprovisionamento automatizados. para concluir. Depois de concluída a esta operação, pode editar o grupo de sincronização e implementar as alterações.

Se tentar remover uma base de dados e, em seguida, edite um grupo de sincronização sem a implementar primeiro as alterações, um ou a outra operação falha e pode obter a interface de portal num estado inconsistente. Neste caso, pode atualizar a página para restaurar o estado correto.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a sincronização de dados do SQL Server, consulte:

-   [Sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   [Configurar a sincronização de dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
-   [Monitor sincronização de dados SQL do Azure com a análise de registos do OMS](sql-database-sync-monitor-oms.md)
-   [Resolver problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server:
    -   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Transferir a documentação da API de REST de sincronização de dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados do SQL Server](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
