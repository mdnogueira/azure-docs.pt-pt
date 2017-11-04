---
title: "Os procedimentos de segurança da base de dados do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança da base de dados do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: tomsh
ms.openlocfilehash: 5bd6fe0dd369b3bbc7ca0d697c964badda557cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-best-practices"></a>Procedimentos de segurança da base de dados do Azure

A segurança é uma preocupação superior quando gerir bases de dados e foi sempre uma prioridade para a SQL Database do Azure. As bases de dados podem ser totalmente protegidos para o ajudar a satisfazer mais regulamentação ou requisitos de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS nível 1, entre outros. Uma lista atual de certificações de conformidade de segurança está disponível na [site Microsoft Trust Center](http://azure.microsoft.com/support/trust-center/services/). Também pode optar por colocar as bases de dados específicos centros de dados do Azure, com base em requisitos regulamentares.

Neste artigo, vamos abordar uma coleção de práticas recomendadas de segurança de base de dados do Azure. Estas melhores práticas são derivadas da nossa experiência com a segurança da base de dados do Azure e as experiências dos clientes, como por si.

Para cada melhor prática, vamos explicar:

-   O que é a melhor prática
-   Por que motivo que pretende ativar essa recomendado
-   Se falhar ativar a melhor prática, que poderá ser o resultado
-   Como pode saber mais ativar a melhor prática

Este artigo de melhores práticas de segurança do Azure da base de dados baseia-se num opinião consenso e capacidades da plataforma do Azure e conjuntos de funcionalidades, tal como existem no momento que este artigo foi escrito. Opinions e tecnologias alteram ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Base de dados do Azure melhores práticas de segurança abordadas neste artigo incluem:

-   Utilize as regras de firewall para restringir o acesso de base de dados
-   Ativar a autenticação de base de dados
-   Proteger os seus dados através da encriptação
-   Proteger dados em trânsito
-   Ativar a auditoria de base de dados
-   Ativar a deteção de ameaças de base de dados


## <a name="use-firewall-rules-to-restrict-database-access"></a>Utilize as regras de firewall para restringir o acesso de base de dados

A Base de Dados SQL do Microsoft Azure disponibiliza um serviço de bases de dados relacionais para o Azure e outras aplicações baseadas na Internet. Para fornecer a segurança de acesso, base de dados SQL controla o acesso com regras de firewall limitar a conectividade ao endereço IP, mecanismos de autenticação que obriga os utilizadores provar a sua identidade e mecanismos de autorização limitar os utilizadores aos seus dados e as ações específicas. Firewalls impedir que todo o acesso ao seu servidor de base de dados até especificar quais os computadores que tem permissão. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

![Regras da firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço da Base de Dados SQL do Azure só está disponível através da porta TCP 1433. Para aceder a uma Base de Dados SQL a partir do seu computador, certifique-se de que a firewall do computador de cliente permite comunicação TCP de saída na porta TCP 1433. Se não for necessário para outras aplicações, bloquear ligações de entrada na porta TCP 1433 utilizando regras de firewall.

Como parte do processo de ligação, as ligações a partir de máquinas virtuais do Azure são redirecionadas para um endereço IP diferente e a porta exclusiva para cada função de trabalho. O número de porta está no intervalo de 11000 a 11999. Para mais informações sobre portas TCP, consulte [portas para além de 1433 para ADO.NET 4.5 e o SQL Server Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Ativar a autenticação de base de dados
Base de dados SQL suporta dois tipos de autenticação, a autenticação do SQL Server e o Azure Active Directory Authentication (autenticação do Azure AD).

**Autenticação do SQL Server** recomenda-se nos seguintes casos:

-   -Lo permite ao Azure SQL Server suportar ambientes com sistemas operativos mistos, onde todos os utilizadores não são autenticados por um domínio do Windows.
-   Permite ao Azure SQL Server suportar mais antigos aplicações e aplicações fornecidas por terceiros que necessitam de autenticação do SQL Server.
-   Permite aos utilizadores ligar a partir de domínios desconhecidos ou não fidedignos. Por exemplo, uma aplicação onde estabelecidos clientes se ligam com atribuído inícios de sessão do SQL Server para receber o estado dos respetivos ordens.
-   Permite ao Azure SQL Server suportar aplicações baseadas na Web em que os utilizadores criar as suas próprias identidades.
-   Permite que os programadores de software distribuir as aplicações através da utilização de uma hierarquia de permissão complexas com base em inícios de sessão predefinidos, conhecidos do SQL Server.

> [!Note]
> No entanto, a autenticação do SQL Server não é possível utilizar o protocolo de segurança de Kerberos.

Se utilizar **autenticação SQL** tem de:

-   Faça a gestão de credenciais seguras por si.
-   Proteger as credenciais na cadeia de ligação.
-   Proteger (potencialmente) as credenciais transmitidas através da rede do servidor Web para a base de dados. Para obter mais informações consulte [como: ligar a autenticação do SQL Server utilizando o SQL Server no ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

**Autenticação do Azure Active Directory** é um mecanismo de ligar à base de dados do Microsoft Azure SQL Server e [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) utilizando as identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure AD, pode gerir centralmente as identidades de utilizadores de base de dados e outros serviços Microsoft numa localização central. Gestão de ID central fornece um único local para gerir utilizadores de base de dados e simplifica a gestão de permissão. Vantagens incluem o seguinte:

-   Fornece uma alternativa à autenticação do SQL Server.
-   Ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados.
-   Permite a rotação de palavra-passe num único local.
-   Os clientes podem gerir as permissões de base de dados através de grupos do externos (AAD).
-   -Pode eliminar armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportados pelo Azure Active Directory.
-   Autenticação do Azure AD utiliza os utilizadores de base de dados contida para autenticar identidades ao nível da base de dados.
-   AD do Azure suporta a autenticação baseada em tokens para aplicações ligar à base de dados do SQL Server.
-   Autenticação do AD do Azure suporta ADFS (Federação de domínio) ou autenticação de utilizador/palavra-passe nativo para um Azure Active Directory local sem sincronização de domínio.
-   Azure AD suporta ligações de SQL Server Management Studio que utilizam a autenticação do Active Directory Universal, que inclui o multi-factor Authentication (MFA). MFA inclui autenticação forte com uma gama de opções de verificação fácil — chamada telefónica, mensagem de texto, os smart cards com pin ou a notificação da aplicação móvel. Para obter mais informações, consulte [SSMS suporte para o MFA do Azure AD com base de dados SQL e SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure Active Directory.

-   Criar e preencher do Azure AD.
-   Opcional: Associar ou altere o active directory que está atualmente associado a sua subscrição do Azure.
-   Criar um administrador do Azure Active Directory para o Azure SQL server ou [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure os computadores cliente.
-   Crie utilizadores de base de dados contida na base de dados mapeado para as identidades do Azure AD.
-   Liga à base de dados através da utilização de identidades do Azure AD.

Pode encontrar informações de detalhes [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Proteger os seus dados através da encriptação

[Encriptação de dados transparente de base de dados SQL do Azure (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) ajuda a proteger contra a ameaça de atividade maliciosa efetuando a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transações Inativos sem exigir alterações à aplicação. TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados.

Mesmo quando o armazenamento completo é encriptado, é muito importante também encripte a sua própria base de dados. Esta é uma implementação de defesa numa abordagem de profundidade para proteção de dados. Se estiver a utilizar o SQL Database do Azure e pretender proteger os dados confidenciais, tais como o cartão de crédito ou números de segurança social, pode encriptar as bases de dados com encriptação AES de 256 bits de 140-2 validado de FIPS que cumpra os requisitos de muitos normas da indústria (por exemplo, HIPAA, PCI) .

É importante compreender que os ficheiros relacionados com [a memória intermédia de extensão do conjunto (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não são encriptadas quando uma base de dados é encriptado utilizando TDE. Tem de utilizar ferramentas de encriptação no nível do sistema de ficheiros como [BitLocker](https://technet.microsoft.com/library/cc732774) ou [sistema de encriptação de ficheiros (EFS)](https://technet.microsoft.com/library/cc700811.aspx) para BPE relacionados com os ficheiros.

Dado que um utilizador autorizado, tais como um administrador de segurança ou um administrador da base de dados pode aceder aos dados, mesmo se a base de dados é encriptado com TDE, também deve seguir as recomendações abaixo:

-   Ative a autenticação do SQL Server ao nível da base de dados.
-   Utilizar o Azure AD de autenticação utilizando [funções do RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Utilizadores e aplicações devem utilizar contas separadas para autenticação. Desta forma pode limitar as permissões concedidas a utilizadores e aplicações e reduzir os riscos de atividade maliciosa.
-   Segurança de nível de base de dados de implementar através de funções de base de dados fixa (como db_datareader ou db_datawriter), ou pode criar funções personalizadas para a sua aplicação conceder permissões explícitas para objetos de base de dados selecionada.

Para outras formas de encriptar os seus dados, considere:

-   A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
-   Encriptação em utilizar a utilizar sempre encriptados: [sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx) permite aos clientes encriptar dados confidenciais dentro de aplicações de cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server). Como resultado, sempre encriptado proporciona uma separação entre os utilizadores que possuem os dados (e pode vê-la) e aqueles que gerir os dados (mas deve ter sem acesso).
-   Utilizar a segurança ao nível da linha: segurança ao nível da linha permite que os clientes controlar o acesso às linhas numa tabela da base de dados com base em caraterísticas do utilizador executar uma consulta (por exemplo, grupo ou execução da associação contexto). Para obter mais informações, veja [Segurança ao Nível da Linha](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito
Proteger dados em trânsito deve fazer parte essencial da sua estratégia de proteção de dados. Uma vez que os dados irão ser movidos anterior e descritos de diversas localizações, a recomendação geral é que utilize sempre protocolos SSL/TLS para trocar dados entre localizações diferentes. Em algumas circunstâncias, poderá pretender isolar o canal de comunicação completa entre o local e nuvem infraestrutura através da utilização de uma rede privada virtual (VPN).

Para dados mover entre a infraestrutura no local e o Azure, deve considerar as proteções de adequada, tais como HTTPS ou VPN.

Para organizações que precisam de proteger o acesso a partir de várias estações de trabalho localizados no local para o Azure, utilize [VPN de site para site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam de proteger o acesso a partir de estações de trabalho individuais, localizada no local ou fora do local para o Azure, considere a utilização [ponto a Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos através de uma ligação WAN de alta velocidade dedicada, tais como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para maior proteção.

Se interage com o Storage do Azure através do Portal do Azure, todas as transações ocorrem através de HTTPS. [API REST do Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS também pode ser utilizado para interagir com a ativação pós-falha [Storage do Azure](https://azure.microsoft.com/services/storage/) e [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que falham para proteger dados em trânsito sejam mais suscetíveis de [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [escutas](https://technet.microsoft.com/library/gg195641.aspx) e hijacking de sessão. Estes ataques podem ser o primeiro passo na obtendo acesso aos dados confidenciais.

Para obter mais informações sobre a opção de VPN do Azure ao ler o artigo [planeamento e design para o Gateway de VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Ativar a auditoria de base de dados
Uma instância do motor de base de dados do SQL Server ou uma base de dados individuais de auditoria envolve controlo e registo de eventos que ocorrem no motor de base de dados. Auditoria de SQL Server permite-lhe criar as auditorias de servidor, que podem conter as especificações de auditoria do servidor para eventos ao nível do servidor e as especificações de auditoria de base de dados de eventos ao nível da base de dados. Eventos auditados podem ser escritos para os registos de eventos ou para os ficheiros de auditoria.

Existem vários níveis de auditoria para o SQL Server, dependendo das agências ou requisitos de padrões para a sua instalação. SQL Server Audit fornece as ferramentas e processos que tem de ter para ativar, armazenar e visualizar as auditorias de vários objetos de servidor e base de dados.

[Auditoria de base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) controla da base de dados eventos e escreve-los para uma auditoria iniciar sessão na sua conta do Storage do Azure.

A auditoria pode ajudá-lo a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam traduzir preocupações comerciais ou suspeitas de violações de segurança.

Auditoria permite e facilita a conformidade com as normas de conformidade, mas não garante a compatibilidade.

Para saber mais sobre a auditoria de base de dados e como ativá-la, leia o artigo [ativar a auditoria e deteção de ameaças em SQL servers no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Ativar a deteção de ameaças de base de dados
A deteção de ameaças do SQL Server permite-lhe detetar e reagir a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança em atividades anómalas. Receberá um alerta após a atividades suspeitas da base de dados, potenciais vulnerabilidades e ataques de injeção de SQL, bem como os padrões de acesso de base de dados anómalas. Alertas de deteção de ameaças do SQL Server fornecem detalhes de atividade suspeita e recomenda ação sobre a investigar e mitigar a ameaça.

Por exemplo, a injeção de SQL é uma dos problemas comuns de segurança de aplicação Web na Internet, utilizado para atacar aplicações condicionada por dados. Os atacantes tirar partido de vulnerabilidades de aplicação para injetar maliciosas instruções SQL para campos de entrada de aplicação, ser, ou modificar dados na base de dados.

Para saber mais sobre como configurar a deteção de ameaças para a base de dados do Azure portal, consulte [deteção de ameaças de base de dados do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Além disso, a deteção de ameaças do SQL Server integra-se alertas com [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/). Experimente durante 60 dias sem encargos.

Para saber mais sobre a deteção de ameaças da base de dados e como ativá-la, leia o artigo [ativar a auditoria e deteção de ameaças em SQL servers no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Conclusão
Base de dados do Azure é uma plataforma de base de dados robusta, com um conjunto completo de funcionalidades de segurança que cumprem os requisitos de conformidade de regulamentação e organizacional muitos. Pode ajudar a proteger os dados ao controlar o acesso físico aos seus dados e, utilizando uma variedade de opções de segurança de dados no ficheiro-, -de coluna ou ao nível da linha com a encriptação transparente de dados, a encriptação de nível de células ou a segurança ao nível da linha. Sempre encriptado também permite operações contra os dados encriptados, simplificar o processo de atualizações da aplicação. Por sua vez, acesso a auditoria de registos de atividade de base de dados do SQL Server fornece-lhe as informações que necessárias, permitindo-lhe saber como e quando os dados são acedidos.

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre as regras de firewall, consulte o artigo [regras de Firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Para saber mais sobre utilizadores e inícios de sessão, veja [Gerir inícios de sessão](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Para um tutorial, consulte [proteger a base de dados do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
