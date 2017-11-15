---
title: Proteger a base de dados SQL do Azure | Microsoft Docs
description: "Saiba mais sobre as técnicas e funcionalidades para proteger a base de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/28/2017
ms.author: daredis
ms.openlocfilehash: 90c03f1538197e1cd1c90165417a4ec74c9c5961
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="secure-your-azure-sql-database"></a>Proteger a base de dados SQL do Azure

A Base de Dados SQL protege os seus dados ao limitar o acesso à base de dados com regras de firewall, mecanismos de autenticação que exigem que os utilizadores forneçam a sua identidade e autorização a dados através de permissões e associações baseadas em funções, bem como através de segurança ao nível da linha e máscara de dados dinâmicos.

Pode melhorar a proteção da base de dados contra utilizadores mal intencionados ou acesso não autorizado com apenas alguns passos simples. Neste tutorial que aprende a: 

> [!div class="checklist"]
> * Configurar regras de firewall ao nível do servidor para o servidor no portal do Azure
> * Configurar regras de firewall ao nível da base de dados para a base de dados com o SSMS
> * Ligar à base de dados através de uma cadeia de ligação segura
> * Gerir o acesso de utilizador
> * Proteger os seus dados com a encriptação
> * Ativar a auditoria de base de dados SQL
> * Ativar a deteção de ameaças de base de dados SQL

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de que tem o seguinte:

- Instalou a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Microsoft Excel instalado
- Criar um servidor SQL do Azure e a base de dados - Consulte [criar uma base de dados SQL do Azure no portal do Azure](sql-database-get-started-portal.md), [criar uma base de dados SQL do Azure único utilizando a CLI do Azure](sql-database-get-started-cli.md), e [criar um único SQL do Azure base de dados com o PowerShell](sql-database-get-started-powershell.md). 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

Bases de dados do SQL Server estão protegidas por uma firewall no Azure. Por predefinição, todas as ligações ao servidor e as bases de dados no servidor são rejeitadas exceto ligações a partir de outros serviços do Azure. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL e ao nível da base de dados](sql-database-firewall-configure.md).

A configuração mais segura é a definição "permitir o acesso aos serviços do Azure' para OFF. Se precisar de ligar à base de dados a partir de um serviço de nuvem ou VM do Azure, deve criar um [IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) e permitir que apenas o reservado IP endereço acesso através da firewall. 

Siga estes passos para criar um [regra de firewall ao nível do servidor de base de dados SQL](sql-database-firewall-configure.md) para o servidor permitir ligações a partir de um endereço IP específico. 

> [!NOTE]
> Se tiver criado uma base de dados de exemplo no Azure utilizando um dos tutoriais anteriores ou inícios rápidos e estiver a executar este tutorial num computador com o mesmo endereço IP que tinha quando walked através estes tutoriais, pode ignorar este passo porque terá já cre ated uma regra de firewall ao nível do servidor.
>

