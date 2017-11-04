---
title: Criar o FQDN para uma VM do Windows no portal do Azure | Microsoft Docs
description: "Saiba como criar um nome de domínio completamente qualificado, ou FQDN, para um Gestor de recursos com base em máquina virtual no portal do Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d5a555cd873222efcdb29e8eb3aaf128a24414b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Windows

Quando cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), é criado automaticamente um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente a VM. Embora o portal não cria um [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, pode criar um assim que a VM ser criada. Este artigo demonstra os passos para criar um nome DNS ou um FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que já criou uma VM. Se necessário, pode [criar uma VM no portal do](quick-create-portal.md) ou [com o Azure PowerShell](quick-create-powershell.md). Assim que a VM está a funcionar, siga estes passos:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora pode ligar remotamente para a VM com este nome DNS, tal como para o protocolo de ambiente de trabalho remoto (RDP).

## <a name="next-steps"></a>Passos seguintes
Agora que a VM tem um nome IP e DNS público, pode implementar comuns das estruturas de aplicações ou serviços como o IIS, SQL Server ou SharePoint.

Também pode ler mais sobre [com o Resource Manager](../../azure-resource-manager/resource-group-overview.md) para sugestões sobre como criar as suas implementações do Azure.

