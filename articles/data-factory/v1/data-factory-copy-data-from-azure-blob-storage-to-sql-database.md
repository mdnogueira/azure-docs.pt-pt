---
title: Copiar dados de armazenamento de BLOBs para base de dados do SQL Server - Azure | Microsoft Docs
description: "Este tutorial mostra como utilizar a atividade de cópia num pipeline do Azure Data Factory para copiar dados de armazenamento de BLOBs para base de dados SQL."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: c3a2d4b126d43017ec004bde82deb190584455c4
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Copiar dados do armazenamento de BLOBs para base de dados SQL utilizando o Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [tutorial de atividade de cópia na documentação da versão 2](../quickstart-create-data-factory-dot-net.md). 

Neste tutorial, vai criar uma fábrica de dados com um pipeline para copiar dados de armazenamento de BLOBs para base de dados SQL.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para obter uma descrição detalhada do serviço Data Factory, consulte o [introdução ao Azure Data Factory](data-factory-introduction.md) artigo.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

* **Subscrição do Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) artigo para obter detalhes.
* **Conta de armazenamento do Azure**. Utilizar o armazenamento de blob como uma **origem** do arquivo de dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter os passos para criar uma.
* **Base de Dados SQL do Azure**. Utilizar uma base de dados SQL do Azure como um **destino** do arquivo de dados neste tutorial. Se não tiver uma base de dados SQL do Azure que pode utilizar o tutorial, consulte [como criar e configurar uma base de dados do SQL do Azure](../../sql-database/sql-database-get-started.md) para criar um.
* **SQL Server 2012/2014 ou Visual Studio 2013**. Utilize o SQL Server Management Studio ou Visual Studio para criar uma base de dados de exemplo e ver dados de resultados na base de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Recolher o nome de conta do blob storage e chave
Tem o nome de conta e chave de conta da sua conta de armazenamento do Azure para efetuar este tutorial. Tome nota **nome da conta** e **chave da conta** para a sua conta de armazenamento do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços** no menu à esquerda e selecione **contas do Storage**.

    ![Procurar - contas de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. No **contas do Storage** painel, selecione o **conta do storage do Azure** que pretende utilizar neste tutorial.
4. Selecione **chaves de acesso** ligação em **definições**.
5. Clique em **cópia** botão (imagem) junto a **nome da conta de armazenamento** texto caixa e guardar/colá-lo algures (por exemplo: num ficheiro de texto).
6. Repita o passo anterior para copiar ou tome nota do **chave1**.

    ![Chave de acesso de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Fechar todos os painéis clicando **X**.

## <a name="collect-sql-server-database-user-names"></a>Recolher do SQL Server, a base de dados, os nomes de utilizador
Terá dos nomes do servidor SQL do Azure, base de dados e utilizador para efetuar este tutorial. Tome nota dos nomes dos **servidor**, **base de dados**, e **utilizador** da base de dados SQL do Azure.

1. No **portal do Azure**, clique em **mais serviços** à esquerda e selecione **bases de dados SQL**.
2. No **painel de bases de dados do SQL Server**, selecione o **base de dados** que pretende utilizar neste tutorial. Tome nota do **nome de base de dados**.  
3. No **base de dados SQL** painel, clique em **propriedades** em **definições**.
4. Tome nota dos valores para **nome do servidor** e **início de sessão do servidor de ADMIN**.
5. Fechar todos os painéis clicando **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure aceder ao SQL server
Certifique-se de que **permitir o acesso aos serviços do Azure** definição desativada **ON** para o servidor de SQL do Azure para que o serviço fábrica de dados pode aceder ao seu servidor SQL do Azure. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
3. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
4. Fechar todos os painéis clicando **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o armazenamento de BLOBs e a base de dados SQL
Agora, prepare o armazenamento de Blobs do Azure e a SQL database do Azure para o tutorial, efetuando os seguintes passos:  

1. Inicie o Bloco de notas. Copie o seguinte texto e guarde-o como **emp.txt** para **C:\ADFGetStarted** pasta no disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Utilize ferramentas, como o [Explorador do Storage do Azure](http://storageexplorer.com/), para criar o contentor **adftutorial** e carregar o ficheiro **emp.txt** para o contentor.

    ![Explorador de armazenamento do Azure. Copiar dados de armazenamento de BLOBs para base de dados SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilize o seguinte script SQL para criar a tabela **emp** na SQL Database do Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Se tiver o SQL Server 2012/2014 instalado no seu computador:** siga as instruções do [gerir a SQL Database do Azure utilizando o SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) para ligar ao seu servidor SQL do Azure e execute o script SQL. 

    Se o cliente não tiver permissão para aceder ao servidor SQL do Azure, terá de configurar a firewall do seu servidor SQL do Azure para permitir o acesso a partir do seu computador (Endereço IP). Veja [este artigo](../../sql-database/sql-database-configure-firewall-settings.md) para obter os passos para configurar a firewall para o seu servidor SQL do Azure.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Concluiu os pré-requisitos. Pode criar uma fábrica de dados utilizando uma das seguintes formas. Clique das opções na lista pendente na parte superior ou as ligações seguintes para executar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build your first pipeline to transform data using Hadoop cluster (Tutorial: Criar o seu primeiro pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 
