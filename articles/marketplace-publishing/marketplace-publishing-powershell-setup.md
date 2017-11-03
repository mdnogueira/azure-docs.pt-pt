---
title: Configurar o PowerShell para criar uma VM para o Marketplace | Microsoft Docs
description: "Instruções para configurar o Azure PowerShell e utilizá-lo como um processo opcional fluir para criar imagens VM para implementar e propor no Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar o Azure PowerShell para criar uma oferta para o Azure Marketplace
Para obter informações detalhadas sobre como configurar o PowerShell no Azure, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Uma abordagem simple consiste em utilizar o método de certificado, que transfere e importa um certificado necessário para autenticação. Para obter o certificado necessário, utilize o **Get-AzurePublishSettingsFile** cmdlet. Guarde o ficheiro quando lhe for pedida. Para importar o certificado para uma sessão do PowerShell, utilize o **importação AzurePublishSettingsFile** cmdlet.

Para configurar e armazenar as definições de subscrição do Microsoft Azure comuns para a sessão do PowerShell, utilize o **Set-AzureSubscription** e **Select-AzureSubscription** cmdlets:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta do storage predefinida da subscrição (necessária para algumas operações de aprovisionamento de VM).  O segundo faz com que a subscrição dos atual (reconhecido por outros cmdlets).

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)

