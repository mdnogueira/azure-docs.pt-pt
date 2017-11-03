---
title: "Gestão de região na pilha do Azure | Microsoft Docs"
description: "Descrição geral da gestão de região na pilha do Azure."
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Gestão de região na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pilha do Azure tem o conceito de regiões, que são entidades lógicas compostas os recursos de hardware que compõem a infraestrutura de pilha do Azure. Dentro de gestão de região, pode encontrar todos os recursos que são necessários para funcionar com êxito o ciclo de vida de infraestrutura de pilha do Azure.

Uma implementação do sistema de integrada (referido como um *cloud de pilha do Azure*) constitui uma única região. Cada Kit de desenvolvimento de pilha do Azure tem uma região, com o nome **local**. Se implementar um sistema de pilha do Azure integrada de segundo ou configure outra instância do kit de desenvolvimento no hardware em separado, esta nuvem de pilha do Azure é uma região diferente.

## <a name="information-available-through-the-region-management-tile"></a>Informações disponíveis através do mosaico de gestão de região
Pilha do Azure tem um conjunto de capacidades de gestão de região disponíveis no **gestão região** mosaico. Este mosaico não está disponível para um operador de pilha do Azure no dashboard predefinido no portal do administrador. Através deste mosaico, pode monitorizar e atualizar a sua região de pilha do Azure e os respetivos componentes, o que são específicos da região.

 ![O mosaico de gestão de região](media/azure-stack-manage-region/image1.png)

 Se clicar numa região no mosaico de gestão de região, pode aceder as seguintes informações:

  ![Descrição de painéis no painel de gestão de região](media/azure-stack-manage-region/image2.png)

1. **O menu de recurso**. Aqui, pode aceder a áreas de gestão de infraestrutura específica e ver e gerir recursos de utilizador, tais como contas de armazenamento e redes virtuais.

2. **Alertas**. Este mosaico apresenta uma lista de alertas de todo sistema e fornece detalhes sobre cada um dos alertas.

3. **Atualizações**. Neste mosaico, pode ver a versão atual da sua infraestrutura de pilha do Azure.

4. **Fornecedores de recursos**. Fornecedores de recursos é o local para gerir a funcionalidade de inquilino oferecida pelos componentes necessários para executar a pilha do Azure. Cada fornecedor de recursos é fornecido com uma experiência administrativa. Esta experiência pode incluir alertas para o fornecedor específico, métricas e outras capacidades de gestão específicas para o fornecedor de recursos.
 
5. **Funções de infraestrutura**. As funções de infraestrutura são os componentes necessários para executar a pilha do Azure. São listadas apenas as funções de infraestrutura que dependem dos alertas. Ao clicar uma função, pode ver os alertas associados a função específica e as instâncias da função onde esta função está em execução. Embora a capacidade de iniciar, reiniciar, ou encerrar uma instância de função de infraestrutura, efetue **não** fazê-lo num ambiente de kit de desenvolvimento. Estas opções foram concebidas apenas para um ambiente com vários nós, onde existe mais do que uma instância de função por função de infraestrutura. Reinício de uma instância de função (especialmente AzS-Xrp01) no kit de desenvolvimento provoca instabilidade do sistema.

## <a name="next-steps"></a>Passos seguintes
[Monitorizar o estado de funcionamento e alertas na pilha do Azure](azure-stack-monitor-health.md)

[Gerir as atualizações na pilha do Azure](azure-stack-updates.md)






