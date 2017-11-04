---
title: "Fábrica de dados do Azure - Samples"
description: "Fornece detalhes sobre os exemplos que são enviados juntamente com o serviço do Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4241aedebd388f24834e573fac7a8a61371e728a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-factory---samples"></a>Fábrica de dados do Azure - Samples
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [exemplos do PowerShell na versão 2 do Data Factory](../samples-powershell.md) e [exemplos na galeria do Azure exemplos de código de código](https://azure.microsoft.com/en-us/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exemplos em GitHub
O [repositório do GitHub do Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam a rapidamente preparação com o serviço Azure Data Factory (ou) modificar os scripts e utilizá-lo na própria aplicação. A pasta de Samples\JSON contém fragmentos JSON para cenários comuns.

| Exemplo | Descrição |
|:--- |:--- |
| [Instruções ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Este exemplo fornece uma instruções ponto a ponto para processar ficheiros de registo utilizando o Azure Data Factory para ativar os dados de ficheiros de registo às informações. <br/><br/>Esta explicação passo a passo, o pipeline de fábrica de dados recolhe registos de exemplo, processos e otimiza os dados de registos com dados de referência e transforma os dados para avaliar a eficácia de uma campanha de marketing que recentemente foi iniciada. |
| [Exemplos JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Este exemplo fornece exemplos JSON para cenários comuns. |
| [Exemplo de dispositivo de transferência de dados de HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Este exemplo showcases durante a transferência de dados de um ponto final de HTTP para armazenamento de Blobs do Azure com a atividade personalizada do .NET. |
| [Entre o ponto de AppDomain atividade de rede de exemplo](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Este exemplo permite-lhe criar uma atividade personalizada do .NET que não está restringida para versões de assemblagem utilizadas pelo iniciador do ADF (por exemplo, Windowsazure v4.3.0 newtonsoft v6.0.x, etc.). |
| [Execute o R script](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Este exemplo inclui a atividade personalizada de fábrica de dados que pode ser utilizada para invocar RScript.exe. Este exemplo funciona apenas com o seu próprio cluster do HDInsight (não a pedido) que já tenha o R instalado no mesmo. |
| [Invocação de tarefas do Spark no cluster de Hadoop do HDInsight](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Este exemplo mostra como utilizar a atividade de MapReduce para invocar um programa de Spark. O programa de spark apenas copia dados a partir de um contentor de Blob do Azure para outro. |
| [Análise de twitter com atividade de classificação de lote do Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Este exemplo mostra como utilizar AzureMLBatchScoringActivity para invocar um modelo do Azure Machine Learning que efetua a análise de sentimento do twitter, classificação de predição, etc. |
| [Análise de twitter utilizar atividades personalizadas](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Este exemplo mostra como utilizar uma atividade personalizada do .NET para invocar um modelo do Azure Machine Learning que efetua a análise de sentimento do twitter, classificação de predição, etc. |
| [Parametrizada Pipelines do Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |O exemplo fornece um código c# ponto-a-ponto para implementar pipelines N para classificação e reparametrização cada um com um parâmetro de região diferente em que a lista de regiões for proveniente de um ficheiro de parameters.txt, que está incluído com este exemplo. |
| [Referência de atualização de dados para tarefas do Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Este exemplo mostra como utilizar o Azure Data Factory e Azure Stream Analytics em conjunto, para executar consultas com dados de referência e configurar a atualização de dados de referência com base numa agenda. |
| [Pipeline de híbrida com o Hadoop de Hortonworks no local](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Este exemplo utiliza um cluster de Hadoop no local como um destino de computação para a execução de tarefas na fábrica de dados, tal como faria adicionar outros destinos de computação, tal como um HDInsight baseado em cluster do Hadoop na nuvem. |
| [Ferramenta de conversão de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Esta ferramenta permite-lhe converter JSONs de versão antes de pré-visualização 2015-07-01-a mais recente ou 2015-07-01-preview (predefinição). |
| [Ficheiro de entrada de exemplo do U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Este ficheiro é um ficheiro de exemplo utilizado por uma atividade U-SQL. |
| [Eliminar o ficheiro de blob](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Este exemplo showcases um ficheiro de c# que pode ser utilizado como parte da atividade de .net personalizada ADF para eliminar os ficheiros da origem de localização de Blob do Azure, uma vez que os ficheiros foram copiados.|

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Pode encontrar os seguintes modelos Azure Resource Manager para a fábrica de dados no GitHub.

| Modelo | Descrição |
| --- | --- |
| [Copiar do armazenamento de BLOBs para base de dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A implementar este modelo cria um Azure data factory com um pipeline que copia dados a partir do armazenamento de Blobs do Azure especificado para a base de dados SQL do Azure |
| [Copiar do Salesforce ao armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A implementar este modelo cria um Azure data factory com um pipeline que copia dados da conta do Salesforce especificada para o armazenamento de Blobs do Azure. |
| [Transformar dados executando o script de ramo de registo num cluster do Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A implementar este modelo cria um Azure data factory com um pipeline que transforma os dados ao executar o script de ramo de registo de exemplo num cluster do Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Amostras no portal do Azure
Pode utilizar o **pipelines de exemplo** mosaico na home page da fábrica de dados para implementar pipelines de exemplo e as entidades associadas (conjuntos de dados e serviços ligados) na sua fábrica de dados.

1. Criar uma fábrica de dados ou abrir a fábrica de dados existente. Consulte [copiar dados de armazenamento de BLOBs para base de dados SQL utilizando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter os passos criar uma fábrica de dados.
2. No **DATA FACTORY** painel data Factory, clique em de **pipelines de exemplo** mosaico.

    ![Mosaico de pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. No **pipelines de exemplo** painel, clique em de **exemplo** que pretende implementar.

    ![Painel de pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especificar definições de configuração para o exemplo. Por exemplo, a chave de conta e o nome de conta do storage do Azure, nome do servidor SQL do Azure, base de dados, ID de utilizador e palavra-passe, etc.

    ![Painel de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar de especificar as definições de configuração, clique em **criar** criar/implementar o exemplo pipelines e serviços/tabelas ligadas utilizadas pelo pipelines.
6. Ver o estado de implementação no mosaico de exemplo em que clicou no anteriormente o **pipelines de exemplo** painel.

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir o **implementação concluída com êxito** mensagem no mosaico para o exemplo, feche o **pipelines de exemplo** painel.  
8. No **DATA FACTORY** painel, pode ver que os serviços ligados, conjuntos de dados e pipelines são adicionadas à fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Amostras no Visual Studio
### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado no computador:

* Visual Studio 2013 ou Visual Studio 2015
* Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
* Transfira o plug-in mais recente do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se estiver a utilizar o Visual Studio 2013, também poderá atualizar o plug-in. Para tal, realize os seguintes passos: no menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**.

### <a name="use-data-factory-templates"></a>Utilizar modelos de fábrica de dados
1. Clique em **ficheiro** no menu, aponte para **novo**e clique em **projeto**.
2. No **novo projeto** diálogo caixa, efetue os seguintes passos:

   1. Selecione **DataFactory** em **modelos**.
   2. Selecione **modelos de fábrica de dados** no painel direito.
   3. Introduza um **nome** para o projeto.
   4. Selecione um **localização** para o projeto.
   5. Clique em **OK**.

      ![Caixa de diálogo Novo Projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. No **modelos de fábrica de dados** diálogo caixa, selecione o modelo de exemplo do **modelos de caso de utilização** secção e clique em **seguinte**. Os seguintes passos guiá-lo utilizando o **a criação de perfis de cliente** modelo. Os passos são semelhantes de outros exemplos.

    ![Caixa de diálogo de modelos de fábrica de dados](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. No **configuração de fábrica de dados** caixa de diálogo, clique em **seguinte** no **Noções básicas de fábrica de dados** página.
5. No **configurar fábrica de dados** página, efetue os seguintes passos:
   1. Selecione **criar nova fábrica de dados**. Também pode selecionar **utilizar fábrica de dados existente**.
   2. Introduza um **nome** da fábrica de dados.
   3. Selecione o **subscrição do Azure** no qual pretende a fábrica de dados ser criada.
   4. Selecione o **grupo de recursos** da fábrica de dados.
   5. Selecione o **EUA oeste**, **EUA Leste**, ou **Europa do Norte** para o **região**.
   6. Clique em **Seguinte**.
6. No **configurar arquivos de dados** página, especifique um existente **SQL database do Azure** e **conta do storage do Azure** (ou) crie/armazenamento de base de dados e clique em seguinte.
7. No **configurar computação** página, selecione as predefinições e clique em **seguinte**.
8. No **resumo** página, consulte todas as definições e clique em **seguinte**.
9. No **estado da implementação** , aguarde pela conclusão da implementação e, em **concluir**.
10. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**.
11. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
12. Deverá ver a seguinte caixa de diálogo:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na página **Configurar fábrica de dados**, execute os seguintes passos:

    1. Confirme que **utilizar fábrica de dados existente** opção.
    2. Selecione o **fábrica de dados** tinha selecione ao utilizar o modelo.
    3. Clique em **Seguinte** para mudar para a página **Publicar Itens**. (Prima **Tecla de Tabulação** para sair do campo Nome se o botão **Seguinte** estiver desativado.)
14. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.     
15. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
16. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.

Consulte [criar a primeira fábrica de dados (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como utilizar o Visual Studio para criar entidades do Data Factory e publicando-os para o Azure.          
