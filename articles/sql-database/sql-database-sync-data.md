---
title: "Sincronização de dados SQL do Azure (pré-visualização) | Microsoft Docs"
description: "Esta descrição geral apresenta a sincronização de dados de SQL do Azure (pré-visualização)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 5cf74140969fb354e426c41552d4d73a06c76890
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados do SQL Server (pré-visualização)

Sincronização de dados do SQL Server é um serviço incorporado numa base de dados do SQL do Azure que permite-lhe sincronizar os dados que selecionar bidirecional entre várias bases de dados SQL e instâncias do SQL Server.

Sincronização de dados baseia-se à volta o conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bases de dados que pretende sincronizar.

Um grupo de sincronização tem as seguintes propriedades:

-   O **esquema de sincronização** descreve os dados estão a ser sincronizados.

-   O **direção de sincronização** pode ser bidirecionais ou flua em apenas uma direção. Ou seja, a direção de sincronização pode ser *Hub membro* ou *membro ao Hub*, ou ambos.

-   O **intervalo de sincronização** é com que frequência ocorre a sincronização.

-   O **política de resolução de conflito** é uma política de nível de grupo, que pode ser *Hub wins* ou *wins membro*.

Sincronização de dados utiliza uma topologia hub- and -spoke para sincronizar dados. É possível definir uma das bases de dados no grupo como base de dados do Hub. O resto das bases de dados são bases de dados do membro. Sincronização ocorre apenas entre o Hub e os membros individuais.
-   O **base de dados do Hub** tem de ser uma base de dados do SQL do Azure.
-   O **bases de dados do membro** pode estar bases de dados SQL, bases de dados no local do SQL Server ou instâncias do SQL Server em virtual machines do Azure.
-   O **base de dados de sincronização** contém os metadados e o registo de sincronização de dados. A base de dados de sincronização tem de ser que uma base de dados do SQL do Azure localizado na mesma região que a base de dados do Hub. A base de dados de sincronização é criado de cliente e cliente pertencentes à empresa.

