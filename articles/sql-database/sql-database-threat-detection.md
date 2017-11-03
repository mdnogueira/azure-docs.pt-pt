---
title: "Deteção - base de dados SQL do Azure de ameaças | Microsoft Docs"
description: "A deteção de ameaças Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 06/19/2017
ms.author: ronmat; ronitr
ms.openlocfilehash: 647bd11fe305e255ab492939586241e28557ead8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database-threat-detection"></a>Deteção de ameaças de base de dados do SQL Server

SQL Server a deteção de ameaças Deteta atividades anómalas, indicando invulgar e potencialmente prejudicial tenta aceder ou explorar menores, bases de dados.

## <a name="overview"></a>Descrição geral

A deteção de ameaças do SQL Server fornece uma nova camada de segurança, o que permite que os clientes detetar e reagir a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança em atividades anómalas.  Os utilizadores recebem um alerta após a atividades suspeitas da base de dados, potenciais vulnerabilidades e ataques de injeção de SQL, bem como os padrões de acesso de base de dados anómalas. Alertas de deteção de ameaças do SQL Server fornecem detalhes de atividade suspeita e recomenda ação sobre a investigar e mitigar a ameaça. Os utilizadores podem explorar os eventos suspeitos utilizando [auditoria de base de dados SQL](sql-database-auditing.md) para determinar se eles resultam de uma tentativa de aceder, infringir ou exploram os dados na base de dados. A deteção de ameaças torna simples para resolver potenciais ameaças à base de dados sem a necessidade de ser um especialista de segurança ou faça a gestão de sistemas de monitorização de segurança avançada.

Por exemplo, a injeção de SQL é uma dos problemas comuns de segurança de aplicação Web na Internet, utilizado para atacar aplicações condicionada por dados. Os atacantes tirar partido de vulnerabilidades de aplicação para injetar maliciosas instruções SQL para campos de entrada de aplicação, ser, ou modificar dados na base de dados.

A deteção de ameaças do SQL Server integra-se de alertas com [Centro de segurança do Azure](https://azure.microsoft.com/en-us/services/security-center/), e, em cada servidor de base de dados do SQL Server protegido é faturado o mesmo preço como o escalão padrão de centro de segurança do Azure, no $15/nó/mês, onde cada protegidas base de dados SQL servidor é contabilizado como um nó.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurar a deteção de ameaças para a base de dados no portal do Azure
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para a página de configuração da base de dados SQL Server que pretende monitorizar. Na página de definições, selecione **a deteção de ameaças e auditoria**. 
    ![Painel de navegação][1]
3. No **a deteção de ameaças e auditoria** página de configuração, ative **ON** auditoria, que apresentam as definições de deteção de ameaças.
  
    ![Painel de navegação][2]
4. Ativar **ON** deteção de ameaças.
5. Configure a lista de mensagens de correio eletrónico para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
6. Clique em **guardar** no **deteção de ameaças e auditoria** página para guardar a nova ou atualizada de auditoria e ameaças as definições de deteção.
       
    ![Painel de navegação][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configurar a deteção de ameaças através do PowerShell

Para obter um exemplo de script, consulte [configurar a auditoria e deteção de ameaças através do PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades de base de dados anómalas mediante a deteção de um evento suspeita
1. Receberá uma notificação por e-mail mediante a deteção de atividades de base de dados anómalas. <br/>
   O e-mail fornece informações sobre o evento de segurança suspeita, incluindo a natureza as atividades anómalas, nome de base de dados, nome do servidor, nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre as causas possíveis e as ações para investigar e mitigar a potencial ameaça à base de dados recomendadas.<br/>
     
    ![Painel de navegação][4]
2. O alerta de e-mail inclui uma ligação direta para o registo de auditoria de SQL. Ao clicar nesta hiperligação inicia o portal do Azure e abre-se os registos de auditoria de SQL perto da hora do evento suspeita. Clique num registo de auditoria para ver mais detalhes sobre as atividades suspeitas da base de dados, tornando mais fácil localizar as instruções SQL que foram executadas (quem acedeu às, que fizeram e quando) e determinar se o evento foi legítimos ou malicioso (por exemplo, a vulnerabilidade de aplicação para a injeção de SQL foi forem explorada, alguém infringido dados confidenciais, etc.).<br/>
   ![Painel de navegação][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Explorar a alertas de deteção de ameaças para a base de dados no portal do Azure

Deteção de ameaças de base de dados do SQL Server integra-se os alertas com [Centro de segurança do Azure](https://azure.microsoft.com/en-us/services/security-center/). Um mosaico de segurança do SQL Server em direto na página da base de dados no portal do Azure controla o estado das ameaças de Active Directory. 

   ![Painel de navegação][6]
   
1. Mosaico segurança clicando no SQL inicia a página de alertas do Centro de segurança do Azure e fornece uma descrição geral do Active Directory ameaças SQL detetado na base de dados. 

  ![Painel de navegação][7]

2. Clicar num alerta específico fornece detalhes adicionais e ações para investigar esta ameaça e a remediação ameaças futuras.

  ![Painel de navegação][8]


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a deteção de ameaças, visite o [blogue do Azure](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Saiba mais sobre [auditoria de base de dados SQL do Azure](sql-database-auditing.md)
* Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)
* Para obter mais detalhes sobre preços, consulte o [página de preços de base de dados do SQL Server](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* Para obter um exemplo de script do PowerShell, consulte [configurar a auditoria e deteção de ameaças através do PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


