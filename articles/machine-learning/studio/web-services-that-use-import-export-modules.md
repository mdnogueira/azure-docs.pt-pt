---
title: "Utilizar dados de importação/exportação em serviços web do Azure Machine Learning | Microsoft Docs"
description: "Saiba como utilizar os módulos de importar dados e exportar dados para enviar e receber dados de um serviço web."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 40e1dbf4fc1618deec5e7f85fb956f2881e08319
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implementar serviços Web do Azure ML que utilizam módulos Importar Dados e Exportar Dados

Quando cria uma experimentação preditiva, normalmente, adicione uma entrada de serviço web e de saída. Quando implementa a experimentação, os consumidores podem enviar e receber dados do serviço web através de entradas e saídas. Para algumas aplicações, dados de um consumidor poderão estar disponível a partir de um feed de dados ou já residirem numa origem de dados externos, como o armazenamento de Blobs do Azure. Nestes casos, não precisa de ler e escrever dados através do web service entradas e saídas. Podem, em vez disso, utilize o serviço de execução de lote (BES) para ler dados a partir da origem de dados utilizando um módulo de importar dados e escrever os resultados de classificação para uma localização de dados diferentes, utilizando um módulo de exportar dados.

Os dados de importação e exportação de módulos de dados, pode ler a partir do e escrever dados de várias localizações como um URL de Web através de HTTP, uma consulta do Hive, uma SQL database do Azure, Table storage do Azure, Blob storage do Azure, um Feed de dados fornecem ou uma base de dados do SQL Server no local.

Este tópico utiliza o "exemplo 5: Evaluate de comboio, teste, para classificação do binária: conjunto de dados para adultos" de exemplo e pressupõe que já foi carregado o conjunto de dados para uma tabela SQL do Azure com o nome censusdata.

## <a name="create-the-training-experiment"></a>Criar a experimentação de preparação
Ao abrir o "exemplo 5: Evaluate de comboio, teste, para classificação do binária: conjunto de dados para adultos" exemplo utiliza o conjunto de dados de classificação de binário para adultos Census receitas de exemplo. E a experimentação na tela será semelhante a imagem seguinte:

