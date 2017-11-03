---
title: "Gerir iterações de experimentação no Machine Learning Studio | Microsoft Docs"
description: "Como gerir iterações de experimentação no Azure Machine Learning Studio"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 6a53530f-20d5-40ae-9b49-7b499ccb44b7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: 062620f2174ecc93c1deb816069e32152dbef636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Gerir iterações da experimentação no Azure Machine Learning Studio
Desenvolver um modelo de Análise Preditiva é um processo iterativo - à medida que modifica as várias funções e os parâmetros da sua experimentação, os seus resultados convergem até achar que tem um modelo preparado e eficaz. Chave para este processo está a controlar vários iterações da sua experimentação parâmetros e configurações.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Pode rever execuções anteriores das suas experimentações em qualquer altura para desafio, revê e, em última análise confirmar ou refinar pressupostos anteriores. Quando executa uma experimentação, o Machine Learning Studio mantém um histórico de execução, incluindo o conjunto de dados, módulo e ligações de porta e os parâmetros. Este histórico também captura resultados, informações de tempo de execução, tais como iniciar e parar vezes, as mensagens do registo e estado de execução. Pode ser novamente em qualquer um destes é executado em qualquer altura para rever o chronology da sua experimentação e resultados intermédios. Mesmo pode utilizar uma execução anterior da sua experimentação para iniciar numa fase de consulta e de deteção nova no seu caminho para a criação simples, complexas ou soluções de modelação de ensemble mesmo.

> [!NOTE]
> Quando visualiza uma execução anterior de uma experimentação, essa versão a experimentação está bloqueada e não pode ser editada. No entanto, pode, guarde uma cópia do mesmo ao clicar em **guardar como** e fornecer um novo nome para a cópia. O Machine Learning Studio abre-se a cópia novo, o que pode, em seguida, editar e executar. Esta cópia da sua experimentação está disponível no **EXPERIMENTAÇÕES** lista juntamente com todos os seus outras experimentações.
> 
> 

## <a name="viewing-the-prior-run"></a>Ver a execução anterior
Quando tiver de abrir uma experimentação que executou, pelo menos, uma vez, pode ver a execução anterior a experimentação ao clicar **executar o anterior** no painel Propriedades.

Por exemplo, suponha que criar uma experimentação e executam versões do mesmo às 11:23, 11:42 e 11:55. Se abrir a última execução da experimentação (11:55) e clique em **executar o anterior**, a versão que executou às 11:42 é aberta.

## <a name="viewing-the-run-history"></a>Visualizar o histórico de execução
Pode ver todas as execuções anteriores de uma experimentação, clicando em **ver histórico de execuções** numa experiência aberta.

Por exemplo, suponha que cria uma experimentação com o [regressão Linear] [ linear-regression] módulo e pretendido para observar o efeito de alterar o valor de **ritmo de aprendizagem** no seu resultados de experimentação. Executar a experimentação várias vezes com valores diferentes para este parâmetro, da seguinte forma:

| Valor de velocidade de aprendizagem | Hora de início de execução |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0.4 |9/11/2014 4:28:36 pm |
| 0.5 |9/11/2014 4:33:31 pm |

Se clicar em **ver histórico de EXECUÇÕES**, é apresentada uma lista de todas as execuções destas:

![Histórico de execução de exemplo][runhistory]

Clique em qualquer uma destas executa para ver um instantâneo da experimentação o momento em que foi executada. A configuração, os valores de parâmetros, comentários e os resultados são todos preservados para lhe dar um registo de concluir essa execução da sua experimentação.

> [!TIP]
> Documentar as iterações de experimentação, pode modificar o título de cada vez executa, pode atualizar o **resumo** da experimentação nas propriedades de painel e, pode adicionar ou atualizar comentários sobre o módulos individuais para registar o alterações. Os comentários título, o resumo e módulo são guardados com cada execução da experimentação.
> 
> 

A lista de experimentações no **EXPERIMENTAÇÕES** separador no Machine Learning Studio apresenta sempre a versão mais recente de uma experimentação. Se abrir uma execução anterior a experimentação (utilizando **executar o anterior** ou **ver histórico de EXECUÇÕES**), poderá regressar à versão de rascunho clicando **ver histórico de EXECUÇÕES** e selecionando a iteração que tenha um **estado** de **Editable**.

## <a name="iterating-on-a-previous-run"></a>Iterating numa execução anterior
Ao clicar em **executar o anterior** ou **ver histórico de EXECUÇÕES** e abrir uma execução anterior, pode ver uma experimentação concluída no modo só de leitura.

Se pretender começar uma iteração da sua experimentação começando com a forma como configurou para uma execução anterior, pode fazer isto, ao abrir a executar e clicar em **guardar como**. Esta ação cria uma nova experimentação, com um título de novo, vazio histórico, de execução e execute a todos os componentes e os valores de parâmetros de anterior. Esta nova experiência está listada no **EXPERIMENTAÇÕES** separador home page do Machine Learning Studio e pode modificar e executá-la, iniciar uma nova histórico de execução para esta iteração da sua experimentação. 

Por exemplo, suponha que tem a experimentação mostrado na secção anterior do histórico de execução. Pretendido para observar o que acontece quando definir o **ritmo de aprendizagem** parâmetro 0.4 e tente valores diferentes para o **diversas epochs formação** parâmetro.

1. Clique em **ver histórico de EXECUÇÕES** e abra a iteração da experimentação que tenha sido executada em 4:28:36 pm (na qual pode define o valor do parâmetro como 0.4).
2. Clique em **guardar como**.
3. Introduza um título nova e clique em de **OK** marca de verificação. É criada uma nova cópia a experimentação.
4. Modificar o **diversas epochs formação** parâmetro.
5. Clique em **executar**.

Pode agora continuar modificar e executar esta versão da sua experimentação, criar um novo histórico de execução para registar o seu trabalho.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
