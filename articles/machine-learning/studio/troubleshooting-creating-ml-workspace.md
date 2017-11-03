---
title: "Resolução de problemas: Criar e ligar a uma área de trabalho do Machine Learning | Microsoft Docs"
description: "Soluções para problemas comuns na criação e ligação a uma área de trabalho do Azure Machine Learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: cdccd4ce7b87f47d21578076653bde901748188b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guia de resolução de problemas: criar e ligar a uma área de trabalho do Machine Learning
Este guia fornece soluções para algumas encontrou frequentemente desafios quando estiver a configurar áreas de trabalho do Azure Machine Learning.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietário da área de trabalho
Para abrir uma área de trabalho no Machine Learning Studio, têm de ser assinados para o Microsoft Account que utilizou para criar a área de trabalho ou terá de receber um convite do proprietário para associar área de trabalho. No portal do Azure pode gerir a área de trabalho, que inclui a capacidade de configurar o acesso.

Para obter mais informações sobre gerir uma área de trabalho, consulte [gerir uma área de trabalho do Azure Machine Learning].

[gerir uma área de trabalho do Azure Machine Learning]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
Aprendizagem está atualmente disponível num número limitado de regiões. Se a sua subscrição não incluir um estas regiões, poderá ver a mensagem de erro, "Tem não existem subscrições de regiões permitidas."

Para pedir que uma região adicionadas à sua subscrição, criar um novo pedido de suporte da Microsoft a partir do portal do Azure, escolha **faturação** como o tipo de problema e siga as instruções para submeter o pedido.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning tem uma conta do storage para armazenar dados. Pode utilizar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento ao criar a nova área de trabalho do Machine Learning (se tiver de quota para criar uma nova conta de armazenamento).

Depois de criada a nova área de trabalho do Machine Learning, pode iniciar sessão Machine Learning Studio, utilizando a conta Microsoft que utilizou para criar a área de trabalho. Se encontrar mensagem de erro, "Área de trabalho não encontrada" (semelhante à seguinte captura de ecrã), utilize os seguintes passos para eliminar os cookies do browser.

![Área de trabalho não foi encontrada][screen3]

**Para eliminar cookies do browser**

1. Se utilizar o Internet Explorer, clique em de **ferramentas** botão no canto superior direito e selecione **opções da Internet**.  

![Opções da Internet][screen4]

2. Sob o **geral** separador, clique em **eliminar...**

![Separador Geral][screen5]

3. No **eliminar histórico de navegação** diálogo caixa, certifique-se **Cookies e dados do site** está selecionada e clique em **eliminar**.

![Eliminar cookies][screen6]

Depois dos cookies são eliminados, reiniciar o browser e, em seguida, vá para o [Microsoft Azure Machine Learning](https://studio.azureml.net) página. Quando lhe for pedido um nome de utilizador e palavra-passe, introduza a mesma conta Microsoft que utilizou para criar a área de trabalho.

## <a name="comments"></a>Comentários

O nosso objetivo é para tornar a experiência de Machine Learning como totalmente integrada quanto possível. Publique quaisquer comentários e problemas no [fórum do Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para o ajudar-na servir a melhor.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
