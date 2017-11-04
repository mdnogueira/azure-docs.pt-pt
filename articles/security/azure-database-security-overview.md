---
title: "Descrição geral de segurança da base de dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral da base de dados do Azure funcionalidades de segurança."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 3c83645f61cd42c8c2b46f787c9e7531726d3fbb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-database-security-overview"></a>Descrição geral de segurança da base de dados do Azure

A segurança é uma preocupação superior quando gerir bases de dados e foi sempre uma prioridade para a SQL Database do Azure. Base de dados SQL do Azure suporta segurança de ligação com as regras de firewall e a encriptação da ligação. Suporta a autenticação com o nome de utilizador e palavra-passe e o Azure Active Directory Authentication, que utiliza identidades geridas pelo Azure Active Directory. Autorização utiliza o controlo de acesso baseado em funções.

Base de dados SQL do Azure suporta a encriptação efetuando em tempo real encriptação e desencriptação de bases de dados, cópias de segurança associadas e os ficheiros de registo de transações Inativos sem necessidade de alterações à aplicação.

A Microsoft fornece formas adicionais para encriptar dados da empresa:

-   Encriptação de nível de células para encriptar colunas específicas ou até mesmo células de dados com diferentes chaves de encriptação.
-   Se precisar de um módulo de Hardware de segurança ou gestão central da hierarquia de chave de encriptação, considere utilizar o Cofre de chaves do Azure com o SQL Server numa VM do Azure.
-   Sempre encriptado (atualmente em pré-visualização) faz encriptação transparente para as aplicações e permite aos clientes encriptar dados confidenciais dentro de aplicações de cliente sem partilhar as chaves de encriptação com base de dados SQL.