![Configuração inicial da experimentação.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela SQL do Azure:

1. Elimine o módulo de conjunto de dados.
2. Na caixa de pesquisa de componentes, tipo de importação.
3. Na lista de resultados, adicione um *importar dados* módulo para a tela de experimentação.
4. Ligar o resultado a *importar dados* a entrada do módulo do *apagar dados em falta* módulo.
5. No painel de propriedades, selecione **SQL Database do Azure** no **origem de dados** pendente.
6. No **nome do servidor de base de dados**, **nome de base de dados**, **nome de utilizador**, e **palavra-passe** campos, introduza as informações adequadas para o base de dados.
7. No campo de consulta de base de dados, introduza a seguinte consulta.
   
     Selecione [idade]
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     de dbo.censusdata;
8. Na parte inferior da tela de experimentação, clique em **executar**.

## <a name="create-the-predictive-experiment"></a>Criar a experimentação preditiva
Em seguida, configurar a experimentação preditiva a partir da qual implementar o serviço web.

1. Na parte inferior da tela de experimentação, clique em **segurança serviço Web** e selecione **preditiva Web Service [Recomendado]**.
2. Remova o *entrada de serviço Web* e *módulos de saída de serviço Web* da experimentação preditiva. 
3. Na caixa de pesquisa de componentes, escreva a exportação.
4. Na lista de resultados, adicione um *exportar dados* módulo para a tela de experimentação.
5. Ligar o resultado a *Pontuar modelo* a entrada do módulo do *exportar dados* módulo. 
6. No painel de propriedades, selecione **SQL Database do Azure** na lista pendente de destino de dados.
7. No **nome do servidor de base de dados**, **nome de base de dados**, **nome de conta de utilizador do servidor**, e **palavra-passe de conta de utilizador do** campos, introduza o informações adequadas para a base de dados.
8. No **lista de colunas a ser guardado separados por vírgulas** , digite etiquetas classificadas.
9. No **campo de nome de tabela de dados**, escreva dbo. ScoredLabels. Se a tabela não existe, é criado quando a experimentação é executada ou o serviço web é chamado.
10. No **lista de colunas de datatable separados por vírgulas** campo, escreva ScoredLabels.

Quando escreve uma aplicação que chama o serviço final web, pode pretender especificar uma consulta de entrada diferente ou tabela de destino em tempo de execução. Para configurar estas entradas e saídas, utilize a funcionalidade de parâmetros de serviço Web para definir o *importar dados* módulo *origem de dados* propriedade e o *exportar dados* dados do modo de propriedade de destino.  Para obter mais informações sobre os parâmetros do serviço Web, consulte o [entrada de parâmetros de serviço Web de AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) no blogue do Machine Learning e Cortana Intelligence.

Para configurar os parâmetros de serviço Web para a consulta de importação e a tabela de destino:

1. No painel de propriedades para o *importar dados* módulo, clique no ícone na parte superior direita do **consulta de base de dados** campo e selecione **definido como parâmetro de serviço web**.
2. No painel de propriedades para o *exportar dados* módulo, clique no ícone na parte superior direita do **nome da tabela de dados** campo e selecione **definido como parâmetro de serviço web**.
3. Na parte inferior do *exportar dados* painel de propriedades do módulo, no **parâmetros de serviço Web** secção, clique em consulta de base de dados e alterá-lo a consulta.
4. Clique em **nome da tabela de dados** e renomeie- **tabela**.

Quando tiver terminado, sua experimentação deve ter um aspeto semelhante ao seguinte imagem:

![Aspeto final da experimentação.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora pode implementar a experimentação como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar um clássico ou o novo serviço web.

### <a name="deploy-a-classic-web-service"></a>Implementar um serviço Web clássico
Para implementar como um serviço Web clássico e criar uma aplicação para utilizadores o consomem:

1. Na parte inferior da tela de experimentação, clique em executar.
2. Quando a execução foi concluída, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]**.
3. No dashboard do serviço web, localize a chave de API. Copie e guarde-a para utilizar mais tarde.
4. No **ponto final predefinido** tabela, clique em de **de execução de lote** ligação para abrir a página de ajuda de API.
5. No Visual Studio, crie uma aplicação de consola c#: **novo** > **projeto** > **Visual c#** > **Windows Ambiente de trabalho clássico** > **(.NET Framework) de aplicação de consola**.
6. Na página de ajuda de API, localizar o **código de exemplo** secção na parte inferior da página.
7. Copie e cole o código de exemplo do c# no seu ficheiro Program.cs e remova todas as referências para o armazenamento de Blobs.
8. Atualize o valor da *apiKey* variável com a chave de API que guardou anteriormente.
9. Localize a declaração de pedido e atualize os valores de parâmetros de serviço Web que são transmitidos para o *importar dados* e *exportar dados* módulos. Neste caso, utilize a consulta original, mas definir um novo nome de tabela.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Execute a aplicação. 

A execução após a conclusão, é adicionada uma nova tabela na base de dados que contém os resultados de classificação.

### <a name="deploy-a-new-web-service"></a>Implementar um novo serviço Web

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implementar como um novo serviço Web e criar uma aplicação para utilizadores o consomem:

1. Na parte inferior da tela de experimentação, clique em **executar**.
2. Quando a execução foi concluída, clique em **implementar serviço Web** e selecione **implementar o serviço Web do [New]**.
3. Na página de experimentação implementar, introduza um nome para o seu serviço web e selecione um plano de preços, em seguida, clique em **implementar**.
4. No **início rápido** página, clique em **Consume**.
5. No **código de exemplo** secção, clique em **Batch**.
6. No Visual Studio, crie uma aplicação de consola c#: **novo** > **projeto** > **Visual c#** > **Windows Ambiente de trabalho clássico** > **(.NET Framework) de aplicação de consola**.
7. Copie e cole o código de exemplo do c# no seu ficheiro Program.cs.
8. Atualize o valor da *apiKey* variável com o **chave primária** localizado no **informações de consumo básico** secção.
9. Localize o *scoreRequest* declaração e atualize os valores de parâmetros de serviço Web que são transmitidos para o *importar dados* e *exportar dados* módulos. Neste caso, utilize a consulta original, mas definir um novo nome de tabela.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Execute a aplicação. 

