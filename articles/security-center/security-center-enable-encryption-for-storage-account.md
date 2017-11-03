---
title: "Ativar a encriptação da conta de armazenamento no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar as recomendações do Centro de segurança do Azure * * ativar a encriptação para o Azure armazenamento conta *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: b7b2e8a12cbab68da9c8fcc348e8e3c543607007
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Ativar a encriptação para a conta de armazenamento do Azure no Centro de segurança do Azure
Centro de segurança do Azure poderá recomendar que ativar a encriptação do serviço de armazenamento do Azure para dados inativos.

Encriptação de serviço de armazenamento (SSE) funciona por encriptar os dados ao que está escrito para o armazenamento do Azure e os dados antes de obtenção de desencriptação.  SSE está atualmente disponível apenas para o serviço Blob do Azure e pode ser utilizado para blobs de blocos, blobs de páginas e blobs de acréscimo.  Para obter mais informações, consulte [encriptação do serviço de armazenamento para dados Inativos](../storage/common/storage-service-encryption.md).


> [!Note]
> Depois de ativar a encriptação, apenas novos dados são encriptados. Os blobs existentes na sua conta de armazenamento de permanecer desencriptados. Para encriptar os blobs existentes, consulte o [FAQ de encriptação do serviço de armazenamento](../storage/common/storage-service-encryption.md#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).
>
>

Encriptação do serviço de armazenamento só é suportada em contas de armazenamento do Resource Manager. Contas de armazenamento clássicas não são atualmente suportadas. Para compreender clássica e modelos de implementação do Resource Manager, consulte [modelos de implementação do Azure](../azure-classic-rm.md).

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **ativar a encriptação da conta de armazenamento do Azure**.
   ![Ativar a encriptação da conta de armazenamento][1]
2. O **ativar a encriptação de armazenamento** abre o painel. Este painel lista as contas de armazenamento do Azure onde a encriptação de armazenamento está desativada. Neste exemplo, vamos selecione **storageacct1**.
   ![Ativar a encriptação de armazenamento][2]
3. O **encriptação** painel **storageacct1** abre. Selecione **ativada**.
   ![Painel de encriptação][3]
4. Selecione **Guardar**.

Agora que tiver ativado a encriptação de armazenamento para **storageacct1**.


## <a name="see-also"></a>Consultar também
Este documento mostrou como implementar a recomendação de centro de segurança "Ativar a encriptação para a conta de armazenamento do Azure." Para saber mais sobre a encriptação de serviço de armazenamento do Azure, consulte o seguinte:

* [Encriptação do serviço de armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) -Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) -Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) -aprender a gerir e responder a alertas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) -Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [FAQ do Centro de segurança do Azure](security-center-faq.md) -encontre as perguntas mais frequentes sobre como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) -encontre mensagens do Blogue acerca da segurança do Azure e conformidade.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
