---
title: "Certifique-se de uma ligação do VPN Gateway | Microsoft Docs"
description: "Este artigo mostra como verificar uma ligação do VPN Gateway de rede virtual."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="verify-a-vpn-gateway-connection"></a>Certifique-se de uma ligação de Gateway de VPN

Este artigo mostra como verificar uma ligação de gateway VPN para clássica e modelos de implementação do Resource Manager.

## <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Para verificar uma ligação de gateway VPN para o modelo de implementação do Resource Manager com o PowerShell, instale a versão mais recente do [cmdlets do Azure Resource Manager PowerShell](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>CLI do Azure

Para verificar uma ligação de gateway VPN para o modelo de implementação do Resource Manager utilizando a CLI do Azure, instale a versão mais recente do [comandos da CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 ou posterior).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portal do Azure (clássica)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (clássico)

Para verificar a ligação de gateway VPN para o modelo de implementação clássica com o PowerShell, instale as versões mais recentes dos cmdlets do PowerShell do Azure. Não se esqueça de transferir e instalar o [gestão de serviço](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0) módulo. Utilize o Add-AzureAccount para iniciar sessão no modelo de implementação clássica.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.