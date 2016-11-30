---
title: "Começar através da execução do Assistente Enable Database for Stretch | Microsoft Docs"
description: Saiba como configurar uma base de dados para o Stretch Database, executando o Assistente Enable Database for Stretch.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 1189ab95-ba84-459c-bfb1-57cdf36ee111
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c171da72bbdbfc8c15c6e39fcc8d5000f6be087


---
# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Começar através da execução do Assistente Enable Database for Stretch
Para configurar uma base de dados para o Stretch Database, execute o Assistente Enable Database for Stretch.  Este tópico descreve as informações que tem de introduzir e as escolhas que tem de fazer no assistente.

Para saber mais sobre o Stretch Database, consulte o artigo [Stretch Database](sql-server-stretch-database-overview.md).

> [!NOTE]
> Mais tarde, se desativar a Stretch Database, lembre-se de que a desativação da Stretch Database para uma tabela ou uma base de dados não elimina o objeto remoto. Se pretender eliminar a tabela remota ou a base de dados remota, terá de removê-la através do Portal de Gestão do Azure. Os objetos remotos continuam a implicar custos no Azure até serem eliminados manualmente. 
> 
> 

## <a name="launch-the-wizard"></a>Iniciar o assistente
1. No SQL Server Management Studio, no Object Explorer, selecione a base de dados na qual pretende ativar o Stretch.
2. Clique com o botão direito do \-rato e selecione **Tasks** (Tarefas), selecione **Stretch** (Ampliar) e, em seguida, selecione **Enable** (Ativar) para iniciar o assistente.

## <a name="a-nameintroaintroduction"></a><a name="Intro"></a>Introdução
Reveja o objetivo do assistente e os pré-requisitos.

Os pré-requisitos importantes incluem os seguintes:

* Tem de ser um administrador para alterar as definições da base de dados.
* Tem de ter uma subscrição do Microsoft Azure.
* O seu SQL Server tem de ser capaz de comunicar com o servidor remoto do Azure.

![Página de introdução do assistente do Stretch Database][StretchWizardImage1]

## <a name="a-nametablesaselect-tables"></a><a name="Tables"></a>Selecionar tabelas
Selecione as tabelas que pretende ativar no Stretch.

As tabelas com muitas linhas aparecem na parte superior da lista ordenada. Antes de o Assistente apresentar a lista de tabelas, analisa-as em busca de tipos de dados que não são atualmente suportados pela Stretch Database.

![Página de seleção de tabelas do assistente do Stretch Database][StretchWizardImage2]

| Coluna | Descrição |
| --- | --- |
| (nenhum título) |Selecione a caixa de verificação nesta coluna para ativar a tabela selecionada para o Stretch. |
| **Nome** |Especifica o nome da coluna na tabela. |
| (nenhum título) |Um símbolo nesta coluna pode representar um aviso que não\' o impede de ativar a tabela selecionada para Stretch. Pode também representar um problema de bloqueio que o impede de ativar a tabela selecionada para Stretch \-, por exemplo, porque a tabela utiliza um tipo de dados não suportado. Coloque o cursor sobre o símbolo para apresentar mais informações numa descrição. Para obter mais informações, veja [Limitations for Stretch Database (Limitações da Stretch Database)](sql-server-stretch-database-limitations.md). |
| **Stretched (Ampliada)** |Indica se a tabela já está ativada para Stretch. |
| **Migrar** |Pode migrar uma tabela inteira (**Entire Table**) ou pode especificar um filtro numa coluna existente na tabela. Se pretender utilizar uma função de filtro diferente para selecionar as linhas a migrar, execute a instrução ALTER TABLE para especificar a função de filtro depois de sair do assistente. Para obter mais informações sobre a função de filtro, veja [Selecionar as linhas a migrar com uma função de filtro](sql-server-stretch-database-predicate-function.md). Para obter mais informações sobre como aplicar a função, veja [Enable Stretch Database for a table (Ativar a Stretch Database para uma tabela)](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx). |
| **Rows (Linhas)** |Especifica o número de linhas na tabela. |
| **Size (KB) (Tamanho (KB))** |Especifica o tamanho da tabela em KB. |

