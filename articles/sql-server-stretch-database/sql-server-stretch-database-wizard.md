<properties
    pageTitle="Começar através da execução do Assistente Enable Database for Stretch | Microsoft Azure"
    description="Saiba como configurar uma base de dados para o Stretch Database, executando o Assistente Enable Database for Stretch."
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
    ms.topic="hero-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Começar através da execução do Assistente Enable Database for Stretch

Para configurar uma base de dados para o Stretch Database, execute o Assistente Enable Database for Stretch.  Este tópico descreve as informações que tem de introduzir e as escolhas que tem de fazer no assistente.

Para saber mais sobre o Stretch Database, consulte o artigo [Stretch Database](sql-server-stretch-database-overview.md).

## Iniciar o assistente

1.  No SQL Server Management Studio, no Object Explorer, selecione a base de dados na qual pretende ativar o Stretch.

2.  Clique com o botão direito do \-rato e selecione **Tasks** (Tarefas), selecione **Stretch** (Ampliar) e, em seguida, selecione **Enable** (Ativar) para iniciar o assistente.

## <a name="Intro"></a>Introdução
Reveja o objetivo do assistente e os pré-requisitos.

![Página de introdução do assistente do Stretch Database][StretchWizardImage1]

## <a name="Tables"></a>Selecionar tabelas
Selecione as tabelas que pretende ativar no Stretch.

![Página de seleção de tabelas do assistente do Stretch Database][StretchWizardImage2]

