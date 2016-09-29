<properties 
    pageTitle="Tutorial: Criar um pipeline com o Assistente de Cópia" 
    description="Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Assistente de Cópia suportado pelo Data Factory" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" **
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# Tutorial: Criar um pipeline com Atividade de Cópia com o Assistente de Cópia do Data Factory
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Neste tutorial, irá utilizar o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia numa fábrica de dados. Em primeiro lugar, crie uma fábrica de dados utilizando o portal do Azure. Em seguida, utilize o Assistente de Cópia para criar serviços ligados ao Data Factory, conjuntos de dados e um pipeline com uma Atividade de Cópia para copiar dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 

> [AZURE.IMPORTANT] Veja o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos** antes de executar este tutorial.

## Criar fábrica de dados
Neste passo, irá utilizar o Portal do Azure para criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**.

1.  Depois de iniciar sessão no [Portal do Azure](https://portal.azure.com), clique em **+NOVO** no canto superior esquerdo, selecione **Análise de dados** no painel **Criar** e clique em **Data Factory** no painel **Análise de dados**. 

    ![Novo -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. No painel **Nova fábrica de dados**:
    1. Introduza **ADFTutorialDataFactory** como **nome**. 
    
        ![Painel Nova fábrica de dados](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
    2. Clique em **NOME DO GRUPO DE RECURSOS** e execute os seguintes passos:
        1. Clique em **Criar um novo grupo de recursos**.
        2. No painel **Criar grupo de recursos**, introduza **ADFTutorialResourceGroup** como o **nome** do grupo de recursos e clique em **OK**. 

            ![Criar Grupo de Recursos](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

        Alguns dos passos deste tutorial pressupõe que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../resource-group-overview.md).  
7. No painel **Nova fábrica de dados**, repare que a opção **Adicionar ao Startboard** está selecionada.
8. Clique em **Criar** no painel **Nova fábrica de dados**.

    O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **Nome "ADFTutorialDataFactory" da fábrica de dados não disponível**, altere o nome (por exemplo, seunomeADFTutorialDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
     
    ![Nome do Data Factory não disponível](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
    
    > [AZURE.NOTE] O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.  

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure notificações do processo de criação. Clique em **X** para fechar o painel **NOTIFICAÇÕES** se estiver aberto. 
10. Após concluir a criação, verá o painel **DATA FACTORY**, conforme apresentado na imagem seguinte:

    ![Home page da fábrica de dados](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Criar pipeline

1. Na home page do Data Factory, clique no mosaico **Copiar dados** para iniciar o **Assistente de Cópia**. 

    > [AZURE.NOTE] Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
2. Na página **Propriedades**:
    1. Introduza **CopyFromBlobToAzureSql** para **Nome da tarefa**
    2. Introduza **Descrição** (opcional).
    3. Tenha em atenção a **Data e hora de início** e a **Data e hora de fim**. Altere a **Data e hora de fim** para o dia seguinte à **Data e hora de início**. 
    3. Clique em **Seguinte**.  

    ![Página Ferramenta Copiar – Propriedades](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para utilizar um serviço ligado existente, clique em **A PARTIR DE SERVIÇOS LIGADOS EXISTENTES** e selecione o serviço ligado correto. 

    ![Página Ferramenta Copiar – Arquivo de dados de origem](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. Na página **Especificar a conta de armazenamento de blobs do Azure**:
    1. Introduza **AzureStorageLinkedService** para **Nome do serviço ligado**.
    2. Confirme a opção **A partir de subscrições do Azure** para o **Método de seleção de contas**. 
    3. Selecione uma **Conta de armazenamento do Azure** na lista de contas de armazenamento do Azure disponível na subscrição que selecionou. Também pode optar por introduzir manualmente as definições da conta de armazenamento selecionando a opção **Introduzir manualmente** para **Método de seleção de contas** e, em seguida, clicar em **Seguinte**. 

    ![Ferramenta Copiar – Especificar a conta de armazenamento de blobs do Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. Na página **Escolher o ficheiro ou pasta de entrada**:
    1. Navegue até à pasta **adftutorial**.
    2. Selecione **emp.txt** e clique em **Escolher**
    3. Clique em **Seguinte**. 

    ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. Na página **Escolher o ficheiro ou pasta de entrada**, clique em **Seguinte**. Não selecione **Cópia binária**. 

    ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
8. Na página **Definições do formato de ficheiro**, selecione os valores **predefinidos** e clique em **Seguinte**.

    ![Ferramenta Copiar – Definições do formato de ficheiro](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Na página Armazenamento de dados de destino, clique no mosaico ** SQL Database do Azure** e em **Seguinte**.
9. Na página **Especificar a base de dados SQL do Azure**:
    1. Introduza **AzureSqlLinkedService** no campo **Nome do serviço ligado**. 
    2. Confirme que o **Método de seleção do servidor/base de dados** está definido como **A partir de subscrições do Azure**.
    3. Selecione **Nome do servidor** e **Base de dados**.
    4. Introduza o **Nome de utilizador** e a **Palavra-passe**.
    5. Clique em **Seguinte**.  
9. Na página **Mapeamento de tabelas**, selecione **emp** para o campo **Destino** na lista pendente, clique na **seta para baixo** (opcional) para ver o esquema e pré-visualizar os dados.

    ![Ferramenta Copiar – Mapeamento da tabelas](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. Na página **Mapeamento de esquemas**, clique em **Seguinte**.
11. Na página **Definições de desempenho**, clique em **Seguinte**. 
11. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia). 
12. Na página **Implementação com êxito**, clique na ligação: **Clicar aqui para monitorizar o pipeline de cópia**.

    ![Ferramenta Copiar – Implementação concluída com êxito](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. Siga as instruções em [Monitorizar e gerir o pipeline com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) para saber como monitorizar o pipeline que acabou de criar. Clique no ícone **Atualizar** na lista **JANELAS DE ATIVIDADE** para ver o setor. 

    ![Aplicação de Monitorização](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png) 
 

## Veja também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 


<!--HONumber=Sep16_HO3-->


