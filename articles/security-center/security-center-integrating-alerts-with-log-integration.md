---
title: "A integração de alertas do Centro de segurança do Azure com a integração de registos do Azure | Microsoft Docs"
description: "Este artigo ajuda-o a começar a utilizar a integração de alertas do Centro de segurança com a integração de registos do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
ms.openlocfilehash: d13e5b87c446e587091551b22d80fe568d5d8093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>A integração de alertas do Centro de segurança do Azure com a integração de registos do Azure
Muitas operações de segurança e as equipas de resposta a incidentes dependem de uma solução Security Information and Event Management (SIEM) como ponto de partida para a triagem e investigar alertas de segurança. Com a integração de registo do Azure, pode integrar alertas do Centro de segurança do Azure com a sua solução do SIEM.

Integração de registos do Azure suporta atualmente HP ArcSight, Splunk e IBM QRadar.

## <a name="what-logs-can-i-integrate"></a>Os registos que pode a integrar
Azure produz um vasto conjunto registo para cada serviço. Estes registos são categorizados como:

* **Registos de controlo/gestão** que dar visibilidade para as operações do Azure Resource Manager criar, ATUALIZAR e eliminar. Estes eventos de plane controlo estão anexados nos registos de atividade do Azure
* **Dados Plane registos** que dar visibilidade sobre os eventos gerados ao utilizar um recurso do Azure. Um exemplo é o registo de eventos do Windows, onde pode obter informações sobre eventos de segurança de canal de segurança o Visualizador de eventos. Eventos de plane de dados (que foram gerados por uma máquina virtual ou um serviço do Azure) estão anexados por registos de diagnóstico do Azure.

Integração de registos do Azure, atualmente, suporta a integração de:

* Registos de VM do Azure
* Registos de auditoria do Azure
* Alertas do Centro de segurança do Azure

## <a name="install-azure-log-integration"></a>Instalar a integração de registos do Azure
Transferir [integração de registos do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração de registos do Azure recolhe dados de telemetria a partir do computador no qual está instalado.  Os dados telemétricos recolhidos são:

* Exceções que ocorrem durante a execução de integração de registos do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre qual Azlog.exe Opções da linha de comandos estão a ser utilizadas

> [!NOTE]
> Pode desativar a recolha de dados de telemetria desmarcando esta opção.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar alertas do Centro de segurança e os registos de auditoria do Azure
1. Abra a linha de comandos e **cd** para **c:\Program Files\Microsoft Azure registo integração**.
2. Execute o **azlog createazureid** comando para criar um [Principal de serviço de diretório do Azure Active Directory](../active-directory/active-directory-application-objects.md) em inquilinos do Azure Active Directory (AD) que alojam as subscrições do Azure.

    É-lhe pedido para o início de sessão do Azure.

   > [!NOTE]
   > Tem de ser a proprietário da subscrição ou Coadministrador da subscrição.
   >
   >

    Autenticação do Azure é feita através do Azure AD.  Criar um principal de serviço para a integração de registos do Azure cria a identidade do Azure AD, que é dado acesso ao ler a partir de subscrições do Azure.
3. Execute o **azlog autorizar <SubscriptionID>**  comando para atribuir acesso de leitor de subscrição para o principal de serviço que criou no passo 2. Se não especificar um **SubscriptionID**, em seguida, o principal de serviço é atribuído a função de leitor para todas as subscrições para o qual tem acesso.

   > [!NOTE]
   > Poderá ver avisos se executar o **autorizar** comando imediatamente após o **createazureid** comando. Não há alguma latência entre quando é criada a conta do Azure AD e quando a conta está disponível para utilização. Se Aguarde cerca de 10 segundos após a execução de **createazureid** comando a executar o **autorizar** comando, em seguida, a não deve ser apresentado estes avisos.
   >
   >
4. Verifique as seguintes pastas para confirmar que os ficheiros de JSON do registo de auditoria existem:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verifique as seguintes pastas para confirmar que os alertas do Centro de segurança existem nos mesmos:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Configure o conector de reencaminhador de ficheiro do SIEM para a pasta adequada. O procedimento irá variar consoante o SIEM está a utilizar.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre definições de propriedades e registos de atividade do Azure, consulte:

* [Auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenha as mais recentes notícias de segurança do Azure e as informações.
