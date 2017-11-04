---
title: "Fábrica de dados do Azure - perguntas mais frequentes"
description: Perguntas mais frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 47ebebbd838d245c2559bff8d8750e80dbcc3fd8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Fábrica de dados do Azure - perguntas mais frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [perguntas mais frequentes pergunta - versão 2 do Data Factory](../frequently-asked-questions.md).

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
Fábrica de dados é uma nuvem com base no serviço de integração de dados que **automatiza o movimento e a transformação de dados**. Tal como uma fábrica que opera equipamento para tomar raw materiais e transformá-los em bens acabados, Data Factory orquestra os serviços existentes que recolhem dados não processados e transformação-los em informações prontas a utilizar.

Fábrica de dados permite-lhe criar fluxos de trabalho condicionada por dados para mover dados entre ambos no local e arquivos de dados de nuvem, bem como os dados de processo/transformação utilizando os serviços de computação, tais como o Azure HDInsight e o Azure Data Lake Analytics. Depois de criar um pipeline que executa a ação que é necessário, pode agendar para execução periodicamente (hora a hora, diária, semanal, etc.).   

Para obter mais informações, consulte [descrição geral e conceitos chave](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar os detalhes de preços do Azure Data Factory?
Consulte [página de detalhes de preços de fábrica de dados] [ adf-pricing-details] para os detalhes dos preços do Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como começar a utilizar com o Azure Data Factory?
* Para obter uma descrição geral do Azure Data Factory, consulte [introdução ao Azure Data Factory](data-factory-introduction.md).
* Para um tutorial sobre como **copiar/mover dados** utilizando a atividade de cópia, consulte [copiar dados do Blob Storage do Azure para a SQL Database do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para um tutorial sobre como **transformar dados** utilizando a atividade do ramo de registo do HDInsight. Consulte [processar dados executando o script de ramo de registo num cluster de Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>O que é a disponibilidade de região de fábrica de dados?
Fábrica de dados está disponível no **dos EUA oeste** e **Europa do Norte**. Os serviços de computação e armazenamento utilizados pelo fábricas de dados podem ser noutras regiões. Consulte [regiões suportadas](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites no número de data factories/pipelines/atividades/conjuntos de dados?
Consulte **limites de fábrica de dados do Azure** secção o [subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md#data-factory-limits) artigo.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>O que é a experiência de criação/programador com o serviço Azure Data Factory?
Pode criar/criar fábricas de dados utilizando uma das ferramentas/SDKs seguintes:

* **Portal do Azure** painéis do Data Factory no portal do Azure fornecem a interface de utilizador avançada para a criação de serviços de ad ligado de fábricas de dados. O **Editor do Data Factory**, que também faz parte do portal, permite-lhe criar facilmente serviços ligados, tabelas, conjuntos de dados e pipelines com a especificação de definições JSON destes artefactos. Consulte [construir o seu primeiro pipeline de dados através do portal do Azure](data-factory-build-your-first-pipeline-using-editor.md) para um exemplo de como utilizar o portal/editor para criar e implementar uma fábrica de dados.
* **Visual Studio** pode utilizar o Visual Studio para criar um Azure data factory. Consulte [construir o seu primeiro pipeline de dados com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obter mais detalhes.
* **O Azure PowerShell** consulte [criar e monitor do Azure Data Factory com o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para uma tutorial instruções para criar uma fábrica de dados com o PowerShell. Consulte [referência de Cmdlet do Data Factory] [ adf-powershell-reference] o conteúdo na biblioteca do MSDN para a documentação completa dos cmdlets do Data Factory.
* **Biblioteca de classe de .NET** através de programação podem criar fábricas de dados utilizando o SDK .NET da fábrica de dados. Consulte [criar, monitorizar e gerir fábricas de dados com o .NET SDK](data-factory-create-data-factories-programmatically.md) para obter instruções sobre a criação de uma fábrica de dados com o .NET SDK. Consulte [referência da biblioteca do Data Factory classe] [ msdn-class-library-reference] para a documentação completa do SDK do .NET de fábrica de dados.
* **REST API** também pode utilizar a API de REST exposta pelo serviço do Azure Data Factory para criar e implementar as fábricas de dados. Consulte [referência de API de REST do Data Factory] [ msdn-rest-api-reference] para a documentação completa da API de REST de fábrica de dados.
* **Modelo Azure Resource Manager** consulte [Tutorial: criar a primeira fábrica de dados do Azure utilizando o modelo Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) detalhes fo.

### <a name="can-i-rename-a-data-factory"></a>Pode mudar o nome de uma fábrica de dados?
Não. Como outros recursos do Azure, não é possível alterar o nome de um Azure data factory.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma fábrica de dados de uma subscrição do Azure para outro?
Sim. Utilize o **mover** botão no painel da fábrica de dados, conforme mostrado no diagrama seguinte:

![Mover a fábrica de dados](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes de computação suportados pela fábrica de dados?
A tabela seguinte fornece uma lista de ambientes de computação suportada pela fábrica de dados e as atividades que podem ser executadas nos mesmos.

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md) |
| [O Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Análise do Azure Data Lake](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [SQL do Azure](data-factory-compute-linked-services.md#azure-sql-linked-service), [do armazém de dados SQL do Azure](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [do SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento Armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como é que o Azure Data Factory comparar com o SQL Server Integration Services (SSIS)? 
Consulte o [vs do Azure Data Factory. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) apresentação de um dos nossos Most (mais valores profissionais): Reza Rad. Algumas das alterações recentes na fábrica de dados não podem ser apresentadas no deck gradualmente. Que estamos continuamente a adicionar mais capacidades ao Azure Data Factory. Que estamos continuamente a adicionar mais capacidades ao Azure Data Factory. Iremos irá incorporar estas atualizações a comparação de tecnologias de integração de dados da Microsoft algum tempo ainda este ano.   

## <a name="activities---faq"></a>Atividades - FAQ
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os diferentes tipos de atividades que pode utilizar um pipeline do Data Factory?
* [Atividades de movimentos de dados](data-factory-data-movement-activities.md) para mover os dados.
* [Atividades de transformação de dados](data-factory-data-transformation-activities.md) ao processo/transformação de dados.

### <a name="when-does-an-activity-run"></a>Quando executa uma atividade?
O **disponibilidade** definição de configuração na tabela de dados de saída determina quando a atividade é executada. Se forem especificados conjuntos de dados de entrada, a atividade verifica se forem satisfeitas todas as dependências de dados de entrada (ou seja, **pronto** Estado) antes de começar a ser executado.

## <a name="copy-activity---faq"></a>Copiar atividade - FAQ
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor para ter um pipeline com várias atividades ou de um pipeline separado para cada atividade?
Pipelines são suposto agrupar as atividades relacionadas. Se os conjuntos de dados que os ligam não são consumidos por qualquer outra atividade fora do pipeline, pode manter as atividades no pipeline de um. Desta forma, não terá períodos ativos de pipeline de cadeia para que o se alinham com entre si. Além disso, a integridade dos dados nas tabelas internas para o pipeline melhor é preservada quando atualizar o pipeline. Atualização do pipeline essencialmente deixa de todas as atividades no pipeline, remove-os e cria-las novamente. Da perspetiva de criação, também pode ser mais fácil ver o fluxo de dados dentro de atividades relacionadas com um ficheiro JSON para o pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são os arquivos de dados suportados?
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de ficheiro suportados?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde é executada a operação de cópia?
Consulte [movimento de dados globalmente disponível](data-factory-data-movement-activities.md#global) secção para obter detalhes. Em suma, quando está envolvido num arquivo de dados no local, a operação de cópia é realizada o Data Management Gateway no seu ambiente no local. E, quando o movimento de dados está entre dois arquivos de nuvem, a operação de cópia é executada na região mais próxima para a localização de receptores na geografia a mesma.

## <a name="hdinsight-activity---faq"></a>Atividade do HDInsight - FAQ
### <a name="what-regions-are-supported-by-hdinsight"></a>As regiões são suportadas pelo HDInsight?
Consulte a secção de disponibilidade geográfica no seguinte artigo: ou [detalhes dos preços do HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Que região é utilizado por um cluster do HDInsight a pedido?
O cluster do HDInsight a pedido é criado na mesma região onde o armazenamento que especificou para ser utilizado com o cluster existe.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar as contas de armazenamento adicional ao cluster do HDInsight?
Se estiver a utilizar o seu próprio Cluster do HDInsight (BYOC - traga seu próprio Cluster), consulte os seguintes tópicos:

* [Utilizar um Cluster do HDInsight com contas de armazenamento alternativo e Metastores][hdinsight-alternate-storage]
* [Utilizar contas de armazenamento adicional com o Hive do HDInsight][hdinsight-alternate-storage-2]

Se estiver a utilizar um cluster a pedido, que é criado pelo serviço Data Factory, especifique as contas de armazenamento adicional para o HDInsight serviço ligado para que o serviço fábrica de dados pode registá-los em seu nome. Na definição de JSON para o serviço ligado a pedido, utilize **additionalLinkedServiceNames** propriedade para especificar as contas de armazenamento alternativo, conforme ilustrado no fragmento JSON seguinte:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços ligados cujas definições contêm as credenciais que o cluster do HDInsight tem de aceder às contas de armazenamento alternativo.

## <a name="slices---faq"></a>Setores - FAQ
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Por que razão são os meus setores de entrada não está num estado pronto?
Um erro comum não é a definição **externo** propriedade **verdadeiro** no conjunto de dados entrado quando os dados de entrada são externos à fábrica de dados (não produzida pela fábrica de dados).

No exemplo seguinte, só tem de definir **externo** como true no **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3 -> activity3 -> dataset4

Se tiver outra fábrica de dados com um pipeline que demora dataset4 (produzido pelo pipeline 2 na fábrica de dados 1), marcar dataset4 como um conjunto de dados externo porque o conjunto de dados é produzido por uma fábrica de dados diferentes (DataFactory1, não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4 activity4 -> -> dataset5

Se a propriedade externa está corretamente definida, verifique se os dados de entrada existem na localização especificada na definição do conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como executar um setor noutra altura à meia-noite quando o setor está a ser produzido diariamente?
Utilize o **desvio** propriedade para especificar o tempo em que pretende que o setor ser produzidos. Consulte [disponibilidade do conjunto de dados](data-factory-create-datasets.md#dataset-availability) secção para obter detalhes sobre esta propriedade. Eis um breve exemplo:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Iniciar o setores diárias **6: 00** em vez da meia-noite predefinido.     

### <a name="how-can-i-rerun-a-slice"></a>Como posso executar um setor?
Pode voltar a executar um setor de uma das seguintes formas:

* Utilize a monitorizar e gerir aplicações para voltar a executar uma janela de atividade ou o setor. Consulte [Reexecução selecionado windows atividade](data-factory-monitor-manage-app.md#perform-batch-actions) para obter instruções.   
* Clique em **executar** na barra de comando no **SETOR de dados** painel para o setor no portal do Azure.
* Executar **Set-AzureRmDataFactorySliceStatus** cmdlet com o estado definido como **a aguardar** para o setor.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Consulte [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto demora a processar um setor?
Utilize o Explorador de janela de atividade no Monitor e gerir aplicações para saber quanto tempo demorou a processar um setor de dados. Consulte [Explorador de janela de atividade](data-factory-monitor-manage-app.md#activity-window-explorer) para obter mais detalhes.

Também pode fazer o seguinte no portal do Azure:  

1. Clique em **conjuntos de dados** mosaico a **DATA FACTORY** painel para a fábrica de dados.
2. Clique o conjunto de dados específico no **conjuntos de dados** painel.
3. Selecione o setor que está interessado em do **ciclos recentes** lista o **tabela** painel.
4. Clique na atividade executar a partir de **execuções de atividade** lista o **SETOR de dados** painel.
5. Clique em **propriedades** mosaico a **detalhes da execução da ATIVIDADE** painel.
6. Deverá ver o **duração** campo com um valor. Este valor é o tempo decorrido para processar o setor.   

### <a name="how-to-stop-a-running-slice"></a>Como parar um setor em execução?
Se precisar de parar o pipeline de execução, pode utilizar [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) cmdlet. Atualmente, a suspensão do pipeline não parar as execuções de setor que estão em curso. Depois de concluir as execuções de em curso, nenhum setor adicional é captado.

Se a certeza de que pretende parar todas as execuções de imediato, a única forma de seria eliminar o pipeline e criá-la novamente. Se optar por eliminar o pipeline, não terá de eliminar tabelas e os serviços ligados utilizados pelo pipeline.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