Auditoria de base de dados SQL do Azure permite que as empresas eventos de registo para um início de sessão de auditoria do armazenamento do Azure. A Auditoria da Base de Dados SQL também pode ser integrada no Microsoft Power BI para facilitar a leitura detalhada de relatórios e análises.

 Bases de dados SQL Azure podem ser totalmente protegidos para satisfazer mais regulamentação ou requisitos de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS nível 1, entre outros. Uma lista atual de certificações de conformidade de segurança está disponível na [site Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

Este artigo explica as noções básicas de proteger bases de dados do Microsoft Azure SQL Server para Structured, tabela e dados relacionais. Em particular, este artigo irá ajudá-lo a começar a utilizar recursos para proteger dados, controlar o acesso e a monitorização proativa.

Este artigo de descrição geral de segurança de base de dados do Azure concentra-se nas seguintes áreas:

-   Proteger os dados
-   Controlo de acesso
-   Monitorização proativa
-   Gestão de segurança centralizada
-   Mercado do Azure

## <a name="protect-data"></a>Proteger os dados

Base de dados SQL protege os seus dados ao fornecer a encriptação dos dados em movimento utilizando [Transport Layer Security](https://support.microsoft.com/kb/3135244), para dadosem rest utilizando [encriptação transparente de dados](http://go.microsoft.com/fwlink/?LinkId=526242)e para os dados em utilização utilizando [ Sempre encriptado](https://msdn.microsoft.com/library/mt163865.aspx).

Nesta secção, iremos falar sobre:

-   Encriptação em movimento
-   Encriptação inativa
-   Encriptação em utilização (cliente)

Para outras formas de encriptar os seus dados, considere:

-   A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
-   Se precisar de um Módulo de Segurança de Hardware ou de fazer a gestão centralizada da hierarquia de chaves de encriptação, considere utilizar o [Azure Key Vault com o SQL Server numa VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Encriptação em movimento

Um problema comum para todas as aplicações de cliente/servidor é a necessidade de privacidade como dados movem-se em redes públicas e privadas. Se mover através de uma rede de dados não for encriptados, há a possibilidade de que podem ser capturada e roubada por utilizadores não autorizados. Ao lidar com os serviços de base de dados, tem de certificar-se de que os dados são encriptados entre o cliente de base de dados e o servidor, bem como entre servidores de base de dados que comunicam entre si e com aplicações de camada média.

Um problema ao administrar uma rede é proteger os dados que estão a ser enviados entre aplicações através de uma rede não fidedigna. Pode utilizar [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, utilizá-la para encriptar mensagens entre as partes autenticadas.

O processo de autenticação, um cliente TLS/SSL envia uma mensagem para um servidor TLS/SSL e o servidor responde com as informações que o servidor precisa para se autenticar. O cliente e o servidor efetuam uma troca adicional de chaves de sessão e a caixa de diálogo de autenticação termina. Quando autenticação for concluída, a comunicação protegida por SSL pode começar entre o servidor e o cliente utilizando as chaves de encriptação simétricas que são estabelecidas durante o processo de autenticação.

Todas as ligações à Base de Dados SQL do Azure precisam de encriptação (SSL/TLS) em todos os momentos enquanto os dados se encontram "em trânsito" para e a partir da base de dados. SQL Azure utiliza o TLS/SSL para autenticar servidores e clientes e, em seguida, utilizá-la para encriptar mensagens entre as partes autenticadas. Na cadeia de ligação da sua aplicação, tem de especificar parâmetros para encriptar a ligação e não a fidedignidade do certificado do servidor (isto é feito automaticamente se copiar a cadeia de ligação fora do Portal do Azure); caso contrário, a ligação não irão verificar a identidade do servidor e vai ser susceptível a ataques "man-in-the-middle". Para o controlador do ADO.NET, por exemplo, estes parâmetros de cadeia de ligação são encriptar = True e TrustServerCertificate = False.

### <a name="encryption-at-rest"></a>Encriptação inativa
Pode demorar várias precauções para ajudar a proteger a base de dados, tais como conceber um sistema seguro, encriptação ativos confidenciais e criação de uma firewall em torno de servidores de base de dados. No entanto, num cenário em que o suporte de dados físico (por exemplo, discos ou bandas de cópia de segurança) é roubados, uma parte maliciosa pode apenas restaurar ou expor a base de dados e procurar os dados.

Uma solução é para encriptar os dados sensíveis na base de dados e protege as chaves que são utilizadas para encriptar os dados com um certificado. Isto impede que qualquer pessoa sem as chaves a utilizar os dados, mas este tipo de proteção têm de ser planeado.

Para resolver este problema, SQL Server e SQL do Azure suportam [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). TDE encripta os ficheiros de dados do SQL Server e SQL Database do Azure, conhecidos como dados de encriptação de inativos.

Encriptação de dados transparente de base de dados SQL do Azure ajuda a proteger contra a ameaça de atividade maliciosa efetuando a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transações Inativos sem necessidade de alterações para o aplicação.  

TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados. Na base de dados do SQL Server, a chave de encriptação da base de dados está protegida por um certificado de servidor incorporada. O certificado de servidor incorporada é exclusivo para cada servidor de base de dados SQL.

Se tiver uma base de dados numa relação GeoDR, está protegido por uma chave diferente em cada servidor. Se duas bases de dados estiverem ligados ao mesmo servidor, estes partilham o mesmo certificado incorporado. Microsoft roda automaticamente estes certificados, pelo menos, todos os 90 dias. Para obter uma descrição geral de TDE, consulte [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Encriptação em utilização (cliente)

A maioria das falhas de dados envolvem o roubo de dados críticos, como números de cartão de crédito ou informações pessoais identificáveis. Bases de dados podem ser treasure troves de informações confidenciais. Podem conter dados pessoais dos clientes, informações competitivos confidenciais e propriedade intelectual. Dados perdidos ou roubados, especialmente os dados de cliente, podem resultar em danos marca, desvantagem competitiva e graves fines — mesmo lawsuits.

![Sempre Encriptado](./media/azure-databse-security-overview/azure-database-fig1.png)

[Sempre encriptado](https://msdn.microsoft.com/library/mt163865.aspx) é uma funcionalidade concebida para proteger os dados confidenciais, como números de cartão de crédito ou números de identificação national (por exemplo, E.U.A. números de segurança social), armazenados nas bases de dados SQL Database do Azure ou SQL Server. Sempre encriptado permite aos clientes encriptar dados confidenciais dentro de aplicações de cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server).

Sempre encriptado fornece uma separação entre os utilizadores que possuem os dados (e pode vê-la) e aqueles que gerir os dados (mas deve ter sem acesso). Operadores de base de dados da nuvem, assegurando que os administradores de base de dados no local, ou outros privilégios elevados, mas os utilizadores não autorizados, não é possível aceder aos dados encriptados.

Além disso, sempre encriptados faz encriptação transparente para as aplicações. Um ativada sempre encriptado um controlador instalado no computador cliente para que possam automaticamente encriptar e desencriptar dados sensíveis na aplicação de cliente. O controlador encripta os dados nas colunas confidenciais antes de passar os dados para o motor de base de dados e automaticamente reescreve consultas para que a semântica para a aplicação é preservada. Da mesma forma, o controlador de forma transparente desencripta os dados, armazenados em colunas de base de dados encriptados contidas nos resultados de consulta.

## <a name="access-control"></a>Controlo de acesso
Para fornecer segurança, a Base de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores provem a sua identidade e mecanismos de autorização que limitam os utilizadores a ações e dados específicos.

### <a name="database-access"></a>Acesso de base de dados

Proteção de dados começa com a controlar o acesso aos seus dados. O datacenter que aloja os dados gere o acesso físico, embora possa configurar uma firewall para gerir a segurança na camada de rede. Também pode controla o acesso ao configurar os inícios de sessão para autenticação e definir permissões para funções de servidor e base de dados.

Nesta secção, iremos falar sobre:

-   Firewall e regras de firewall
-   Autenticação
-   Autorização

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

A Base de Dados SQL do Microsoft Azure disponibiliza um serviço de bases de dados relacionais para o Azure e outras aplicações baseadas na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

O [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) serviço só está disponível através da porta TCP 1433. Para aceder a uma Base de Dados SQL a partir do seu computador, certifique-se de que a firewall do computador de cliente permite comunicação TCP de saída na porta TCP 1433. Se não for necessário para outras aplicações, bloqueie as ligações de entrada na porta TCP 1433.

#### <a name="authentication"></a>Autenticação

A autenticação da Base de Dados SQL diz respeito à forma como o utilizador prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

-   **Autenticação do SQL Server:** é criada uma conta de início de sessão único quando é criada uma instância do SQL Server lógica, denominada a conta de subscritor do SQL Server da base de dados. Esta conta liga-se utilizando [autenticação do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome de utilizador e palavra-passe). Esta conta é administradora na instância do servidor lógico e em todas as bases de dados de utilizador ligadas a essa instância. As permissões da Conta de Subscritor não podem ser restritas. Só pode existir uma destas contas.
-   **Autenticação do Active Directory do Azure:** [autenticação do Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) é um mecanismo de ligar à base de dados do Microsoft Azure SQL Server e do armazém de dados SQL utilizando as identidades no Azure Active Directory ( Azure AD). Isto permite-lhe gerir centralmente identidades de utilizadores de base de dados.

![Autenticação](./media/azure-databse-security-overview/azure-database-fig2.png)

 Vantagens da autenticação do Azure Active Directory incluem:
  - Fornece uma alternativa à autenticação do SQL Server.
  - Também ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados & permite rotação de palavra-passe num único local.
  - Pode gerir as permissões de base de dados através de grupos do externos (Azure Active Directory).
  - -Pode eliminar armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportados pelo Azure Active Directory.

#### <a name="authorization"></a>Autorização
[Autorização](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) refere-se a que um utilizador pode fazê-lo dentro de uma base de dados do SQL do Azure e este é controlado pela base de dados da sua conta de utilizador [associações de função](https://msdn.microsoft.com/library/ms189121) e [ao nível do objeto permissões](https://msdn.microsoft.com/library/ms191291.aspx). Autorização é o processo de determinar quais os recursos com capacidade de segurança pode aceder a um principal e as operações são permitidas para esses recursos.

### <a name="application-access"></a>Acesso à aplicação

Nesta secção, iremos falar sobre:

-   Máscara de dados dinâmica
-   Segurança ao Nível da Linha

#### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica
Um representante de serviço no Centro de atendimento telefónico pode identificar os chamadores por vários dígitos do seu número de segurança social ou um número de cartão de crédito, mas esses itens de dados não devem ser expostas completamente para o representante de serviço.

Uma regra de máscara pode ser definida, mas os últimos quatro dígitos de qualquer número de segurança social ou o número de cartão de crédito nos resultados de máscaras de todos os definido qualquer consulta.

![Máscara de dados dinâmica](./media/azure-databse-security-overview/azure-database-fig3.png)

Outro exemplo, pode ser definida uma máscara de dados adequada para proteger os dados de informação identificativa (PII), para que um programador pode consultar os ambientes de produção para fins de resolução de problemas sem violar as normas de conformidade.

[SQL Server da base de dados máscara de dados dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais através da máscara-lo para utilizadores não privilegiados. A máscara de dados dinâmica é suportada para a versão V12 da base de dados do Azure SQL.

[Máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado aos dados confidenciais, permitindo-lhe designar a quantidade dos dados confidenciais para revelar com um impacto mínimo na camada da aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.


> [!Note]
> Máscara de dados dinâmicos pode ser configurada pelo administrador de base de dados do Azure, administrador de servidor ou funções de responsável pela segurança.

#### <a name="row-level-security"></a>Segurança ao nível da linha
É outro requisito de segurança comuns para bases de dados multi-inquilino [segurança ao nível da linha](https://msdn.microsoft.com/library/dn765131.aspx). Esta funcionalidade permite-lhe controlar o acesso às linhas numa tabela da base de dados com base em caraterísticas do utilizador executar uma consulta (por exemplo, grupo ou execução da associação contexto).

![Segurança de nível de linha](./media/azure-databse-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizado na camada de base de dados em vez de away dos dados na camada da aplicação em outro. O sistema de base de dados aplica-se as restrições de acesso sempre que esse acesso de dados é tentado a partir de qualquer camada. Isto torna o seu sistema de segurança mais fiável e robusta, reduzindo a área de superfície do seu sistema de segurança.

Segurança ao nível da linha apresenta o controlo de acesso baseado em predicado. Funcionalidades de uma avaliação flexível, centralizada, baseados no predicado que pode ter em consideração metadados ou quaisquer outros critérios, que o administrador determina conforme adequada. O predicado é utilizado como um critério para determinar se o utilizador tem o acesso adequado aos dados com base nos atributos de utilizador. Controlo de acesso baseado em etiqueta pode ser implementado utilizando o controlo de acesso baseado em predicado.

## <a name="proactive-monitoring"></a>Monitorização proativa
Base de dados SQL protege os seus dados, fornecendo **auditoria** e **deteção de ameaças** capacidades.

### <a name="auditing"></a>Auditoria
Auditoria de base de dados SQL aumenta a capacidade para obter conhecimentos aprofundados sobre eventos e as alterações que ocorrem na base de dados, incluindo atualizações e consultas em relação as dados.

[Auditoria de base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla da base de dados eventos e escreve-los para uma auditoria iniciar sessão na sua conta do Storage do Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam traduzir preocupações comerciais ou suspeitas de violações de segurança. Auditoria permite e facilita a conformidade com as normas de conformidade, mas não garante a compatibilidade.

Auditoria de base de dados SQL permite-lhe:

-   **Manter** um registo de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados a serem auditados.
-   **Relatório** na atividade de base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar a trabalhar rapidamente com atividade e o relatório de eventos.
-   **Analisar** relatórios. Pode encontrar eventos suspeitos, atividade invulgar e tendências.

Existem dois métodos de auditoria:

-   **Auditoria de blob** -os registos são escritos para armazenamento de Blobs do Azure. Este é um método de auditoria mais recente, que fornece um desempenho superior, suporta a auditoria de maior granularidade ao nível do objeto e é mais económico.
-   **Auditoria de tabela** -os registos são escritos para o Table Storage do Azure.

### <a name="threat-detection"></a>Deteção de ameaças
[A deteção de ameaças de base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) Deteta atividades suspeitas que indicam potenciais ameaças de segurança. A deteção de ameaças permite-lhe responder a eventos suspeitos na base de dados, tais como SQL Injections, à medida que ocorrem. Disponibiliza alertas e permite a utilização de auditoria de base de dados de SQL do Azure para explorar os eventos suspeitos.

![Deteção de ameaças](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Por exemplo, a injeção de SQL é uma dos problemas comuns de segurança de aplicação Web na Internet, utilizado para atacar aplicações condicionada por dados. Os atacantes tirar partido de vulnerabilidades de aplicação para injetar maliciosas instruções SQL para campos de entrada de aplicação, ser, ou modificar dados na base de dados.

Officers de segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas da base de dados à medida que ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como ainda mais a investigar e mitigar a ameaça.        

## <a name="centralized-security-management"></a>Gestão de segurança centralizada

O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

[Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ajuda a salvaguardar dados na base de dados do SQL Server, fornecendo visibilidade para a segurança de todos os servidores e bases de dados. Centro de segurança, pode:

-   Defina políticas para auditoria e de encriptação de base de dados SQL.
-   Monitorize a segurança dos recursos de base de dados SQL em todas as subscrições.
-   Rapidamente identificar e resolver problemas de segurança.
-   Integrar alertas a partir do [deteção de ameaças da SQL Database do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Centro de segurança suporta o acesso baseado em funções.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um mercado online de aplicações e serviços que permite às empresas recém-criadas e aos fabricantes independentes de software (ISV) oferecerem as suas soluções aos clientes Azure de todo o mundo.
O Azure Marketplace combina o ecossistema de parceiros do Microsoft Azure numa plataforma unificada que proporciona uma experiência otimizada aos nossos clientes e parceiros. Clique em [aqui](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para produtos de segurança de base de dados de relance disponíveis no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [proteger a base de dados do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Saiba mais sobre [serviço Centro de segurança do Azure e SQL Database do Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Para saber mais sobre a deteção de ameaças, consulte o artigo [deteção de ameaças de base de dados do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Para obter mais informações, consulte [desempenho de base de dados do SQL Server melhorar](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
