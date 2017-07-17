---
title: "Relatórios do Azure Active Directory: Introdução | Microsoft Docs"
description: "Lista os vários relatórios disponíveis no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: pt-pt
ms.lasthandoff: 02/24/2017

---
# Introdução aos Relatórios do Azure Active Directory
<a id="getting-started-with-azure-active-directory-reporting" class="xliff"></a>
## O que é
<a id="what-it-is" class="xliff"></a>
O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. Aqui está uma lista dos relatórios incluídos:

### Relatórios de segurança
<a id="security-reports" class="xliff"></a>
* Inícios de sessão de fontes desconhecidas
* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas
* Inícios de sessão de endereços IP com atividade suspeita
* Atividades irregulares de início de sessão
* Inícios de sessão de dispositivos possivelmente infetados
* Utilizadores com atividade anómala de início de sessão

### Relatórios de atividade
<a id="activity-reports" class="xliff"></a>
* Utilização da aplicação: resumo
* Utilização da aplicação: detalhado
* Dashboard de aplicações
* Erros de aprovisionamento de contas
* Dispositivos de utilizadores individuais
* Atividade de utilizadores individuais
* Relatório de atividade de grupos
* Relatório de atividade de registo de reposição de palavra-passe
* Atividade de reposição de palavra-passe

### Relatórios de auditoria
<a id="audit-reports" class="xliff"></a>
* Relatório de auditoria de diretório

> [!TIP]
> Para obter mais documentação sobre os relatórios do Azure AD, consulte o artigo [Ver relatórios de acesso e utilização](active-directory-view-access-usage-reports.md).
> 
> 

## Como funciona
<a id="how-it-works" class="xliff"></a>
### Pipeline de relatórios
<a id="reporting-pipeline" class="xliff"></a>
O pipeline de relatórios consiste em três passos principais. Sempre que um utilizador inicia sessão ou é feita uma autenticação, acontecerá o seguinte:

* Primeiro, o utilizador é autenticado (com êxito ou não) e o resultado é armazenado nas bases de dados de serviço do Azure Active Directory.
* Em intervalos regulares, todos os inícios de sessão recentes são processados. Nesse momento, os nossos algoritmos de atividade anómala e segurança estão à procura de atividade suspeita em todos os inícios de sessão recentes.
* Após o processamento, os relatórios são escritos, ficam em cache e servidos no Portal Clássico do Azure.

### Tempos de geração dos relatórios
<a id="report-generation-times" class="xliff"></a>
Devido ao grande volume de autenticações e inícios de sessão processados pela plataforma do Azure AD, os inícios de sessão mais recentes processados são, em média, processados uma hora depois. Em casos raros, pode demorar até 8 horas para processar os inícios de sessão mais recentes.

Pode localizar o mais recente início de sessão processado, examinando o texto de ajuda na parte superior de cada relatório.

![Texto de ajuda na parte superior de cada relatório](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Para obter mais documentação sobre os relatórios do Azure AD, consulte o artigo [Ver relatórios de acesso e utilização](active-directory-view-access-usage-reports.md).
> 
> 

## Introdução
<a id="getting-started" class="xliff"></a>
### Inicie sessão no Portal Clássico do Azure
<a id="sign-into-the-azure-classic-portal" class="xliff"></a>
Primeiro será necessário iniciar sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como um administrador global ou de compatibilidade. Também será necessário ser um administrador ou coadministrador de serviço de subscrição do Azure ou utilizar a subscrição do Azure “Acesso ao Azure AD”.

### Navegue até aos Relatórios
<a id="navigate-to-reports" class="xliff"></a>
Para ver Relatórios, navegue até ao separador Relatórios na parte superior do diretório.

Se esta for a primeira vez que visualiza relatórios, terá de aceitar uma caixa de diálogo antes de poder visualizar os relatórios. Isto é para garantir que é aceitável para os administradores da organização ver estes dados, que podem ser considerados informação privada em alguns países/regiões.

![Caixa de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### Explorar cada relatório
<a id="explore-each-report" class="xliff"></a>
Navegue em cada relatório para ver os dados obtidos e os inícios de sessão processados. Pode encontrar uma [lista de todos os relatórios aqui](active-directory-reporting-guide.md).

![Todos os relatórios](./media/active-directory-reporting-getting-started/reportsMain.png)

### Transferir os relatórios como CSV
<a id="download-the-reports-as-csv" class="xliff"></a>
Cada relatório pode ser transferido como ficheiro CSV (valores separados por vírgulas). Pode utilizar estes ficheiros no Excel, PowerBI ou programas de análise de terceiros para analisar ainda mais os seus dados.

Para transferir qualquer relatório como CSV, navegue até ao relatório e clique em "Transferir" na parte inferior.

![Botão Transferir](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Para obter mais documentação sobre os relatórios do Azure AD, consulte o artigo [Ver relatórios de acesso e utilização](active-directory-view-access-usage-reports.md).
> 
> 

## Passos seguintes
<a id="next-steps" class="xliff"></a>
### Personalizar alertas para atividade anómala de início de sessão
<a id="customize-alerts-for-anomalous-sign-in-activity" class="xliff"></a>
Navegue até ao separador “Configurar” do diretório.

Desloque-se para a secção “Notificações”.

Ative ou desative a secção “Notificações por email de inícios de sessão anómalos”.

![A secção de Notificações](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Integrar com a API do Relatório do Azure AD
<a id="integrate-with-the-azure-ad-reporting-api" class="xliff"></a>
Consulte o artigo [Introdução à API do Relatório](active-directory-reporting-api-getting-started.md).

### Acionar o Multi-Factor Authentication nos utilizadores
<a id="engage-multi-factor-authentication-on-users" class="xliff"></a>
Selecione um utilizador num relatório.

Clique no botão “Ativar a MFA” na parte inferior do ecrã.

![O botão do Multi-Factor Authentication na parte inferior do ecrã](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Para obter mais documentação sobre os relatórios do Azure AD, consulte o artigo [Ver relatórios de acesso e utilização](active-directory-view-access-usage-reports.md).
> 
> 

## Saiba mais
<a id="learn-more" class="xliff"></a>
### Eventos de auditoria
<a id="audit-events" class="xliff"></a>
Saiba mais sobre os eventos auditados no diretório em [Eventos de auditoria de Relatórios do Azure Active Directory](active-directory-reporting-audit-events.md).

### Integração de API
<a id="api-integration" class="xliff"></a>
Consulte o artigo [Introdução à API de Relatórios](active-directory-reporting-api-getting-started.md) e a [documentação de referência da API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Contactos
<a id="get-in-touch" class="xliff"></a>
Envie um e-mail para [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com), para receber comentários, ajuda ou esclarecer qualquer dúvida.

> [!TIP]
> Para obter mais documentação sobre os relatórios do Azure AD, consulte o artigo [Ver relatórios de acesso e utilização](active-directory-view-access-usage-reports.md).
> 
> 