|Coluna|Descrição|
|----------|---------------|
|(nenhum título)|Selecione a caixa de verificação nesta coluna para ativar a tabela selecionada para o Stretch.|
|**Nome**|Especifica o nome da coluna na tabela.|
|(nenhum título)|Normalmente, um símbolo nesta coluna indica que não é possível ativar a tabela selecionada para o Stretch devido a um problema de bloqueio. Isto poderá acontecer porque a tabela utiliza um tipo de dados não suportado. Coloque o cursor sobre o símbolo para apresentar mais informações numa descrição. Para obter mais informações, consulte [Limitações de área de superfície e problemas de bloqueios no Stretch Database](sql-server-stretch-database-limitations.md).|
|**Stretched (Ampliada)**|Indica se a tabela já está ativada.|
|**Migrar**|Pode migrar uma tabela inteira (**Entire Table**) ou pode especificar um predicado de filtro com base na data no assistente. Se pretender utilizar um predicado de filtro diferente para selecionar linhas para migrar, execute a instrução ALTER TABLE para especificar o predicado de filtro depois de sair do assistente. Para obter mais informações sobre o predicado de filtro, consulte o artigo [Utilizar um predicado de filtro para selecionar linhas para migrar (Stretch Database)](sql-server-stretch-database-predicate-function.md). Para obter mais informações sobre como aplicar o predicado, consulte o artigo [Ativar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rows (Linhas)**|Especifica o número de linhas na tabela.|
|**Size (KB) (Tamanho (KB))**|Especifica o tamanho da tabela em KB.|

## <a name="Filter"></a>Fornecer opcionalmente um predicado de filtro com base na data

Se pretender fornecer um predicado de filtro com base na data para selecionar linhas para migrar, efetue os seguintes procedimentos na página **Select tables** (Selecionar tabelas).

1.  Na lista **Select the tables you want to stretch** (Selecionar as tabelas que pretende ampliar), clique em **Entire Table** (Tabela inteira) na linha referente à tabela. A caixa de diálogo **Select rows to stretch** (Selecionar linhas a ampliar) abre.

    ![Definir um predicado de filtro com base na data][StretchWizardImage2a]

2.  Na caixa de diálogo **Select rows to stretch** (Selecionar linhas a ampliar), selecione **Choose Rows** (Escolher linhas).

3.  No campo**Nome**, indique um nome para o predicado de filtro.

4.  Para a cláusula **Where**, escolha uma coluna de data da tabela, escolha um operador e indique um valor de data.

5. Clique em **Check** (Verificar) para testar o predicado. Se o predicado devolver resultados da tabela - ou seja, se existirem linhas para migrar que satisfaçam a condição - o teste comunica **Success** (Êxito).

6.  Clique em Done (Concluído) para regressar à página **Select tables** (Selecionar tabelas).

    ![Selecionar a página Tables (Tabelas) depois de definir um predicado de filtro][StretchWizardImage2b]

## <a name="Configure"></a>Configurar a implementação do Azure

1.  Inicie sessão no Microsoft Azure com uma conta Microsoft.

    ![Iniciar sessão no Azure - assistente do Stretch Database][StretchWizardImage3]

2.  Selecione a subscrição do Azure a utilizar para o Stretch Database.

3.  Selecione uma região do Azure. Se criar um novo servidor, o servidor é criado nesta região.

    Para minimizar a latência, escolha a região do Azure na qual o SQL Server se encontra. Para obter mais informações sobre regiões, consulte o artigo [Regiões do Azure](https://azure.microsoft.com/regions/).

4.  Especifique se pretende utilizar um servidor existente ou criar um novo servidor do Azure.

    Se o Active Directory no seu SQL Server estiver Federado com o Azure Active Directory, opcionalmente, pode utilizar uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure. Para obter mais informações sobre os requisitos para esta opção, consulte o artigo [Opções de ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Criar um novo servidor**

        1.  Crie um início de sessão e uma palavra-passe para o administrador do servidor.

        2.  Opcionalmente, utilize uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure.

        ![Criar novo servidor do Azure - assistente do Stretch Database][StretchWizardImage4]

    -   **Servidor existente**

        1.  Selecione ou introduza o nome do servidor do Azure existente.

        2.  Selecione o método de autenticação.

            -   Se selecionar **SQL Server Authentication** (Autenticação do SQL Server), crie um novo início de sessão e palavra-passe.

            -   Selecione **Active Directory Integrated Authentication** (Autenticação Integrada do Active Directory) para utilizar uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure.

        ![Selecionar servidor do Azure existente - assistente do Stretch Database][StretchWizardImage5]

## <a name="Credentials"></a>Credenciais seguras
Introduza uma palavra-passe segura para criar uma chave mestra da base de dados ou, se já existir uma chave mestra da base de dados, introduza a palavra-passe da mesma.

Tem de ter uma chave mestra da base de dados para proteger as credenciais que o Stretch Database utiliza para ligar à base de dados remota.

![Página de credenciais seguras do assistente do Stretch Database][StretchWizardImage6]

Para obter mais informações sobre a chave mestra da base de dados, consulte o artigo [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [Criar uma Chave Mestra da Base de Dados](https://msdn.microsoft.com/library/aa337551.aspx). Para obter mais informações sobre a credencial que o assistente cria, consulte o artigo [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selecionar o endereço IP
Utilize o endereço IP público do SQL Server ou introduza um intervalo de endereços IP para criar uma regra de firewall no Azure que permite que o SQL Server comunique com o servidor remoto do Azure.

O endereço ou endereços IP que fornece nesta página indicam ao servidor do Azure que deve permitir que dados de entrada, consultas e operações de gestão iniciadas pelo SQL Server passem pela firewall do Azure. O assistente não alterar nada nas definições de firewall no SQL Server.

![Página de seleção de endereço IP do assistente do Stretch Database][StretchWizardImage7]

## <a name="Summary"></a>Resumo
Reveja os valores que introduziu e as opções que selecionou no assistente. Em seguida, selecione **Finish** (Concluir) para ativar o Stretch.

![Página de resumo do assistente do Stretch Database][StretchWizardImage8]

## <a name="Results"></a>Resultados
Reveja os resultados.

Opcionalmente, selecione **Monitor** (Monitorizar) para iniciar a monitorização do estado de migração de dados no Stretch Database Monitor. Para obter mais informações, consulte o artigo [Monitorizar e resolver problemas de migração de dados (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Resolver problemas do assistente
**O assistente do Stretch Database falhou.**
Se o Stretch Database ainda não estiver ativado ao nível do servidor e o utilizador executar o assistente sem as permissões do administrador do sistema para ativá-lo, o assistente irá falhar. Peça ao administrador do sistema para ativar o Stretch Database na instância do servidor local e, em seguida, execute novamente o assistente. Para obter mais informações, consulte o artigo [Pré-requisito: permissão para ativar o Stretch Database no servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Passos seguintes
Ative tabelas adicionais para o Stretch Database. Monitorize a migração de dados e gira tabelas e bases de dados com o Stretch\- ativado.

-   [Ativar o Stretch Database para uma base de dados](sql-server-stretch-database-enable-table.md) para ativar tabelas adicionais.

-   [Monitorizar o Stretch Database](sql-server-stretch-database-monitor.md) para ver o estado da migração de dados.

-   [Colocar em pausa e retomar o Stretch Database](sql-server-stretch-database-pause.md)

-   [Gerir e resolver problemas do Stretch Database](sql-server-stretch-database-manage.md)

-   [Criar cópias de segurança e restaurar bases de dados com o Stretch ativado](sql-server-stretch-database-backup.md)

## Consulte também

[Ativar o Stretch Database para uma base de dados](sql-server-stretch-database-enable-database.md)

[Ativar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png



<!--HONumber=Jun16_HO2-->


