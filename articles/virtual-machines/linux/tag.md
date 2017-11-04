---
title: "Como tag de uma máquina virtual do Linux do Azure | Microsoft Docs"
description: "Saiba mais sobre a marcação de uma máquina virtual de Linux do Azure criada no Azure utilizando o modelo de implementação Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: da3ff0de2a5d6ac8994b7c16b758f976228a53b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como tag de uma máquina virtual Linux no Azure
Este artigo descreve as diferentes formas para marcar uma máquina virtual Linux no Azure através do modelo de implementação Resource Manager. As etiquetas são definidos pelo utilizador pares chave-valor que podem ser colocadas diretamente um recurso ou um grupo de recursos. Atualmente, o Azure suporta até 15 etiquetas por recursos e o grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção, as etiquetas são suportadas para os recursos criados através de apenas o modelo de implementação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com a CLI do Azure
Para começar, terá da versão mais recente [2.0 CLI do Azure (pré-visualização)](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Também pode efetuar estes passos com a [CLI 1.0 do Azure](tag-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pode ver todas as propriedades para uma determinada máquina Virtual, incluindo as etiquetas, utilizar este comando:

        az vm show --resource-group MyResourceGroup --name MyTestVM

Para adicionar uma nova etiqueta VM através da CLI do Azure, pode utilizar o `azure vm update` comando juntamente com o parâmetro de tag **– definir**:

        az vm update --resource-group MyResourceGroup --name MyTestVM –-set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2

Para remover as etiquetas, pode utilizar o **– remover** parâmetro o `azure vm update` comando.

        az vm update –-resource-group MyResourceGroup –-name MyTestVM --remove tags.myNewTagName1


Agora que tem Aplicamos etiquetas aos nossos recursos CLI do Azure e o Portal, vamos ver os detalhes de utilização para ver as etiquetas no portal de faturação.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a etiquetagem os recursos do Azure, consulte o artigo [descrição geral do Azure Resource Manager] [ Azure Resource Manager Overview] e [etiquetas a utilizar para organizar os recursos do Azure] [ Using Tags to organize your Azure Resources].
* Para ver como as etiquetas podem ajudá-lo a gerir a utilização de recursos do Azure, consulte [compreender a fatura do Azure] [ Understanding your Azure Bill] e [obter informações acerca do consumo de recursos do Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
