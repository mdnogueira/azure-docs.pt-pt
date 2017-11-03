---
title: Criar o FQDN para uma VM com Linux no portal do Azure | Microsoft Docs
description: "Saiba como criar um nome de domínio completamente qualificado, ou FQDN, para um Gestor de recursos com base em máquina virtual no portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 49bfec791fcca3feabc4eb280cefd7faada0ea31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio completamente qualificado no portal do Azure para uma VM com Linux

Quando cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), é criado automaticamente um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente a VM. Embora o portal não cria um [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, pode adicionar uma assim que a VM ser criada. Este artigo demonstra os passos para criar um nome DNS ou um FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que já criou uma VM. Se necessário, pode [criar uma VM no portal do](quick-create-portal.md) ou [com a CLI do Azure](quick-create-cli.md). Assim que a VM está a funcionar, siga estes passos:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar remotamente para a VM com este nome DNS, tal como com `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Passos seguintes
Agora que a VM tem um nome IP e DNS público, pode implementar comuns das estruturas de aplicações ou serviços como o nginx, MongoDB, Docker, etc.

Também pode ler mais sobre [com o Resource Manager](../../azure-resource-manager/resource-group-overview.md) para sugestões sobre como criar as suas implementações do Azure.

