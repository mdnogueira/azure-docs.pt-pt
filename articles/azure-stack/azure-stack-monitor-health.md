---
title: Monitorizar o estado de funcionamento e alertas na pilha do Azure | Microsoft Docs
description: Saiba como monitorizar o estado de funcionamento e alertas na pilha do Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: b2ba7ec922341464ea7160d08e475999c941c42a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorizar o estado de funcionamento e alertas na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pilha do Azure inclui capacidades que permitem-lhe ver o estado de funcionamento e alertas para uma região do Azure pilha de monitorização de infraestrutura. O **gestão região** mosaico afixado por predefinição no portal do administrador da subscrição do fornecedor predefinido, apresenta uma lista de todas as regiões implementadas da pilha do Azure. O mosaico mostra o número de alertas críticos e de aviso ativos para cada região e é o ponto de entrada para o estado de funcionamento e a funcionalidade alerta de pilha do Azure.

 ![O mosaico de gestão de região](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Compreender o estado de funcionamento na pilha do Azure

 Estado de funcionamento e alertas são geridos pelo fornecedor de recursos de estado de funcionamento. Componentes de infraestrutura de pilha do Azure de registar o fornecedor de estado de funcionamento de recursos durante a configuração e implementação de pilha do Azure. Este registo permite a visualização de estado de funcionamento e alertas para cada componente. Estado de funcionamento na pilha do Azure é um conceito simple. Se existirem alertas para uma instância de um componente registada, o estado de funcionamento desse componente reflete a gravidade do alerta ativa pior; aviso ou crítico.
 
 ## <a name="view-and-manage-component-health-state"></a>Ver e gerir o estado de funcionamento do componente
 
 Como um operador de pilha do Azure, pode ver o estado de funcionamento dos componentes no portal do administrador e através da REST API e PowerShell.
 
Para ver o estado de funcionamento no portal, clique em que pretende ver na região de **gestão região** mosaico. Pode ver o estado de funcionamento das funções de infraestrutura e dos fornecedores de recursos. Nesta versão, o fornecedor de recursos de computação não reportar o estado de funcionamento.

![Lista de funções de infraestrutura](media/azure-stack-monitor-health/image2.png)

Pode clicar num fornecedor de recursos ou função de infraestrutura para ver informações mais detalhadas.

> [!WARNING]
>Se clique uma função de infraestrutura e, em seguida, clique a instância de função, existem opções para iniciar, reiniciar ou encerrar. Utilize estas ações durante a aplicar atualizações para um sistema integrado. Além disso, execute **não** utilizar estas opções num ambiente Kit de desenvolvimento de pilha do Azure. Estas opções foram concebidas apenas para um ambiente integrado sistemas, onde existe mais do que uma instância de função por função de infraestrutura. Reinício de uma instância de função (especialmente AzS-Xrp01) no kit de desenvolvimento provoca instabilidade do sistema. Para obter assistência de resolução de problemas, após o seu problema para o [fórum de Azure pilha](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Ver alertas

A lista de alertas ativos para cada região de pilha do Azure está disponível diretamente a partir de **gestão região** painel. O primeiro mosaico na configuração predefinida é a **alertas** mosaico, que apresenta um resumo do críticos e alertas de aviso para a região. Pode afixar o mosaico alertas, como quaisquer outro mosaico neste painel, para o dashboard para acesso rápido.   

![Mosaico que mostra um aviso de alertas](media/azure-stack-monitor-health/image3.png)

Ao selecionar a parte superior do **alertas** mosaico, navegue para a lista de todos os alertas ativos para a região. Se selecionar um o **crítico** ou **aviso** item de linha no mosaico, navegue para uma lista filtrada de alertas (crítico ou aviso). 

![Alertas de aviso filtrado](media/azure-stack-monitor-health/image4.png)
  
O **alertas** painel suporta a capacidade de filtrar no Estado (ativos ou fechados) e a gravidade a (crítico ou aviso). A vista predefinida apresenta todos os alertas ativos. Todos os alertas fechados são removidos do sistema depois de sete dias.

![Painel de filtro para filtrar por críticos ou o estado de aviso](media/azure-stack-monitor-health/image5.png)

O **vista API** ação apresenta a API de REST que foi utilizado para gerar a vista de lista. Esta ação mostra uma forma rápida para se familiarizar com a sintaxe de REST API que pode utilizar a alertas de consulta. Pode utilizar esta API na automatização ou para a integração com o seu centro de dados existente, monitorização, relatórios e soluções de emissão de permissões. 

![A opção de API de vista que mostra a API REST](media/azure-stack-monitor-health/image6.png)

Pode clicar em alerta específico para ver os detalhes do alerta. Os detalhes do alerta apresentam todos os campos que estão associados com o alerta e permitem a navegação rápida para o componente afetado e a origem do alerta. Por exemplo, o seguinte alerta ocorre se uma das instâncias de função de infraestrutura fica offline ou não está acessível.  

![O painel de detalhes do alerta](media/azure-stack-monitor-health/image7.png)

Depois da instância de função de infraestrutura está novamente online, este alerta é fechado automaticamente. Alerta de muitas, mas nem todas as é fechada automaticamente quando o problema subjacente for resolvido. Recomendamos que selecione **fechar alerta** depois de efetuar os passos de remediação. Se o problema persistir, a pilha de Azure gera um novo alerta. Se a resolução do problema, o alerta permanece fechado e não requer nenhuma ação adicional.

## <a name="next-steps"></a>Passos seguintes

[Gerir as atualizações na pilha do Azure](azure-stack-updates.md)

[Gestão de região na pilha do Azure](azure-stack-region-management.md)
