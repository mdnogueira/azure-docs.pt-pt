---
title: "Expandir a sua experiência com R | Microsoft Docs"
description: "Como expandir a funcionalidade do Azure Machine Learning Studio através do idioma de R ao utilizar o módulo de executar o Script do R."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: e9f11ec987a5dd71998f6b23399228554f1dcc11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-experiment-with-r"></a>Ampliar a sua experimentação com R
Pode expandir a funcionalidade do Azure Machine Learning Studio através do idioma de R ao utilizar o [executar Script do R] [ execute-r-script] módulo.

Este módulo aceita vários conjuntos de dados de entrada e gera um único conjunto de dados como saída. Pode escrever um script do R para a **R Script** parâmetro o [executar Script do R] [ execute-r-script] módulo.

Aceder a cada porta de entrada do módulo utilizando código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Listar todos os pacotes atualmente instalados
Pode alterar a lista de pacotes instalados. É possível encontrar uma lista de pacotes atualmente instalados no [R pacotes suportado pelo Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Também pode obter a lista completa, atual de pacotes instalados introduzindo o seguinte código para o [executar Script do R] [ execute-r-script] módulo:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Esta ação permite enviar a lista de pacotes para a porta de saída do [executar Script do R] [ execute-r-script] módulo.
Para ver a lista de pacotes, ligar um módulo de conversão como [converter para CSV] [ convert-to-csv] para a saída à esquerda do [executar Script do R] [ execute-r-script] módulo, Execute a experimentação, em seguida, clique o resultado do módulo conversão e selecione **transferir**. 

![Transferir o resultado do módulo de "Converter para CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importar pacotes
Pode importar pacotes que já não são instalados utilizando os seguintes comandos no [executar Script do R] [ execute-r-script] módulo:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

onde o `my_favorite_package.zip` ficheiro contém o pacote.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