## <a name="a-namefilteraoptionally-provide-a-row-filter"></a><a name="Filter"></a>Opcionalmente, proporcionar um filtro de linha
Se pretende fornecer uma função de filtro para selecionar as linhas a migrar, efetue os seguintes procedimentos na página **Select tables** (Selecionar tabelas).

1. Na lista **Select the tables you want to stretch** (Selecionar as tabelas que pretende ampliar), clique em **Entire Table** (Tabela inteira) na linha referente à tabela. A caixa de diálogo **Select rows to stretch** (Selecionar linhas a ampliar) abre.
   
   ![Definir uma função de filtro][StretchWizardImage2a]
2. Na caixa de diálogo **Select rows to stretch** (Selecionar linhas a ampliar), selecione **Choose Rows** (Escolher linhas).
3. No campo**Name** (Nome), indique um nome para a função de filtro.
4. Para a cláusula **Where** (Onde), escolha uma coluna da tabela, escolha um operador e indique um valor.
5. Clique em **Check** (Verificar) para testar a função. Se a função devolver resultados da tabela, ou seja, se existirem linhas para migrar que satisfaçam a condição, o teste comunica **Success** (Êxito).
   
   > [!NOTE]
   > A caixa de texto que apresenta a consulta de filtro é só de leitura. Não pode editar a consulta na caixa de texto.
   > 
   > 
6. Clique em Done (Concluído) para regressar à página **Select tables** (Selecionar tabelas).

A função de filtro é criada no SQL Server apenas quando concluir o assistente. Até lá, pode voltar à página **Select tables** (Selecionar tabelas) para alterar ou mudar o nome da função de filtro.

![Página Select Tables (Selecionar Tabelas) depois de definir uma função de filtro][StretchWizardImage2b]

Se pretender utilizar um tipo diferente de função de filtro para selecionar as linhas a migrar, efetue um dos seguintes procedimentos.  

