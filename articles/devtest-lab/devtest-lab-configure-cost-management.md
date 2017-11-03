---
title: "Ver a tendência do custo estimado do laboratório mensal no Azure DevTest Labs | Microsoft Docs"
description: "Saiba mais sobre o gráfico de tendências de custo estimado mensal do Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Ver a tendência do custo estimado do laboratório mensal no Azure DevTest Labs
A funcionalidade de gestão de custo de DevTest Labs ajuda-o a controlar o custo de laboratório. Este artigo ilustra como utilizar o **tendência do custo estimado mensalmente** gráfico para ver custos-para-data estimada o calendário do mês atual dos e o custo de fim do mês previsto para o mês de calendário atual. Neste artigo, irá aprender a ver o gráfico de tendência do custo estimado mensalmente no portal do Azure.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizar o gráfico de tendência do custo estimado mensalmente
Para ver o gráfico de tendência do custo estimado mensalmente, siga estes passos: 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.   
4. No painel do laboratório, selecione **custo definições**.
5. No laboratório de **custo definições** painel, selecione **tendência do custo de laboratório**.
6. A seguinte captura de ecrã mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O **estimado custo** valor é custos-para-data estimada o calendário do mês atual dos. O **projetado custo** é o custo estimado para o mês de calendário atual todo, calculado com o custo de laboratório de cinco dias anteriores.

As quantidades de custo são arredondadas para o número inteiro seguinte. Por exemplo: 

* 5.01 Arredonda por excesso até 6 
* 5.50 Arredonda por excesso até 6
* 5.99 Arredonda por excesso até 6

Como Estados acima do gráfico, são os custos que vê no gráfico *estimado* custos de utilização [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) oferecem taxas.
Além disso, seguem-se *não* incluídas no cálculo custo:

* As subscrições Dreamspark e CSP atualmente não são suportadas como Azure DevTest Labs utiliza o [APIs de faturação do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o custo, que não suporta CSP ou Dreamspark subscrições de laboratório.
* As taxas de oferta. Atualmente, mas não é possível utilizar as taxas de oferta (mostradas na sua subscrição) que pode ter negociados com Microsoft ou Microsoft parceiros. Estamos a utilizar as taxas de pay as you go.
* As taxas
* Os descontos
* Moeda de faturação. Atualmente, o custo de laboratório é apresentado apenas na moeda EUR.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados
* [Duas coisas mais para manter os seus custos rumo no DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Limiares de custo por que motivo?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passos seguintes
Eis algumas coisas a experimentar seguinte:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) -Saiba como definir várias políticas utilizadas para governar a forma como são utilizados o laboratório e respetivas VMs. 
* [Criar a imagem personalizada](devtest-lab-create-template.md) - quando criar uma VM, especifique uma base que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um ficheiro VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) - DevTest Labs suporta a criação de VMs baseadas nas imagens do Azure Marketplace. Este artigo ilustra como especificar que, se existirem, as imagens do Azure Marketplace podem ser utilizado ao criar as VMs num laboratório.
* [Criar uma VM num laboratório](devtest-lab-add-vm-with-artifacts.md) -ilustra como criar uma VM a partir de uma imagem de base (ou personalizado ou Marketplace) e como trabalhar com artefactos em VM.