> [!NOTE]
> Se estiver a utilizar uma base de dados no local, terá de [configura um agente local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar os dados entre bases de dados](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quando utilizar a sincronização de dados

Sincronização de dados é útil nos casos em que dados tem de ser atualizados em várias bases de dados do Azure SQL ou bases de dados do SQL Server. Seguem-se os casos de utilização principal para sincronização de dados:

-   **Sincronização de dados de híbrida:** com sincronização de dados, pode manter os dados sincronizados entre as bases de dados no local e as bases de dados do Azure SQL Server para permitir que aplicações híbridas. Esta capacidade pode recorrer aos clientes que estiver a considerar a mudança para a nuvem e pretenderem colocar alguns da respetiva aplicação no Azure.

-   **As aplicações distribuídas:** em muitos casos, é vantajoso separar cargas de trabalho diferentes em bases de dados diferentes. Por exemplo, se tiver uma base de dados de produção de grandes dimensões, mas também tem de executar uma carga de trabalho de criação de relatórios ou análise estes dados, é útil ter uma segunda base de dados para esta carga de trabalho adicional. Esta abordagem minimiza o impacto do desempenho na sua carga de trabalho de produção. Pode utilizar a sincronização de dados para manter estas duas bases de dados sincronizadas.

-   **Global de aplicações distribuídas:** muitas empresas abrangem várias regiões e até mesmo vários países. Para minimizar a latência de rede, é melhor terá os seus dados numa região perto de si. Sincronização de dados, pode conservar facilmente bases de dados em regiões em todo o mundo sincronizados.

Sincronização de dados não é adequada para os seguintes cenários:

-   Recuperação Após Desastre

-   Escala de leitura

-   ETL (OLTP para OLAP)

-   Migração de SQL Server no local para a base de dados SQL do Azure

## <a name="how-does-data-sync-work"></a>Como funciona a sincronização de dados? 

-   **Controlar as alterações de dados:** sincronização de dados regista as alterações utilizando Inserir, atualizar e eliminar acionadores. As alterações são registadas numa tabela do lado na base de dados do utilizador.

-   **Sincronização de dados:** sincronização de dados foi concebida de um modelo Hub- and -Spoke. O Hub sincroniza-se com cada membro individualmente. As alterações do Hub são transferidas para o membro e, em seguida, as alterações de membro são carregadas para o Hub.

-   **Resolução de conflitos:** sincronização de dados fornece duas opções para a resolução do conflito, *Hub wins* ou *wins membro*.
    -   Se selecionar *Hub wins*, as alterações no hub substitui sempre as alterações no membro.
    -   Se selecionar *wins membro*, as alterações nas alterações de substituição de membro no hub. Se existir mais do que um membro, o valor final depende que membro sincroniza-se primeiro.

## <a name="sync-req-lim"></a>Requisitos e limitações

### <a name="general-considerations"></a>Considerações gerais

#### <a name="eventual-consistency"></a>Consistência eventual
Uma vez que a sincronização de dados é baseado no acionador, consistência transacional não é garantida. Microsoft garante que todas as alterações são efetuadas, eventualmente, e que a sincronização de dados não irá causar perda de dados.

#### <a name="performance-impact"></a>Impacto no desempenho
Sincronização de dados utiliza insere, atualizar e eliminar acionadores para controlar as alterações. Cria tabelas lado na base de dados de utilizador de registo de alterações. Estas atividades de controlo de alterações tem um impacto no sua carga de trabalho de base de dados. Avaliar o escalão de serviço e atualize, se necessário.

### <a name="general-requirements"></a>Requisitos gerais

-   Cada tabela tem de ter uma chave primária. Não altere o valor da chave primária em qualquer linha. Se tiver de alterar um valor de chave primária, eliminar a linha e recriá-la com o novo valor de chave primário. 

-   Isolamento do instantâneo tem de estar ativado. Para obter mais informações, consulte [isolamento do instantâneo no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

-   Uma tabela não pode ter uma coluna de identidade que não é a chave primária.

-   Os nomes dos objetos (bases de dados, tabelas e colunas) não podem conter o período de caracteres imprimíveis (.), à esquerda parêntesis Reto ([), ou Reto direito square (]).

-   Não é suportada a autenticação do Azure Active Directory.

#### <a name="unsupported-data-types"></a>Tipos de dados não suportado

-   FileStream

-   SQL SERVER/CLR UDT

-   XMLSchemaCollection (XML suportado)

-   Cursor, Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações sobre dimensões de serviço e a base de dados

| **Dimensões**                                                      | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização qualquer base de dados pode pertencer a.       | 5                      |                             |
| Número máximo de pontos finais de um grupo de sincronização único              | 30                     | Criar vários grupos de sincronização |
| Número máximo de pontos finais de locais de um grupo de sincronização único. | 5                      | Criar vários grupos de sincronização |
| Nomes de base de dados, de tabela, de esquema e de coluna                       | 50 carateres por nome |                             |
| Tabelas num grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas existentes numa tabela num grupo de sincronização                              | 1000                   |                             |
| Tamanho de linha de dados numa tabela                                        | 24 mb                  |                             |
| Intervalo de sincronização mínima                                           | 5 minutos              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>FAQ sobre a sincronização de dados do SQL Server

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Quanto custo do serviço de sincronização de dados do SQL Server (pré-visualização)?

Durante a pré-visualização, há sem encargos durante o próprio serviço de sincronização de dados do SQL Server (pré-visualização).  No entanto, que ainda acumular custos de transferência de dados de movimento de dados e para a instância de base de dados SQL. Para obter mais informações, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>As regiões suportam a sincronização de dados?

Sincronização de dados do SQL Server (pré-visualização) está disponível em todas as regiões de nuvem pública.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta de base de dados SQL? 

Sim. Tem de ter uma conta de base de dados do SQL Server para alojar a base de dados do Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Pode utilizar a sincronização de dados a sincronização entre o SQL Server no local bases de dados apenas? 
Não diretamente. Pode sincronizar os entre bases de dados do SQL Server no local indiretamente, no entanto, ao criar uma base de dados do Hub no Azure e, em seguida, adicionar as bases de dados no local ao grupo de sincronização.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Posso utilizar a sincronização de dados para dados de seed da minha base de dados de produção para uma base de dados vazio e, em seguida, mantê-las sincronizados? 
Sim. Crie manualmente o esquema na nova base de dados, processamento de scripts do original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e de as manter sincronizadas.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Deve utilizar sincronização de dados do SQL Server para criar cópias de segurança e restaurar os meus bases de dados?

Não se recomenda utilizar a sincronização de dados do SQL Server (pré-visualização) para criar uma cópia de segurança dos seus dados. Não é possível fazer cópias de segurança e restaurar para um ponto específico no tempo, porque as sincronizações de sincronização de dados do SQL Server (pré-visualização) não são com a versão. Além disso, a sincronização de dados do SQL Server (pré-visualização) não efetuar cópias de segurança outros objetos SQL, tais como procedimentos armazenados e não o equivalente a uma operação de restauro rapidamente.

Para obter um recomendado técnica de cópia de segurança, consulte [copiar uma base de dados SQL do Azure](sql-database-copy.md).

### <a name="is-collation-supported-in-sql-data-sync"></a>Agrupamento é suportado sincronização de dados SQL?

Sim. Sincronização de dados do SQL Server suporta o agrupamento nos seguintes cenários:

-   Se as tabelas de esquema de sincronização selecionado ainda não estiverem no seu hub ou membro bases de dados, em seguida, ao implementar o grupo de sincronização, o serviço cria automaticamente o correspondentes tabelas e colunas com as definições de agrupamento selecionadas nas bases de dados de destino vazio.

-   Se as tabelas para ser sincronizado já existem nas bases de dados do seu hub e o membro, a sincronização de dados do SQL Server requer que as colunas de chave primárias tem o mesmo agrupamento entre bases de dados de hub e o membro com êxito a implementar o grupo de sincronização. Não existem sem restrições de agrupamento em colunas que não sejam as colunas de chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>Federação é suportada sincronização de dados SQL?

Base de dados de raiz de Federação pode ser utilizado no serviço de sincronização de dados do SQL Server (pré-visualização) sem qualquer limitação. Não é possível adicionar o ponto final de base de dados federada para a versão atual de sincronização de dados do SQL Server (pré-visualização).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a sincronização de dados do SQL Server, consulte:

-   [Configurar a sincronização de dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
-   [Melhores práticas para a sincronização de dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   [Monitor sincronização de dados SQL do Azure com a análise de registos do OMS](sql-database-sync-monitor-oms.md)
-   [Resolver problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server:
    -   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Transferir a documentação da API de REST de sincronização de dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados do SQL Server](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
