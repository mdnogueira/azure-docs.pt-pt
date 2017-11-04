---
title: "Serviço de centro de segurança do Azure e SQL Database do Azure | Microsoft Docs"
description: "Este artigo mostra como o Centro de segurança pode ajudar a proteger as bases de dados na base de dados do Azure SQL."
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Serviço de centro de segurança do Azure e SQL Database do Azure
O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Este artigo mostra como o Centro de segurança pode ajudar a proteger as bases de dados na base de dados do Azure SQL.

## <a name="why-use-security-center"></a>Porquê utilizar o Centro de Segurança?
Centro de segurança ajuda a salvaguardar os dados na base de dados do SQL Server, fornecendo visibilidade para a segurança de todos os servidores e bases de dados. Centro de segurança, pode:

* Defina políticas para auditoria e de encriptação de base de dados SQL.
* Monitorize a segurança dos recursos de base de dados SQL em todas as subscrições.
* Rapidamente identificar e resolver problemas de segurança.
* Integrar alertas a partir do [deteção de ameaças da SQL Database do Azure](../sql-database/sql-database-threat-detection.md).

Para além de ajudar a proteger os seus recursos de base de dados SQL, o Centro de segurança também fornece monitorização de segurança e gestão de máquinas virtuais do Azure, serviços em nuvem, serviços de aplicações, redes virtuais e muito mais. Saiba mais acerca do Centro de segurança [aqui](security-center-intro.md).

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. O escalão gratuito do Centro de segurança está ativado com a sua subscrição. Para obter mais informações sobre o Centro de segurança escalões gratuito e Standard, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).

