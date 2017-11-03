---
title: "Plataformas suportadas no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento fornece uma lista dos sistemas de operatings Windows e Linux suportados no Centro de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: terrylan
ms.openlocfilehash: c80e78b39b3fba4dcd9523e1e679822758822805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-platforms-in-azure-security-center"></a>Plataformas suportadas no Centro de segurança do Azure
Monitorização de estado de segurança e as recomendações são disponíveis para máquinas virtuais (VMs) criadas utilizando modelos de implementação do Resource Manager e clássico.

> [!NOTE]
> Saiba mais sobre o [clássica e modelos de implementação do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
>
>

## <a name="supported-platforms-for-windows-vms"></a>Plataformas suportadas para as VMs do Windows
Sistemas de operativos Windows suportados:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-vms"></a>Plataformas suportadas para VMs com Linux
Sistemas operativos Linux suportados:

* Versões do Ubuntu 12.04, 14.04, 16.04, 16.10
* Versões debian 7, 8
* Versões 6 do centOS. \*, 7.*
* Versões do Red Hat Enterprise Linux (RHEL) 6. \*, 7.*
* Versões 11 SP4 + do SUSE Linux Enterprise Server (SLES), 12.*
* Versões de Linux Oracle 6. \*, 7.*

> [!NOTE]
> Análise comportamental de máquina virtual ainda não está disponíveis para os sistemas operativos Linux.
>
>

## <a name="vms-and-cloud-services"></a>VMs e serviços em nuvem
VMs em execução num serviço em nuvem também são suportadas. Nuvem apenas dos serviços de funções web e de trabalho em execução na produção ranhuras são monitorizadas. Para saber mais sobre o serviço em nuvem, consulte o artigo [descrição geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Passos seguintes

- [Guia de operações e planeamento do Centro de segurança do Azure](security-center-planning-and-operations-guide.md) — Saiba como planear e compreender as considerações de conceção para adoção do Centro de segurança do Azure
- [Alertas de segurança por tipo no Centro de segurança do Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) - Saiba mais sobre a análise comportamental de máquina virtual e análise de memória de captura no Centro de segurança de falhas
- [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
- [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e conformidade
