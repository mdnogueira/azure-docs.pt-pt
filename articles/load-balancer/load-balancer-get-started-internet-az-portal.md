---
title: "Criar um padrão de Balanceador de público de carga com redundância de zona IP público endereço front-end através do portal do Azure | Microsoft Docs"
description: "Saiba como criar um padrão de Balanceador de público de carga com redundância de zona IP público endereço front-end com o portal do Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Criar um padrão de Balanceador de público de carga com redundância de zona IP público endereço front-end através do portal do Azure

Os passos neste artigo através da criação de um público [padrão de Balanceador de carga](https://aka.ms/azureloadbalancerstandard) com um front-end com redundância de zona utilizando um endereço IP público padrão.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registar-se na pré-visualização padrão zonas de disponibilidade, o padrão de Balanceador de carga e o IP público
 
>[!NOTE]
[SKU Standard do Balanceador de carga](https://aka.ms/azureloadbalancerstandard) está atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade poderá não ter o mesmo nível de disponibilidade e fiabilidade oferecido na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Utilizar geralmente disponível [SKU básico de Balanceador de carga](load-balancer-overview.md) para os serviços de produção. 

> [!NOTE]
> Zonas de disponibilidade estão em pré-visualização e estão prontas para o seu desenvolvimento e teste cenários. O suporte está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de selecionar uma zona ou a opção com redundância de zona para o endereço IP público de front-end de Balanceador de carga, primeiro tem de concluir os passos em [registar-se para a pré-visualização de zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Criar um balanceador de carga redundante de zona

1. Num browser, navegue para o Portal do Azure: [http://portal.azure.com](http://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. No lado superior esquerdo do ecrã, selecione **novo** > **redes** > **Balanceador de carga.**
3. No * * criar o Balanceador de carga, em **nome** tipo **myPublicLB**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **Standard (pré-visualização)**.
6. Clique em **endereço IP público**, clique em **criar nova**, no **criar um endereço IP público** página, sob o nome, tipo **myPublicIPStandard**, e para **zona de disponibilidade (pré-visualização)**, selecione **zona redundante**.
7. Em **localização**, selecione **US2 Leste**e, em seguida, clique em **OK**. Em seguida, o balanceador de carga inicia a implementação e demora alguns minutos a concluir a implementação com êxito.

    ![criar o padrão de Balanceador de carga com redundância de zona com o portal do Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar um IP público numa zona de disponibilidade](../virtual-network/create-public-ip-availability-zone-portal.md)