Centro de segurança suporta o acesso baseado em funções. Para saber mais sobre o controlo de acesso baseado em funções (RBAC) no Azure, consulte o artigo [controlo de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md). FAQ do Centro de segurança fornece informações sobre [como permissões são processadas no Centro de segurança](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Aceder ao Centro de Segurança
Pode aceder ao Centro de Segurança a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sessão portal do](https://portal.azure.com/) e selecione o **opção Centro de segurança**.

![Opção de centro de segurança][1]

O **Centro de segurança** abre o painel.
![Painel Centro de segurança][2]

## <a name="set-security-policy"></a>Definir política de segurança
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada ou o grupo de recursos. No Centro de segurança, é possível definir políticas para as suas subscrições ou grupos de recursos, de acordo com as necessidades de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

Pode definir uma política para mostrar recomendações para auditoria de SQL e a encriptação transparente de dados do SQL Server (TDE).

* Quando ativar **auditoria de SQL e deteção de ameaças**, o Centro de segurança recomenda que a auditoria de acesso à base de dados do Azure esteja ativada de conformidade, deteção e efeitos de investigação avançadas.
* Quando ativar **encriptação transparente de dados SQL**, o Centro de segurança recomenda que a encriptação Inativos estar ativado para a SQL Database do Azure, cópias de segurança associadas e ficheiros de registo de transações.

Para definir uma política de segurança, selecione o **política** mosaico no painel do Centro de segurança. No **política de segurança** painel, selecione a subscrição na qual pretende ativar a política de segurança. Selecione **política de prevenção** e ative **no** as recomendações de segurança que pretende utilizar nesta subscrição.
![Política de segurança][3]

Para obter mais informações, consulte [definir políticas de segurança](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gerir a recomendação de segurança
O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários.

Depois de definir uma política de segurança, o Centro de segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. As recomendações são apresentadas num formato de tabela em que cada linha representa uma recomendação específica. Utilize a tabela seguinte como uma referência para ajudar a compreender as recomendações disponíveis para a SQL Database do Azure e o que faz cada recomendação se aplicá-lo. Selecionar uma recomendação leva-o para um artigo que explica como implementar a recomendação no Centro de segurança.

| Recomendação | Descrição |
| --- | --- |
| [Ativar a auditoria e deteção de ameaças em servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda-se que ative a auditoria e deteção de ameaças para servidores de base de dados SQL. (Apenas para serviço de base de dados SQL. Não inclui o Microsoft SQL Server em execução em máquinas virtuais.) |
| [Ativar a auditoria e deteção de ameaças nas bases de dados do SQL Server](security-center-enable-auditing-on-sql-databases.md) |Recomenda-se que ative a auditoria e deteção de ameaças para bases de dados de base de dados SQL. (Apenas para serviço de base de dados SQL. Não inclui o Microsoft SQL Server em execução em máquinas virtuais.) |
| [Ativar a Encriptação de Dados Transparente](security-center-enable-transparent-data-encryption.md) |Recomenda-se de que permitem a encriptação de bases de dados do SQL Server. (Apenas serviço base de dados SQL.) |

Para ver as recomendações para os seus recursos do Azure, selecione o **recomendações** mosaico no painel do Centro de segurança. No **recomendações** painel, selecione uma recomendação para ver os detalhes. Neste exemplo, vamos selecione **deteção de ameaças e ativar a auditoria em servidores SQL**.

![Recomendações][4]

Como é mostrado abaixo, o Centro de segurança mostra os SQL servers em que a auditoria e deteção de ameaças não estão ativadas. Depois de ativar a auditoria, pode configurar as definições de deteção de ameaças e as definições de e-mail para receber alertas de segurança. A deteção de ameaças alerta quando Deteta atividades de base de dados anómalas que indicam potenciais ameaças de segurança para a base de dados. Os alertas são apresentados no dashboard do Centro de segurança.
![Auditoria e deteção de ameaças][5]

Siga os passos no [deteção de ameaças de base de dados SQL no portal do Azure](../sql-database/sql-database-threat-detection-portal.md) para ativar e configurar a deteção de ameaças e para configurar a lista de mensagens de correio eletrónico que vão receber alertas de segurança mediante a deteção de atividades anómalas.

Para saber mais sobre as recomendações, consulte o artigo [gerir recomendações de segurança](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorizar o estado de funcionamento da segurança
Depois de ativar [políticas de segurança](security-center-policies.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades.  Pode ver o estado de segurança dos seus recursos no **estado de funcionamento de segurança de recursos** mosaico. Ao clicar em **dados** no **estado de funcionamento de segurança de recursos** mosaico, o **recursos de dados** painel abre-se com recomendações de SQL para problemas tais como a encriptação de dados de auditoria e transparente não ativada. Também tem recomendações para o estado de funcionamento geral da base de dados.
![Estado de funcionamento de segurança de recursos][6]

Para obter mais informações, consulte [monitorização de estado de funcionamento de segurança](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gerir e responder a alertas de segurança
Centro de segurança automaticamente recolhe, analisa e integra-se os dados de registo de [deteção de ameaças de SQL do Azure](../sql-database/sql-database-threat-detection.md), assim como outros recursos do Azure, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

Para ver alertas, selecione o **alertas de segurança** mosaico no painel do Centro de segurança. No **alertas de segurança** painel, selecione um alerta para obter mais informações sobre os eventos que acionou o alerta e o que, se aplicável, os passos precisa de tomar para remediar um ataque. Neste exemplo, vamos selecione **injeção de SQL potenciais**.
![Alertas de segurança][7]

Como é mostrado abaixo, o Centro de segurança fornece detalhes adicionais facultam informações aprofundadas que acionou o alerta, o recurso de destino, quando aplicável o endereço IP de origem e as recomendações sobre como remediar.
![Potencial injeção de SQL][8]

Para obter mais informações, consulte [gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Passos seguintes
* [FAQ do Centro de segurança](security-center-faq.md) – encontre as perguntas mais frequentes sobre como utilizar o serviço.
* [Guia de operações e planeamento do Centro de segurança](security-center-planning-and-operations-guide.md) - um conjunto de passos e tarefas para otimizar a utilização do Centro de segurança com base nos requisitos de segurança da sua organização e o modelo de gestão de nuvem.
* [Segurança de dados do Centro de segurança](security-center-data-security.md) – Saiba como o Centro de segurança recolhe e processa dados sobre os seus recursos do Azure, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha e muito mais.
* [Processamento de incidentes de segurança](security-center-incident.md) -aprender a utilizar a capacidade de alerta de segurança no Centro de segurança para ajudar no processamento de incidentes de segurança.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
