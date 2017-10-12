---
title: "Criar um balanceador de carga com acesso à Internet – Portal clássico do Azure | Microsoft Docs"
description: "Saiba como criar um balanceador de carga com acesso à Internet num modelo de implementação clássica com o portal clássico do Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: e07b6808f2401ac7b2b21e5f8816bac5a15b50b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Introdução à criação de um balanceador de carga com acesso à Internet (modo clássico) no portal clássico do Azure

> [!div class="op_single_selector"]
> * [Portal Clássico do Azure](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços em Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Para trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: o Azure Resource Manager e a implementação clássica. Confirme que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica. Também pode [saber como criar um balanceador de carga com acesso à Internet com o Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurar um balanceador de carga com acesso à Internet para máquinas virtuais

Para efetuar o balanceamento de carga do tráfego de rede a partir da Internet nas máquinas virtuais de um serviço em nuvem, tem de criar um conjunto com balanceamento de carga. Este procedimento assume que já criou as máquinas virtuais e que estão todas no mesmo serviço em nuvem.

**Para configurar um conjunto com balanceamento de carga para máquinas virtuais**

1. No portal clássico do Azure, clique em **Máquinas Virtuais** e, em seguida, clique no nome de uma máquina virtual no conjunto com balanceamento de carga.
2. Clique em **Pontos finais** e, em seguida, clique em **Adicionar**.
3. Na página **Adicionar um ponto final a uma máquina virtual**, clique na seta para a direita.
4. Na página **Especificar os detalhes do ponto final**:

   * Em **Nome**, escreva um nome para o ponto final ou selecione o nome na lista de pontos finais predefinidos para protocolos comuns.
   * Em **Protocolo**, selecione o protocolo necessário para o tipo de ponto final, TCP ou UDP, conforme necessário.
   * Em **Porta Pública e Porta Privada**, escreva os números de porta que pretende que a máquina virtual utilize, conforme necessário. Pode utilizar a porta privada e as regras de firewall na máquina virtual para redirecionar o tráfego de uma forma que seja adequada para a sua aplicação. A porta privada pode ser a mesma que a porta pública. Por exemplo, para um ponto final para tráfego Web (HTTP), pode atribuir a porta 80 à porta pública e privada.

5. Selecione **Criar um conjunto com balanceamento de carga** e, em seguida, clique na seta para a direita.
6. Na página **Configurar o conjunto com balanceamento de carga**, escreva um nome para o conjunto com balanceamento de carga e, em seguida, atribua os valores para o comportamento de sonda do Balanceador de Carga do Azure. O Balanceador de Carga utiliza sondas para determinar se as máquinas virtuais no conjunto com balanceamento de carga estão disponíveis para receber o tráfego de entrada.
7. Clique na marca de verificação para criar o ponto final com balanceamento de carga. Verá **Sim** na coluna **Nome do conjunto com balanceamento de carga** da página **Pontos finais** da máquina virtual.
8. No portal, clique em **Máquinas Virtuais**, clique no nome de uma máquina virtual adicional no conjunto com balanceamento de carga, clique em **Pontos finais** e, em seguida, clique em **Adicionar**.
9. Na página **Adicionar um ponto final a uma máquina virtual**, clique em **Adicionar ponto final a um conjunto com balanceamento de carga existente**, selecione o nome do conjunto com balanceamento de carga e, em seguida, clique na seta para a direita.
10. Na página **Especificar os detalhes do ponto final**, escreva um nome para o ponto final e, em seguida, clique na marca de verificação.

Para as máquinas virtuais adicionais no conjunto com balanceamento de carga, repita os passos 8 a 10.

## <a name="next-steps"></a>Passos seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
