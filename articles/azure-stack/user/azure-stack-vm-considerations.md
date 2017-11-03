---
title: "Diferenças e as considerações para máquinas virtuais na pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre as diferenças e as considerações ao trabalhar com máquinas virtuais na pilha do Azure."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: sngun
ms.openlocfilehash: 7d841dba798c2b706c26dcf51361ce0447710b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Considerações para máquinas virtuais na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Máquinas virtuais são uma a pedido, dimensionáveis recursos informáticos disponibilizadas pela pilha de Azure. Quando utilizar máquinas virtuais, tem de compreender que existem diferenças entre as funcionalidades que estão disponíveis no Azure e pilha do Azure. Este artigo fornece uma descrição geral das considerações de exclusivas para máquinas virtuais e as respetivas funcionalidades na pilha do Azure. Para saber mais sobre das principais diferenças entre a pilha do Azure e do Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-virtual-machine-differences"></a>Cheat folha: diferenças de Máquina Virtual

| Funcionalidade | Azure (global) | Azure Stack |
| --- | --- | --- |
| Imagens da máquina virtual | O Azure Marketplace contém imagens que pode utilizar para criar uma máquina virtual. Consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) página para ver a lista de imagens que estão disponíveis no Azure Marketplace. | Por predefinição, não sabemos de quaisquer imagens disponíveis no mercado pilha do Azure. O administrador da nuvem do Azure pilha deve publicar ou transferir imagens no Marketplace de pilha do Azure antes dos utilizadores podem utilizá-los. |
| Tamanhos de máquinas virtuais | Azure suporta uma grande variedade de tamanhos de máquinas virtuais. Para saber mais sobre as opções e tamanhos disponíveis, consulte o [tamanhos de máquinas virtuais do Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos de máquinas virtuais do Linux](../../virtual-machines/linux/sizes.md) tópicos. | Pilha do Azure suporta um subconjunto de tamanhos de máquinas virtuais que estão disponíveis no Azure. Para ver a lista de tamanhos suportados, consulte o [tamanhos de máquina virtual](#virtual-machine-sizes) secção deste artigo. |
| Quotas de máquina virtual | [Limites de quota](../../azure-subscription-service-limits.md#service-specific-limits) estão definidas pela Microsoft | O administrador da nuvem do Azure pilha tem de atribuir quotas para máquinas virtuais oferecem aos respetivos utilizadores. |
| Extensões de máquina virtual |Azure suporta uma grande variedade de extensões de máquina virtual. Para saber mais sobre as extensões disponíveis, consulte o [extensões de máquina virtual e funcionalidades](../../virtual-machines/windows/extensions-features.md) tópico.| Pilha do Azure suporta um subconjunto de extensões que estão disponíveis no Azure e cada da extensão têm versões específicas. O administrador da nuvem do Azure pilha pode escolher quais as extensões para ficarem disponíveis para os seus utilizadores. Para ver a lista de extensões suportadas, consulte o [extensões de máquina virtual](#virtual-machine-extensions) secção deste artigo. |
| Rede de máquinas virtuais | Endereços IP públicos atribuídos à máquina virtual de inquilino estejam acessíveis através da Internet.<br><br><br>Máquinas virtuais do Azure com um nome DNS fixo | Endereços IP públicos atribuídos a uma máquina virtual inquilina estão acessíveis apenas o ambiente de Kit de desenvolvimento de pilha do Azure. Um utilizador tem de ter acesso para o Kit de desenvolvimento de pilha do Azure através de [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para ligar a uma máquina virtual que é criada na pilha do Azure.<br><br>Máquinas virtuais criadas dentro de uma instância específica de pilha do Azure tem um nome DNS com base no valor que é configurado pelo administrador da nuvem. |
| Armazenamento de máquina virtual | Suporta [discos geridos pelo.](../../virtual-machines/windows/managed-disks-overview.md) | Discos geridos não são suportados ainda na pilha do Azure. |
| Versões da API | O Azure tem sempre as versões de API mais recentes para todas as funcionalidades de máquina virtual. | Pilha do Azure suporta serviços específicos do Azure e versões de API específicas para estes serviços. Para ver a lista de versões de API suportadas, consulte o [versões de API](#api-versions) secção deste artigo. |
|Conjuntos de disponibilidade de máquina virtual|Vários domínios de falhas (2 ou 3 por região)<br>Vários domínios de atualização<br>Gerido suporte de disco|Domínio de falhas único<br>Domínio de atualização única<br>Sem suporte de disco gerido|
|Conjuntos de dimensionamento de máquinas virtuais|Escala automática suportada|Não suportado dimensionamento automático.<br>Adicione mais instâncias a uma escala definida utilizando o portal, modelos do Resource Manager ou PowerShell.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais 

O Kit de desenvolvimento de pilha do Azure suporta os tamanhos seguintes: 

| Tipo | Tamanho | Intervalo de tamanhos suportados |
| --- | --- | --- |
|Fins gerais |Básico A|A0 A4|
|Fins gerais |Um padrão|A0 A7|
|Fins gerais |D padrão|D1 D4|
|Fins gerais |Dv2 padrão|D1v2 D5v2|
|Com otimização de memória|Série D|D11 D14|
|Com otimização de memória |Série Dv2|D11v2 D14v2|

Tamanhos de máquina virtual e as quantidades de recursos associados são consistentes entre pilha do Azure e o Azure. Por exemplo, isto inclui a quantidade de memória, número de núcleos de e/tamanho do número de discos de dados que podem ser criadas. No entanto, o desempenho do mesmo tamanho VM na pilha do Azure depende as características subjacentes de um determinado ambiente de pilha do Azure.

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual 

 O Kit de desenvolvimento de pilha do Azure suporta as seguintes versões de extensão de máquina virtual:

![Extensões de VM](media/azure-stack-vm-considerations/vm-extensions.png)

Utilize o seguinte script do PowerShell para obter a lista de extensões de máquina virtual que estão disponíveis no seu ambiente de pilha do Azure:

```powershell 
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize 
```

## <a name="api-versions"></a>Versões da API 

Funcionalidades de máquina virtual no Kit de desenvolvimento de pilha do Azure suportam as seguintes versões de API:

![Tipos de recurso VM](media/azure-stack-vm-considerations/vm-resoource-types.png)

Pode utilizar o seguinte script do PowerShell para obter as versões de API para as funcionalidades de máquina virtual que estão disponíveis no seu ambiente de pilha do Azure:

```powershell 
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
A lista de tipos de recurso suportados e versões de API pode variar devido às se o operador da nuvem atualiza o seu ambiente de pilha do Azure para uma versão mais recente.

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual do Windows com o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)
