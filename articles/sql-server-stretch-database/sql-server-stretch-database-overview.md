<properties
    pageTitle="Descrição geral da Stretch Database | Microsoft Azure"
    description="Saiba como a Stretch Database migra os dados históricos de forma transparente e segura para a nuvem do Microsoft Azure."
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
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Descrição geral da Stretch Database

A Stretch Database migra os dados históricos de forma transparente e segura para a nuvem do Microsoft Azure.

Se pretender começar com a Stretch Database imediatamente, consulte [Começar ao executar o Assistente de Ativação de Base de dados para Stretch](sql-server-stretch-database-wizard.md).

## Quais são as vantagens da Stretch Database?
A Stretch Database fornece as seguintes vantagens:

**Proporciona uma disponibilidade \-rentável para dados amovíveis** Transfira dados transacionais semiativos e amovíveis dinamicamente a partir do SQL Server para o Microsoft Azure com a SQL Server Stretch Database. Ao contrário do armazenamento típico de dados amovíveis, os seus dados estão sempre online e disponíveis para consulta. Pode fornecer linhas cronológicas de retenção de dados mais longas sem ultrapassar o orçamento para tabelas grandes, como a do Histórico de encomendas de clientes. Tire partido do custo reduzido do Azure em vez de\- dimensionar armazenamento dispendioso no local. Escolha o escalão de preço e configure as definições no Portal do Azure para manter o controlo sobre os custos. Aumente ou reduza verticalmente, conforme necessário. Visite a página [Preços da SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obter mais detalhes.

**Não necessita de alterações nas consultas ou nas aplicações** Aceda aos seus dados do SQL Server de forma totalmente integrada, independentemente de se \-estiverem no local ou transferidos para a nuvem.  Defina a política que determina onde os dados são armazenados e o SQL Server processa o movimento de dados em segundo plano. A tabela completa está sempre online e consultável. Além disso, a Stretch Database não requer quaisquer alterações nas consultas ou aplicações existentes – a localização dos dados é completamente transparente para a aplicação.

**Simplifica a \-manutenção de dados no local** Reduza a \-manutenção e o armazenamento no local dos seus dados. As cópias de segurança para a parte da nuvem dos seus dados são executadas automaticamente. As cópias de segurança para os seus \-dados no local são executadas mais rápido e terminam dentro da janela de manutenção. As suas \-necessidades de armazenamento no local são significativamente reduzidas. O Storage do Azure pode ser 80% menos \-dispendioso do que adicionar SSD no local.

**Mantém os seus dados protegidos, mesmo durante a migração** Mantenha a tranquilidade durante a transferência das aplicações mais importantes de forma segura para a nuvem. Sempre Encriptado do SQL Server proporciona a encriptação dos seus dados em movimento. A Segurança de nível da linha (RLS) e outras funcionalidades avançadas de segurança do SQL Server também funcionam com a Stretch Database para proteger os seus dados.

## O que faz a Stretch Database?
Depois de ativar a Stretch Database para uma instância do SQL Server, uma base de dados e pelo menos uma tabela, começa automaticamente a migrar os seus dados históricos para o Azure.

-   Se armazenar dados históricos numa tabela em separado, é possível migrar a tabela completa.

-   Se a tabela contiver dados históricos e atuais, pode especificar um predicado de filtro para selecionar as linhas a migrar.

A Stretch Database assegura que não existem perdas de dados se ocorrer uma falha durante a migração. Também tem lógica de repetição para lidar com problemas de ligação que possam ocorrer durante a migração. Uma vista de gestão dinâmica fornece o estado de migração.

Pode interromper a migração de dados para resolver problemas no servidor local ou maximizar a largura de banda da rede disponível.

Não tem de alterar as consultas existentes nem as aplicações cliente. Continua a ter acesso totalmente integrado aos dados locais e remotos, mesmo durante a migração de dados. Existe uma pequena quantidade de latência para consultas remotas, mas esta latência ocorre apenas ao consultar os dados históricos.

![Descrição geral da stretch database][StretchOverviewImage1]

## A Stretch Database é adequada para si?
Caso se identifique com as afirmações que se seguem, a Stretch Database pode ajudá-lo a satisfazer as suas necessidades e a resolver os problemas.

|Se for um decisor|Se for um administrador de bases de dados|
|------------------------------|-------------------|
|Tenho de manter os dados transacionais durante muito tempo.|O tamanho das minhas tabelas está a ficar descontrolado.|
|Por vezes, tenho de consultar os dados históricos.|Os meus utilizadores dizem que querem aceder a dados históricos, mas utilizam-nos raramente.|
|Tenho aplicações, incluindo aplicações mais antigas, que não pretendo atualizar.|Tenho de comprar e adicionar mais armazenamento continuamente.|
|Pretendo encontrar uma forma de poupar dinheiro no armazenamento.|Não consigo criar cópias de segurança ou restaurar tabelas tão grandes dentro do SLA.|

## Que tipo de bases de dados e tabelas são candidatos para a Stretch Database?
A Stretch Database está orientada para as bases de dados transacionais com grandes quantidades de dados históricos, normalmente armazenados num pequeno número de tabelas. Estas tabelas podem conter mais de mil milhões de linhas.

Se utilizar a funcionalidade de tabela temporal do SQL Server 2016, utilize a Stretch Database para migrar a totalidade ou parte da tabela histórica associada para o \-armazenamento rentável no Azure. Para obter mais informações, consulte [Gerir Retenção de Dados Históricos em Tabelas Temporais com Controlo de Versões do Sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Utilize o Stretch Database Advisor, uma funcionalidade do Assistente de Atualização do SQL Server 2016, para identificar bases de dados e tabelas para a Stretch Database. Para obter mais informações, consulte [Identificar bases de dados e tabelas para a Stretch Database](sql-server-stretch-database-identify-databases.md). Para obter mais informações sobre potenciais problemas de bloqueios, consulte [Limitações de área de superfície e problemas de bloqueios na Stretch Database](sql-server-stretch-database-limitations.md).

## <a name="FAQ"></a>Perguntas mais frequentes acerca da Stretch Database
**A Stretch Database funciona com &lt;nome da funcionalidade do SQL Server&gt;?**
-   Para obter uma lista das funcionalidades do SQL Server que tornam uma tabela não elegível para a Stretch, consulte [Limitações de área de superfície e problemas de bloqueios na Stretch Database](sql-server-stretch-database-limitations.md).

-   Opcionalmente, transfira o Assistente de Atualização do SQL Server 2016 e execute o Stretch Database Advisor para identificar bases de dados e tabelas que sejam candidatas para a Stretch Database. Para obter mais informações, consulte [Identificar bases de dados e tabelas para a Stretch Database](sql-server-stretch-database-identify-databases.md).

**Pode visar outra instância do SQL Server local para a Stretch Database?**
Não. A Stretch Database não suporta outra instância do SQL Server local como ponto final remoto.

**Posso desativar a Stretch e mover os dados migrados de volta à tabela local?**
Sim. Para obter mais informações, consulte [Desativar a Stretch Database e recuperar os dados remotos](sql-server-stretch-database-disable.md).

## Termos
**Base de dados local**. A \-base de dados do SQL Server no local.

**Ponto final remoto**. A localização no Microsoft Azure que contém os dados remotos da base de dados.

**Dados locais**. Dados numa base de dados com a Stretch Database ativada que não serão movidos para o Azure com base na configuração da Stretch Database das tabelas na base de dados.

**Dados elegíveis**. Dados numa base de dados com a Stretch Database ativada que ainda não foram movidos, mas serão movidos para o Azure com base na configuração da Stretch Database das tabelas na base de dados.

**Dados remotos**. Dados numa base de dados com a Stretch Database ativada que já foram movidos para o Azure.

## Arquitetura
A Stretch Database tira partido dos recursos no Microsoft Azure para descarregar o armazenamento de dados de arquivo e o processamento de consultas.

Quando ativa a Stretch Database numa base de dados, esta cria numa definição de \-servidor ligado seguro SQL Server no local. Esta definição de servidor ligado tem o ponto final remoto como destino. Quando ativa a Stretch Database numa tabela na base de dados, esta aprovisiona recursos remotos e começa a migrar dados elegíveis, se a migração estiver ativada.

As consultas relativas a tabelas com a Stretch Database ativada são executadas automaticamente em relação à base de dados local e ao ponto final remoto. A Stretch Database tira partido do poder de processamento no Azure para executar consultas em relação a dados remotos ao converter a consulta. Pode ver esta conversão como um operador de "consulta remota" no novo plano de consulta.

![Arquitetura da Stretch Database][StretchOverviewImage2]

## Segurança e permissões

### Ativar e desativar a Stretch Database para uma instância do SQL Server
Para começar a configurar bases de dados para a Stretch Database, primeiro tem de alterar a \-opção de configuração de instância ao nível de "arquivo de dados remotos" ao utilizar sp\_configure. Esta operação requer privilégios sysadmin ou serveradmin. Com esta opção ativada, pode configurar bases de dados para a Stretch Database, migrar dados e consultar dados no ponto final remoto.

### Ativar e desativar uma Stretch Database para uma base de dados ou tabela
Para configurar uma base de dados para a Stretch Database, tem de ter a permissão CONTROLAR BASE DE DADOS. Além disso, tem de facultar as credenciais de administrador ao ponto final remoto.

Para configurar uma tabela para a Stretch Database, tem de ter o privilégio ALTERAR na tabela e a base de dados já tem de estar configurada para a Stretch Database.

### Acesso a dados
Apenas os processos do sistema podem aceder à definição do servidor ligado por trás da Stretch Database. Os inícios de sessão do utilizador não podem emitir consultas através da definição do servidor ligado para o ponto final remoto.

A Stretch Database não altera o modelo de permissões de uma base de dados existente. Os inícios de sessão do utilizador podem consultar os dados numa tabela com a Stretch Database ativada através da base de dados local. A base de dados local efetua verificações de permissão de todas as ações iniciadas pelo utilizador da mesma forma que realiza noutros objetos quaisquer. Se estiver autorizado a aceder à tabela com a Stretch Database ativada, tem acesso a todos os respetivos conteúdos para os quais está autorizado, independentemente de onde os dados estão fisicamente.

![Modelo de acesso a dados da Stretch Database][StretchOverviewImage3]

## Experimentar a Stretch Database
**Experimente a Stretch Database com a base de dados de exemplo AdventureWorks.** Para obter a base de dados de exemplo AdventureWorks, transfira pelo menos o ficheiro da base de dados e o ficheiro de exemplos e scripts a partir [daqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar a base de dados de exemplo para uma instância do SQL Server 2016, deszipe o ficheiro de amostras e abra o ficheiro Stretch DB Samples a partir da pasta Stretch DB. Execute os scripts neste ficheiro para verificar o espaço utilizado pelos seus dados antes e depois de ativar a Stretch Database, para controlar o progresso da migração de dados e para confirmar que é possível continuar a consultar dados existentes e inserir novos dados, durante e após a migração de dados.

## Passo seguinte
**Identificar bases de dados e tabelas que são candidatos para a Stretch Database.** Transfira o Assistente de Atualização do SQL Server 2016 e execute o Stretch Database Advisor para identificar bases de dados e tabelas que sejam candidatas para a Stretch Database. O Stretch Database Advisor também identifica problemas de bloqueio. Para obter mais informações, consulte [Identificar bases de dados e tabelas para a Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Jun16_HO2-->


