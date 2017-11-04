---
title: Utilizar o Azure Machine Learning com o SQL Data Warehouse | Microsoft Docs
description: "Tutorial para utilizar o Azure Machine Learning com o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Utilização do Azure Machine Learning com o SQL Data Warehouse
O Azure Machine Learning é um serviço de Análise Preditiva completamente gerido que pode utilizar para criar modelos preditivos contra os dados no armazém de dados do SQL Server e, em seguida, publicar como serviços web prontos a consumir. Pode aprender as noções básicas de Análise Preditiva e machine learning ao ler [introdução ao Machine Learning no Azure][Introduction to Machine Learning on Azure].  Em seguida, pode saber como criar, dar formação, Pontuar e testar um machine learning utilizando o modelo de [criar experimentação tutorial][Create experiment tutorial].

Neste artigo, ficará a saber como fazer o seguinte utilizando a [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Ler dados a partir da base de dados para criar, dar formação e Pontuar um modelo preditivo
* Escrever dados na base de dados

## <a name="read-data-from-sql-data-warehouse"></a>Ler dados do SQL Data Warehouse
Iremos ler dados da tabela do produto na base de dados AdventureWorksDW.

### <a name="step-1"></a>Passo 1
Inicie uma nova experimentação, clicando em + nova na parte inferior da janela Machine Learning Studio, selecione experimentação e, em seguida, selecione a experimentar em branco. Selecione o nome predefinido da experimentação na parte superior da tela e mude o nome para algo significativo, por exemplo, predição do preço de bicicletas.

### <a name="step-2"></a>Passo 2
Procure o módulo de leitor na paleta de conjuntos de dados e módulos à esquerda da tela de experimentação. Arraste o módulo para a tela de experimentação.
![][drag_reader]

### <a name="step-3"></a>Passo 3
Selecione o módulo leitor e preencher o painel de propriedades.

1. Selecione a base de dados SQL do Azure como a origem de dados.
2. Nome do servidor de base de dados: escreva o nome do servidor. Pode utilizar o [portal do Azure] [ Azure portal] pode encontrá-lo.

![][server_name]

1. Nome da base de dados: escreva o nome de uma base de dados no servidor que acabou de especificar.
2. Nome de conta de utilizador do servidor: escreva o nome de utilizador de uma conta que tenha permissões de acesso para a base de dados.
3. Palavra-passe de conta de utilizador do: forneça a palavra-passe da conta de utilizador especificado.
4. Aceitar qualquer certificado de servidor: Utilize esta opção (menos segura), se pretender ignorar a rever o certificado de site antes de ler os dados.
5. Consulta de base de dados: introduza uma instrução de SQL que descreve os dados que pretende ler. Neste caso, iremos irá ler os dados da tabela de produto utilizando a seguinte consulta.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Passo 4
1. Execute a experimentação ao clicar em execução na tela de experimentação.
2. Quando a experimentação estiver concluída, o módulo leitor terá uma marca de verificação verde para indicar que foi concluída com êxito. Repare também terminado com o estado no canto superior direito.

![][run]

1. Para ver os dados importados, clique na porta de saída na parte inferior do conjunto de dados para automóveis e selecione o visualizar.

## <a name="create-train-and-score-a-model"></a>Criar, dar formação e um modelo de pontuação
Agora pode utilizar este conjunto de dados:

* Criar um modelo: processar os dados e definir funcionalidades
* Preparar o modelo: escolher e aplicar um algoritmo de aprendizagem
* Pontuar e testar o modelo: prever novos preços de bicicletas

![][model]

Para obter mais informações sobre como criar, dar formação, Pontuar e testar uma modelo de utilização de aprendizagem do [criar experimentação tutorial][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Escrever dados no Azure SQL Data Warehouse
Iremos escrever o resultado definido como ProductPriceForecast tabela na base de dados AdventureWorksDW.

### <a name="step-1"></a>Passo 1
Procure o módulo de escritor na paleta de conjuntos de dados e módulos à esquerda da tela de experimentação. Arraste o módulo para a tela de experimentação.

![][drag_writer]

### <a name="step-2"></a>Passo 2
Selecione o módulo de escritor e preencher o painel de propriedades.

1. Selecione a base de dados SQL do Azure como o destino de dados.
2. Nome do servidor de base de dados: escreva o nome do servidor. Pode utilizar o [portal do Azure] [ Azure portal] pode encontrá-lo.
3. Nome da base de dados: escreva o nome de uma base de dados no servidor que acabou de especificar.
4. Nome de conta de utilizador do servidor: escreva o nome de utilizador de uma conta que tenha permissões de escrita para a base de dados.
5. Palavra-passe de conta de utilizador do: forneça a palavra-passe da conta de utilizador especificado.
6. Aceitar qualquer certificado de servidor (inseguras): selecione esta opção se não pretender ver o certificado.
7. Lista separada por vírgulas de colunas a ser guardado: forneça uma lista de colunas de conjunto de dados ou o resultado que pretende de saída.
8. Nome da tabela de dados: Especifique o nome da tabela de dados.
9. Lista separada por vírgulas de colunas de datatable: especificar os nomes de coluna a utilizar na tabela de novo. Os nomes das colunas podem ser diferentes no conjunto de dados de origem, mas tem de listar o mesmo número de colunas aqui por si para a tabela de saída.
10. Número de linhas escrito por operação de SQL Azure: pode configurar o número de linhas que são escritos na base de dados SQL numa única operação.

![][writer_properties]

### <a name="step-3"></a>Passo 3
1. Execute a experimentação ao clicar em execução na tela de experimentação.
2. Quando a experimentação estiver concluída, todos os módulos terá uma marca de verificação verde para indicar que se foi concluída com êxito.

## <a name="next-steps"></a>Passos seguintes
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
