---
title: "Começar a utilizar a deteção de ameaças de armazém de dados SQL"
description: "Como começar com a deteção de ameaças"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-threat-detection"></a>Começar a utilizar a deteção de ameaças
> [!div class="op_single_selector"]
> * [Auditoria](sql-data-warehouse-auditing-overview.md)
> * [Deteção de ameaças](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Descrição geral
A deteção de ameaças Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados. A deteção de ameaças está em pré-visualização e é suportada para o SQL Data Warehouse.

A deteção de ameaças fornece uma nova camada de segurança, o que permite que os clientes detetar e reagir a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança em atividades anómalas. Os utilizadores podem explorar os eventos suspeitos utilizando [auditoria do armazém de dados de SQL do Azure](sql-data-warehouse-auditing-overview.md) para determinar se eles resultam de uma tentativa de aceder, infringir ou exploram os dados no armazém de dados.
A deteção de ameaças torna simples a potenciais ameaças de endereço para o armazém de dados sem a necessidade de ser um especialista de segurança ou faça a gestão de sistemas de monitorização de segurança avançada.

Por exemplo, a deteção de ameaças Deteta determinadas atividades de base de dados anómalas, indicando potenciais tentativas de injeção de SQL. Injeção de SQL é uma dos problemas comuns de segurança de aplicação Web na Internet, utilizado para atacar aplicações condicionada por dados. Os atacantes tirar partido de vulnerabilidades de aplicação para injetar maliciosas instruções SQL para campos de entrada de aplicação, para ser, ou modificar dados na base de dados.

## <a name="set-up-threat-detection-for-your-database"></a>Configurar a deteção de ameaças para a base de dados
1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para o painel de configuração do armazém de dados do SQL Server que pretende monitorizar. No painel de definições, selecione **a deteção de ameaças e auditoria**.
   
    ![Painel de navegação][1]
3. No **a deteção de ameaças e auditoria** configuração painel ative **ON** auditoria, que apresentará as definições de deteção de ameaças.
   
    ![Painel de navegação][2]
4. Ativar **ON** deteção de ameaças.
5. Configure a lista de mensagens de correio eletrónico que vão receber alertas de segurança mediante a deteção de atividades de armazém de dados anómalas.
6. Clique em **guardar** no **deteção de ameaças e auditoria** painel de configuração para guardar a nova ou atualizada auditoria e política de deteção da ameaça.
   
    ![Painel de navegação][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades do armazém de dados anómalas mediante a deteção de um evento suspeita
1. Receberá uma notificação por e-mail mediante a deteção de atividades de base de dados anómalas. <br/>
   O e-mail irá fornecer informações sobre o evento de segurança suspeita, incluindo a natureza as atividades anómalas, nome de base de dados, nome do servidor e a hora do evento. Além disso, será fornecem informações sobre as causas possíveis e as ações para investigar e mitigar a potencial ameaça à base de dados recomendadas.<br/>
   
    ![Painel de navegação][4]
2. No e-mail, clique em de **registo de auditoria de SQL do Azure** ligação, que irá iniciar o Portal clássico do Azure e mostrar os registos de auditoria relevantes perto da hora do evento suspeita.
   
    ![Painel de navegação][5]
3. Clique nos registos de auditoria para ver mais detalhes sobre as atividades suspeitas da base de dados, tais como instrução de SQL, IP de cliente e o motivo da falha.
   
    ![Painel de navegação][6]
4. No painel de registos de auditoria, clique em **abrir no Excel** para abrir um pré-configurado do excel modelo a importar e executar uma análise mais aprofundada do registo de auditoria perto da hora do evento suspeita.<br/>
   **Nota:** no Excel 2010 ou posterior, o Power consulta e o **combinar Rápido** definição é necessária
   
    ![Painel de navegação][7]
5. Para configurar o **combinar Rápido** definição - na **POWER QUERY** separador, selecione **opções** para apresentar a caixa de diálogo Opções. Selecione a secção de privacidade e escolha a segunda opção - 'Ignorar os níveis de privacidade e melhorar potencialmente o desempenho':
   
    ![Painel de navegação][8]
6. Para carregar os registos de auditoria SQL, certifique-se de que os parâmetros nas definições do separador estão definidas corretamente e, em seguida, selecione o friso 'Data' e clique no botão 'Atualizar todos os'.
   
    ![Painel de navegação][9]
7. Os resultados são apresentados no **registos de auditoria de SQL** folha que lhe permite executar uma análise mais profunda das atividades anómalas que foram detetados e mitigar o impacto do evento de segurança na sua aplicação.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
