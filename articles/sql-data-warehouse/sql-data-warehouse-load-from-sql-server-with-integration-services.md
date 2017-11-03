---
title: "Carregar dados do SQL Server do armazém de dados de SQL do Azure (SSIS) | Microsoft Docs"
description: Mostra como criar um pacote de SQL Server Integration Services (SSIS) para mover dados de uma ampla variedade de origens de dados para o SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Carregar dados do SQL Server do armazém de dados de SQL do Azure (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Crie um pacote de SQL Server Integration Services (SSIS) para carregar dados do SQL Server para o Azure SQL Data Warehouse. Pode, opcionalmente, reestruturar, transformar e limpar os dados, estes passam pelo fluxo de dados SSIS.

Neste tutorial, irá:

* Crie um novo projeto de serviços de integração no Visual Studio.
* Ligar às origens de dados, incluindo o SQL Server (como uma origem) e o SQL Data Warehouse (como um destino).
* Conceber um pacote SSIS carrega dados de origem para o destino.
* Execute o pacote SSIS para carregar os dados.

Este tutorial utiliza o SQL Server como origem de dados. SQL Server pode estar em execução no local ou numa máquina virtual do Azure.

## <a name="basic-concepts"></a>Conceitos básicos
O pacote é a unidade de trabalho no SSIS. Pacotes relacionados são agrupadas numa projetos. Crie projetos e pacotes de estrutura no Visual Studio com SQL Server Data Tools. O processo de conceção é um processo visual da qual pode arraste e largue os componentes da caixa de ferramentas a superfície do design, ligue-e definir as respetivas propriedades. Depois de concluir o pacote, pode opcionalmente implementá-lo para o SQL Server para a Gestão abrangente, monitorização e de segurança.

## <a name="options-for-loading-data-with-ssis"></a>Opções para carregar dados com SSIS
SQL Server Integration Services (SSIS) é um conjunto flexível de ferramentas que fornece uma variedade de opções para ligar a e carregar dados para o SQL Data Warehouse.

1. Utilize um destino de NET ADO para ligar ao SQL Data Warehouse. Este tutorial utiliza um destino de NET ADO porque tem as opções de configuração menos.
2. Utilize um destino de OLE DB para ligar ao SQL Data Warehouse. Esta opção pode fornecer ligeiramente melhor desempenho do que o destino de NET ADO.
3. Utilize a tarefa de carregar de Blob do Azure para testar os dados no Blob Storage do Azure. Em seguida, utilize as tarefas de SSIS executar SQL para iniciar um script do Polybase que carrega os dados para o SQL Data Warehouse. Esta opção fornece o melhor desempenho das três opções listadas aqui. Para obter a tarefa de carregar de Blob do Azure, transfira o [Microsoft SQL Server 2016 integração serviços Feature Pack para o Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Para saber mais sobre o Polybase, consulte o artigo [Guia do PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Antes de começar
Para seguir este tutorial, é necessário:

1. **SQL Server Integration Services (SSIS)**. SSIS é um componente do SQL Server e necessita de uma versão de avaliação ou uma versão licenciada do SQL Server. Para obter uma versão de avaliação do SQL Server 2016 Preview, consulte [avaliações do SQL Server][SQL Server Evaluations].
2. **Visual Studio**. Para obter a edição de Comunidade do Studio Visual gratuita, consulte [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools para Visual Studio (SSDT)**. Para instalar o SQL Server Data Tools para Visual Studio, consulte [transferir SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Dados de exemplo**. Este tutorial utiliza armazenados no SQL Server na base de dados de exemplo AdventureWorks, como os dados de origem de dados de exemplo para ser carregado para o SQL Data Warehouse. Para obter a base de dados de exemplo AdventureWorks, consulte [bases de dados de exemplo AdventureWorks 2014][AdventureWorks 2014 Sample Databases].
5. **Uma base de dados do SQL Data Warehouse e as permissões**. Este tutorial liga a uma instância do SQL Data Warehouse e carrega dados para a mesma. Tem de ter permissões para criar uma tabela e carregar os dados.
6. **Uma regra de firewall**. Tem de criar uma regra de firewall no SQL Data Warehouse com o endereço IP do computador local antes de pode carregar dados para o armazém de dados do SQL Server.

## <a name="step-1-create-a-new-integration-services-project"></a>Passo 1: Criar um novo projeto de serviços de integração
1. Inicie o Visual Studio.
2. No **ficheiro** menu, selecione **novo | Projeto**.
3. Navegue para o **instalado | Modelos | Business Intelligence | Serviços de integração** tipos de projetos.
4. Selecione **projeto dos serviços de integração**. Fornecer valores para **nome** e **localização**e, em seguida, selecione **OK**.

Visual Studio abre e cria um novo projeto do Integration Services (SSIS). Em seguida, o Visual Studio abre o estruturador para o pacote SSIS novo (Package.dtsx) único no projeto. Consulte as seguintes áreas de ecrã:

* À esquerda, o **Toolbox** dos componentes SSIS.
* No meio, a superfície do design, com vários separadores. Normalmente utiliza, pelo menos, o **fluxo de controlo** e **do fluxo de dados** separadores.
* À direita, a **Explorador de soluções** e **propriedades** painéis.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Passo 2: Criar o fluxo de dados básicos
1. Arraste uma tarefa de fluxo de dados da caixa de ferramentas para o centro da superfície do design (no **fluxo de controlo** separador).
   
    ![][02]
2. Faça duplo clique a tarefa de fluxo de dados para mudar para o separador do fluxo de dados.
3. Na lista de outras origens na caixa de ferramentas, arraste uma origem do ADO.NET para a superfície do design. Com o adaptador de origem ainda selecionado, altere o nome do **origem do SQL Server** no **propriedades** painel.
4. Na lista de outros destinos na caixa de ferramentas, arraste um destino do ADO.NET para a superfície do design na origem ADO.NET. Com o adaptador de destino ainda selecionado, altere o nome do **destino do armazém de dados do SQL Server** no **propriedades** painel.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Passo 3: Configure o adaptador de origem
1. Faça duplo clique a placa de origem para abrir o **Editor de origem do ADO.NET**.
   
    ![][03]
2. No **Gestor de ligações** separador do **Editor de origem do ADO.NET**, clique no **novo** junto ao **Gestor de ligações do ADO.NET** lista para abrir o **configurar Gestor de ligação do ADO.NET** caixa de diálogo caixa e criar definições de ligação para a partir do qual este tutorial carrega dados da base de dados do SQL Server.
   
    ![][04]
3. No **configurar Gestor de ligação do ADO.NET** caixa de diálogo, clique no **novo** botão para abrir o **Gestor de ligações** diálogo caixa e criar uma nova ligação de dados.
   
    ![][05]
4. No **Gestor de ligações** diálogo caixa, efetue os seguintes procedimentos.
   
   1. Para **fornecedor**, selecione o fornecedor de dados SqlClient.
   2. Para **nome do servidor**, introduza o nome do SQL Server.
   3. No **inicie sessão no servidor de** secção, selecione ou introduza as informações de autenticação.
   4. No **ligar a uma base de dados** secção, selecione a base de dados de exemplo AdventureWorks.
   5. Clique em **Testar ligação**.
      
       ![][06]
   6. Na caixa de diálogo reporta os resultados do teste de ligação, clique em **OK** para voltar para o **Gestor de ligações** caixa de diálogo.
   7. No **Gestor de ligações** caixa de diálogo, clique em **OK** para voltar para o **configurar Gestor de ligação do ADO.NET** caixa de diálogo.
5. No **configurar Gestor de ligação do ADO.NET** caixa de diálogo, clique em **OK** para voltar para o **Editor de origem do ADO.NET**.
6. No **Editor de origem do ADO.NET**, no **nome da tabela ou vista** lista, selecione o **Sales.SalesOrderDetail** tabela.
   
    ![][07]
7. Clique em **pré-visualização** para ver as primeiros 200 linhas de dados na tabela de origem no **pré-visualizar resultados de consulta** caixa de diálogo.
   
    ![][08]
8. No **pré-visualizar resultados de consulta** caixa de diálogo, clique em **fechar** para regressar ao **Editor de origem do ADO.NET**.
9. No **Editor de origem do ADO.NET**, clique em **OK** para concluir a configuração a origem de dados.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Passo 4: Ligar o adaptador de origem para o adaptador de destino
1. Selecione o adaptador de origem a superfície do design.
2. Selecione a seta azul que alarga a partir do adaptador de origem e arraste-o para o editor de destino até que o se ajusta no local.
   
    ![][10]
   
    Um pacote SSIS típico, utiliza um número de outros componentes da caixa de ferramentas SSIS entre a origem e destino para reestruturar, transformar e limpar os seus dados, estes passam pelo fluxo de dados SSIS. Para manter este exemplo tão simples quanto possível, iremos está a ligar a origem diretamente para o destino.

## <a name="step-5-configure-the-destination-adapter"></a>Passo 5: Configurar o adaptador de destino
1. Faça duplo clique o adaptador de destino para abrir o **ADO.NET destino Editor**.
   
    ![][11]
2. No **Gestor de ligações** separador do **ADO.NET destino Editor**, clique no **novo** junto ao **Gestor de ligações** lista para abrir o **configurar Gestor de ligação do ADO.NET** caixa de diálogo caixa e criar definições de ligação para a base de dados do armazém de dados SQL do Azure na qual este tutorial carrega dados.
3. No **configurar Gestor de ligação do ADO.NET** caixa de diálogo, clique no **novo** botão para abrir o **Gestor de ligações** diálogo caixa e criar uma nova ligação de dados.
4. No **Gestor de ligações** diálogo caixa, efetue os seguintes procedimentos.
   1. Para **fornecedor**, selecione o fornecedor de dados SqlClient.
   2. Para **nome do servidor**, introduza o nome do SQL Data Warehouse.
   3. No **inicie sessão no servidor de** secção, selecione **autenticação do SQL Server de utilização** e introduza as informações de autenticação.
   4. No **ligar a uma base de dados** secção, selecione uma base de dados existente do SQL Data Warehouse.
   5. Clique em **Testar ligação**.
   6. Na caixa de diálogo reporta os resultados do teste de ligação, clique em **OK** para voltar para o **Gestor de ligações** caixa de diálogo.
   7. No **Gestor de ligações** caixa de diálogo, clique em **OK** para voltar para o **configurar Gestor de ligação do ADO.NET** caixa de diálogo.
5. No **configurar Gestor de ligação do ADO.NET** caixa de diálogo, clique em **OK** para regressar ao **ADO.NET destino Editor**.
6. No **ADO.NET destino Editor**, clique em **novo** junto ao **utilizar uma tabela ou vista** lista para abrir o **Create Table** caixa de diálogo para criar uma nova tabela de destino com uma lista de colunas que corresponde à tabela de origem.
   
    ![][12a]
7. No **Create Table** diálogo caixa, efetue os seguintes procedimentos.
   
   1. Alterar o nome da tabela de destino para **SalesOrderDetail**.
   2. Remova o **rowguid** coluna. O **uniqueidentifier** tipo de dados não é suportado no SQL Data Warehouse.
   3. Alterar o tipo de dados a **LineTotal** coluna **dinheiro**. O **decimal** tipo de dados não é suportado no SQL Data Warehouse. Para informações sobre os tipos de dados suportadas, consulte [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Clique em **OK** para criar a tabela e voltar para o **ADO.NET destino Editor**.
8. No **ADO.NET destino Editor**, selecione o **mapeamentos** separador para ver como colunas de origem são mapeadas para colunas de destino.
   
    ![][13]
9. Clique em **OK** para concluir a configuração a origem de dados.

## <a name="step-6-run-the-package-to-load-the-data"></a>Passo 6: Executar o pacote para carregar os dados
Execute o pacote ao clicar no **iniciar** botão na barra de ferramentas ou selecionando uma do **executar** opções no **depurar** menu.

À medida que o pacote começa a executar, vê amarela girar rodas, para indicar atividade, bem como o número de linhas processadas até ao momento.

![][14]

Quando o pacote tiver terminado, verá a marca de verificação verde para indicar êxito, bem como o número total de linhas de dados carregados a partir da origem para o destino.

![][15]

Parabéns! Utilizou com êxito os serviços de integração do SQL Server para carregar dados para o Azure SQL Data Warehouse.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o fluxo de dados SSIS. Comece por aqui: [do fluxo de dados][Data Flow].
* Saiba como depurar e o direito de pacotes no ambiente de estrutura de resolução de problemas. Comece por aqui: [ferramentas de resolução de problemas de mensagens em fila para o desenvolvimento de pacote][Troubleshooting Tools for Package Development].
* Saiba como implementar os projetos SSIS e os pacotes para o servidor do Integration Services ou para outra localização de armazenamento. Comece por aqui: [de projetos de implementação do e pacotes][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
