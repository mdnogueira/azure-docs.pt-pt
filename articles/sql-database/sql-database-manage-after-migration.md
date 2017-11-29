---
title: "Gerir após a migração - SQL Database do Azure | Microsoft Docs"
description: "Saiba como gerir a sua base de dados após a migração para a SQL Database do Azure."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Como posso gerir os meus SQL database do Azure após a migração?

*Perguntas mais frequentes sobre à volta de gerir os seus investimentos SQL Database do Azure*

Por isso tiver sido movido recentemente bases de dados do SQL Server a SQL Database do Azure ou, talvez que estiver a planear em mover muito em breve. Assim que tiver sido movido, o que é o seguinte? Dado que a base de dados do SQL Server é um *plataforma como serviço*, Microsoft processa várias áreas em seu nome. Mas como exatamente esta muda práticas da sua empresa para a sua chaves áreas, tais como segurança, continuidade do negócio, manutenção de base de dados, a otimização de desempenho, monitorização e muito mais? 

O objetivo deste artigo é para succinctly organizar os recursos e as orientações que irá precisar de efetuar o shift para gerir os seus investimentos de base de dados SQL. As áreas principais neste artigo abrangem continuidade do negócio, a segurança, a manutenção de base de dados e monitorização, movimento de dados de desempenho e. Iremos abranger áreas principais que são diferentes entre o SQL Server e base de dados SQL e chamar melhores práticas operacionais que irão ajudar a maximizar as vantagens e minimizar o risco. 

## <a name="manage-business-continuity-after-migration"></a>Gerir a continuidade do negócio após a migração

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como criar e gerir cópias de segurança na base de dados SQL? 
Base de dados do SQL Server efetua cópias de segurança de bases de dados para si e fornece-lhe a capacidade de restaurar para qualquer ponto no tempo no período de retenção automaticamente. O período de retenção é 35 dias para Standard e Premium bases de dados e de 7 dias para básicos bases de dados. Além disso, a funcionalidade de retenção de longo prazo permite-lhe manter os ficheiros de cópia de segurança durante um período de tempo (até 10 anos) e restaurar a partir destas cópias de segurança em momento algum. Além disso, as cópias de segurança da base de dados estão georreplicação para garantir que a capacidade de georrestauro em qualquer região na eventualidade de ocorrer um desastre ou regional catastrophe. Consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Como posso assegurar a continuidade do negócio em caso de desastre ao nível do Centro de dados ou um catastrophe regional? 

Cópias de segurança da base de dados são georreplicação para garantir que a capacidade de georrestauro em qualquer região na eventualidade de ocorrer um desastre ou regional catastrophe. Consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md). Além disso, a base de dados do SQL Server fornece a capacidade de manter ativamente georreplicação bases de dados secundárias noutra região. A configurar um grupo de ativação de pós-falha automática irá garantir que as bases de dados automaticamente efetuar a ativação pós-falha para o secundário num cenário de desastre. Se não estiver configurado um grupo de ativação de pós-falha automática, em seguida, a aplicação tem ativamente monitorizar um desastre e inicie uma ativação pós-falha para o secundário. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server fornecido-me réplicas secundárias legíveis, posso aceder secundárias na base de dados SQL? 

