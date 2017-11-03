---
title: "Integração com o Centro de segurança do registo do Azure | Microsoft Docs"
description: "Saber como obter a trabalhar com a integração do registo de alertas do Centro de segurança do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Como obter os alertas do Centro de segurança na integração de registos do Azure

Este artigo fornece os passos necessários para ativar o serviço de integração de registo do Azure solicitar informações de segurança alerta geradas pelo centro de segurança do Azure. Tem concluiu com êxito os passos a [começar](security-azure-log-integration-get-started.md) artigo antes de efetuar os passos neste artigo.

## <a name="detailed-steps"></a>Passos detalhados

Os passos seguintes irão criar o necessário Principal de serviço de diretório do Active Directory do Azure e atribua o princípio do serviço de permissões de leitura para a subscrição:
1. Abra a linha de comandos e navegue para **c:\Program Files\Microsoft Azure registo integração**
2. Execute o comando``azlog createazureid``

    Este comando pede-lhe o início de sessão do Azure. O comando, em seguida, cria um [Principal de serviço de diretório do Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) no Azure AD inquilinos que alojam as subscrições do Azure na qual o utilizador com sessão iniciada é um administrador, Coadministrador ou um proprietário. O comando irá falhar se o utilizador com sessão iniciada é apenas um utilizador convidado no inquilino do Azure AD. Autenticação do Azure é efetuada através do Azure Active Directory (AD). Criar um principal de serviço para a integração de Azlog cria a identidade do Azure AD que terá acesso de leitura de subscrições do Azure.

3. Em seguida, executar um comando que atribui acesso de leitor de subscrição para o principal de serviço que acabou de criar. Se não especificar um ID de subscrição, em seguida, o comando tentará atribuir a função de leitor de principal de serviço para todas as subscrições para a qual tem qualquer acesso. </br></br>
``azlog authorize <SubscriptionID>`` </br> Por exemplo </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Poderá ver avisos se executar o comando de autorizar imediatamente após o ```createazureid``` comando. Não há alguma latência entre quando é criada a conta do Azure AD e quando a conta está disponível para utilização. Se Aguarde cerca de 60 segundos após a execução de ```createazureid``` comando a executar o comando de autorizar, em seguida, a não deve ser apresentado estes avisos.

4. Verifique as seguintes pastas para confirmar que os ficheiros de JSON do registo de auditoria existem:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Verifique as seguintes pastas para confirmar que os alertas do Centro de segurança existem nos mesmos:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Caso se depare com problemas durante a instalação e configuração, abra uma [pedido de suporte](/azure-supportability/how-to-create-azure-support-request.md), selecione **integração de registo** como o serviço para o qual está a pedir suporte.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a integração de registo do Azure, consulte os seguintes documentos:

* [Integração de registo do Microsoft Azure para os registos do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de transferências para obter detalhes, requisitos de sistema e instalar as instruções na integração de registos do Azure.
* [Introdução à integração de registos do Azure](security-azure-log-integration-overview.md) – este documento apresenta-lhe integração de registos do Azure, as suas capacidades principais e como funciona.
* [Passos de configuração do parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – este blogue mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Registos do Azure integração perguntas mais frequentes (FAQ) do sobre](security-azure-log-integration-faq.md) -FAQ este respondem a dúvidas sobre a integração de registos do Azure.
* [Novas funcionalidades de diagnóstico do Azure e os registos de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta mensagem de blogue apresenta-lhe os registos de auditoria do Azure e outras funcionalidades que o ajudam a obterem informações sobre as operações dos seus recursos Azure.
