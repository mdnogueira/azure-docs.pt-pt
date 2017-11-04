---
title: "Proteger as máquinas virtuais no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento trata as recomendações no Centro de segurança do Azure que o ajudam a proteger as máquinas virtuais e ficam em conformidade com as políticas de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: terrylan
ms.openlocfilehash: 6121717a8ce5661c70947e32dc877c6a898a9777
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Proteger as máquinas virtuais no Centro de segurança do Azure
Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identificar potenciais vulnerabilidades de segurança, cria recomendações ajudá-lo durante o processo de configuração de controlos necessários.  Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs), redes, SQL e aplicações.

Este artigo aborda as recomendações que se aplicam a VMs.  Centro de recomendações da VM em torno de recolha de dados, aplicar atualizações do sistema, aprovisionamento de antimalware, encriptar os discos da VM e muito mais.  Utilize a tabela abaixo como referência para ajudar a compreender as recomendações da VM disponíveis e que cada um irá fazer se aplicá-lo.

## <a name="available-vm-recommendations"></a>Recomendações da VM disponíveis
| Recomendação | Descrição |
| --- | --- |
| [Ativar a recolha de dados para subscrições](security-center-enable-data-collection.md) |Recomenda-se que ative a recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições. |
| [Ativar a encriptação da conta de armazenamento do Azure](security-center-enable-encryption-for-storage-account.md) | Recomenda-se de ativar a encriptação do serviço de armazenamento do Azure para dados inativos. Encriptação de serviço de armazenamento (SSE) funciona ao encriptar os dados quando são escritos no armazenamento do Azure e desencripta antes de obtenção. SSE está atualmente disponível apenas para o serviço Blob do Azure e pode ser utilizado para blobs de blocos, blobs de páginas e blobs de acréscimo. Para obter mais informações, consulte [encriptação do serviço de armazenamento para dados Inativos](../storage/common/storage-service-encryption.md).</br>SSE só é suportado em contas de armazenamento do Resource Manager. Contas de armazenamento clássicas não são atualmente suportadas. Para compreender clássica e modelos de implementação do Resource Manager, consulte [modelos de implementação do Azure](../azure-classic-rm.md). |
| [Remediar vulnerabilidades do SO](security-center-remediate-os-vulnerabilities.md) |Recomenda-se que alinhe as configurações do SO com as regras de configuração recomendadas, por exemplo, não permitir que as palavras-passe sejam guardadas. |
| [Aplicar atualizações do sistema](security-center-apply-system-updates.md) |Recomenda-se que implemente o sistema de segurança em falta e atualizações críticas nas VMs. |
| [Aplicar um Just-In-Time controlo de acesso de rede](security-center-just-in-time.md) | Recomenda-se de que se aplicam apenas no acesso VM de tempo. O apenas na hora funcionalidade em pré-visualização e está disponível na camada padrão do Centro de segurança. Consulte [preços](security-center-pricing.md) para saber mais acerca do Centro de segurança do escalões de preço. |
| [Reiniciar após atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Recomenda-se que reinicie uma VM para concluir o processo de aplicar atualizações do sistema. |
| [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md) |Recomenda-se que aprovisione programas de antimalware em VMs (apenas VMs do Windows). |
| [Ativar o Agente de VM](security-center-enable-vm-agent.md) |Permite-lhe ver as VMs que necessitam do Agente de VM. O Agente de VM tem de estar instalado em VMs para aprovisionar a análise de patch, a análise de linha de base e os programas de antimalware. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| [Aplicar encriptação de discos](security-center-apply-disk-encryption.md) |Recomenda-se que encripte os discos da VM com o Azure Disk Encryption (VMs Windows e Linux). A encriptação é recomendada para o SO e os volumes de dados na sua VM. |
| [Atualizar a versão do SO](security-center-update-os-version.md) |Recomenda-se de que atualiza a versão do sistema operativo (SO) do serviço em nuvem para a versão mais recente disponível para a sua família de SO.  Para saber mais sobre os serviços de nuvem, consulte o [descrição geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidades não instalada](security-center-vulnerability-assessment-recommendations.md) |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Permite-lhe ver as vulnerabilidades de sistema e de aplicações detetadas pela solução de avaliação de vulnerabilidade instalada na sua VM. |

## <a name="see-also"></a>Consultar também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as suas aplicações no Centro de segurança do Azure](security-center-application-recommendations.md)
* [Proteger a sua rede no Centro de segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
