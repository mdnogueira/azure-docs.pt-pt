---
title: "Ferramentas de Comunidade - mover recursos clássicos do Azure Resource Manager | Microsoft Docs"
description: "Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para ajudar a migrar os recursos IaaS do clássico para o modelo de implementação Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: d3fc0246088eddeb345bea0ffbd2c5247b218006
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas da comunidade para migrar recursos de IaaS da implementação clássica para Azure Resource Manager
Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para ajudá-lo com a migração de recursos IaaS do clássico para o modelo de implementação Azure Resource Manager.

> [!NOTE]
> Estas ferramentas não são suportadas oficialmente por Support da Microsoft. Por conseguinte, estão abertos obtido no GitHub e estamos satisfeitos aceitar PRs para correções ou cenários adicionais. Para comunicar um problema, utilize a funcionalidade de problemas do GitHub.
> 
> Migrar com estas ferramentas irá causar período de indisponibilidade para a Máquina Virtual clássica. Se estiver à procura de migração de plataforma suportada, visite 
> 
>   * [Migração de plataforma suportada dos recursos de IaaS do clássico para a pilha do Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Técnica detalhada da plataforma suportada a migração do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar os recursos IaaS do clássico para o Azure Resource Manager com o Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Esta é uma coleção de ferramentas de programa auxiliar criada como parte da empresa as migrações do gestão de serviço do Azure para o Azure Resource Manager. Esta ferramenta permite-lhe replicar a infraestrutura para outra subscrição que pode ser utilizada para testar a migração e iron eventuais problemas antes de executar a migração na sua subscrição de produção.

[A documentação da ferramenta de ligação](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS clássicos para recursos IaaS do Azure Resource Manager. A migração pode ocorrer dentro da mesma subscrição ou entre subscrições diferentes e tipos de subscrição (ex: subscrições de CSP).

[A documentação da ferramenta de ligação](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar o PowerShell para migrar os recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar os recursos IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rever as perguntas mais frequentes sobre a migração de IaaS de recursos do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

