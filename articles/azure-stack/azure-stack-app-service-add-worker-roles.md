---
title: "Aumentar horizontalmente as funções de trabalho nos serviços de aplicações - pilha do Azure | Microsoft Docs"
description: "Orientação detalhada para o dimensionamento dos serviços de aplicação de pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 30ab325488684a26a6ef442e7c8241526a66aa4c
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Serviço de aplicações na pilha do Azure: adicionar várias funções de infraestrutura ou de trabalho

Este documento fornece instruções sobre como dimensionar o serviço de aplicações nas funções de infraestrutura e de trabalho de pilha do Azure. Contém passos para criar funções de trabalho adicionais para suportar aplicações de qualquer dimensão.

> [!NOTE]
> Se o seu ambiente de pilha do Azure tem mais do que 96 GB de RAM tiver dificuldades adicionar capacidade adicional.

Serviço de aplicações na pilha do Azure, por predefinição, suporta escalões gratuito e partilhado de trabalho. Para adicionar outros escalões de trabalho, terá de adicionar mais funções de trabalho.

Se não tem a certeza de que foi implementado com a predefinição do serviço de aplicações numa instalação de pilha do Azure, pode rever informações adicionais a [do serviço de aplicações na descrição geral do Azure pilha](azure-stack-app-service-overview.md).

App Service do Azure na pilha do Azure implementa todas as funções utilizando conjuntos de dimensionamento de Máquina Virtual e como tal tira partido das capacidades de dimensionamento desta carga de trabalho. Por conseguinte, todas as dimensionamento das camadas de trabalho é feita através do administrador de serviço de aplicações.

Adicionar técnicos adicionais diretamente no Admin de fornecedor de recursos de serviço de aplicações

1. Inicie sessão no portal de administração do Azure pilha como o administrador de serviço.

2. Navegue até à **serviços aplicacionais**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)
  
3. Clique em **funções**. Aqui pode ver a repartição de todas as funções do serviço de aplicações implementadas.

4. Clique na linha do tipo pretende dimensionar e, em seguida, clique em **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)
  
5. Clique em **dimensionamento**, selecione o número de instâncias que pretende dimensionar e, em seguida, clique em **guardar**.
    
    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Serviço de aplicações na pilha do Azure irá agora adição de mais VMs, configurá-los, instalar o software necessário e marcá-los como preparada quando este processo esteja concluído. Este processo pode demorar cerca de 80 minutos.

7. Pode monitorizar o progresso da disponibilidade das novas funções ao visualizar os trabalhadores no **funções** painel.

Depois de estarem totalmente implementado e pronto, os trabalhadores ficam disponíveis para os utilizadores implementar a sua carga de trabalho no-los. O seguinte mostra um exemplo dos vários escalões de preços disponíveis por predefinição. Se não houver nenhuma trabalhadores disponíveis para uma camada de trabalho específica, a opção de escolher o escalão de preço correspondente não está disponível.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para ampliar a gestão, funções de Front-End ou publicador adicionar que tem ampliar o conjunto de dimensionamento de VM correspondente. Iremos adicionar a capacidade de aumentar horizontalmente estas funções através de gestão do serviço de aplicações numa versão futura.

Para ampliar a gestão, Front-End ou funções do publicador, siga os mesmos passos selecionar o tipo de função adequados. Os controladores não são implementados como conjuntos de dimensionamento e, por conseguinte, duas devem ser implementadas no momento de instalação para todas as implementações de produção.

### <a name="next-steps"></a>Passos seguintes

[Configurar origens de implementação](azure-stack-app-service-configure-deployment-sources.md)
