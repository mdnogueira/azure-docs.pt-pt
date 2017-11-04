---
title: "Aplicar a encriptação de disco no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * aplicar encriptação de disco *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar a encriptação de disco no Centro de segurança do Azure
Centro de segurança do Azure recomenda que aplique a encriptação de disco se tiver discos do Windows ou Linux VM que não são encriptados utilizando a Azure Disk Encryption. Encriptação de disco permite-lhe encriptar os discos do Windows e a VM do IaaS Linux.  A encriptação é recomendada para o SO e os volumes de dados na sua VM.

Encriptação de disco utiliza o padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funcionalidade do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux. Estas funcionalidades fornecem encriptação de dados e SO para ajudar a proteger e a salvaguardar os seus dados e a satisfazer os seus compromissos de segurança e conformidade organizacionais. Encriptação de disco está integrada [Cofre de chaves do Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, enquanto garantir que todos os dados nos discos VM são encriptados em pausa no seu [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Encriptação de disco do Azure é suportada nos seguintes sistemas operativos Windows server - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Encriptação de disco é suportada nos seguintes sistemas operativos Linux server - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **aplicar encriptação de disco**.
2. No **aplicar encriptação de disco** painel, pode ver uma lista de VMs para os quais se recomenda a encriptação de disco.
3. Siga as instruções para aplicar encriptação para estas VMs.

![][1]

Para encriptar as máquinas virtuais do Azure que tenham sido identificadas pelo centro de segurança como tendo necessidade de encriptação, recomendamos os seguintes passos:

* Instalar e configurar o Azure PowerShell. Isto permite-lhe executar os comandos do PowerShell necessários para configurar os pré-requisitos necessários para encriptar Virtual Machines do Azure.
* Obtenha e execute o script do PowerShell do Azure disco encriptação pré-requisitos do Azure.
* Encriptar as máquinas virtuais.

[Encriptar uma Máquina Virtual do Azure](security-center-disk-encryption.md) explica-lhe como seguir esses passos.  Este tópico parte do princípio de que está a utilizar o Windows 10 que o computador cliente a partir da qual pode configura a encriptação de disco.

Existem várias abordagens que podem ser utilizadas para máquinas virtuais do Azure. Se já estiver bem familiarizado com o Azure PowerShell ou o CLI do Azure, pode preferir utilizar abordagens alternativas. Para saber mais sobre estas outras abordagens, consulte [encriptação de disco do Azure](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Consultar também
Este documento mostrou como implementar a recomendação de centro de segurança "Aplicar encriptação de disco." Para saber mais sobre a encriptação de disco, consulte o seguinte:

* [Gestão de encriptação e a chave com o Cofre de chaves do Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 min 39 seg) – Saiba como utilizar a gestão de encriptação de disco para as VMs de IaaS e Cofre de chaves do Azure para ajudar a proteger e salvaguardar os seus dados.
* [Encriptar uma Máquina Virtual do Azure](security-center-disk-encryption.md) (documento) – Saiba como encriptar Virtual Machines do Azure.
* [Encriptação de disco do Azure](../security/azure-security-disk-encryption.md) (documento) – Saiba como ativar a encriptação de disco para o Windows e VMs com Linux.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e conformidade.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
