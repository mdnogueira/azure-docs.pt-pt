---
title: "Criar um balanceador de carga interno – Portal do Azure | Microsoft Docs"
description: Saiba como criar um balanceador de carga interno no Resource Manager com o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3be595b03f667cf9700d2f17eb2080aa74f41dd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Criar um balanceador de carga interno no portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Resource Manager, o que é recomendado pela Microsoft para a maioria das novas implementações em vez do [modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Introdução à criação de um balanceador de carga interno com o portal do Azure

Utilize os passos seguintes para criar um balanceador de carga interno a partir do portal do Azure.

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. No canto superior esquerdo do ecrã, clique em **Novo** > **Rede** > **Balanceador de carga**.
3. No painel **Criar balanceador de carga**, introduza um **Nome** para o balanceador de carga.
4. Em **Esquema**, clique em **Interno**.
5. Clique em **Rede virtual**, e, em seguida, selecione a rede virtual onde pretende criar o balanceador de carga.

   > [!NOTE]
   > Se não vir a rede virtual que pretende utilizar, verifique a **localização** que está a utilizar para o balanceador de carga e altere-a em conformidade.

6. Clique em **Sub-rede** e, em seguida, selecione a sub-rede onde pretende criar o balanceador de carga.
7. Em **Atribuição de endereços IP**, clique em **Dinâmico** ou **Estático**, dependendo se pretende que o endereço IP do balanceador de carga seja fixo (estático) ou não.

   > [!NOTE]
   > Se optar por utilizar um endereço IP estático, tem de fornecer um endereço para o balanceador de carga.

8. Em **Grupo de recursos**, especifique o nome de um novo grupo de recursos para o balanceador de carga ou clique em **Selecionar existente** e selecione um grupo de recursos existente.
9. Clique em **Criar**.

## <a name="configure-load-balancing-rules"></a>Configurar regras de balanceamento de carga

Após a criação do balanceador de carga, navegue para o recurso do balanceador de carga para configurá-lo.
Configure primeiro um conjunto de endereços de back-end e uma sonda antes de configurar uma regra de balanceamento de carga.

### <a name="step-1-configure-a-backend-pool"></a>Passo 1: Configurar um conjunto de back-end

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e, em seguida, clique no balanceador de carga que criou anteriormente.
2. No painel **Definições**, clique em **Conjuntos de back-end**.
3. No painel **Conjuntos de endereços de back-end**, clique em **Adicionar**.
4. No painel **Adicionar conjunto de back-end**, introduza um **Nome** para o conjunto de back-end e, em seguida, clique em **OK**.

### <a name="step-2-configure-a-probe"></a>Passo 2: Configurar uma sonda

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e, em seguida, clique no balanceador de carga que criou anteriormente.
2. No painel **Definições**, clique em **Sondas**.
3. No painel **Sondas**, clique em **Adicionar**.
4. No painel **Adicionar sonda**, introduza um **Nome** para a sonda.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outras aplicações baseadas em TCP).
6. Em **Porta**, especifique a porta a utilizar quando aceder à sonda.
7. Em **Caminho** (apenas para sondas HTTP), especifique o caminho a utilizar como uma sonda.
8. Em **Intervalo**, especifique a frequência da sonda à aplicação.
9. Em **Limiar de mau estado de funcionamento**, especifique o número de tentativas que devem falhar antes de a máquina virtual de back-end ser marcada como em mau estado de funcionamento.
10. Clique em **OK** para criar a sonda.

### <a name="step-3-configure-load-balancing-rules"></a>Passo 3: Configurar regras de balanceamento de carga

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e, em seguida, clique no balanceador de carga que criou anteriormente.
2. No painel **Definições**, clique em **Regras de balanceamento de carga**.
3. No painel **Regras de balanceamento de carga**, clique em **Adicionar**.
4. No painel **Adicionar regra de balanceamento de carga**, introduza um **Nome** para a regra.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outras aplicações baseadas em TCP).
6. Em **Porta**, especifique os clientes que ligam à porta no balanceador de carga.
7. Em **Porta de back-end**, especifique a porta a ser utilizada no conjunto de back-end (normalmente, a porta do balanceador de carga e a porta de back-end são as mesmas).
8. Em **Conjunto de back-end**, selecione o conjunto de back-end que criou anteriormente.
9. Em **Persistência da sessão**, selecione como pretende que as sessões persistam.
10. Em **Tempo limite de inatividade (minutos)**, especifique o tempo limite de inatividade.
11. Em **IP flutuante (devolução direta do servidor)**, clique em **Desativado** ou **Ativado**.
12. Clique em **OK**.

## <a name="next-steps"></a>Passos seguintes

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