1. Clique em **bases de dados SQL** no menu da esquerda e clique em regras da base de dados que pretende configurar a firewall para o **bases de dados SQL** página. Abre a página de descrição geral da base de dados, que mostra o nome de servidor completamente qualificado (tais como **mynewserver 20170313.database.windows.net**) e fornece opções para continuar a configuração.

      ![regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Clique em **Configurar firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP público do computador ligado para o portal com ou introduzir manualmente a regra de firewall e, em seguida, clique em **guardar**.

      ![configurar regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Clique em **OK** e, em seguida, clique no **X** para fechar a página **Definições da firewall**.

Agora pode ligar a uma base de dados no servidor com o endereço IP especificado ou o intervalo de endereços IP.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao seu servidor de Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Criar uma regra de firewall ao nível da base de dados com o SSMS

Regras de firewall ao nível da base de dados permitem-lhe para criar definições de firewall diferente para bases de dados diferentes no mesmo servidor lógico e crie regras de firewall são portátil - o que significa que se seguem a base de dados durante um [ativação pós-falha](sql-database-geo-replication-overview.md) em vez de ser armazenados no SQL server. Regras de firewall ao nível da base de dados só podem ser configurado utilizando instruções Transact-SQL e apenas depois de ter configurado a primeira regra de firewall ao nível do servidor. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL e ao nível da base de dados](sql-database-firewall-configure.md).

Segue estes passos para criar uma regra de firewall de base de dados específica.

1. Ligar à base de dados, por exemplo utilizando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. No Object Explorer, clique com botão direito na base de dados que pretende adicionar uma regra de firewall para e clique em **nova consulta**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.

3. Na janela da consulta, modificar o endereço IP para o seu endereço IP público e, em seguida, execute a seguinte consulta:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Na barra de ferramentas, clique em **executar** para criar a regra de firewall.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Ver como ligar uma aplicação à base de dados através de uma cadeia de ligação segura

Para garantir uma ligação segura, encriptada entre uma aplicação de cliente e a base de dados do SQL Server, a cadeia de ligação tem de ser configurada para:

- Pedir uma ligação encriptada, e
- Não confiar no certificado de servidor. 

Isto estabelece uma ligação com segurança de camada de transporte (TLS) e reduz o risco de ataques man-in-the-middle. Pode obter cadeias de ligação corretamente configurados para a base de dados do SQL Server para o cliente suportado controladores do portal do Azure conforme mostrado para ADO.net, nesta captura de ecrã.

1. Selecione **bases de dados SQL** no menu da esquerda e clique em sua base de dados no **bases de dados SQL** página.

2. No **descrição geral** página para a base de dados, clique em **Mostrar cadeias de ligação de base de dados**.

3. Reveja a cadeia de ligação **ADO.NET** completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Criar utilizadores de base de dados

Antes de criar os utilizadores, tem primeiro de escolher um dos dois tipos de autenticação suportados pelo SQL Database do Azure: 

**Autenticação do SQL Server**, que utiliza o nome de utilizador e palavra-passe para inícios de sessão e os utilizadores que são válidos apenas no contexto de uma base de dados específica dentro de um servidor lógico. 

**Azure autenticação do Active Directory**, que utiliza identidades geridas pelo Azure Active Directory. 

Se pretender utilizar [do Azure Active Directory](./sql-database-aad-authentication.md) para se autenticarem nas base de dados SQL, uma preenchidos Azure Active Directory tem de existir antes de poder continuar.

Siga estes passos para criar um utilizador a utilizar a autenticação do SQL Server:

1. Ligar à base de dados, por exemplo utilizando [SQL Server Management Studio](./sql-database-connect-query-ssms.md) utilizando as credenciais de administrador do servidor.

2. No Object Explorer, clique com botão direito na base de dados que pretende adicionar um novo utilizador e clique em **nova consulta**. Será apresentada uma janela de consulta em branco que está ligado à base de dados selecionada.

3. Na janela da consulta, introduza a seguinte consulta:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Na barra de ferramentas, clique em **executar** para criar o utilizador.

5. Por predefinição, o utilizador pode ligar à base de dados, mas não tem permissões para ler ou escrever dados. Para conceder as permissões para o utilizador recentemente criado, execute os dois comandos seguintes numa nova janela consulta

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

É recomendável criar estas contas de administrador não ao nível da base de dados para ligar à base de dados, a menos que terá de executar tarefas de administrador, como a criação de novos utilizadores. Reveja o [tutorial do Azure Active Directory](./sql-database-aad-authentication-configure.md) sobre como efetuar a autenticação utilizando o Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Proteger os seus dados com a encriptação

Encriptação de dados transparente de base de dados SQL do Azure (TDE) encripta automaticamente dados inativos, sem necessidade de quaisquer alterações à aplicação aceder à base de dados encriptado. Para bases de dados recentemente criados, TDE está ativada por predefinição. Para ativar o TDE da base de dados ou verifique se TDE, siga estes passos:

1. Selecione **bases de dados SQL** no menu da esquerda e clique em sua base de dados no **bases de dados SQL** página. 

2. Clique em **encriptação transparente de dados** para abrir a página de configuração para TDE.

    ![Encriptação de Dados Transparente](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Se necessário, defina **encriptação de dados** como ON e clique em **guardar**.

Inicia o processo de encriptação em segundo plano. Pode monitorizar o progresso estabelecendo ligação à base de dados do SQL Server utilizar [SQL Server Management Studio](./sql-database-connect-query-ssms.md) consultando a coluna encryption_state o `sys.dm_database_encryption_keys` vista.

## <a name="enable-sql-database-auditing-if-necessary"></a>Ativar a auditoria de base de dados SQL, se necessário

Auditoria de base de dados SQL do Azure controla os eventos de base de dados e escreve-los para uma auditoria iniciar sessão na sua conta do Storage do Azure. Auditoria pode ajudar a manter a conformidade de regulamentação, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que possam indicar a potencial violação de segurança. Siga estes passos para criar uma predefinição de auditoria de política para a base de dados do SQL Server:

1. Selecione **bases de dados SQL** no menu da esquerda e clique em sua base de dados no **bases de dados SQL** página. 

2. No painel de definições, selecione **a deteção de ameaças e auditoria**. Tenha em atenção que ao nível do servidor de auditoria é diabled e que existe um **ver definições do servidor** ligação que permite-lhe ver ou modificar o servidor de definições de auditoria neste contexto.

    ![Painel de auditoria](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Se preferir ativar um tipo de auditoria (ou localização?) especificado diferente ao nível do servidor, ative **ON** auditoria e escolha o **Blob** tipo de auditoria. Se estiver ativada a auditoria de Blob de servidor, a auditoria da base de dados configurada existirá side by side with a auditoria de Blob do servidor.

    ![Ativar a auditoria](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Selecione **detalhes armazenamento** para abrir o painel de armazenamento de registos de auditoria. Selecione a conta de armazenamento do Azure onde serão guardados registos e o período de retenção, após o qual os registos antigos serão eliminados, em seguida, clique em **OK** na parte inferior. 

   > [!TIP]
   > Utilize a mesma conta de armazenamento para todas as bases de dados auditadas para tirar o máximo partido os modelos de relatórios de auditoria.
   > 

5. Clique em **Guardar**.

> [!IMPORTANT]
> Se pretender personalizar os eventos auditados, isto pode ser feito através do PowerShell ou a API de REST - consulte [auditoria de base de dados SQL](sql-database-auditing.md) para obter mais detalhes.
>

## <a name="enable-sql-database-threat-detection"></a>Ativar a deteção de ameaças de base de dados SQL

A deteção de ameaças fornece uma nova camada de segurança, o que permite que os clientes detetar e reagir a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança em atividades anómalas. Os utilizadores podem explorar os eventos suspeitos utilizando a auditoria de base de dados do SQL Server para determinar se eles resultam de uma tentativa de aceder, infringir ou exploram os dados na base de dados. A deteção de ameaças torna simples para resolver potenciais ameaças à base de dados sem a necessidade de ser um especialista de segurança ou faça a gestão de sistemas de monitorização de segurança avançada.
Por exemplo, a deteção de ameaças Deteta determinadas atividades de base de dados anómalas, indicando potenciais tentativas de injeção de SQL. Injeção de SQL é uma dos problemas comuns de segurança de aplicação Web na Internet, utilizado para atacar aplicações condicionada por dados. Os atacantes tirar partido de vulnerabilidades de aplicação para injetar maliciosas instruções SQL para campos de entrada de aplicação, para ser, ou modificar dados na base de dados.

1. Navegue para o painel de configuração da base de dados do SQL Server que pretende monitorizar. No painel de definições, selecione **a deteção de ameaças e auditoria**.

    ![Painel de navegação](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. No **a deteção de ameaças e auditoria** configuração painel ative **ON** auditoria, que apresentará as definições de deteção de ameaças.

3. Ativar **ON** deteção de ameaças.

4. Configure a lista de mensagens de correio eletrónico que vão receber alertas de segurança após a deteção de atividades de base de dados anómalas.

5. Clique em **guardar** no **deteção de ameaças e auditoria** painel para guardar a nova ou atualizada auditoria e política de deteção da ameaça.

    ![Painel de navegação](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Se forem detetadas atividades de base de dados anómalas, receberá uma notificação por e-mail mediante a deteção de atividades de base de dados anómalas. O e-mail irá fornecer informações sobre o evento de segurança suspeita, incluindo a natureza as atividades anómalas, nome de base de dados, nome do servidor e a hora do evento. Além disso, será fornecem informações sobre as causas possíveis e as ações para investigar e mitigar a potencial ameaça à base de dados recomendadas. Os passos seguintes guiá-lo através do que fazer deve receber uma mensagem de e-mail:

    ![E-mail de deteção de ameaças](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. No e-mail, clique em de **registo de auditoria de SQL do Azure** ligar, que irá iniciar o portal do Azure e mostrar os registos de auditorias relevantes perto da hora do evento suspeita.

    ![Registos de auditoria](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Clique nos registos de auditoria para ver mais detalhes sobre as atividades suspeitas da base de dados, tais como instrução de SQL, IP de cliente e o motivo da falha.

    ![Detalhes do registo](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. No painel de registos de auditoria, clique em **abrir no Excel** para abrir um pré-configurado do excel modelo a importar e executar uma análise mais aprofundada do registo de auditoria perto da hora do evento suspeita.

    > [!NOTE]
    > No Excel 2010 ou posterior, o Power consulta e o **combinar Rápido** definição é necessária.

    ![Registos abertos no Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Para configurar o **combinar Rápido** definição - na **POWER QUERY** separador, selecione **opções** para apresentar a caixa de diálogo Opções. Selecione a secção de privacidade e escolha a segunda opção - 'Ignorar os níveis de privacidade e melhorar potencialmente o desempenho':

    ![Combinar rápido do Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Para carregar os registos de auditoria SQL, certifique-se de que os parâmetros nas definições do separador estão definidas corretamente e, em seguida, selecione o friso 'Data' e clique no botão 'Atualizar todos os'.

    ![Parâmetros de Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Os resultados são apresentados no **registos de auditoria de SQL** folha que lhe permite executar uma análise mais profunda das atividades anómalas que foram detetados e mitigar o impacto do evento de segurança na sua aplicação.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a melhorar a proteção da base de dados contra utilizadores mal intencionados ou acesso não autorizado com apenas alguns passos simples.  Aprendeu a: 

> [!div class="checklist"]
> * Configurar regras de firewall para o servidor e/ou da base de dados
> * Ligar à base de dados através de uma cadeia de ligação segura
> * Gerir o acesso de utilizador
> * Proteger os seus dados com a encriptação
> * Ativar a auditoria de base de dados SQL
> * Ativar a deteção de ameaças de base de dados SQL

Avançar para o próximo tutorial para saber como implementar uma base de dados de georreplicação distribuída.

> [!div class="nextstepaction"]
>[Implementar uma base de dados distribuída geograficamente](sql-database-implement-geo-distributed-database.md)

