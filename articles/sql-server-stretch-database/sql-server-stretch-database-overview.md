<properties
    pageTitle="Descrição geral da Stretch Database | Microsoft Azure"
    description="Saiba como a Stretch Database migra os seus dados amovíveis de forma transparente e segura para a nuvem do Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>


# Descrição geral da Stretch Database

A Stretch Database migra os seus dados amovíveis de forma transparente e segura para a nuvem do Microsoft Azure.

Se pretender começar com a Stretch Database imediatamente, consulte [Começar ao executar o Assistente de Ativação de Base de dados para Stretch](sql-server-stretch-database-wizard.md).

## Quais são as vantagens da Stretch Database?
A Stretch Database fornece as seguintes vantagens:

### Fornece \-disponibilidade rentável para dados amovíveis
Transfira dados transacionais semiativos e amovíveis dinamicamente a partir do SQL Server para o Microsoft Azure com a SQL Server Stretch Database. Ao contrário do armazenamento típico de dados amovíveis, os seus dados estão sempre online e disponíveis para consulta. Pode fornecer linhas cronológicas de retenção de dados mais longas sem ultrapassar o orçamento para tabelas grandes, como a do Histórico de encomendas de clientes. Tire partido do custo reduzido do Azure em vez de\- dimensionar armazenamento dispendioso no local. Escolha o escalão de preço e configure as definições no Portal do Azure para manter o controlo sobre os custos. Aumente ou reduza verticalmente, conforme necessário. Visite a página [Preços da SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obter mais detalhes.

### Não necessita de alterações nas consultas ou nas aplicações
Aceda aos seus dados do SQL Server de forma totalmente integrada, independentemente de estarem \-no local ou transferidos para a nuvem.  Defina a política que determina onde os dados são armazenados e o SQL Server processa o movimento de dados em segundo plano. A tabela completa está sempre online e consultável. Além disso, a Stretch Database não requer quaisquer alterações nas consultas ou aplicações existentes – a localização dos dados é completamente transparente para a aplicação.

### Simplifica a \-manutenção de dados no local
Reduz \-a manutenção e armazenamento no local dos seus dados. As cópias de segurança para os seus \-dados no local são executadas mais rápido e terminam dentro da janela de manutenção. As cópias de segurança para a parte da nuvem dos seus dados são executadas automaticamente. As suas \-necessidades de armazenamento no local são significativamente reduzidas. O Storage do Azure pode ser 80% menos \-dispendioso do que adicionar SSD no local.

### Mantém os seus dados seguros até mesmo durante a migração
Mantenha a tranquilidade durante a transferência das aplicações mais importantes de forma segura para a nuvem. Sempre Encriptado do SQL Server proporciona a encriptação dos seus dados em movimento. A Segurança de nível da linha (RLS) e outras funcionalidades avançadas de segurança do SQL Server também funcionam com a Stretch Database para proteger os seus dados.

## O que faz a Stretch Database?
Depois de ativar a Stretch Database para uma instância do SQL Server, uma base de dados e pelo menos uma tabela, a Stretch Database começa automaticamente a migrar os seus dados amovíveis para o Azure.

-   Se armazenar dados amovíveis numa tabela em separado, pode migrar a tabela completa.

-   Se a tabela contiver dados de acesso frequente e dados amovíveis, pode especificar uma função de filtro para selecionar as linhas a migrar.

**Não tem de alterar as consultas existentes nem as aplicações cliente.** Continua a ter acesso totalmente integrado aos dados locais e remotos, mesmo durante a migração de dados. Existe uma pequena quantidade de latência para consultas remotas, mas esta latência ocorre apenas ao consultar os dados amovíveis.

A **Stretch Database assegura que não existem perdas de dados** se ocorrer uma falha durante a migração. Também tem lógica de repetição para lidar com problemas de ligação que possam ocorrer durante a migração. Uma vista de gestão dinâmica fornece o estado de migração.

**Pode interromper a migração de dados** para resolver problemas no servidor local ou maximizar a largura de banda da rede disponível.

![Descrição geral da stretch database][StretchOverviewImage1]

## A Stretch Database é adequada para si?
Caso se identifique com as afirmações que se seguem, a Stretch Database pode ajudá-lo a satisfazer as suas necessidades e a resolver os problemas.

|Se for um decisor|Se for um administrador de bases de dados|
|------------------------------|-------------------|
|Tenho de manter os dados transacionais durante muito tempo.|O tamanho das minhas tabelas está a ficar descontrolado.|
|Por vezes, tenho de consultar os dados amovíveis.|Os meus utilizadores dizem que querem aceder a dados amovíveis, mas utilizam-nos raramente.|
|Tenho aplicações, incluindo aplicações mais antigas, que não pretendo atualizar.|Tenho de comprar e adicionar mais armazenamento continuamente.|
|Pretendo encontrar uma forma de poupar dinheiro no armazenamento.|Não consigo criar cópias de segurança ou restaurar tabelas tão grandes dentro do SLA.|

## Que tipo de bases de dados e tabelas são candidatos para a Stretch Database?
A Stretch Database está orientada para as bases de dados transacionais com grandes quantidades de dados amovíveis, normalmente armazenados num pequeno número de tabelas. Estas tabelas podem conter mais de mil milhões de linhas.

Se utilizar a funcionalidade de tabela temporal do SQL Server 2016, utilize a Stretch Database para migrar a totalidade ou parte da tabela histórica associada para o \-armazenamento rentável no Azure. Para obter mais informações, consulte [Gerir Retenção de Dados Históricos em Tabelas Temporais com Controlo de Versões do Sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Utilize o Stretch Database Advisor, uma funcionalidade do Assistente de Atualização do SQL Server 2016, para identificar bases de dados e tabelas para a Stretch Database. Para obter mais informações, consulte [Identificar bases de dados e tabelas para a Stretch Database](sql-server-stretch-database-identify-databases.md). Para saber mais sobre potenciais problemas de bloqueios, veja [Limitations for Stretch Database (Limitações da Stretch Database)](sql-server-stretch-database-limitations.md).

## Experimentar a Stretch Database
**Experimente a Stretch Database com a base de dados de exemplo AdventureWorks.** Para obter a base de dados de exemplo AdventureWorks, transfira pelo menos o ficheiro da base de dados e o ficheiro de exemplos e scripts a partir [daqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar a base de dados de exemplo para uma instância do SQL Server 2016, deszipe o ficheiro de amostras e abra o ficheiro Stretch DB Samples a partir da pasta Stretch DB. Execute os scripts neste ficheiro para verificar o espaço utilizado pelos seus dados antes e depois de ativar a Stretch Database, para controlar o progresso da migração de dados e para confirmar que é possível continuar a consultar dados existentes e inserir novos dados, durante e após a migração de dados.

## Passo seguinte
**Identificar bases de dados e tabelas que são candidatos para a Stretch Database.** Transfira o Assistente de Atualização do SQL Server 2016 e execute o Stretch Database Advisor para identificar bases de dados e tabelas que sejam candidatas para a Stretch Database. O Stretch Database Advisor também identifica problemas de bloqueio. Para obter mais informações, consulte [Identificar bases de dados e tabelas para a Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Sep16_HO3-->


