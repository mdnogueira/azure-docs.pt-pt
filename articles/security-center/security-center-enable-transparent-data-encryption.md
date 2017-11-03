---
title: "Ativar a encriptação transparente de dados no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * ativar transparente dados encriptação * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Ativar a encriptação transparente de dados no Centro de segurança do Azure
Centro de segurança do Azure recomendará ativar encriptação de dados transparente (TDE) nas bases de dados do SQL Server se o TDE não estiver ativado. TDE protege os dados e ajuda a cumprir os requisitos de conformidade ao encriptar a sua base de dados, cópias de segurança associadas e os ficheiros de registo de transações inativos, sem necessidade de alterar a aplicação. Para saber mais, consulte [encriptação transparente de dados com a SQL Database do Azure](https://msdn.microsoft.com/library/dn948096).

Esta recomendação aplica-se apenas; o serviço do SQL do Azure não inclui o SQL Server em execução em máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **ativar a encriptação transparente de dados**.
   ![Ativar a Encriptação de Dados Transparente][1]
2. Esta ação abre o **ativar a encriptação de dados transparente em bases de dados do SQL Server** painel. Selecione uma base de dados do SQL Server para ativar o TDE no.
   ![Selecione a base de dados SQL para ativar o TDE no][2]
3. No **encriptação transparente de dados** painel, selecione **ON** sob a encriptação de dados e selecione **guardar** no Friso superior do painel.
   ![Ativar o TDE][3]

   Depois do TDE está ativado na base de dados selecionada do SQL Server, o **estado de encriptação** será alterado para **encriptado**.    

   ![Estado de encriptação][4]

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar a recomendação de centro de segurança "Ativar a encriptação transparente de dados". Para saber mais sobre a SQL TDE, consulte o seguinte:

* [Encriptação transparente de dados com base de dados SQL do Azure](https://msdn.microsoft.com/library/dn948096)
* [Começar com encriptação de dados transparente (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