* Saia do assistente e execute a instrução ALTER TABLE para ativar Stretch para a tabela e especificar uma função de filtro. Para obter mais informações, veja [Enable Stretch Database for a table (Ativar a Stretch Database para uma tabela)](sql-server-stretch-database-enable-table.md).  
* Depois de sair do assistente, execute a instrução ALTER TABLE para especificar uma função de filtro. Para obter os passos necessários, veja [Add a filter function after running the Wizard (Adicionar uma função de filtro depois de executar o Assistente)](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="a-nameconfigureaconfigure-azure-deployment"></a><a name="Configure"></a>Configurar a implementação do Azure
1. Inicie sessão no Microsoft Azure com uma conta Microsoft.
   
   ![Iniciar sessão no Azure - assistente do Stretch Database][StretchWizardImage3]
2. Selecione a subscrição existente do Azure a utilizar para a Stretch Database.
3. Selecione uma região do Azure.
   
   * Se criar um novo servidor, o servidor é criado nesta região.  
   * Se tiver servidores existentes na região selecionada, o assistente apresenta uma lista dos mesmos quando escolhe **Servidor existente**.
   
   Para minimizar a latência, escolha a região do Azure na qual o SQL Server se encontra. Para obter mais informações sobre regiões, consulte o artigo [Regiões do Azure](https://azure.microsoft.com/regions/).
4. Especifique se pretende utilizar um servidor existente ou criar um novo servidor do Azure.
   
   Se o Active Directory no seu SQL Server estiver Federado com o Azure Active Directory, opcionalmente, pode utilizar uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure. Para obter mais informações sobre os requisitos para esta opção, consulte o artigo [Opções de ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).
   
   * **Criar um novo servidor**
     
     1. Crie um início de sessão e uma palavra-passe para o administrador do servidor.
     2. Opcionalmente, utilize uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure.
     
     ![Criar novo servidor do Azure - assistente do Stretch Database][StretchWizardImage4]
   * **Servidor existente**
     
     1. Selecione o servidor existente do Azure.
     2. Selecione o método de autenticação.
        
        * Se selecionar **SQL Server Authentication** (Autenticação do SQL Server), forneça o início de sessão e palavra-passe de administrador.
        * Selecione **Active Directory Integrated Authentication** (Autenticação Integrada do Active Directory) para utilizar uma conta de serviço federado para que o SQL Server comunique com o servidor remoto do Azure. Se o servidor selecionado não estiver integrado com o Azure Active Directory, esta opção não aparece.
     
     ![Selecionar servidor do Azure existente - assistente do Stretch Database][StretchWizardImage5]

## <a name="a-namecredentialsasecure-credentials"></a><a name="Credentials"></a>Credenciais seguras
Tem de ter uma chave mestra da base de dados para proteger as credenciais que o Stretch Database utiliza para ligar à base de dados remota.  

Se já existir uma chave mestra de base de dados, introduza a palavra-passe para a mesma.  

![Página de credenciais seguras do assistente do Stretch Database][StretchWizardImage6b]

Se a base de dados não tiver uma chave mestra existente, introduza uma palavra-passe segura para criar uma chave mestra de base de dados.  

![Página de credenciais seguras do assistente do Stretch Database][StretchWizardImage6]

Para obter mais informações sobre a chave mestra da base de dados, consulte o artigo [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [Criar uma Chave Mestra da Base de Dados](https://msdn.microsoft.com/library/aa337551.aspx). Para obter mais informações sobre a credencial que o assistente cria, consulte o artigo [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="a-namenetworkaselect-ip-address"></a><a name="Network"></a>Selecionar o endereço IP
Utilize o intervalo de endereços IP da sub-rede (recomendado) ou o endereço IP público do seu SQL Server para criar uma regra de firewall no Azure que permite que o SQL Server comunique com o servidor remoto do Azure.

O endereço ou endereços IP que fornece nesta página indicam ao servidor do Azure que deve permitir que dados de entrada, consultas e operações de gestão iniciadas pelo SQL Server passem pela firewall do Azure. O assistente não alterar nada nas definições de firewall no SQL Server.

![Página de seleção de endereço IP do assistente do Stretch Database][StretchWizardImage7]

## <a name="a-namesummaryasummary"></a><a name="Summary"></a>Resumo
Reveja os valores que introduziu e as opções que selecionou no assistente e os custos estimados no Azure. Em seguida, selecione **Finish** (Concluir) para ativar o Stretch.

![Página de resumo do assistente do Stretch Database][StretchWizardImage8]

## <a name="a-nameresultsaresults"></a><a name="Results"></a>Resultados
Reveja os resultados.

Para monitorizar o estado da migração de dados, veja [Monitor and troubleshoot data migration (Stretch Database) (Monitorizar e resolver problemas de migração de dados (Stretch Database))](sql-server-stretch-database-monitor.md).

![Página de resultados do assistente da Stretch Database][StretchWizardImage9]

## <a name="a-nameknownissuesatroubleshooting-the-wizard"></a><a name="KnownIssues"></a>Resolver problemas do assistente
**O assistente da Stretch Database falhou.**
Se o Stretch Database ainda não estiver ativado ao nível do servidor e o utilizador executar o assistente sem as permissões do administrador do sistema para ativá-lo, o assistente irá falhar. Peça ao administrador do sistema para ativar o Stretch Database na instância do servidor local e, em seguida, execute novamente o assistente. Para obter mais informações, consulte o artigo [Pré-requisito: permissão para ativar o Stretch Database no servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Passos seguintes
Ative tabelas adicionais para o Stretch Database. Monitorize a migração de dados e gira tabelas e bases de dados com o Stretch\- ativado.

* [Ativar o Stretch Database para uma base de dados](sql-server-stretch-database-enable-table.md) para ativar tabelas adicionais.
* [Monitorizar e resolver problemas de migração de dados](sql-server-stretch-database-monitor.md) para ver o estado da migração de dados.
* [Colocar em pausa e retomar a Stretch Database](sql-server-stretch-database-pause.md)
* [Gerir e resolver problemas da Stretch Database](sql-server-stretch-database-manage.md)
* [Criar cópias de segurança de bases de dados com a Stretch ativada](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consultar também
[Ativar a Stretch Database para uma base de dados](sql-server-stretch-database-enable-database.md)

[Ativar a Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png



<!--HONumber=Nov16_HO2-->


