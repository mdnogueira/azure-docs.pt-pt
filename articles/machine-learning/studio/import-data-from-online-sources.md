---
title: Importar dados para o Machine Learning Studio a partir de origens de dados online | Microsoft Docs
description: "Como importar os dados de formação do Azure Machine Learning Studio a partir de várias origens online."
keywords: "Importar dados, o formato de dados, os tipos de dados, as origens de dados, dados de formação"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
ms.openlocfilehash: f58bd7206bb19328635b9834ca81bcdf692882c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importar dados para o Azure Machine Learning Studio provenientes de várias origens de dados online com o módulo Importar Dados
Este artigo descreve o suporte para importar dados online a partir de várias origens e as informações necessárias para mover dados a partir destas origens para uma experimentação do Azure Machine Learning.

> [!NOTE]
> Este artigo fornece informações gerais sobre o [importar dados] [ import-data] módulo. Para obter mais informações sobre os tipos de dados pode aceder, formatos, parâmetros e as respostas a perguntas comuns, consulte o tópico de referência do módulo para a [importar dados] [ import-data] módulo.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introdução
Utilizando o [importar dados] [ import-data] módulo, pode aceder aos dados de uma das várias origens de dados online durante a execução sua experimentação [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Um URL de Web através de HTTP
* Utilizar o HiveQL de Hadoop
* Armazenamento de Blobs do Azure
* Tabela do Azure
* Base de dados SQL do Azure ou do SQL Server numa VM do Azure
* Base de dados do SQL Server no local
* Um fornecedor de OData atualmente de feed de dados
* CosmosDB do Azure (anteriormente denominadas DocumentDB)

Para aceder a origens de dados online na sua experimentação do Studio, adicione o [importar dados] [ import-data] módulo para o, selecione o **origem de dados**e, em seguida, forneça os parâmetros necessários para aceder a os dados. As origens de dados online são suportadas estão descritas na tabela abaixo. Nesta tabela também resume os formatos de ficheiro suportados e os parâmetros que são utilizados para aceder aos dados.

Tenha em atenção que porque estes dados de formação são acedidos durante a execução da sua experimentação, só está disponível nessa experiência. Por comparação, os dados que tenham sido armazenados num módulo conjunto de dados estão disponíveis para qualquer experimentação na sua área de trabalho.

> [!IMPORTANT]
> Atualmente, o [importar dados] [ import-data] e [exportar dados] [ export-data] módulos podem ler e escrever dados apenas a partir do armazenamento do Azure criado com clássica modelo de implementação. Por outras palavras, o novo tipo de conta de armazenamento de Blobs do Azure oferece uma camada de acesso de armazenamento frequente ou camada de acesso de armazenamento esporádico é ainda não suportado. 
> 
> Geralmente, as contas de armazenamento do Azure que pode ter criado antes desta opção de serviço ficou disponível a não deve ser afetado. 
> Se precisar de criar uma nova conta, selecione **clássico** para a implementação de modelo, ou utilize o Gestor de recursos e selecione **fins gerais** vez **armazenamento de BLOBs** para  **Tipo de conta**. 
> 
> Para obter mais informações, consulte [Blob Storage do Azure: camadas de armazenamento frequente e esporádico](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Origens de dados online suportadas
O Azure Machine Learning **importar dados** módulo suporta as seguintes origens de dados:

| Origem de dados | Descrição | Parâmetros |
| --- | --- | --- |
| URL da Web através de HTTP |Lê os dados no ficheiro de valores separados por vírgulas (CSV), separador com valores separados por (TSV), o formato de ficheiro de relação de atributo (ARFF) e formatos de Vetor de suporte máquinas (SVM leve), a partir de qualquer URL de web que utiliza HTTP |<b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome de ficheiro, com uma extensão. <br/><br/><b>Formato de dados</b>: Especifica um dos dados suportados formatos: CSV, TSV, ARFF ou SVM leve. Se os dados tem uma linha de cabeçalho, é utilizado para atribuir nomes de coluna. |
| Hadoop/HDFS |Lê os dados de armazenamento distribuído no Hadoop. Especifique os dados que pretende utilizando HiveQL, uma linguagem de consulta como o SQL Server. Também pode ser utilizado o HiveQL para agregar dados e executar dados filtragem antes de adicionar os dados ao Machine Learning Studio. |<b>Consulta de base de dados de ramo de registo</b>: Especifica a consulta do Hive utilizada para gerar os dados.<br/><br/><b>URI do servidor de HCatalog </b> : especificado o nome do cluster utilizando o formato  *&lt;o nome do cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome de conta de utilizador do Hadoop</b>: Especifica o nome de conta de utilizador do Hadoop utilizado para aprovisionar o cluster.<br/><br/><b>Palavra-passe de conta de utilizador Hadoop</b> : Especifica as credenciais utilizadas quando aprovisionar o cluster. Para obter mais informações, consulte [clusters do Hadoop criar no HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Localização dos dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuído do Hadoop (HDFS) ou no Azure. <br/><ul>Se armazenar dados de saída no HDFS, especifique o URI do servidor do HDFS. (Lembre-se de que utiliza o nome do cluster de HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os dados de saída no Azure, tem de especificar o nome da conta de armazenamento do Azure, a chave de acesso de armazenamento e o nome de contentor de armazenamento.</ul> |
| Base de dados SQL |Lê os dados que estão armazenados numa base de dados SQL do Azure ou numa base de dados do SQL Server em execução numa máquina virtual do Azure. |<b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><ul>Em caso de SQL Database do Azure, introduza o nome do servidor que é gerado. Normalmente, tem o formato  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Em caso de um servidor SQL alojado numa máquina Virtual do Azure, introduza *tcp:&lt;nome de DNS da Máquina Virtual&gt;, 1433*</ul><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Palavra-passe de conta de utilizador do</b>: Especifica a palavra-passe da conta de utilizador.<br/><br/><b>Aceitar qualquer certificado de servidor</b>: Utilize esta opção (menos segura), se pretender ignorar a rever o certificado de site antes de ler os dados.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução de SQL que descreve os dados que pretende ler. |
| Base de dados do SQL Server no local |Lê os dados que estão armazenados numa base de dados SQL no local. |<b>Gateway de dados</b>: Especifica o nome do Data Management Gateway instalado num computador onde porque pode aceder à sua base de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte [executar análise avançada com o Azure Machine Learning utilizando dados de um servidor SQL no local](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Nome de utilizador e palavra-passe</b>: clique em <b>introduza valores</b> insira as credenciais da base de dados. Pode utilizar a autenticação integrada do Windows ou a autenticação de servidor de SQL consoante o modo como o SQL Server no local está configurado.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução de SQL que descreve os dados que pretende ler. |
| Tabela do Azure |Lê os dados do serviço tabela no armazenamento do Azure.<br/><br/>Se ler grandes quantidades de dados com pouca frequência, utilize o serviço de tabela do Azure. Fornece flexível e não relacionais (NoSQL), a solução de armazenamento extremamente dimensionável, económico e extremamente disponível. |As opções de **importar dados** alterar, dependendo se está a aceder ao informações públicas ou uma conta de armazenamento privada que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Da conta", cada um dos quais tem o seu próprio conjunto de parâmetros. <br/><br/><b>Pública ou de acesso partilhado assinatura (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>Tabela URI</b>: Especifica o público ou o SAS URL para a tabela.<br/><br/><b>Especifica as linhas para procurar os nomes de propriedade</b>: os valores são <i>TopN</i> para analisar o número de linhas, especificado ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados são homogénea e previsível, é recomendável que selecione *TopN* e introduza um número de N. Para tabelas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam consoante a profundidade e a posição da tabela, escolha o *ScanAll* opção para analisar todas as linhas. Isto assegura a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/></ul><b>Conta de armazenamento privada</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela a ler.<br/><br/><b>Chave de conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados para ler.<br/><br/><b>As linhas para procurar os nomes de propriedade</b>: os valores são <i>TopN</i> para analisar o número de linhas, especificado ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados são homogénea e previsível, recomendamos que selecione *TopN* e introduza um número de N. Para tabelas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam consoante a profundidade e a posição da tabela, escolha o *ScanAll* opção para analisar todas as linhas. Isto assegura a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço Blob no Storage do Azure, incluindo as imagens, não estruturado, dados text ou binários.<br/><br/>Pode utilizar o serviço de BLOBs para expor publicamente os dados ou em privado armazenar dados da aplicação. Pode a aceder aos seus dados a partir de qualquer lugar, utilizando ligações HTTP ou HTTPS. |As opções de **importar dados** módulo alterar, dependendo se está a aceder ao informações públicas ou uma conta de armazenamento privada que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Conta".<br/><br/><b>Pública ou de acesso partilhado assinatura (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o público ou o URL de SAS do storage blob.<br/><br/><b>Formato de ficheiro</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento privada</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém o blob que pretende ler.<br/><br/><b>Chave de conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contentor, diretório ou blob </b> : Especifica o nome do blob que contém os dados para ler.<br/><br/><b>Formato de ficheiro do blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportados são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato CSV ou TSV, lembre-se de que indicam se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção de Excel ler dados de livros do Excel. No <i>formato de dados do Excel</i> opção, indique se os dados são um intervalo de folha de cálculo do Excel ou uma tabela do Excel. No <i>folha do Excel ou tabela incorporada </i>opção, especifique o nome da folha ou tabela que pretende ler a partir do.</ul><br/> |
| Fornecedor de Feed de dados |Lê os dados de um fornecedor de feed suportado. Atualmente apenas o formato da Open Data Protocol (OData) é suportado. |<b>Tipo de conteúdo de dados</b>: Especifica o formato de OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo para os feed de dados. <br/>Por exemplo, o seguinte URL lê a partir da base de dados de exemplo Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Passos seguintes

[Implementação do Azure ML web dos serviços que utilizam módulos de dados de exportação e importação de dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
