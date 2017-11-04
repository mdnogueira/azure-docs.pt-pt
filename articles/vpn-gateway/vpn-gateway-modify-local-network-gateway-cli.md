---
title: "Modificar os prefixos de endereço IP de gateway de rede local e o endereço IP do VPN Gateway | Azure | CLI | Microsoft Docs"
description: "Este artigo explica como alterar os prefixos de endereço IP para o gateway de rede local utilizando a CLI do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 7db1ad970ebb93d46d5a861f9a9b27bf121531a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as definições do gateway de rede local, utilizando a CLI do Azure

Por vezes, altere as definições para o seu prefixo de endereço de gateway de rede local ou o endereço IP do Gateway. Este artigo mostra como modificar as definições do gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posteriores). Para obter informações sobre como instalar os comandos da CLI, veja [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalar a CLI 2.0 do Azure).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modificar os prefixos de endereço IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Passos seguintes

Pode verificar a ligação de gateway. Consulte [verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).