Sim, a funcionalidade 'Georreplicação ativa' é utilizada para criar as réplicas secundárias legíveis. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>Como meu plano de recuperação após desastre alterar do local para a base de dados SQL? 
Implementações do SQL Server necessário ativamente gerir cópias de segurança utilizando as funcionalidades, tais como Clustering de ativação pós-falha, o espelhamento da base de dados, a replicação, envio de registo ou apenas simples clássica BACPAC as cópias de segurança. No entanto, na base de dados do SQL Server, as cópias de segurança são completamente geridas pela Microsoft e só pode ter planos de recuperação após desastre e de cópia de segurança configurado e a funcionar com apenas alguns cliques no portal do Azure (ou alguns comandos PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>Em caso de desastre, como recupero meu bases de dados? 
Base de dados SQL automaticamente permite-lhe restaurar as bases de dados para qualquer ponto no tempo nos últimos 35 dias. Esta é uma opção se perder dados ou enfrentam um desastre relacionado com a aplicação. 

No caso de enfrentam um desastre regional, se estiverem configurados de georreplicação secundárias bases de dados, pode recuperar a partir das bases de dados de georreplicação secundária no noutra região. Para obter acesso em tempo real para as suas aplicações, pode ativação pós-falha para o secundário georreplicação na região outro manualmente. Em alternativa, se tiver um grupo de ativação de pós-falha automática configurado, esta ativação pós-falha para uma secundária georreplicação ocorre automaticamente um cenário de desastres. Se não tiver georreplicação base de dados secundária configurada, pode continuar a recuperar as bases de dados de ficheiros de cópia de segurança replicados automáticos (uma funcionalidade incorporada, não é necessária configuração), com relativamente mais tempo de recuperação (RTO de 12 horas) e até um perda de dados de hora. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>As ativações pós-falha para o secundário são transparentes? Como a minha aplicação processar as ativações pós-falha de base de dados? 
Se tiver grupos de ativação de pós-falha automaticamente configurados, a ativação pós-falha para o secundário é transparente. No entanto, se não tiver, em seguida, a aplicação tem de incorporar a lógica para monitorizar a disponibilidade dos principais e, em seguida, manualmente efetuar a ativação pós-falha para o secundário. 
 
## <a name="manage-security-after-migration"></a>Gerir a segurança após a migração

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Como posso restringir o acesso ao meu base de dados SQL? 
 
Existem algumas formas para bloquear o acesso de conectividade para as bases de dados do SQL Server. 
1. Limite de tráfego através da rota Internet Express fornece fibra dedicado à rede do Azure para que os dados não viajam através da internet. Também é possível configurar cruzada conectividade região a utilizar o expressroute. As hiperligações seguintes descrevem Express Route mais detalhadamente: 
 - [Introdução no Expressroute](../expressroute/expressroute-introduction.md)
 - [Pré-requisitos](../expressroute/expressroute-prerequisites.md) 
 - [Fluxos de trabalho](../expressroute/expressroute-workflows.md) 
 
2. Selecione os recursos que ligar à base de dados SQL: 

   Por predefinição, a base de dados do SQL Server está configurado para "Permitir que todos os serviços do Azure" – o que significa que todas as VMs no Azure pode tentar ligar à base de dados.  Autenticação de todos os inícios de sessão ainda tem de ocorrer. Se não pretender que a base de dados esteja acessível para todos os IPs do Azure, pode desativar "Permitir que todos os serviços do Azure" e utilizar [pontos finais do serviço de VNET](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso de entrada para a base de dados a partir dos recursos do Azure só estão dentro um determinado Sub-rede VNET do Azure. 

   ![Pontos finais do serviço VNET](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   É uma opção alternativa para aprovisionar [reservado IPs](../virtual-network/virtual-networks-reserved-public-ip.md) para as VMs e lista branca específico os endereços IP da VM no servidor de definições de firewall. (Consulte a captura de ecrã abaixo como um exemplo no portal do Azure.) Ao atribuir IPs reservados, guarde o trabalho de ter de atualizar as regras de firewall com a alteração dos endereços IP. 

3. Evitar a exposição a porta 1433 fora do Azure

   Executar no Azure com o SSMS [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Isto requer a abrir ligações de saída à porta 1433, o IP estático para a base de dados pode ser aberto para apenas o RemoteApp, suporta o Multi-factor Authentication (MFA) e vários utilizador. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>Os métodos de autenticação são fornecidos na base de dados SQL?

Os métodos de autenticação principal oferecido na base de dados SQL e do armazém de dados do SQL Server são a autenticação do Azure Active Directory e autenticação de SQL. Azure Active Directory (AD) é um serviço de gestão de identidades e acessos centralizado e SQL Server é apenas um dos vários serviços do Azure que estão integrados com o Azure AD. A vantagem de um serviço gerido centralizada é que as credenciais de um utilizador é partilhado entre todos os serviços do Azure que utiliza para autenticação mais simples. Isto também permite que a base de dados do SQL Server e o SQL Data Warehouse oferecem autenticação multifator e contas de utilizador convidado dentro de um domínio do Azure AD. 

Se já tiver o Active Directory no local, podem federar o diretório no Azure Active Directory para expandir o seu diretório no Azure. 


|||
|---|---|
| Se…|Base de dados SQL do Azure / do armazém de dados SQL do Azure|
| Prefere para não utilizar o Azure Active Directory (AD) no Azure|Utilize [autenticação SQL](sql-database-security-overview.md)|
| AD utilizado no SQL Server no local|[Federar AD com o Azure AD](../active-directory/connect/active-directory-aadconnect.md)e utilizar a autenticação do Azure AD. Com esta opção, pode utilizar o início de sessão único.|
| Têm de impôr a autenticação multifator (MFA)|Exigir a MFA como uma política através de [acesso condicional do Microsoft](sql-database-conditional-access.md)e utilizar [Universal do Azure AD authentication com suporte MFA](sql-database-ssms-mfa-authentication.md).|
| Ter contas de convidado de contas Microsoft (live.com, outlook.com) ou de outros domínios (gmail.com)|Utilize [autenticação do Azure AD Universal](sql-database-ssms-mfa-authentication.md) no armazém de base de dados do SQL Server, que tira partido da [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Tem sessão iniciada Windows utilizando as credenciais do Azure AD a partir de um domínio federado|Utilize [a autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
| Tem sessão iniciada Windows utilizando credenciais de um domínio não Federado com o Azure|Utilize [autenticação de palavra-passe do Azure AD](sql-database-aad-authentication-configure.md).|
| Possui os serviços de camada média que precisam para ligar à SQL Database do Azure ou do armazém de dados|Utilize [autenticação de token do Azure AD](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Como pode posso limitar o acesso a dados confidenciais na minha bases de dados do lado de aplicação? 

Para impedir que utilizadores não autorizados a capacidade de ver dados confidenciais, existem algumas opções disponíveis na base de dados do SQL Server: 

- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é uma forma de encriptação do lado do cliente que encripta confidenciais colunas na base de dados (para que se encontram no ficheiro de encriptação para administradores de base de dados e os utilizadores não autorizados). A chave para sempre encriptado é armazenada no lado do cliente, pelo que apenas clientes autorizados podem desencriptar as colunas confidenciais. Sempre encriptado suporta comparações de igualdade hoje em dia, para que a DBAs pode continuar a consultar colunas encriptadas como parte do respetivos comandos SQL. Sempre encriptado pode ser utilizado uma variedade de opções de armazenamento de chaves, tais como [Cofre de chaves do Azure](sql-database-always-encrypted-azure-key-vault.md), arquivo de certificados do Windows e módulos de segurança de local hardware.
- [Máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md) é uma funcionalidade de máscara de dados que limita a exposição de dados confidenciais através da máscara-lo a utilizadores não privilegiados na camada da aplicação. Definir uma regra de máscara que pode criar um padrão de máscara (por exemplo, para mostrar apenas últimos 4 dígitos de um ID de national e marcar o resto como X's) e identificar os utilizadores que podem ser excluídos da regra de máscara.
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security) permite-lhe controlo de acesso a linhas numa tabela da base de dados com base no utilizador executar a consulta (grupo associação ou execução contexto). A restrição de acesso é feita na camada de base de dados em vez de uma camada de aplicação, para simplificar a lógica de aplicação. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Que opções de encriptação se é necessário na base de dados do SQL Server e que atores does a encriptação proteger contra?
Existem três tecnologias de encriptação principal que estão disponíveis na base de dados do SQL Server: 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (que é mencionado à pergunta acima): encripta confidenciais colunas na tabela ponto a ponto, a partir de clientes não autorizados para o disco físico. O servidor e os administradores de dados não podem ver os dados confidenciais, uma vez que as chaves de encriptação são armazenadas no cliente. 
- [A encriptação transparente de dados](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): encriptação de Inativos que encripta ao nível da base de dados e protege os ficheiros de dados, ficheiros de registo e cópias de segurança associadas de roubo de suporte de dados físico. TDE está ativada por predefinição para todas as bases de dados recentemente criados.
 
  O diagrama seguinte mostra uma descrição geral sobre a encriptação escolhas de tecnologia.

   ![Descrição geral da encriptação](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Como posso gerir a chaves de encriptação na nuvem? 
Existem opções de gestão de chaves para sempre encriptados (encriptação do lado do cliente) e a encriptação transparente de dados (encriptação de Inativos). É recomendado para rodar chaves de encriptação regularmente e uma frequência que está alinhada com regulamentos internos e compatibilidade requisitos.

- **Sempre encriptado**: existe um [hierarquia de duas chave](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) no sempre encriptados - uma coluna de dados confidenciais é encriptada por uma chave de encriptação de coluna AES 256 (CEK), que por sua vez, é encriptada por uma chave mestra da coluna (CMK). Os controladores de cliente fornecidos para sempre encriptado têm não existem limitações ao comprimento de CMKs.

  O valor encriptado do CEK é armazenado na base de dados e o CMK é armazenado num arquivo de chave fidedigno, como arquivo de certificados do Windows, o Cofre de chaves do Azure ou um módulo de hardware de segurança. 
  
  Tanto o [CEK e CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) pode rotação. Rotação de CEK pode ser intensiva de tempo, consoante o tamanho das tabelas que contém as colunas encriptadas. Por conseguinte, planeie cuidadosamente rotações de CEK. Rotação de CMK por outro lado, não interfere com o desempenho de base de dados e pode ser feita com funções separadas.

  O diagrama seguinte mostra as opções de armazenamento de chaves para as chaves de mestra de coluna no sempre encriptado 

   ![Sempre encriptado CMK armazenar fornecedores](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Encriptação de dados transparente (TDE)**: não há uma hierarquia de duas chave TDE – os dados em cada base de dados do utilizador são encriptados por uma simétrica AES 256 exclusivo para a base de dados da base de dados chave de encriptação (DEK), que por sua vez, é encriptada por um servidor único RSA assimétrico chave mestra de 2048. 

  Por predefinição, a chave mestra para a encriptação transparente de dados é gerida pelo serviço de base de dados SQL para sua comodidade. Se a organização gostaria controlo sobre a chave mestra, é uma opção a utilizar [Cofre de chaves do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) como o armazenamento de chaves. 

  Ao utilizar o Cofre de chaves do Azure, organização assume o controlo sobre controlos de aprovisionamento, rotação e permissão de chaves. [Rotação ou mudar o tipo de uma chave mestra TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápido, uma vez que apenas encripta novamente o DEK. 

  Para organizações com separação de funções entre segurança e gestão de dados, um administrador de segurança foi aprovisionar o material de chave para a chave mestra TDE no Cofre de chaves do Azure e fornecer um identificador de chave do Cofre de chaves do Azure para o administrador da base de dados a utilizar para encriptação de Inativos num servidor. 

## <a name="monitoring-and-compliance-after-migration"></a>Monitorização e conformidade após a migração

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Como posso monitorizar atividades de base de dados na base de dados SQL?
Existem algumas capacidades incorporadas no base de dados SQL de monitorização, que controlar a segurança e outros eventos na base de dados:
- [Auditoria de SQL](sql-database-auditing.md) permite-lhe recolher registos de auditoria de eventos de base de dados na sua própria conta do Storage do Azure.
- [A deteção de ameaças do SQL Server](sql-database-threat-detection.md) permite-lhe detetar atividades suspeitas que indicam uma possível intenções maliciosas para aceder, infringir ou exploram os dados na base de dados. Deteção de ameaças de base de dados do SQL Server é executado vários conjuntos de algoritmos que detetar potenciais vulnerabilidades e ataques de injeção de SQL, bem como os padrões de acesso de base de dados anómalas (por exemplo, o acesso a partir de uma localização invulgar ou por um principal de familiarizado). Officers de segurança ou outros administradores designados recebem uma notificação por e-mail, se for detetada uma ameaça na base de dados. Cada notificação fornece detalhes sobre a atividade suspeita e as recomendações sobre como ainda mais a investigar e mitigar a ameaça. 
- [Avaliação da vulnerabilidade SQL](sql-vulnerability-assessment.md) é uma base de dados de análise e relatórios de serviço que lhe permite monitorizar o estado de segurança das bases de dados à escala e identificar riscos de segurança e que se desviam de uma linha de base de segurança definida por si. Depois de cada análise, são fornecidos uma lista personalizada de passos acionáveis e scripts de remediação, bem como um relatório de avaliação que pode ser utilizado para ajudar a para estar em conformidade. 
- [Aplicação de sincronização de segurança do SQL OMS](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) utiliza a APIs públicas do Operations Management Suite (OMS) para emitir os registos de auditoria SQL OMS para análise de registos e a capacidade de definir alertas de deteção personalizado, incluindo: 
 - Mosaico de auditoria de base de dados do SQL Server & dashboard que fornecem claro e coherent relatório das suas atividades de base de dados. 
 - Análise de registos do SQL Server para analisar a atividade de base de dados e investigar discrepâncias e anomalias que possam indicar a violação de segurança suspeito.
 - Avançadas de regras específicas de alertas no observado eventos que activam e-mail, o runbook de automatização de Webhook e o Azure alertas (ou seja, alterações de palavra-passe, comandos SQL específicas, after-hours).
- [Centro de segurança do Azure](../security-center/security-center-intro.md) oferece gestão de segurança centralizada em cargas de trabalho em execução no Azure, no local e em outros nuvens. Pode ver se estão configurados em todos os recursos essencial proteção de base de dados SQL, tais como a auditoria e a encriptação transparente de dados e criar políticas com base nos seus requisitos. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>É a base de dados SQL em conformidade com os requisitos de regulamentação e forma que o ajuda a conformidade da minha própria organização? 
Base de dados SQL do Azure está em conformidade com um intervalo de regulamentação compliances. Para ver o conjunto mais recente de compliances que tiverem sido cumpridos, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) e desagregar compliances que são importantes para a sua organização para ver se a SQL Database do Azure está incluído em conformidade do Azure serviços. É importante ter em atenção que embora SQL Database do Azure pode ser certificada como um serviço em conformidade, que ajuda na conformidade de serviço da sua organização, mas não automaticamente garante-lo. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Manutenção de base de dados e monitorização após a migração

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Como monitorizar o crescimento na utilização de tamanho e de recursos de dados?

- Pode ver as métricas de monitor sobre a utilização de recursos e de tamanho de base de dados no gráfico 'Monitorização' no portal do Azure. 

  ![Gráfico de monitorização](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Para obter informações mais aprofundadas e desagregar os detalhes das consultas, é possível utilizar 'Query Performance Insight' disponíveis no portal do Azure. Esta ação exige que o arquivo de consultas está ativo na base de dados.

  ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Em alternativa, pode ver as métricas de utilizar as vistas de gestão dinâmica (DMVs) demasiado - utilizando [sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>Como muitas vezes, é necessário executar verificações de consistência como DBCC_CHECKDB?
DBCC_CHECKDB verifica a integridade lógica e física de todos os objetos na base de dados. Já não precisa de fazer estas verificações porque estes são geridos pela Microsoft no Azure. Para obter mais informações, consulte [integridade dos dados na SQL Database do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Monitorizar a utilização de desempenho e de recursos após a migração

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Como monitorizar desempenho e a utilização de recursos em SQL Database do Azure?
Pode monitorizar a utilização de desempenho e de recursos na base de dados do SQL do Azure utilizando os seguintes métodos:

- **Portal do Azure**: portal do Azure mostra a utilização de um único da base de dados selecionando a base de dados e clicando em gráfico no painel de descrição geral. Pode modificar o gráfico para mostrar várias métricas, incluindo a percentagem de CPU, percentagem de DTU, percentagem de es de dados, percentagem de sessões e percentagem do tamanho da base de dados. 
  ![utilização de recursos](./media/sql-database-manage-after-migration/resource-utilization.png)

  Neste gráfico também pode configurar alertas por recurso. Estes alertas permitem responder às condições de recursos com uma mensagem de e-mail, escrever um ponto final HTTPS/HTTP ou efetuar uma ação. Consulte o [monitorizar o desempenho de base de dados na base de dados do Azure SQL](sql-database-single-database-monitor.md) para obter instruções detalhadas.

- **Vistas**: pode consultar o [sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista de gestão dinâmica para devolver o histórico de estatísticas de consumo de recursos de última hora e a [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Vista de catálogo de sistema para devolver o histórico para os últimos 14 dias. 

- **Query Performance Insight**: [Query Performance Insight](sql-database-query-performance.md) permite-lhe ver um histórico das consultas de consumo de recursos principais e consultas de execução longa para uma base de dados específica. Esta funcionalidade requer [arquivo de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ser ativada e o Active Directory para a base de dados.

- **Análise de SQL do Azure (pré-visualização) no Log Analytics**: [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md) permite-lhe recolher e visualizar as métricas de desempenho de chaves do Azure SQL do Azure, suportar até 150.000 bases de dados de SQL do Azure e 5000 elástico de SQL Agrupamentos por área de trabalho. Pode utilizá-lo para monitorizar e receber notificações. Pode monitorizar a SQL Database do Azure e métricas de conjunto elástico entre várias subscrições do Azure e conjuntos elásticos e pode ser utilizado para identificar problemas em cada camada de uma pilha de aplicação. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Como garantir que estiver a utilizar o nível de desempenho e o escalão de serviço apropriado?
Monitor de [sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vistas de gestão dinâmica para compreender o consumo de CPU, memória e e/s. Também pode utilizar a SQL Database [Query Performance Insight](sql-database-query-performance.md) para ver o consumo de recursos. Se consistentemente estiver está a executar uma percentagem elevada de recursos disponíveis, deve considere mover para um nível de desempenho superior na camada de serviço existente ou mudar para um escalão de serviço mais elevado. Por outro lado, se estiver a utilizar consistentemente baixa percentagem de recursos disponíveis, pode considerar a mudança para um nível de desempenho inferior ou camada de serviço.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou a ver problemas de desempenho. Como o meu metodologia de resolução de problemas da SQL Database do Azure diferem do SQL Server?
Muitos aspetos da metodologia de resolução de problemas de desempenho permanecer o mesmo na SQL Database do Azure, mas será algumas diferenças. Por exemplo, se vir uma degradação do desempenho global, monitorizar o [sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vistas de gestão dinâmica para compreender a CPU, memória e e/s consumo. Poderá ter de alterar o nível de desempenho e/ou com base na carga de trabalho exigências de camada de serviço.
Para um conjunto abrangente de recomendações para problemas de desempenho de otimização, consulte [otimização de desempenho na base de dados do Azure SQL](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>É necessário manter as estatísticas e índices?
Base de dados SQL do Azure não mantém índices e estatísticas automaticamente como parte do serviço. É responsável pela manutenção dos índices e estatísticas de agendamento. O seguinte artigo, métodos de automatização do Azure, fornece detalhes sobre as várias opções para agendar tarefas de manutenção em relação a SQL Database do Azure.

## <a name="data-movement-after-migration"></a>Movimento de dados após a migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Como exportar e importar dados como ficheiros BACPAC da SQL Database do Azure? 

- **Exportar**: pode exportar a base de dados do SQL do Azure como um ficheiro BACPAC do portal do Azure.

  ![Exportar como um BACPAC](./media/sql-database-export/database-export.png)

- **Importar**: pode importar como ficheiro BACPAC para uma base de dados no portal do Azure.

  ![Importar um BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Como sincronizar dados entre o Azure SQL da base de dados SQL Server 2016 / 2012?
O [sincronização de dados](sql-database-sync-data.md) funcionalidade ajuda-o a sincronizar dados bidirecional entre várias bases de dados no local do SQL Server e SQL Database do Azure. No entanto, uma vez que este acionador com base, consistência eventual é garantida (sem perda de dados), mas não é garantida a consistência de transação. 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [base de dados SQL do Azure](sql-database-technical-overview.md).
